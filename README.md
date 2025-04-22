# Medicare-Inpatient-Dataset-Summary-Insights-SQL-SERVER
The Medicare Inpatient Hospitals Dataset is a public dataset prepared by the Centers for Medicare & Medicaid Services (CMS). It provides comprehensive information on services and procedures delivered to Medicare beneficiaries by over 3,000 U.S. hospitals under the Inpatient Prospective Payment System (IPPS).

Dataset Coverage:

1-Scope: Hospital-specific charges for Medicare Inpatient Discharges.

2-Population: Medicare Fee-For-Service (FFS) beneficiaries.

3-Payment Model: Based on Medicare Severity Diagnosis Related Group (MS-DRG) rates.

4-Data Status: Includes only final-action claims (all adjustments resolved).

5-Data Frequency: Captures 100% of discharges under IPPS in the reporting period.





 ## STEP 1: Change column names for better understanding,
 This step renames the columns to make them more descriptive.

EXEC sp_rename 'dbo.MIH22.Rndrng_Prvdr_CCN', 'Rendering_Provider_CCN', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Rndrng_Prvdr_Org_Name', 'Rendering_Provider_Name', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Rndrng_Prvdr_City', 'Rendering_Provider_City', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Rndrng_Prvdr_St', 'Rendering_Provider_City_Alt', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Rndrng_Prvdr_State_FIPS', 'Rendering_Provider_State_FIPS_Code', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Rndrng_Prvdr_Zip5', 'Rendering_Provider_Zip_Code', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Rndrng_Prvdr_State_Abrvtn', 'Rendering_Provider_State_Abbreviation', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Rndrng_Prvdr_RUCA', 'Rendering_Provider_Rural_Urban_Commuting_Code', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Rndrng_Prvdr_RUCA_Desc', 'Rendering_Provider_Rural_Urban_Commuting_Description', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.DRG_Cd', 'DRG_Definition', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.DRG_Desc', 'DRG_Description', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Tot_Dschrgs', 'Total_Discharges', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Avg_Submtd_Cvrd_Chrg', 'Average_Covered_Charges', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Avg_Tot_Pymt_Amt', 'Average_Total_Payments', 'COLUMN';

EXEC sp_rename 'dbo.MIH22.Avg_Mdcr_Pymt_Amt', 'Average_Medicare_Total_Payments', 'COLUMN';



 ## STEP 2: Check for NULL values,
 This query helps identify rows with NULL values in any of the specified columns.

SELECT *
FROM dbo.MIH22
WHERE 
    Rendering_Provider_CCN IS NULL OR
    
    Rendering_Provider_Name IS NULL OR
    
    Rendering_Provider_City IS NULL OR
    
    Rendering_Provider_City_Alt IS NULL OR
    
    Rendering_Provider_State_FIPS_Code IS NULL OR
    
    Rendering_Provider_Zip_Code IS NULL OR
    
    Rendering_Provider_State_Abbreviation IS NULL OR
    
    Rendering_Provider_Rural_Urban_Commuting_Code IS NULL OR
    
    Rendering_Provider_Rural_Urban_Commuting_Description IS NULL OR
    
    DRG_Definition IS NULL OR
    
    DRG_Description IS NULL OR
    
    Total_Discharges IS NULL OR
    
    Average_Covered_Charges IS NULL OR
    
    Average_Total_Payments IS NULL OR
    
    Average_Medicare_Total_Payments IS NULL;
    


 ## STEP 3: Replace NULL values with default values (for visualization),
 This step shows what the output will look like when NULL values are replaced.

SELECT 
    COALESCE(Rendering_Provider_Rural_Urban_Commuting_Code, 0) AS Rendering_Provider_Rural_Urban_Commuting_Code,
    
    COALESCE(Rendering_Provider_Rural_Urban_Commuting_Description, 'N/A') AS Rendering_Provider_Rural_Urban_Commuting_Description
    
FROM dbo.MIH22;


 ## STEP 4: Make the changes permanent by updating the table,
This step updates the table to replace NULL values with specified default values (0 and 'N/A').

UPDATE dbo.MIH22

SET 
    Rendering_Provider_Rural_Urban_Commuting_Code = COALESCE(Rendering_Provider_Rural_Urban_Commuting_Code, 0),
    
    Rendering_Provider_Rural_Urban_Commuting_Description = COALESCE(Rendering_Provider_Rural_Urban_Commuting_Description, 'N/A')
    
WHERE 

    Rendering_Provider_Rural_Urban_Commuting_Code IS NULL 
    
    OR Rendering_Provider_Rural_Urban_Commuting_Description IS NULL;
    

## STEP 5: Drop unwanted Columns

ALTER TABLE MIH22

DROP COLUMN DRG_Definition , DRG_Description;

## STEP 6: Optimize table performance
 After making changes, rebuild the table indexes to improve query performance, especially if the table is large.

ALTER INDEX ALL ON dbo.MIH22 REBUILD;



# Medicare Inpatients Hospitals - 2022 Power BI Dashboard


Dashboard Summary:

## 1. KPIs at a Glance
   
Total Discharges: 5 Million

Total Covered Charges: 13 Billion USD

Total Medicare Payments: 2 Billion USD

Total Payments: 3 Billion USD



Visual Analysis

## 2. Medicare Total by State (Pie Chart)
Displays the distribution of total Medicare charges across U.S. states.

Top contributors include:

California (CA): 266.9M (11.88%)

New York (NY): 166.95M (7.43%)

Texas (TX): 149.77M (6.66%)

Florida (FL): 149.49M (6.65%)

This helps identify the states with the highest inpatient Medicare activity.

## 3. Covered Charges vs Total Payments by Zip Code (Bar Chart)

Compares billed charges vs. Medicare payments per ZIP code.

Reveals major discrepancies between covered charges and actual payments, helping uncover cost inefficiencies or high-billing ZIPs.

Examples:

ZIPs with high charges but low payments (visible spikes)

ZIPs with 0 payments despite charges (possibly claim rejections or unresolved billing)

## 4. Total Discharges by Urban vs Rural (Pie Chart)

Breaks down discharge count by urban vs rural classification.

Urban discharges dominate (e.g., one segment = 652 discharges = 34.19%).

Supports policy review on healthcare accessibility and funding by area type.

## 5. Average CCN by Medicare Total Payments (Line Chart)

Displays Medicare payments distribution across Certified Medicare Provider Numbers (CCNs).

Patterns show:

Consistent spikes in certain CCNs

High variance, indicating uneven provider payouts

Insightful for identifying high-cost hospitals or potential billing anomalies.

## 🛠️ Filters for Dynamic Analysis

The dashboard includes slicers for:

City

State

Provider Name

This allows users to drill down into any geographic area or hospital facility for custom insights.

