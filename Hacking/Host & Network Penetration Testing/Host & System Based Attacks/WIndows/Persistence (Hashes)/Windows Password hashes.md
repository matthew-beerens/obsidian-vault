Need elevated session (root)

Stored locally in the SAM (security accounts manager) database

windows < vista uses LM and NTLM hashes
vista onwards uses NTLM

Authentication and verifications is facilitate by the LSA (local system authority)


SAM Database
=
all user account passwords stored here and hashed by default
SAM cannot be copied while the system is running
NT kernel keeps it locked

LSASS (lsa process) keeps a cache of SAM database to interact with

Modern version of windows encrypt SAM with a syskey

LM Hash
=
- broken into 7 character chunks
- not case sensitive
- DES algorithm used
- chunks connected to make LM hash

NTLM (NThash) Hash
=
- case sensitive
- does not split into chunks
- uses MD4 hashing
- allows use of symbols and unicode chars
- do not use password salts