# Simple Huffman Steganography in BMP Images

This project implements a basic steganography tool that hides a secret file within a BMP image using Huffman coding for compression and Least Significant Bit (LSB) insertion. It provides a simple interactive Command Line Interface (CLI) for encoding and decoding operations.

## Description

The program allows a user to:
1.  **Encode:** Take a "cover" BMP image and a "secret" file (of any type), compress the secret file using Huffman coding, and then hide the compressed data along with necessary metadata (original size, frequency table) into the least significant bits of the cover image's pixel data. The result is a new "stego" BMP image that looks very similar to the original cover image.
2.  **Decode:** Take a "stego" BMP image (created by this tool), extract the hidden metadata and compressed data from the LSBs, rebuild the Huffman tree, decompress the data, and save it back as the original secret file.

This implementation focuses on clarity and understanding the core concepts rather than efficiency or robust error handling, making it suitable for educational purposes or college projects.

## Features

*   Hide any type of file (text, image, executable, etc.) inside a BMP image.
*   Extract the hidden file from the stego BMP image.
*   Uses Huffman coding to compress the secret data before hiding, reducing the required space in the cover image.
*   Employs Least Significant Bit (LSB) steganography to embed data.
*   Simple interactive Command Line Interface (CLI) for ease of use.

## How it Works

### Encoding Process

1.  **Read Secret File:** The entire secret file is read into memory as raw bytes.
2.  **Calculate Frequencies:** The frequency of each byte (0-255) in the secret file is calculated.
3.  **Build Huffman Tree:** A Huffman tree is constructed based on the byte frequencies. Bytes appearing more often get shorter codes.
4.  **Generate Codes:** Huffman codes (sequences of '0's and '1's) are generated for each byte present in the secret file by traversing the tree.
5.  **Compress Data:** The secret file is read again, and each byte is replaced by its corresponding Huffman code, creating a stream of bits (represented inefficiently as an array of 0s and 1s in this simple version).
6.  **Read Cover Image:** The cover BMP image is opened.
7.  **Write Header:** The 54-byte header of the cover BMP is copied directly to the output stego BMP file.
8.  **Embed Metadata:**
    *   The original size of the secret file (32 bits) is embedded into the LSBs of the subsequent pixels.
    *   The frequency table (256 counts, each 32 bits) is embedded into the LSBs.
9.  **Embed Compressed Data:** The stream of compressed bits generated in step 5 is embedded into the LSBs.
10. **Copy Remaining Pixels:** Any remaining pixel data from the cover image (after the hidden data) is copied directly to the output stego BMP file.
11. **Save Stego Image:** The output file now contains the hidden data.

### Decoding Process

1.  **Read Stego Image:** The stego BMP image is opened.
2.  **Skip Header:** The 54-byte header is skipped.
3.  **Extract Metadata:**
    *   The original secret file size (32 bits) is read from the LSBs.
    *   The frequency table (256 * 32 bits) is read from the LSBs.
4.  **Rebuild Huffman Tree:** A Huffman tree is reconstructed using the extracted frequency table (identical to the one used for encoding).
5.  **Decode Data:** Bits are read one by one from the LSBs of the subsequent pixels. These bits are used to traverse the rebuilt Huffman tree from the root. When a leaf node is reached, the corresponding byte is recovered. This process repeats until the number of bytes specified by the extracted file size has been recovered.
6.  **Write Output File:** The recovered bytes are written to the specified output file, reconstructing the original secret file.

## Prerequisites

*   A C compiler (like GCC or Clang)
*   Standard C libraries (stdio.h, stdlib.h, string.h)

The code is platform-independent but typically compiled and run on Linux, macOS, or Windows (using MinGW, Cygwin, or WSL).

## Compilation

Open a terminal or command prompt in the directory containing the source code file (`huffman_stego_simple.c`) and run:

```bash
gcc huffman_stego_simple.c -o huffman_stego_simple
