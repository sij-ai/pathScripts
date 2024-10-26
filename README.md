# PATH-worthy Scripts

Hey folks, this repo is just a collection of various scripts I use frequently enough to justify keeping them in my system PATH. I haven't written documentation for all of these scripts. I might in time. For now, here's just a few highlights.

## bates

A simple Python-based utility for extracting Bates numbers from PDF documents and optionally renaming files based on those numbers. Particularly useful for organizing legal documents or any PDFs with sequential numbering.

### Overview

This tool helps you:
- Extract Bates numbers from PDFs (both text-based and scanned documents)
- Rename files based on their Bates number range
- Preserve original filenames in macOS Finder comments
- Process entire folders of PDFs in one go
- Prepare files for use with my [Bates Source Link](https://sij.ai/sij/DEVONthink/src/branch/main/Bates%20Source%20Link.scpt$0) DEVONthink script

### Installation

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

### Basic Usage

Test extraction without renaming files:
```bash
python3 bates.py /path/to/folder --prefix "FWS-" --digits 6 --dry-run
```

Rename files based on Bates numbers:
```bash
python3 bates.py /path/to/folder --prefix "FWS-" --digits 6 --name-prefix "FWS "
```

### Options

- `--prefix`: The Bates number prefix to search for (default: "FWS-")
- `--digits`: Number of digits after the prefix (default: 6)
- `--ocr`: Enable OCR for scanned documents
- `--dry-run`: Test extraction without renaming files
- `--name-prefix`: Prefix to use when renaming files
- `--log`: Set logging level (DEBUG, INFO, WARNING, ERROR, CRITICAL)

### Notes

- Always test with `--dry-run` first
- Original filenames are preserved in Finder comments (macOS only)
- OCR is disabled by default to keep things fast




## vpn

A command-line utility for managing Tailscale exit nodes with privacy-focused features.

### Overview

This tool helps you:
- Start and stop Tailscale exit nodes
- Select new exit nodes automatically
- Choose random exit nodes from privacy-friendly countries
- Verify successful connections using Mullvad's API

### Requirements

- Python 3
- Tailscale installed and configured
- `requests` Python package

Install dependencies:
```bash
pip3 install requests
```

### Usage

```bash
python3 vpn.py <action>
```

#### Available Actions

- `start`: Connect to a suggested exit node if not already connected
```bash
python3 vpn.py start
```

- `stop`: Disconnect from current exit node
```bash
python3 vpn.py stop
```

- `new`: Switch to a new suggested exit node
```bash
python3 vpn.py new
```

- `shh`: Connect to a random exit node in a privacy-friendly country
```bash
python3 vpn.py shh
```

### Privacy Features

The script considers the following countries as privacy-friendly for the `shh` command:
- Sweden
- Switzerland
- Germany
- Finland
- Netherlands
- Norway

### Configuration

The script uses these default Tailscale arguments:
- `--exit-node-allow-lan-access`: Allow access to LAN while using exit node
- `--accept-dns`: Use DNS settings from the exit node
- `--accept-routes`: Accept advertised routes from the exit node

### Verification

The script verifies successful connections by:
1. Checking Tailscale's reported exit node
2. Verifying the connection using Mullvad's API
3. Confirming the hostnames match

### Examples

Start using an exit node:
```bash
$ python3 vpn.py start
Suggested exit node: stockholm.ts.net
Current exit node hostname: stockholm
Exit node set successfully!
```

Switch to a privacy-focused node:
```bash
$ python3 vpn.py shh
Selected random privacy-friendly exit node: zurich.ts.net
Current exit node hostname: zurich
Exit node set successfully!
```

### Notes

- The script requires an active Tailscale configuration
- Internet connectivity is required for verification
- Some commands may require administrative privileges
