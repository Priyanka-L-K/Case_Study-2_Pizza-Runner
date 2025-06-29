# 🍕 Pizza Runner - SQL Case Study

<p align="center">
  <img src="https://8weeksqlchallenge.com/images/case-study-designs/2.png" alt="Foodie-Fi" width="480" />
</p>

## 📋 Table of Contents
- [Introduction](#introduction)
- [Business Problem](#business-problem)
- [Available Data](#available-data)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Data Issues & Cleaning](#data-issues--cleaning)
- [Case Study Questions](#case-study-questions)
- [Key Insights](#key-insights)
- [Technologies Used](#technologies-used)
- [Project Structure](#project-structure)
- [How to Use](#how-to-use)
- [Contributing](#contributing)

## 📖 Introduction

Did you know that over **115 million kilograms** of pizza is consumed daily worldwide? 🌍

This project analyzes the operations of **Pizza Runner**, Danny's innovative pizza delivery startup that combines 80s retro styling with modern "Uberized" delivery services. Using SQL, we dive deep into the business metrics, runner performance, customer experience, and operational optimization opportunities.

## 🎯 Business Problem

Danny launched Pizza Runner with a simple but ambitious vision:
- Deliver fresh pizza from Pizza Runner Headquarters (Danny's house)
- Recruit "runners" for efficient delivery
- Build a mobile app for seamless customer ordering
- Use data-driven insights to optimize operations and direct runners

As a data scientist, Danny knew that **data collection would be critical** for business growth. This case study helps clean the data and apply calculations to optimize Pizza Runner's operations.

## 🗃️ Available Data

The analysis uses 6 main tables within the `pizza_runner` database schema:

### 📊 Database Schema Overview

| Table | Description | Key Fields |
|-------|-------------|------------|
| `runners` | Runner registration data | runner_id, registration_date |
| `customer_orders` | Individual pizza orders | order_id, customer_id, pizza_id, exclusions, extras |
| `runner_orders` | Delivery assignments & status | order_id, runner_id, pickup_time, distance, duration |
| `pizza_names` | Available pizza types | pizza_id, pizza_name |
| `pizza_recipes` | Standard pizza toppings | pizza_id, toppings |
| `pizza_toppings` | Topping reference data | topping_id, topping_name |

## 🔗 Entity Relationship Diagram

<p align="center">
  <img src="https://github.com/Priyanka-L-K/Case_Study-2_Pizza-Runner/blob/main/image.png" alt="Entity Relationship Diagram" width="480" />
</p>

## 🧹 Data Issues & Cleaning

Before analysis, several data quality issues were identified and addressed:

### `customer_orders` table:
- **Null values**: Empty strings and 'null' text in `exclusions` and `extras`
- **Inconsistent formatting**: Mixed data types and formats

### `runner_orders` table:
- **Distance field**: Mixed units (km, kilometers) and data types
- **Duration field**: Mixed formats (minutes, mins) and data types  
- **Pickup time**: Null values for cancelled orders
- **Cancellation field**: Inconsistent null representations

**Solution**: Created cleaned temporary tables with standardized data types and consistent null handling.

## ❓ Case Study Questions

This comprehensive case study covers **5 major areas** with multiple questions each:

### [🍕 A. Pizza Metrics](https://github.com/Priyanka-L-K/Case-Study-3-Foodie-Fi/blob/main/A.%20Customer%20Journey.md)  
Analyzing order volumes, pizza types, and customer behavior patterns.

**Key Questions:**
- How many pizzas were ordered?
- How many unique customer orders were made?
- What was the maximum number of pizzas delivered in a single order?
- Pizza volume analysis by hour and day of week

### [🏃‍♂️ B. Runner and Customer Experience](https://github.com/Priyanka-L-K/Case-Study-3-Foodie-Fi/blob/main/A.%20Customer%20Journey.md)  
Evaluating delivery performance and runner efficiency.

**Key Questions:**
- Runner registration patterns
- Average pickup and delivery times
- Relationship between order size and preparation time
- Runner speed analysis and delivery success rates

### [🧄 C. Ingredient Optimisation](https://github.com/Priyanka-L-K/Case-Study-3-Foodie-Fi/blob/main/A.%20Customer%20Journey.md)  
Deep dive into pizza customization and ingredient usage.

**Key Questions:**
- Most popular extras and exclusions
- Custom order formatting and ingredient lists
- Total ingredient usage across all delivered pizzas

### [💰 D. Pricing and Ratings](https://github.com/Priyanka-L-K/Case-Study-3-Foodie-Fi/blob/main/A.%20Customer%20Journey.md)  
Revenue analysis and customer satisfaction metrics.

**Key Questions:**
- Revenue calculations with different pricing models
- Design and implementation of a ratings system
- Profitability analysis including runner compensation

### [🚀 E. Bonus DML Challenges](https://github.com/Priyanka-L-K/Case-Study-3-Foodie-Fi/blob/main/A.%20Customer%20Journey.md)  
Database design considerations for business expansion.

**Focus:**
- Impact of menu expansion on existing data structure
- Implementation of new pizza additions

## 💡 Key Insights

*[This section would contain your main findings - add your key insights here after completing the analysis]*

### Business Performance
- Total orders processed: `[Your findings]`
- Success rate: `[Your findings]`
- Peak ordering times: `[Your findings]`

### Operational Efficiency  
- Average delivery time: `[Your findings]`
- Runner performance: `[Your findings]`
- Most popular customizations: `[Your findings]`

### Revenue Analysis
- Total revenue generated: `[Your findings]`
- Impact of extras on profitability: `[Your findings]`

## 🛠️ Technologies Used

- **Database**: PostgreSQL / MySQL / SQL Server
- **Language**: SQL
- **Tools**: [Add your specific tools - e.g., pgAdmin, MySQL Workbench, etc.]

## 📁 Project Structure

```
pizza-runner-analysis/
├── README.md
├── data/
│   ├── schema.sql
│   └── sample_data.sql
├── cleaning/
│   └── data_cleaning.sql
├── analysis/
│   ├── A_pizza_metrics.sql
│   ├── B_runner_customer_experience.sql
│   ├── C_ingredient_optimisation.sql
│   ├── D_pricing_ratings.sql
│   └── E_bonus_challenges.sql
└── insights/
    └── summary_report.md
```

## 🚀 How to Use

1. **Clone the repository**
   ```bash
   git clone [your-repo-url]
   cd pizza-runner-analysis
   ```

2. **Set up the database**
   ```sql
   -- Run schema creation
   source data/schema.sql
   
   -- Load sample data
   source data/sample_data.sql
   ```

3. **Run data cleaning scripts**
   ```sql
   source cleaning/data_cleaning.sql
   ```

4. **Execute analysis queries**
   - Navigate to the `analysis/` folder
   - Run each SQL file corresponding to the question sections
   - Review results and insights

## 📈 Results & Visualizations

*[Add links to any dashboards, charts, or visualization tools you used]*

## 🤝 Contributing

This project is part of a SQL case study challenge. Feel free to:
- Suggest optimizations to existing queries
- Propose additional analysis questions
- Share alternative solution approaches

## 📞 Contact

*[Add your contact information]*

---

**Note**: This case study is inspired by data analysis challenges and demonstrates practical SQL skills for business intelligence and data-driven decision making.

## 🏷️ Tags

`#SQL` `#DataAnalysis` `#BusinessIntelligence` `#CaseStudy` `#DatabaseDesign` `#DataCleaning` `#PostgreSQL`
