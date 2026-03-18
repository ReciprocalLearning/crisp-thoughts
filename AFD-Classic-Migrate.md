###### *Disclaimer: The views and opinions expressed in blog post are solely those of the individual authors and do not necessarily reflect the official policy or position of any organization. The organization is not responsible for any errors or omissions in the content of the blog posts or for any damages or losses that may arise from reliance on the information contained in them. The organization does not endorse or guarantee the accuracy, completeness, or usefulness of any information presented in the blog posts, nor does it warrant the validity of any advice, opinion, or statement provided therein. Readers are advised to independently verify any information presented here with thier scenarios and to seek professional advice before acting on any information contained in them.*

# Navigating the 2026 TLS Modernization: A Guide for Azure Front Door Classic Customers

## The Big Picture:
If you’ve noticed upcoming changes to your **Azure Front Door (Classic)** certificates or validation methods, you aren't alone. We are currently in the middle of a global "security spring cleaning."

The entire internet—led by the **CA/Browser Forum** (Google, Microsoft, Apple, and Mozilla)—is moving toward a more resilient trust model. These changes, including the removal of certain "multi-purpose" certificate features and the requirement for global validation (**MPIC**), are mandatory for all Certificate Authorities (CAs) like DigiCert. Also, The removal of the Client Authentication Extended Key Usage (EKU) is a part of this change as well.

**Like Others, Azure is implementing these global rules to ensure your site stays "green" and trusted in every browser worldwide.**

These are convergence of multiple security and platform changes like -

 1. CNAME Expiration & Validation Changes (The MPIC Impact)
 2. Managed Certificate Expiration (The G2 Forcing Function)
---

## The "No-Panic" Reality Check
* **Standard Web Traffic:** If your customers visit your site via a browser (Chrome, Edge, Safari), they are **100% safe**. Browsers do not require the features being removed.
* **The "Why":** These changes prevent "routing & BGP hijacks" where a hacker might try to trick a single data center into issuing a fake certificate for your domain. 

---
## Understand the Timeline
 ![Diagram](https://github.com/ReciprocalLearning/crisp-thoughts/blob/main/Images/AFD-classic-timeline.png)
## Key Deadlines & Impact Summary

| Date | Change Event | Impact to You |
| :--- | :--- | :--- |
| **April 1, 2026** | **DHE Cipher Retirement** | Legacy clients (older than 2015) may fail to connect. |
| **April 15, 2026** | **G1 Root Distrust** | Browsers will block sites still using legacy **DigiCert G1** roots. |
| **Ongoing (2026)** | **EKU Removal** | Managed certificates will no longer support internal "Client Login" tags. |
| **March 31, 2027** | **AFD Classic Retirement** | The Classic service officially ends; migration to **Standard/Premium** is required. |

---

## 3 Steps to Assess Your Environment

### Step 1: Check your "Root" Status
You need to know if your Front Door is already using the new "G2" root or if you are still on the legacy "G1."

Bash - Sample
```bash
openssl s_client -connect yourdomain.com:443 -showcerts | grep -i "DigiCert Global Root G2"
```
Powershell - Sample
```Powershell
$domain = "yourdomain.com"
$req = [System.Net.HttpWebRequest]::Create("https://$domain")
try { $req.GetResponse() | Out-Null } catch { }
$chain = New-Object System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($req.ServicePoint.Certificate) | Out-Null
$chain.ChainElements.Certificate | Where-Object { $_.Issuer -match "DigiCert Global Root G2" } | Select-Object Subject, Issuer, NotAfter
```

* **Result G2/G3:** You are already modernized. 
* **Result G1:** You are still on the legacy chain and must ensure your clients trust the **G2 root** before April 15.

### Step 2: Scan for "At-Risk" Legacy Clients
Use this sample **KQL query** in your **Azure Log Analytics** to find older clients (like Java 1.7 or old Python scripts) that might not trust the new global roots.

```kusto
// Find legacy clients that might fail the G2 Trust update
AzureDiagnostics
| where Category == "FrontDoorAccessLog"
| where TimeGenerated > ago(30d)
| extend UserAgent = userAgent_s
// Looking for Java 7, Python 2, Win7, or old curl versions
| where UserAgent matches regex @"(?i)(Java/1\.[0-7]|Python/2\.|Windows NT 6\.1|curl/7\.[0-5])"
| summarize RequestCount = count() by UserAgent
| order by RequestCount desc
```

### Step 3: Audit your Backend "Login" Logic
The removal of the **Client Authentication EKU** only breaks things if your backend (App Service/VM) specifically checks the Front Door certificate for a "Client Auth" tag.

* **The Check:** Ask your developers if they use "Mutual TLS" (mTLS) or custom code that validates the **OID 1.3.6.1.5.5.7.3.2**.
* **The Fix:** If they do, they must update the code to accept the **Server Auth OID (1.3.6.1.5.5.7.3.1)** instead.

---

## What’s Next?
Don't wait for the April deadlines. Use this month to:
1.  **Update CNAMEs:** If Azure asks you to re-validate a domain, do it immediately. This is the new global **MPIC** check in action.
2.  **Test G2 Trust:** Ensure any automated scripts or internal servers have the **DigiCert Global Root G2** installed.
3.  **Plan the Jump:** Azure Front Door (Classic) is retiring in 2027. Moving to **Standard/Premium** now solves many of these certificate headaches automatically.
    Review the Official Microsoft Migration Documentation to begin moving your profiles to the Standard/Premium tier.
    
    ![Migration process overview](https://learn.microsoft.com/en-us/azure/frontdoor/tier-migration)
    
    ![Using Azure Portal - Migrate Azure Front Door (classic) to Standard or Premium tier](https://learn.microsoft.com/en-us/azure/frontdoor/migrate-tier)

---
