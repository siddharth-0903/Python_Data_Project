# Overview

Welcome to my analysis of the data job market, focusing on data analyst roles. This project was created out of a desire to navigate and understand the job market more effectively. It delves into the top-paying and in-demand skills to help find optimal job opportunities for data analysts.

The data sourced from [Luke Barousse's Python Course](https://www.lukebarousse.com/python) which provides a foundation for my analysis, containing detailed information on job titles, salaries, locations, and essential skills. Through a series of Python scripts, I explore key questions such as the most demanded skills, salary trends, and the intersection of demand and salary in data analytics.
# The Questions

Below are the questions I want to answer in my project:

1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for data analysts to learn? (High Demand AND High Paying)

# Tools I Used

For my deep dive into the data analyst job market, I harnessed the power of several key tools:

- Python: The backbone of my analysis, allowing me to analyze the data and find critical insights.I also used the following Python libraries:
    - Pandas Library: This was used to analyze the data.
    - Matplotlib Library: I visualized the data.
    - Seaborn Library: Helped me create more advanced visuals.
- Jupyter Notebooks: The tool I used to run my Python scripts which let me easily include my notes and analysis.
- Visual Studio Code: My go-to for executing my Python scripts.
- Git & GitHub: Essential for version control and sharing my Python code and analysis, ensuring collaboration and project tracking.

# Data Preparation and Cleanup
This section outlines the steps taken to prepare the data for analysis, ensuring accuracy and usability.
## Import & Clean Up Data
```python
#importing Libraries
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt

#loading data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

#data cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x)else x)

```
## Filter India Jobs
To focus my analysis on the Indian job market, I apply filters to the dataset, narrowing down to roles based in the India.
```python
df_India = df[df['job_country'] == 'United States']
```


# The Analysis

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the most demanded skills for the top 3 most popular data roles. I filtered out of those positions by which ones were  the most popular, and got the top 5 skills for these top 3 roles. This query highlights the most popular job titles and their top skills, showing which skills I should pay attention to depending on the role I'm targeting.

View my notebook with detailed steps here:   
[2_Skill_Demand.ipynb](3_Project\2_Skills_Demand.ipynb)

### Visualize Data

```python
fig, ax = plt.subplots(len(job_titles))

sns.set_theme(style='ticks')

for i, job_title in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head(5)
    sns.barplot(data=df_plot, x='skill_percent', y='job_skills', ax=ax[i], hue='skill_count', palette='dark:b_r')
    ax[i].set_title(job_title)
    ax[i].set_ylabel('')
    ax[i].set_xlabel('')
    ax[i].legend().set_visible(False)
    ax[i].set_xlim(0, 78)

    for n, v in enumerate(df_plot['skill_percent']):
        ax[i].text(v + 1, n, f'{v:.0f}%', va='center')

    if i != len(job_titles) - 1:
        ax[i].set_xticks([])

fig.suptitle('Likelihood of Skills Requested in India Job Postings', fontsize=15)
fig.tight_layout(h_pad=0.5)
plt.show()
```

### Results

![Visualization of Top Skills for Data Nerds](Images\Skill_demand_all_data.png)

### Insights


- SQL is the most requested skill for Data Analysts and Data Engineer, with it in over half the job postings for both roles. For Data Scientist, Python is the most sought-after skill, appearing in 61% of job postings.

- Data Engineers require more specialized technical skills (AWS, Azure, Spark) compared to Data Analysts and Data Scientists who are expected to be proficient in more general data management and analysis tools (Excel, Tableau).

- Python is a versatile skill, highly demanded across all three roles, but most prominently for Data Scientists (70%) and Data Engineers (61%).


## 2. How are in-demand skills trending for Data Analysts?

### Visualize Data
```python
df_plot = df_DA_India_percent.iloc[:, :5]

plt.title("Trending Top Skills for Data Analysts in India")
plt.ylabel('Likelihood in Job Posting')
plt.xlabel("")
plt.legend().remove()

from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter())


for i in range(5):
    plt.text(11.2, df_plot.iloc[-1, i], df_plot.columns[i])

plt.show()

```

### Results

![Trending Top Skills for Data Analysts in India](3_Project\Images\trending_DA_skills.png)
*Bar graph visualizing the trending top skills for data analysts in India in 2023.*

### Insights

1. SQL remains the foundation—consistently the most demanded skill.

- SQL shows high and stable demand across all months, peaking around May (≈68%) and never falling below 52%.
- This indicates that companies consider SQL non-negotiable for data analyst roles, regardless of industry or season.

2. Python is steadily rising—especially toward year end.

- Python maintains a stable mid-range demand (35–45%), with noticeable growth in June and December.
- This suggests businesses increasingly value analysts who can go beyond spreadsheets and perform automation, advanced analytics, and machine learning prototyping.

3. Excel demand spikes in May—likely reflecting hiring for operational roles.

- Excel shows a big peak in May (~58%), higher than its usual monthly trend.
- This reflects periods where employers seek analysts for reporting-heavy or business operations roles, where Excel remains essential despite newer tool adoption.

4. BI tools (Tableau & Power BI) show growing relevance, especially Power BI.

- Power BI shows a strong rise in the second half, ending the year near 35%.

- Tableau maintains moderate demand with periodic peaks (July, Oct).

- Trend shows that companies are shifting toward dashboarding and business intelligence, with Power BI becoming increasingly preferred, possibly due to Microsoft ecosystem adoption.

#### High-impact takeaway

- To stay competitive as a Data Analyst in India:
SQL + Python + Power BI is emerging as the winning skill combo, with Excel still essential for day-to-day analysis.


## 3. How well do jobs and skills pay for Data Analysts?

To identify the highest-paying roles and skills, I only got jobs in the India and looked at their median salary. But first I looked at the salary distributions of common data jobs like Data Scientist, Data Engineer, and Data Analyst, to get an idea of which jobs are paid the most.

View my notebook with detailed steps here: [4_Salary_Analysis].(3_Project\4_Salary_Analysis.ipynb)

#### Visualize Data
```python
sns.boxplot(data=df_India_top6, x='salary_year_avg', y='job_title_short', order=job_order)
sns.set_theme(style='ticks')

plt.title('Salary Distribution in the India')
plt.xlabel('Yearly Salary ($USD)')
plt.ylabel('')
ax = plt.gca()
ax.xaxis.set_major_formatter(plt.FuncFormatter(lambda x,pos: f'${int(x/1000)}K'))
plt.xlim(0, 600000)
plt.show

```
### Results

![Salary Distribution of Data Jobs in India](3_Project\Images\Salary_analysis.png)
*Box plot visualizing the salary distributions for the top 6 data job titles.*

#### Insights
1. Data Engineering & Machine Learning Engineering offer the highest earning potential.

- Data Engineers show consistently high median salaries, while Machine Learning Engineers have the widest spread, indicating opportunities for very high pay at senior or specialized levels.
- These roles reward strong technical depth (cloud, pipelines, ML systems) and are in high demand as companies scale data infrastructure.

2. Data Scientists earn more than Data Analysts, but the gap reflects skill complexity.

- Data Scientists typically have a higher median range than Data Analysts due to expectations like model building, statistical expertise, and advanced programming.
- Analysts who upskill in Python, statistics, and machine learning can transition into higher-paying DS or ML roles.

3. Data Analyst salaries grow with BI + engineering-aligned skills.

- Data Analyst salaries appear lower compared to engineering and science roles, but analysts with skills like SQL, Python, Power BI/Tableau, and cloud/data warehousing tend to move into higher-paying hybrid roles (Analytics Engineer, BI Engineer).
- The salary premium is strongest for analysts who can work at the intersection of analytics + engineering.

### Highest Paid & Most Demanded Skills for Data Analysts

Next, I narrowed my analysis and focused only on data analyst roles. I looked at the highest-paid skills and the most in-demand skills. I used two bar charts to showcase these.

### Visualize Data
```python

fig, ax = plt.subplots(2, 1)  

# Top 10 Highest Paid Skills for Data Analysts
sns.barplot(data=df_DA_top_pay, x='median', y=df_DA_top_pay.index, hue='median', ax=ax[0], palette='dark:b_r')

# Top 10 Most In-Demand Skills for Data Analystsr')
sns.barplot(data=df_DA_skills_sorted, x='median', y=df_DA_skills_sorted.index, hue='median', ax=ax[1], palette='light:b')

plt.show()

```
### Result
Here's the breakdown of the highest-paid & most in-demand skills for data analysts in the US:

![highest paid skills and most in-demand skills for data analysts in India.](3_Project\Images\highest_paid_vs_in_demand_skills.png)
*Two separate bar graphs visualizing the highest paid skills and most in-demand skills for data analysts in India.*

### Insights
1. High-paying skills lean heavily toward engineering-oriented and database technologies.

- Skills like PostgreSQL, PySpark, GitLab, MongoDB, and Databricks top the salary chart, with median pay reaching close to $160K. These tools are widely used in big data processing, version control, and scalable data infrastructure, indicating that analysts who expand into engineering-heavy skill sets gain access to significantly higher-paying roles.

2. The most in-demand skills focus on business intelligence and practical analytics.

- Skills such as Power BI, Tableau, Excel, SQL, and Python dominate the demand chart, highlighting what companies require most in day-to-day analytics. These tools support reporting, dashboarding, and basic-to-intermediate data manipulation, which form the core responsibilities of most data analyst roles.

3. There is a clear gap between what pays the most and what is most in-demand — creating an opportunity for differentiation.

- Foundational skills are essential for employability, but the highest salaries go to analysts who stack BI and SQL skills with big data, cloud, and engineering skills like PySpark, Databricks, and PostgreSQL.

    -  Data Analysts who bridge analytics and data engineering stand to see the biggest salary growth and career mobility.

    ## 4. What is the most optimal skill to learn for Data Analyst?

    #### Visualize Data
    ```python
    from adjustText import adjust_text
    import matplotlib.pyplot as plt

    plt.scatter(df_DA_skills_high_demand['skill_percent'], df_DA_skills_high_demand['median_salary'])
    plt.show()

    ```
    #### Results

    ![Most Optimal Skills for Data Analysts in India](3_Project\Images\optimal_skills_for_Data_Analyst.png)
    *A scatter plot visualizing the most optimal skills (high paying & high demand) for data analysts in India.*

    ### Insights:

1. Programming Skills Offer Strong Salary Advantages (Blue Points)

    - Programming-related skills—especially SQL and Python—appear toward the higher end of the median salary scale, with SQL standing out for both high demand (≈50%) and high salary.
    This indicates that employers place strong value on technical skills that enable data extraction, automation, and large-scale processing.

    Key takeaway:
    - Strengthening programming foundations significantly boosts salary potential for data analysts.

2. Analyst Tools Provide High Demand & Competitive Pay (Orange Points)

    - Tools like Power BI, Tableau, and Excel show a combination of high demand and above-average salary levels.
    - Power BI, in particular, offers one of the best combinations of high job percentage and high salary, reflecting its increasing use across industries.

    Key takeaway:
    - Visualization and dashboarding tools remain essential and offer great ROI in terms of job availability and compensation.

3. Cloud Skills Show Moderate Demand with Strong Salary Potential (Green Points)

    - Cloud platforms such as Azure and AWS fall in the mid-range of demand but show competitive salaries, indicating that cloud knowledge enhances a data analyst’s value—especially in modern data environments.

    Key takeaway:
    - Cloud familiarity is growing in importance and can differentiate analysts in a competitive market.

4. Libraries & Specialized Tools Show High Salaries but Lower Demand (Red Points)

    - Skills like Looker, Spark, and PowerPoint (advanced reporting) cluster around high salary levels but appear in lower job percentages.
    These are typically specialized or advanced skills relevant to more mature analytics teams.

    Key takeaway:
    - Specialized tools may not be widely required, but they offer higher pay where they are used.

    # What I Learned
    Throughout this project, I deepened my understanding of the data analyst job market and enhanced my technical skills in Python, especially in data manipulation and visualization. Here are a few specific things I learned:

    - Advanced Python Usage: Utilizing libraries such as Pandas for data manipulation, Seaborn and Matplotlib for data visualization, and other libraries helped me perform complex data analysis tasks more efficiently.
    - Data Cleaning Importance: I learned that thorough data cleaning and preparation are crucial before any analysis can be conducted, ensuring the accuracy of insights derived from the data.
    - Strategic Skill Analysis: The project emphasized the importance of aligning one's skills with market demand. Understanding the relationship between skill demand, salary, and job availability allows for more strategic career planning in the tech industry.

# Overall Insights

- Core Skill Value: SQL and Python consistently show the strongest combination of high demand and competitive salaries, making them the foundational skills for any data analyst.

- Market Demand Trends: BI tools like Power BI and Tableau remain highly requested, with Power BI showing growing demand—highlighting the need for strong visualization and reporting skills.

- Salary Drivers: The highest salaries are linked to engineering-oriented and cloud skills such as PySpark, Databricks, and AWS, showing that analysts who expand into data engineering gain significant earning potential.

- Optimal Skill Strategy: Skills that are both widely demanded and well-paid—such as SQL, Python, and Power BI—offer the best return on learning investment, while specialized big-data tools provide strong salary boosts for advanced roles.

# Challenges I Faced

This project was not without its challenges, but it provided good learning opportunities:

- Data Inconsistencies: Handling missing or inconsistent data entries requires careful consideration and thorough data-cleaning techniques to ensure the integrity of the analysis.
- Complex Data Visualization: Designing effective visual representations of complex datasets was challenging but critical for conveying insights clearly and compellingly.
- Balancing Breadth and Depth: Deciding how deeply to dive into each analysis while maintaining a broad overview of the data landscape required constant balancing to ensure comprehensive coverage without getting lost in details.

# Conclusion
This analysis reveals that the data analytics job market strongly favors professionals who combine foundational skills like SQL and Python with modern BI tools such as Power BI and Tableau. While these skills ensure high employability, advancing into cloud and big-data technologies opens doors to significantly higher-paying roles. Trends throughout the year show a rising demand for analysts who can automate workflows, build scalable solutions, and support data-driven decision-making. The salary insights clearly highlight that bridging analytics with engineering skills provides the greatest career acceleration. Ultimately, the most successful data analysts are those who continuously update their skill set to match the evolving industry landscape. By understanding which skills are most valuable and how they impact compensation, analysts can make smarter, more strategic decisions about their learning path and long-term career growth.
