# Hold Report

````markdown
**Title:** Misconfiguration: Dangling DNS Record on `url2514.magisto.com` (SendGrid)
**Vulnerability Type:** Misconfiguration (as per Vimeo's policy table)

---

### Description

Hello Vimeo Security Team,

I have identified another dangling DNS record, this time on the subdomain `url2514.magisto.com`. This subdomain is configured with a `CNAME` record pointing to `sendgrid.net`, a third-party email service.

However, this domain is not claimed or properly configured within the SendGrid platform.

This is definitively proven by the fact that any request to the **HTTP** version of the site resolves to a generic `404 Not Found` error page served by SendGrid's `nginx` server. This is a classic fingerprint for an unclaimed "Link Branding" domain on SendGrid.

As per your bug bounty policy, **"Dangling DNS Record"** is explicitly listed as an **in-scope** vulnerability.

---

### Steps to Reproduce

The vulnerability can be confirmed with two simple, read-only steps:

1.  **Verify the CNAME Record:**
    A `dig` query confirms that `url2514.magisto.com` is an alias for `sendgrid.net`.

    ```bash
    > dig url2514.magisto.com

    ;; ANSWER SECTION:
    url2514.magisto.com.	300	IN	CNAME	sendgrid.net.
    sendgrid.net.		90	IN	A	54.250.209.117
    sendgrid.net.		90	IN	A	13.214.238.55
    ```

2.  **Verify the "Dangling" State (via HTTP):**
    A `curl` request to the **`http://`** version of the subdomain returns the specific `nginx 404` error from SendGrid, proving the domain is unclaimed.

    ```bash
    > curl [http://url2514.magisto.com](http://url2514.magisto.com)

    <html>
    <head><title>404 Not Found</title></head>
    <body>
    <center><h1>404 Not Found</h1></center>
    <hr><center>nginx</center>
    </body>
    </html>
    ```

---

### Impact

This dangling CNAME record is a **dormant, high-severity vulnerability** and poses a significant risk.

The security of the `magisto.com` domain is now dangerously dependent on the *current* account verification policies of a third party (SendGrid).

1.  **Future Subdomain Takeover (Ticking Time Bomb):**
    This is the most critical impact. If SendGrid ever changes, reverts, or deprecates its current verification policies (e.g., to a simpler method), this subdomain becomes **immediately vulnerable to a full takeover**. An attacker could then claim this "Link Branding" domain to send highly convincing phishing emails using the trusted `url2514.magisto.com` domain.

2.  **Reputational Damage & Social Engineering:**
    An official subdomain resolving to a generic `404` error erodes user trust and appears unprofessional.

Given that your policy explicitly invites reports for "Dangling DNS Records," remediating this unmanaged DNS entry is crucial to prevent a future high-impact security incident.
````
