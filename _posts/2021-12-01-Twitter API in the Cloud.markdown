---
layout: post
title: Analysing tweets from Twitter API using AWS EC2 and Google Clound SQL connection
date: 2021-12-01 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: twitter_granica.png # Add image post (optional)
tags: [pyspark, python] # add tag
---


## 1. Context
The project goal was to extract data from Twitter API and transfer it to MySql database for further data analysis. 
The Twitter API allows to stream public Tweets from the platform in real-time so that basic metrics about them can be obtained.

The data was created from tweets traffic, which contains two hashtags: 'granica', 'białoruś'. Those hashtags were popular during Poland border crisis 
in Q4 of 2021. Data was collected on 01.12.2021 and cosist of 3000 tweets. Code was written using python programming langugage.

## 2. Project architecture

<span>
    <img src="/assets/img/architecture.png" style="width:80%">
</span>

Connecting to Google Cloud SQL Platform:
```
def store_data(column_name):
    mydb = mysql.connector.connect(host='00.000.000.000', port=0000, \
        user='user_name',passwd='',db='db_name', charset='utf8mb4')
    cursor = mydb.cursor()
    table_name = 'table_name'
    insert_query = f"INSERT INTO {table_name} (column_name) VALUES (%s)"
    cursor.execute(insert_query, (id, column_name))
    mydb.commit()
    cursor.close()
    mydb.close()
    return

```
Connecting with Twitter API (part of code)
```
class TwitterClient():
    """
    Class for connecting with twitter API.
    """

    def __init__(self, twitter_user=None):
        self.auth = TwitterAuthenticator().authenticate_twitter_app()
        self.twitter_client = API(self.auth)

        self.twitter_user = twitter_user

    def get_twitter_client_api(self):
        return self.twitter_client

class TwitterAuthenticator():
    """
    Class for API authorisation.
    """
    def authenticate_twitter_app(self):
        auth = OAuthHandler(CONSUMER_KEY,
                            CONSUMER_SECRET)
        auth.set_access_token(ACCESS_TOKEN,
                              ACCESS_TOKEN_SECRET)
        return auth

class TwitterStreamer():
    """
    Class for streaming and processing live tweets.
    """

    def __init__(self):
        self.twitter_authenticator = TwitterAuthenticator()

    def stream_tweets(self, fetched_tweets_filename,):  
        listener = TwitterListener(fetched_tweets_filename,)
        auth = self.twitter_authenticator.authenticate_twitter_app()
        stream = Stream(auth, listener)
        stream.filter(track=['granica', 'bialorus'])

```



## 3. Cloud usage monitoring

Cloud monitoring is important process of reviewing and managing the operational workflow and processes within a cloud infrastructure or asset.
Additionaly due to payment methods 'pay for what you use', it is crucial to control cloud infrastructure, that is why AWS E2 and Google Cloud
are providing with following monitoring tools:


### AWS EC2 usage monitoring
<span>
    <img src="/assets/img/aws_cloud.PNG" style="width:80%">
</span>

### Google Cloud Platform SQL usage monitoring
<span>
    <img src="/assets/img/gcp_sql.PNG" style="width:80%">
</span>




## 4. Data analysis
Data cleaning and data manipulation were performed using Pandas framework.

```
df['u_device'] = df['source'].apply(lambda s: s[s.find('>')+1:s.find('</a>')])
ax = (df.u_device.value_counts()/len(df)*100).iloc[:5].plot(kind="bar", rot=0)
ax.set_yticks(np.arange(0, 60, 10));
```
<span>
    <img src="/assets/img/device.png" style="width:80%">
</span>

The most surprising thing from the above graph is that nearly 40% of tweets that particular day was posted using desktop.


```
df['u_created_at'] = df['u_created_at'].apply(lambda x: datetime.strptime(x, '%a %b %d %X %z %Y'))
df['u_created_at_year'] = pd.DatetimeIndex(df['u_created_at']).year
ax = sns.barplot(x="u_created_at_year", y="u_created_at_year", data=df, estimator=lambda x: len(x) / len(df) * 100)
```
<span>
    <img src="/assets/img/join_year.png" style="width:80%" >
</span>

The graph shown above indicates that the most active authors made account within years 2020-2021.

```
df['created_at_hour'] = df['created_at'].apply(lambda x: datetime.strptime(x, '%Y-%m-%d %H:%M:%S')).dt.hour
ax = sns.barplot(x='created_at_hour', y='created_at_hour', data=df, estimator=lambda x: len(x) / len(df) * 100)
```
<span>
    <img src="/assets/img/post_date.png" style="width:80%">
</span>

The graph of daily rhytm of twitter activity shows that significant part of tweets were made during working hours. It may mean that
this particular Polish Border topic is so engaging, that people decides to tweet even from work.
