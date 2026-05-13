# 📊 The Analysis
## 🎯 The Most Demanded Skills for Remote Data Analyst Jobs

I put together this analysis to find out which technical skills are most requested for remote **Data Analyst** jobs. By looking at percentages rather than raw counts, it's easier to see the real probability of an employer asking for a specific skill.




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
View my [notebook](1_skills_in_demand.ipynb) to see the full analysis, including data cleaning and methodology.
### Visualize Data
![Image](Images\skills_likelihood.png)


### 💡 Key Insights

- **SQL is non-negotiable** — appearing in 54.8% of remote data analyst postings, it's the only skill demanded by more than half of employers, making it nearly twice as common as the next skill (Excel at 34.5%).
- **The core stack is SQL + Excel + Python + Tableau** — these four skills cover the top 28%–55% demand range and represent the minimum viable toolkit for remote analyst roles; everything below Power BI (19.6%) drops off sharply.
- **Cloud and presentation tools are rare differentiators** — Azure, PowerPoint, and Looker are only requested in about 6% of job postings. However, checking this against salary data shows Azure is a bit of a hidden gem. It has lower demand but a high median salary ($105K), making it a smart niche skill to learn after mastering the core stack


## 📈 Top skills trends

Just looking at total skill counts doesn't tell the whole story. I wanted to track the **momentum** of these skills over the year.To get a clear picture, I converted the data into **percentages** instead of raw counts. This smoothed out seasonal hiring spikes and gives a better idea of how likely a skill is to be requested in any given month.




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
View my [notebook](2_skills_trend.ipynb) to see the full analysis, including data cleaning and methodology.
### Visualization: Top Skills Trend
![Image](Images\skills_trend.png)

### 💡 Key Insights

- **SQL dominates data analyst job postings** (44–50%), maintaining a lead over Excel throughout the year making it the single most critical skill to have.
- **Excel and Python are neck-and-neck in mid-tier demand** (both 28–36%), but Python shows resilience with a sharp late-year recovery in December, signaling growing employer preference for programmatic analysis.
- **Tableau and Power BI remain baseline expectations** (20–25%), suggesting visualization tools are treated as complementary skills rather than primary hiring filters but their consistent presence means neither can be ignored.

## 💰 The Financial Value of Skills

### 🎯 Objective
While SQL and Excel are the most requested skills, are they the highest paying skills? The goal of this analysis was to identify which technical skills yield the highest median salaries for Data Analysts, isolating the tools that provide the best return on investment for learning.


```python
# The core logic to extract valid, high-paying skills
skill_stats = df_DA_exploded.groupby('job_skills').agg(
    median_salary=('salary_year_avg', 'median'),
    skill_count=('job_skills', 'count')
).reset_index()

# Statistical filtering to remove low-sample outliers
min_count = len(df_DA_salary) * 0.05 
top_paying_skills = skill_stats[skill_stats['skill_count'] >= min_count]
top_paying_skills = top_paying_skills.sort_values(by='median_salary', ascending=False).head(10)
```
View my [notebook](3_skills_salary.ipynb) to see the full analysis, including data cleaning and methodology.
### 📉 Visualization: Top Paying Skills
This bar chart displays the premium the market places on specific competencies:

![Image](Images\highest_skills.png)
### Key Insights

- **Cloud platforms command the highest salaries** — **Snowflake** ($106,479), **Azure** ($105,000), and **AWS** ($104,000) occupy the top 3 spots, paying ~$10K+ more than traditional BI tools, signaling a clear market premium for cloud-native skills.
- **The $95K cluster reveals a competitive mid-tier** — **Python**, **Power BI**, **SQL Server**, and **Looker** all land at exactly $95,000, suggesting employers bundle these as interchangeable "standard analyst" skills with no differentiation in pay.
- **Even the lowest-ranked skill (Tableau at $92,500) pays six figures nearby** — the entire top 10 sits within a tight $14K band, meaning any one of these skills can anchor a strong remote analyst salary, but cloud tools are the ceiling-breakers.

## 🎓 The Degree Requirement

It's no secret that the tech industry is open to self-taught talent. But does that hold true for remote Data Analysts? I wanted to find out if a formal degree is actually required to land a job, and whether lacking one hurts your earning potential.


```python
# Create readable labels and calculate volume percentages
df_remote['degree_req'] = df_remote['job_no_degree_mention'].map({False: 'Degree Required', True: 'No Degree Required'})
volume_pct = df_remote['degree_req'].value_counts(normalize=True) * 100

# Calculate median salary for each group
df_salary = df_remote.dropna(subset=['salary_year_avg'])
salary_median = df_salary.groupby('degree_req')['salary_year_avg'].median()
```
View my [notebook](4_degree_requirement.ipynb) to see the full analysis, including data cleaning and methodology.

### 📉 Visualization: Opportunity & Pay
![Image](Images\degree_required.png)

### 💡 Key Insights
- **A degree is still the majority expectation** — 59.2% of remote data analyst roles list a degree requirement, but the 40.8% that don't represents a substantial and accessible entry point for non-traditional candidates.
- **No-degree roles actually pay more** — median salary for degree-not-required postings is $90,000 vs. $85,000 for degree-required roles, a $5K gap that suggests employers waiving degree requirements are compensating with higher pay to attract skilled candidates.
- **Portfolio over pedigree is a viable strategy** — the combination of a large no-degree market share and a salary premium for those roles means demonstrable skills (projects, certifications, tools) can outperform a degree both in access and earning potential.

## 🧗 The Seniority Divide

Since working remotely requires a lot of independence, are companies hesitant to hire junior analysts for these roles? I broke down the job postings by experience level to see where the real remote opportunities are—and how much your salary grows as you level up.


```python
# Custom function to parse job titles and engineer a 'seniority' column
def assign_seniority(title):
    title = str(title).lower()
    if any(word in title for word in ['senior', 'sr', 'lead', 'principal']):
        return 'Senior'
    elif any(word in title for word in ['junior', 'jr', 'entry']):
        return 'Junior / Entry'
    else:
        return 'Mid-Level'

df_remote['seniority'] = df_remote['job_title'].apply(assign_seniority)
```
View my [notebook](5_seniority_divide.ipynb) to see the full analysis, including data cleaning and methodology.

### 📉 Visualization: Availability & Compensation
![Image](Images\job_roles.png)

### 💡 Key Insights

- **Mid-level is the sweet spot for remote work** — a massive 83% of postings are aimed at mid-level candidates. That completely dwarfs both junior (~9%) and senior (~8%) roles combined. If you want a remote job, your best bet is to focus on building up that mid-level credibility as quickly as possible.
- **The junior-to-mid jump is a huge turning point** — salaries jump from about $65K for juniors to $85K for mid-level (a solid $20K bump). And the pay bumps just keep growing: stepping up to a senior role pushes the median salary up another $35K to $120K.
- **Senior roles are rare but not out of reach** — they make up about 8% of postings, which is roughly the same as entry-level jobs. This changes the perspective: the biggest hurdle isn't slowly racking up years of experience to become a senior, it's actually breaking into that mid-level tier to begin with.