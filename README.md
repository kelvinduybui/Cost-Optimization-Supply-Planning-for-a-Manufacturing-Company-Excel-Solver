# Supply Planning | Excel Solver  

**Author:** Bùi Xuân Bảo Duy (Kelvin)  
**Date:** September 2025  
**Tools Used:** Excel Solver  

## 🗂️ Table of Contents
1️⃣ [Context](#context)  
2️⃣ [Problem Description & Parameters](#problem-description--parameters)  
3️⃣ [Model Formulation](#model-formulation)  
4️⃣ [Solution Approach](#solution-approach)  
5️⃣ [Visualization](#visualization)  
6️⃣ [Insights & Recommendations](#insights--recommendations)  

---

## 1️⃣ Context

📘 **Main Context**  
- A **manufacturing company** operates in a **highly competitive market** where **efficient production** and **inventory management** are critical to **meeting customer demand** while **minimizing costs**  
- The company faces increasing pressure to **balance demand fluctuations** with **limited production capacity**, **strict cost controls**, and **service level requirements**  
- The **Supply Planning team** is tasked with building a **6-month production plan (Jan–Jun)** using **Excel**:  
  - Ensure **demand fulfillment** while adhering to operational constraints such as **inventory policies**, **workforce capacity**, and **subcontracting restrictions**  
  - Compare alternative strategies (e.g., **Chase vs. Level**) to support **cost-effective decision-making**  

👥 **Target Audience**  
- Supply Planning & Production Planning Teams  
- Operations & Manufacturing Managers  
- Decision-makers & Stakeholders  

🎯 **Objective**  
To design a **supply planning model** that ensures **demand fulfillment, prevents stockouts, minimizes costs** (inventory, production, overtime, subcontracting), and improves **resource utilization** — ultimately supporting operational efficiency and customer satisfaction.  

🛠️ **Tools Used**  
- **Excel:** Used to input data and calculate costs (**inventory, workforce, hiring/layoff, overtime, subcontracting, etc.**) through direct formulas  

- **Excel Solver:** Used to test different planning strategies (**Chase vs. Level**) and determine the **optimal production–workforce plan** under constraints (**no stockouts, ending inventory requirement, limited overtime, etc.**)

---

## 2️⃣ Problem Constraints & Parameters  
### Definition
- **Chase Plan**: A production strategy where output is adjusted each period to match demand, keeping inventory minimal but causing frequent workforce changes.
- **Level Plan**: A production strategy where output is kept constant over time, stabilizing the workforce but leading to inventory build-up when demand fluctuates.

### Planning Horizon  
- 6 months: **Jan – Jun**  

### ⚙️ Parameters

| **Object**              | **Qty** | **Unit**           |
| ----------------------- | ------: | ------------------ |
| **Inventory Holding Cost**  |       2 | USD/unit/month     |
| **Stockout Cost**           |       5 | USD/unit/month     |
| **Hiring Cost**             |     300 | USD/worker         |
| **Regular-Time Salary**     |     640 | USD/worker/month   |
| **Layoff Cost**             |     500 | USD/worker         |
| **Subcontracting Cost**     |      30 | USD/unit           |
| **Overtime Cost**           |       6 | USD/hour           |
| **Available Regular Time**  |     160 | hours/month/worker |
| **Maximum Overtime**        |      10 | hours/month/worker |
| **Starting Inventory**      |   1,000 | units              |
| **Ending Inventory Target** |     500 | units              |
| **Starting Workforce**      |      80 | workers            |
| **Material Cost**           |      10 | USD/unit           |
| **Net Production Time**     |       4 | hours/unit         |
| **Working Day**             |      20 | days/month         |
| **Working Hour**            |       8 | hours/day/worker   |

### 📋 Specific Business Rules  

- **Chase Plan**  
  - Do not create any inventory from **Jan to May**, ending inventory of **June = 500**  
  - Do not have any **stockout**  
  - Do not use any **overtime** and **subcontracting**  
  - Try to produce what is demanded (**production closely follows monthly demand; workforce can be adjusted via hiring/layoff**)  

- **Level Plan**  
  - Do not use any **overtime** and **subcontracting**  
  - Produce the **same quantity** every period (**fixed production level each month**)  
  - **Carrying inventory** is allowed  
  - Do not create any **stockout**  
  - Aim to **minimize required capacity** (minimize number of workers needed given fixed production rate)  

### 🔎 Outputs Required  
- **Month-by-month table**: Gross Demand, Net Demand, Production, Workers Employed, Workers Hired, Workers Laid off, On-hand Inventory, Overtime, Subcontract, Stockout, Total Cost  
- **Scenario comparison**: Total cost breakdown (material, holding, hiring/layoff, salary, OT/subcontract if used, stockout)  
- **Charts**: Demand vs Production, Workforce trend, Inventory level, Cost breakdown

---

## 3️⃣ Model Formulation  
### Planning Model  

- Each **row** = 1 month (**Jan–Jun**)  
- Each **column** = Demand, Workforce, Production, Inventory, Costs  

#### 🔹 Inputs: 
- Gross Demand   
- Parameters

#### 🔹 Decision Variables
- Production(i)  
- Workers Employed(i)  
- Workers Hired(i)  
- Workers Laid off(i)  
- On-hand Inventory(i)  

#### 🔹 Key Formulas

| Description | Formula |
|-------------|---------|
| Net demand in period *i* | `Net Demand(i) = Gross Demand(i) – Inventory(i–1)` |
| Production quantity in period *i* | `Production(i) = WorkersEmployed(i) × WorkingDay × WorkingHour ÷ NetProductionTime` |
| Number of workers employed in period *i* | `WorkersEmployed(i) = Workers(i–1) + Hired(i) – LaidOff(i)` |
| Inventory balance at end of period *i* | `On-hand Inventory(i) = Inventory(i–1) + Production(i) + Overtime(i) + Subcontract(i) + Stockout(i) – Stockout(i–1) – Gross Demand(i)` |
| Total cost in period *i* | `Total Monthly Cost(i) = Production(i) × MaterialCost + WorkersEmployed(i) × RegularTimeSalary + Hired(i) × HiringCost + LaidOff(i) × LayoffCost + Inventory(i) × HoldingCost + Overtime(i) × OvertimeCost + Subcontract(i) × SubcontractCost + Stockout(i) × StockoutCost` |

#### 🔹 Solver Objective
- **Minimize**: TotalCost = Cost(Jan) + Cost(Feb) + Cost(Mar) + Cost(Apr) + Cost(May) + Cost(Jun)
- Ensure all constraints defined by the chosen plan are satisfied.  

## 4️⃣ Solution Approach  

### Enable Excel Solver  
Go to **File > Options > Add-ins > Excel Add-ins > Go**, check **Solver Add-in**, click **OK**, then find **Solver** under the **Data** tab.

### ⚡ Chase Plan  

#### Supply Planning with Excel & Solver

The supply planning problem for chase plan is addressed using **Excel formulas** and **Solver optimization**.  
The approach consists of the following steps:

#### 📝 Step 1. Model Setup in Excel
- Build a month-by-month planning table (Jan–Jun).
- Input fixed data: gross demand, cost parameters, initial workforce, and inventory.
- Define **decision variable cells**: Production, Workers Employed, Hired, Laid off, Inventory (highlighted in the table).
- Link all other cells (Net Demand, Workforce Balance, Inventory Balance, Total Costs) using formulas.

![Images](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Chase%20initial.png?raw=true)

#### ⚙️ Step 2. Define Constraints  
Constraints differ by planning strategy:  

**Chase Plan**
- No inventory from Jan–May; ending inventory of Jun = 500 units.
- No stockout allowed.
- No overtime and subcontracting.
- Production closely follows monthly demand (workforce adjusts via hiring/layoff).

![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Chase%20Solver.png?raw=true)  

#### 📈 Step 3. Solver Result
- **Objective**: Minimize total cost across the 6-month horizon.  
- **Decision Variables**: Production, Workforce Hired, Workforce Laid off, Workforce Employed, Inventory.  
- **Constraints**: As defined above for Chase plan.  
- **Solver Method**: *Simplex LP* (suitable for linear relationships in the model).

![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Chase%20result.png?raw=true)

#### 🔍 Step 4. Scenario Takeaways  
- The chase strategy helps **eliminate inventory holding costs** (almost zero inventory). However, it leads to very **high labor costs** due to the continuous hiring and dismissing of employees.

### 📊 Level Plan:  

#### Supply Planning with Excel & Solver

The supply planning problem for Level Plan is addressed using **Excel formulas** and **Solver optimization**.  
The approach consists of the following steps:

#### 📝 Step 1. Model Setup in Excel
- Build a month-by-month planning table (Jan–Jun).
- Input fixed data: gross demand, cost parameters, initial workforce, and inventory.
- Define **decision variable cells**: Production, Workers Employed, Hired, Laid off, Inventory (highlighted in the table).
- Link all other cells (Net Demand, Workforce Balance, Inventory Balance, Total Costs) using formulas.

![Images](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Level%20initial.png?raw=true)

#### ⚙️ Step 2. Define Constraints  
Constraints differ by planning strategy:  

**Level Plan**
- Allow carrying inventory from Jan–May
- No stockout allowed.
- No overtime and subcontracting.
- Produce the same quantity in every period.

![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Chase%20Solver.png?raw=true)  

#### 📈 Step 3. Solver Result
- **Objective**: Minimize total cost across the 6-month horizon.  
- **Decision Variables**: Production, Workforce Hired, Workforce Laid off, Workforce Employed, Inventory.  
- **Constraints**: As defined above for Level plan.  
- **Solver Method**: *Simplex LP* (suitable for linear relationships in the model).

![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Level%20result.png?raw=true)

#### 🔍 Step 4. Scenario Takeaways
- The level plan helps **keep workforce and production stable**, avoiding production fluctuations. However, it incurs **high inventory costs** (due to overproduction in low-demand months) and a large layoff cost at the beginning.

## 5️⃣ Visualization
### Net Demand vs Production
![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Net%20Demand%20vs%20Production.png?raw=true)

#### Key Takeaways:
- ⚡ **Chase Plan:** **Production** closely **follows demand** across months, ranging from 600 units in January to 2,700 units in June, ensuring output always matches actual requirements.
- 📊 **Level Plan:** Production is **fixed** at 1,425 units each month, while **net demand fluctuates widely**, creating a clear difference between constant supply and variable demand.

### Onhand Inventory
![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Onhand%20Inventory.png?raw=true)

#### Key Takeaways:
- ⚡ **Chase Plan:** Keeps **inventory minimal**, starting with 1,000 units and ending with only 500 units.
- 📊 **Level Plan:** Inventory builds up steadily from 1,000 → 7,175 units as **constant production** exceeds demand in low-demand months.

### Workforce
![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Workforce.png?raw=true)

#### Key Takeaways:
- ⚡ **Chase Plan:** **Workforce fluctuates** sharply with continuous hiring and layoffs (layoff 65 in Jan, hire 80 from Feb–Apr, layoff 40 in May, hire 13 in Jun), leading to high labor cost.
- 📊 **Level Plan:** **Stable workforce** maintained at 36 workers (after laying off 44 in Jan), ensuring no further changes throughout the period.

### Cost
![Image](https://github.com/kelvinduybui/Supply-Planning/blob/main/Images/Cost%20distribution.png?raw=true)

#### Key Takeaways:
- ⚡ **Chase Plan**:
  - Cost is dominated by worker employed (51%) and production (32%), with significant shares from layoff (11%) and hiring (6%), reflecting **heavy workforce fluctuation**.
  - **Cost driven by labor**: **Total cost = 484,250 USD**, mainly from **hiring/layoff expenses** and **regular-time salaries**, with **no overtime, subcontracting, or stockouts**.

- 📊 **Level Plan**:
  - Cost is concentrated in worker employed (48%) and production (30%), with additional burden from inventory holding (14%) and layoff (8%), reflecting **stable workforce but high inventory**.
  - **Cost structure:** Total cost = **284,088 USD**, mainly from **regular-time salaries, initial layoff cost, and rising inventory holding cost** (no overtime, subcontracting, or stockouts).

## 6️⃣ Insights & Recommendations
### Insights

#### Cost Structure Trade-off
⚡ **Chase Plan**: Total cost = **484,250 USD**.  
- ✅ **Advantage**: Almost **no inventory** → inventory cost nearly zero.  
- ❌ **Drawback**: **Workforce fluctuates** heavily → high hiring/layoff costs and labor management challenges.  

📊 **Level Plan**: Total cost = **284,088 USD**.  
- ✅ **Advantage**: **Stable workforce** with minimal fluctuation → easier labor management.  
- ❌ **Drawback**: **Inventory builds up** significantly → high inventory holding cost.  

#### ⚙️ Operational Feasibility

⚡ **Chase**: High **HR management risks** (continuous layoffs & hiring → lower employee morale, higher training costs).  

📊 **Level**: High **financial and storage risks** (large inventory, potential obsolescence, especially for short product life cycles).  

### Recommendations
- If the company prioritizes **labor stability** → lean toward **Level plan**.
- If the company prioritizes **inventory minimization & working capital** efficiency → lean toward **Chase plan**.
- However, **Pure Chase or Level plan** is not optimal → A **hybrid approach** is preferable: e.g., maintain a leveled production rate around the average demand + allow some overtime/subcontracting to cover peak demand reduces inventory without excessive workforce fluctuation.
