# PATH-worthy Scripts 🛠️

A collection of useful scripts for your system PATH. Here's how to get started:

## Installation

```bash
# Clone the repository
git clone https://sij.ai/sij/pathScripts.git

# Add to PATH on macOS
echo 'export PATH="$PATH:$HOME/path/to/pathScripts"' >> ~/.zshrc
source ~/.zshrc

# Add to PATH on Debian/Ubuntu
echo 'export PATH="$PATH:$HOME/path/to/pathScripts"' >> ~/.bashrc
source ~/.bashrc
```

Make scripts executable:
```bash
cd pathScripts
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

_More scripts will be documented as they're updated. Each script includes `--help` for detailed usage information._
