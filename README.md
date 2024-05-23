# Restaurant-Data-Analysis

#Import all the necessary libraries
import os
import pandas as pd

pd.set_option('display.max_rows', 20)
pd.set_option('display.max_columns', None)

import matplotlib.pyplot as plt
import seaborn as sns

import warnings
warnings.simplefilter('ignore')

#View the current working directory
os.getcwd()

#List all files in the directory
os.listdir(os.getcwd())

master_data = pd.read_excel('data.xlsx')
master_data.head(2)

#Identify the structure of the dataset that has been imported
master_data.shape

master_data.info()

#Identify null values
master_data.isnull().sum()

#Drop the row with a null value
master_data.dropna(subset=['Restaurant Name'], inplace=True)

#Verify number of remaining null values
master_data.isnull().sum()

#Address the remaining null values in the 'Cuisines' column by filling them with 'American' as the restaurants are in the US
master_data['Cuisines'].fillna('American', inplace=True)

#Confirm there is no null values in the dataset
master_data.isnull().sum()

#Check for any duplicates
master_data.duplicated().sum()

#Exploring geographical distribution of the restaurants
city_max_restaurant = master_data['City'].value_counts().idxmax()
city_min_restaurant = master_data['City'].value_counts().idxmin()

print("City with maximum number of restaurants:", city_max_restaurant)
print("City with minimum number of restaurants:", city_min_restaurant)

#Explore distribution of ratings
master_data['Aggregate rating'].describe()

#Explore franchise with the most national presence
print("Franchise with the most national presence:", master_data['Restaurant Name'].value_counts().idxmax())

#Count number of restaurants that allow table booking
table_booking_yes = (master_data['Has Table booking'] == 'Yes').sum()

#Count number of restaurants that do not allow
table_booking_no = (master_data['Has Table booking'] == 'No').sum()

#Calculate the ratio
ratio_table_booking = table_booking_yes/table_booking_no

print(f"Ratio between restaurants that allow table booking vs that do not: {ratio_table_booking:.2f}")

#Percentage of restaurants providing online delivery
master_data['Has Online delivery'].value_counts(normalize=True)*100

#Difference in no. of votes for restaurants that deliver and those that don't
master_data.groupby('Has Online delivery')['Votes'].describe()

#Identify top 10 cuisines across cities
cuisines_df = master_data['Cuisines'].str.split(', ', expand=True).stack().reset_index(level=1, drop=True).rename('Cuisine')
cuisines_df.value_counts().head(10)

#Identify maximum and minimum no. of cuisines that a restaurant serves
split_cuisines = master_data['Cuisines'].str.split(', ')

num_cuisines = split_cuisines.apply(lambda x: len(x))

print('Maximum no. of cuisines served in a restaurant:', num_cuisines.max())
print('Minimum no. of cuisines served in a restaurant:', num_cuisines.min())

#Relationship between number of cuisines served in a restaurant and given ratings
avg_rating_by_cuisines = master_data.groupby(num_cuisines)['Aggregate rating'].mean()

plt.plot(avg_rating_by_cuisines.index, avg_rating_by_cuisines.values)
plt.xlabel('Number of Cuisines Served')
plt.ylabel('Average Rating')
plt.show()

#Relationship between cost and other variables
columns_for_pairplot = ['Price range', 'Aggregate rating', 'Votes']
sns.pairplot(master_data[columns_for_pairplot])

#Relationship between cost and other variables
master_data.corr()['Price range']

#Relationship between rating and various factors
master_data.corr()['Aggregate rating']
