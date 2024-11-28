# HR Analysis
## Table Contant
- Project Overview
- Data Sourses
- Recommendations
## Project Overview
o	Company information about employees describes each employee, his education level, name, gender, salary, performance, years at company, satisfaction, attrition……
o	five tables (EducationLevel, Employee, PerformanceRating, RatingLevel, SatisfiedLevel)
Tables Description:
### Tools and Techniques
- Power BI : for visualizing
- Power Query :for cleaning
### Cleaning OverView
This documentation outlines the steps taken to clean and transform the HR dataset. The focus was on standardizing values in the EducationField column, correctly transforming the HireDate column to the appropriate date format, and creating a calculated column, Actual years at company, with decimal precision. Each transformation step is described in detail below.
### 1. Standardizing the "Marketing" Values in the EducationField Column
•	Column: EducationField
•	Issue Identified: The EducationField column contained two similar but inconsistent values for "Marketing"—one with an extra trailing space.
o	Example: "Marketing  " and "Marketing"
![image](https://github.com/user-attachments/assets/5b9712a1-edff-4dd5-913c-ad155b128b8e)
•	Cleaning Action:
o	The value "Marketing " (with an extra space) was replaced with "Marketing" to ensure consistency across the dataset.
•	Approach:
o	Applied a transformation to replace "Marketing " with "Marketing".
![image](https://github.com/user-attachments/assets/d2a21b13-1214-42c3-80f8-d7f6e8f533b4)
•	Code:
```
= Table.ReplaceValue(#"Previous Step", "Marketing ", "Marketing", Replacer.ReplaceText, {"EducationField"})
```

### 2. Correcting the "HireDate" Format
•	Column: HireDate
•	Issue Identified: The HireDate column had dates in the MM/DD/YYYY format, which could not be directly converted to the DD/MM/YYYY format needed for the analysis.
•	Cleaning Action:
o	The HireDate column was split into three separate columns for the month, day, and year using the “/” delimiter.
o	After splitting, the order of the columns was rearranged to DD/MM/YYYY format.
o	Finally, the three columns were merged back together into a single date column in the correct format.
![image](https://github.com/user-attachments/assets/3d25a3c6-f50e-4b9c-bb63-55ea08a52cb5)
•	Steps Taken:
1.	Split the HireDate column by the / delimiter to create three separate columns: Month, Day, and Year.
2.	Rearranged the columns from Month-Day-Year to Day-Month-Year.
3.	Merged the columns back into one column in the DD/MM/YYYY format.
4.	Transformed the merged column to the Date data type.
•	Code:
```
// Step 1: Split the column
Table.SplitColumn(#"Previous Step", "HireDate", Splitter.SplitTextByDelimiter("/", QuoteStyle.Csv), {"Month", "Day", "Year"})

// Step 2: Reorder and merge the columns
Table.CombineColumns(#"Previous Step", {"Day", "Month", "Year"}, Combiner.CombineTextByDelimiter("/", QuoteStyle.Csv), "HireDate")

// Step 3: Convert to Date type
Table.TransformColumnTypes(#"Previous Step", {{"HireDate", type date}})
```
### 3. Creating the "Actual years at company" Column
•	Calculated Column: Actual years at company
•	Objective: To calculate the exact tenure (in decimal years) for employees with no attrition, while retaining the value from the YearsAtCompany column for employees with attrition.
![image](https://github.com/user-attachments/assets/ea9e3e69-eca6-4ebe-ba5f-7827cc364498)
 •	Steps Taken:
1.	Created a new calculated column, "Actual years at company", based on the following logic:
	If Attrition = "No", calculate the number of years from HireDate to the current date (31/12/2022).
	If Attrition = "Yes", use the existing value from the YearsAtCompany column.
2.	Formula used for this calculation:
Table.AddColumn(#"Change Type", "Actual years at company", each if [Attrition] = "No" then Duration.Days(#date(2022, 12, 31) - [HireDate]) / 365 else [YearsAtCompany])
### 4. Rounding "Actual years at company" and Changing Data Type
•	Objective: Round the Actual years at company column to 2 decimal places for consistency and readability, and then change its type to Decimal Number.
•	Steps Taken:
1.	Applied rounding to the Actual years at company column to limit the values to 2 decimal places.
2.	Changed the column type to Decimal Number for accurate numerical representation and calculations.

Code:
```Table.TransformColumns(#"Previous Step", {{"Actual years at company", each Number.Round(_, 2), type number}})
```
![image](https://github.com/user-attachments/assets/b9159829-5ac9-4a23-8854-fdbde79bde0c)
### Summary of Transformations:
-	Standardized the "Marketing" values in the EducationField column by replacing "Marketing " with "Marketing".
-	Corrected the HireDate format by splitting, rearranging, and merging date components to follow the DD/MM/YYYY structure.
-	Created a new calculated column, "Actual years at company", to represent employees' tenure in decimal years.
-	Rounded the calculated values to 2 decimal places and converted the column type to Decimal Number for precision.

### Analysis and insights:
###	Attrition and Retention
-	What is the attrition rate by department, job role, and education field?
o	Understanding which areas have higher turnover can help target retention efforts.
•	What are the key factors (e.g., job satisfaction, distance from home, salary) influencing employee attrition?

-	Is there a correlation between tenure (Actual years at company) and attrition?
o	Investigate whether longer tenure is associated with lower attrition rates.

-	Do employees with higher salaries have lower attrition rates?
o	Examine if competitive compensation reduces turnover.
![Screenshot 2024-10-18 194843](https://github.com/user-attachments/assets/ef7cdc46-f0c0-4ad8-8284-aeaf537423fe)

 2.	Performance and productivity vs salary and promotion

-	Is there a correlation between salary and performance ratings?
o	Understand if higher salaries lead to higher performance.

-	Are employees with higher performance closer to have promotions?
o	Search the relation between performance rating and promotions.

-	Do employees promoted within the last year perform better or worse than those who haven’t been promoted?
o	Examine how promotions affect employee performance.
![Screenshot 2024-10-18 194929](https://github.com/user-attachments/assets/a8b59ad6-cdd1-4303-af8a-9b40e7301041)

 3	Departments comparison 

-	What are the differences in job satisfaction across different departments and roles?
o	Compare satisfaction levels to identify areas needing attention.

-	What are the differences between departments across average salaries, average employees rating and education levels?
![Screenshot 2024-10-18 194900](https://github.com/user-attachments/assets/fe3fc9c4-63fd-4e0f-ae0c-fad45928aac8)

##  Data Sourses
DataSet after cleaning uplaeded 
## Recommendations
-Increase Minimum Salaries and Stock Benefits: Raising base salaries and offering more stock options can help reduce employee turnover, especially for lower-paid employees. This may also improve employee performance.

-Focus on High-Turnover Roles: Jobs like data scientist, sales executive, and software engineer need special attention. The company should figure out why employees in these roles are leaving and offer better compensation or growth opportunities.

-Link Performance to Pay: Employee performance ratings should influence salary increases and promotions. Rewarding high performers will motivate employees to work harder.

-Reward Long-Term Employees: Giving salary increases based on how long employees stay at the company can encourage them to remain for longer periods.

-Address Attrition in Sales and HR: The sales and HR departments need attention due to high turnover. Finding out why employees are leaving and making improvements will help reduce attrition in these departments. Also, compensating the candidates who have left is important.






