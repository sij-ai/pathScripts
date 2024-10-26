# PATH-worthy Scripts 🛠️

A collection of various scripts I use frequently enough to justify keeping them in my system PATH. 

I haven't written documentation for all of these scripts. I might in time. Find documentation for some of the highlights below.

## Installation

1. Clone and enter repository:

```bash
git clone https://sij.ai/sij/pathScripts.git
cd pathScripts
```

2. Add to your system PATH:

macOS / ZSH:
```bash
echo "export PATH=\"\$PATH:$PWD\"" >> ~/.zshrc
source ~/.zshrc
```

Linux / Bash:
```bash
echo "export PATH=\"\$PATH:$PWD\"" >> ~/.bashrc
source ~/.bashrc
```

3. Make scripts executable:

```bash
chmod +x *
```

---

## 📄 bates - PDF Bates Number Tool

Extracts and renames PDFs based on Bates numbers.

### Setup
```bash
pip3 install pdfplumber
# For OCR support:
pip3 install pytesseract pdf2image
brew install tesseract poppler  # macOS
# or
sudo apt-get install tesseract-ocr poppler-utils  # Debian
```

### Usage
```bash
bates /path/to/folder --prefix "FWS-" --digits 6 --name-prefix "FWS "
```

### Key Features
- Extracts Bates numbers from text/scanned PDFs
- Renames files with number ranges
- Prepare files for use with my [Bates Source Link](https://sij.ai/sij/DEVONthink/src/branch/main/Bates%20Source%20Link.scpt#) DEVONthink script
- Preserves original names in Finder comments
- OCR support for scanned documents
- Dry-run mode with `--dry-run`

### Options
- `--prefix`: The Bates number prefix to search for (default: "FWS-")
- `--digits`: Number of digits after the prefix (default: 6)
- `--ocr`: Enable OCR for scanned documents
- `--dry-run`: Test extraction without renaming files
- `--name-prefix`: Prefix to use when renaming files
- `--log`: Set logging level (DEBUG, INFO, WARNING, ERROR, CRITICAL)

### Examples
```bash
# Test without making changes
bates /path/to/pdfs --prefix "FWS-" --digits 6 --dry-run

# Rename files with OCR support
bates /path/to/pdfs --prefix "FWS-" --digits 6 --name-prefix "FWS " --ocr
```

### Notes
- Always test with `--dry-run` first
- Original filenames are preserved in Finder comments (macOS only)
- OCR is disabled by default to keep things fast

---


## 📦 kip - Intelligent Python Package Installer

A smart package installer that automates Python dependency management, supporting both mamba and pip.

### Setup
```bash
chmod +x kip
```

### Usage
```bash
kip <package1> [<package2> ...]  # Install specific packages
kip <script.py>                  # Install from Python file
kip -r <requirements.txt>        # Install from requirements file
```

### Key Features
- Smart import detection in Python files
- Handles package name corrections automatically
- Uses mamba first, falls back to pip
- Supports multiple installation methods
- Filters out built-in modules

### Examples
```bash
# Install single package
kip requests

# Analyze script and install dependencies
kip analysis.py

# Install from requirements
kip -r requirements.txt
```

### Package Corrections
Automatically corrects common package names:
- `yaml` → `pyyaml`
- `dateutil` → `python-dateutil`
- `dotenv` → `python-dotenv`
- `newspaper` → `newspaper3k`

### Notes
- Requires Python 3.x
- Works with mamba or pip
- Provides clear installation feedback
- Ignores built-in modules and generic names

---


## 🔒 vpn - Tailscale Exit Node Manager

Privacy-focused Tailscale exit node management.

### Setup
```bash
pip3 install requests
```

### Usage
```bash
vpn <action>  # actions: start, stop, new, shh
```

### Actions
- `start`: Connect to a suggested exit node if not already connected
- `stop`: Disconnect from current exit node
- `new`: Switch to a new suggested exit node
- `shh`: Connect to a random exit node in a privacy-friendly country

### Key Features
- Auto-connects to suggested nodes
- Privacy-friendly country selection (SE, CH, DE, FI, NL, NO)
- Connection verification via Mullvad API
- Default Tailscale arguments:
  - `--exit-node-allow-lan-access`
  - `--accept-dns`
  - `--accept-routes`

### Examples
```bash
vpn start  # Connect to suggested node
vpn shh    # Connect via privacy-friendly country
```

### Verification
- Checks Tailscale's reported exit node
- Verifies connection using Mullvad's API
- Confirms hostname matches

### Notes
- Requires active Tailscale configuration
- Internet connectivity needed for verification
- Some commands may need admin privileges

---

_More scripts will be documented as they're updated. Most scripts include `--help` for basic usage information._
