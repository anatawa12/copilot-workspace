# AAO-BugReport-File Viewer

A web-based tool for parsing and viewing files in the AAO-BugReport-File/1.0 format.

## Features

- **Multiple Input Methods**:
  - Paste text directly into a textarea
  - Drag and drop files
  - Click to browse and select files
  - Support for gzip-compressed files (.gz)
  - Support for plain text files

- **Interactive Viewer**:
  - Split-panel interface with file list on the left and content viewer on the right
  - View global headers from the report file
  - View per-file headers for each included file
  - Monospace font for content display
  - Click to switch between files

- **Robust Parser**:
  - Parses AAO-BugReport-File/1.0 format
  - Handles duplicate header keys (stores as list of key-value pairs)
  - Extracts boundary delimiter from global headers
  - Parses file sections with headers and content
  - Extracts filenames from Content-Disposition headers

## Usage

### Online

Simply open `aao-bugreport-viewer.html` in any modern web browser. No server or build step required!

### Input Methods

1. **Text Input**: Paste your AAO-BugReport-File content directly into the textarea and click "Parse Content"

2. **File Upload**: 
   - Click the drop zone or drag and drop a file
   - Supports both `.gz` (gzip-compressed) and plain text files

### Viewing Files

After parsing:
- The left panel shows all files included in the report
- Click "Global Headers" to expand and view report-level metadata
- Click any file in the list to view its content
- Click "Show File Headers" to view headers specific to that file
- The content is displayed in a monospace font for easy reading

## File Format Specification

The AAO-BugReport-File format is structured as follows:

```
AAO-BugReport-File/1.0
[Global Headers - key: value pairs]

--[boundary]
[File Headers]

[File Content]
--[boundary]
[File Headers]

[File Content]
...
```

### Structure Details

1. **Format Header**: First line must be `AAO-BugReport-File/1.0`
2. **Global Headers**: Key-value pairs (format: `key: value`) before the first blank line
   - Must include a `boundary` header that defines the delimiter
3. **File Sections**: Each file section starts with `--{boundary}` followed by:
   - File headers (including `Content-Disposition` with filename)
   - A blank line
   - The file content
   - Content continues until the next boundary marker

### Example

```
AAO-BugReport-File/1.0
boundary: abc123
version: 1.0
reporter: John Doe

--abc123
Content-Disposition: attachment; filename="log.txt"

This is the log file content
Line 2 of the log
--abc123
Content-Disposition: attachment; filename="config.json"

{
  "setting": "value"
}
```

## Sample File

A sample AAO-BugReport-File is included as `sample-report.txt` for testing purposes.

## Technical Details

- **No dependencies**: Pure HTML, CSS, and JavaScript
- **Gzip support**: Uses Pako library loaded from CDN when needed
- **Client-side only**: All processing happens in your browser
- **No data sent to servers**: Your files never leave your computer

## Browser Compatibility

Works in all modern browsers that support:
- ES6 JavaScript
- File API
- TextDecoder API
- Flexbox CSS

Tested in Chrome, Firefox, Safari, and Edge.

## License

This tool is part of the copilot-workspace repository.
