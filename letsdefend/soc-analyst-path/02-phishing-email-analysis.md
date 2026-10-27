# Phishing Email Analysis

**Platform:** LetsDefend | **Path:** SOC Analyst | **Status:** Completed (7 lessons + quiz)
**Goal:** Learn how to examine a suspicious email step by step, since phishing is the most common attack vector.

---

## 1. Introduction to Phishing
Phishing is tricking someone into giving up credentials, running malware, or sending money, usually by pretending to be someone trusted.
- **Mass phishing:** the same email sent to many people
- **Spear phishing:** targeted at one person or team
- **Whaling:** targets executives
- **BEC (Business Email Compromise):** impersonating a boss or vendor to request payments
- **Smishing / vishing:** the same idea over SMS or phone calls

**Common red flags:** urgency or threats, mismatched sender, unexpected attachments, links that don't match their text, and generic greetings.

## 2. Information Gathering
Before deep analysis, collect the basics: **sender address, subject, body, links, attachments, and who received it**.
- Check the **reputation** of the sender domain, IPs, and URLs (VirusTotal, URLScan, AbuseIPDB)
- Check the **domain age** with WHOIS (newly registered domains are suspicious)
- Search the email gateway: **did anyone else get the same email?**

## 3. What Is an Email Header and How to Read It
The header is the email's "travel log." Key fields:
| Field | Meaning |
|-------|---------|
| From | The sender shown to the user (easy to fake) |
| Reply-To | Where replies actually go |
| Return-Path | Where bounces go |
| Received | Each server the email passed through (read **bottom to top**) |
| Message-ID | Unique ID, often reveals the real sending system |
| Authentication-Results | SPF, DKIM, DMARC results |

## 4. Email Header Analysis
**The three authentication checks**
- **SPF:** was the sending server allowed to send for this domain?
- **DKIM:** is the message signed and unaltered?
- **DMARC:** what to do if SPF/DKIM fail, aligned with the From domain

**What looks suspicious:** From and Return-Path/Reply-To pointing to different domains, SPF/DKIM/DMARC failures, a sending IP that doesn't match the claimed company, and lookalike domains (`paypa1.com`, `micros0ft-support.com`).

**Tools:** MXToolbox Header Analyzer, Google Messageheader.

## 5. Static Analysis
Examining the email and attachments **without running anything**.
- Inspect URLs without clicking, and **defang** them when writing them down: `hxxp://example[.]com`
- Compute the attachment's **hash (SHA256)** and look it up on VirusTotal
- Check whether the real file type matches the extension (an `.pdf` that's really an `.exe`)
- Office documents: look for **macros** (tools like `olevba`, `oledump`)
- View the HTML source for hidden links or redirects

## 6. Dynamic Analysis
Running the suspicious link or file in a **safe, isolated sandbox** to watch what it does: network connections, files dropped, processes started, registry changes.
- Sandboxes: Any.Run, Hybrid Analysis, Joe Sandbox, URLScan
- **Never open suspicious files on your own computer.** Use an isolated VM or an online sandbox.

## 7. Additional Techniques
- Spot **lookalike (homograph) domains**, URL shorteners, and QR-code links
- Check **proxy, DNS, and EDR logs** to see whether anyone **clicked or opened** it
- Look for other recipients and delete or quarantine the email from all mailboxes

---

## A simple handling workflow
1. **Collect** the basics (sender, links, attachments, recipients)
2. **Check the header** (authentication, mismatched fields)
3. **Static analysis** (reputation, hashes, defanged URLs)
4. **Dynamic analysis** in a sandbox if still unclear
5. **Decide:** malicious or safe, and document why
6. **Respond:** if anyone clicked or opened it, isolate the host and reset credentials; block the IOCs; remove the email from all mailboxes

## Practice alert in the course
- SOC141 - Phishing URL Detected: _(add your own reflection on how you approached it. I'm not posting the answers, to respect the platform's rules.)_

---

## My own takeaways
- The **From** field is easy to fake. The header's authentication results (SPF, DKIM, DMARC) and the `Received` chain tell the real story.
- Read `Received` lines **bottom to top** to follow the email's actual path.
- Static analysis first, dynamic analysis second. I never run suspicious files on my own machine.
- Defang URLs and IPs in reports so nobody clicks them by accident.
- The most important question after "is it malicious?" is "**did anyone click or open it?**" That decides whether I need to isolate a host or reset passwords.
- Look for other recipients. One phishing email is usually part of a campaign.
- Document each step, so the decision is clear to the next analyst.

**Quiz score:** _(add yours)_
**What confused me / what I want to revisit:** _(add yours, for example reading Received chains)_

## Related notes
- [SOC Fundamentals](01-soc-fundamentals.md)
