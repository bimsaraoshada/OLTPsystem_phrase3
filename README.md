OLTP Sales System
A complete Online Transaction Processing (OLTP) database system for managing customer sales across multiple locations, now extended with a dimensional (star schema) layer for analytics.

📋 Project Overview
This project implements a normalized OLTP database design with four core tables:

CUSTOMER: Customer information
PRODUCT: Product catalog
LOCATION: Store/sales locations
SALES: Transaction records (fact table)
It also includes a warehouse-oriented star schema:

DIM_DATE: Calendar attributes for time analysis
DIM_CUSTOMER: Customer analytics dimension
DIM_PRODUCT: Product analytics dimension
DIM_LOCATION: Location analytics dimension
FACT_SALES_DW: Analytical sales fact table keyed by dimensions
And a 6-level geographic dimension modelled after Oracle's CREATE DIMENSION syntax:

DIM_COUNTRIES: Geographic lookup — LEVEL country → subregion → region
CUSTOMERS_DIM: Customer dimension — LEVEL customer → city → state → (JOIN KEY) country
VW_CUSTOMERS_GEOG_ROLLUP: Flattened view of all 6 hierarchy levels
🎯 Supported Queries
The database is optimized to efficiently answer:

Sales for a given product by location over a period of time

Filter by product, location, and date range
Returns aggregated sales metrics
Maximum number of sales for a given product over time for a given location

Identifies peak sales periods
Supports inventory and demand planning
🗄️ Database Schema
CUSTOMER (customer_id, first_name, last_name, email, phone, created_at, updated_at)
PRODUCT (product_id, product_name, category, brand, unit_price, is_active, created_at, updated_at)
LOCATION (location_id, location_name, city, address, country, is_active, created_at, updated_at)
SALES (sale_id, sale_timestamp, quantity, unit_price, total_amount, customer_id, product_id, location_id)

DIM_DATE (date_key, full_date, day_of_month, month_number, month_name, quarter_number, year_number, day_name, is_weekend)
DIM_CUSTOMER (customer_key, customer_id_nk, full_name, email, phone, created_at)
DIM_PRODUCT (product_key, product_id_nk, product_name, category, brand, base_unit_price, is_active)
DIM_LOCATION (location_key, location_id_nk, location_name, city, address, country, is_active)
FACT_SALES_DW (sales_key, sale_id_nk, date_key, customer_key, product_key, location_key, quantity, unit_price, total_amount, sale_timestamp)

DIM_COUNTRIES (country_id, country_name, country_subregion, country_region)
CUSTOMERS_DIM (customer_key, cust_id, cust_first_name, cust_last_name, cust_gender, cust_marital_status, cust_year_of_birth, cust_income_level, cust_credit_limit, cust_city, cust_state_province, country_id)
See ATTRIBUTES_SUMMARY.md for detailed attribute specifications.

🚀 Quick Start
Prerequisites
Python 3.10+
MySQL Server 5.7+ or 8.0+
pip (Python package manager)
Option A: Use Virtual Environment (recommended)
Create and activate venv
python -m venv .venv
.\.venv\Scripts\Activate.ps1
Install dependencies
python -m pip install -r requirements.txt
Run scripts with venv python
python test_connection.py
python create_db.py
python sample_data.py
python customers_dim.py
python queries.py
streamlit run app.py
Option B: Run Without venv (system Python)
Install dependencies into system Python
C:\Users\ASUS\AppData\Local\Programs\Python\Python313\python.exe -m pip install -r requirements.txt
Run all scripts with the same interpreter
C:\Users\ASUS\AppData\Local\Programs\Python\Python313\python.exe test_connection.py
C:\Users\ASUS\AppData\Local\Programs\Python\Python313\python.exe create_db.py
C:\Users\ASUS\AppData\Local\Programs\Python\Python313\python.exe sample_data.py
C:\Users\ASUS\AppData\Local\Programs\Python\Python313\python.exe customers_dim.py
C:\Users\ASUS\AppData\Local\Programs\Python\Python313\python.exe queries.py
C:\Users\ASUS\AppData\Local\Programs\Python\Python313\python.exe -m streamlit run app.py
Database Configuration
Edit config.py and set your MySQL credentials:

DB_USER = 'root'
DB_PASSWORD = 'your_password_here'  # set this
DB_HOST = 'localhost'
DB_PORT = 3306
DATABASE_NAME = 'oltp_sales_db'
If your MySQL root account has no password, keep DB_PASSWORD = ''.

End-to-End Setup Order
Test database connection
python test_connection.py
Create database and tables
python create_db.py
Load sample data
python sample_data.py
Populate the geographic dimension (required before queries)
python customers_dim.py
This creates and fills dim_countries and customers_dim with the 6-level geographic hierarchy. queries.py expects these tables to exist — it does not run customers_dim.py automatically.

Run sample queries
python queries.py
Run web UI
streamlit run app.py
✅ Current System Status
sample_data.py fully loads OLTP and DW tables successfully.
app.py runs with Streamlit after dependencies are installed.
The date key generation in dimensional loading is fixed (%Y%m%d format).
The project can run with either .venv or system Python, as long as one interpreter is used consistently.
📁 File Structure
oltp_sales_system/
├── README.md                    # This file
├── ATTRIBUTES_SUMMARY.md        # Detailed table attributes
├── QUICKSTART.md                # Step-by-step setup guide
├── requirements.txt             # Python dependencies
├── config.py                    # Database configuration
├── schema.sql                   # SQL schema definition
├── test_connection.py           # Database connection test
├── create_db.py                 # Database setup script
├── sample_data.py               # Sample data generator
├── queries.py                   # Query demonstrations
├── app.py                       # Streamlit web UI for interactive queries
├── customers_dim.py             # 6-level geographic hierarchy dimension
├── check_data.py                # Data verification script
└── er_diagram.py                # ER diagram (ASCII + Mermaid)
Web UI
You can run an interactive web interface for the same OLTP + dimensional queries:

streamlit run app.py
Without venv:

C:\Users\ASUS\AppData\Local\Programs\Python\Python313\python.exe -m streamlit run app.py
The UI includes:

Query 1: sales by product, location, and date range
Query 2: peak sales day with period statistics
Geography rollup/drill-down from customers_dim
Toggle between WITHOUT DIM and WITH DIM query paths
Daily trend charts for sales count and revenue
Region distribution bar chart for customer rollup
💻 Usage Examples
Query 1: Sales by Product, Location, and Time Period
from queries import get_sales_by_product_location_time

results = get_sales_by_product_location_time(
    product_id=1,
    location_id=2,
    start_date='2024-01-01',
    end_date='2024-12-31'
)
Query 2: Maximum Sales for Product at Location
from queries import get_max_sales_for_product_location

results = get_max_sales_for_product_location(
    product_id=1,
    location_id=2,
    start_date='2024-01-01',
    end_date='2024-12-31'
)
Query 3: Geographic Hierarchy Rollup (customers_dim)
from queries import get_customers_by_region, get_customers_drilldown

# Rollup to region level
region_summary = get_customers_by_region()

# Drill down into a single country
us_customers = get_customers_drilldown("United States of America")
Populate the geographic dimension
Run this before queries.py. It creates and seeds dim_countries, customers_dim, and vw_customers_geog_rollup:

python customers_dim.py
⚡ Query Performance Comparison: Without vs With Dimension
queries.py automatically benchmarks every query 5 runs and prints timing statistics plus a side-by-side comparison.

How it works
Step	Detail
Benchmark function	benchmark_query(fn, *args, runs=5) — measures avg / min / max execution time in milliseconds using time.perf_counter
OLTP queries	Run directly against the normalized sales + product + location tables
Dimensional queries	Run against the pre-joined star schema fact_sales_dw + dim_date + dim_product + dim_location
Output printed by queries.py
================================================================================
QUERY PERFORMANCE
================================================================================

WITHOUT DIM
  Query 1 avg=X.XXX ms  min=X.XXX ms  max=X.XXX ms
  Query 2 avg=X.XXX ms  min=X.XXX ms  max=X.XXX ms

WITH DIM
  Query 1 avg=X.XXX ms  min=X.XXX ms  max=X.XXX ms
  Query 2 avg=X.XXX ms  min=X.XXX ms  max=X.XXX ms

PERFORMANCE DELTA
  Query 1 dim/oltp ratio = X.XXx
  Query 2 dim/oltp ratio = X.XXx
A ratio < 1.0x means the dimensional query is faster; > 1.0x means the OLTP query is faster.

Comparison summary also printed
================================================================================
COMPARISON: WITHOUT DIM vs WITH DIM
================================================================================

1. WITHOUT DIM (OLTP)
   Source tables: sales + product + location
   Best for: live transaction queries and operational reporting

2. WITH DIM (Single Dimensional Schema)
   Source tables: fact_sales_dw + dim_date + dim_product + dim_location
                  + customers_dim + dim_countries
   Best for: aggregated analytics plus customer geographic rollup/drill-down

RESULT CHECK
   Query 1 WITHOUT DIM vs WITH DIM: sales_match=True, revenue_match=True
   Query 2 WITHOUT DIM vs WITH DIM: peak_day_match=True, peak_count_match=True

SUMMARY
   WITHOUT DIM = transaction-oriented normalized model
   WITH DIM    = one dimensional schema combining star analytics and customer geography hierarchy
When dimensional queries are skipped
If dim_q1_perf / dim_q2_perf are None it means the dimensional tables were not ready (missing or empty). Run the setup steps in order to enable the WITH DIM columns:

python create_db.py
python sample_data.py
python customers_dim.py
python queries.py      # now prints both WITHOUT DIM and WITH DIM timings
🔧 OLTP Design Features
✅ ACID Compliance: Full transaction support
✅ Normalized Design: 3NF schema minimizes data redundancy
✅ Referential Integrity: Foreign key constraints
✅ Optimized Indexes: Strategic indexing for query performance
✅ Audit Trail: Timestamp tracking on all tables
✅ Data Quality: NOT NULL and CHECK constraints
✅ Scalability: Auto-increment primary keys
📊 Sample Data
The sample_data.py script generates:

100 customers
20 products across 4 categories
10 locations across 5 countries
1,000 sales transactions
Dimension rows derived from OLTP source tables
1,000 fact_sales_dw rows mapped to dimensional keys
The customers_dim.py script populates:

10 countries across 4 regions (Americas, Asia, Europe, Oceania)
12 sample customers mapped across all 6 geographic hierarchy levels
🔍 Performance Optimization
Key indexes for OLTP performance:

-- For Query 1: Product sales by location over time
INDEX idx_sales_product_location_time (product_id, location_id, sale_timestamp)

-- For Query 2: Maximum sales analysis
INDEX idx_sales_product_location (product_id, location_id)

-- General performance
INDEX idx_sales_timestamp (sale_timestamp)
📝 Notes
All timestamps are stored in UTC
Prices are stored with 2 decimal precision
Email addresses must be unique per customer
Foreign keys use ON DELETE RESTRICT to prevent orphaned records
🤝 Contributing
This is an educational project for OLTP database design. Feel free to extend with additional features:

Payment processing
Inventory management
Customer loyalty programs
Multi-currency support
📄 License
Educational use only.
