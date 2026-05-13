# 📊 The Analysis
## 1. What are the most demanded skills for remote data analyst jobs?

### 🎯 Objective
I put together this analysis to find out which technical skills are most requested for remote **Data Analyst** jobs. By looking at percentages rather than raw counts, it's easier to see the real probability of an employer asking for a specific skill.

View my [notebook](skill_in_demand.ipynb) to see the full analysis, including data cleaning and methodology.

### 📈 Visualize Data
```
plt.figure(figsize=(10, 6))
ax = sns.barplot(data=skill_stats, x='percentage', y='skill', hue='skill', palette='magma', legend=False)
plt.title('Top 10 Skills for Remote Data Analysts (%)', fontsize=15)
plt.xlabel('Percentage of Jobs (%)')
plt.ylabel('')

for i, p in enumerate(ax.patches):
    ax.annotate(f'{skill_stats.percentage.iloc[i]:.1f}%', 
                (p.get_width(), p.get_y() + p.get_height() / 2), 
                ha='left', va='center', xytext=(5, 0), textcoords='offset points')

plt.tight_layout()
plt.show()
```


### 💡 Key Insights (Phase 1)
* 👑 **The "Gold Standard"**: SQL and Excel remain the most dominant requirements, appearing in over **[X]%** of remote postings.
* 🐍 **The Python Factor**: Proficiency in Python is a massive differentiator, appearing frequently in high-level remote analyst roles.
* ☁️ **Cloud Connectivity**: There is a noticeable trend in remote roles requiring cloud platforms like **AWS** or **Azure**, suggesting that off-site teams prioritize analysts who can operate in cloud-based data environments.