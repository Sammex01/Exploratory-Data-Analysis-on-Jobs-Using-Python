# 📊 The Analysis
## 1. What are the most demanded skills for remote data analyst jobs?

### 🎯 Objective
I put together this analysis to find out which technical skills are most requested for remote **Data Analyst** jobs. By looking at percentages rather than raw counts, it's easier to see the real probability of an employer asking for a specific skill.

View my [notebook](skill_in_demand.ipynb) to see the full analysis, including data cleaning and methodology.

### 📈 Visualize Data
```python
# 1. Filter for remote Data Analyst roles and find total job count
df_remote = df[(df['job_title_short'] == 'Data Analyst') & (df['job_work_from_home'] == True)].copy()
total_remote_jobs = len(df_remote)

# 2. Clean stringified lists and explode the skills into individual rows
df_remote['job_skills'] = df_remote['job_skills'].str.replace(r"[\[\]']", "", regex=True).str.split(", ")
df_skills_exploded = df_remote.explode('job_skills')

# 3. Calculate the percentage of appearance for the Top 10 skills
skill_percentages = (df_skills_exploded['job_skills'].value_counts() / total_remote_jobs) * 100
top_10_skills = skill_percentages.head(10)

# 4. Plot the results
top_10_skills.sort_values().plot(kind='barh', figsize=(10, 6), title='Top 10 Remote Data Analyst Skills (%)')
```
### Results
![Image](Images\skills_likelihood.png)


### 💡 Key Insights
* 👑 **The "Gold Standard"**: **SQL** and **Excel** remain the most dominant requirements, appearing in over **30%** of remote postings.
* 🐍 **The Python Factor**: Proficiency in **Python** is a massive differentiator, appearing frequently in high-level remote analyst roles.
* ☁️ **Cloud Connectivity**: There is a noticeable trend in remote roles requiring cloud platforms like **AWS** or **Azure**, suggesting that off-site teams prioritize analysts who can operate in cloud-based data environments.


## How are the top 5 skills in-demand trending over the year?

### 🎯 Objective
Just looking at total skill counts doesn't tell the whole story. I wanted to track the **momentum** of these skills over the year.To get a clear picture, I converted the data into **percentages** instead of raw counts. This smoothed out seasonal hiring spikes and gives a better idea of how likely a skill is to be requested in any given month.
### Visualize Data
```python
# 1. Explode the skills list and pivot the data by month
df_exploded = df_DA.explode('job_skills')
df_pivot = df_exploded.pivot_table(index='job_posted_month', columns='job_skills', aggfunc='size')

# 2. Normalize into percentages based on total monthly job postings
monthly_totals = df_DA.groupby('job_posted_month').size()
df_pivot_pct = df_pivot.div(monthly_totals, axis=0) * 100

# 3. Extract the Top 5 skills and plot the trend
top_5 = df_pivot.sum().sort_values(ascending=False).head(5).index
df_pivot_pct[top_5].plot(kind='line', figsize=(10, 6), marker='o')
```
### Results
![Image](Images\skills_trend.png)


### 💡 Key Insights
By normalizing the data into percentages, we removed seasonal hiring biases (like the December slowdown) to uncover the true momentum of these tools:

* 👑 **The "Evergreen" Core**:Even when normalized for hiring volume, **SQL** and **Excel** maintain a high requirement rate across all 12 months, proving they are fundamental to the role regardless of the season.
* 📈 **The Analytics Push**: The probability of a role requiring **Python** saw a distinct upward trend beginning in November, indicating a growing industry shift towards advanced predictive analytics heading into the new year.
* ☁️ **Fading vs. Emerging Tech**: While legacy visualization tools like **Excel** showed a gradual decline in mention rate over the year, cloud-centric skills like **Power BI** established a solid upward trajectory. This emphasizes the growing necessity of cloud literacy for modern Data Analysts.