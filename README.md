# **Module 12 Challenge**
# Deliverable 1: Scrape Titles and Preview Text from Mars News
### Import Splinter and BeautifulSoup
from splinter import Browser
from bs4 import BeautifulSoup
browser = Browser('chrome')

## Step 1: Visit the Website
1. Use automated browsing to visit the [Mars news site](https://static.bc-edx.com/data/web/mars_news/index.html). Inspect the page to identify which elements to scrape.
   
### Visit the Mars news site
url = 'https://static.bc-edx.com/data/web/mars_news/index.html'
browser.visit(url)

## Step 2: Scrape the Website
Create a Beautiful Soup object and use it to extract text elements from the website.

### Create a Beautiful Soup object
html = browser.html
soup = BeautifulSoup(html,"html.parser")

### Extract all the text elements
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
  
### Create an empty list to store the dictionaries
list = []

### Loop through the text elements
### Extract the title and preview text from the elements
### Store each title and preview pair in a dictionary
### Add the dictionary to the list
for element in text_ele:
    title = element.find('div', class_='content_title').text
    preview = element.find('div', class_='article_teaser_body').text
    result = {'title': title, 'preview': preview}
    list.append(result)
print(list)
    
### Print the list to confirm success
print(list)
browser.quit()
