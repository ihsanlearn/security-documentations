# Live Host Discovery

***

### HTTP Probing

#### HTTPX Basic

```sh
cat subdomains.txt | httpx > subdomains-alive.txt
```

#### HTTPX Advance

```sh
cat subdomain.txt | httpx -sc -title -server -td -ports 80,443,8080,8000,8888 -threads 200
```

#### Shortcut Pipeline

```sh
cat subdomains.txt \
  | alterx -enrich  -silent \
  | dnsx -a -aaaa -cname -r resolvers.txt -silent \
  | httpx -silent -title -status-code -threads 40 -o subdomains-alive.txt

```

***

### Visual Reconnaissance

#### Aquatone Basic

```sh
cat subdomains-alive.txt | aquatone
```

#### Aquatone Extended Ports

```sh
cat subdomains-alive.txt | aquatone -ports 80,81,443,591,2082,2087,2095,2096,3000,8000,8001,8008,8080,8083,8443,8834,8888
```
