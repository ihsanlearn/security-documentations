# Templates

````markdown
**Title:** [Medium] Misconfiguration: Dangling DNS Record on `email.loop.vimeo.com`
**Vulnerability Type:** Misconfiguration (as per Vimeo's policy table)

---

### Description

Hello Vimeo Security Team,

I have identified a dangling DNS record on the subdomain `email.loop.vimeo.com`. This subdomain is configured with a `CNAME` record pointing to `eu.mailgun.org`, a third-party email service.

However, the subdomain is not claimed or properly configured within the Mailgun platform. This is proven by the fact that any request to `email.loop.vimeo.com` resolves to a generic `404 page not found` error served by the Mailgun service.

As per your bug bounty policy, **"Dangling DNS Record"** is explicitly listed as an **in-scope** vulnerability (Low-High). Furthermore, your policy graciously states that a report does not need to include a full exploit, which is relevant here as this finding confirms the misconfiguration without a full (and potentially disruptive) takeover.

---

### Steps to Reproduce

The vulnerability can be confirmed with two simple steps:

1.  **Verify the CNAME Record:**
    A `dig` query confirms that `email.loop.vimeo.com` is an alias for `eu.mailgun.org`.

    ```bash
    > dig email.loop.vimeo.com

    ;; ANSWER SECTION:
    email.loop.vimeo.com.	300	IN	CNAME	eu.mailgun.org.
    eu.mailgun.org.		60	IN	A	34.111.99.212
    ```

2.  **Verify the "Dangling" State:**
    A `curl` request to the subdomain does not resolve to a Vimeo service, but instead returns a generic `404 page not found` error from the third-party (Mailgun). This proves the CNAME is not claimed.

    ```bash
    > curl -L email.loop.vimeo.com
    404 page not found
    ```

---

### Impact

This dangling CNAME record is a **dormant, high-severity vulnerability** and poses a significant risk.

The security of the `vimeo.com` domain is now dangerously dependent on the *current* account verification policies of a third party (Mailgun).

1.  **Future Subdomain Takeover (Ticking Time Bomb):**
    This is the most critical impact. If Mailgun ever changes, reverts, or deprecates its current verification policies (e.g., to a simpler method), this subdomain becomes **immediately vulnerable to a full takeover**. An attacker could then claim this subdomain to send highly convincing phishing emails, harvest credentials, or conduct other attacks under the authority of the trusted `vimeo.com` domain.

2.  **Reputational Damage & Social Engineering:**
    An official Vimeo subdomain resolving to a generic `404` error erodes user trust and appears unprofessional. This "broken" official link can also be leveraged as a "pretext" in sophisticated social engineering attacks (e.g., *"Our email link is down, please use this malicious link instead"*).

Given that your policy explicitly invites reports for "Dangling DNS Records" even without a full exploit, remediating this unmanaged DNS entry is crucial to prevent a future high-impact security incident.
````
