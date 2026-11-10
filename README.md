# SQL-hashing

## Status: legacy

This repository is legacy. Nobody maintains it. Use [linkja-hashing](https://github.com/MRAIA-ORG/linkja-hashing) for new work.

## What it is

SQL Server scripts that hash patient details inside the database, plus Windows batch files that run them.

* `SQLFiles/HashSetup.sql` creates the hash function and the clean-up functions. Run it once with `hash_setup.bat`.
* `SQLFiles/InsertintoHash.sql` cleans the patient details and makes the hash values. `SQLFiles/SelectHash.sql` exports them. `hash_run_site.bat` runs both and writes a CSV to `Output/`.
* `todecryptSaltFile.bat` uses OpenSSL to unlock the salt file with the site's private key.

The batch files call `sqlcmd.exe` from SQL Server 2012 (`Tools\Binn` under version `110`).

## Its hash values do not match linkja-hashing

Hash values from this repository cannot be matched with hash values from linkja-hashing. The two tools hash different input with a different algorithm.

* **Algorithm.** This repository uses SHA-512: `HASHBYTES('SHA2_512', ...)` in `SQLFiles/HashSetup.sql`. linkja-hashing uses SHA-256 from the linkja-crypto library (`hash_data` in `src/linkja-crypto.c`).
* **Field labels.** linkja-hashing puts a short label before each value it hashes, e.g. `PRS` before the project salt and `SID` before the site ID (`HashingStep.java`). This repository joins the bare values and adds the salt at the end.
* **Library secret.** linkja-hashing passes each hash value through the secret build of linkja-crypto, which adds a library secret (`createSecureHash`). This repository has no library secret.

## Old OpenSSL

`DecryptSourceCodes/bin` holds OpenSSL 0.9.8k binaries (`openssl.exe`, `libeay32.dll`, `ssleay32.dll`). That version is from March 25, 2009. The OpenSSL 0.9.8 series is at end of life and gets no security fixes. Do not use these binaries.
