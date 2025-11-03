#  SSIS Project: Automated Data Load with Logging

## Project Overview
This SSIS project automates the data loading process from an existing SQL Server table into a new **staging table**, including full logging and error handling.  
The package is designed as part of an ETL (Extract, Transform, Load) pipeline for the **AdventureWorksDW2022** database.

---

##  Main Features
- **Automated Data Load:**  
  Transfers data from the source table (`FactResellerSales`) into a new table (`ResellerSales_Staging`).

- **Dynamic Logging System:**  
  Inserts log entries at the start of the process and updates them upon completion or failure.

- **Error Handling:**  
  If an error occurs, the log table is automatically updated with the error message and timestamp.

- **Scalable Structure:**  
  Can easily be adapted to handle other tables or incremental loads.

---

##  SSIS Components Used

### 1 Data Flow Task
Transfers data from the source table to the staging table using OLE DB Source and OLE DB Destination.

### 2 Execute SQL Task (Start Log)
Inserts a new record into the log table when the process starts:
```sql
INSERT INTO dbo.ETL_Log (ProcessName, Status, StartTime)
VALUES ('Load Reseller Sales', 'Started', GETDATE());


### 3 Execute SQL Task (End Log)
Updates the log table when the process completes successfully:
```sql
UPDATE dbo.ETL_Log
SET Status = 'Success', EndTime = GETDATE()
WHERE ProcessName = 'Load Reseller Sales'
  AND EndTime IS NULL;



## Database Tables

### Source Table
FactResellerSales — from AdventureWorksDW2022.

### Staging Table
ResellerSales_Staging — created to store new or transformed data.

### Log Table
ETL_Log — tracks every data load.

Column Name And Description:

LogID	Primary key
ProcessName	Name of the ETL process
Status	Started / Success / Failed
StartTime	When the process started
EndTime	When the process ended
ErrorMessage	Any error message if failed


## How to Run

1.Open the .dtsx package in Visual Studio (SSDT).

2.Set up your OLE DB connection manager to connect to AdventureWorksDW2022.

3.Run the package — logs will automatically update in the ETL_Log table.


## Screenshots

Control Flow Example:

Data Flow Example:


## Key Learning Outcomes

1.Designing an end-to-end ETL process in SSIS

2.Building a logging system to track data load execution

3.Implementing error handling and control flow

4.Understanding how to automate data integration using SSIS


## Tools & Technologies

1.Microsoft SQL Server

2.SQL Server Data Tools (SSDT)

3.SSIS (Integration Services)

4.AdventureWorksDW2022
