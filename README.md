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

## 🐪 camel - File Renaming Utility

Renames files in the current directory by splitting camelCase, PascalCase, and other compound words into readable, spaced formats.

### Features

- **Smart Splitting**:
  - Handles camelCase, PascalCase, underscores (`_`), hyphens (`-`), and spaces.
  - Preserves file extensions.
  - Splits on capital letters and numbers intelligently.
- **Word Detection**:
  - Uses NLTK’s English word corpus and WordNet to identify valid words.
  - Common words like "and", "the", "of" are always treated as valid.
- **Automatic Renaming**:
  - Processes all files in the current directory (ignores hidden files).
  - Renames files in-place with clear logging.

### Setup
1. Install dependencies:
   ```bash
   pip3 install nltk
   ```
2. Download NLTK data:
   ```bash
   python3 -m nltk.downloader words wordnet
   ```

### Usage
Run the script in the directory containing the files you want to rename:
```bash
./camel
```

### Examples
Before running the script:
```plaintext
Anti-OedipusCapitalismandSchizophrenia_ep7.aax
TheDawnofEverythingANewHistoryofHumanity_ep7.aax
TheWeirdandtheEerie_ep7.aax
```

After running the script:
```plaintext
Anti Oedipus Capitalism and Schizophrenia ep 7.aax
The Dawn of Everything A New History of Humanity ep 7.aax
The Weird and the Eerie ep 7.aax
```

### Notes
- Hidden files (starting with `.`) are skipped.
- If a word isn’t found in the dictionary, it’s left unchanged.
- File extensions are preserved during renaming.

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

## 📏 linecount - Line Counting Tool for Text Files

Recursively counts the total lines in all text files within the current directory, with optional filtering by file extensions.

### Usage
```bash
linecount [<extension1> <extension2> ...]
```

### Examples
```bash
linecount            # Count lines in all non-binary files
linecount .py .sh    # Count lines only in .py and .sh files
```

### Key Features
- **Recursive Search**: Processes files in the current directory and all subdirectories.
- **Binary File Detection**: Automatically skips binary files.
- **File Extension Filtering**: Optionally count lines in specific file types (case-insensitive).
- **Quick Stats**: Displays the number of files scanned and total lines.

### Notes
- If no extensions are provided, all non-binary files are counted.
- Use absolute or relative paths when running the script in custom environments.

--- 

## 🔄 up - Bulk Repository Updater

Automatically pulls, stages, commits, and pushes changes for multiple Git repositories.

### Setup

1. **Create a Repository List**  
   Add repository paths to `~/myrepos.txt`, one per line:
   ```plaintext
   ~/workshop/sijapi
   ~/workshop/scripts/gitea/pathScripts
   ~/workshop/Nova/Themes/Neonva/neonva.novaextension
   ~/workshop/scripts/Swiftbar
   ```

2. **Run the Script**  
   ```bash
   ./up
   ```

### Features

- Pulls the latest changes from each repository.
- Stages and commits local changes.
- Pushes updates to the current branch.
- Automatically configures the `origin` remote if missing.

### Notes
- Skips directories that aren’t Git repositories.
- Commit messages are auto-generated as `Auto-update: <timestamp>`.

---

## 🔒 vpn - Tailscale Exit Node Manager

Privacy-focused Tailscale exit node management with automated logging.

### Setup
```bash
pip3 install requests
```

### Usage
```bash
vpn <action> [<country>]  # Actions: start, stop, new, shh, to, status
```

### Actions
- **`start`**: Connect to a suggested exit node if not already connected.
- **`stop`**: Disconnect from the current exit node.
- **`new`**: Switch to a new suggested exit node.
- **`shh`**: Connect to a random exit node in a privacy-friendly country.
- **`to <country>`**: Connect to a random exit node in a specific country.
- **`status`**: Display the current exit node, external IP, and connection duration.

### Features
- **Privacy-Friendly Quick Selection**: Supports random exit nodes from:
  `Finland`, `Germany`, `Iceland`, `Netherlands`, `Norway`, `Sweden`, `Switzerland`.
- **Connection Verification**: Ensures exit node and IP via Mullvad API.
- **Automated Logging**: Tracks all connections, disconnections, and IP changes in `/var/log/vpn_rotation.txt`.
- **Default Tailscale arguments**:
  - `--exit-node-allow-lan-access`
  - `--accept-dns`
  - `--accept-routes`

### Examples
```bash
vpn start         # Connect to a suggested node.
vpn shh           # Connect to a random privacy-friendly node.
vpn to Germany    # Connect to a random exit node in Germany.
vpn status        # Show current connection details.
vpn stop          # Disconnect from the exit node.
```

### Notes
- Requires active Tailscale configuration and internet access.
- Logging is handled automatically in `/var/log/vpn_rotation.txt`.
- Use `sudo` for actions requiring elevated permissions (e.g., `crontab`).

---

_More scripts will be documented as they're updated. Most scripts include `--help` for basic usage information._
