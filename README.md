# Cracking a Password-Protected PDF with John the Ripper

A walkthrough of extracting a crackable hash from a password-protected PDF and recovering the password using **John the Ripper (Johnny GUI)**, completed as part of a hands-on lab from Networkwalks Academy. Done in an isolated Windows 11 VM (VirtualBox) against a PDF provided for this exercise.

> ⚠️ **Scope note:** This was performed against a training file I was explicitly given for practice. Don't run these steps against PDFs or systems you don't own or have permission to test.

## Goal

Recover the open-password of an encrypted PDF (`My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf`, 65.2 KB) using only the hash extracted from the file — no brute-forcing the UI, no guessing.

## Tools used

- **Johnny** (GUI front-end for John the Ripper) — `John the Ripper 1.9.0-jumbo-1`
- A browser-based PDF hash extractor (pdf2john/pdf2hashcat-compatible output)
- Windows 11 VM in Oracle VirtualBox (isolated lab environment)

## Steps

### 1. Confirm the PDF is encrypted and extract its hash

Opening the PDF directly prompts for a password:

![Password prompt](images/06-johnny-hash-loaded.png)

Rather than guessing, I ran the file through a hash-extraction tool that parses the PDF locally and outputs a `pdf2john`/hashcat-compatible hash:

![Hash calculator tool](images/10-networkwalks-hash-calculator.png)
![PDF hash extracted](images/11-networkwalks-pdf-hash-page.png)

This confirmed the encryption parameters:

```
Revision: R4   Version: V4   Key length: 128-bit
```

And produced the extractable hash:

```
$pdf$4*4*128*-1060*1*16*55d1a5c14175da449753199e44971d32*32*777fd021a7f3c5ae598c0c6495c7f76e0000000000000000000000000000000*32*ceecdac74b19b5a62688d3b3524e1374c955cbb9cc3c45316494d9446ef81af1
```

### 2. Save the hash to a file

Saved the extracted hash as `hash1.txt`, ready for John the Ripper's `--format=PDF` mode:

![Hash saved to file](images/04-hash-saved-to-file.png)

### 3. Point Johnny at the John the Ripper binary

Configured Johnny's **Settings** tab to use the jumbo build of John the Ripper (needed for the wider range of supported hash formats, including PDF):

![Johnny settings](images/05-johnny-settings-john-path.png)

### 4. Load the hash and run the attack

Loaded `hash1.txt` into Johnny and kicked off the cracking session:

![Hash loaded in Johnny](images/07-johnny-password-field.png)

### 5. Password cracked

Johnny recovered the password in seconds, flagging a **100% (1/1 cracked, 0 left)** result:

![Cracked result](images/08-johnny-cracked-result.png)
![Cracked confirmed](images/09-johnny-cracked-confirmed.png)

**Recovered password:** `password1`

### 6. Verify by opening the PDF

Entered the recovered password at the original prompt — the file unlocked successfully, confirming a correct crack:

![Password cracker UI confirmation](images/13-password-cracked-success.png)

### 7. Flag captured

The unlocked PDF contained the lab's completion flag:

```
nw{networkwalks_flag1_jtr_...}
```

## Key takeaways

- A PDF's "open password" encryption can be reduced to a `$pdf$...` hash format that John the Ripper / hashcat can attack directly — no need to interact with the PDF reader at all.
- `John the Ripper (jumbo build)` is required for PDF format support; the core/"proper" John build does not include it.
- `password1` being crackable almost instantly is a reminder of how weak, dictionary-style passwords fail immediately against even a small built-in wordlist.

## Disclaimer

This writeup is for educational purposes as part of a structured cybersecurity training lab. Only attempt password recovery on files and systems you own or are explicitly authorized to test.
