# Cracking a Password-Protected PDF with John the Ripper

A walkthrough of extracting a crackable hash from a password-protected PDF and recovering the password using **John the Ripper (Johnny GUI)**, completed as part of a hands-on lab from Networkwalks Academy. Done in an isolated Windows 11 VM (VirtualBox) against a PDF provided for this exercise.

> ⚠️ **Scope note:** This was performed against a training file I was explicitly given for practice. Don't run these steps against PDFs or systems you don't own or have permission to test.

## Goal

Recover the open-password of an encrypted PDF (`My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf`, 65.2 KB) using only the hash extracted from the file — no brute-forcing the UI, no guessing.

## Tools used

- **Johnny** (GUI front-end for John the Ripper) — `John the Ripper 1.9.0-jumbo-1`
- Networkwalks' browser-based Hash Calculator (extracts a `pdf2john`/hashcat-compatible hash)
- Windows 11 VM in Oracle VirtualBox (isolated lab environment)

## Steps

### 1. Extract a crackable hash from the PDF

Using the Hash Calculator's "PDF" tab:

![Hash Calculator - PDF tab](Screenshot%202026-11-20%20113041.png)

Uploading the locked PDF extracts its encryption parameters and a crackable hash — nothing is sent anywhere, the file is parsed locally:

![Extracted hash](Screenshot%202026-11-20%20113149.png)

```
Revision: R4   Version: V4   Key length: 128-bit

$pdf$4*4*128*-1060*1*16*55d1a5c14175da449753199e44971d32*32*777fd021a7f3c5ae598c0c6495c7f76e0000000000000000000000000000000*32*ceecdac74b19b5a62688d3b3524e1374c955cbb9cc3c45316494d9446ef81af1
```

### 2. Save the hash to a file

Saved the extracted hash as `hash1.txt`, ready for John the Ripper's `--format=PDF` mode:

![hash1.txt](Screenshot%202026-11-20%20111648.png)

### 3. Point Johnny at the John the Ripper binary

Configured Johnny's **Settings** tab to use the jumbo build of John the Ripper (needed for the wider range of supported hash formats, including PDF):

![Johnny settings](Screenshot%202026-11-20%20110653.png)

### 4. Load the hash and run the attack

Loaded `hash1.txt` into Johnny and ran the built-in wordlist attack against it. It cracked instantly, reporting **100% (1/1 cracked, 0 left)**:

![Cracked result in Johnny](Screenshot%202026-11-20%20111852.png)

**Recovered password:** `password1`

### 5. Confirm with Networkwalks' Password Cracker tool

Ran the same hash through Networkwalks' online cracking tool as a second confirmation — same result, `password1`, matched against its built-in wordlist:

![Password Cracker attack](Screenshot%202026-11-20%20113305.png)
![Password cracked successfully](Screenshot%202026-11-20%20113324.png)

### 6. Unlock the PDF

Entered the recovered password at the original prompt:

![Password prompt](Screenshot%202026-11-20%20111942.png)

The file opened, confirming a correct crack, and revealed the lab's completion flag:

![Flag captured](Screenshot%202026-11-20%20111959.png)

```
nw{networkwalks_flag1_jtr_...}
```

## Key takeaways

- A PDF's "open password" encryption reduces to a `$pdf$...` hash that John the Ripper / hashcat can attack directly — no need to interact with the PDF reader at all.
- `John the Ripper (jumbo build)` is required for PDF format support; the core/"proper" John build does not include it.
- `password1` being crackable almost instantly is a reminder of how weak, dictionary-style passwords fail immediately against even a small built-in wordlist.

## Disclaimer

This writeup is for educational purposes as part of a structured cybersecurity training lab. Only attempt password recovery on files and systems you own or are explicitly authorized to test.
