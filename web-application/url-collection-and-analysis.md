# URL Collection & Analysis

***

### Active Crawling

#### Katana

```sh
katana -u subdomains-alive.txt -d 2 -o katana.txt
```

#### Hakrawler

```sh
cat katana.txt | hakrawler -u > hakrawler.txt
```

### Passive Crawling

#### GAU (Get All URLs)

```sh
cat subdomains-alive.txt | gau | sort -u > gau.txt
```

#### GAU with Status Filter

```sh
echo example.com | gau --mc 200 | urldedupe > urls.txt
```

#### URLFinder

<pre class="language-sh"><code class="lang-sh"><strong>cat subdomains-alive.txt | urlfinder > urlfinder.txt
</strong><strong>urlfinder -d example.com | sort -u > urlfinder.txt
</strong></code></pre>

***

### Parameter Extraction

#### Extract URLs with Parameters

```sh
cat all-urls.txt | grep '=' | urldedupe | tee output.txt
```

#### GF SQLi Pattern

```sh
cat all-urls.txt | gf sqli
```
