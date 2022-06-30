# Github

## General

```
# Online Service used to search in many git repositories
https://grep.app/
```

## Finding user e-mail

```
# Method 1
# Go for it
→ https://api.github.com/users/xxxxxxx/events/public

# And search for “email”

------------------------

# Method 2
# Go for user repositories
# Find one repo and go checks commits
# Take one commit
https://github.com/angularjs-gdit/example-01-helloworld/commit/4243528ac995e2bb717eed82b06fea620ab7f0f2

# Add .patch at the end and check for email
https://github.com/angularjs-gdit/example-01-helloworld/commit/4243528ac995e2bb717eed82b06fea620ab7f0f2.patch
```

```
# Usually, credential leaks do not occur on the company public Github
# But more on developer/employees personal Github
```

### Gitrob

```
# Gitrob is a github scanner and crawler
# Can be used to retrieve sensitive data in github repositories

https://michenriksen.com/blog/gitrob-putting-the-open-source-in-osint/
https://github.com/michenriksen/gitrob

# First step : Collecting all public repositories of the organization
# Then it goes on organization members and all their repo
# It proceeds files to gather filenames and run them through filters to detect sensitive files
# It will then start a Sinatra web server locally

go get github.com/michenriksen/gitrob or precompiled binaries
```

### GitMiner

```
# Advanced search tool and automation in Github.
# This tool aims to facilitate research by code or code 
# snippets on github through the site's search page.

# Demonstrates the fragility of trust in public repositories to store codes with sensitive information.

https://github.com/UnkL4b/GitMiner

# Options
-m  --- Specify the search module
-c   --- Specify the cookie for your Github
-q   --- Speficy search terms


# Searching for wordpress configuration files with passwords
python3 gitminer-v2.0.py -q 'filename:wp-config extension:php FTP_HOST in:file ' -m wordpress -c pAAAhPOma9jEsXyLWZ-16RTTsGI8wDawbNs4 -o result.txt

# Looking for brasilian government files containing passwords
python3 gitminer-v2.0.py --query 'extension:php "root" in:file AND "gov.br" in:file' -m senhas -c pAAAhPOma9jEsXyLWZ-16RTTsGI8wDawbNs4

# Looking for shadow files on the etc paste
python3 gitminer-v2.0.py --query 'filename:shadow path:etc' -m root -c pAAAhPOma9jEsXyLWZ-16RTTsGI8wDawbNs4

# Searching for joomla configuration files with passwords
python3 gitminer-v2.0.py --query 'filename:configuration extension:php "public password" in:file' -m joomla -c pAAAhPOma9jEsXyLWZ-16RTTsGI8wDawbNs4

# Interesting queries
| Dork | Description |
| filename:.npmrc _auth | npm registry authentication data |
| filename:.dockercfg auth | docker registry authentication data |
| extension:pem private | private keys |
| extension:ppk private | puttygen private keys |
| filename:id_rsa or filename:id_dsa | private ssh keys |
| extension:sql mysql dump | mysql dump |
| extension:sql mysql dump password | mysql dump look for password; you can try varieties |
| filename:credentials aws_access_key_id | might return false negatives with dummy values |
| filename:.s3cfg | might return false negatives with dummy values |
| filename:wp-config.php | wordpress config files |
| filename:.htpasswd | htpasswd files |
| filename:.env DB_USERNAME NOT homestead | laravel .env (CI, various ruby based frameworks too) |
| filename:.env MAIL_HOST=smtp.gmail.com | gmail smtp configuration (try different smtp services too) |
| filename:.git-credentials | git credentials store, add NOT username for more valid results |
| PT_TOKEN language:bash | pivotaltracker tokens |
| filename:.bashrc password | search for passwords, etc. in .bashrc (try with .bash_profile too) |
| filename:.bashrc mailchimp | variation of above (try more variations) |
| filename:.bash_profile aws | aws access and secret keys |
| rds.amazonaws.com password | Amazon RDS possible credentials |
| extension:json api.forecast.io | try variations, find api keys/secrets |
| extension:json mongolab.com | mongolab credentials in json configs |
| extension:yaml mongolab.com | mongolab credentials in yaml configs (try with yml) |
| jsforce extension:js conn.login | possible salesforce credentials in nodejs projects |
| SF_USERNAME salesforce | possible salesforce credentials |
| filename:.tugboat NOT _tugboat | Digital Ocean tugboat config |
| HEROKU_API_KEY language:shell | Heroku api keys |
| HEROKU_API_KEY language:json | Heroku api keys in json files |
| filename:.netrc password | netrc that possibly holds sensitive credentials |
| filename:_netrc password | netrc that possibly holds sensitive credentials |
| filename:hub oauth_token | hub config that stores github tokens |
| filename:robomongo.json | mongodb credentials file used by robomongo |
| filename:filezilla.xml Pass | filezilla config file with possible user/pass to ftp |
| filename:recentservers.xml Pass | filezilla config file with possible user/pass to ftp |
| filename:config.json auths | docker registry authentication data |
| filename:idea14.key | IntelliJ Idea 14 key, try variations for other versions |
| filename:config irc_pass | possible IRC config |
| filename:connections.xml | possible db connections configuration, try variations to be specific |
| filename:express.conf path:.openshift | openshift config, only email and server thou |
| filename:.pgpass | PostgreSQL file which can contain passwords |
| filename:proftpdpasswd | Usernames and passwords of proftpd created by cpanel |
| filename:ventrilo_srv.ini | Ventrilo configuration |
| [WFClient] Password= extension:ica | WinFrame-Client infos needed by users to connect toCitrix Application Servers |
| filename:server.cfg rcon password | Counter Strike RCON Passwords |
| JEKYLL_GITHUB_TOKEN | Github tokens used for jekyll |
| filename:.bash_history | Bash history file |
| filename:.cshrc | RC file for csh shell |
| filename:.history | history file (often used by many tools) |
| filename:.sh_history | korn shell history |
| filename:sshd_config | OpenSSH server config |
| filename:dhcpd.conf | DHCP service config |
| filename:prod.exs NOT prod.secret.exs | Phoenix prod configuration file |
| filename:prod.secret.exs | Phoenix prod secret |
| filename:configuration.php JConfig password | Joomla configuration file |
| filename:config.php dbpasswd | PHP application database password (e.g., phpBB forum software) |
| path:sites databases password | Drupal website database credentials |
| shodan_api_key language:python | Shodan API keys (try other languages too) |
| filename:shadow path:etc | Contains encrypted passwords and account information of new unix systems |
| filename:passwd path:etc | Contains user account information including encrypted passwords of traditional unix systems |
| extension:avastlic | Contains license keys for Avast! Antivirus |
| extension:dbeaver-data-sources.xml | DBeaver config containing MySQL Credentials |
| filename:.esmtprc password | esmtp configuration |
| extension:json googleusercontent client_secret | OAuth credentials for accessing Google APIs |
| HOMEBREW_GITHUB_API_TOKEN language:shell | Github token usually set by homebrew users |
| xoxp OR xoxb | Slack bot and private tokens |
| .mlab.com password | MLAB Hosted MongoDB Credentials |
| filename:logins.json | Firefox saved password collection (key3.db usually in same repo) |
| filename:CCCam.cfg | CCCam Server config file |
| msg nickserv identify filename:config | Possible IRC login passwords |
| filename:settings.py SECRET_KEY | Django secret keys (usually allows for session hijacking, RCE, etc) |
```
