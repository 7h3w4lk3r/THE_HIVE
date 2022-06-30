# Instagram

## Tips & Tricks

### **Instagram ID**

```
# Instagram User ID can be very important to investigate
# Each account has a unique user ID linked to the @username
# Some websites allows to get it for an account
https://codeofaninja.com/tools/find-instagram-user-id

# You can then trace and spot re-used instagram account by
# searching for old names and post, for example using
# Google Dorks
```

### **Mirrors**

```
# Instagram Mirrors can be usefull to browse Instagram accounts
# Search features are less restrictive
# Some deleted content can still be indexed
https://rankersta.com/
https://pikdo.biz/
https://www.piwox.com/
https://gramfly.com/
https://web.stagram.com
```

### **Downloaders**

```
# Instagram Online Downloaders
https://downloadgram.com/
https://www.instadp.com/
https://www.instadp.com/
https://instasave.xyz/
```

### **Location Search**

```
https://twitter.com/quiztime/status/1339231557509402624
https://github.com/bellingcat/instagram-location-search

# You can searh instagram posts for a specific location 
python3 instagram-locations.py --session "<session-id-token>" --lat 32.22 --lng -110.97 --csv locs.csv

# Usage with date
python3 instagram-locations.py --session "<session-id-token>" --lat 47.6164311 --lng -122.3203952 --map map.html --date 2020-06-09
```

## Manual investigation

```
# Viewing Instagram accounts without being logged in

# METHOD 1 - JSON
# You can User ID, user bio, posts infos...
# But you can only see the first 11 messages
https://www.instagram.com/targetAccount
https://www.instagram.com/targetAccount?__a=1

# METHOD 2 - Developper Tools
# On the profile, go F12
# View the "<body class =" "style =" overflow: hidden; ">" section ==> visible
# "<div class =" RnEpo Yx5HN "role = presentation">" ==> Delete node

# METHOD 3 - Adlocker
# Ublock Origin ==> "Element Sector Mode"
# Click on the Instagram pop-up
# Add & Create : 
#   - ##.Yx5HN.RnEpo
#   - ##body:style(overflow: visible !important;)

# METHOD 4 : Images
# Go to target account
# Click on any image and browse using arrow keys

# METHOD 5 : Embed
# You can add /embed/captioned at the end of the URL to get the post
/p/CGvIxnpFrzL/embed/captioned
```

### Toutatis ([https://github.com/megadose/toutatis/](https://github.com/megadose/toutatis/))

```
# Toutatis is a small python script used to retrieve informations such as phone number or target e-mail.
# It needs a valid session id (cookie)
# You can also search for a bunch of profiles

$ python toutatis.py -u starbucks -i ep -s <session_id>
The informations about : starbucks
User id : 1034466
Phone of starbucks : 18007827282
Mail of starbucks : info@starbucks.com
```

### OSI.IG

```
# Simple python script used to scrape and parse data from Instagram
# NOTE : pictures downloaded are sent to tinyurl

$ python3 main.py -h             
usage: main.py [-h] -u USERNAME [-p] [-s]

optional arguments:
  -h, --help            show this help message and exit
  -u USERNAME, --username USERNAME
                        username of account to scan
  -p, --postscrap       scrape all uploaded images info
  -s, --savedata        save data to file
```

### InstagramOSINT ([https://github.com/sc1341/InstagramOSINT](https://github.com/sc1341/InstagramOSINT))

```
# The Instagram OSINT Tool gets a range of information from an Instagram account
# that you normally wouldn't be able to get from just looking at their profile
# Username
# Profile Name
# URL
# Followers
# Following
# Number of Posts
# Bio
# Profile Picture URL
# Is Business Account?
# Connected to a FB account?
# External URL
# Joined Recently?
# Business Category Name
# Is private?
# Is Verified?
# Downloads Public Photos

python3 main.py --username USERNAME
```

### Instaloader ([https://github.com/instaloader/instaloader](https://github.com/instaloader/instaloader))

```
# Downloads public and private profiles, hashtags, user stories,feeds and saved media,
# Downloads comments, geotags and captions of each post,
# Automatically detects profile name changes and renames the targetdirectory accordingly,
# Allows fine-grained customization of filters and where to storedownloaded media.

# Download public profile informations
instaloader profile <profile-name>

# Download private profile informations
instaloader --login=<username> profile <profile-name>
```

### InstaLooter ([https://github.com/althonos/InstaLooter](https://github.com/althonos/InstaLooter))

```
# Another tool used to download information on public profiles
# No API call

# Usage
instalooter --usage / instalooter --help

# Download all pictures from an account
instalooter user <username>

# Download single post
instalooter post "https://www.instagram.com/p/BFB6znLg5s1/" ~/Pictures

# Download the latest 20 pictures or videos tagged with python to /tmp
instalooter hashtag python /tmp -n 20 -v -c MYLOGIN
```
