# Aliens Box - HackRocks CTF

## Description
We are given an image that looks completely normal at first glance.
The challenge is to find a hidden message inside it.

<img width="400" height="200" alt="intercepted_transmission" src="https://github.com/user-attachments/assets/6334a0d0-1da8-4526-a35d-f895eb3c2009" />

**Category:** Steganography  
**Platform:** HackRocks  
**Difficulty:** Easy 
**Flag:** `FLAG{NourAlpha8_Invasi0n_Pl4ns_2023}`

---

## Tools used
- `exiftool` → metadata analysis
- `binwalk` → hidden file detection
- `zsteg` → LSB steganography detection for PNG files

---

## Process

### Step 1 - Metadata analysis
First thing I always do is check the metadata:

```bash
exiftool intercepted_transmission.png
```
<img width="769" height="404" alt="kali-linux  Corriendo  - Oracle VirtualBox 22_05_2026 16_59_26" src="https://github.com/user-attachments/assets/3af29de6-46c3-483d-be2c-34614df93586" />

**Result:** Nothing relevant found in the metadata.

### Step 2 - Hidden file detection
```bash
binwalk intercepted_transmission.png
```
<img width="759" height="194" alt="kali-linux  Corriendo  - Oracle VirtualBox 22_05_2026 17_00_18" src="https://github.com/user-attachments/assets/67c63743-ba03-482d-8b86-570f874c64f7" />

**Result:** Only the normal PNG structure was detected. Nothing hidden.

### Step 3 - LSB Steganography
Since nothing was found with the previous methods, I tried zsteg,
which analyzes the least significant bits of each pixel:

```bash
zsteg -a intercepted_transmission.png
```

**Result:** The flag was hidden in the LSB bits of the RGB channels.

---

## Technical explanation

The technique used is called **LSB (Least Significant Bit)**.

Every pixel in a PNG image contains colors in RGB format.
Each color channel is represented with 8 bits.
The last bit (the least significant one) barely affects
the color visually.

The difference is imperceptible to the human eye, but by
concatenating all those hidden bits, a complete message
can be reconstructed.

---

## Lessons learned

When an image reveals nothing in metadata or surface analysis,
LSB steganography is the next step.

The correct methodology for this type of challenge:
1. Metadata → `exitfool`
2. Hidden files → `binwalk`
3. Steganography → `zsteg`, `steghide`

---

## References
- [zsteg GitHub](https://github.com/zed-0xff/zsteg)
- [Steganography - Wikipedia](https://en.wikipedia.org/wiki/Steganography)
- [HackRocks](https://hackrocks.com)
