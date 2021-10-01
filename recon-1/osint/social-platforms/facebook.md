# Facebook



#### Tips & Tricks <a id="tips-tricks"></a>

```text
# Stalking and Information Gathering
https://stalkscan.com/

# Facebook video downloader
https://fbdown.net/

# Find Facebook ID
https://findmyfbid.com
https://lookup-id.com/

# Facebook tool for multi searches
https://whopostedwhat.com/

# You can search for Facebook videos indexed on Google
"topic" site:facebok.com!/*/videos

# Source + script to do that
https://twitter.com/lorenzoromani/status/1301843624230035456?s=19
https://github.com/lorenzoromani1983/facebookTranscripts/blob/master/facebookTranscripts.py

# DumpItBlue Extension
# Facebook tool box
https://le-tools.com/DumpItBlueExtensionDoc.html
```

#### Facebook Tricks <a id="facebook-tricks"></a>

```text
# You can search by e-mail

# You can find accounts by e-mail with the recover function
https://www.facebook.com/login/identify?ctx=recover
https://www.facebook.com/login/identify

# Search photo by ID
https://www.facebook.com/photo.php?fbid=PHOTO-ID-HERE

# You can search for car using plate number
# For example ST-597-NS will give you car photos identified with relating plate number

# You can search by text on Google for indexed videos
Example: "hacking" site:http://facebook.com/*/videos.

# You can also search for unique identifier
# Even if they're not tagued
https://twitter.com/quiztime/status/1339544098529832969
```

#### Facebook Sleep Stats <a id="facebook-sleep-stats"></a>

```text
# A small tool to show the potential privacy implications modern social media have. 
# By tracking online/offline status of people on Facebook, it is possible to get an accurate image of their sleep pattern.
https://github.com/sqren/fb-sleep-stats

# Two process are running simultaniously : scrapper and webserver
# Scraping
npm run scrape

# Server (localhost:3000)
npm start

# Ad blocker must be disabled
# When the scrapper is off, it stops tracking
```

#### Ultimate Facebook Scraper <a id="ultimate-facebook-scraper"></a>

```text
# Python automated script used to scrape and parse data from a Facebook account
# You need to provide your credentials (dedicated account) and targets as input in "input.txt".

# Limits : 
# - If the profile target is configured and not in your friends, you may see nothing
# - If you request too much profiles, Facebook can block requests, resulting in crash
# - If target profiles are big, can be very long an resources hungry

# Results are stored in txt format, easily grepable by dates for examples.
```

#### Facebook Graph Search <a id="facebook-graph-search"></a>

```text
# Graph Search is a Facebook internal search engine used to get precise results when searching
# Facebook need to be in English version to activate the Graph Search

# On June 2019, Facebook shut down its Graph Search options.
# The ‘new’ Graph Search seems a little comprehensive at first
# but it’s pretty easy once you get the hang of it. It involves some JSON and Base64.
```

**The New Graph Search Methods**

```text
# Resources
https://osintcurio.us/2019/08/22/the-new-facebook-graph-search-part-1/
https://osintcurio.us/2019/08/22/the-new-facebook-graph-search-part-2/

# Tools
https://graph.tips/beta/
https://intelx.io/tools?tab=facebook
```

```text
# Identifiers are always required to performs queries
# CTRL+U on page, profile, group...
# CTFL+F 
# "entity_id" = Profile 
# "page_id" = Page
# "group_id" = Group

# EVERY Facebook requests start like this
facebook.com/search/top/?q=people&epa=FILTERS&filters=

# top/  Search top content
# posts/    Search public posts
# people/   Search for people
# photos/   Search for photos
# videos/   Search for videos
# pages/    Search for pages
# places/   Search for places

# And then the content of the query
facebook.com/search/posts/?q=baseball&epa=FILTERS&filters=

# It uses then JSON and BASE64 to apply filters
# For example, the JSON string for "most recent" is
{“rp_chrono_sort”:”{\”name\”:\”chronosort\”,\”args\”:\”\”}”}

# Then base64 encode it
facebook.com/search/posts/?q=baseball&epa=FILTERS&filters=<BASE64>

# You can also perform combined queries
# Example : From one city and one employer
{“city”:”{\”name\”:\”users_location\”,\”args\”:\”108212625870265\”}”}
{“employer”:”{\”name\”:\”users_employer\”,\”args\”:\”104958162837\”}”}
# Result
{“city”:”{\”name\”:\”users_location\”,\”args\”:\”108212625870265\”}”,”employer”:”{\”name\”:\”users_employer\”,\”args\”:\”104958162837\”}”}

# Think about testing queries
https://jsonformatter.curiousconcept.com/

# And then concat with a classic query
facebook.com/search/posts/?q=baseball&epa=FILTERS&filters=<BASE64>

# Some queries
# Most recent populair content
{“rp_chrono_sort”:”{\”name\”:\”chronosort\”,\”args\”:\”\”}”}

# Most populair public content
{“rp_author”:”{\”name\”:\”merged_public_posts\”,\”args\”:\”\”}”}

# Most populair content viewed by your profile
{“interacted_posts”:”{\”name\”:\”interacted_posts\”,\”args\”:\”\”}”}

# Posts from your friends
{“rp_author”:”{\”name\”:\”author_friends_feed\”,\”args\”:\”\”}”}

# Posts from the pages you like and the groups you are a member of
{“rp_author”:”{\”name\”:\”my_groups_and_pages_posts\”,\”args\”:\”\”}”}

# People who work at a specific employer
{“employer”:”{\”name\”:\”users_employer\”,\”args\”:\”PutIDHere\”}”}

# Photos viewed by your profile
{“interacted_posts”:”{\”name\”:\”interacted_posts\”,\”args\”:\”\”}”}

# Photos posted by your friends
{“rp_author”:”{\”name\”:\”author_friends_feed\”,\”args\”:\”\”}”}
```

**Old Graph Search**

```text
# Classic requests are like in natural language
Photos of my friends

# But some requests give bad results or false positive
# Alternative is using the Graph Search through the URL
facebook.com/search/str/<search>
```

```text
# Track you Facebook activity
My favorites pages
My favorites music
Books I like
Photos I have liked
Photos have I like that are recent
Places visited by me
Places nearby I visited
Games I like
...
```

```text
# View photos of friends or even strangers
Photos of my friends
Photos of my friends of friends taken in <Place>
Photos of people named [Name]
Photos of (or uploaded by) [Name]
Photos of [Name] in (or before or after) [Year]
Photos commented on by [Name]
Photos liked by [Name]
Photos of [Person A] liked by [Person B]
Photos of [Person A] AND [Person B]
photos taken in Paris, France from 2018
photos taken by phone xperia U
Photos of friends of my friends
photos of wedding uploaded by friends of my friends
photos of graduation uploaded by friends of my friends
photos of single friends of my friends uploaded by friends of my friends
```

```text
# Find new places
Restaurants nearby liked by my friends
[Cuisine] Restaurants liked by my friends of friends in [City]
Hotels nearby liked by my friends of friends
Friends who live (or have lived) in [Place]
Recent photos taken in [Place]
Places in [City] visited by people who live nearby
```

```text
# Know your followers
People who follow me
People who follow me and like [Page Name]
People who follow me and live in [City]
People who follow me and work at [Company]
People who follow me and were born in [Year]
Friends of friends who follow me
```

```text
# Find someone
People who work nearby
People who are [Profession Name] (like Doctors, Programmers, etc.)
People who live in [Place]
People who are named [Full or Partial Name]
People who are [Gender] and live in [City] and [Relationship Status]
People who believe in [Religion Name]
People who are N years old
People who are older than X and younger than Y years
People who were born in [Year]
People who work at [Company A] and previously worked at [Company B]
People who live in Paris
People who went to Harvard University
friends of my friends
friends of my friends who are single
friends of my friends who are men interested in men
people who live in new york city who uploaded photos taken in versailles
```

```text
# But some requests give bad results or false positive
# Alternative is using the Graph Search through the URL
facebook.com/search/str/<search>

# If there are several searchs you can use AND / OR parameters
xxx/intersect 
xxx/union
facebook.com/search/str/<search>/str/secondone/intersect

# People who live in Paris (present and past)
www.facebook.com/search/str/paris/pages-named/residents/present/
www.facebook.com/search/str/paris/pages-named/residents/past/

# People who live in Ile de France now or in France (code)
www.facebook.com/search/str/ile-de-france/pages-named/residents/present/
www.facebook.com/search/str/105604449474183/residents/present/

# Intersect one
# People "Patrick" who live in Toulouse now
www.facebook.com/search/str/patrick/users-named/str/toulouse/pages-named/residents/present/intersect/

# People "Patrick" who live in Toulouse now and Paris in the past
www.facebook.com/search/str/patrick/users-named/str/toulouse/pages-named/residents/present/str/paris/pages-named/residents/past/intersect/

# People who live in Paris and who like the page "afis sciences et pseudo sciences"
www.facebook.com/search/str/afis%20-%20science%20et%20pseudo-sciences/pages-named/likers/str/paris/pages-named/residents/present/intersect/

# Photos containing the keyword "humour"
www.facebook.com/search/str/humour/photos-keyword/

# People "Jeanne" friends of people "Louise"
www.facebook.com/search/str/jeanne/users-named/friends/str/louise/users-named/intersect/

# People who live in Geneva, who like Patrick Bruel's page and Céline Dion's page
www.facebook.com/search/str/geneva/pages-named/residents/present/str/c%C3%A9line%20dion/pages-named/likers/str/patrick%20bruel/pages-named/likers/intersect/

# Searching the keyword "scientifique" posted last month
/www.facebook.com/search/str/scientifique/stories-keyword/last-month/date/stories/intersect/

# Searching someone in Ile de France / France who posted the keyword "scientifique" last month
www.facebook.com/search/str/ile-de-france/pages-named/residents/present/stories/str/scientifique/stories-keyword/stories/intersect/
www.facebook.com/search/str/105604449474183/pages-named/residents/present/stories/str/scientifique/stories-keyword/stories/intersect/

# Searching posts containing "foutaises" or "conneries"
www.facebook.com/search/str/foutaises/stories-keyword/str/conneries/stories-keyword/union/

# Photos posted by French people
www.facebook.com/search/str/105604449474183/residents/present/photos/

# People under 29 who live in Toulouse
www.facebook.com/search/str/toulouse/pages-named/residents/present/29/users-older/intersect/
```

