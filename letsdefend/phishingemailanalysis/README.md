# LetsDefend Phishing Email — Technical Write-up

> **Platform:** LetsDefend  
> **Challenge:** Phishing Email  
> **Category:** Email / Phishing Analysis / SOC  
> **Objective:** Determine whether the supplied email is phishing by analyzing its headers, embedded URL, and associated infrastructure.

---

# 1. Overview

The investigation focused on a suspicious email impersonating PayPal and written in German.

The successful investigation path was:

```text
Open the email
      ↓
Inspect message source / headers
      ↓
Identify Return-Path
      ↓
Inspect embedded URL
      ↓
Extract the URL domain
      ↓
Check the domain/URL in VirusTotal
      ↓
Identify the Body SHA-256
      ↓
Correlate the indicators
      ↓
Classify the email as phishing
```

The challenge ultimately required five answers:

| Question | Answer |
|---|---|
| Return-Path | `bounce@rjttznyzjjzydnillquh.designclub.uk.com` |
| URL domain | `storage.googleapis.com` |
| Is the domain suspicious? | `yes` |
| Body SHA-256 | `13945ecc33afee74ac7f72e1d5bb73050894356c4bf63d02a1a53e76830567f5` |
| Is the email phishing? | `yes` |

---

# 2. Inspect the Email

The email presented itself as a PayPal-related reward notification.

The message used urgency and a reward theme to encourage the recipient to interact with the email.

The visible sender and message content were not sufficient to determine legitimacy, so the investigation moved to the underlying email data.

<img width="2159" height="1483" alt="image" src="https://github.com/user-attachments/assets/0ee17525-4629-4ebc-982b-8e211e24dfab" />


---

# 3. Analyze the Email Headers

The message source was opened to inspect the raw headers.

The important indicator was the `Return-Path`:

```text
bounce@rjttznyzjjzydnillquh.designclub.uk.com
```

<img width="2252" height="1467" alt="image" src="https://github.com/user-attachments/assets/45e9e7cb-32d1-4a44-8d89-16d2f23c3027" />


This is suspicious because the email presents itself as a PayPal-related message, while the return-path belongs to an unrelated domain with a highly random-looking subdomain.

### Answer

```text
bounce@rjttznyzjjzydnillquh.designclub.uk.com
```

---

# 4. Identify the Embedded URL

The email body contained a link hosted on Google's storage infrastructure.

The relevant domain was:

```text
storage.googleapis.com
```

The important distinction is between the **full URL** and the **domain** requested by the challenge.

The challenge expects only:

```text
storage.googleapis.com
```

rather than the complete path and query string.

---

# 5. Investigate the Domain and URL

VirusTotal was used to investigate the infrastructure.

A key lesson from this step is that a legitimate provider can be abused to host malicious content.

In this case, `storage.googleapis.com` is legitimate Google infrastructure, but that does not automatically make a specific resource hosted there trustworthy.

![Uploading image.png…]()


The investigation therefore focused on the actual content and HTTP response associated with the domain/resource rather than treating the reputation of Google as proof of legitimacy.

### Answer

```text
storage.googleapis.com
```

### Is the domain suspicious?

```text
yes
```

---

# 6. Identify the Body SHA-256

The challenge specifically asked for the **Body SHA-256 of the domain**.

The relevant value shown in the investigation was:

```text
13945ecc33afee74ac7f72e1d5bb73050894356c4bf63d02a1a53e76830567f5
```

![Uploading image.png…]()


### Important distinction

The Body SHA-256 is **not** the SHA-256 of the URL string itself.

It represents the hash associated with the HTTP response body shown by the analysis platform.

### Answer

```text
13945ecc33afee74ac7f72e1d5bb73050894356c4bf63d02a1a53e76830567f5
```

---

# 7. Determine Whether the Email Is Phishing

The final classification was:

```text
yes
```

The decision was supported by multiple indicators:

- PayPal branding/theme combined with an unrelated return-path.
- A random-looking sender infrastructure domain.
- An embedded URL hosted on infrastructure unrelated to the claimed brand.
- The URL/domain investigation provided additional suspicious context.
- The message used a reward/urgency-based social-engineering lure.

![Challenge result](screenshots/05_challenge_answers.png)

---

# 8. Final Indicator Summary

```text
Return-Path:
bounce@rjttznyzjjzydnillquh.designclub.uk.com

URL domain:
storage.googleapis.com

Domain suspicious:
yes

Body SHA-256:
13945ecc33afee74ac7f72e1d5bb73050894356c4bf63d02a1a53e76830567f5

Final verdict:
PHISHING
```

---

# 9. Investigation Chain

```text
Suspicious PayPal-themed email
            │
            ├── Email headers
            │      └── Suspicious Return-Path
            │
            ├── Email body
            │      └── Embedded Google Storage URL
            │
            ├── VirusTotal
            │      ├── Domain / URL analysis
            │      └── Body SHA-256
            │
            └── Correlated indicators
                   │
                   └── Phishing verdict
```

---

# 10. Conclusion

The email was classified as phishing through **indicator correlation**, not by relying on a single suspicious characteristic.

The most important analytical step was moving from the visible email to the underlying technical evidence:

**message → headers → URL → infrastructure → content hash → verdict**

This is a useful SOC workflow because phishing investigations often require combining several weak or moderate indicators into a defensible final classification.
