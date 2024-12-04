# A study of Chicago's Living Experience
*we want to study... Crime, Housing, and Transportation. 

###* First, we import libraries needed *###
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
```python

## Next, we import data csv files
```python
# Load data from a CSV file and store as different dataframes (df)
df_crime = pd.read_csv('chicago_crime_data.csv')
df_real_est = pd.read_csv('realest.csv')
df_red_light = pd.read_csv('red_light_camera_violation.csv')
df_speed = pd.read_csv('speed_camera_violation.csv')
```

## First, I want to study Chicago's Crime
### Data Prepare
First, prepare the data ready for use
```python
# Ensure DATE column is datetime format
df_crime['DATE'] = pd.to_datetime(df_crime['DATE'], errors='coerce')

# Filter data from 2019 onwards
df_filtered = df_crime[df_crime['DATE'].dt.year >= 2019]

# Rename selected columns
columns_to_select = {
    'DATE': 'date',
    'ZIP_CODE': 'zip_zone',
    'DAY_OF_WEEK': 'day_of_week',
    'HOUR': 'hour',
    'INCIDENT_TYPE_DESCRIPTION': 'single_or_multi',
    'ROUNDS': 'rounds'
}

df_for_use = df_filtered[list(columns_to_select.keys())].rename(columns=columns_to_select)
```
I did.... so we can ....

### Visualization for 6 aspects 

#### 1. Prepare the data for visualization By quarter 
```python

# Subplot 1: Distribution by quarter
quarters = {
    'Fall': [(9, 13), (12, 25)],
    'Winter': [(12, 25), (3, 15)],
    'Spring': [(3, 15), (5, 20)],
    'Summer': [(5, 20), (9, 13)]
}

quarter_counts = {'Fall': 0, 'Winter': 0, 'Spring': 0, 'Summer': 0}
for date in df_for_use['date'].dropna():
    month, day = date.month, date.day
    for quarter, ((start_month, start_day), (end_month, end_day)) in quarters.items():
        if (start_month < month < end_month) or (
            start_month == month and day >= start_day
        ) or (end_month == month and day <= end_day):
            quarter_counts[quarter] += 1

quarter_df = pd.DataFrame(
    {'Quarter': list(quarter_counts.keys()), 'Count': list(quarter_counts.values())}
)
```

#### 2. Prepare for visualization by day of week 
```python 
weekday_counts = df_for_use['day_of_week'].dropna().value_counts()
```


#### 3. Prepare for ....
```python
zip_counts = df_for_use['zip_zone'].dropna().value_counts().head(7)

```

#### 4,5,6 By ....
```python
# Subplot 4: Single vs multiple gunshots
gunshot_counts = df_for_use['single_or_multi'].dropna().value_counts()

# Subplot 5: Most common rounds
rounds_counts = df_for_use['rounds'].dropna().value_counts().head(3)

# Subplot 6: Hour distribution
bins = [7, 12, 17, 23, 3, 7]
hour_bins = pd.cut(df_for_use['hour'], bins=[0, 7, 12, 17, 23, 24], labels=['3-7', '7-12', '12-5', '5-11', '12-3'])
hour_counts = hour_bins.value_counts().sort_index()

```


### Visualize them in a graph

```python

# Create 3x2 subplots
fig, axes = plt.subplots(3, 2, figsize=(15, 15))

# Plot each subplot
sns.barplot(x='Quarter', y='Count', data=quarter_df, ax=axes[0, 0], palette='Blues')
axes[0, 0].set_title('Case Distribution by Quarter')

axes[0, 1].pie(
    weekday_counts, labels=weekday_counts.index, autopct='%1.1f%%',
    colors=sns.color_palette('husl', len(weekday_counts)), startangle=90
)
axes[0, 1].set_title('Day of Week Distribution')

sns.barplot(x=zip_counts.index, y=zip_counts.values, ax=axes[1, 0], palette='pink')
axes[1, 0].set_title('Top 7 Zip Zones')

sns.barplot(x=gunshot_counts.index, y=gunshot_counts.values, ax=axes[1, 1], palette='Oranges')
axes[1, 1].set_title('Gunshot Type Distribution')

sns.barplot(x=rounds_counts.index, y=rounds_counts.values, ax=axes[2, 0], palette='Greens')
axes[2, 0].set_title('Most Common Rounds')

sns.barplot(x=hour_counts.index, y=hour_counts.values, ax=axes[2, 1], palette='copper')
axes[2, 1].set_title('Hour Distribution')

plt.tight_layout()

# Save the plot to a file
fig.savefig("crime_analysis_plots.png")
plt.show()

```

result is : [use the link]
