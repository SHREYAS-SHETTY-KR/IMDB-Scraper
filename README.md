# IMDB-Scraper
The goal of this project is to build a IMDB Scraper that extracts Top 250 movies from www.imdb.com/chart/top/

## Prerequisites
[BeautifulSoup]() and [openpyxl]()
Additionally, the related Python libraries need to be imported.

## Create Excel workbook
```python

excel = openpyxl.Workbook()
print(excel.sheetnames)
sheet = excel.active

#giving name to excel sheet
sheet.title = 'Top Rated Movies'
print(excel.sheetnames)

#append column name to the excel sheet
sheet.append(['Movie Rank', 'Movie Name', 'Year of Release', 'IMDB Rating'])
```
## Extract collection
Next, we are going to extract the content of this page from the html content in the background.  Before we do this, we'll need to create a **soup** object, which will parse the html content from the page content. Create a soup object using the `source.text` to retrieve the html text, and then we'll use the default html parser to parse the html.

```python

#loading url using  get request
source = requests.get('https://www.imdb.com/chart/top/')
source.raise_for_status()
    
soup = BeautifulSoup(source.text,'html.parser')
```
Right-click on the item you want to inspect, such as the heading, and click on "Inspect". On the right-hand side, or lower side of the browser, a document inspector will show the html content in the background. If you hover over an html element, it should highlight the element on the webpage so that you can see the element on the page related to the element you are hovering over in the code.

Once you've found the appropriate tag, you must find a way to identify it uniquely amongst all the other tags of it's name. A "div" tag is pretty generic, but often you can use a class, and id, or another property to identify it's group so that you can extract it. Given the available fields, it appears that the **tbody** tag and **class** with it's value of `lister-list` would be good options to identify the record.

```python
movies = soup.find('tbody', class_="lister-list").find_all('tr')
```

## looping through each elements

```python

#extract details of movie through looping
for movie in movies:
        
        name = movie.find('td', class_="titleColumn").a.text

        rank = movie.find('td', class_="titleColumn").get_text(strip=True).split('.')[0]

        year = movie.find('td', class_="titleColumn").span.text.strip('()')

        rating = movie.find('td', class_="ratingColumn imdbRating").strong.text
```

printing result and appending all the extracted data to excel sheet
```python
print(rank, name, year, rating)

sheet.append([rank, name, year, rating])
````

## Handling errors
using **try** and **except** method we can avoid any error while loading url

```python
try:
    source = requests.get('https://www.imdb.com/chart/top/')
    source.raise_for_status()

    soup = BeautifulSoup(source.text,'html.parser')
    
    movies = soup.find('tbody', class_="lister-list").find_all('tr')
    
    for movie in movies:
        
        name = movie.find('td', class_="titleColumn").a.text

        rank = movie.find('td', class_="titleColumn").get_text(strip=True).split('.')[0]

        year = movie.find('td', class_="titleColumn").span.text.strip('()')

        rating = movie.find('td', class_="ratingColumn imdbRating").strong.text
        
        print(rank, name, year, rating)
        sheet.append([rank, name, year, rating])

except Exception as e:
    print(e)
```

## putting it all together
final script to exctract Top 250 movies from IMDB website
```python
from bs4 import BeautifulSoup
import requests, openpyxl

excel = openpyxl.Workbook()
print(excel.sheetnames)
sheet = excel.active
sheet.title = 'Top Rated Movies'
print(excel.sheetnames)
sheet.append(['Movie Rank', 'Movie Name', 'Year of Release', 'IMDB Rating'])

try:
    source = requests.get('https://www.imdb.com/chart/top/')
    source.raise_for_status()

    soup = BeautifulSoup(source.text,'html.parser')
    
    movies = soup.find('tbody', class_="lister-list").find_all('tr')
    
    for movie in movies:
        
        name = movie.find('td', class_="titleColumn").a.text

        rank = movie.find('td', class_="titleColumn").get_text(strip=True).split('.')[0]

        year = movie.find('td', class_="titleColumn").span.text.strip('()')

        rating = movie.find('td', class_="ratingColumn imdbRating").strong.text
        
        print(rank, name, year, rating)
        sheet.append([rank, name, year, rating])

except Exception as e:
    print(e)

excel.save('IMDB Movie Ratings.xlsx')
```
