# CORS Testing

***

### Manual CORS Testing

#### CORS Testing with Curl

```sh
curl -H "Origin: http://example.com" -I https://example.com/wp-json/
```

#### Detailed CORS Analysis

```sh
curl -H "Origin: http://example.com" -I https://example.com/wp-json/ | grep -i -e "access-control-allow-origin" -e "access-control-allow-methods" -e "access-control-allow-credentials"
```

***

### Automated CORS Testing

#### Nuclei CORS Test

```sh
cat subdomains.txt | httpx -silent | nuclei -t nuclei-templates/vulnerabilities/cors/ -o cors_results.txt
```
