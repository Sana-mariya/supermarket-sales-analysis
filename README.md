# supermarket-sales-analysis
import pandas as pd
import matplotlib.pyplot as plt

# ==========================================
# SUPERMARKET SALES DATA ANALYSIS
# Author: Sana Mariya
# ==========================================

# 1. LOAD DATA
df = pd.read_csv("supermarket_sales.csv")

print("Dataset Shape:", df.shape)
print("\nFirst 5 Rows:")
print(df.head())


# 2. DATA CLEANING
df.columns = (
    df.columns
    .str.strip()
    .str.lower()
    .str.replace(" ", "_")
)

# Convert date column
if "date" in df.columns:
    df["date"] = pd.to_datetime(df["date"], errors="coerce")
    df["month"] = df["date"].dt.month_name()
    df["month_number"] = df["date"].dt.month

# Remove duplicate records
duplicates = df.duplicated().sum()
print("\nDuplicate Records:", duplicates)

df = df.drop_duplicates()

# Check missing values
print("\nMissing Values:")
print(df.isnull().sum())


# 3. KEY PERFORMANCE INDICATORS
total_sales = df["sales"].sum()
total_quantity = df["quantity"].sum()
total_cogs = df["cogs"].sum()
total_gross_income = df["gross_income"].sum()
average_rating = df["rating"].mean()

profit_margin = (total_gross_income / total_sales) * 100

print("\n========== KEY PERFORMANCE INDICATORS ==========")
print(f"Total Sales       : {total_sales:,.2f}")
print(f"Total Quantity    : {total_quantity:,.0f}")
print(f"Total COGS        : {total_cogs:,.2f}")
print(f"Gross Income      : {total_gross_income:,.2f}")
print(f"Average Rating    : {average_rating:.2f}")
print(f"Profit Margin     : {profit_margin:.2f}%")


# 4. MONTHLY SALES ANALYSIS
monthly_sales = (
    df.groupby(["month_number", "month"])["sales"]
    .sum()
    .sort_index()
)

print("\n========== MONTHLY SALES ==========")
print(monthly_sales)


# 5. BRANCH PERFORMANCE
branch_sales = (
    df.groupby(["branch", "city"])
    .agg(
        total_sales=("sales", "sum"),
        total_quantity=("quantity", "sum"),
        gross_income=("gross_income", "sum"),
        average_rating=("rating", "mean")
    )
    .sort_values("total_sales", ascending=False)
)

print("\n========== BRANCH PERFORMANCE ==========")
print(branch_sales)


# 6. PRODUCT LINE PERFORMANCE
product_sales = (
    df.groupby("product_line")
    .agg(
        total_sales=("sales", "sum"),
        total_quantity=("quantity", "sum"),
        gross_income=("gross_income", "sum"),
        average_rating=("rating", "mean")
    )
    .sort_values("total_sales", ascending=False)
)

print("\n========== PRODUCT LINE PERFORMANCE ==========")
print(product_sales)


# 7. PAYMENT METHOD ANALYSIS
payment_sales = (
    df.groupby("payment")
    .agg(
        total_sales=("sales", "sum"),
        transactions=("invoice_id", "count")
    )
    .sort_values("total_sales", ascending=False)
)

print("\n========== PAYMENT METHOD ANALYSIS ==========")
print(payment_sales)


# 8. TOP 10 TRANSACTIONS
top_transactions = (
    df.sort_values("sales", ascending=False)
    [["invoice_id", "branch", "city", "product_line",
      "sales", "quantity", "payment"]]
    .head(10)
)

print("\n========== TOP 10 TRANSACTIONS ==========")
print(top_transactions)


# 9. SAVE ANALYSIS RESULTS
monthly_sales.to_csv("monthly_sales_analysis.csv")
branch_sales.to_csv("branch_performance.csv")
product_sales.to_csv("product_line_performance.csv")
payment_sales.to_csv("payment_method_analysis.csv")
top_transactions.to_csv("top_10_transactions.csv")


# 10. VISUALIZATION - MONTHLY SALES
plt.figure(figsize=(8, 5))

plt.plot(
    monthly_sales.index.get_level_values("month"),
    monthly_sales.values,
    marker="o"
)

plt.title("Monthly Sales Performance")
plt.xlabel("Month")
plt.ylabel("Sales")
plt.tight_layout()
plt.show()


# 11. VISUALIZATION - BRANCH SALES
plt.figure(figsize=(8, 5))

plt.bar(
    branch_sales.index.get_level_values("branch"),
    branch_sales["total_sales"]
)

plt.title("Sales by Branch")
plt.xlabel("Branch")
plt.ylabel("Sales")
plt.tight_layout()
plt.show()


# 12. VISUALIZATION - PRODUCT LINE SALES
plt.figure(figsize=(10, 6))

plt.barh(
    product_sales.index,
    product_sales["total_sales"]
)

plt.title("Sales by Product Line")
plt.xlabel("Sales")
plt.ylabel("Product Line")
plt.gca().invert_yaxis()

plt.tight_layout()
plt.show()


# 13. VISUALIZATION - PAYMENT METHOD
plt.figure(figsize=(8, 5))

plt.bar(
    payment_sales.index,
    payment_sales["total_sales"]
)

plt.title("Sales by Payment Method")
plt.xlabel("Payment Method")
plt.ylabel("Sales")
plt.tight_layout()
plt.show()


print("\n==========================================")
print("SUPERMARKET SALES ANALYSIS COMPLETED")
print("==========================================")+
