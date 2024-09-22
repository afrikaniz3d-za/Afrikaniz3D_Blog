---
title: Lobbyists 4 America
author: Ntobeko Sosibo [ Afrikaniz3D ]
date: '2024-08-04'
slug: lobbyists-4-america
categories:
  - project
tags:
  - analytics
  - sql
  - data science
  - sentiment analysis
  - a/b testing
  - etl
  - cte
  - coursera
  - uc davis
  - mode analytics
  - databricks
  - python
  
draft: no
image: 'img/portfolio/W12/w12000.webp'
showonlyimage: yes
---
  
## 1. Project Overview

The following presentation is geared at anyone interested at taking a short look into how a political-twitter dataset is used to inform a general strategy for the client, **Lobbyists4America**, to incrementally improve the presence and shareability of their message on [Twitter](https://twitter.com).  
  
## 2.  The Audience for this project
As part of a [capstone project](https://www.coursera.org/learn/sql-data-science-capstone/home/info), this presentation is directed at an **external audience** interested in seeing what steps were taken to extract the necessary insights from the supplied dataset to formulate data-driven frameworks that inform messaging strategies using machine learning (ML) and other forms of analysis.  
  
## 3. Thoughts going in  
####  3.1 Assumptions  
The framing of the brief initially led me to believe I'd be looking at "typically-political" entries, lots of name-calling, championing for the rights of the people, name-calling etc.  
  
I was also expecting a relatively balanced representation of Republicans vs Democrats - speaking directly to one another, pointing out faults and shortcomings.  
  
All-in-all, theatre.  
  
####  3.2 Hypotheses  
Given that the platform of the chosen discourse is Twitter, engagement (mostly in the form of likes and retweets) would be a really good indicator to discern how well the message is being delivered. The following are my main hypotheses:  

##### 3.2.1 The more engagement, the more effective the tweet/user is at getting the message across  
For the purposes of this project, engagement is used to refer to mainly likes and retweets   

##### 3.2.2 Negativity drives conversation   
Here I want to look at the tone/slant of the users that get the most engagement is usually negative.  
  
## 4.  Bringing the data into Databricks  

There are two tables, name 'tweets' and 'users'. They are both in JSON format and are downloaded off-site. Because I decided to use Databricks rather than the Jupyter notebook on Coursera's Sandbox platform, I had to take slightly different routes to perform the same actions shown in the course literature.  
  
After uploading the two files to the platform's DBFS I executed the following cells to create the tables, making them usable for the exploration:  
  
#### 4.1 Importing the tweets JSON file from the DBFS  
  
```[r]
file_location = "/FileStore/tables/tweets-30.json"
file_type = "json"

infer_schema = "false"
first_row_is_header = "false"
delimiter = ","

df = spark.read.format(file_type) \
  .option("inferSchema", infer_schema) \
  .option("header", first_row_is_header) \
  .option("sep", delimiter) \
  .load(file_location)

df_limit = df.limit(5)
display(df_limit)  
```  
  
#### 4.2 Making tweets a permanent table  
  
```[r]
permanent_table_name = "tweets"

path = "dbfs:/user/hive/warehouse/tweets_20240502"
df.write.format("parquet").mode("overwrite").option("path", path).saveAsTable(permanent_table_name)  
```  

#### 4.3 Importing the users JSON file from the DBFS  
  
```[r]  
file_location = "/FileStore/tables/users-30.json"
file_type = "json"

infer_schema = "false"
first_row_is_header = "false"
delimiter = ","

df = spark.read.format(file_type) \
  .option("inferSchema", infer_schema) \
  .option("header", first_row_is_header) \
  .option("sep", delimiter) \
  .load(file_location)

df_limit = df.limit(5)
display(df_limit)  
```  
  
####  4.4 Making users a permanent table  
  
```[r]  
permanent_table_name = "users"
path = "dbfs:/user/hive/warehouse/users_20240502"
df.write.format("parquet").mode("overwrite").option("path", path).saveAsTable(permanent_table_name)  
```  
  
With the above four (4) cells executed, the tables are now usable in any notebook on a newly-created cluster in Community edition of Databricks without having to re-upload them every time I log in. The next thing to do is to take a look at the data.  
  
## 5. First Look  
  
#### 5.1 Looking at the tweets table  
  
![][1]  
  
#### 5.2  Looking at the users table  
  
![][2]  
  
#### 5.3 Descriptive Stats

To make sure I was able to keep the size of the notebook below 10.4MB, I have omitted the queries and simply listed the resultant figures. I've you'd like to take a look at the calculations feel free to contact me.  
  
#####  5.3.1 The tweets table  
**Tweet Counts**:  
    • Mean: 				2 281 tweets  
    • Mode: 				3 258 tweets (RepDonBeyer)  
    • Range: 				min 4 (GregHarper), max 3 258 (RepDonBeyer) tweets  
  
  
**Tweet Length**:  
    • Mean: 				120 characters  
    • Median: 			132 characters  
    • Mode: 				140 characters  
    • Range: 				min 0, max 415 characters  
  
  
**Retweets**:  
    • Mean: 				190 retweets  
    • Median: 			4 retweets  
    • Mode: 				1 retweet  
    • Range: 				min 0, max 3 637 896 retweets  
  
  
**Favourites**:  
    • Mean: 				200 favourites  
    • Median: 			2 favourites  
    • Mode: 				0 favourites  
    • Range: 				min 0, max 984 832 favourites  
  
**Conclusion**  
The tweets table contains a total of 1 243 370 tweets of an average length of 120 characters. Tweets received an average of 190 retweets - the lowest being 0 and the most retweeted tweet receiving 3 637 896 retweets. Tweets were favourited an average of 200 times, with the highest receiving 984 832 favourites.  
  
##### 5.3.2 The users table  
**Description Length**:  
    • Mean: 				99 characters  
    • Median: 				102 characters  
    • Mode: 				160 characters    
    • Range: 				min 0, max 160 characters    
  
  
**Favourites**: (referring to how many tweets the user has favourited/liked)   
    • Mean: 				413 favourites  
    • Median: 			120 favourites  
    • Mode: 				0 favourites  
    • Range: 				min , max 12 507 favourites  
  
  
**Followers**:  
    • Mean: 				163 433 followers  
    • Median: 			16 690 followers  
    • Mode: 				5 100 followers  
    • Range: 				min 4, max 31 712 585 followers  
  
  
**Friends**:  
    • Mean: 				2033 friends  
    • Median: 			748 friends  
    • Mode: 				4 friends  
    • Range: 				min 0, max 92 934 friends  
      
      
**Listed**:  
    • Mean: 				1340 lists  
    • Median: 			749 lists  
    • Mode: 				498 lists  
    • Range: 				min 0, max 70 660 lists  
  
  
**Statuses**:  
    • Mean: 				3658 statuses  
    • Median: 			2682 statuses  
    • Mode: 				0 statuses  
    • Range: 				min 0, max 59 535 statuses  
  
**Conclusion**  
The users table contains the information pertaining to the 545 (or 548*) users that generated the posts from the tweets table. Although there a number of variables that can not be aggregated due to being things like default-formatted links or colour hexcodes, the table still contained information like each user having an average description length of 99 characters, had between 0 and 12 507 favourites – averaging 413 per user, between 4 and 31 712 585 followers, 0 and 92 934 friends, listed between 0 and 70 660 times, and had between 0 and 59 535 statuses, averaging 3658 per user.  
  
####  5.4 What stood out  
Apart from the columns that the dataset could have done without (discussed in the next section) I found some other details that I think anyone working with this dataset needs to be mindful of.  
  

##### 5.4.1 Possible Collector Bias  
The tweets are collected through an account that has engaged with some of the posts and follows some of its users. The only concern here is in the event bias is seriously explored.  
  
![][3]  
  
##### 5.4.2 Three Mystery Users  
Prior to looking at the descriptive stats I wanted to make sure that some of the figures in the tweets table correlated with the data in the users table.  
  
![][4]  
  
![][5]  
  
Yvette Clarke, Rob Bishop and Tiberius Press were the 3 missing users which I discovered by performing a **JOIN** on the **screen_name**, looking for where the join was unsuccessful (NULL).  
  
![][6]  
  
![][7]  
  
![][8]  
  
![][9]  
  
I eventually found that Rep. Bob Bishop wasn't an active user on Twitter, but had a [profile](https://twitter.com/RepRobBishop) probably for the purposes of co-signing other users. I also think something similar was done for Yvette Clarke and Tiberius (@lordtiberius), although looking at some of the tweets they are mentioned in I could also assume that their accounts/tweets were removed for one reason or another.  
  
##### 5.4.3 (potential) Censorship  
Something I took subconscious note of at the start of the exploration was the **possibly_sensitive** column. It became clear that these tweets would often discuss things like violence or death, so I imagine this marker was used to affect the ranking/visibility of the tweet to protect sensitive viewers.  
  
Below, I've pulled some tweets where they were marked as possibly sensitive and the results should raise concerns.  
  
![][10]  
  
Some of these tweets are not only harmless, but are similar to other tweets that have not been flagged.  
  
![][11]  
  
It's clear Rep. Norma J Torres has had her account blanket flagged for possibly sensitive content, which could affect her reach.  
  
The lesson here is to keep in-mind that whether it's done by algorithm or manually by an engineer, you may run into periods where a tweet or two could jeopardise your reach without notifying you, so stay aware of things like dips in engagement and keep a friendly relationship with the Twitter support team.  
  
##### 5.4.4 Poliglot*  
  
While looking at the descriptive stats I started paying attention to columns I initially thought wouldn't be useful - "***If they were 99% NULL, what can be said about the rows that aren't empty***?"  
  
Something I hadn't considered going into the project was the importance of making the tweet accessible in other languages.  
  
I think this is an important datapoint to keep in mind as simply relying on built-in translator tools runs the risk of a loss of impact or unintended inflammation. Becoming a regular translator shows an additional layer of consideration for a multi-cultural audience.  
  
![][12]  
  
##### 5.4.5 Politicing the algorithm isn't the only way to get your message pushed  
The chicken nugget meme that involved reposting a user’s campaign to reach a target number of likes was non-political, but generated a lot of activity – which could be used to boost primary messaging without falling into the trap of relying on drama/scandal to get attention because it generally has wider appeal.  
  
![][13]  
  
What's most interesting about this result is that the most viral tweet (by shareability) does not receive any likes, meaning, by more recent discussion regarding how the algorithm works, that the tweet relies solely on user-driven push to get eyes on it.  

[Buffer](https://buffer.com/resources/successful-tweets/) conducted a study of their best tweets to better understand why those tweets did so well. Key elements I would cite as being the reasons for why the nugget tweet was so successful were:  
  
  - *It is entertaining*  
  - *Relatable content*  
  - *Call to action*  
  - *Asking for engagement*  
  
Other element elements identified in the study will inform the final recommendations at the end of this exercise.  
  
##  6.  ETL  
Before I could use the data, I first needed to go through it and make sure that it was formatted accordingly.  
  
#### 6.1 Transforming the data  
  
##### 6.1.1 DATETIME variable  
The timestamp column (created_at) is in the Unix format. To transform it into a readable format I used the query below to split it into a ***date*** and ***time*** column:  
  
![][14]  
  
#####  6.1.2 Tweet Length  
The **display_text_range** variable is in the array format. To make it easier to read, and be able to calculate figures for the descriptive statistics, I transformed it to a regular integer using the following:  
  
![][15]  
  
Most of the other transformations were renaming to use terms we are more familiar with today - "likes" instead of "favorites", "screen_name" to "user", "text" to "tweet" etc.  

Something else that I found was that the ordering of the columns often found me scrolling past many columns to headings I used often. In situations where I knew this table wouldn't be updated (such as this project) I would do all necessary ETL and export it as a new easier-to-use table for the project's duration, but I decided to leave it as-is to better practice working in scenarios where I'm not the only user of the table, as well as minimising instances where something undesirable could happen.  
  
#### 6.2    Are all the columns necessary for the exercise?
There was no metadata/ background data that came with the dataset, I slowly started piecing a few things together during my initial look-through.  
The first thing that stood out was the high number of variables/columns, especially how many of their rows had NULL values.  

I also put forward that some columns indicate that the dataset relates to the account used to mine the tweets. By this I am referring to the columns **favorited** - has the user also liked the tweet?, and **following** - is the owner of the account following the listed user?, to name a few.  
  
**tweets table**  
•      created_at  
•      id  
•      in_reply_to_screen_name  
•      in_reply_to_status_id_string  
•      in_reply_to_user_id_str  
•      is_quoted_status  
•      quoted_status_id_str  
•      retweeted  
•      retweet_count  
•      screen_name  
•      text  
•      user_id  
  
**users table**  
•      created_at  
•      description  
•      entities  
•      favourites_count  
•      followers_count  
•      friends_count  
•      id_str  
•      listed_count  
•      name  
•      screen_name  
•      statuses_count  
  
####  6.3 Transformed dataframe  
Below is a typical example of the tweets table transformed to reflect a readable and format of the records that also uses more-familiar terms.  
  
![][16]  
  
##  7. Text Data Analysis    
  
I employed the text blob, or word cloud, to parse out the most commonly-used words and terms to get a sense for what topics defined the overall discourse.  
  
An element of this tool that needed the most attention was the refinement of the lexicon that was used to filter-out ***stop-words***.  
  
Something else I need to point out was that I didn't change the case of all the tweets to lower-case as I think it would make it easier to identify acronyms and other kinds of shorthand.  
  
####    7.1 Top Issues Discussed (keyword frequency)  
 •   Congress  
 •   Healthcare  
 •   President  
 •   Obama  
 •   Americans  
 •   Families  
 •   Women  
 •   Jobs  
 •   Tax  
 •   GOP  
 •   Trump  
  
#####  7.1.1 Word Cloud of Top Issues Overall  
  
![][17]  
  
![][18]  
  
####    7.2 Top Topics/Tags by Year  
  
**2008** --   #pork  
**2009** --   #tcot, #sctweets, #masen, #TCOT, #healthcare  
**2010** --   #tcot, #gop, #hcr, #sctweets, #ncsen, #ncgop  
**2011** --   #tcot, #GOP, #jobs, #tn03, #sctweets, #va01, #p2  
**2012** --   #tcot, #jobs, #mDayton, #NEPA, #GOP  
**2013** --   #Obamacare, #IRS, #immigration, #mepolitics, #Syria, #Benghazi, #SmallBiz, #ACA  
**2014** --   #tcot, #SOTU, #IRS, #mepolitics, #IASEN, #immigration, #RenewUI  
**2015** --   #SOTU, #mepolitics, #IranDeal, #Obamacare  
**2016** --   #SCOTUS, #NoBillNoBreak, #Zika  
  
##### 7.2.1 Top Topics/Tags per Year  
  
![][19]  
  
![][20]  
  
![][21]  
  
![][22]  
  
![][23]  
  
![][24]  
  
![][25]  
  
![][26]  
  
![][27]  
  
![][28]  
  
**#tcot** is a hashtag that started appearing often towards the end of 2008 as an acronym for "*Top Conservatives of Twitter*". I'd [read](https://www.ibtimes.com/what-does-tcot-mean-about-tcot-hashtag-top-conservatives-use-twitter-1109812) that it was used as a call sign of-sorts for conservative users to easily find tweets from like-minded users on the platform. Given that the party is also referred to with **#GOP**, the is a large imbalance when compared to the volume of democrat-identifying tags. The democrats do, though, have their own tag in the form of **#p2** (for the progressives) which begins featuring in 2011, 2014, but never in a magnitude comparable to #tcot.  
  
####  7.3 Top voices and conversations in #Healthcare  
  
##### 7.3.1 Ranked #Healthcare Tweets  
  
![][29]  
  
![][30]  
  
![][31]  
  
![][32]  
  
####  7.4 Top voices and conversations in #Jobs  
  
##### 7.4.1 Ranked #Jobs Tweets  
  
![][33]  
  
![][34]  
  
![][35]  
  
##  8. Sentiment Analysis  

After identifying ***healthcare*** and ***jobs*** as being the most frequently discussed topics using textual analysis (word cloud), to identify users (overall) that represent a more optimistic outlook when engaging on the platform.  
  
####  8.1 Preparing the Dataframe for the Analysis  
  
##### 8.1.1 Creating the Tweets Dataframe  
  
![][36]  
  
##### 8.1.2 Performing the Sentiment Analysis on the Tweets Table  
  
![][37]  
  
![][38]  
  
![][39]  
  
![][40]  
  
##  9. Shareability Metrics  
Given that this exercise takes place on a platform that encourages you to create content that is effective at being highly shareable, I wanted to look more into the likes and retweet and their measured impact past the universally-accepted "more is better". This led me to pose the question: "**Which is better for ensuring that more people get to see your message, more likes or more retweets?**"  
  
An [article](https://genflow.com/blogs/how-the-twitter-algorithm-works-in-2023/) by Molly Rees on [Genflow](https://genflow.com/) shared some surprising nuggets of information when it came to changes in the algorithm (in 2023) that are worth looking into in general.  
  
When it came to likes and retweets it Rees says **likes are the most important**. If your post has more likes than retweets or replies the algorithm will boost the ranking of the tweet by **30x**, compared to the **20x** boost it would have gotten if it had more retweets.  
  
####  9.1 What I was looking for  
  
Given what I had learned above, I now have an idea of what to look at to measure what I consider a good example of a tweet worth emulating. Thesequalities are:  
- **The tweet has more likes than retweets and replies/quotes, regardless of the volume**. This is so that it at least will be seen as a good postto be pushed by the algorithm.
- **Sentiment analysis value between 0 and 1 - 0 being neutral, and 1 being positive**. This ensures that you're also only looking at techniques of injecting more positivity into the discourse, regardless of the nature of the subject matter.  
  
##### 9.1.1 Generating an ETL'd base table  
I repeated the steps of generating a table with the most useful columns, including the sentiment column, then saving it as it's own table to use in the next step.  
  
![][41]  
  
##  10. Recommendations using this table  
The resultant table does something I had not anticipated at the start - and that's provide a further opportunity introduce a change in tone by adjusting the range values for sentiment whilst keeping the tweet algo-push-friendly.  
  
  It may seem tedious at the start, but running tweet drafts through this set-up will go a long way towards guiding the user in using the right language and, over time, you might not need to refer to it once it has set in.  
  
##  11. Final Thoughts  
This has been an informative exercise in many ways. Aside from learning to work with a large dataset, I explored qualitative data with machine learning. It was also interesting to look at this older data through an algorithm-centric lens in 2024 in the sense that it has changed so much (and continues to do so), knowing how much more important it has become to aware of changes in the algorithm, [shadow banning](https://en.wikipedia.org/wiki/Shadow_banning#:~:text=Shadow%20banning%2C%20also%20called%20stealth,whether%20the%20action%20is%20taken), and other forms of censorship's impact on the participant's experience (and reach) in the social media space.  
  
Something else I feel is important to always keep in mind when having any kind of discussion about social media figures is the phenomenon of [botting](https://www.cloudflare.com/learning/bots/what-is-a-social-media-bot#:~:text=Broadly%20speaking%2C%20social%20media%20bots,designed%20to%20mimic%20human%20users.), particularly on Twitter, and even more particularly in the political niche. It's a [nuanced conversation](https://blog.x.com/common-thread/en/topics/stories2021/four-truths-about-bots) that I think should be kept in-mind when looking to use user-generated content to make data-driven decisions in a space where authenticity cannot be guaranteed 100%.  
    
Lastly, I didn't even look at using the users table for this, but another good exercise would be to then isolate the top voices and match the contents/formatting of their user profiles in terms of fullness, types of links provided, as well as the quality of all graphics used. This makes me think back to the first episode of the sixth season of [House of Cards](https://www.imdb.com/title/tt7538918/) where imaging software is used to morph Claire's facial features to look a little more like Frank's to garner more support against her rivals.

((**(double table thump)**))  

  





[1]: /img/portfolio/W12/w12001.png
[2]: /img/portfolio/W12/w12002.png
[3]: /img/portfolio/W12/w12003.png
[4]: /img/portfolio/W12/w12004.png
[5]: /img/portfolio/W12/w12005.png
[6]: /img/portfolio/W12/w12006.png
[7]: /img/portfolio/W12/w12007.png
[8]: /img/portfolio/W12/w12008.png
[9]: /img/portfolio/W12/w12009.png
[10]: /img/portfolio/W12/w12010.png
[11]: /img/portfolio/W12/w12011.png
[12]: /img/portfolio/W12/w12012.png
[13]: /img/portfolio/W12/w12013.png
[14]: /img/portfolio/W12/w12014.png
[15]: /img/portfolio/W12/w12015.png
[16]: /img/portfolio/W12/w12016.png
[17]: /img/portfolio/W12/w12017.png
[18]: /img/portfolio/W12/w12018.png
[19]: /img/portfolio/W12/w12019.png
[20]: /img/portfolio/W12/w12020.png
[21]: /img/portfolio/W12/w12021.png
[22]: /img/portfolio/W12/w12022.png
[23]: /img/portfolio/W12/w12023.png
[24]: /img/portfolio/W12/w12024.png
[25]: /img/portfolio/W12/w12025.png
[26]: /img/portfolio/W12/w12026.png
[27]: /img/portfolio/W12/w12027.png
[28]: /img/portfolio/W12/w12028.png
[29]: /img/portfolio/W12/w12029.png
[30]: /img/portfolio/W12/w12030.png
[31]: /img/portfolio/W12/w12031.png
[32]: /img/portfolio/W12/w12032.png
[33]: /img/portfolio/W12/w12033.png
[34]: /img/portfolio/W12/w12034.png
[35]: /img/portfolio/W12/w12035.png
[36]: /img/portfolio/W12/w12036.png
[37]: /img/portfolio/W12/w12037.png
[38]: /img/portfolio/W12/w12038.png
[39]: /img/portfolio/W12/w12039.png
[40]: /img/portfolio/W12/w12040.png
[41]: /img/portfolio/W12/w12041.png
