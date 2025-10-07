# **India Energy Transition Dashboard (2000-2025)**

**Power BI Dashboard**

## 1\. Project Overview

This project develops an interactive Power BI dashboard to analyse India's installed capacity, generation, energy mix, renewable share, and emissions at national and state levels (2000-2025) & to provide insights useful for energy policy, planning, and consulting.

## 2\. Objectives

- Understand the progress of India's energy transition from fossil fuels to renewables by combining domain expertise in the energy systems with Power BI data analytics skills.
- Visualize capacity addition, generation trends, and RE share at both national & state level.
- Quantify emissions from fossil generation and avoided emissions from renewable energy.
- Build interactive dashboards with filters and drilldowns for decision-making.
- Demonstrate data cleaning, modelling, and visualization skills in Power BI.

## 3\. Repository Structure

India_Energy_Transition/

**README.md**

**Data/**

- raw/ # Original downloaded datasets
- processed/ # Cleaned & structured Excel files

**Docs/**

- Data_model_diagram
- DAX_measures

**Output docs/**

- Report_India_Energy_Transition.pdf
- #Image outputs

**PBIX/**

- India_Energy_Transition_Dashboard.pbix

## 4\. Data

### 4.1 Data Sources

| **Datasets** | **Source** |
| --- | --- |
| Electricity Installed Capacity (State/national, by source) | NITI Aayog Energy Data Portal (ICED)  <br><https://iced.niti.gov.in> |
| Electricity Power Generation (State/national, by source) | NITI Aayog Energy Data Portal (ICED)  <br><https://iced.niti.gov.in> |
| Emission factors | CEA Baseline CO₂ factors (<https://cea.nic.in>) |
| India States Locations (Longitude, Latitude) | <https://www.kaggle.com/> |

### 4.2 Assumptions & Limitations

- Year Ranges: Multi-year blocks (e.g., 2002-07) converted to single-year data at range-end.
- Renewable Definition: Solar, Wind, Small-Hydro, Bio Power.
- Capacity Factor: Derived directly from reported generation values.
- Missing Data: year gaps data filled using interpolation.
- Scope: Only national level & Indian states considered (Union Territories excluded).
- India Map: Used ArcGIS (only visible in online mode) because of Power BI desktop version limitation

### 4.3 Data Preparation

- **Source Data**: Downloaded raw datasets from NITI Aayog Energy data portal & CEA.
- **Restructuring Years:** Where only 5-year cumulative values were given, disaggregated into single years by interpolation. Renamed all "March-end" columns to reflect the corresponding year.
- **Renewable Energy (RE) Consolidation:** Created RES Total (2009-2025). Grouped sub-categories into Solar (all types combined) and Biopower for consistency (2014-2025).
- **Back casting (1997, 2002, 2007):** Derived missing values using multiplying factors based on 2009 shares: Multiplying factor = year 2009 specific row value/ Total RES for year 2009
- **Interpolation:** Estimated annual values between plan-period years (1997, 2002, 2007) by dividing differences equally across intervals.
- **Validation**: Cross-checked reconstructed yearly series with India's 5-Year Plan periods for consistency.
- **Final Transformation:** Clean dataset structured into columns: Year | State | Source | Capacity (MW) | Generation (GWh) | Emissions (tCO₂ per MWh)
- **Cleaning Tools:** MS Excel (pre-processing), Power BI (pre-processing, modelling & visualization).

### 4.4 Standardization

- Units: Capacity (MW), Generation (GWh).
- Source names harmonized: Coal, Oil & Gas, Nuclear, Hydro, Solar, Wind, Small-Hydro, Bio Power
- Added "All India" rows in fact tables for direct national-level KPIs.
- Excluded Union Territories for simplification

## 5\. Methodology & Calculation

- Data Collection & Integration
- Data Cleaning and Transformation
- Data Modelling
- KPI & Metric Calculation
- Dashboard Development
- Validation & Cross-Check

### 5.1 Emission Factors & Calculations

Emission Factors (CEA Baseline Database, FY 2023-24):

EF<sub>Coal</sub> = 0.97 tCO₂/MWh

EF<sub>Oil & Gas</sub> = 0.45 tCO₂/MWh

Nuclear, Hydro & Renewables = 0

EF<sub>source</sub> = Emission factor of a particular fuel source

EF<sub>fossil, avg</sub>\= weighted average emission factor of fossil fuels (Coal, Oil & gas)

EF<sub>fossil, avg</sub>\=\[∑<sub>Fossil sources</sub> (Generation<sub>Source</sub>\*EF<sub>source</sub>)\]/ \[∑<sub>fossil sources</sub> (Generation<sub>Source</sub>)\]

### 5.2 Efficiency, Utilization and Emission Calculation

- Total Emissions = ∑<sub>all sources</sub>(Generation<sub>Source</sub>\*EF<sub>source</sub>)
- Avoided Emissions = Total Renewable Generation \* EF<sub>fossil, avg</sub>
- Grid Emission Intensity gCO2 per kWh = (Total Emission)/(Total Generation )
- Avoided Emission = Total Renewable Generation\* EF<sub>fossil,avg</sub>
- Capacity Factor = (Total Generation)/(Total Capacity\*Yearly Hours)

## 6\. Data Model & Tables

- The model follows a Star Schema design in Power BI for efficient querying and flexible analysis.
- Fact tables store the main quantitative data for capacity, generation & emission factors
- Dimension tables provide descriptive attributes (state, region, source, year) to allow easy filtering/slicing

Relationship: Many to one between facts and dimension tables

| **From Table** | **Column** | **To Table** | **Cardinality** |
| --- | --- | --- | --- |
| Capacity_Table | Source | \_Dim_Source | Many to one |
| Capacity_Table | State | \_Dim_State | Many to one |
| Capacity_Table | Year | \_Dim_Year | Many to one |
| Generation_Table | Source | \_Dim_Source | Many to one |
| Generation_Table | State | \_Dim_State | Many to one |
| Generation_Table | Year | \_Dim_Year | Many to one |
| Location_Coordinates | State | \_Dim_State | One to one |

### 6.1 Fact Tables

- **Generation_table:** Year, State, Source, Generation (GWh)
- **Capacity_table:** Year, State, Source, Capacity (MW)
- **EF_Table:** Source, Emission Factor (tCO2 PER MWh), notes
- **Location_Coordinates:** State, Latitude, Longitude

### 6.2 Dimension Tables

- **DimYear:** Calendar year (2000-2025)
- **DimState:** State, Region
- **DimSource:** Source, Source group, Is_Renewable (True/False), Emission_Factor_tCO₂/MWh

## 7\. Key Measures & KPIs

Core metrics developed in DAX, designed to work at both state and national levels:

\_Measures: Logical grouping of all KPIs

- Capacity Share (%)
- Generation Share (%)
- Renewable Energy Share
- Capacity Factor
- Grid Emission Intensity
- Avoided Emission
- Growth metrics (2020-25)
- Top 5 States by different metrics

| **Capacity** | **Generation** | **Emissions** | **National Cards** |
| --- | --- | --- | --- |
| CAP_RE_MW<br><br>CAP_Total_MW<br><br>CAP_Total_NoAllIndia_MW<br><br>CAP_RE_NoAllIndia_MW<br><br>Cap_BySource_MW | GEN_RE_GWh<br><br>GEN_Total_GWh<br><br>GEN_Total_NoAllIndia_GWh<br><br>GEN_RE_NoAllIndia_GWh<br><br>Gen_BySource_GWh | EM_AVG_Fossil_tCo2_perMWh<br><br>EM_Avoided_MtCO2<br><br>EM_Intensity_Total_gCO2_per kWh<br><br>EM_Total_MtCO2<br><br>EM_Total_NoAllIndia_MtCO2 | Cap_%\_by_Source<br><br>CAP_Growth_2020_2025_%<br><br>CAP_Growth \_YoY_fixed_%<br><br>CAP_RE_fixed_MW<br><br>CAP_Total_fixed_MW<br><br>CF_Total_Fixed_%<br><br>EM_Avoided_fixed_MtCO2<br><br>EM_intensity_Total_gCO2_per_kWh_fixed<br><br>EM_Total_fixed_MtCO2<br><br>Gen_%\_by_source_<br><br>GEN_Growth_2020_2025_%<br><br>GEN_Growth_YoY_\_fixed_%<br><br>GEN_RE_fixed_GWh<br><br>GEN_Total_fixed_GWh |

| **Efficiency and Utilization** | **Shares (Energy Mix)** | **State Cards** |
| --- | --- | --- |
| CF_RE_%<br><br>CF_Total_%<br><br>CAP_Growth_YoY_%<br><br>GEN_Growth_YoY_% | CAP_RE_Share_%<br><br>CAP_RE_NoAllIndia_Share_%<br><br>CAP_RE_Source_Share_%<br><br>CAP_Source_Share_%<br><br>GEN_RE_Share_%<br><br>GEN_RE_Share_NoAllIndia_%<br><br>GEN_Source_Share_%<br><br>GEN_RE_Source_Share_% | CAP_Growth_State_2020_2025_%<br><br>CAP_RE_State_fixed_MW<br><br>CAP_Total_State_fixed_MW<br><br>CF_Total_State_Fixed_%<br><br>EM_Avoided_State_fixed_MtCO2<br><br>EM_intensity_Total_State_gCO2_per_kWh_fixed<br><br>EM_Total_State_fixed_MtCO2<br><br>GEN_Growth_State_2020_2025_%<br><br>GEN_RE_State_fixed_GWh<br><br>GEN_Total_State_fixed_GWh<br><br>Top5_Rank_CAP<br><br>Top5_Rank_GEN<br><br>Top5_Rank_CAP_RE<br><br>Top5_Rank_EM_Avoided<br><br>Top5_Rank_EM_Total<br><br>Top5_Rank_RE_GEN |

## 8\. Dashboard Layout

### 8.1 Page 1 - National Level Energy Summary

- **Cards (2025 baseline):** Renewable capacity, Renewable Generation, Total Capacity, Total Generation, avoided emissions, Total CO2 emissions, Grid emission intensity, capacity factor (%), 2020-25 capacity and generation growth.
- **Graphs:** Total capacity & generation trends, emissions trends, avoided emissions, Capacity and Generation share by source for year 2000-2025
- **Slicers:** Year, source, source Type, Is Renewable.
![National Level  Energy Summary](https://github.com/user-attachments/assets/82640fb9-21c2-4ea1-9981-a9391be77717)

### 8.2 Page 2 - State Level Energy Summary (1/2)

- **Cards (2025):** Same KPIs as national but state-specific.
- **Graphs:** Same trends but state-specific for year 2016-2025
- **Slicers:** Year, state, region, source, source type, Is renewable
![State Level  Energy Summary page1_2](https://github.com/user-attachments/assets/36fd44f6-91d0-4380-8281-4c3f6ab2fb41)

### 8.3 Page 3 - State Level Energy Summary (2/2)

- **Graphs**: Top 5 states by capacity, Renewable capacity, generation, Renewable generation, emissions, avoided emissions.
- **Map**: State-wise total & Renewable generation.
- **Slicers**: Year, state, region, source, source type, Is renewable
![State Level  Energy Summary page2_2](https://github.com/user-attachments/assets/3e4249b2-da19-41cd-b780-f3f3408f4252)

## 9\. Analytical Use Cases

- **National-level insights:** Track India's RE transition, fossil reliance, and emissions avoidance.
- **State benchmarking:** Compare states by generation, capacity mix, and emission.
- **Top performers:** Identify top 5 states driving the Renewable energy push.
- **Scenario testing:** Use slicers to dynamically see year-wise, source-wise, and region-wise shifts.

## 10\. Key Insights

- India's Renewable share in capacity is significantly higher than in generation shows utilization challenge.
- Coal remains the backbone of power generation (~70%), driving emissions.
- Avoided emissions from Renewable Energy are growing rapidly, reducing around 240 MtCO₂ in 2025.
- State-level variations: Southern & Western states dominate in solar and wind, while Eastern India is still coal-heavy.
- Rajasthan and Gujarat lead in Renewable generation while Chhattisgarh and Uttar Pradesh lead in Emissions.

## 11\. Technical Skills Demonstrated

- **Power BI:** Data transformation (Power Query), star schema modelling and dimension design, DAX, measure, Dashboard design principles, interactive map
- **Excel:** Data cleaning & transformation
- **Analytics:** KPI design, Emission modelling, actionable insights
- **Energy Domain:** Knowledge of India's power sector, emissions accounting, RE integration.

## 12\. Business Value

- Helps policymakers assess progress toward renewable targets.
- Provides state-level insights for investment prioritization.
- Quantifies emission reduction benefits of RE adoption.

## 13\. Future Scope

- Integrate Real-time data APIs
- Add Financial Metrics - LCOE, tariff data etc.
- Scenario Analysis - Add projections for 2030/2070 net zero
- Automate updates via Power Query
- Benchmarking - Compare India with global Renewable/CO₂ intensity benchmarks

## 14\. Dashboards Access (How to Use)

- Download the Power BI file(.pbix) from repository link below  
    <br/>\[Download from GitHub Repository\]  
    <br/>(<https://github.com/tarunchandra007/India_Energy_Transition_Dashboard/raw/refs/heads/main/PBIX/India_Energy_Transition_Dashboard.pbix>)  

- Open file in Power BI Desktop.
- Keep Internet ON to view India map
- Use slicers to explore national/state data

## Author

**TARUN CHANDRA**

M.TECH.(Energy Systems Engineering) - IIT BOMBAY

\[Linkedin Profile\]  

(<https://www.linkedin.com/in/tarun-chandra-72701b85>)
