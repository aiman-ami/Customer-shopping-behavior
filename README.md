# Customer Shopping Behavior Analysis

An end-to-end data analysis project looking at the shopping patterns of 3,900 customers across four product categories. The goal was to understand what actually drives purchases — age, subscription status, discounts, product type — and turn that into something readable.

---

## Tools

- **Python (pandas)** — cleaning and feature engineering
- **MySQL** — analysis via SQL queries
- **Power BI** — interactive dashboard

---

## Dataset

3,900 customer records with 18 columns covering demographics, purchase history, shipping preferences, review ratings, and payment methods.

---

## Data Cleaning (Python)

The raw dataset needed work before it was usable:

- Standardized all column names to snake_case
- Found and handled 37 null values in `review_rating`
- Discovered that `discount_applied` and `promo_code_used` were identical across every row — dropped `promo_code_used` as redundant
- Mapped `frequency_of_purchases` (text like "weekly", "monthly") to numeric values for easier analysis
- Created a new `age_group` column — **Young Adults / Adults / Middle-aged / Seniors** — to enable age-based segmentation
- Loaded the cleaned dataset into MySQL using SQLAlchemy

---

## SQL Analysis

13 business questions answered using SQL, ranging from basic aggregations to window functions and CTEs. Questions 11–13 were independently formulated based on patterns noticed during exploration.

**Q1 — Revenue by gender**
Male customers generated $157,890 vs $75,191 for female customers. The dataset skews heavily male which likely explains most of this gap.

**Q2 — High spenders who still used discounts**
Identified customers who applied a discount but still spent above the average purchase amount ($59.76). Used a subquery to calculate the average dynamically.

**Q3 — Top 5 products by review rating**
Gloves (3.86), Sandals (3.84), Boots (3.82), Hat (3.80), Skirt (3.78). Ratings are fairly tight across the top 5.

**Q4 — Shipping type vs average spend**
Express shipping customers spend slightly more on average ($60.48) compared to Standard ($58.46) — a small but consistent difference.

**Q5 — Do subscribers spend more?**
Surprisingly, no. Non-subscribers actually average slightly higher spend ($59.87) vs subscribers ($59.49). Non-subscribers also make up 73% of the customer base (2,847 vs 1,053).

**Q6 — Products with highest discount usage**
Hat (50%), Sneakers (49.66%), Coat (49.07%), Sweater (48.17%), Pants (47.37%). Used a CASE statement inside SUM to calculate discount rates as percentages.

**Q7 — Customer segmentation**
Segmented all customers into new (1 previous purchase), returning (2–10), and loyal (11+) using a CTE. Results: 3,116 loyal, 701 returning, 83 new customers.

**Q8 — Top 3 products per category**
Used `ROW_NUMBER() OVER(PARTITION BY category)` to rank products within each category. Top sellers: Jewelry (Accessories), Blouse (Clothing), Sandals (Footwear), Jacket (Outerwear).

**Q9 — Do repeat buyers subscribe?**
Among customers with more than 5 previous purchases: 2,518 are not subscribed vs 958 who are. Repeat buying doesn't seem to predict subscription behavior.

**Q10 — Revenue by age group**
Young Adults lead at $62,143, followed by Middle-aged ($59,197), Adults ($55,978), and Seniors ($55,763). Revenue is fairly evenly spread across age groups.

---

## My Own Analysis

Three additional questions I identified independently after exploring the data.

**Q11 — Payment method preference by customer segment**
Used a CTE to segment customers first, then crossed with payment method. Loyal customers show fairly even spread across all payment methods — Credit Card (541), Cash (535), PayPal (532), Venmo (516), Debit Card (509), Bank Transfer (483). New customers are too few (83 total) to draw conclusions. Returning customers slightly prefer PayPal (131) and Cash (121) over other methods.

**Q12 — Which season drives the most revenue and which category leads each season?**
Used `RANK() OVER(PARTITION BY season)` to find the top category per season. Clothing ranks #1 in every season without exception — Spring ($27,692), Winter ($27,274), Fall ($26,220), Summer ($23,078). Accessories consistently ranks #2. This suggests seasonal promotions should still lead with Clothing regardless of time of year.

**Q13 — Does applying a discount affect review ratings?**
Discounted purchases average a rating of 3.74 vs 3.76 for non-discounted — essentially no difference. This suggests customers who use discounts are not more or less satisfied than those who pay full price, which challenges the assumption that discounts attract lower-quality or less engaged buyers.

---

## Power BI Dashboard

Built an interactive single-page dashboard with slicers for subscription status, gender, category, and shipping type.

**KPI cards:** 3.9K customers · $59.76 avg purchase · 3.75 avg rating

**Charts included:**
- Subscription breakdown (donut) — 73% non-subscribed
- Revenue by category — Clothing leads across all seasons
- Sales volume by category
- Revenue and sales count by age group

---

## Key Takeaways

- The majority of customers (73%) are not subscribed, yet they spend almost the same as subscribers — suggesting the subscription program may not be adding perceived value
- Clothing dominates revenue in every single season, making it the most reliable category regardless of time of year
- Discounts do not meaningfully affect review ratings (3.74 vs 3.76) — customers are equally satisfied whether they paid full price or not
- 80% of customers are classified as loyal (11+ purchases), and their payment method preferences are evenly distributed — no single method dominates
- Young Adults are the highest revenue group but the gap between age groups is smaller than expected, suggesting relatively uniform spending behavior across ages
