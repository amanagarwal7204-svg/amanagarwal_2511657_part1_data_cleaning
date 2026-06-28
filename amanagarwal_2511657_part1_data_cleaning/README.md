# 1. Problem summary:
The company's raw point-of-sale data (raw_orders.xlsx) contained numerous data quality issues, including conflicting duplicates, calculation mismatches, invalid financial entries (e.g., negative discounts), and inconsistent text/date formatting. These inconsistencies prevented leadership from generating accurate revenue summaries. The objective of this project was to audit, clean, validate, and standardize this dataset to create an analysis-ready database and generate a suite of reliable business reports.

# 2. Dataset description
- Source file: raw_data.xlsx
- Cleaned file: cleaned_data.xlsx
- Output files: cleaning_log.md , data_quality_report.xlsx, and pivot_summary.xlsx
- Description: Transactional retail data at the order-line-item level. Key fields include order and shipping dates, customer demographics (segment, region), product hierarchies (category, sub-category), financial metrics (quantity, unit price, discount, cost), and fulfillment statuse

# 3. Tools used:
- Microsoft Excel (Native app for MacOS)
- Advanced Formulas: Logical (IF, AND, OR, IFERROR), Statistical (COUNTIF, SUMIFS), Text/Date Extraction (TEXT, MONTH, YEAR), and mathematical rounding guards.
- Data summarization with Pivot tables.

# 4. Cleaning steps performed:
- Text Standardization: Removed leading/trailing/extra spaces and standardized capitalization across categorical fields (Customer Name, Category, Region, etc.).

- Date Normalization: Converted mixed-format date strings into a uniform YYYY-MM-DD format. 

- Duplicate Handling: Removed 100% exact duplicate rows. Retained and flagged duplicate order_ids that contained conflicting data to prevent silent data loss.

- Calculated Fields: Created protected mathematical columns for calculated_sales, calculated_profit, profit_margin, and shipping_delay_days to bypass systemic errors in the raw export.

- Master Auditing: Built a dynamic data_quality_flag to evaluate every record against all business rules simultaneously, outputting "Clean", "Warning", or "Invalid". 

# 5. Business rules applied: 
- Missing Demographics: Any blank Region or Ship Mode was populated as "Unknown" and flagged as a Warning.

- Discount Logic: Blank discounts were treated as 0 ONLY if Quantity * Unit Price = Original Sales. Otherwise, they were flagged. Negative discounts or discounts > 50% were marked as Invalid.

- Revenue Recognition: "Calculated Sales" and "Calculated Profit" were only validated for perfectly finalized transactions. Refunded, Cancelled, and Failed orders were excluded from standard revenue metrics and summarized separately.

- Chronological Integrity: Any order where the Ship Date occurred prior to the Order Date (resulting in a negative delay) was flagged as Invalid.

# 6. Summary of data quality issues found: 
### Missing Value Summary
| Metric | Amount |
| :--- | :---:|
| Missing region | 25 |
| Missing ship_mode | 21 |
| Missing discount | 14 |

### Duplicate Summary
| Metric | Amount / Details |
| :--- | :---: |
| Number of exact duplicate rows found | 20 |
| Number of duplicate order IDs found | 24 |
| Number of records removed | 20 |
| Number of records flagged for review | 24 |
| Logic used for handling duplicates | We used "Remove Duplicates" to find and eliminate exact duplicates. For duplicate order IDs, we used conditional formatting to isolate them and then flagged them for manual review under `data_quality_flag`. |

### Invalid Discount Summary
| Metric | Amount |
| :--- | :---: |
| Number of negative discounts | 15 |
| Number of discounts above allowed range | 15 |

### Date Issue Summary
| Metric | Amount |
| :--- | :---: |
| Number of dates with errors(Negative shipping_delay_days) | 193 |

### Order Status Summary
| Metric | Amount |
| :--- | :---: |
| Total cancelled orders | 145 |
| Total completed orders | 604 |
| Total returned orders | 163 |

### Sales/Profit Calculation Mismatch Summary
| Metric | Amount |
| :--- | :---: |
| Records with perfectly matching financial calculations | 799 |
| Records with a Calculation Mismatch (System Error) | 70 |
| Records where calculations were blocked due to invalid data | 43 |

### Final Clean vs. Flagged Record Count
| Metric | Amount |
| :--- | :---: |
| Clean records | 720 |
| Invalid records | 132 |
| Records with warnings | 60 |

# 7. Summary of final pivot reports
A dedicated pivot_summary.xlsx file was generated to answer core business questions:

- Sales & Profit by Region: South region had the highest sales whereas East region had the highest profit. 

- Category & Sub-Category Performance: Furniture had the highest sales whereas technology had the highest profits. 

- Fulfillment Volume: Standard class had the 

- Segment Margins: Average profit margin percentage broken down by Consumer, Corporate, and Home Office segments.

- Lost Revenue Tracking: Filtered report isolating exactly how many Refunded, Cancelled, or Failed orders occurred per Region.

- Chronological Trends: Sum of valid sales plotted chronologically by month (Jan-Dec) to track seasonal performance.
# 8. Key business insights
Top Performing Region: The South region generated the highest overall calculated sales, totaling ₹ 2,165,320.33

Margin Efficiency: While the small business segment generated high volume, the consumer segment actually yielded the highest average profit margin at 29%.

Logistics/Fulfillment: Standard class is overwhelmingly the most utilized shipping method, handling 242 total orders.

Revenue Leakage: The company experienced 140 total failed or refunded orders.
# 9. Assumptions and limitations

Assumptions: We assumed a maximum logical discount of 50%; anything higher was flagged as a data-entry error. We also assumed that conflicting duplicates (same order ID, different products/prices) required manual management review and could not be algorithmically deleted.

Limitations: The cleaning logic was built natively in Excel. Since it is not a scripted pipeline (like SQL or Python), importing a new batch of raw data requires manually copying down the calculated formulas and re-applying the "Paste as Values" locking mechanism. Furthermore, the system flags conflicting duplicates but cannot autonomously determine which row is the "true" record.
# 10. Screenshots included
cleaned_data_preview.png, raw_data_preview.png, pivot_summary_1.png and pivot_summary_2.png
