# SecureSHell

SSH or SecureSHell uses the Diffie-Hellman key exchange to maintain Perfect Forward Secrecy\* between two clients. Here's how it works:

When we browse the web, the client must authenticate the server. However, when SSH is being used, both the client and server must authenticate. The server uses a private key that lets the client verify the authenticity of the serverr with a public key kept on file by the client. When it's tim to talk, the client and server don't exchange symmetric keys using AES over RSA, they use Diffie-Hellman instead.

Diffie-Hellman is a public key technique but unlike RSA, it does not rely on factoring large numbers. Diffie-Hellman requires each member of the exchange have their common values and secret values. Here is an example:

> ```
> 	g = 29
> 	p = 101
> 	p is the modulus
> 	g is the base
> 	A Secret = 17
> 	B Secret = 61
> 	
>     DH Exchange does the following: 
> 	A calculates g^A mod p to derive Aprime
> 		29^17 mod 101 = 63 || pow(29,17,101) = 63
> 	B calculates g^B mod p to derive Bprime
> 		29^61 mod 101 = 99 || pow(29,61,101) = 99
> 	
>     Aprime is sent to B and Bprime is sent to A
> 	A calculates Bprime^Aprime mod p to derive S
> 		99^17 mod 101 = 26 || pow(99,17,101)
> 	B calculates Aprime^Bprime mod p to derive S
> 		63^61 mod 101 = 26 || pow(63,61,101) 
> 	S is the Shared Secret! This is possible because (g^a)^b = (g^b)^a 
> 	Every time for any values this is true. 
>     
>     The Diffie-Hellman Key Exchange is hoping that if an attacker gets
>     ahold of g and ga they will not be able to recover the original
>     exponent a. This is the Discrete Log Problem and is computationally
>     difficult for large values.
> ```

SSH mitigates the risk of session replay attacks but does not eliminate the risk entirely because you can still have your session hijacked.

\*Perfect Forward Secrecy is a security concept defining the access threat actors have if your keys are compromised. In the ideal Perfect Forward Secrecy environment, even if your keys are compromised your previous sessions cannot be decrypted. Not all implementations of SSL do this. If your RSA keys are stolen, for example, you are absolutely fucked as an attacker can use your RSA key to crack your AES session key and get ahold of everything ever encrypted with those keys. SSH allows for PFS because the generated keys are ephemeral.
