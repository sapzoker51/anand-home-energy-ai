import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
from io import StringIO

# ================== PAGE CONFIG ==================
st.set_page_config(
    page_title="Anand Shreekar Home Energy AI",
    page_icon="⚡",
    layout="centered"
)

st.title("⚡ Anand Shreekar Home Energy AI")
st.markdown("**Real LADWP Billing Dashboard** — Los Angeles, California")

# ================== YOUR REAL DATA ==================
data = """Billing_Periods,Number_of_Days,Tier_1,Tier_2,Tier_3,Total_kWh,Average_Daily_Use
03/14/2025 - 05/09/2025,56,649,0,649,649,11.59
05/09/2025 - 07/12/2025,64,1000,724,0,1724,26.94
07/12/2025 - 09/12/2025,62,1000,694,0,1694,27.32
09/12/2025 - 11/13/2025,62,1000,594,0,1594,25.71
11/13/2025 - 01/15/2026,63,1000,37,0,1037,16.46
01/15/2026 - 03/17/2026,61,1000,471,0,1471,24.11"""

df = pd.read_csv(StringIO(data))

# ================== TABLE ==================
st.subheader("📋 Detailed Usage History")
st.dataframe(df, use_container_width=True)

# ================== CHARTS ==================
col1, col2 = st.columns(2)

with col1:
    st.subheader("Tier Consumption Distribution")
    fig1, ax1 = plt.subplots(figsize=(6, 6))
    tier_totals = [df['Tier_1'].sum(), df['Tier_2'].sum(), df['Tier_3'].sum()]
    labels = ['Tier 1 (Cheapest)', 'Tier 2', 'Tier 3 (Most Expensive)']
    colors = ['#22c55e', '#eab308', '#ef4444']
    ax1.pie(tier_totals, labels=labels, autopct='%1.1f%%', colors=colors, explode=(0.1, 0, 0))
    st.pyplot(fig1)

with col2:
    st.subheader("Seasonal Usage Trend")
    fig2, ax2 = plt.subplots(figsize=(6, 6))
    df['Period'] = range(1, len(df)+1)
    ax2.plot(df['Period'], df['Average_Daily_Use'], marker='o', linewidth=4, color='#8b5cf6')
    ax2.set_xlabel('Billing Period')
    ax2.set_ylabel('Avg Daily Use (kWh)')
    st.pyplot(fig2)

# ================== SUMMARY & BILL ==================
st.subheader("🔬 Executive Summary")
col3, col4 = st.columns(2)

with col3:
    st.metric("Annual Avg Daily Use", f"{df['Average_Daily_Use'].mean():.2f} kWh/day")
    st.metric("Peak Summer Daily Use", f"{df['Average_Daily_Use'].max():.2f} kWh/day")

with col4:
    ratio = df['Average_Daily_Use'].iloc[1:4].mean() / df['Average_Daily_Use'].iloc[0]
    st.metric("Summer vs Spring Ratio", f"{ratio:.2f}x")
    st.metric("Tier 2 Exposure", f"{df['Tier_2'].sum() / df['Total_kWh'].sum() * 100:.1f}%")

# Bill Calculation
total_cost = (df['Tier_1']*0.15 + df['Tier_2']*0.25 + df['Tier_3']*0.35).sum()

st.subheader("💰 Estimated Annual Cost")
st.success(f"**${total_cost:,.2f}**")

st.info(f"💡 Potential Savings (5% reduction): **${total_cost*0.05:,.2f}**")
st.info(f"💡 Potential Savings (10% reduction): **${total_cost*0.10:,.2f}**")

st.subheader("🚀 Strategic Recommendations")
st.markdown("""
- **Primary focus**: Summer AC optimization (May–Sep) — highest ROI
- Shift EV charging, laundry, dishwasher to off-peak hours
- Target: Keep summer average below 24 kWh/day  
- Next step: Get daily/hourly data for advanced forecasting
""")

st.caption("Built by Anand Shreekar | Home Energy AI Tool v2")