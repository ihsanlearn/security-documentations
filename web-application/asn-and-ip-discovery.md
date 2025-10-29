# ASN & IP Discovery

***

### ASN Mapping

#### ASN Discovery

```sh
asnmap -d example.com | dnsx -silent -resp-only
```

#### Amass Intel by Organization

```sh
amass intel -org "organization_name"
```

#### Amass Intel by CIDR

```sh
amass intel -active -cidr 159.69.129.82/32
```

#### Amass Intel by ASN

```sh
amass intel -active -asn [asnno]
```

***

### IP Harvesting

#### VirusTotal IP Lookup

```sh
curl -s "https://www.virustotal.com/vtapi/v2/domain/report?domain=example.com&apikey=[api-key]" | jq -r '.. | .ip_address? // empty' | grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}'
```

#### AlienVault OTX

```sh
curl -s "https://otx.alienvault.com/api/v1/indicators/hostname/example.com/url_list?limit=500&page=1" | jq -r '.url_list[]?.result?.urlworker?.ip // empty' | grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}'
```

#### URLScan.io

```sh
curl -s "https://urlscan.io/api/v1/search/?q=domain:example.com&size=10000" | jq -r '.results[]?.page?.ip // empty' | grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}'
```

#### Shodan SSL Search

```sh
shodan search Ssl.cert.subject.CN:"example.com" 200 --fields ip_str | httpx -sc -title -server -td
```
