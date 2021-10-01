# TikTok

## TikTok

```text
# Resources
https://www.skopenow.com/news/tiktok-investigations-and-osint-tips

# Searching info
https://www.osintcombine.com/tiktok-quick-search

# Downloader
https://ttdown.org/
https://en.savefrom.net/download-from-tiktok

# Tiktok Analytics
https://cloutmeter.com/
```

### **Profile Discovery**

```text
# Method 1 : You have usernames
tiktok.com/@{username}

# Method 2 : Involving Dorks
site:tiktok.com "username" OR "first and last name"

# Method 3 : Search for mentions on other social media
"username" OR "first and last name" "tiktok.com" -site:tiktok.com
```

### **Profile Picture**

```text
# Method 1
# Right click --> Inspect

# Method 2
https://tiktok.com/node/share/user/@{username}?isUniqueId=true

# Method 3 (Firefox exclusive)
# Media panel by using Ctrl+I

# Want 1080 ones ? (@aware_online) 
1. Go to TikTok profile http://tiktok.com@{username}
2. Open dev tools (F12)
3. Click on "Network tab"
4. Refresh page (F5)
5. Select "XHR" tab
6. Double click on "api/user/detail/"
7. Open "AvatarLarger" link
8. Have fun!
```

### **Extracting video content**

```text
# Pretty much same methods as for pictures
# Just need the individual video page, then inspect

# Method 2 (Firefox exclusive)
# Media panel by using Ctrl+I
```

```text
# Date and timestamp for each videos

# In the individual video link, get page sources and search for
uploadDate 

# Convert
http://www.timebie.com/std/zulu.php
```

### **Reach analysis**

```text
# In tiktok video, feature called "reach" can allow to extract sound from a video and use it

# By clicking the link, you can find all videos using the same sound/music
```

### **Searching for target mentions on other profiles**

```text
# Looking for TikTok profiles mentionning target in the body but not in the title
site:tiktok.com intext:@username -intitle:"username" inurl:video

# TikTok allow to link to other social medias
# You can search for other mentions, or eventually modified usernames etc.
site:youtube.com OR site:instagram.com "username" OR "first and last name" "tiktok" "tik tok" OR intext:"tiktok.com/@username"
```

