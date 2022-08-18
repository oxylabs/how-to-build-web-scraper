# How to Build a Web Scraper with Python?
Step by step guide to building a web scraper with Python

## STEP 1. How to get the HTML?
The first step to building a web scraper is getting the HTML of a page. We will be using the ```requests``` library to get the HTML. It allows us to send a request and get a response. This can be installed using pip or pip3, depending on your Python installation.

```bash
pip install requests
```

Now create a new file with extension ```.py``` in your favorite editor and open it. Alternatively, you can also use Jupyter Notebooks or even a Python console. This allows the execution of small code snippets and viewing of the result immediately.

If you are using Jupyter Notebooks, enter these lines in a cell, and execute the cell. If you are using a code editor, enter these lines, save the file, and execute it with Python.

```python
import requests

url_to_parse = "https://en.wikipedia.org/wiki/Python_(programming_language)"
response = requests.get(url_to_parse)
print(response)
```

```python
<Response [200]>
```

```python
print(response.text)
```

```html
<!DOCTYPE html><html class="client-nojs" lang="en" dir="ltr"><head><meta charset="UTF-8"/><title>Python (programming language) - Wikipedia</title>…
```

```bash
pip install beautifulsoup4
```

```bash
pip install bs4
```

```python
import requests
from bs4 import BeautifulSoup
url_to_parse = "https://en.wikipedia.org/wiki/Python_(programming_language)"
response = requests.get(url_to_parse)
soup = BeautifulSoup(response.text,'html.parser')
```

```python
soup.title
# output <title>Python (programming language) - Wikipedia</title>
soup.title.name
# output 'title'
soup.title.text
# output 'Python (programming language) - Wikipedia'
soup.title.parent.name
# output 'head'
```

```python
soup.find("div",id="toc")
```

```html
<a href="/wiki/End-of-life_(product)" class="mw-redirect" title="End-of-life (product)">end-of-life</a>
```

```python
soup.find('a',title="End-of-life (product)")
soup.find('a',href="/wiki/End-of-life_(product)")
```

```python
soup.find('a',title="End-of-life (product)",href="/wiki/End-of-life_(product)")
```

```python
soup.find('a',class="mw-redirect") # SyntaxError: invalid syntax
```

```python
soup.find('a',class_="mw-redirect") # will return first a tag with this class
```

```html
<a href="/wiki/End-of-life_(product)" class="mw-redirect" title="End-of-life (product)">end-of-life</a>
```

```python
soup.select_one('a[title="End-of-life (product)"]')

soup.select_one('a[href="/wiki/End-of-life_(product)"]')
```

```python
soup.select_one('a[title="End-of-life (product)"][href="/wiki/End-of-life_(product)"]')
```

```python
soup.select_one('a.mw-redirect') # will return the first a tag with mw-redirect
soup.select_one('a#mw-redirect') # will return the first a tag with id mw-redirect
```

```python
soup.select_one('a.mw-redirect.external') # will return the first a tag with classes mw-redirect and external.
```

```python
toc = soup.select("span.toctext")
for item in toc:
print(item)
# OUTPUT
# <span class="toctext">History</span>
# <span class="toctext">Design philosophy and features</span>
# <span class="toctext">Syntax and semantics</span>
```

```python
toc = soup.select("span.toctext")
for item in toc:
print(item.text)
# OUTPUT
# History
# Design philosophy and features
# Syntax and semantics
# ...
```

```python
for item in soup.select('li.toclevel-1'):
toc_number = item.select_one('span.tocnumber').text
print(toc_number)
# OUTPUT
# 1
# 2
# 3
# …
```

```python
# Create empty list
data = []
# loop over outer elements
for item in soup.select('li.toclevel-1'):
	# Get the toc number element and it’s text
toc_number = item.select_one('span.tocnumber').text
	# Get the toc text element and it’s text
toc_text= item.select_one('span.toctext').text
# Create a dictionary and add to the list
data.append({
         'TOC Number': toc_number,
         'TOC Text': toc_text
})
```

```python
# Import csv module
import csv
# open a new file in write mode
with open('wiki.csv', 'w', newline='') as csvfile:
	# Specify the column names
fieldnames = ['TOC Number', 'TOC Text']
# create a dictionary writer object
writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
# write the headers, this will write the fieldnames as column headings
writer.writeheaders()
# run a loop on the data
for item in data:
	# Each item in the list is a dictionary
	# this will be written in one row.
writer.writerow(item)
```

```bash
npm init -y
```

```bash
npm install axios cheerio json2csv
```

```javascript
// load axios
const axios = require("axios");
const wiki_python = "https://en.wikipedia.org/wiki/Python_(programming_language)";

//create an async function
(async function() {
// get the response		
const response = await axios.get(wiki_python);
// prints 200 if response is successful
console.log(response.status)
})();
```

```javascript
const response = await axios.get(url);
```

```javascript
const $ = cheerio.load(response.data);
```

```javascript
const TOC = $("li.toclevel-1"); 
```

```javascript
const toc_data = []
TOC.each(function () {
        level = $(this).find("span.tocnumber").first().text();
        text = $(this).find("span.toctext").first().text();
        toc_data.push({ level, text });
    });
```

```javascript
const parser = new j2cp();
    const csv = parser.parse(toc_data);
    fs.writeFileSync("./wiki_toc.csv", csv);
```

```javascript
const fs = require("fs");
const j2cp = require("json2csv").Parser;
const axios = require("axios");
const cheerio = require("cheerio");

const wiki_python =  "https://en.wikipedia.org/wiki/Python_(programming_language)";

async function getWikiTOC(url) {
  try {
    const response = await axios.get(url);
    const $ = cheerio.load(response.data);

    const TOC = $("li.toclevel-1");
    let toc_data = [];
    TOC.each(function () {
      level = $(this).find("span.tocnumber").first().text();
      text = $(this).find("span.toctext").first().text();
      toc_data.push({ level, text });
    });
    const parser = new j2cp();
    const csv = parser.parse(toc_data);
    fs.writeFileSync("./wiki_toc.csv", csv);
  } catch (err) {
    console.error(err);
  }
}

getWikiTOC(wiki_python);
```
