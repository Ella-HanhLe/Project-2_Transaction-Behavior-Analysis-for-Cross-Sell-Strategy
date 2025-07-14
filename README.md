### PROJECT 2: Transaction Behavior Analysis for Cross-Sell Strategy (SQL-Based)

This project explores customer transaction data to uncover key behavioral patterns for cross-sell opportunities.

This project uses simulated data for learning and demonstration purposes only. No real customer information is involved.*

---

## 1. Background

Understanding customer transaction behavior is essential for designing effective cross-sell strategies in the banking sector.

This project focuses on:
- Identifying high-engagement customer segments.
- Uncovering spending patterns by region and age group.
- Proposing targeted credit card promotions based on customer behavior.

---

## 2. Dataset Description

The project utilizes 4 tables:

| Table Name  | Description |
|-------------|-------------|
| `customers` | Demographics (customer_ID, name, age, gender, join date, region) |
| `accounts` | Bank account details (account_id, customer_id, account_type, open_date) |
| `transactions` | Transaction logs (transaction_id, account_id, transaction_date, type, amount) |
| `products` | Available banking products and their target segments (customer_id, product_id, recommend_reason)|

---

## 3. Objectives

Key business questions addressed:

- Which customer segment uses Fund Transfer the most?
- What’s the average monthly spending by region and age group?
- Which customers are eligible for cross-sell (credit cards) based on their behavior?

---

## 4. Tools & SQL Functions Used

- SQL
- Key SQL functions: `CAST`, `FLOOR`, `COUNT`, `JOIN`, `GROUP BY`, `ORDER BY`, `EXTRACT`, `AVG`

---
## 5. What I Did

- Explored raw transactional data across 4 tables using SQL (no BI tools involved).

- Designed segmentation logic to categorize users by age group, region, and engagement level.

- Used aggregation queries to reveal patterns in fund transfer usage and monthly spending.

- Created temporary tables (WITH clauses) to compare user behavior with overall averages.

- Identified high-value users for credit card cross-sell based on frequency and amount of spending.

- Summarized insights into actionable recommendations for marketing and product teams.

- Documented SQL logic and analysis process clearly for reproducibility.

---

## 6. Key SQL Queries

# Insight 1: Fund Transfer Usage by Segment

SELECT
   CAST(FLOOR(a.age / 10) * 10 AS INTEGER) AS age_group,
   a.gender,
   COUNT(c.transaction_id) AS Fund_Transfer_count
FROM customers a
JOIN accounts b ON a.customer_id = b.customer_id
JOIN transactions c ON b.account_id = c.account_id
WHERE c.type = 'Fund Transfer'
GROUP BY age_group, a.gender
ORDER BY Fund_Transfer_count DESC;

---

# Insight 2: Monthly Spending by Region and Age Group

SELECT
    a.region,
    FLOOR(a.age / 10) * 10 AS age_group,
    EXTRACT(MONTH FROM c.transaction_date) AS month,
    AVG(c.amount) AS average_amount
FROM customers a
JOIN accounts b ON a.customer_id = b.customer_id
JOIN transactions c ON b.account_id = c.account_id
WHERE c.type IN ('Bill Payment', 'Fund Transfer')
GROUP BY a.region, age_group, month;

---

# Insight 3: Identify Credit Card Cross-Sell Opportunities

WITH txn_summary AS (
    SELECT
        a.customer_id,
        a.name,
        a.age,
        COUNT(c.transaction_id) AS txn_count,
        AVG(c.amount) AS avg_amount
    FROM customers a
    JOIN accounts b ON a.customer_id = b.customer_id
    JOIN transactions c ON b.account_id = c.account_id
    GROUP BY a.customer_id, a.name, a.age
),
avg_metrics AS (
    SELECT
        AVG(txn_count) AS avg_txn,
        AVG(avg_amount) AS avg_amt
    FROM txn_summary
)
SELECT s.*
FROM txn_summary s
JOIN avg_metrics m
  ON s.txn_count > m.avg_txn AND s.avg_amount > m.avg_amt
WHERE s.age BETWEEN 25 AND 44;

---

## 7.Key insights

1. Customer segments that use Fund Transfer the most:
    - Males in their 30s and 40s are the top user groups, with Fund Transfer counts of 7 and 6 respectively
    - Young females in their 20s also know strong usage (5 transactions), indicating engagement across age groups.
    
  These insights suggest targeting male customers aged 30–49 for campaigns related to fund transfer products.

2. The average monthly spending by region and age group:
    - Customers aged 40s in VIC spend the most on average (~680 AUD/month)
    - Young users (20s) in QLD also show high spending (~537AUD), indicating strong engagement.
    - Spending is lower in WA and SA, suggesting potential for targeted campaigns.

3. Promote credit cards to eligible customers based on their behavior.
    - After analyzing the metrics, Bob and Isla were identified as two customers who demonstrated high engagement through frequent transactions (≥9) and substantial average spending (over $500 per transaction).
    - In addition, both fall within the 25–44 age range, which aligns well with our credit card target segment.

## 8. Recommendations

1. Customer segments

    - Males 30–49
        
        Core user segment with the highest Fund Transfer usage.
        
        Prioritize campaigns that highlight speed and convenience.
        
    - Females 20–29
        
        Emerging segment with notable engagement.
        
        Test personalized offers or incentives to deepen usage.
        
2. Regional Segment Strategie
    - VIC (40s): Introduce premium credit cards or bundled products (e.g. rewards + savings) for high-value customers.
    - QLD (20s): Run digital lifestyle campaigns with cashback or in-app incentives to drive engagement.
    - WA & SA: Launch awareness and onboarding campaigns, targeting entry-level products to activate lower-spending users.

3. Additional Promotion Opportunity
    - Target Bob and Isla with personalized credit card offers, highlighting reward programs or usage-based perks.
    - Use their profiles to build a lookalike segment for broader campaigns aimed at high-engagement users in the same age bracket.