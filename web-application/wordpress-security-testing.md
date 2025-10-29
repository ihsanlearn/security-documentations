# Wordpress Security Testing

***

### WPScan

#### WPScan Full Enumeration

```sh
wpscan --url https://example.com --disable-tls-checks -e at -e ap -e u --enumerate ap --plugins-detection aggressive --force
```
