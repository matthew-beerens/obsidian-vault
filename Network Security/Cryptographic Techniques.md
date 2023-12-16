

### Cryptography 

- encryption decryption protects confidentiality and privacy from passive attacks
- keyed-hash functions and digital signatures protect integrity and authenticity from active attacks

access control and key management technique make use of cryptographic methods also

symmetric key encryption:
- faster than public key algorithms
- more predictable in performance
- useful for protecting larger messages
- easier to generate a key
- uses block cipher and stream cipher

public key encryption:
- boy public and private belong to the same owner
- public key distributed private kept safe by owner
- less efficient in performance, computationally expensive in key generating and encryption/decryption
- used for smaller messages

diffie-hellman key exchange:
- shared key agreement protocol
- share secret key using public domain
- one cannot calculate key in reasonable amount of time only knowing q,a, ya and yb
- computationally infeasible to compute
- widely used in establishment of session keys
- vulnerable to MiTM attacks

Hash:
- ensures integrity and authenticity of messages
- no easily reversible
- computationally unique value

### Authentication

- entity authentication
- data origin authentication
	- verify messages comes from the authenticated origin and integrity is checked

entity authentication methods:
- address-based
- password or secret-based

message authentication includes:
- MAC (message authentication code) or keyed-hash
- digital signature (result of encrypting hash of a message (signing the message) using private key)

### Address-based authentication
- ip address can be used to represent the sending computers identity
- mostly used at the network layer
- used by firewalls or other monitoring/inspection devices
- can be easily spoofed
- mac addresses are often used for access control to a network (especially wireless) also easily spoofed

defense a against spoofing:
- packet filer to block obvious ip spoofed packets
- disable source routing
- other monitoring tools

### Secret based entity authentication
- to show the other side that you have the secret by a number of secure handshake messages
- securely arranged beforehand (both sides have shared secret copies)

1. A says hello
2. B sends challenge NONCE
3. A uses shared secret and N to created a keyed-hash and sends it to B
4. B create hash from his copy of shared secret and N to compare the received hash from A

there are many variants of this including:
- secure handshake variant
- one-message secure handshake (uses hash or encryption to send shared secret and timestamp, receiver then compares and checks the difference in timestamp)

### Mutual (Entity) Authentication

- both sides authenticate each other in one communication session
- could be done by both sides using 3 secure handshake messages (6 messages in total 5 without second hello)
- variants are possible

### Entity authentication using private key

public/private key authentication
keys are used to encrypt and decrypt nonce for authentication between entities

### Message Authentication use Keyed-Hash (MAC)

keyed-hash is also called message authentication code

1. entity sends message to other
2. both entities hash shared secret and m and then compare hashes
3. if match they're authenticated
4. protects integrity as attackers can tamper message in between

### Message authentication using Digital Signature

for a large amount of data M, the hash of M is used to create the digital signature of M

1. Message goes through hash function
2. hash and private key go through public-key encryption
3. entity sends encrypted message to receiver containing message and their private key as well as plain M
4. receiver hashes M
5. receiver decrypts encrypted message using public key
6. receiver compares hashes

![[Pasted image 20230731223016.png]]

### Digital Certificate

- also called public-key certificate
- used to distribute owners public key on the internet securely
	- integrity and origin of the key can be verified by user of key
	- carried out by CA
- trusted public key can be used to verify the CA's signature in a digital certificate isssued by the CA directly

![[Pasted image 20230731223318.png]]