# Beginning_Data_Scrubbing
#!/usr/bin/env python
# coding: utf-8

# In[ ]:


# Build a Reddit crawler/scraper from scratch
# Step 1:
# You need to have a Reddit account



# In[ ]:


# Step 2: Find an interesting subreddit
# https://www.reddit.com/r/askscience/


# In[ ]:


# Step 3: Try to google some examples how to build a Reddit crawler/scraper
# Crawler is used collecting links
# scrapper is collecting content
# For example
# https://www.storybench.org/how-to-scrape-reddit-with-python/
# Copy and Paste and make necessary modifications
reddit = praw.Reddit(client_id='PERSONAL_USE_SCRIPT_14_CHARS',                      client_secret='SECRET_KEY_27_CHARS ',                      user_agent='YOUR_APP_NAME',                      username='rachelccanon',                      password='Guiness95!')


# In[ ]:


# So lets start our "copy and paste" here
# Step 3.1: create your application ("APP")
# https://www.reddit.com/prefs/apps
# All infos:
# personal use script: v1M5SD6H067gVA
# secret: 37-HMjFlH5K9jz1Vs_l2IqznKeo


# In[1]:


# Step 3.2: We need to import a third-party module to use the Reddit API (Application Programming Interface)
# run this in the command line (or in jupyter notebook, you can use "!" to start a command)
# praw documentation
# https://praw.readthedocs.io/en/latest/getting_started/quick_start.html
get_ipython().system('pip install praw')


# In[ ]:


# How to do that in command shell
# Add to system environment variable path
# D:\Anaconda3; 
# D:\Anaconda3\Scripts; 
# D:\Anaconda3\Library\bin 

# Mac: make path adjustment accordingly


# Download get-pip.py
# https://bootstrap.pypa.io/get-pip.py

# run this file 
# python /{path}/get-pip.py

# finally run pip in your command shell


# In[2]:


# Step 3.3: import this module and log in!
import praw
reddit = praw.Reddit(client_id='vBxKfo_GnzlHxA',
                     client_secret='c8pnvvsRRMrMNCNanXyrIdDW7Go',
                     user_agent='My Reddit Text Scrapper',
                     username='rachelccanon',
                     password='Guiness95!')


# In[3]:


# To a specific subreddit
subreddit = reddit.subreddit('askscience')
print(subreddit.display_name)  # Displayed name
print(subreddit.title)         # "Full" name
print(subreddit.description)   # Description


# In[7]:


# Get some threads
subreddit = reddit.subreddit('askscience')
for submission in subreddit.top(limit=3):
    print(submission.id)
    print(submission.title)
    print(submission.author)
    print(submission.url)
    print("\n")


# In[8]:


# Get comments
for submission in subreddit.hot(limit=1):
    print(submission.comments)


# In[9]:


for submission in subreddit.hot(limit=1):
    print(submission.comments.list())


# In[12]:



# get comment text
for submission in subreddit.hot(limit=1):
    comment_list = submission.comments.list()
    for comment in comment_list:
        print(comment.body)


# In[10]:


# Search keywords
for thread in subreddit.search(query="COVID"):
    print(thread.id)


# In[15]:


submission = reddit.submission(id='fwzaft')
print(submission.title)
print(submission.author)
print(submission.created_utc)


# In[21]:


# sql is useful so we cna compare relational data
import sqlite3
destination = r"C:\Users\zhany\OneDrive\Desktop"
conn = sqlite3.connect('{0}\Reddit.db'.format(destination))
cur = conn.cursor()


# In[22]:


# cur.execute('pragma encoding')
# rows = cur.fetchall()
# for row in rows:
#     print(row)


# In[23]:


# Store into database

# Identify what types of data you want
# id, title, body, author, url, created_utc, number of comments, voting score, comments (Do we need a new table? -> Yes)
# Create database first



create_sql1 = """CREATE TABLE threads (ID text primary key, title text, body text, author text, url text, 
                created_utc real, num_comments integer, score integer);"""
create_sql2 = """CREATE TABLE comments (ID text primary key, parent_ID text, thread_ID text,
                body text, author text, created_utc real, score integer,
                foreign key (ID) references threads(ID));"""



cur.execute(create_sql1)
cur.execute(create_sql2)


# In[24]:


# delete_sql1 = "DROP TABLE threads;"
# delete_sql2 = "DROP TABLE comments;"
# cur.execute(delete_sql1)
# cur.execute(delete_sql2)


# In[25]:


# Now INSERT!
subreddit = reddit.subreddit('askscience')
for thread in subreddit.search(query="COVID"):
    sql = "INSERT INTO threads VALUES (?, ?, ?, ?, ?, ?, ?, ?);"
    cur.execute(sql, (thread.id, thread.title, thread.selftext, thread.author.name, 
                      thread.url, thread.created_utc, thread.num_comments, thread.score))


# In[26]:


query_sql = "SELECT * FROM threads;"


# In[48]:


cur.execute(query_sql)
for record in cur:
    print(record)


# In[27]:


# Twitter Program
get_ipython().system('pip install tweepy')

# http://docs.tweepy.org/en/latest/


# In[28]:


# Twitter API
# https://developer.twitter.com/en/apps

import tweepy

consumer_key = "BvDW5NjpoPxPzDX9oVVylggl2"
consumer_secret = "ieVAOrInPDLRUt0WxJf2UTcgn2QfQ6coS4IXyd39tagteMDQ8r"
access_token = "3023066384-qReOIlcWSmQB2NHsBc0YDsok4pSUJy5HVKCdVL6"
access_token_secret = "HbKDpG1nslqUP9NJF6D6AdKCntQ1BtpTsrXIFr6xyBROV"

auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)

api = tweepy.API(auth)


# In[30]:


for tweet in tweepy.Cursor(api.search,
                          q="coronavirus",
                          rpp=100,
                          result_type="recent",
                          include_entities=True,
                          lang="en").items(100):


    print(tweet.text)
    print(tweet.user.name)
    print("\n\n")
    
    


# In[53]:


tweet_list = []
user_list = []

for tweet in tweepy.Cursor(api.search,
                          q="coronavirus",
                          rpp=100,
                          result_type="recent",
                          include_entities=True,
                          lang="en").items(100):


    tweet_list.append(tweet.text)
    user_list.append(tweet.user.name)


# In[55]:


import csv


# In[61]:


# encoding problems

with open(r"C:\Users\zhany\OneDrive\Desktop\tweet4.csv", "a") as csvfile:
    csvWriter = csv.writer(csvfile, delimiter=' ', quotechar='|', quoting=csv.QUOTE_MINIMAL)
    for i in range(len(tweet_list)):
        output = [tweet_list[i], user_list[i]]
        csvWriter.writerow(output)


# In[63]:


with open(r"C:\Users\zhany\OneDrive\Desktop\tweet4.csv", "a", encoding="utf-8") as csvfile:
    csvWriter = csv.writer(csvfile, delimiter=' ', quotechar='|', quoting=csv.QUOTE_MINIMAL)
    for i in range(len(tweet_list)):
        output = [tweet_list[i], user_list[i]]
        csvWriter.writerow(output)
        
    

# with open(r"C:\Users\zhany\OneDrive\Desktop\tweet4.csv", "r", encoding="utf-8") as csvfile:
#     csvReader = csv.Reader(...)

    


# In[62]:


import locale
# Default encoding
locale.getpreferredencoding()
