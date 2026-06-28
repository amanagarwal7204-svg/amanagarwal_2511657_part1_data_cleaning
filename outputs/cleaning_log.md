**List of issues found:**
1. Inconsistent Date formats: Dates for orders and shipping were a mix of formats like YYYY/MM/DD, YYYY-MM-DD, MM-DD-YYYY, DD-MM-YYYY, etc. which makes standardizing them difficult since excel does not have a variable option which can assess the format and context and accurately. 

2. Redundant and Conflicting Data: Exact duplicates found in the data which artificially inflate the data as well as order_id which had duplicate ids causing data quality issues. 

3. Missing/Invalid discounts: Some discounts were missing whereas some discounts were either incorrect or unusually high with some disocunts even being negative. 

4. Status Inconsistencies: Cancelled, refunded, failed and pending orders were mixed in with valid orders which skewed the revenue information. 

5. Absurdly wrong timestamps: Some orders had negative shipping time which was caused due to shipping dates being recorded before the order dates. 

**Cleaning actions performed:**

1. Used Trim, Proper, Char(10) & Substitute and clean to standardize and clean the names of the customers, removing spaces and making the names proper nouns where First letter of the names is capitalized. 

2. Used text to columns to standardize dates for both order dates and ship dates which would help us in creating the shipping_delay_days column. 

3. Deleted 20 duplicate rows where the data was exact duplicate across all columns and also used conditional formatting and COUNTIF formula to identify and flag duplicate order IDs which we found out to be 24 in total. 

4.  Identified and standardised dates while identifying and flagging orders with shipping dates before the order dates and values which are read as text. We used text to column function where we standardized the dates for faster processing. We also flagged dates which were invalid like ship dates before the order dates. 

5. Financial calculations like calculated_sales, profit_margin and others utlizing IF, AND, and ROUND functions to prevent floating point calculation error.

6. Data extraction: Used text formulas to extract order month and year as well as shipping_delay_days.

**Business rules applied:**

- Handled missing values for ship_date, region, discount and designated them as Unknown as requested and noted down specific values in the data_quality_report.xlsx
- Used IF and OR function for finding invalid discounts like negatives and discounts above 50% as discount above allowable limit, setting the limit at 50% since. 
- Cancelled and failed payment orders excluded from the total sales data by creating a new calculated sales data which designates 0 for such sales and keeps the sale amount same 
- Refunded order summary created seperate and did not add into the the total sales summary. 
- Shipping order before order date flagged seperately if the shipping_delay_days were negative. 
- Missing shipping informtion like region and/or ship_mode which were blanks in some rows were flagged as invalid data. 
- Missing discounts were labeled as 0 only if the quantity * unit_price was equal to sales otherwise flagged as missing values whereas negative discounts and discounts above normal were flagged as invalid. 
- Calculation mismatch was identified if the raw system's 'Sales' or 'Profit' values did not perfectly align (rounded to 2 decimal places) with the internally verified 'Calculated Sales' and 'Calculated Profit'.

**Assumptions made:**
- Discount threshold was kept at 50% as anything above that is unusually high was assumed as data entry error rather than genuine promotion. 
- Revenue recognition where pending payments and returned orders were not considered in the final sales sumary. 
- Conflicting duplicates in order_id were considered to manual entry errors, but instead of deleting them, they were flagged for auditing rather than silently deleting to prevent data loss. 

**Records removed:**
- Exact duplicates which were about 20 were deleted to prevent double counting. 

**Records flagged:**
- 24 records flagged as "Conflicting Duplicate" (Duplicate Order IDs).
- 14 records flagged as "Flag - Missing Value" (Blank discount where Qty*Price did not match Sales).
- 30 records flagged as "Flag - Invalid Discount" (Discount < 0 or > 50%).
- 132 records flagged as "Invalid" in the master data quality column (Due to negative shipping delays or compounding errors).