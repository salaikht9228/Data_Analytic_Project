# Measles Data Analysis
## Project Overview
This project uses the data that contains measles (MMR) vaccination rates for 46,412 schools in 32 US states. The project's initial aim is to explore what types of schools (Public, Private or kindergarten) have the highest MMR vaccination rates. 
![MMR by schools](https://github.com/salaikht9228/Data_Analytic_Projecct/assets/120165361/549493b6-95b5-424e-bc8e-52072ef138a2)

### Date Source
Measles Data: [click the link](https://github.com/WSJ/measles-data)
### Libraries from Python
- Panda - data cleaning and data synthesis
- NumPy - data analysis
- Matplotlib and seaborn - data visualisation
- scipy - Kruskal Wallis test
### Data Cleaning
The data frame only includes schools with a percentage of MMR and overall vaccination rates from 0% to 100%; percentages of -1% and below are removed from the data analysis. 
### Exploratory Data Analysis
Firstly, the distributions of MMR rate in each type of school are visualised using displot from the Seaborn library. The distributions are left-skewed for all three types of schools (Figure b). Hence, the Kruskal Wallis test is performed to test whether the MMR vaccination rates among the three types of schools are significantly different.
### Data analysis
Include important codes for data analysis
```python
# create a df with mmr and overall more than -1
df_pos = df.query('mmr > -1 and overall >-1')

# create hist plots for the mmr, separated by the type of school in df_pos
g = sns.displot(data=df_pos, x='mmr', col='type', bins=10)
# Change the subplot titles
g.set_titles("{col_name}")
plt.show()

from scipy.stats import kruskal
# Filter the data for each type of school
public = df_pos[df_pos['type'] == 'Public']['mmr']
private = df_pos[df_pos['type'] == 'Private']['mmr']
kindergarten = df_pos[df_pos['type'] == 'Kindergarten']['mmr']
# Perform the Kruskal-Wallis test
stat, p = kruskal(public, private, kindergarten)
print('Kruskal-Wallis H-test test:\nStatistics=%.3f, p=%.3f' % (stat, p))
# Set the significance level
alpha = 0.05
if p > alpha:
    print('Same distributions (fail to reject H0)')
else:
    print('Different distributions (reject H0)')

# Calculate the median and IQR for each type of school
medians = df_pos.groupby('type')['mmr'].median().reindex(['Public', 'Private', 'Kindergarten'])
iqr = df_pos.groupby('type')['mmr'].apply(lambda x: np.percentile(x, 75) - np.percentile(x, 25)).reindex(['Public', 'Private', 'Kindergarten'])
fig, ax = plt.subplots()
# Plotting with ordered categories
ax.bar(medians.index, medians, yerr=iqr, color='lightblue', width=0.4, capsize=10)
ax.spines[['right', 'top']].set_visible(False)
ax.set_ylabel('MMR vaccination rate')
plt.show()

# divide the mmr rate into four quadrants to see the counts on the types of school for each quadrant
df_pos['mmr_quad']=''
for lab, row in df_pos.iterrows():
    if row['mmr'] <= 25.000000:
        df_pos.loc[lab, 'mmr_quad'] = 'below 25%'
    elif 25.000001 <= row['mmr'] <= 50.000000:
        df_pos.loc[lab, 'mmr_quad'] = 'between 25% and 50%'
    elif 50.000001 <= row['mmr'] <= 75.000000:
        df_pos.loc[lab, 'mmr_quad'] = 'between 50% and 75%'
    else:
        df_pos.loc[lab, 'mmr_quad'] = 'above 75%'

# Creating a dictionary to hold the data for each quadrant
mmr_quadrants = {
    'above 75%': df_pos.query('mmr_quad == "above 75%"')['type'].value_counts(sort=False),
    'between 50% and 75%': df_pos.query('mmr_quad == "between 50% and 75%"')['type'].value_counts(sort=False),
    'between 25% and 50%': df_pos.query('mmr_quad == "between 25% and 50%"')['type'].value_counts(sort=False),
    'below 25%': df_pos.query('mmr_quad == "below 25%"')['type'].value_counts(sort=False)
}

# Plot bar graphs to visualize the highest number of particular school type in each quadrant of the MMR rate
fig, axes = plt.subplots(nrows=1, ncols=4, figsize=(24, 6))  
for ax, (quad, data) in zip(axes, mmr_quadrants.items()):
    sns.barplot(x=data.values, y=data.index, palette='BuGn', ax=ax)
    ax.set_title(quad)
    ax.set_xlabel('No. of each type of schools')
    ax.set_ylabel('')
    ax.spines[['right', 'top']].set_visible(False)
axes[0].set_ylabel('Type of School')
fig.suptitle('MMR vaccination rate',fontsize=20)
plt.tight_layout() 
plt.show()

#Convert mmr_quandrants into a dataframe
df_mmrq = pd.DataFrame(mmr_quadrants)
# Calculate the percentage of each type of school within each quadrant
df_mmrq_percent = df_mmrq.apply(lambda x: (x / x.sum())*100, axis=1)

# Plot df_mmrq_percent into bar graphs
fig, axes = plt.subplots(nrows=1, ncols=4, figsize=(24, 6))  
# Iterate over the columns of df_mmrq_percent and the axes simultaneously
for ax, col in zip(axes, df_mmrq_percent.columns):
    sns.barplot(x=df_mmrq_percent[col], y=df_mmrq_percent.index, palette='magma', ax=ax)
    ax.set_title(col)
    ax.set_xlabel('% of each type of schools')
    ax.set_ylabel('')
    ax.spines[['right', 'top']].set_visible(False)
axes[0].set_ylabel('Type of School')
fig.suptitle('MMR vaccination rate',fontsize=20)
plt.tight_layout() 
plt.show()
```
### Results
Since the number of Public schools is about 7x more than both Private and Kindergarten schools (Figure c) and most of the Public schools have high percentages of MMR vaccination rates, the Kruskal Wallis test suggests that the MMR rates are significantly different among the three types of schools (_p_<0.001)(Figure a, error bar represents the median of the vaccination rate and interquartile range). However, when we look at the percentage of each type of school within the above 75% quadrant of the MMR vaccination rate (Figure d), we will notice that 95% of all three types of schools have an above 75% rate of MMR vaccination rate, highlighting that types of school may not influence that MMR vaccination rate. 
### Recommendations
Hence, for the next part of this project, I will look at the 32 US states to examine whether the MMR vaccination rate will be different among the 32 US states.
