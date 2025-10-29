# Subdomain Enumeration

***

### Automated Enumeration

#### Subfnder

```sh
subfinder -d example.com -all -o subfinder.txt
subfinder -dL wildcard.txt -all -o subfinder.txt
```

#### Assetfinder

```sh
assetfinder --subs-only example.com > assetfinder.txt
```

#### ShodanX

```sh
shodanx subdomain -d example.com > shodanx.txt
```

#### Sublist3r

```sh
sublist3r -d example.com -e baidu,yahoo,google,bing,ask,netcraft,virustotal,threatcrowd,crtsh,passivedns -v -o sublist3r.txt
```

#### Amass

```sh
amass enum -passive -d example.com | cut -d']' -f 2 | awk '{print $1}' | sort -u > amass.txt
```

***

### Public Resource

#### Crt.sh

```sh
curl -s https://crt.sh\?q\=\example.com\&output\=json | jq -r '.[].name_value' | grep -Po '(\w+\.\w+\.\w+)$' > crtsh.txt
```

#### Wayback Machine (webarchive)

```shell
curl -s "http://web.archive.org/cdx/search/cdx?url=*.example.com/*&output=text&fl=original&collapse=urlkey" |sort| sed -e 's_https*://__' -e "s/\/.*//" -e 's/:.*//' -e 's/^www\.//' | sort -u > wayback.txt
```

***

### Subdomain Processing

#### Dedupe & Merging

```sh
cat *.txt | sort -u > subdomains.txt
```

#### Subdomain Bruteforce

```sh
ffuf -u "https://FUZZ.example.com" -w wordlist.txt -mc 200,301,302
```

#### Subdomain Permutation

```sh
subfinder -d example.com | alterx | dnsx
```

#### Alterx Enrichment

```
echo example.com | alterx -enrich | dnsx
```

#### Alterx with Wordlist

```sh
echo example.com | alterx -pp word=/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt | dnsx
```
