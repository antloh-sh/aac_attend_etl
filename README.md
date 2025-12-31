# AAC Attendance CSV Processor

AAC Attendance CSV Processor is a browser-based tool that cleans up the **AAC Activity Attendance** export from IngoTPCC and generates a new CSV file with only attended records, key fields, and a `gui` flag derived from the activity master.

Website: https://antloh-sh.github.io/aac_attend_etl/

## What it does

- Reads `AACActivityAttendance.csv` from **IngoTPCC → Report → AAC Activity Attendance**.
- Reads `AAC_ActivityMaster.xlsx` (activity master with GUI information).
- Filters attendance data to keep only rows where `Attendance = 1`.
- Outputs a new CSV with these lowercase fields only:
  - `activitydatedisplay`
  - `activitydisplay`
  - `fullname`
  - `age`
  - `postalcode1`
  - `gender`
  - `registrationdocumentnumber`
  - `gui`
- Truncates `ActivityDisplay` to only the first line (anything after a line break is removed) before matching and output.
- Attempts to format `ActivityDateDisplay` to `dd-mm-yy` where possible (e.g. `19-11-25`), otherwise retains the original text.
- Uses `AAC_ActivityMaster.xlsx` to compute `gui`:
  - If the matching activity’s GUI value starts with `"Yes"` (case-insensitive), then `gui = TRUE`.
  - Otherwise `gui = FALSE` (including when no matching activity is found).
- Processes everything client-side in your browser; no data is uploaded to any server.

## Matching logic (activity ↔ master)

- `activitydisplay` from the attendance CSV is normalised by:
  - Taking only the first line (split on `\r` / `\n`).
  - Trimming whitespace.
  - Converting to lowercase.
- The same normalisation is applied to the activity name column in `AAC_ActivityMaster.xlsx` (e.g. `ActivityDisplay` / `Activity` / `ActivityName`, detected case-insensitively and with spaces removed in the header name).
- This normalised text is used as the key to look up the corresponding GUI value in the master.

## Getting started

1. Clone or download this repository.
2. Either:
   - Open `index.html` directly in a modern browser (Chrome, Edge, etc.), or
   - Visit the website: https://antloh-sh.github.io/aac_attend_etl/
3. Prepare two files from your AAC workflow:
   - `AACActivityAttendance.csv` exported from IngoTPCC → Report → AAC Activity Attendance.
   - `AAC_ActivityMaster.xlsx` containing at least:
     - An activity column (e.g. `ActivityDisplay`, `Activity`, or `ActivityName`).
     - A `GUI` column where values may start with `"Yes"` for GUI activities.
4. In the web page:
   - First upload **`AAC_ActivityMaster.xlsx`** (recommended, for clearer status).
   - Then upload **`AACActivityAttendance.csv`** via the attendance dropzone.
5. Wait for the status message to show that processing is done and the row count is updated.
6. Click **Download processed CSV** to save `AACFilteredAttendance.csv` to your computer.

## Input requirements

### Attendance CSV (AACActivityAttendance.csv)

The file should contain the standard AAC Activity Attendance report headers, including at least:

- `Attendance`
- `ActivityDateDisplay`
- `ActivityDisplay`
- `FullName`
- `Age`
- `PostalCode1`
- `Gender`
- `RegistrationDocumentNumber`

The tool auto-detects the delimiter (comma vs tab) from the first non-empty line and supports CSV or TSV exports.

If any of the required headers are missing, an error message will appear in the status area and no output file will be generated.

### Activity master (AAC_ActivityMaster.xlsx)

- First worksheet is used.
- Required columns (case-insensitive, header spaces ignored):
  - Activity column: one of `ActivityDisplay`, `Activity`, or `ActivityName`.
  - `GUI` column.
- GUI interpretation:
  - If the GUI cell, after trimming and lowercasing, starts with `yes` (e.g. `"Yes"`, `"Yes - GUI"`, `"yes (Y)"`), then `gui = TRUE`.
  - For all other values or missing matches, `gui = FALSE`.

## Output format

The generated `AACFilteredAttendance.csv` uses this header row: `activitydatedisplay`, `activitydisplay`, `fullname`, `age`, `postalcode1`, `gender`, `registrationdocumentnumber`, `gui`

Each subsequent row:

- Corresponds to a record where `Attendance = 1` in the input CSV.
- Contains the transformed values described above plus `gui = TRUE/FALSE` derived from the master file.

## Notes

- The page is designed to work fully offline once loaded; all parsing (CSV and XLSX) happens in the browser using JavaScript and the SheetJS `xlsx` library.
- Large attendance exports or masters may take a short time to parse; the status chip and “Processed rows” counter show progress.
- If `AAC_ActivityMaster.xlsx` is not loaded, processing still works but all `gui` values will default to `FALSE` and a note will be shown in the status.
