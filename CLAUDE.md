# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

tidyarxiv is a Python package that prepares LaTeX papers for arXiv submission by:
- Copying only necessary files to a staging directory
- Filtering out TeX comments from source files
- Building the LaTeX project
- Creating a tarball and PDF ready for arXiv submission

## Development Commands

### Setup and Dependencies
```bash
# Install dependencies using Poetry
poetry install

# Activate Poetry shell
poetry shell
```

### Testing the Script
```bash
# Run tidyarxiv directly (requires tidyarxiv.cfg in current directory)
poetry run tidyarxiv

# Or if installed in development mode
python -m tidyarxiv.tidyarxiv
```

### Building and Publishing
```bash
# Build the package
poetry build

# Publish to PyPI (requires credentials)
poetry publish
```

## Architecture

The entire application logic is contained in `tidyarxiv/tidyarxiv.py` with these key components:

1. **Configuration Loading**: Reads `tidyarxiv.cfg` (JSON format) from the current directory
2. **File Selection**: Uses `build_file_list()` to create include/exclude lists based on glob patterns
3. **TeX Comment Filtering**: `filter_tex()` removes comments from .tex and .sty files using regex
4. **Build Process**: Executes LaTeX build command (default: latexmk) in temporary staging directory
5. **Output Generation**: Creates timestamped PDF, tarball, and build log files

## Configuration System

The script expects a `tidyarxiv.cfg` JSON file with these optional keys:
- `target`: Main .tex file (default: "main")
- `jobname`: LaTeX jobname parameter (default: same as target)
- `outdir`: Output directory (default: ".")
- `files`: Glob patterns for files to include in build
- `files_exclude`: Glob patterns to exclude from build
- `arxiv_files_include`: Additional files for final tarball
- `arxiv_files_exclude`: Files to exclude from tarball
- `filter_files`: Files to filter for TeX comments
- `build_command`: LaTeX build command with %FILE% and %JOBNAME% placeholders
- `metadata_file`: Optional metadata file to copy to output

## Key Implementation Details

- Uses Python's `tempfile.TemporaryDirectory()` for staging
- Build errors are captured and saved to build.log with UTF-8 encoding handling
- Output files are timestamped with format: `{jobname}_{YYYYMMDD_HHMMSS}.{ext}`
- Config file name can be overridden via `TIDYARXIV_CONFIG_NAME` environment variable