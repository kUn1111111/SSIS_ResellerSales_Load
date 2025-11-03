# SSIS Project — Automated Data Load with Logging

This SSIS project provides an end-to-end ETL package that loads data from the AdventureWorksDW2022 source table FactResellerSales into a staging table (ResellerSales_Staging). The package includes logging, error handling, and a straightforward structure so it can be adapted for other tables or incremental loads.

---

## Features

- Automated data load from FactResellerSales to ResellerSales_Staging
- Logging of process start, success, and failure (ETL_Log table)
- Error handling to record failures and messages
- Easily adaptable for other source tables or incremental loads

---

## SSIS Package Overview

### Control Flow
- Start by inserting a "Start" log entry.
- Run the Data Flow Task to copy data from the source to the staging table.
- On success, update the log entry to "Success".
- On failure, update the log entry to "Failed" and include the error message.

### Data Flow Task
- OLE DB Source: reads from AdventureWorksDW2022.dbo.FactResellerSales
- (Optional) Transformations: apply any necessary data transformations
- OLE DB Destination: writes to dbo.ResellerSales_Staging

### Logging Tasks

- Start Log (Execute SQL Task)
```sql
INSERT INTO dbo.ETL_Log (ProcessName, Status, StartTime)
VALUES ('Load Reseller Sales', 'Started', GETDATE());
```

- End Log (Execute SQL Task — on success)
```sql
UPDATE dbo.ETL_Log
SET Status = 'Success', EndTime = GETDATE()
WHERE ProcessName = 'Load Reseller Sales'
  AND EndTime IS NULL;
```

- Failure Log (Execute SQL Task — on failure)
```sql
UPDATE dbo.ETL_Log
SET Status = 'Failed', EndTime = GETDATE(), ErrorMessage = ?
WHERE ProcessName = 'Load Reseller Sales'
  AND EndTime IS NULL;
```
(Replace the placeholder ? with the SSIS system variable or expression you use to capture the error message.)

---

## Database Objects

- Source table
  - FactResellerSales (AdventureWorksDW2022)

- Staging table
  - ResellerSales_Staging (created in your target database to receive the loaded rows)

- Log table
  - ETL_Log — records each run and its status

Suggested ETL_Log schema (example)
- LogID INT IDENTITY(1,1) PRIMARY KEY
- ProcessName NVARCHAR(200)
- Status NVARCHAR(50)         -- Started / Success / Failed
- StartTime DATETIME
- EndTime DATETIME NULL
- ErrorMessage NVARCHAR(MAX) NULL

---

## How to Run

1. Open the package (.dtsx) in Visual Studio / SQL Server Data Tools (SSDT).
2. Configure the OLE DB Connection Manager to point to your AdventureWorksDW2022 (or the correct instance).
3. Make sure the target database has ResellerSales_Staging and ETL_Log (or let the package create them if implemented).
4. Execute the package. The package will:
   - Insert a start record into ETL_Log,
   - Run the data flow to load staging,
   - Update ETL_Log to Success on completion or Failed with an error message on failure.

---

## Screenshots

- Control Flow example: https://bit.ly/SSIS_ResellerSales_Load_ControlFlow  
- Data Flow example: https://bit.ly/SSIS_ResellerSales_Load_DataFlow

---

## Learning Outcomes

By working with this project you will learn:
- How to design an end-to-end SSIS ETL package
- How to implement a logging mechanism for ETL runs
- How to add error handling and update logs with failure messages
- How to automate and adapt data integration processes using SSIS

---

## Tools & Technologies

- Microsoft SQL Server
- SQL Server Data Tools (SSDT)
- SQL Server Integration Services (SSIS)
- AdventureWorksDW2022 sample database

---

If you'd like, I can:
- Add a suggested CREATE TABLE script for ResellerSales_Staging and ETL_Log,
- Standardize SSIS expressions/variables for error messages,
- Or update the README with any additional details specific to your package (connection names, parameter names, or control flow diagram).
