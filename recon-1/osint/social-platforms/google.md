# Google



## Linking to Google Account

```text
https://medium.com/week-in-osint/keeping-a-grip-on-googleids-506814ae098b
https://subfn.net/2020/06/15/ga-osint-trick/

# Using Google Analytics, you can check if a non-google e-mail address is linked to a Google account

# Log into Google Analytics
# Navigate to Admin > Account User Management > Add user
# At this point, enter an email address. If the email is a backup email of a Google account, the primary Google email (e.g. the Google login email) will be disclosed
```

```text
# Automated tool for identifying Google account, getting ID and relations on Google services
# Works like a charm (from https://epieos.com/)
https://tools.epieos.com/google-account.php
```

## Get UserIDs

### **Google Drive Documents**

```text
# Get user ID from Google Drive document

# Logged
# Open the source code and search for: "\x2210" and "\x2211"
# Paste it in the Google Photos or Maps URL and find out more about your target.

# Not logged in
# Developer tools > Network > Filter "Permissions"
# Load the document and get informations for owner & co
```

### **Google Contacts**

```text
# Google Contacts can help getting IDs from e-mails
# But it may not work ! 

# CSV file with name and e-mails
# Import it, then several methods
```

```text
# Method 1
# - Developer tools and reload the page
# - Look for 4th raw requests to "contacts.google.com" with "batchexecute" endpoint
# - Copy request content to a file
$ cat file | grep -oP "10+\d{19}|11+\d{19}" | sort -u

# Method 2
# You can also find them in the page source code
# Go for the contact and open source code
# Search for "data-sourceid"

# Method 3
# If you have target YouTube channel page
# Look in the source code for the link to plus.google.com/<id>
```

### **Google API**

```text
# The only trustworthly way is to use the Google API

# First get contact information like above
# In the raw request, look for a contactID in the Form data
# String is beginning by "c" an +- 19 chars long.

# Then look on the API
https://developers.google.com/people/api/rest/v1/people/get

# resourceName
people/c0123456789...

# personFields
metada
```

### **Google Hangouts**

```text
https://medium.com/week-in-osint/keeping-a-grip-on-googleids-506814ae098b

# Using the "network" developper tool, open a new chat and fill in the target e-mail address
# Filter on the word "lookup" to get informations such as the ID
# You can also get the list of other connected Apps (Youtube...) ("appType")

# Look for "autocomplete"
# You can get information about other Google Accounts connected to this ID

# "lastUpdateTimeMicros" is the last time the account was edited (or sometimes created)
```

## Using UserIDs

```text
# 3 ways
# - Find photos albums
# - Look for geolocation
# - Find YouTube account connected to this
```

### **Photos albums**

```text
# Photos albums of a given user
https://get.google.com/albumarchive/{userID}

# Sometimes photos are tied to exact locations on Google Maps
```

### **Geolocation**

```text
# Google Maps and Google Reviews can both be accessed via GMaps
https://www.google.com/maps/contrib/{userID}

# By default is shows the photos that are uploaded with location information
# But the users reviews can be accessed via the option on the left.
# You are still able to find out someones real name.
```

### **Youtube**

```text
# Youtube ressources and tools
https://www.osintessentials.com/youtube

# Data can be different for different languages (descriptions...) and can be used to hide some infos

# YouTube channel
# Does not work with userID but accountName
# Possible to have several channels/profiles, but only one account
https://www.youtube.com/feeds/videos.xml?user={accountName}

# In most cases, it is the part before "@gmail.com"

# You can link different channels to the same user
https://www.youtube.com/channel/UCAiaFvFFZOwlU8Sw8S0dunQ

# Getting accountName
# In the source code : "ownerProfileUrl" or "vanityChannelUrl"

# Getting UserID
# In the source code : "plus.google.com"
```

### Automating Google Account Hunt \(GHunt\)

```text
# GHunt is a fully functionnal automated tool used to recover many informations about a Google Account
https://github.com/mxrch/GHunt

# The tools needs valid token for some features
# Grab these cookies after login to account.google.com
$ python check_and_gen.py 
__Secure-3PSID => xxx
APISID => xxx
SAPISID => xxx
HSID => xxx

# Docker Building
docker build --build-arg UID=$(id -u ${USER}) --build-arg GID=$(id -g ${USER}) -t ghunt .

# Docker Using
docker run -v $(pwd)/resources:/usr/src/app/resources -ti ghunt check_and_gen.py
docker run -v $(pwd)/resources:/usr/src/app/resources -ti ghunt hunt.py <email_address>

# Example
$ docker run -v $(pwd)/resources:/usr/src/app/resources -ti ghunt hunt.py xxx.xxx@gmail.com
[+] 1 account found !

------------------------------

Name: xxx xxx

Last profile edit : 2020/09/09 17:40:47 (UTC)

Email : xxx.xxx@gmail.com
Google ID : 11xxxxxx

Hangouts Bot : No

Activated Google services :
- Youtube
- Hangouts
- Photos
- Maps

Youtube channel (confidence => 37.5%) :
- [xxx xxx] https://youtube.com/channel/xx
- [xxx xxx] https://youtube.com/channel/xx
- [xxx xxx] https://youtube.com/channel/xx

Google Photos : https://get.google.com/albumarchive/xx/albums/profile-photos
=> Couldn't fetch the public photos.            

Google Maps : https://www.google.com/maps/contrib/xx/reviews
=> No reviews     
```

