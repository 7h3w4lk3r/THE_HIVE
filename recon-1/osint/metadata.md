# ⭕ Metadata

## Online

#### Gathering site/domain metadata from online sources

### Metadata Sites

* [**Archive.org**](https://archive.org) ****&#x20;
* ****[**metapicz**](http://metapicz.com/#landing) ****&#x20;
* ****[**image forensic**](http://www.imageforensic.org) ****&#x20;
* ****[**findexif**](http://findexif.com) ****&#x20;
* ****[**exif regex**](http://exif.regex.info/exif.cgi)****

### wget

```
wget -nd -r -R htm,html,php,asp,aspx,cgi -P
wget -nd -r -A pdf,doc,docx,xls,xlsx -P
```

### metagoofil

find all files on a domain

```
metagoofil  -d [domain] -t pdf,docx,txt -o metagoofil -n 50 -l 200
```

### AWS Recon

{% embed url="https://github.com/darkbitio/aws-recon" %}

### Infoga

linux and windows

```
git clone https://github.com/m4ll0k/Infoga.git
cd Infoga
python setup.py install
python infoga.py

python infoga.py --domain nsa.gov --source all --breach -v 2 --report ../nsa_gov.txt

python infoga.py --info m4ll0k@protonmail.com --breach -v 3 --report ../m4ll0k.txt
```

### VPN Configuration files

ome organizations publicly distribute configuration files and keys for VPN systems. Cisco profile configuration files (PCFs) contain IPsec VPN client variables, including the following:

• VPN server endpoint addresses

• Plaintext credentials (group name and password)

• Encrypted credentials (an obfuscated group password)

#### using google search strings:

![](<../../.gitbook/assets/image (278) (1) (1) (1) (1) (1).png>)

#### crack / decode the passwords

{% embed url="https://artemkin.github.io/cisco-password-decoder/index_plain.html" %}

{% embed url="http://artemkin.github.io/cisco-password-decoder" %}

{% embed url="https://www.ifm.net.nz/cookbooks/passwordcracker.html" %}

## Offline

#### Extract metadata from the data we have.

### exiftool

```
exiftool poissonrecon.pdf
```

### Strings

```
-e >>> for 16 bit big-endian unicode
-el >>> for 16 bit little-endian unicode
-n >>> set minimum caracter length
-a >>> ascii
-u >>> unicode

strings -n 4 test.elf
```

### tesseract

find text in images

```
apt install tesseract-ocr
apt-get install tesseract-ocr-LANG
apt-get install tesseract-ocr-all

for x in *.jpg; do tesseract $x stdout -psm 11 -l eng >> tesser.out; done
```

### Music Metadata

{% embed url="https://github.com/mischif/music-metadata-tools" %}
