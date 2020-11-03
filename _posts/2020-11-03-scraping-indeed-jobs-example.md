---
layout: post
title: Scraping Indeed Jobs Example
date: 2020-11-03 23:23 +0530
---

Scraping data science skills
=============================

- What skills are in demand for data scientists?
- Should we have a lecture on Spark or only on MapReduce?

We want to scrape the information from job advertisements for data scientists from indeed.com
Let's scrape and find out!

```python
## all imports
from IPython.display import HTML
import urllib as urllib2
import bs4 #this is beautiful soup
import pickle as cPickle
import re # regular expressions
```
```python
# Fixed url for job postings containing data scientist
url = 'http://www.indeed.com/jobs?q=data+scientist&l='
# read the website
source = urllib2.request.urlopen(url).read()
# parse html code
bs_tree = bs4.BeautifulSoup(source)
```
```python
# see how many job postings we found
job_count_string = bs_tree.find(id = 'searchCountPages').contents[0]
job_count_string = job_count_string.split()[-2]
print("Search yielded %s hits." % (job_count_string))

# not that job_count so far is still a string, 
# not an integer, and the , separator prevents 
# us from just casting it to int
job_count_digits = [int(d) for d in job_count_string if d.isdigit()]

job_count = np.sum([digit*(10**exponent) for digit, exponent in 
                    zip( job_count_digits[::-1], range(len(job_count_digits)) )])

print (job_count)
```
**Output:**
```
Search yielded 10,193 hits.
10193
```
```python
# The website is only listing 10 results per page, 
# so we need to scrape them page after page
num_pages = int(np.ceil(job_count/10.0))

base_url = 'http://www.indeed.com'
job_links = [2]
for i in range(1): #do range(num_pages) if you want them all
    if i%10==0:
        print (num_pages-i)
    url = 'http://www.indeed.com/jobs?q=data+scientist&start=' + str(i*10)
    html_page = urllib2.request.urlopen(url).read() 
    bs_tree = bs4.BeautifulSoup(html_page)
    job_link_area = bs_tree.find(id = 'resultsCol')
    job_postings = job_link_area.findAll("div", {"class": "jobsearch-SerpJobCard"})
#     print([jp.get('class') for jp in job_postings])
#     job_postings = [jp for jp in job_postings if not jp.get('class') is None 
#                     and ''.join(jp.get('class')) =="jobsearch-SerpJobCard"]
#     print(job_postings)
    job_ids = [jp.get('data-jk') for jp in job_postings]
    print(job_ids)
    
    # go after each link
    for id in job_ids:
        job_links.append(base_url + '/rc/clk?jk=' + id)

    time.sleep(1)

print( "We found a lot of jobs: ", len(job_links))
print(job_links)
```
**Output:**
```
1020
['22d70948c72621b5', '23f087c8f24a97c0', 'fc24628e0b434a5a', 'bb1ed3058e870b5c', 'dfda875f6c2c2f4b', '27a567a4b72cb449', '3d80239809596f2c', 'b28ac2ad86a2620c', '1659efbba759a208', 'f148db68e5e66581']
We found a lot of jobs:  11
[2, 'http://www.indeed.com/rc/clk?jk=22d70948c72621b5', 'http://www.indeed.com/rc/clk?jk=23f087c8f24a97c0', 'http://www.indeed.com/rc/clk?jk=fc24628e0b434a5a', 'http://www.indeed.com/rc/clk?jk=bb1ed3058e870b5c', 'http://www.indeed.com/rc/clk?jk=dfda875f6c2c2f4b', 'http://www.indeed.com/rc/clk?jk=27a567a4b72cb449', 'http://www.indeed.com/rc/clk?jk=3d80239809596f2c', 'http://www.indeed.com/rc/clk?jk=b28ac2ad86a2620c', 'http://www.indeed.com/rc/clk?jk=1659efbba759a208', 'http://www.indeed.com/rc/clk?jk=f148db68e5e66581']
```

Some precautions to enable us to restart our search
=========================
```python
# Save the scraped links
with open('scraped_links.pkl', 'wb') as f:
   cPickle.dump(job_links, f)
    
# Read canned scraped links
with open('scraped_links.pkl', 'rb') as f:
    job_links = cPickle.load(f)
```
```python
skill_set = {'mapreduce': 0, 'spark': 0}

## write initialization into a file, so we can restart later
with open('scraped_links_restart.pkl', 'wb') as f:
   cPickle.dump((skill_set, 0),f)    
```

Load the pickled links to continue scraping.
=========================
```python
# This code below does the trick, but could be optimized for speed if necessary
# e.g. skills are typically listed at the end of the webpage
# might not need to split/join the whole webpage, as we already know
# which words we are looking for 
# and could stop after the first occurance of each word

with open('scraped_links_restart.pkl', 'rb') as f:
    skill_set, index = cPickle.load(f)
    print ("How many websites still to go? ", len(job_links) - index)
```
**Output:**
```
How many websites still to go?  140
```
```python
counter = 0

for link in job_links[index:]:
    counter +=1  
    print(link)
    try:
        html_page = urllib2.request.urlopen(link).read().decode('utf-8')
    except urllib2.error.HTTPError:
        print ("HTTPError:")
        continue
    except urllib2.error.URLError:
        print ("URLError:")
        continue
    except socket.error as error:
        print ("Connection closed")
        continue
    
    
    html_text = re.sub("[^a-z.+3]"," ", html_page.lower()) # replace all but the listed characters
        
    for key in skill_set.keys():
        if key in html_text:  
            skill_set[key] +=1
            
    if counter % 5 == 0:
        print (len(job_links) - counter - index)
        print (skill_set)
        with open('scraped_links_restart.pkl','wb') as f:
            cPickle.dump((skill_set, index+counter),f)
```
**Output**
```
http://www.indeed.com/rc/clk?jk=22d70948c72621b5
http://www.indeed.com/rc/clk?jk=fc24628e0b434a5a
http://www.indeed.com/rc/clk?jk=23f087c8f24a97c0
HTTPError:
http://www.indeed.com/rc/clk?jk=3d80239809596f2c
http://www.indeed.com/rc/clk?jk=23ebb206eca72f54
135
{'mapreduce': 0, 'spark': 0}
http://www.indeed.com/rc/clk?jk=b28ac2ad86a2620c
.........
45
{'mapreduce': 0, 'spark': 21}
http://www.indeed.com/rc/clk?jk=8769d3b601a5d009
http://www.indeed.com/rc/clk?jk=17a98fda0978f458
HTTPError:
http://www.indeed.com/rc/clk?jk=5e4c8179efd94fc2
http://www.indeed.com/rc/clk?jk=95368911fea627e3
http://www.indeed.com/rc/clk?jk=5d5115c6c551d55a
```
```python
print (skill_set)
```
**Output**
```
{'mapreduce': 0, 'spark': 23}
```
```python
pseries = pd.Series(skill_set)
# pseries.sort(ascending=False)

pseries.plot(kind = 'bar')
## set the title to Score Comparison
plt.title('Data Science Skills')
plt.xlabel('Skills')
plt.ylabel('Count')
plt.show()
```
![Output](/assets/img/data_science_skills_bar_plot.png)

Looks like nobody wants mapreduce!!
===================================