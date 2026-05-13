# Diabetes Healthcare Analytics Dashboard using SQL and Power BI

---

### Project Overview  
This project explores **state-wise stock sales data** for used Tesla vehicles across the U.S., leveraging **Power BI dashboards** to compare model performance, price trends, mileage, and accident history.

The goal is to extract insights into:
Analyze a diabetes healthcare dataset to identify:

- High-risk patients
- Key diabetes indicators
- Age and demographic risk patterns
- Health metric trends 



---

### Tools & Technologies  
- SQL
- Data Cleaning
- Data Analysis  
- Healthcare Analytics
- DAX
- Power BI Visualization
- Dashboard Design

---

### Excel for Data Formatting & Pre-Processing  

- Cleaned missing values and duplicates  
- Formatted numeric fields (e.g., price, mileage, EMI)  
- Standardized column names and categories  
- Created calculated fields like EMI (if missing)  
- Prepared structured layout for Power BI import  





### Dataset Breakdown  
The dataset used for this analysis includes:
Typical columns:

  Column	                   Meaning
- Pregnancies   	           Number of pregnancies
- Glucose	                   Glucose level
- BloodPressure	             Blood pressure
- SkinThickness	             Skin thickness
- Insulin	                   Insulin level
- BMI	                       Body Mass Index
- DiabetesPedigreeFunction	 Family history indicator
- Age	                       Patient age
- Outcome	                   1 = diabetic, 0 = non-diabetic



---

### SQL
- Create Database & Table

```
CREATE DATABASE DiabetesDB;

USE DiabetesDB;

CREATE TABLE diabetes_data (
    Pregnancies INT,
    Glucose INT,
    BloodPressure INT,
    SkinThickness INT,
    Insulin INT,
    BMI DECIMAL(5,2),
    DiabetesPedigreeFunction DECIMAL(5,3),
    Age INT,
    Outcome INT
);

```
- Data Cleaning in SQL
- Healthcare datasets often contain zeros for missing values.

Find Missing/Invalid Values
```
SELECT *
FROM diabetes_data
WHERE Glucose = 0
   OR BloodPressure = 0
   OR BMI = 0;
```
- Replace Invalid Values with NULL
```
UPDATE diabetes_data
SET Glucose = NULL
WHERE Glucose = 0;

UPDATE diabetes_data
SET BloodPressure = NULL
WHERE BloodPressure = 0;

UPDATE diabetes_data
SET BMI = NULL
WHERE BMI = 0;
```
- Handle NULL Values

Option 1 → Replace with averages
```
UPDATE diabetes_data
SET BMI = (
    SELECT AVG(BMI)
    FROM diabetes_data
)
WHERE BMI IS NULL;

```
- Exploratory SQL Analysis
- 1. Diabetes Distribution
```
SELECT
    Outcome,
    COUNT(*) AS TotalPatients
FROM diabetes_data
GROUP BY Outcome;
```
Insight:

Percentage of diabetic vs non-diabetic patients

- 2. Average BMI by Outcome
```
SELECT
    Outcome,
    ROUND(AVG(BMI),2) AS AvgBMI
FROM diabetes_data
GROUP BY Outcome;
```
Insight:

Higher BMI may correlate with diabetes

- 3. High-Risk Patients
```
SELECT *
FROM diabetes_data
WHERE Glucose > 140
  AND BMI > 30
  AND Age > 40;
```
Insight:

Identify potentially high-risk patients

- 4. Age Group Risk Analysis
```
SELECT
    CASE
        WHEN Age < 30 THEN 'Under 30'
        WHEN Age BETWEEN 30 AND 45 THEN '30-45'
        WHEN Age BETWEEN 46 AND 60 THEN '46-60'
        ELSE '60+'
    END AS AgeGroup,

    COUNT(*) AS TotalPatients,

    SUM(CASE WHEN Outcome = 1 THEN 1 ELSE 0 END) AS DiabeticPatients

FROM diabetes_data
GROUP BY AgeGroup
ORDER BY AgeGroup;
```
Insight:

Which age group has the highest diabetes rate

- 5. Glucose Trend Analysis
```
SELECT
    CASE
        WHEN Glucose < 100 THEN 'Normal'
        WHEN Glucose BETWEEN 100 AND 125 THEN 'Prediabetes'
        ELSE 'Diabetes'
    END AS GlucoseCategory,

    COUNT(*) AS Patients
FROM diabetes_data
GROUP BY GlucoseCategory;
```
- 6. Correlation-Type Analysis
```
SELECT
    Outcome,
    AVG(Glucose) AS AvgGlucose,
    AVG(BMI) AS AvgBMI,
    AVG(Age) AS AvgAge
FROM diabetes_data
GROUP BY Outcome;
```

- 7. Top Risk Factors
```
SELECT
    CASE
        WHEN BMI > 35 THEN 'Obese'
        ELSE 'Non-Obese'
    END AS BMIGroup,

    COUNT(*) AS Patients,

    SUM(CASE WHEN Outcome = 1 THEN 1 ELSE 0 END) AS DiabeticPatients

FROM diabetes_data
GROUP BY BMIGroup;
```

- Create SQL Views for Power BI
```
CREATE VIEW vw_risk_analysis AS
SELECT
    Age,
    BMI,
    Glucose,
    Outcome,

    CASE
        WHEN Glucose > 140
         AND BMI > 30
         AND Age > 40
        THEN 'High Risk'

        ELSE 'Moderate Risk'
    END AS RiskCategory

FROM diabetes_data;
```

- Age Analysis View
```
CREATE VIEW vw_age_analysis AS
SELECT
    CASE
        WHEN Age < 30 THEN 'Under 30'
        WHEN Age BETWEEN 30 AND 45 THEN '30-45'
        WHEN Age BETWEEN 46 AND 60 THEN '46-60'
        ELSE '60+'
    END AS AgeGroup,

    Outcome,
    Glucose,
    BMI

FROM diabetes_data;
```



---

### Connect SQL to Power BI 

Load:

diabetes_data
vw_risk_analysis
vw_age_analysis

### Power BI Dashboards

Dashboard 1 — Patient Risk Dashboard

Purpose:
Identify high-risk diabetic patients

<img width="765" height="430" alt="image" src="https://github.com/user-attachments/assets/6496bfd5-233a-4582-b1e1-b19dea3e8f38" />

Dashboard 2 — Health Metrics Dashboard


<img width="766" height="430" alt="image" src="https://github.com/user-attachments/assets/36edfb99-9638-4430-9733-343f1673dcba" />


![model x dashboard](https://github.com/user-attachments/assets/f3409ee2-8347-4a86-92da-971011239a3c)

![model y](https://github.com/user-attachments/assets/c7567f61-9a0a-4c38-b835-32f0261c7d48)


---

### Build the Model in Python

Step 1: Load dataset

```
import pandas as pd

df = pd.read_csv("diabetes.csv")
df.head()
```

Step 2: Define features & target


```
X = df[['Glucose','BMI','Age','BloodPressure','Insulin','Pregnancies']]
y = df['Outcome']
```

- 2. Average BMI by Outcome
```
SELECT
    Outcome,
    ROUND(AVG(BMI),2) AS AvgBMI
FROM diabetes_data
GROUP BY Outcome;
```

- 2. Average BMI by Outcome
```
SELECT
    Outcome,
    ROUND(AVG(BMI),2) AS AvgBMI
FROM diabetes_data
GROUP BY Outcome;
```

- 2. Average BMI by Outcome
```
SELECT
    Outcome,
    ROUND(AVG(BMI),2) AS AvgBMI
FROM diabetes_data
GROUP BY Outcome;
```

- 2. Average BMI by Outcome
```
SELECT
    Outcome,
    ROUND(AVG(BMI),2) AS AvgBMI
FROM diabetes_data
GROUP BY Outcome;
```


### Insights & Findings  
1. **Model Y** is the most affordable option at ~$33.9K with the lowest EMI ($529.37).  
2. **Model X** has the highest average price and EMI, making it the most premium used Tesla option.  
3. **Model 3** is the most balanced in terms of price vs. mileage.  
4. **Cybertruck** data suggests it's new to the market (lower mileage) but priced mid-range.  
5. **States like NY, OH, NC, VA** appear across the top for mileage, suggesting high usage or resales.  
6. **Accident-free rate** is high across all models, with **Model 3 and Y** being the safest (>90%).

---

### Recommendations  

#### 1. 🎯 Target High-Volume States  
Focus marketing and resale efforts in states like **NY, OH, NC, VA**, which show high vehicle turnover and mileage demand.

#### 2. 💼 Price Based on DAS  
Use **Driver Assist System tiers** as a pricing lever.  
> Full Self-Driving variants command significantly higher average prices (up to **$63.2K**).

#### 3. 📉 Promote Lower EMI Models  
**Model Y and Model 3** with lower EMIs may be ideal for entry-level EV buyers or fleet purchases.

#### 4. 🔍 Expand Dataset for Model Y & Cybertruck  
As newer models, their datasets will grow. Monitoring pricing trends can help forecast **depreciation** and **resale value**.

---

### Future Work
* Integrate live market data (Tesla API, vehicle resale sites)
* Include model year and battery degradation metrics
* Predict resale value using regression models (Python + ML)
* Build a web-based dashboard using Power BI Embedded or Streamlit
* Analyze charging patterns and locations (if location data becomes available)
