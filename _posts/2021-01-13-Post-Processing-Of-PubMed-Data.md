---
layout: post
title:  "Post-Processing of PubMed Data"
date:   2021-01-13 15:18:33 -0500
categories: Data-Gathering
---

The first version of a post-processing parser for our abstracts obtained through the scraping of PubMed. The output is a list of tuples that include both the sentence containing one of our keywords as well as the keyword itself. This is one iteration, the following iterations to include context in the form of parts of the sentences that surround our viral language containing sentence. 

{% highlight python %}
import json
import re

# list of keywords of interest
viral_lang = [" virus ", " viral ", " transmission ", " transmit "," symptom ", " pandemic ", " infectious ", " infection ", " infected ", " infected ", " infect ", " immunity ", " immunize ", " immune ", " healthy ", " health ", " epidemic ", " disease ", " diseased ", " contagious ", " contagion "]
# where our tokenized sentences are stored
sentences = []
# list of tuples (sentence, keyword in sentence)
viral_sentences = []

with open('Studies.json') as f:
    data = json.load(f)

# take data and tokenizes into sentences
temp = []
for item in data:
    temp = (re.split(r' *[\.\?!][\'"\)\]]* *', item))
    for things in temp:
        sentences.append(things)

# looks over tokenized sentences and checks for our keyword list
for item in sentences:
    for x in viral_lang:
        if x in item:
            viral_sentences.append((item, x))

{% endhighlight %}

An Example Output:

{% highlight python %}
('Further monitoring of the disease prognosis and effective control of the "relapse" of the epidemic has become the next focus of work','epidemic')
-------------------------
('Further monitoring of the disease prognosis and effective control of the "relapse" of the epidemic has become the next focus of work', '   disease')
-------------------------
('Therefore, we suggest that faecal virus nucleic acid should be tested as a routine monitoring index for COVID-19 and a negative result be added to the criteria', ' virus ')   
-------------------------
('Simultaneously, we should strengthen the regular follow-up of discharged patients with continuous monitoring of the recurrence of viral nucleic acid', ' viral ')
-------------------------
('Sexual transmission of SARS-CoV-2 virus and its role in the spread of COVID-19: A living systematic review protocol', ' virus ')
-------------------------
('Sexual transmission of SARS-CoV-2 virus and its role in the spread of COVID-19: A living systematic review protocol', ' transmission ')
-------------------------
('Objective: To provide a review of the literature on the presence of SARS-CoV-2 in the sexual fluids of patients with COVID-19 and to observe its possible sexual transmission in a timely, rigorous, and continuously updated manner', ' transmission ')
-------------------------
('We will include randomized trials evaluating the sexual transmission of the SARS-CoV-2 virus', ' transmission ')
{% endhighlight %}