# ⭕ Word List

## <mark style="color:red;">Wordlists</mark>

\
[https://github.com/danielmiessler/SecLists​](https://github.com/danielmiessler/SecLists%E2%80%8B)\
[https://github.com/Dormidera/WordList-Compendium​](https://github.com/Dormidera/WordList-Compendium%E2%80%8B)\
​https://github.com/kaonashi-passwords/Kaonashi\
\


## <mark style="color:red;">Default Credentials</mark>

{% embed url="http://www.phenoelit.org/dpl/dpl.html%E2%80%8B" %}

{% embed url="http://www.vulnerabilityassessment.co.uk/passwordsC.htm%E2%80%8Bl" %}
\

{% endembed %}

{% embed url="https://192-168-1-1ip.mobi/default-router-passwords-list" %}

{% embed url="https://datarecovery.com/rd/default-passwords" %}

{% embed url="https://bizuns.com/default-passwords-list%E2%80%8B" %}

{% embed url="https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/default-passwords.csv%E2%80%8B" %}

## <mark style="color:red;">crawl website to find words for a wordlist</mark>

```
cewl www.megacorpone.com -m 6 -w megacorp-cewl.txt
```

```
-k, --keep: Keep the downloaded file.
-d <x>,--depth <x>: Depth to spider to, default 2
-m, --min_word_length: Minimum word length, default 3.
-o, --offsite: Let the spider visit other sites.
--exclude: A file containing a list of paths to exclude
-w, --write: Write the output to the file.
-u, --ua <agent>: User agent to send.
-n, --no-words: Don't output the wordlist.
--with-numbers: Accept words with numbers in as well as just letters
-a, --meta: include meta data.
--meta_file file: Output file for meta data.
-e, --email: Include email addresses.
--email_file <file>: Output file for email addresses.
--meta-temp-dir <dir>: The temporary directory used by exiftool when parsing files, default /tmp.
-c, --count: Show the count for each word found.
-v, --verbose: Verbose.
```

## <mark style="color:red;">crunch</mark>

![](<../../.gitbook/assets/image (51) (1) (1).png>)

![](<../../.gitbook/assets/image (14) (1).png>)

### <mark style="color:orange;">Example Rules</mark>

\[Capital Letter] \[2 x lower case letters] \[2 x special chars] \[3 x numeric]

```
crunch 8 8 -t ,@@^^%%%
```

using predefined character sets:

```
crunch 4 6 -f /usr/share/crunch/charset.lst mixalpha -o crunch.txt
```

pipe with other tools

```
crunch 4 8 01234567890abcdefghijklmnopqrstuvwxyz | john hashes --stdin
```

Iranian birth dates

```
crunch 8 8 1234567890 -t 13%%13%%
```

iranina phone number

```
crunch 11 11 1234567890 -t 091%%%%%%%%
crunch 11 11 1234567890 -t 099%%%%%%%%
```

## <mark style="color:red;">rsmangler</mark>

will take an existing wordlist and perform various manipulations resulting in a more targeted wordlist which will include permutations and variations of words we can in our wordlist.

With Rsmangler and other wordlist generator tools, we’ll typically want to start with a small list of several words since just supplying a few words to start, can quickly generate a very large file

Using the following command line, we can generate a list containing variations and permutations

```
cat words.txt | rsmangler --file - > words_new.txt
```

## <mark style="color:red;">mentalist</mark>

#### [https://github.com/sc0tfree/mentalist/wiki](https://github.com/sc0tfree/mentalist/wiki)

