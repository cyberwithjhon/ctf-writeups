# Aliens Box - HackRocks CTF

## Description
We are given an image that looks completely normal at first glance.
The challenge is to find a hidden message inside it.

<img width="769" height="404" alt="kali-linux  Corriendo  - Oracle VirtualBox 22_05_2026 16_59_26" src="https://github.com/user-attachments/assets/c8c1149a-d197-4c1a-9fb9-aba8dc12be67" />

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
<img width="1920" height="974" alt="kali-linux  Corriendo  - Oracle VirtualBox 22_05_2026 16_59_26" src="https://github.com/user-attachments/assets/6a390bce-1106-4d42-a7eb-cde727f5bedb" />

**Result:** Nothing relevant found in the metadata.

### Step 2 - Hidden file detection
```bash
binwalk intercepted_transmission.png
```

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
