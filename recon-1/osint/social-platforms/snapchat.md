# Snapchat

```
# Snapchat guide for law enforcment
https://storage.googleapis.com/snap-inc/privacy/lawenforcement.pdf
```

## Online Research Tools

```
# Online browser
# Potentially NSFW
https://sovip.io

# User Search
https://www.snapdex.com/
https://snapyfox.com/

# Location 
https://map.snapchat.com/
```

### Snapchat Map

```
https://osintcurio.us/2020/04/13/using-snapchat-for-osint-10-minute-tip/

# Using the map you can view public content
# You can play with overlays and save videos

# To get video
# - Developper Tools (F12)
# - Network and filter on medias
# - Reload (Ctrl+R)
# - On the snap link, right click and open in new tab

# By default it removes extra graphics layer
https://s.sc-cdn.net/<some random string>/default/media.mp4

# You can get original video by tweaking media.mp4 to embedded.mp4
https://s.sc-cdn.net/<some random string>/default/embedded.mp4
```

```
# Scraper for the snapcat map
https://github.com/nemec/snapchat-map-scraper

# Create the db
$ python3 story_downloader.py create snap.db

# Add locations
# Added downtown dallas to database
$ python3 story_downloader.py add --database snap.db --label "downtown dallas" 32.783038 -96.796388

# The scrape command iterates through all of the added locations and downloads snaps at that location
# --randomize will randomize the geo location within 500m, intended to prompt Snapchat's API to 
# send new videos that it hasn't yet served.
# --repeat will cause the application to loop infinitely looking for new videos.
# Leave it running overnight to collect as much as possible.
# --sleep will modify how long the application sleeps before repeating.
# Defaults to 120 seconds and only takes effect if --repeat is included
$ python3 story_downloader.py scrape --database snap.db --randomize --repeat

# The review command looks through all unreviewed videos and opens each one in a video player for review
$ python3 story_downloader.py review --database snap.db

# The export command will copy all videos which are "classified" to a new folder for further review/editing/publishing.
$ python3 story_downloader.py export --database snap.db export/
```

### Snapchat Stories

```
# You can get Snapchat stories
https://story.snapchat.com/

# Each user story URL is like that
https://story.snapchat.com/s/username

# If a user's settings are public content, you can try

# For downloading, same as for the map
# - Developper Tools (F12)
# - Network and filter on medias
# - Reload (Ctrl+R)
# - On the snap link, right click and open in new tab
```
