import streamlit as st
import pickle
import json
import numpy as np
import pandas as pd

model = pickle.load(open("model.pkl", "rb"))

with open("columns.json", "r") as f:
    columns = json.load(f)

st.title("HR Attrition Prediction")

age = st.number_input("Age", 18, 60)
income = st.number_input("Monthly Income")
years = st.number_input("Years at Company")
job_sat = st.slider("Job Satisfaction", 1, 4)
wlb = st.slider("Work Life Balance", 1, 4)
overtime = st.selectbox("OverTime", ["Yes", "No"])

overtime = 1 if overtime == "Yes" else 0

if st.button("Predict"):
    input_dict = dict.fromkeys(columns, 0)

    input_dict['Age'] = age
    input_dict['MonthlyIncome'] = income
    input_dict['YearsAtCompany'] = years
    input_dict['JobSatisfaction'] = job_sat
    input_dict['WorkLifeBalance'] = wlb

    if 'OverTime_Yes' in input_dict:
        input_dict['OverTime_Yes'] = overtime

    input_df = pd.DataFrame([input_dict])

    pred = model.predict(input_df)

    if pred[0] == 1:
        st.error("Employee likely to leave")
    else:
        st.success("Employee likely to stay")
