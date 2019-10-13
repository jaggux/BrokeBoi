# Welcome to BrokeBoi!

We get it, you're broke. But you wanna look fresh. Give us your number and we will text you cheap deals. 

We used the Twilio SMS API, Reddit Wrapper, Flask and Cloud functions using Google Cloud.


# Setup

Make a developer account on Twilio, Reddit, and Google Cloud. 

Create a Cloud function with the following code:
```python
import pandas as pd
import numpy as np
from twilio.rest import Client
from flask import request, redirect 
import json
import praw


def send_text(txt,num):
	# Add your Twilio account information here
    account_sid = ''
    auth_token = ''
    client = Client(account_sid, auth_token)
    message = client.messages \
                .create(
                     body=txt
                     # Put Twilio phone number here
                     from_='+___',
                     to=num
                 )

reddit = praw.Reddit(client_id='',
                     client_secret='',
                     password='',
                     user_agent='testscript by __',
                     username='')
def get_deals(request):
    """Responds to any HTTP request.
    Args:
        request (flask.Request): HTTP request object.
    Returns:
        The response text or any set of values that can be turned into a
        Response object using
        `make_response <http://flask.pocoo.org/docs/1.0/api/#flask.Flask.make_response>`.
    """

    df = pd.DataFrame(columns=['title','url','created_utc','num_comments','score','F'])
    result_list = []
    for i, submission in enumerate(reddit.subreddit('frugalmalefashion').hot(limit=30)):
        diction_dict = {
            'title': submission.title,
            'url': submission.url,
            'created_utc':submission.created_utc,
            'num_comments':submission.num_comments,
            'score': submission.score
        }
        result_list.append(diction_dict)
        
    df = pd.DataFrame(result_list)
    df = df[df['title'].str.contains("$|%")]
    result = []
    name = str(request.args.get('name'))
    result.append(name)
    number = str(request.args.get('tel'))
    result.append(number)
    brands = str(request.args.get('brands'))
    result.append(brands)
    num = '+1' + str(number)
    df = df[df["title"].str.contains(brands, case=False)]   

    if(df.empty is False):
        title = df.iloc[0]['title']
        url = df.iloc[0]['url']
        txt = "yo we found a deal for " + title + " check this out " + url
        send_text(txt,num)
    else:
        txt = "sry fam no deals rn txt u l8r"
        send_text(txt,num)
    
    """Replace link with hosted link of html"""
    return redirect("https://anmolbajajnet.github.io/BrokeBoi/", code=302)
```

Add the following code to your requirements.txt in your Google Cloud function:
```
# Function dependencies, for example:
# package>=version
pandas
numpy
twilio
praw
```
