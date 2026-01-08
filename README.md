# AAC Attendance ETL Processor (with CFS)

A lightweight, browser-based tool designed to clean and merge data from the **AAC Activity Attendance** export, the **Activity Master**, and the **Client Progress Report (CPR)**.

## 🚀 Purpose
This tool automates the process of joining three separate data sources into a single, clean CSV file suitable for importing into systems like Supabase. It specifically filters for actual attendance and maps metadata like `gui` flags and `cfs` scores.

## 🛠 File Upload Sequence
To ensure the logic executes correctly, files should be uploaded in the following order:

1.  **Activity Master (XLSX):** Contains the mapping for the `gui` flag.
2.  **CPR File (XLSX):** The Client Progress Report containing the `cfs` (Clinical Frailty Scale) scores.
3.  **Activity Attendance (CSV):** The core attendance data exported from IngoTPCC.

## ⚙️ ETL Logic & Transformations

### 1. Filtering
- The tool only processes rows where the **Attendance** column is exactly `1`. All other rows (absentees or placeholders) are discarded.

### 2. Data Cleaning
- **Activity Names:** Truncates the `ActivityDisplay` value. It keeps only the first line (e.g., "Yogalates") and discards extra info like time or location ("10:00 AM - Activity Hall").
- **NRIC/UIN:** Automatically trims whitespace from registration numbers to ensure a perfect match across files.

### 3. Joining & Mapping
- **GUI Flag:** Matches the truncated activity name against the Activity Master. If the `GUI` column in the master file starts with "Yes" (case-insensitive), the output is `TRUE`. Otherwise, it is `FALSE`.
- **CFS Score:** Matches the `RegistrationDocumentNumber` against the `UIN / NRIC` column in the CPR file to pull the `CFS (1-9)` value.

### 4. Date Formatting
- Converts dates from the standard export format (e.g., `19-Nov-2025`) to a standardized `dd-mm-yy` format (e.g., `19-11-25`).

## 📋 Required Headers

| File | Key Columns Required |
| :--- | :--- |
| **Activity Master** | `Activity` (or `ActivityDisplay`), `GUI` |
| **CPR File** | `UIN / NRIC`, `CFS (1-9)` (Logic skips first 9 rows automatically) |
| **Attendance CSV** | `ActivityDateDisplay`, `ActivityDisplay`, `FullName`, `Age`, `PostalCode1`, `Gender`, `RegistrationDocumentNumber`, `Attendance` |

## 📁 Output Format
The generated CSV will contain the following columns in order:
`activitydatedisplay`, `activitydisplay`, `fullname`, `age`, `postalcode1`, `gender`, `registrationdocumentnumber`, `gui`, `cfs`

## 🔒 Privacy & Security
- **100% Client-Side:** All processing happens within your browser. No data is uploaded to any server or external database.
- **No Persistence:** Data is cleared as soon as the browser tab is closed.

---
*Developed for Sheng Hong Active Ageing Centre.*
