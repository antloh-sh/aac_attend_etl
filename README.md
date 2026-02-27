# AAC Attendance ETL (Supabase Version)

This tool processes activity attendance data by merging three distinct sources into a single, clean CSV file optimized for direct import into **Supabase (PostgreSQL)**.

## 🚀 Key Features

- **Date Standardisation:** Converts `DD-Mon-YYYY` (e.g., 19-Nov-2025) to `YYYY-MM-DD` for native Supabase **DATE** fields.
- **Native Boolean Mapping:** Maps the Activity Master's GUI status to raw `true` or `false` values for Supabase **BOOL** fields.
- **AAP Filtering:** Excludes any activity where `AAP` is not explicitly `Yes` in the Activity Master. Rows with `No`, blank, or missing AAP values are dropped.
- **CFS Integration:** Joins the Client Progress Report (CPR) data using the NRIC as a unique key.
- **Dynamic Naming:** Automatically generates filenames based on the data month, e.g., `etl_activity_oct2025.csv`.

## 📁 Required Data Sources

The tool expects three files to be uploaded in this order:

1.  **Activity Master (.xlsx):**
    - Looks for an `Activity` column, a `GUI` column, and an `AAP` column.
    - If `GUI` starts with "Yes", the output row is marked `true`.
    - If `AAP` does not start with "Yes" (i.e., is "No", blank, or missing), the activity is **excluded entirely** from the output.
2.  **CPR File (.xlsx):**
    - Standard Client Progress Report export.
    - The tool automatically skips the first 9 metadata rows to find the headers.
    - Maps `UIN / NRIC` to the `CFS (1-9)` value.
3.  **Activity Attendance (.csv):**
    - The raw export from IngoTPCC.
    - **Filter Logic:** Only rows where the `Attendance` column equals `1` are processed, and only for activities that pass the AAP filter above.

## 🛠 Database Schema Mapping

The output CSV headers are lowercase and mapped to the following Supabase types:

| CSV Header | Supabase Type | Transformation Logic |
| :--- | :--- | :--- |
| `activitydatedisplay` | **DATE** | `19-Nov-2025` → `2025-11-19` |
| `activitydisplay` | **TEXT** | Truncated to the first line (removes time/location). |
| `fullname` | **TEXT** | Original text. |
| `age` | **INT2** | Original number. |
| `postalcode1` | **TEXT** | Original text. |
| `gender` | **TEXT** | Original text. |
| `registrationdocumentnumber` | **TEXT** | Cleaned NRIC (Primary Key). |
| `gui` | **BOOLEAN** | "Yes..." → `true`, else → `false`. |
| `cfs` | **TEXT** | CFS score from CPR file. |

> **Note:** The `AAP` column is used as a filter only and is **not** included in the output CSV.

## 📦 Output Filename

The output file follows the format:
`etl_activity_<month><year>.csv` (e.g., `etl_activity_oct2025.csv`).

## 🔒 Security

All processing is performed locally in your browser. **No data is uploaded to a server.** The tool uses the `SheetJS` and `PapaParse` libraries to handle data entirely within your computer's memory.

---
*Created for the Sheng Hong Active Ageing Centre Data Workflow.*
