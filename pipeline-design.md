TASK 1
1.1

                               DATA SOURCES
┌──────────────────────────────┐      ┌──────────────────────────────┐
│ Historical Transaction Data  │      			│ Live Transaction Stream      │
│ Online Retail Dataset (.xlsx)│   		        │ Real-time POS / Web Events   │
│ Batch file uploads (daily)   │                       │ Row-by-row transactions      │
└───────────────┬──────────────┘      └───────────────┬──────────────┘
                      │                                                 │
                      ▼                                                ▼

                           INGESTION LAYER
┌────────────────────────────────────────────────────────────────────┐
│ Batch Ingestion                                                    │
│  - Scheduled ingestion jobs                                        │
│  - Reads .xlsx files                                               │
│  - Converts to structured format                                   │
│                                                                    │
│ Stream Ingestion                                                   │
│  - Streaming event collector                                       │
│  - Real-time message queue                                         │
│  - Micro-batch or event processing                                 │
└───────────────────────────────┬────────────────────────────────────┘
                                            │
                                            ▼

                             LANDING ZONE
                          (RAW STORAGE LAYER)
┌────────────────────────────────────────────────────────────────────┐
│ Raw Data Lake Storage                                              │
│                                                                    │
│ Raw folder structure:                                              │
│  /raw/batch/YYYY/MM/DD/                                            │
│  /raw/stream/YYYY/MM/DD/                                           │
│                                                                    │
│ Format:                                                            │
│  Batch  -> CSV / Parquet                                           │
│  Stream -> JSON / Parquet                                          │
│                                                                    │
│ No transformations applied                                         │
│ Data stored exactly as received                                    │
└───────────────────────────────┬────────────────────────────────────┘
                                │
                                ▼

                          VALIDATION STAGE
┌────────────────────────────────────────────────────────────────────┐
│ Data Quality & Validation Checks                                   │
│                                                                    │
│ Schema validation                                                  │
│ Required field checks                                              │
│ Data type validation                                               │
│ Duplicate detection                                                │
│ Business rules:                                                    │
│   - Quantity > 0                                                   │
│   - Price > 0                                                      │
│   - Valid timestamp                                                │
│                                                                    │
│ Records split into:                                                │
│   - Valid records                                                  │
│   - Invalid records                                                │
└───────────────┬───────────────────────────────┬────────────────────┘
                │                               │
                ▼                               ▼

           CLEAN PIPELINE                 QUARANTINE AREA
┌──────────────────────────┐      ┌─────────────────────────────┐
│ Validated Records        │   			   │ Dead Letter Storage         │
│ Continue Processing      │     		   │                             │
│                          │    		   │ Failed records stored       │
│                          │   			   │ with error metadata         │
│                          │   			   │                             │
│                          │   			   │ Used for investigation      │
└──────────────┬───────────┘      └──────────────┬──────────────┘
               │                                 │
               ▼                                 ▼

                        TRANSFORMATION STAGE
┌────────────────────────────────────────────────────────────────────┐
│ Data Cleaning & Enrichment                                         │
│                                                                    │
│ Standardize formats                                                │
│ Handle missing values                                              │
│ Currency normalization                                             │
│ Timestamp normalization                                            │
│ Join with reference data                                           │
│ Create derived fields                                              │
│   - total_price = quantity * unit_price                            │
│   - transaction_hour                                               │
│   - customer metrics                                               │
└───────────────────────────────┬────────────────────────────────────┘
                                │
                                ▼

                             STORAGE LAYERS
┌────────────────────────────────────────────────────────────────────┐
│ 1. RAW LAYER                                                       │
│    Immutable original data                                         │
│    Format: Parquet                                                 │
│                                                                    │
│ 2. CLEAN (CURATED) LAYER                                           │
│    Validated & standardized data                                   │
│    Partitioned tables                                              │
│    Format: Parquet                                                 │
│                                                                    │
│ 3. FEATURE LAYER                                                   │
│    Aggregated / ML-ready datasets                                  │
│    Example features:                                               │
│      - Customer purchase frequency                                 │
│      - Average basket value                                        │
│      - Product popularity                                          │
│    Format: Parquet / Feature tables                                │
└───────────────────────────────┬────────────────────────────────────┘
                                │
                                ▼

                              CONSUMER LAYER
┌──────────────────────┬──────────────────────┬──────────────────────┐
│                   	       │                               │                              │
▼                  	       ▼                               ▼

         BI DASHBOARD         ML PIPELINE            DATA SCIENCE
┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
│ Business analytics│ 		│ Model training  │	     │ Ad-hoc analysis  │
│ Sales metrics     │ 		│ Fraud detection │         │ Exploratory      │
│ Revenue trends    │			 │ Recommendation  │        │ queries          │
│ Customer insights │ 		│ Forecasting     │         │                  │
└─────────┬─────────┘ └─────────┬────────┘ └──────────────────┘
          │                     │
          ▼                     ▼

                    ANALYTICS / QUERY ENGINE
┌────────────────────────────────────────────────────────────────────┐
│ SQL query engine / data warehouse access                           │
│ Enables dashboards and ML feature access                           │
└────────────────────────────────────────────────────────────────────┘


                                MONITORING
┌────────────────────────────────────────────────────────────────────┐
│ Pipeline Monitoring & Data Quality                                 │
│                                                                    │
│ Metrics tracked:                                                   │
│  - Ingestion latency                                               │
│  - Pipeline failures                                               │
│  - Record counts                                                   │
│  - Validation error rates                                          │
│                                                                    │
│ Alerts triggered for:                                              │
│  - Schema changes                                                  │
│  - Sudden drops or spikes in volume                                │
│  - Excessive failed records                                        │
│                                                                    │
│ Dashboards + alert notifications                                   │
└────────────────────────────────────────────────────────────────────┘

1.2 COMPONENT DESCRIPTIONS
1.DATA SOURCES

Historical Batch File (Online Retail Dataset)
This component represents the historical dataset provided as an Excel (.xlsx) file containing past retail transactions. The file includes information such as invoice number, 
product ID, quantity, unit price, and timestamps. It is uploaded periodically and used to populate the system with historical data. During ingestion, the file is parsed and 
converted into a structured format such as CSV or Parquet.

Live Transaction Stream
The live transaction stream represents real-time events generated by point-of-sale systems or e-commerce platforms. Each event represents a single transaction or purchase 
that occurs continuously. These events are transmitted through a streaming system such as Kafka or another message queue. The records are usually formatted as JSON and 
processed immediately after arrival.

2.INGESTION LAYER
Batch Ingestion
Batch ingestion loads historical transaction files into the pipeline at scheduled intervals. A workflow system reads the Excel file, extracts its contents, and converts it 
into a structured format suitable for storage and analysis. Tools such as Python scripts, Apache Spark, or Apache Airflow can be used to automate this process. The processed 
data is then written to the raw storage layer as Parquet files organized by date.

Stream Ingestion
Stream ingestion processes real-time transaction events as they are generated. The system continuously reads events from a streaming platform and stores them in the data 
lake. Technologies such as Apache Kafka or Spark Structured Streaming can handle this process efficiently. The resulting data is written to the raw storage layer in JSON 
or Parquet format.

3.LANDING ZONE (RAW STORAGE)
Raw Data Storage
The landing zone is the initial storage location for ingested data before any transformations are applied. It preserves the original data exactly as received, which allows 
auditing and reprocessing if necessary. Files are organized using a structured folder system based on date or ingestion time. Data is typically stored in Parquet format 
within a data lake such as AWS S3, Azure Data Lake, or HDFS.

4.VALIDATION STAGE
Data Validation and Quality Checks
This component ensures that incoming data meets predefined schema and quality rules. It verifies that required fields exist, data types are correct, and values fall within 
acceptable ranges. It also detects duplicate or malformed records. Valid records continue to the transformation stage, while invalid records are redirected to the quarantine 
area.

5.QUARANTINE / DEAD LETTER AREA
Failed Record Storage
The quarantine area stores records that fail validation checks. Instead of deleting problematic data, the system keeps it along with metadata describing the error that 
occurred. This allows engineers to review and correct issues before reprocessing the data. The records are stored in a separate directory using JSON or Parquet format.

6.TRANSFORMATION STAGE
Data Cleaning and Enrichment
The transformation stage prepares validated data for analytics and machine learning. Cleaning steps include removing duplicates, standardizing timestamp formats, and 
handling missing values. Additional attributes may also be created, such as total transaction value or time-based features. Tools such as Apache Spark or SQL-based 
transformation tools are typically used to perform these operations.

7.STORAGE LAYERS
Raw Layer
The raw layer stores the original ingested data without any modification. It acts as the source of truth for the pipeline and allows data to be reprocessed if transformation 
logic changes. Data in this layer is usually stored in Parquet format and partitioned by ingestion date.

Clean Layer
The clean layer contains validated and standardized datasets that are ready for analysis. Inconsistent values are corrected, duplicates are removed, and schemas are unified. 
Data in this layer is often partitioned by date or other dimensions to improve query performance.

Feature Layer
The feature layer stores datasets specifically prepared for machine learning models. It includes engineered features derived from the clean data, such as customer purchase 
frequency or average basket value. These datasets are used directly by machine learning pipelines for model training.

8.CONSUMER INTERFACES
BI Dashboard
The BI dashboard allows business users to visualize and analyze retail data. It connects to the clean data layer through a SQL query engine or data warehouse. Visualization 
tools such as Power BI or Tableau can be used to display sales trends, product performance, and customer insights.

Machine Learning Model
The machine learning pipeline uses datasets from the feature layer to train predictive models. These models can be used for tasks such as fraud detection, demand forecasting,
 or recommendation systems. Frameworks such as Python, Scikit-learn, or TensorFlow are commonly used for this process.

Data Science Access
Data scientists access curated datasets for exploratory analysis and experimentation. They typically use environments such as Jupyter notebooks or data platforms like 
Databricks. This allows them to explore patterns, build prototypes, and test hypotheses before deploying models.

9.MONITORING AND ALERTING
Pipeline Monitoring and Data Quality Observability
The monitoring component tracks the performance and reliability of the entire pipeline. Metrics such as ingestion latency, record counts, validation errors, and pipeline 
execution time are monitored continuously. Alerts are triggered when anomalies occur, such as unexpected drops in data volume or schema changes. Monitoring tools help 
maintain pipeline stability and ensure data quality.


TASK2
2.1
Schema Validations

Field	                                Expected Type				Required				Validation Rule
InvoiceNo				String					Yes 					Must be a non-empty string
StockCode				String					Yes					Must be a non-empty string
Description				String					No					Must be non-empty string
Quantity				Integer					Yes					Must be integer
InvoiceDate				Datetime				Yes					Must be valid datetime
UnitPrice				Float					Yes					Must be numeric
CustomerID				Float					No					May be null but must be numeric if present
Country					String					Yes					Must be non-empty string

Schema Validation Rules

Required fields
InvoiceNo, StockCode, Quantity, InvoiceDate, UnitPrice, Country must not be null.

Data type enforcement
Quantity must be integer
UnitPrice must be numeric
InvoiceDate must be datetime

String fields must not be empty
InvoiceNo, StockCode, Country must not be blank.

CustomerID format
If present, CustomerID must be numeric.


Value Range Validations (Sensible Values)

Field			Rule						Rationale
Quantity		≠ 0						A purchase or return must have quantity
Quantity		|Quantity| ≤ 10,000				Prevent unrealistic bulk orders
UnitPrice		≥ 0						Price cannot be negative
UnitPrice		≤ 10,000					Prevent data entry errors
InvoiceDate		≥ 2009 and ≤ current date			Dataset known time range
CustomerID		> 0						IDs must be positive


Value Range Validation Rules
Quantity cannot be zero
Quantity realistic bounds
-10000 ≤ Quantity ≤ 10000
UnitPrice must be ≥ 0
UnitPrice reasonable limit:UnitPrice ≤ 10,000
InvoiceDate validity:Cannot be future date




Business Rule Validations (Domain Logic)

Rule					Description
Cancellation logic			Invoice numbers starting with "C" represent returns
Return quantities			Returns should have negative quantity
Normal orders				Non-cancellation invoices must have positive quantity
Revenue sanity				Quantity × UnitPrice should be reasonable
Product info consistency		If StockCode exists, description should exist
Customer purchase logic			If CustomerID present, it should appear in multiple transactions


Business Rule Validation Rules
Cancellation invoice rule
If InvoiceNo starts with "C" → Quantity < 0

Normal purchase rule
If InvoiceNo does NOT start with "C" → Quantity > 0

Description consistency
If StockCode exists → Description should not be null.

Revenue sanity
Quantity × UnitPrice must be within realistic limits
Example: abs(Quantity * UnitPrice) ≤ 100,000

Duplicate transaction detection
Combination of (InvoiceNo, StockCode, InvoiceDate) should be unique.


2.2 Design the error handling flow
1. Schema Validation Failures
Examples
Missing InvoiceNo
Invalid data type
Corrupted row

Action
Reject the record entirely

Storage
Move record to:  quarantine.invalid_schema_table
or

dead_letter_queue
Logging

Error log example:
ERROR_TYPE: SCHEMA_VALIDATION
FIELD: Quantity
VALUE: "six"
RECORD_ID: 536365
TIMESTAMP: ingestion_time

Alerting
Trigger:
Slack alert
Email notification

Alert condition:
Schema error rate > 1%

2. Value Range Failures
Examples
UnitPrice = 999999
Quantity = 0

Action
Record accepted but flagged

Add a column:
validation_status = "FLAGGED_RANGE_ERROR"

Storage
Main table receives the row but flagged.

Additionally copied to:
data_quality.range_anomalies_table

Alerting
Dashboard metric:
range_error_rate
If anomaly spikes → notify operator.


3. Business Rule Failures
Examples
Invoice starts with "C" but quantity positive
Missing product description

Action
Quarantine the record
Do not load into main fact table.

Storage
Move to: quarantine.business_rule_violations

Include fields:
record
rule_failed
error_description
ingestion_time


Recovery Workflow for Quarantined Records
Step 1 — Investigate
Data engineer checks quarantine table.

Example:
SELECT * 
FROM quarantine.business_rule_violations
WHERE rule_failed = 'R10';

Step 2 — Fix
Example corrections:
Issue					Fix
Positive return quantity		Multiply by -1
Missing description			Backfill from product catalog
Incorrect price				Update from master data

Step 3 — Reprocess
Move corrected records back into pipeline:
quarantine_table → staging_table → validation → production



TASK3
3.1
Stage 1 — Cleaning Transformations
These handle remaining edge cases after validation.

T1 — Trim and Standardize Strings
Purpose: Remove whitespace and normalize categorical text.
Input:
StockCode
Description
Country

Transformation:
Trim leading/trailing whitespace
Convert Country to standardized case
Remove duplicate spaces

Example:" united kingdom " → "United Kingdom"
Output:Cleaned string fields.

Idempotency:Yes
Running multiple times produces the same result.

T2 — Remove Duplicate Records
Purpose: Prevent duplicate transactional rows.
Input:
InvoiceNo
StockCode
InvoiceDate
Quantity
UnitPrice

Transformation
Drop duplicates using key:(InvoiceNo, StockCode, InvoiceDate)

Output:Unique transaction rows.

Idempotency:Not inherently idempotent if new duplicates appear.

To make it idempotent:Use deterministic deduplication:
keep = first by ingestion_timestamp


T3 — Handle Missing Customer IDs
Some rows have missing CustomerID.
Input
CustomerID
InvoiceNo

Transformation Options
Option A — Keep but mark anonymous:
CustomerID = -1
customer_type = "anonymous"

Option B — Remove rows for customer-level modeling.
Output
Clean customer identifiers.

Idempotency:Yes
Replacing nulls consistently makes it safe to re-run.



Stage 2 — Derived Transaction Columns
These enrich each transaction row.

T4 — Line Total Calculation
Input
Quantity
UnitPrice

Transformation
LineTotal = Quantity * UnitPrice

Output
New column:LineTotal
Example
6 × 2.55 = 15.30

Idempotency:Only safe if recomputed each time.

Best practice:overwrite existing LineTotal

T5 — Cancellation Flag
In the dataset, invoices starting with "C" are cancellations.

Input
InvoiceNo

Transformation
IsCancellation = InvoiceNo.startswith("C")

Output:
Boolean column
IsCancellation

Idempotency:Yes

T6 — Return Indicator
Input
Quantity

Transformation
IsReturn = Quantity < 0

Output
Boolean column
IsReturn

Idempotency:Yes

T7 — Date Component Extraction

Input
InvoiceDate

Transformation Extract:
InvoiceYear
InvoiceMonth
InvoiceDay
DayOfWeek
HourOfDay

Output
Additional time columns.

Idempotency:Yes

T8 — Product Category Proxy
Because no product category exists, derive a proxy.

Input
Description
Transformation

Example rule:
Category = first keyword from Description

Example
"WHITE METAL LANTERN" → "LANTERN"

Output
ProductCategory

Idempotency:Yes

Stage 3 — Customer-Level Aggregations
These create behavioral metrics per customer.

Grouping key:CustomerID

T9 — Customer Total Revenue

Input
CustomerID
LineTotal

Transformation
TotalRevenue = SUM(LineTotal)

Output
Customer table column
customer_total_revenue

Idempotency:Not safe if incremental loads duplicate rows.

Solution:Recompute aggregation from deduplicated transactions table.

T10 — Order Count
Input

CustomerID
InvoiceNo

Transformation

OrderCount = COUNT(DISTINCT InvoiceNo)

Output

customer_order_count

Idempotency:Yes if source table is deduplicated.

T11 — Product Diversity

Measures how many unique products a customer buys.

Input

CustomerID
StockCode

Transformation

UniqueProducts = COUNT(DISTINCT StockCode)

Output

customer_product_diversity

Idempotency: Yes

T12 — Average Basket Size
Input

CustomerID
Quantity
InvoiceNo

Transformation
AvgBasketSize =
SUM(Quantity) / COUNT(DISTINCT InvoiceNo)

Output
avg_basket_size

Idempotency:Yes

T13 — Recency Metric
Used for RFM modeling.

Input
CustomerID
InvoiceDate

Transformation
Recency =reference_date - MAX(InvoiceDate)

Example:
reference_date = dataset_max_date

Output
customer_recency_days

Idempotency:Only if reference date fixed.

To ensure idempotency:
reference_date = fixed snapshot date

Stage 4 — ML Feature Engineering

These features must only use data before the observation window cutoff.
Observation Window Concept
Example timeline:

|------Observation Window------|---Prediction Window---|
Jan 2010                    Dec 2010              Mar 2011

ML features only use observation window data.

T14 — Customer Purchase Frequency
Input
Observation window transactions.

Transformation
frequency =
orders / observation_window_days

Output
customer_purchase_frequency

Idempotency:Yes if window boundaries fixed.

T15 — Monetary Value Feature

Input
LineTotal
CustomerID

Transformation
monetary_value =
average order revenue
SUM(LineTotal) / OrderCount

Output
avg_order_value

Idempotency:Yes

T16 — Product Category Diversity

Input
CustomerID
ProductCategory

Transformation

category_diversity =
COUNT(DISTINCT ProductCategory)

Output
customer_category_diversity

Idempotency:Yes

T17 — Return Rate

Measures product returns.

Input
CustomerID
IsReturn

Transformation
return_rate =returns / total_transactions

Output
customer_return_rate

Idempotency:Yes



TASK 3.2
Layer: Raw
Contents: Original ingested transactions
Format: Parquet files
Update Frequency: Continuous ingestion
Retention: Long-term storage

Layer: Clean
Contents: Validated and enriched transaction data
Format: Parquet or Delta tables
Update Frequency: Hourly or daily updates
Retention: Multiple years for analytics

Layer: Feature
Contents: Customer-level machine learning features
Format: Feature store tables or columnar storage
Update Frequency: Daily feature refresh
Retention: Rolling time window for modeling

Raw Layer Justification
The raw layer preserves original ingested data for reproducibility and debugging. Storing data in Parquet provides efficient compression and columnar access. Analysts rarely query this layer directly, but engineers can replay processing 
if transformation logic changes.

Clean Layer Justification
The clean layer stores curated datasets ready for analytics. Columnar formats like Parquet or Delta Lake allow efficient querying by BI tools. Partitioning by date improves performance and scalability as data volume grows.

Feature Layer Justification
The feature layer contains ML-ready datasets optimized for model training and inference. A feature store ensures consistent feature definitions between training and production environments. This layer typically retains only the latest 
feature values within a rolling time window.

TASK 3.3
The pipeline tracks processed data using a high-water mark based on the latest invoice date processed. Streaming systems also track message offsets to ensure events are processed exactly once.

Late-arriving records are accepted within a defined delay window, such as 48 hours. When these records arrive, the pipeline reprocesses the affected partitions in the clean and feature layers.

Customer-level features are refreshed daily. Incremental aggregation updates only customers whose transaction activity changed since the previous run, ensuring efficient processing and up-to-date features for the machine learning model.

