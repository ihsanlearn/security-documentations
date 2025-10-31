# Git Repository Disclosure

### Tips :&#x20;

1. Cari **`.git/`** dir accessible, **`.git/config`**, **`.git/packed-refs`**, **`.env`** dalam repo history.

***

### Git Exposure Detection

#### Git Directory Discovery

```sh
cat subdomains.txt | grep "SUCCESS" | gf urls | httpx -sc -server -cl -path "/.git/" -mc 200 -location -ms "Index of" -probe
```
