# DATA_Factory_Covid_19

## Project Overview 💡
This project focuses on gathering and processing multiple Covid-19 datasets to understand the impact of Covid across the European region during the year 2020. The data is transformed using Azure Data Factory (ADF), HDInsight, and Databricks, and loaded into a SQL Data Warehouse to enable the Analytics team to extract actionable insights.

## Source Data 📤
- **European Centre for Disease Prevention and Control (ECDC) Covid-19 Datasets**
- **Population Data from Azure Blob Storage**

## Destination
- **Azure Data Lake Gen2 Storage**

## Tools ⚙
- **Data Integration/Ingestion**: Azure Data Factory (ADF)
- **Transformation**: Azure Data Factory, Databricks
- **Data Warehouse Solution**: Azure SQL Database
- **Visualization**: Power BI

## Environment Setup
- Azure Data Factory
- Azure Blob Storage Account
- Azure Data Lake Storage Gen2
- Azure SQL Database
- Azure Databricks Cluster
- HDInsight Cluster

## Solution Architecture Overview
![Architecture](https://github.com/user-attachments/assets/f5ada80c-25f1-420a-8251-328f3e8baeb3)

## Data Extraction/Ingestion
Four different datasets were ingested from the ECDC website and Azure Blob Storage into Data Lake Gen2:
- Cases and Deaths Data
- Hospital Admissions Data
- Population Data
- Tests Conducted Data

We utilized various components of ADF pipeline activities for data ingestion, including:
- Validation Activity
- Get Metadata Activity
- Copy Activity

### Population Data: Load into Storage Account and Move to Destination Data Lake
![Frame 3](https://github.com/user-attachments/assets/ddbaf67b-4a3b-4f78-8b3f-4bfb844ed4a6)

### Steps:
1. Create a Linked Service to Azure Blob Storage
2. Create a Source Dataset
3. Create a Linked Service to Azure Data Lake Storage (Gen2)
4. Create a Sink Dataset
5. Create a Pipeline:
   - Execute Copy activity when the file becomes available
   - Validate metadata counts before loading data using the IF condition
   - Load data into the destination
6. Schedule Trigger

### ECDC Data Content - Four CSV Files:
1. Case & Deaths Data.csv
2. Hospital Admission Data.csv
3. Testing Data.csv
4. Country Response.csv

## Solution Flow
![Frame 3 (1)](https://github.com/user-attachments/assets/d904b907-45fc-42df-9df8-5a0d11a448bd)

### Steps:
1. Create a Linked Service using an HTTP connector
2. Create a Source Dataset
3. Create a Linked Service to Azure Data Lake Storage (Gen2)
4. Create a Sink Dataset
5. Create a Pipeline with Parameters & Variables
6. Lookup to get all parameters from the JSON file, then pass it to ForEach ECDC data
7. Schedule Trigger
![ECDC Data Flow](https://github.com/user-attachments/assets/4c5a16df-ae65-4416-8c9c-416a8f45b31b)

## Data Transformation
The Cases and Deaths data along with Hospital Admissions data were transformed using ADF Data Flows. The transformations included:

- Select Transformation
- Lookup Transformation
- Filter Transformation
- Join Transformation
- Sort Transformation
- Conditional Split Transformation
- Derived Columns Transformation
- Sink Transformation

### Steps for Cases and Deaths Data:
1. Source: Cases and Deaths (Azure Data Lake Storage Gen2)
2. Filter for European Data Only
3. Select Required Columns
4. Pivot counts using indicator columns (confirmed cases, deaths) to get daily cases count
5. Lookup to retrieve country_code_2_digit and country_code_3_digit columns
6. Select only the required columns for the sink
7. Create a Sink Dataset (Azure Data Lake Storage Gen2)
8. Use Schedule Trigger

### Steps for Hospital Admissions Data:
1. Source: Hospital Admissions (Azure Data Lake Storage Gen2)
2. Select Required Columns
3. Lookup to retrieve country_code_2_digit and country_code_3_digit columns
4. Conditional Split for Weekly and Daily data
   - For Weekly Path:
     - Join with Date to get ecdc_Year_week, week_start_date, week_End_date
     - Pivot counts using indicator columns (confirmed cases, deaths) to get daily cases count
     - Sort data using reported_year_week ASC and Country DESC
     - Select required columns for sink
     - Create a Sink Dataset (Azure Data Lake Storage Gen2)
     - Use Schedule Trigger
   - For Daily Path:
     - Pivot counts using indicator columns (confirmed cases, deaths) to get daily cases count
     - Sort data using reported_year_week ASC and Country DESC
     - Select required columns for sink
     - Create a Sink Dataset (Azure Data Lake Storage Gen2)
     - Use Schedule Trigger

## Reporting via Power BI
1. Create a Connection from Azure SQL to Power BI and load the data.
2. Analyze data to get the total confirmed cases and deaths count.
3. Identify trends in data based on reporting date.
4. Publish the report to Power BI Server and publish to the web.
