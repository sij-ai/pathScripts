# PATH-worthy Scripts 🛠️

A collection of various scripts I use frequently enough to justify keeping them in my system PATH. 

I haven't written documentation for all of these scripts. I might in time. Find documentation for some of the highlights below.

## Installation

1. Clone and enter repository:

```bash
git clone https://sij.ai/sij/pathScripts.git
cd pathScripts
```

2. Install all Python dependencies (optional; can also install dependencies for scripts individually):

```bash
pip install -r requirements.txt
```

3. Add to your system PATH:

ZSH (macOS default):
```bash
echo "export PATH=\"\$PATH:$PWD\"" >> ~/.zshrc
source ~/.zshrc
```

Bash:
```bash
echo "export PATH=\"\$PATH:$PWD\"" >> ~/.bashrc
source ~/.bashrc
```

---

## 📄 `bates` - PDF Bates Number Tool

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

## 🐪 `camel` - File Renaming Utility

Renames files in the current directory by splitting camelCase, PascalCase, and other compound words into readable, spaced formats.

### Features

- **Smart Splitting**:
  - Handles camelCase, PascalCase, underscores (`_`), hyphens (`-`), and spaces.
  - Preserves file extensions.
  - Splits on capital letters and numbers intelligently.
- **Word Detection**:
  - Uses NLTK's English word corpus and WordNet to identify valid words.
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
camel
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
- If a word isn't found in the dictionary, it's left unchanged.
- File extensions are preserved during renaming.

---

## 📋 `codebase` - Source Code Documentation Generator

Recursively scans directories and generates markdown-formatted documentation of your codebase, suitable for providing context to LLMs or creating project documentation.

### Features

- **Smart File Detection**:
  - Automatically detects and processes text files
  - Skips binary files and common build artifacts
  - Respects `.gitignore` patterns
  - Excludes common directories (node_modules, .git, __pycache__, etc.)

- **Markdown Output**:
  - Generates clean markdown with proper syntax highlighting
  - Organizes files with headers showing relative paths
  - Uses appropriate language identifiers for code blocks

- **Flexible Filtering**:
  - Filter by file extensions (e.g., `.py`, `.js`)
  - Custom directory exclusions with `--exclude`
  - Optional gitignore bypass with `--no-gitignore`

### Usage
```bash
codebase [extensions...] [--exclude dir1 dir2...] [--no-gitignore]
```

### Examples
```bash
# Document all text files in current directory
codebase

# Only Python and JavaScript files
codebase .py .js

# Exclude specific directories
codebase --exclude tests docs

# Process everything, ignoring .gitignore
codebase --no-gitignore

# Python files, excluding venv and tests
codebase .py --exclude venv tests
```

### Output Format
```markdown
# Code Contents for myproject

## ./src/main.py

​```python
import sys

def main():
    print("Hello, world!")
​```

## ./src/utils.py

​```python
def helper():
    return True
​```
```

### Notes
- Outputs to **stdout** - redirect to a file if needed: `codebase > docs.md`
- Summary information printed to **stderr** (won't pollute redirected output)
- Default exclusions include: `node_modules`, `.git`, `__pycache__`, `venv`, `.env`, `build`, `dist`, `.next`, `.nuxt`, `target`, `bin`, `obj`
- Perfect for creating context files for Claude, ChatGPT, or other LLMs

--- 

## 📦 `deps` - Python Dependency Manager with Smart Detection

Automatically discovers, manages, and installs Python dependencies by analyzing import statements in your code. Detects Python files by both `.py` extension and shebang, making it perfect for scripts in your PATH.

### Features

- **Smart Python File Detection**:
  - Finds `.py` files
  - Detects Python scripts by shebang (`#!/usr/bin/env python3`)
  - Reads first 64KB safely to identify Python files

- **Accurate Import Parsing**:
  - Uses Python's AST parser for reliable import detection
  - Filters out built-in modules automatically
  - Applies known package name corrections (e.g., `bs4` → `beautifulsoup4`)
  - Validates module names to avoid false positives

- **Flexible Installation**:
  - Tries `mamba` first (if in conda environment)
  - Falls back to `conda` if mamba unavailable
  - Uses `pip` as final fallback or with `--no-conda`
  - Checks if packages already installed before attempting installation

- **Clean Output**:
  - Generates sorted, deduplicated `requirements.txt`
  - Separates unavailable packages into `missing-packages.txt`
  - Checks PyPI availability before categorizing

### Usage
```bash
deps <subcommand> [options]
```

#### Subcommands

**`deps ls`** - List and categorize imports

```bash
deps ls              # Scan current directory (non-recursive)
deps ls -r           # Recursively scan current directory  
deps ls src          # Scan specific directory
deps ls -r src       # Recursively scan specific directory
deps ls script.py    # Analyze single file
```

**`deps install`** - Install dependencies

```bash
deps install                    # Auto-scan and install (current dir)
deps install -r                 # Auto-scan and install (recursive)
deps install requests flask     # Install specific packages
deps install script.py          # Install imports from a script
deps install -R requirements.txt # Install from requirements file
deps install requests --no-conda # Force pip-only installation
```

### How It Works

1. **File Discovery**: Recursively finds Python files by extension or shebang
2. **Import Extraction**: Uses AST parsing to accurately identify imports
3. **Filtering**: Removes built-in modules and applies name corrections
4. **PyPI Validation**: Checks package availability on PyPI
5. **Categorization**: Sorts into `requirements.txt` (available) and `missing-packages.txt` (unavailable)
6. **Installation**: Intelligently chooses mamba/conda/pip based on environment

### Examples

```bash
# Generate requirements from all Python scripts in your PATH directory
cd ~/scripts
deps ls -r

# Install all detected dependencies
deps install -r

# Quick workflow for a new script
deps install myscript.py

# Update requirements when you add imports
deps ls -r  # Automatically sorts and deduplicates
```

### Notes
- Multiple runs of `deps ls` append new packages and maintain alphabetical sorting
- Import name corrections handled automatically (see `KNOWN_CORRECTIONS` in source)
- Respects conda environment - won't use conda commands outside conda environments
- Built-in modules list includes `__future__` and all standard library modules

---

## 🎵 `flacsplit` - FLAC/CUE Splitting Tool

Splits FLAC audio files using CUE sheets into individual tracks with comprehensive validation and error handling.

### Setup
```bash
brew install shntool flac  # macOS
# or
sudo apt-get install shntool flac  # Debian/Ubuntu
```

### Usage
```bash
flacsplit [OPTIONS] [directory]
```

### Key Features
- **Smart Processing**:
  - Validates CUE sheets before processing
  - Checks available disk space
  - Cleans up on failure
  - Progress tracking with colored output

- **Flexible Options**:
  - Custom track naming formats
  - Dry-run mode to preview operations
  - Optional deletion of source files
  - Force overwrite of existing output

- **Safety Features**:
  - CUE file validation (optional)
  - Disk space verification
  - Existing directory protection
  - Automatic cleanup on errors

### Options
```
-f, --force             Overwrite existing output directories
-v, --verbose           Enable verbose output
-n, --dry-run           Preview operations without executing
-t, --format FORMAT     Track naming format (default: "%n - %t")
-d, --delete            Delete original FLAC/CUE after successful split
--skip-validation       Skip CUE file validation
-h, --help              Show help message
--version               Show version information
```

### Examples
```bash
# Preview what would happen
flacsplit -n ~/Music/Albums

# Process with custom track naming
flacsplit -t "%02n. %t" ~/Music/Albums

# Verbose mode with force overwrite
flacsplit -f -v ~/Music/Albums

# Delete originals after splitting
flacsplit -d ~/Music/Albums

# Skip CUE validation for problematic files
flacsplit --skip-validation ~/Music/Albums
```

### Track Naming Format
Use standard `shnsplit` format codes:
- `%n` - Track number
- `%t` - Track title
- `%a` - Artist name
- `%p` - Performer

Example: `"%02n. %a - %t"` → `01. Artist - Title.flac`

### Notes
- Default output: `<album>_tracks/` subdirectory
- CUE files must have the same base name as the FLAC file
- Always test with `-n` (dry-run) first
- Original files are preserved by default

---

## 🔄 `get` - Git Clone with Auto-Environment Setup

Clones a git repository and automatically creates a conda/mamba environment with dependencies installed.

### Features
- Clones any git repository
- Creates conda environment named after the repo
- Auto-installs from `setup.py` if present
- Auto-installs from `requirements.txt` if present
- Activates the environment after setup

### Usage
```bash
get <repository_url>
```

### Examples
```bash
# Clone and set up a Python project
get https://github.com/user/project.git

# Clone from your own git server
get https://git.sij.ai/sij/myproject.git
```

### How It Works
1. Clones the repository to current directory
2. Checks for `setup.py` or `requirements.txt`
3. If found, creates new mamba environment with repo name
4. Activates the new environment
5. Installs dependencies via `python setup.py install` (if setup.py exists)
6. Installs dependencies via `pip install -r requirements.txt` (if requirements.txt exists)

### Notes
- Requires `mamba` (or `conda`) to be installed
- Environment name matches repository name (e.g., `myproject.git` → `myproject`)
- Skips environment setup if no `setup.py` or `requirements.txt` found
- Useful for quickly cloning and getting started with Python projects

---

## 📁 `gitscan` - Git Repository Scanner

Recursively scans for git repositories and creates a consolidated list. Perfect for setting up bulk repository management with `push` and `pull` scripts.

### Features
- Finds all git repositories in current directory and subdirectories
- Excludes hidden directories
- Removes duplicate entries automatically
- Outputs to `repos.txt` in current directory

### Usage
```bash
gitscan
```

### Output
Creates `repos.txt` with repository paths:
```plaintext
./project1
./subfolder/project2
./workshop/project3
```

### How It Works
1. Searches for `.git` directories recursively
2. Extracts parent directory paths
3. Sorts and deduplicates
4. Writes to `repos.txt`

### Integration with `push`/`pull`
After running `gitscan`, move the output to your home directory:
```bash
gitscan
mv repos.txt ~/.repos.txt
```

Now `push` and `pull` scripts will manage all discovered repositories!

### Examples
```bash
# Scan your projects directory
cd ~/projects
gitscan

# Scan and create master repo list
cd ~/
gitscan
mv repos.txt .repos.txt  # For use with push/pull
```

### Notes
- Only searches visible directories (skips paths like `./.git/subdir/.git`)
- Output file is always in current directory
- Overwrites existing `repos.txt` without prompting

---

## 📏 `linecount` - Line Counting Tool for Text Files

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

## 🔪 `murder` - Force-Kill Processes by Name or Port

A utility script to terminate processes by their name or by the port they are listening on:
- If the argument is **numeric**, the script will terminate all processes listening on the specified port.
- If the argument is **text**, the script will terminate all processes matching the given name.

### Usage Examples
```bash
# Kill all processes listening on port 8080
sudo murder 8080

# Kill all processes with "node" in their name
sudo murder node
```

### Features & Notes
- Automatically detects whether the input is a **port** or a **process name**.
- Uses `lsof` to find processes listening on a specified port.
- Finds processes by name using `ps` and kills them using their process ID (PID).
- Ignores the `grep` process itself when searching for process names. 

### Notes
- Requires `sudo` privileges.
- Use with caution, as it forcefully terminates processes.

---

## 📰 `n3k` - Article Extractor and Markdown Formatter

Fetches web articles and converts them to clean, formatted markdown using dual extraction methods (trafilatura + newspaper4k) for maximum reliability.

### Setup
```bash
pip3 install trafilatura newspaper4k requests
```

### Usage
```bash
n3k <article_url>
```

### Features

- **Dual Extraction Engine**:
  - Primary: trafilatura (excellent for article text)
  - Fallback: newspaper4k (better metadata)
  - Custom headers to bypass basic blocking

- **Clean Output**:
  - Properly formatted markdown
  - Author bylines and publication dates
  - Article images included
  - Smart paragraph handling
  - Normalized whitespace

- **Metadata Extraction**:
  - Title
  - Authors (multiple supported)
  - Publication date
  - Featured image
  - Source domain

### Examples
```bash
# Extract article to stdout
n3k https://example.com/article

# Save to file
n3k https://example.com/article > article.md

# Quick read with glow
n3k https://example.com/article | glow
```

### Output Format
```markdown
# Article Title

*By Author Name*

*Published: 2025-01-15*

![Article Image](https://example.com/image.jpg)

Article content begins here with proper paragraph breaks.

Second paragraph continues...
```

### How It Works
1. Fetches URL with browser-like headers
2. Attempts extraction with trafilatura first
3. Falls back to newspaper4k if needed
4. Cleans and normalizes text (removes excessive whitespace)
5. Formats as markdown with metadata

### Notes
- Async implementation for better performance
- Custom user agent to avoid bot detection
- Handles both text content and metadata gracefully
- Works with most news sites and blogs

---

## 🔍 `ocr` - Multi-Mode OCR Processor

Intelligent OCR tool that automatically detects input type and processes PDFs or images into searchable PDFs. Handles three distinct modes seamlessly.

### Setup
```bash
pip3 install pillow pytesseract PyPDF2 pdf2image
brew install tesseract poppler  # macOS
# or
sudo apt-get install tesseract-ocr poppler-utils  # Debian/Ubuntu
```

### Usage
```bash
ocr <input_path> [OPTIONS]
```

### Three Processing Modes

#### 1. Single PDF Mode
Flattens and re-OCRs an existing PDF to make it searchable.

```bash
ocr document.pdf              # Replaces original by default
ocr document.pdf -o new.pdf   # Creates new file
```

#### 2. Folder of Images Mode
Combines JPG/JPEG images into a single searchable PDF.

```bash
ocr /path/to/images/                    # Creates images_searchable.pdf
ocr /path/to/images/ -o output.pdf      # Custom output name
```

#### 3. Batch PDF Mode
Processes multiple PDFs in a folder, creating searchable versions of each.

```bash
ocr /path/to/pdfs/                      # Creates OCRed_PDFs/ subfolder
ocr /path/to/pdfs/ -o /output/folder/   # Custom output location
ocr /path/to/pdfs/ --replace            # Overwrites originals
```

### Options
```
<input_path>        PDF file, image folder, or PDF folder (required)
-o, --output        Output path (interpretation depends on mode)
-r, --replace       Overwrite original PDF(s)
-l, --lang          OCR language (default: eng)
-t, --threads       Number of OCR threads (default: auto-detect)
-q, --quiet         Suppress output messages
```

### Examples
```bash
# Flatten and OCR a scanned PDF
ocr scanned_document.pdf

# Create searchable PDF from 50 scanned pages
ocr ~/scans/document_pages/

# Batch process a folder of PDFs with custom output
ocr ~/documents/pdfs/ -o ~/documents/searchable/

# Multi-threaded OCR with custom language
ocr document.pdf -l eng+fra -t 8

# Quiet mode for scripting
ocr input.pdf -q
```

### How It Works

**Single PDF/Image Folder:**
1. Flattens PDF pages to images at 300 DPI (or loads images directly)
2. Runs Tesseract OCR on each page/image
3. Generates searchable PDF with embedded text layer
4. Merges into single output file

**Batch Mode:**
1. Identifies all PDFs in folder
2. Processes each independently
3. Creates output folder with `_searchable` suffix for each
4. Preserves original filenames

### Performance
- Parallelized OCR processing (uses all CPU cores by default)
- Progress indicators for long operations
- Estimated time remaining on large batches

### Notes
- Default behavior varies by mode (see `--help` for details)
- Single PDF mode replaces original unless `-o` specified
- Image folder mode creates new PDF (never replaces)
- Batch mode creates new files unless `--replace` used
- Supported image formats: JPG, JPEG
- OCR quality depends on image resolution (300+ DPI recommended)

---

## 🔄 `push` & `pull` - Bulk Git Repository Management

Scripts to automate updates and management of multiple Git repositories.

### Setup

1. **Create a Repository List**  
   Add repository paths to `~/.repos.txt`, one per line:
   ```plaintext
   ~/sijapi
   ~/workshop/Nova/Themes/Neonva/neonva.novaextension
   ~/scripts/pathScripts
   ~/scripts/Swiftbar
   ```

   **Quick way:** Use `gitscan` to auto-generate the list:
   ```bash
   cd ~/projects
   gitscan
   mv repos.txt ~/.repos.txt
   ```

   - Use `~` for home directory paths or replace it with absolute paths.
   - Empty lines and lines starting with `#` are ignored.

2. **Make Scripts Executable**  
   ```bash
   chmod +x push pull
   ```

3. **Run the Scripts**  
   ```bash
   pull    # Pulls the latest changes from all repositories
   push    # Pulls, stages, commits, and pushes local changes
   ```

### Features

#### `pull`
- Recursively pulls the latest changes from all repositories listed in `~/.repos.txt`.
- Automatically expands `~` to the home directory.
- Skips directories that do not exist or are not Git repositories.
- Uses `git pull --force` to ensure synchronization.

#### `push`
- Pulls the latest changes from the current branch.
- Stages and commits all local changes with an auto-generated message: `Auto-update: <timestamp>`.
- Pushes updates to the current branch.
- Configures the `origin` remote automatically if missing, using a URL based on the directory name.

### Notes
- Both scripts assume `~/.repos.txt` is the repository list file. You can update the `REPOS_FILE` variable if needed.
- Use absolute paths or ensure `~` is correctly expanded to avoid issues.
- The scripts skip non-existent directories and invalid Git repositories.
- `push` will attempt to set the `origin` remote automatically if it is missing.
- Works perfectly with `gitscan` to auto-discover repositories

---

## 🌐 `vitals` - System and VPN Diagnostics

The `vitals` script provides detailed system diagnostics, VPN status, DNS configuration, and uptime in JSON format. It integrates with tools like AdGuard Home, NextDNS, and Tailscale for network monitoring.

### Usage
1. **Set up a DNS rewrite rule in AdGuard Home**:
   - Assign the domain `check.adguard.test` to your Tailscale IP or any custom domain.
   - Update the `adguard_test_domain` variable in the script if using a different domain.

2. **Run the script**:
   ```bash
   vitals
   ```

   Example output (JSON):
   ```json
   {
       "local_ip": "192.168.1.2",
       "wan_connected": true,
       "wan_ip": "185.213.155.74",
       "has_tailscale": true,
       "tailscale_ip": "100.100.100.1",
       "mullvad_exitnode": true,
       "mullvad_hostname": "de-ber-wg-001.mullvad.ts.net",
       "nextdns_connected": true,
       "nextdns_protocol": "DoH",
       "adguard_connected": true,
       "uptime": "up 3 days, 2 hours, 15 minutes"
   }
   ```

--- 

## 🔒 `vpn` - Tailscale Exit Node Manager

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
