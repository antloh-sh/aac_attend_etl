# AAC Attendance CSV Processor

AAC Attendance CSV Processor is a small, browser-based tool that cleans up the **AAC Activity Attendance** export from Ingot and generates a new CSV file suitable for analysis or import into other systems.

Repository: https://github.com/antloh-sh/aac_attend_etl

## What it does

- Reads `AACActivityAttendance.csv` (from IngotPCC → Report → AAC Activity Attendance).
- Filters to keep only rows where `Attendance = 1`.
- Outputs a new CSV with these fields only:
  - `activitydatedisplay`
  - `activitydisplay`
  - `fullname`
  - `age`
  - `postalcode1`
  - `gender`
  - `registrationdocumentnumber`
- Truncates `ActivityDisplay` to only the first line (anything after a line break is removed).
- Attempts to format `ActivityDateDisplay` to `dd-mm-yy` where possible (e.g. `19-11-25`), otherwise retains the original text.
- Processes everything client-side in your browser; no data is uploaded to any server.

## Getting started

1. Clone or download this repository, and open `index.html` in a modern browser (Chrome, Edge, etc.).
2. Export `AACActivityAttendance.csv` from Ingot (Report → AAC Activity Attendance).
3. In the web page:
   - Click the upload area or drag-and-drop your CSV/TSV file.
   - Wait for the status to show “Processing done. Click ‘Download processed CSV’.”
4. Click **Download processed CSV** to save `AACFilteredAttendance.csv` to your computer.

## Input requirements

- File should contain the standard AAC Activity Attendance report headers, including:
  - `Attendance`
  - `ActivityDateDisplay`
  - `ActivityDisplay`
  - `FullName`
  - `Age`
  - `PostalCode1`
  - `Gender`
  - `RegistrationDocumentNumber`
- The tool auto-detects delimiter (comma vs tab) from the first non-empty line.

If any of the required headers are missing, an error message will appear in the status area.

## Output format

The generated `AACFilteredAttendance.csv` uses lowercase headers:
activitydatedisplay, activitydisplay, fullname, age, postalcode1, gender, registrationdocumentnumber

Each subsequent row corresponds to a record where `Attendance = 1` after applying the transformation rules described above.

## Notes

- The page is designed to work fully offline once loaded.
- Large files may take a short time to parse and process; progress is indicated in the status chip and “Processed rows” counter.

