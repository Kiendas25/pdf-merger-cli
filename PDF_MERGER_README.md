# PDF Merger CLI

**Merge, split, and rotate PDF files from the command line.**  
Works with text PDFs, scanned documents, forms, encrypted files — everything.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.7+](https://img.shields.io/badge/python-3.7+-blue.svg)](https://www.python.org/downloads/)

---

## Why This Exists

Online PDF tools have limits: file size caps, privacy concerns, batch limits, subscriptions.  
**PDF Merger CLI** runs 100% locally. No uploads. No limits. No account needed.

```bash
# Merge 50 invoices into one PDF
python pdf_merger.py --merge invoices/*.pdf --output all_invoices.pdf

# Split a 200-page contract into individual pages
python pdf_merger.py --split contract.pdf --output page_

# Fix upside-down scanned pages (pages 5-12)
python pdf_merger.py --rotate scan.pdf --pages "5-12" --angle 180 --output fixed.pdf
```

---

## Features

| Feature | Description |
|---------|-------------|
| **Merge** | Combine unlimited PDFs, preserves bookmarks & metadata |
| **Split** | Extract single pages or ranges (`0-5`, `10,15,20-30`) |
| **Rotate** | 90°, 180°, 270° on specific pages or all pages |
| **Glob Support** | `*.pdf` expands to all PDFs in directory |
| **Verbose Mode** | Shows file sizes, page counts, progress |
| **Error Resilient** | Skips corrupt files, continues processing |
| **Encrypted PDFs** | Merge with `--password`; split/rotate need decryption |
| **Single Dependency** | `pip install PyPDF2` only |

---

## Installation

### Option 1: Direct Use (Recommended)
```bash
# Just download the file
curl -O https://raw.githubusercontent.com/yourusername/pdf-merger/main/pdf_merger.py
python pdf_merger.py --help
```

### Option 2: Install as Command
```bash
pip install PyPDF2
# Add to PATH or alias
alias pdfmerge="python /path/to/pdf_merger.py"
```

### Requirements
- Python 3.7+
- PyPDF2 (`pip install PyPDF2`)

---

## Usage

### Merge PDFs
```bash
# Specific files
python pdf_merger.py --merge file1.pdf file2.pdf file3.pdf --output merged.pdf

# All PDFs in folder (alphabetical)
python pdf_merger.py --merge *.pdf --output combined.pdf

# With verbose output
python pdf_merger.py --merge reports/*.pdf --output monthly.pdf --verbose
```

### Split PDF
```bash
# Every page as separate file (page_0.pdf, page_1.pdf, ...)
python pdf_merger.py --split document.pdf --output page_

# Page range only (pages 10-25)
python pdf_merger.py --split document.pdf --pages "10-25" --output chapter2.pdf

# Non-contiguous pages
python pdf_merger.py --split document.pdf --pages "0,2,4,6-10" --output selection.pdf
```

### Rotate Pages
```bash
# Rotate all pages 90° clockwise
python pdf_merger.py --rotate document.pdf --angle 90 --output rotated.pdf

# Rotate only specific pages
python pdf_merger.py --rotate scan.pdf --pages "5-12" --angle 180 --output fixed.pdf

# Multiple angles: 90, 180, 270
python pdf_merger.py --rotate doc.pdf --angle 270 --output landscape.pdf
```

### Combined Operations
```bash
# Merge then rotate
python pdf_merger.py --merge *.pdf --output temp.pdf && \
python pdf_merger.py --rotate temp.pdf --angle 90 --output final.pdf
```

---

## Command Reference

```
pdf_merger.py [--merge FILES... | --split FILE | --rotate FILE] [options]

Merge Options:
  --merge FILES...        PDF files to merge (supports globs)
  --output, -o FILE       Output filename (required for merge/split)

Split Options:
  --split FILE            PDF to split
  --pages RANGE           Pages to extract (e.g., "0-5", "1,3,5-10")
  --output, -o PREFIX     Output prefix (required)

Rotate Options:
  --rotate FILE           PDF to rotate
  --angle DEGREES         90, 180, or 270
  --pages RANGE           Pages to rotate (default: all)
  --output, -o FILE       Output filename (required)

General:
  --password PASS         Password for encrypted PDFs (merge only)
  --verbose, -v           Show progress and file info
  --help, -h              Show help
```

### Page Range Syntax
| Syntax | Meaning |
|--------|---------|
| `5` | Page 5 only (0-indexed) |
| `0-10` | Pages 0 through 10 inclusive |
| `5,8,10` | Pages 5, 8, and 10 |
| `0-5,10-15` | Pages 0-5 and 10-15 |
| `last-5` | Last 5 pages (use negative indices) |

---

## Examples

### Legal: Assemble Case File
```bash
# 47 exhibits + 3 briefs + 2 affidavits
python pdf_merger.py --merge \
  exhibits/*.pdf briefs/*.pdf affidavits/*.pdf \
  --output case_2024-04512_complete.pdf --verbose

# Output:
# [INFO] Found 52 PDF files
# [INFO] Processing...
#   ✓ exhibits/EX-001.pdf (12 pages, 2.4 MB)
#   ✓ exhibits/EX-002.pdf (3 pages, 890 KB)
#   ...
# [INFO] Merged 52 PDFs → case_2024-04512_complete.pdf
# [INFO] Total: 1,247 pages, 89.2 MB
# [INFO] Time: 3.2 seconds
```

### Fix Scanned Documents
```bash
# Scanner fed pages upside down
python pdf_merger.py --rotate scan_batch.pdf --angle 180 --output fixed.pdf

# Only pages 5-12 were sideways
python pdf_merger.py --rotate scan.pdf --pages "5-12" --angle 90 --output fixed.pdf
```

### Extract Chapters from Textbook
```bash
# Syllabus: chapters 3-7 = pages 45-156
python pdf_merger.py --split textbook.pdf --pages "45-156" --output chapters_3-7.pdf
```

### CI/CD: Automated Report Assembly
```yaml
# .github/workflows/monthly-report.yml
- name: Assemble Monthly Report
  run: |
    pip install PyPDF2
    python pdf_merger.py --merge reports/*.pdf --output monthly_report.pdf
    
- name: Upload Artifact
  uses: actions/upload-artifact@v4
  with:
    name: monthly-report
    path: monthly_report.pdf
```

---

## Exit Codes

| Code | Meaning |
|------|---------|
| `0` | Success |
| `1` | Error (file not found, corrupt PDF, password needed) |
| `2` | Usage error (invalid arguments) |

---

## Limitations

- **Encrypted PDFs:** Merge works with `--password`. Split/rotate require decryption first.
- **Form Fields:** Rotation may break form fields on rotated pages.
- **Memory:** Large PDFs (500MB+) loaded into RAM. Split first if needed.
- **Fonts/Graphics:** Preserved during merge/split. Rotation rotates entire page canvas.

---

## License

MIT License — see [LICENSE](LICENSE) file.

**Commercial use, modification, distribution: all permitted.**

---

## Support

- **Issues:** [GitHub Issues](https://github.com/yourusername/pdf-merger/issues)
- **Feature Requests:** Welcome — I use this for client work
- **Security:** Report vulnerabilities privately via GitHub Security Advisories

---

*Stop uploading PDFs to random websites. Process locally. Stay private.*