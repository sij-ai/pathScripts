# PATH-worthy Scripts

Hey folks, this repo is just a collection of various scripts I use frequently enough to justify keeping them in my system PATH. I haven't written documentation for all of these scripts. I might in time. For now, here's just a few highlights.

# bates: PDF Bates Number Extractor & File Renamer

A simple utility for extracting Bates numbers from PDF documents and optionally renaming files based on those numbers. Particularly useful for organizing legal documents or any PDFs with sequential numbering.

## Overview

This tool helps you:
- Extract Bates numbers from PDFs (both text-based and scanned documents)
- Rename files based on their Bates number range
- Preserve original filenames in macOS Finder comments
- Process entire folders of PDFs in one go
- Prepare files for use with my [Bates Source Link](https://sij.ai/sij/DEVONthink/src/branch/main/Bates%20Source%20Link.scpt$0) DEVONthink script

## Installation

1. Install Python dependencies:
```bash
pip3 install pdfplumber
```

If you plan to use OCR capabilities, also install:
```bash
pip3 install pytesseract pdf2image
```

2. For OCR support, install system dependencies:

On macOS:
```bash
brew install tesseract poppler
```

On Ubuntu/Debian:
```bash
sudo apt-get install tesseract-ocr poppler-utils
```

## Basic Usage

Test extraction without renaming files:
```bash
python3 bates.py /path/to/folder --prefix "FWS-" --digits 6 --dry-run
```

Rename files based on Bates numbers:
```bash
python3 bates.py /path/to/folder --prefix "FWS-" --digits 6 --name-prefix "FWS "
```

## Options

- `--prefix`: The Bates number prefix to search for (default: "FWS-")
- `--digits`: Number of digits after the prefix (default: 6)
- `--ocr`: Enable OCR for scanned documents
- `--dry-run`: Test extraction without renaming files
- `--name-prefix`: Prefix to use when renaming files
- `--log`: Set logging level (DEBUG, INFO, WARNING, ERROR, CRITICAL)

## Notes

- Always test with `--dry-run` first
- Original filenames are preserved in Finder comments (macOS only)
- OCR is disabled by default to keep things fast

## Questions or Issues?

Feel free to open an issue on GitHub if you run into any problems or have suggestions for improvements.