import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
from io import StringIO

st.set_page_config(page_title="Anand Home Energy AI", page_icon="⚡", layout="centered")

st.title("⚡ Anand Shreekar Home Energy AI")
st.markdown("**LADWP Real Billing Dashboard**")

data = """Billing_Periods,Number_of_Days,Tier_1,Tier_2,Tier_3,Total_kWh,Average_Daily_Use
03/14/2025-05/09/2025,56,649,0,0,649,11.59
05/09/2025-07/12/2025,64,1000,724,0,1724,26.94
07/12/2025-09/12/2025,62,1000,694,0,1694,27.32
09/12/2025-11/13/2025,62,1000,594,0,1594,25.71
11/13/2025-01/15/2026,63,1000,37,0,1037,16.46
01/15/2026-03/17/2026,61,1000,471,0,1471,24.11"""

df = pd.read_csv(StringIO(data))

st.subheader("Usage History")
st.dataframe(df, use_container_width=True)

st.subheader("Tier Distribution")
fig, ax = plt.subplots()
ax.pie([df['Tier_1'].sum(), df['Tier_2'].sum(), df['Tier_3'].sum()],
       labels=['Tier 1 (Cheap)', 'Tier 2', 'Tier 3 (Expensive)'], autopct='%1.1f%%')
st.pyplot(fig)

total_cost = (df['Tier_1']*0.15 + df['Tier_2']*0.25 + df['Tier_3']*0.35).sum()
st.success(f"Estimated Annual Cost: **${total_cost:,.2f}**")
st.caption("Anand Shreekar Home Energy AI Tool")