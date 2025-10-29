# Hidden Parameter Discovery

***

### Arjun Parameter Discovery

#### Passive Discovery

```sh
arjun -u https://example.com/endpoint.php -oT arjun-passive.txt -t 10 --rate-limit 10 --passive -m GET,POST --headers "User-Agent: Mozilla/5.0"
```

#### Active Discovery

```sh
arjun -u https://example.com/endpoint.php -oT arjun-active.txt -m GET,POST -w /usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt -t 10 --rate-limit 10 --headers "User-Agent: Mozilla/5.0"
```
