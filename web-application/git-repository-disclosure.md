# Git Repository Disclosure

***

### Git Exposure Detection

#### Git Directory Discovery

```sh
cat subdomains.txt | grep "SUCCESS" | gf urls | httpx -sc -server -cl -path "/.git/" -mc 200 -location -ms "Index of" -probe
```
