---
layout: post
title:  "PubMed Scraper"
date:   2021-01-03 15:18:33 -0500
categories: Data-Gathering
---
A simple piece of code that returns Title:Abstract in the form of a json file from a given keyword and desired number of results. This will give us our pre-processed data from PubMed, I will then parse that data down to required lengths in the form of a tuple with the attached keyword that it contains (a current thought, there might be a better approach). Furthermore the parsed data will be in the form of sentences within parts of it's surrounding sentences, in order to better the context in which our keyword of interest is being used. 

{% highlight python %}
from bs4 import BeautifulSoup
import urllib
import requests
import json
import re

url="https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&retmode=json&retmax=NUM&sort=relevance&term=KEYWORD"
dict_studies = {}
links = []

# We ask the user to provide the keyword and number of results and subsequently replace these elements in the url string
keyword = str(input('Enter Search Term: '))
num = int(input('Enter Desired  Number of Results: '))
url = url.replace('NUM', str(num))
url = url.replace('KEYWORD', keyword)

webpage = urllib.request.urlopen(url).read()
dict_page =json.loads(webpage)
idlist = dict_page["esearchresult"]["idlist"]

#Creates the links from embeded href tags
for link in idlist:
    links.append("https://pubmed.ncbi.nlm.nih.gov/"+str(link)+"/")

#Loops through links gathering Title and Abstract
for url in links:
    source = requests.get(url).text
    soup = BeautifulSoup(source, 'html.parser')
    abstract = soup.find(id = "enc-abstract").get_text()
    heading = soup.find(id = "heading")
    title = heading.find('h1').get_text()
    title = title.replace('\n', ' ').replace('\r', '')
    abstract = abstract.replace('\n', ' ').replace('\r', '')
    title = title.strip()
    abstract = abstract.strip()
    dict_studies.update({title:abstract})
    dict_studies[title] = abstract

# Dumps contents of dict into json
with open("Studies.json", "w") as json_file:
    json_string = json.dumps(dict_studies, indent=4)
    print(json_string, file=json_file)
{% endhighlight %}

The idea here is using a defined list of keywords, we can build our "viral" language model with the collecting of where these keywords are used and their greater context. Prof. Beal has provided a solid (yet appendable) list to start.

