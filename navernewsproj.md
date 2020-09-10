## Naver News Article Classification

This project aims scrapes Korean IT news articles from Naver in order to classify them into predefined subcategories. It can be adapted for use with English articles, too. Code blocks below are snippets; full implementation is available on my GitHub.
## Modules/packages used: <br>
Pandas <br> Requests <br> Beautiful Soup <br> Scikit-learn <br> KoNLPy

### 1. Web Scraping

I used BeautifulSoup and Requests to crawl through all IT articles from the past 10 days from eight subcategories (roughly translated): Mobile, Social Networks, Telecommunications/Media, General IT, Security/Hacking, Computers, Games/Reviews, and General Science. <br> <br>
Each category had its own numerical suffix which I added to a dictionary in order to append to the base URL, so I could crawl through each category one by one.

```python
categories = {"모바일":"731", "인터넷/SNS":"226", #subcategories of IT news: mobile, internet, etc.
              "통신/뉴미디어":"227", "IT 일반":"230", "보안/해킹":"732", 
              "컴퓨터":"283", "게임/리뷰":"229", "과학 일반":"228"} 
```
For each category, I further appended page numbers in order to crawl through every article from the last 10 days. This strategy resulted in varying page counts for each date (weekends had only one or two pages), which I accounted for by continuously detecting the "Next Page" button. If not found, the program would decrease the date value by one and repeat the process. <br>

Each page would yield around 20 news articles, and I added the category value as well as the article's raw body text (excluding hyperlinks, images, and annoying JavaScript code blocks) to build my pandas dataframe.

```python
article_response = requests.get(link)
                        article_html = article_response.content
                        article_soup = BeautifulSoup(article_html,'lxml')
                        raw_body = article_soup.find(id = ['articleBodyContents', 'articeBody'])
if unwanted_element != None:
                            unwanted_element.extract() #remove unwanted JavaScript
```

### 2. Data Cleanup and Tokenization

Eventually, I had amassed 36,410 rows of data. 
<img src="images/naverdata.png?raw=true"/>
<br> <br>

The raw text had lots of unhelpful bits and pieces, including filler words (the closest English equivalents I can think of are "very", "of", "for", etc.) that did not add much value in conveying the overall message of the article. Furthermore, the Korean language is quite difficult to approach from an NLP perspective as words like "candy" (사탕) can be separated into "four" (사) and "soup" (탕). "Four soup" was less useful in my data than "candy" was, and I needed a way to make sure phrases weren't separated into meaninglessness. <a href="https://konlpy.org/en/latest/"> KoNLPy</a> is an NLP package  built specifically for the Korean language. I used MeCab-ko, its Korean morphological analyzer and part-of-speech tagger, to eliminate certain phrases and to make sure significant words and certain morphemes were not separated further into less significant morphemes.
  
```python
 for main_index in range(len(main_data)): #run through each item in main_data
    cleaned_text = ""
    tokenized_data = pd.DataFrame([])
    category = main_data.iloc[main_index]["category"] #gets category name
    article_text = main_data.iloc[main_index]["text"] #gets article text
    processed_text = (" ").join(mecab.morphs(mecab.nouns(article_text)))
```

### 3. Dataset Training

```javascript
if (isAwesome){
  return true
}
```

### 4. Results

<img src="images/dummy_thumbnail.jpg?raw=true"/>

