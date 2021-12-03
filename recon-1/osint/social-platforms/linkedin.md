# LinkedIn

{% embed url="https://github.com/vysecurity/LinkedInt" %}

{% embed url="https://github.com/clr2of8/GatherContacts" %}

## Tips & Tricks

```
# Check the "see coordinates" on accounts
# Sometimes, people link other resources
```

```
# Getting likes, comment etc.
/detail/recent-activity
```

## Getting account linked to an e-mail

```
# Great trick
https://www.intelligencewithsteve.com/post/a-guide-to-searching-linkedin-by-email-address

# Sales feature have been discontinued

# Log to OWA and go to contacts
https://outlook.live.com/owa/
https://outlook.live.com/people

# Then add contact, using the target e-mail
# Go to the LinkedIn tab and connect using a fake account

# If an account is linked to the target e-mail, you should now see it in Outlook
```

### ScrapedIn

```
# Tool created to assist in performing recon using the LinkedIn API.
# Provide a search string
# Ouput as XLSX but intented to work with Google Sheets
# After importing you'll have a dataset and report worksheet

# Dataset : First name, last name, occupation, industry, profile URL, picture URL
# Report : Picture (displayed), Full name, occupation, Link to profile

# Full size profile photo
# Then right click "view image"
https://linkedin.com/in/yourprofilename/detail/photo
```

### linkScrape

```
# linkScrape is another enumeration tool
# Pure Web Scrapper → It does not use the LinkedIn API
# /!\ Some bugs → LinkedIn account can be banned and needs to have 10 contacts to perform company based searches

# For companies with whitespace, use simple quotes

# Using a LinkedIn company ID is the most accurate way to search for a company. It's also the only way to search for universities.
python linkScrape.py -c 100 -r1

# To find company ID, go to the company page then "See all numeral employees on LinkedIn → ?facetCurrentCompany=%5B%2239624%22

Usage: python linkScrape.py <OPTIONS>
Example[1]: python linkScrape.py -e LinkedInUser@email.com -c 'Example Company' -r 10 -t 3 -m 7 -d example.com
Example[2]: python linkScrape.py -e LinkedInUser@email.com -c 'Example Company' -r 5-10 -t 3 -m 7 -d example.com
Example[3]: python linkScrape.py -e LinkedInUser@email.com -c 100 -r 3 -t 3 -m 7 -d example.com
Example[4]: python linkScrape.py -m 7 -i ~/Company/names.txt\n\"
Formatted output saved to: linkedIn/linkScrape-data/Company-mangle[x]_time.txt

Login options:
-e <email> Your LinkedIn.com Email Address.
-p <pass>  Your LinkedIn.com Password. (If -p parameter is not defined, you\'ll be prompt to enter a password)

Search options:
-c <company> Search company name or company ID.
-r <results> Searches X number of LinkedIn.com pages, or a range of pages (Default is 1-3).
-t <secs>    Sets timeout value. (Default is 3.)
```

### Peasant

```
# Peaseant is a scraping tool that uses the LinkedIn API
# You can harvest data from a company, set a large connection campaign and even spoof a profile

# Harvesting
# Will generate CSV file
./peasant.py harvest -C "user:password" -cns company -of out.csv

# Contact Requests
./peasant.py add_contacts -if dundermifflin.csv -C "$creds"

# Spoofing 
./peasant.py spoof_profile -pu paul-m-04773b1 -C "$creds"
```

### Linkedin2Username ([https://github.com/initstring/linkedin2username](https://github.com/initstring/linkedin2username))

```
# Pure web scraper used to get username list from the employee list of a company
# It will create several lists of possible username formats for all employees of a company you point it at

# pull all employees of Uber
python linkedin2username.py myname@email.com uber-com

# Pull a shorter list and append the domain name @uber.com to them
python linkedin2username.py myname@email.com uber-com -d 5 -n 'uber.com'
```
