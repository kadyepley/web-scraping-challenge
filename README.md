# Module 11 Challenge
# Deliverable 1: Scrape Titles and Preview Text from Mars News

### Import Splinter and BeautifulSoup

from splinter import Browser
from bs4 import BeautifulSoup
browser = Browser('chrome')

## Step 1: Visit the Website
Use automated browsing to visit the [Mars news site](https://static.bc-edx.com/data/web/mars_news/index.html). 
Inspect the page to identify which elements to scrape.
   
**Visit the Mars news site**

url = 'https://static.bc-edx.com/data/web/mars_news/index.html'
browser.visit(url)

## Step 2: Scrape the Website
Create a Beautiful Soup object and use it to extract text elements from the website.

**Create a Beautiful Soup object**

html = browser.html
soup = BeautifulSoup(html,"html.parser")

**Extract all the text elements**

text_ele = soup.find_all("div", class_='list_text')
print(text_ele)

## Step 3: Store the Results
Extract the titles and preview text of the news articles that you scraped. Store the scraping results in Python data structures as follows:
* Store each title-and-preview pair in a Python dictionary. And, give each dictionary two keys: `title` and `preview`. An example is the following:
  ```python
  {'title': "NASA's MAVEN Observes Martian Light Show Caused by Major Solar Storm", 
   'preview': "For the first time in its eight years orbiting Mars, NASA’s MAVEN mission witnessed two different types of ultraviolet aurorae simultaneously, the result of solar storms that began on Aug. 27."
  }
  ```
* Store all the dictionaries in a Python list.
* Print the list in your notebook.
  
**Create an empty list to store the dictionaries**

list = []

**Loop through the text elements**
**Extract the title and preview text from the elements**
**Store each title and preview pair in a dictionary**
**Add the dictionary to the list**

for element in text_ele:
    title = element.find('div', class_='content_title').text
    preview = element.find('div', class_='article_teaser_body').text
    result = {'title': title, 'preview': preview}
    list.append(result)
    
**Print the list to confirm success**

print(list)

**Close out the browser**
browser.quit()



# Deliverable 2: Scrape and Analyze Mars Weather Data
### Import relevant libraries

from splinter import Browser
from bs4 import BeautifulSoup
import matplotlib.pyplot as plt
import pandas as pd
browser = Browser('chrome')

## Step 1: Visit the Website

Use automated browsing to visit the [Mars Temperature Data Site](https://static.bc-edx.com/data/web/mars_facts/temperature.html). Inspect the page to identify which elements to scrape.

**Visit the website**

https://static.bc-edx.com/data/web/mars_facts/temperature.html

url = "https://static.bc-edx.com/data/web/mars_facts/temperature.html"
browser.visit(url)

## Step 2: Scrape the Table

Create a Beautiful Soup object and use it to scrape the data in the HTML table.

**Create a Beautiful Soup Object**

html = browser.html
soup = BeautifulSoup(html,"html.parser")

**Extract all rows of data**

mars_temp_table = soup.find_all('table')
mars_temp_df = pd.read_html(str(mars_temp_table))[0]
mars_temp_df

## Step 3: Store the Data

Assemble the scraped data into a Pandas DataFrame. The columns will have the same headings as the table on the website. Here’s an explanation of the column headings:

* `id`: the identification number of a single transmission from the Curiosity rover
* `terrestrial_date`: the date on Earth
* `sol`: the number of elapsed sols (Martian days) since Curiosity landed on Mars
* `ls`: the solar longitude
* `month`: the Martian month
* `min_temp`: the minimum temperature, in Celsius, of a single Martian day (sol)
* `pressure`: The atmospheric pressure at Curiosity's location
  
**Create an empty list**

mars_weather_list = []

**Pull all data from the table above to search through**

table_data = soup.find('table', class_='table')

**Display table_date to make sure this pulls the correct data, then hashtag out.**

table_data 

**Loop through the scraped data to create a list of rows**

mars_weather = table_data.find_all('th')

for header_element in mars_weather:
    title = header_element.text
    mars_weather_list.append(title)

**Display mars_weather_list**

mars_weather_list

**Create a Pandas DataFrame by using the list of rows and a list of the column names**

mars_weather_df = pd.DataFrame(columns=mars_weather_list)

for row in table_data.find_all('tr',class_='data-row'):
    data = row.find_all('td')
    row_data = [td.text.strip() for td in data]
    length = len(mars_weather_df)
    mars_weather_df.loc[length] = row_data
    
**Confirm DataFrame was created successfully**

mars_weather_df

## Step 4: Prepare Data for Analysis

Next we'll examine the data types that are currently associated with each column. It was necessary, to cast (or convert) the data to the appropriate `datetime`, `int`, or `float` data types.

**Examine data type of each column**

mars_weather_df.dtypes

**Change data types for data analysis**

**All are currently object, so each was cast to the necessary data types**

mars_weather_df['terrestrial_date'] = pd.to_datetime(mars_temp_df['terrestrial_date'])
mars_weather_df['sol'] = mars_weather_df['sol'].astype('int64')
mars_weather_df['ls'] = mars_weather_df['ls'].astype('int64')
mars_weather_df['month'] = mars_weather_df['month'].astype('int64')
mars_weather_df['min_temp'] = mars_weather_df['min_temp'].astype(float)
mars_weather_df['pressure'] = mars_weather_df['pressure'].astype(float)

**Confirm type changes were successful by examining data types again**

mars_weather_df.dtypes

## Step 5: Analyze the Data

Analyze your dataset by using Pandas functions to answer the following questions:

1. How many months exist on Mars?
2. How many Martian (and not Earth) days worth of data exist in the scraped dataset?
3. What are the coldest and the warmest months on Mars (at the location of Curiosity)? To answer this question:
    * Find the average the minimum daily temperature for all of the months.
    * Plot the results as a bar chart.
4. Which months have the lowest and the highest atmospheric pressure on Mars? To answer this question:
    * Find the average the daily atmospheric pressure of all the months.
    * Plot the results as a bar chart.
5. About how many terrestrial (Earth) days exist in a Martian year? To answer this question:
    * Consider how many days elapse on Earth in the time that Mars circles the Sun once.
    * Visually estimate the result by plotting the daily minimum temperature.

**1. How many months exist on Mars?**

mars_months = mars_weather_df.groupby('month')
mars_months['month'].count()

**2. How many Martian (and not Earth) days worth of data exist in the scraped dataset?**

mars_days = mars_weather_df['sol'].nunique()
mars_days

**3. What are the coldest and the warmest months on Mars (at the location of Curiosity)?** 

*To answer this question:*

    * Find the average the minimum daily temperature for all of the months.
    * Plot the results as a bar chart.
    
**What is the average low temperature by month?** 

avg_low_temp = mars_months['min_temp'].mean()
avg_low_temp

**Plot the average temperature by month**

avg_low_temp_bar = avg_low_temp.plot.bar()
avg_low_temp_bar.set_ylabel('Temperature in Celsius')

**Identify the coldest and hottest months in Curiosity's location**

**Reset the index while renaming column for min_temp to avg_min_temp**

avg_low_temp_reset = avg_low_temp.reset_index().rename(columns = {'min_temp':'avg_min_temp'})

**Set the index to month**

avg_low_temp_reset = avg_low_temp_reset.set_index('month')

**Sort by values in the new avg_min_temp column**

avg_low_temp_sorted = avg_low_temp_reset.sort_values(by = 'avg_min_temp')

**Plot this new df as a bar graph**

avg_low_temp_sorted_bar = avg_low_temp_sorted.plot.bar()

**Label axis and remove the legend**

avg_low_temp_sorted_bar.set_ylabel('Temperature in Celsius')
avg_low_temp_sorted_bar.set_xlabel('month')
avg_low_temp_sorted_bar.get_legend().remove()

**4. Average pressure by Martian month**

avg_pressure = mars_months['pressure'].mean()
avg_pressure

**Plot the average pressure by month**

**Reset the index and rename the 'pressure' column to 'avg_pressure'**

avg_pressure_reset = avg_pressure.reset_index().rename(columns = {'pressure':'avg_pressure'})

**Set the index by month**

avg_pressure_reset = avg_pressure_reset.set_index('month')

**Sort the data by avg_pressure**

avg_pressure_sorted = avg_pressure_reset.sort_values(by = 'avg_pressure')

**Plot the sorted data**

avg_pressure_sorted_bar = avg_pressure_sorted.plot.bar()

**Set the y-axis label**

avg_pressure_sorted_bar.set_ylabel('Atmospheric Pressure')

**Remove the legend from the graph**

avg_pressure_sorted_bar.get_legend().remove()

**5. How many terrestrial (earth) days are there in a Martian year?**

mars_days_sorted = mars_weather_df.sort_values('sol')
 
mars_days_sorted.plot(
    x = 'sol',
    y = 'min_temp',
    xlabel = 'Number of terrestrial days',
    ylabel = 'Minimum temperature',
    legend=False
)

On average, the third month has the coldest minimum temperature on Mars, and the eighth month is the warmest. But it is always very cold there in human terms!


Atmospheric pressure is, on average, lowest in the sixth month and highest in the ninth.
The distance from peak to peak is roughly 1425-750, or 675 days. A year on Mars appears to be about 675 days from the plot. Internet search confirms that a Mars year is equivalent to 687 earth days.

## Step 6: Save the Data**

Export the DataFrame to a CSV file.

**Write the data to a CSV**

mars_weather_df.to_csv('mars_weather_data.csv')

**Close out the browser**

browser.quit()

