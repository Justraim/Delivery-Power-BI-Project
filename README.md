# Pargo Dashboard Project

## Overview

This project involves creating two dashboards for Pargo, a smart logistics company, using Power BI. The dashboards are designed to provide insights into operational performance and tactical performance at the Pickup Points.

## Data

The project uses two datasets:
1. **orders_data.csv**: Contains order-related data.
2. **pickup_points_data.csv**: Contains data related to pickup points.

## Objectives

1. **Operational Dashboard**:
   - Show parcel on-time performance with two measures:
     - On-time Delivery: Delivered within 3 working days for main and 5 working days for regional deliveries.
     - On-time Collection: Collected within 8 days from the pickup point.
   
2. **Tactical Dashboard for Pickup Point Manager**:
   - Measure performance by profitability and growth.
   - Profitability: Monthly calculation with assumptions on costs and revenues.
   - Growth: Year-on-year based on order count.
   - Bonus: Create a measure called "Pickup Point Health" based on profitability and growth.

## Steps to Create the Dashboards

### 1. Setting Up Power BI

1. **Open Power BI Desktop.**
2. **Import Your Data**:
   - Click on `Home > Get Data > Text/CSV`.
   - Import `orders_data.csv` and `pickup_points_data.csv`.
   - Check the preview and click `Load`.

### 2. Data Cleaning

1. **Check for Missing Values**:
   - Go to `Transform Data` (Power Query Editor).
   - Identify and handle any missing values in both datasets.
   - For `orders_data`, ensure critical fields like `order_created_at`, `ts_at_collection_point`, and `ts_completed` are not missing.

2. **Format Date Columns**:
   - Ensure all date columns (`order_created_at`, `ts_at_collection_point`, `ts_completed`) are in the correct date/time format.

3. **Remove Duplicates**:
   - Check for and remove any duplicate rows in both datasets.

4. **Filter Data**:
   - Apply necessary filters to exclude any irrelevant or outlier data points.

5. **Close & Apply**:
   - After cleaning, click `Close & Apply` to apply changes and load the cleaned data back into Power BI.

### 3. Establish Relationships

1. **Go to Data Model**:
   - Click on the `Model` icon on the left sidebar.
2. **Create Relationships**:

![image](https://github.com/user-attachments/assets/9de504ce-aa13-404e-a095-39ccbed55b9f)

   
   - Drag `pickup_point_reference` from `orders_data` to `reference` in `pickup_points_data`.
   - Set and Ensure the relationship is set as `Single` and `Many to One` (from `orders_data` to `pickup_points_data`).

### 4. Creating Calculated Columns

1. **Add Route Information to Orders Table**:
   - Go to the `Data` view.
   - Select the `orders_data` table.
   - Click on `Modeling > New Column`.
   - Use the following DAX formula:
     
![image](https://github.com/user-attachments/assets/6a68446a-652e-4313-ac9c-1800d11f9464)

2. **On-time Delivery Calculation**:
   - Click on `Modeling > New Column`.
   - Use the following DAX formula:
    
![image](https://github.com/user-attachments/assets/e9e337c6-2aca-4be7-97b2-362a5a99ee6e)


3. **On-time Collection Calculation**:
   - Click on `Modeling > New Column`.
   - Use the following DAX formula:

![image](https://github.com/user-attachments/assets/66aa64e0-8639-44bf-aee1-7105a12b966a)

4. **Profitability Calculation**:
##### Click on `Modeling > New Column` and create the following columns:
##### `revenue`
![image](https://github.com/user-attachments/assets/781ad34d-6f7d-4a81-b826-68ede2857efe)

##### `sms_cost`
![image](https://github.com/user-attachments/assets/54d8e809-1bc4-4a44-b66f-9393056de968)
   
##### `monthly_cost`
![image](https://github.com/user-attachments/assets/fb47dce2-d739-49c5-8230-be0ee189c69a)
  
##### `profitability`
![image](https://github.com/user-attachments/assets/97cbc6e9-7f6a-4db4-abc6-a216d33ca4be)
     
5. **Year-on-Year Growth Calculation**:
   - Creating a year column:
     ```dax
     year = YEAR(orders_data[order_created_at])
     ```
   - Creating order count column:
     ```dax
     order_count = COUNT(orders_data[order_id])
     ```
   - Creating year-on-year growth measure:
     ```dax
     yoy_growth = 
     VAR CurrentYear = YEAR(TODAY())
     VAR LastYear = CurrentYear - 1
     VAR CurrentYearOrders = CALCULATE(COUNT(orders_data[order_id]), orders_data[year] = CurrentYear)
     VAR LastYearOrders = CALCULATE(COUNT(orders_data[order_id]), orders_data[year] = LastYear)
     RETURN
     DIVIDE(CurrentYearOrders - LastYearOrders, LastYearOrders, 0)
     ```

6. **Pickup Point Health Calculation**:
   - Creating a measure for pickup point health:
     ```dax
     pickup_point_health = 
     VAR ProfitabilityScore = SUM(orders_data[profitability])
     VAR GrowthScore = [yoy_growth]
     RETURN
     ProfitabilityScore + GrowthScore
     ```




