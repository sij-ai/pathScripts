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
- If a word isn’t found in the dictionary, it’s left unchanged.
- File extensions are preserved during renaming.

--- 

## 📦 `deps` - Unified Python Dependency Manager

A single script that analyzes `import` statements in .py files and installs dependencies using mamba/conda or pip.

### Usage
```bash
deps <subcommand> ...
```

#### Subcommands

1. **`ls`**  
   Analyzes `.py` files for external imports:
   - Writes PyPI-available packages to `requirements.txt`.
   - Writes unavailable packages to `missing-packages.txt`.

   **Examples**:
   ```bash
   deps ls            # Analyze current directory (no recursion)
   deps ls -r         # Recursively analyze current directory
   deps ls src        # Analyze a 'src' folder
   deps ls -r src     # Recursively analyze 'src'
   ```

2. **`install`**  
   Installs Python packages either by analyzing local imports or from explicit arguments.  
   - **Conda Environment Detection**: If in a conda environment, tries `mamba` (if installed), else `conda`.  
   - **Fallback** to `pip` if conda tool fails or is unavailable.  
   - **`--no-conda`**: Skip conda/mamba entirely and go straight to pip.

   **Examples**:
   ```bash
   deps install            # Analyze current folder, install discovered packages (no recursion)
   deps install -r         # Same as above but recursive
   deps install requests   # Directly install 'requests'
   deps install script.py  # Analyze and install packages from 'script.py'
   deps install -R requirements.txt  # Install from a requirements file
   deps install requests --no-conda  # Skip conda/mamba, use pip only
   ```

### How It Works
- **Scanning Imports**: Locates `import ...` and `from ... import ...` lines in `.py` files, skipping built-in modules.  
- **PyPI Check**: Uses `urllib` to confirm package availability at `pypi.org`.  
- **Requirements & Missing Packages**: If you run `deps ls`, discovered imports go into `requirements.txt` (available) or `missing-packages.txt` (unavailable).  
- **Installation**: For `deps install`:
  - If no extra arguments, it auto-discovers imports in the current directory (optionally with `-r`) and installs only PyPI-available ones.  
  - If passed packages, `.py` files, or `-R <reqfile>`, it installs those specifically.  
  - By default, tries conda environment tools first (mamba or conda) if in a conda environment, otherwise pip.  

### Notes
- If `mamba` or `conda` is available in your environment, `deps install` will prefer that. Otherwise, it uses pip.  
- You can run `deps ls` repeatedly to keep updating `requirements.txt` and `missing-packages.txt`.

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
