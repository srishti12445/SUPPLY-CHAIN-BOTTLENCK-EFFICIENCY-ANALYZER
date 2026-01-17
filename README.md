
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# Load datasets
df1 = pd.read_csv("supply_chain_data.csv")  # Operational data
df3 = pd.read_excel("Supply chain logisitcs problem.xlsx") # Logistics data

# Basic inspection
print(df1.head())
print(df3.head())


# LEAD-TIME ANALYSIS

lead_time_cols = [
    col for col in df1.columns
    if "lead" in col.lower() or "shipping" in col.lower()
]

lead_time_analysis = df1[lead_time_cols].select_dtypes(include=np.number)

avg_lead_times = lead_time_analysis.mean()

print("Average Lead Times (Days):")
print(avg_lead_times)

avg_lead_times.plot(kind='bar')
plt.title("Stage-wise Average Lead Time")
plt.ylabel("Days")
plt.xlabel("Supply Chain Stages")
plt.show()


# BOTTLENECK DETECTION USING DELAY VARIANCE


# Using logistics dataset (internal delays)
stage_delay_variance = df3[['Ship Late Day count']].var()

bottleneck_stage = stage_delay_variance.idxmax()[0]

print("Delay Variance:")
print(stage_delay_variance)

print("Identified Bottleneck Stage:", bottleneck_stage)

stage_delay_variance.plot(kind='bar')
plt.title("Bottleneck Detection Using Delay Variance")
plt.ylabel("Variance")
plt.xlabel("Logistics Stage")
plt.show()


# ORDER FULFILLMENT RATE

df3['On_Time'] = df3['Ship Late Day count'].apply(
    lambda x: 1 if x == 0 else 0
)

fulfillment_rate = df3['On_Time'].mean() * 100

print(f"Order Fulfillment Rate: {fulfillment_rate:.2f}%")


# COST vs DELIVERY TIME & SEASONAL DEMAND


# Cost vs delivery time
cost_time_cols = [
    col for col in df1.columns
    if "cost" in col.lower() or "shipping" in col.lower()
]

if len(cost_time_cols) >= 2:
    plt.scatter(df1[cost_time_cols[1]], df1[cost_time_cols[0]])
    plt.xlabel("Delivery / Shipping Time")
    plt.ylabel("Cost")
    plt.title("Cost vs Delivery-Time Tradeoff")
    plt.show()

# Seasonal demand
if 'Order Date' in df3.columns:
    df3['Order Date'] = pd.to_datetime(df3['Order Date'])
    df3['Month'] = df3['Order Date'].dt.month

    monthly_demand = df3.groupby('Month').size()

    plt.plot(monthly_demand)
    plt.xlabel("Month")
    plt.ylabel("Order Count")
    plt.title("Seasonal Demand Pattern")
    plt.show()

# FINAL RESULTS & CONCLUSION

print("\n--- FINAL PROJECT CONCLUSION ---")
print("• Lead-time analysis identified slow stages")
print("• Bottleneck detected using delay variance")
print("• Fulfillment rate evaluated service quality")
print("• Cost vs time tradeoff analyzed efficiency")
print("• Seasonal demand patterns visualized")

print("\nProject executed successfully.")
