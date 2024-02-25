# LA Crimes Data Analysis
## Project Overview
This project analyses modified open LA crime data, including the crime's date and time and the patrol divisions where the crime occurred. 

This mini-project aims to determine which month and time of day has the highest number of crimes and which areas in LA have the highest crime rates during those periods.

### Data Source
Crime Data: The primary dataset used for this analysis is the 'crimes.csv' file. I can email the file if you want to explore the csv file.

### Libraries from Python
- Panda - data cleaning and data synthesis
- Matplotlib and seaborn - data visualisation

### Data Cleaning
 The 'DATE OCC' and 'DATE Rptd' columns are formatted as Date values, while the 'TIME OCC' column is in string format. To examine the crime numbers in each hour of the day, the first two indices of 'TIME OCC' are extracted and converted into integers. 

### Exploratory Data Analysis
Firstly, we looked at which month of the year has the highest number of crimes reported, followed by which time of the day. Next, we looked at which areas of LA have the highest number of crimes reported based on the hour of the day, which has the highest number of crimes reported.

### Data Analysis
Include important codes for data analysis
```python
# Extracting the hours from the 'Time OCC' column
crimes['HOUR OCC'] = crimes['TIME OCC'].str[:2].astype(int)

#create a column with the month in the data frame
crimes['month'] = crimes['DATE OCC'].dt.month
crimes.head()

#create a joint plot between the HOUR OCC and month
sns.countplot(crimes, x='month',hue='month')
plt.legend([],frameon=False)
plt.ylabel('No. of crimes')
plt.show()

#create a count plot and df to find out which hour has the highest frequency of crimes
sns.countplot(crimes, x='HOUR OCC', hue='HOUR OCC',palette='pastel')
plt.legend([], frameon=False)
plt.ylabel('No. of crimes')
plt.xlabel('Time of the Day')
plt.show()

# places with crimes occurring at 12PM
mid_day = crimes.query('`HOUR OCC` == 12')
sns.countplot(data=mid_day, x='AREA NAME', hue='AREA NAME')
plt.xticks(rotation=90)
plt.ylabel('No. of crimes')
plt.xlabel('Patrol Divisions')
plt.legend([],frameon=False)
plt.show()
```
### Results
![Crimes](https://github.com/salaikht9228/Data_Analytic_Project/assets/120165361/bddeee01-54ae-476a-8d5f-d69eb5e4ed09)
Based on the analysis, the highest number of crimes are reported in June, possibly due to the beginning of summer, which suggests increased outdoor activities and larger gatherings, creating more opportunities for crime.

Next, there is a peak in crime reports at noon, as noon might coincide with a high volume of people in public spaces during lunch hours, potentially leading to theft or pickpocketing. 

Lastly, patrol divisions in 77th Street, Central and Pacific areas have the highest crime reported at noon, possibly due to the high density of people and the presence of major public spaces and tourist attractions, which may increase the chance of committing crimes in these areas.
### Recommendations

One of the recommendations would be to increase police visibility in these patrol divisions during lunch hours.
