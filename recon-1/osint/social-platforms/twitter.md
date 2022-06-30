# Twitter

## Tips & Tricks

### **Geolocation**

```
# Search using geocode
geocode:36.1143855,-115.1727518,1km

# Tweet geolocation
https://onemilliontweetmap.com
```

### **Downloader**

```
# Twitter video downloader
https://twdown.net/
```

### **Account Analysis**

```
# Tweeter account analysis (account needed)
https://tweetreach.com/

# When was created an account
http://twbirthday.com/haaxmax/

# Sleeping time (account needed)
http://sleepingtime.org/

# Simple analysis/analytics
https://socialbearing.com/search/general/sodifrance
https://foller.me

# See all tweets from one account (account needed)
https://www.allmytweets.net/connect/

# Tool to see what some accounts have in common
https://tweepdiff.com/

# Another account analysis service
https://accountanalysis.app/
```

### **Data visualization**

```
# Free tool to build graph and visualize the spread of claims and fact checking
https://hoaxy.iuni.iu.edu/
```

### **Tracking Accounts & Modifications**

```
# With spoonbill, you can check and record people accounts modification
# It checks accounts of people you're following
https://spoonbill.io/

# Find stalker (checks who's following and who's followed) and intersect them
https://tweepsect.com/

# You can triangulate accounts by looking who's following x+x or who y+y follows
http://twiangulate.com
https://doesfollow.com/
```

### Export and Archive

```
# Export and Archive Threads
# https://jakecreps.com/2020/04/10/threadreaderapp-osint-archive-twitter/amp/

# This twitter bot allows to archive content
https://twitter.com/threadreaderapp
@threadreaderapp unroll

# Method 2
# You can also use the service engine to look for persons or threads
# It can capture deleted content
https://threadreaderapp.com/
https://threadreaderapp.com/user/fs0c131y

# Method 3
# Twitter Advanded Search
# "@threadreaderapp unroll"

# You can directly download videos from the archived content
# Cannot directly from Twitter

# You can use Wayback Machine to archive the archive ;)
# In the case they choose the remove it
```

### Tweet Analyzer ([https://github.com/x0rz/tweets\_analyzer](https://github.com/x0rz/tweets\_analyzer))

```
# The goal of this simple python script is to analyze a Twitter profile through its tweets by detecting:
#    Average tweet activity, by hour and by day of the week
#    Timezone and language set for the Twitter interface
#    Sources used (mobile application, web browser, ...)
#    Geolocations
#    Most used hashtags, most retweeted users and most mentioned users
#    Friends analysis based on most frequent timezones/languages

# Basic
python tweets_analyzer.py -n <@username>
```

### Twitter Intelligence ([https://github.com/batuhaniskr/twitter-intelligence](https://github.com/batuhaniskr/twitter-intelligence))

```
# Another tool used to analyze twitter accounts
# SQLite database is used

# Search by username
python3 tracking.py --username "HaberSau" 

# Search by query
python3 tracking.py --query "sakarya" 
 
# Search in a time range
python3 tracking.py --username "HaberSau" --since 2015-09-10 --until 2015-09-12 --maxtweets 10 
 
# Get location (slower)
python3 tracking.py --query "sakarya" --location "True"

# Location analysis (you need to get Google API Key in settings.py)
python3 analysis py --location
```

### Twitter Back From The Death

```
# This tool checks an user account history to get tweets
# It then parses tweets to extract all URL links and then
# Checks if the domain is available for registration
https://github.com/misterch0c/twitterBFTD
```
