# SOC Fundamentals

**Platform:** LetsDefend | **Path:** SOC Analyst | **Status:** Completed (9 lessons + quiz)
**Goal:** Understand how a Security Operations Center works, who does what, and which tools an analyst uses every day.

---

## 1. Introduction to SOC
A **SOC (Security Operations Center)** is the team, process, and technology that **monitors, detects, investigates, and responds** to security threats, usually 24/7. It rests on three things: **People** (analysts), **Process** (playbooks, escalation rules), and **Technology** (SIEM, EDR, SOAR, and more).

## 2. SOC Types and Roles
**Types of SOC**
- **In-house:** the company runs its own SOC (full control, higher cost)
- **Outsourced (MSSP):** a provider monitors for you (cheaper, less customized)
- **Hybrid:** a mix of both

**Typical roles**
| Role | What they do |
|------|--------------|
| Tier 1 analyst | Watches alerts, does first triage, escalates what's real |
| Tier 2 analyst | Deeper investigation and response to escalated incidents |
| Tier 3 / threat hunter | Proactively hunts threats, handles advanced cases |
| SOC manager | Runs the team, metrics, and reporting |
| Supporting roles | Security engineers, threat intelligence, forensics |

## 3. SOC Analyst and Their Responsibilities
- Monitor alerts and **triage** them (is it real or not?)
- Investigate using logs and tools, then **escalate** or close
- Follow **playbooks** so every incident is handled consistently
- **Document** every step (what I checked, what I found, why I decided)
- Help **tune** detections to reduce false alarms

## 4. SIEM and Analyst Relationship
A **SIEM** (Security Information and Event Management) collects logs from many systems in one place, **correlates** them, and raises **alerts**. Examples: Splunk, QRadar, Elastic, Wazuh.

Every alert ends up as one of four outcomes:
| | Real attack | No attack |
|---|---|---|
| **Alert raised** | True Positive | False Positive |
| **No alert** | False Negative (the dangerous one) | True Negative |

**Why it matters:** The SIEM only *points* at something. The analyst decides whether it's real by checking context.

## 5. Log Management
Logs are the raw evidence. Good log management means **collecting** them from many sources (firewalls, proxies, DNS, servers, endpoints, email), **normalizing** them into a common format, **storing** them for a set retention period, and making them **searchable**.

Two details that matter in investigations: clocks must be **time-synchronized** (otherwise events can't be put in order), and logs must be **kept long enough** to look back at an incident.

## 6. EDR (Endpoint Detection and Response)
EDR runs an **agent on each endpoint** (laptop, server) and records process, file, registry, and network activity. Unlike classic antivirus that mainly blocks known malware, EDR gives **visibility and response**: an analyst can see what a program did, **isolate the host**, kill a process, and collect evidence remotely.

## 7. SOAR (Security Orchestration, Automation and Response)
SOAR connects security tools and **automates repetitive tasks** using playbooks (for example, auto-checking an IP against threat intel, then opening a ticket). It saves analysts' time and speeds up response.

## 8. Threat Intelligence Feed
Feeds supply **IOCs (Indicators of Compromise)**: malicious IPs, domains, URLs, and file hashes. Analysts use them to check "have we seen this before, and is it known bad?" Common sources include VirusTotal, AbuseIPDB, and MISP.

**Caution:** IOCs go stale (an IP can be reused by someone innocent), so look at how recent and how trusted the source is.

## 9. Common Mistakes Made by SOC Analysts
Typical pitfalls to avoid (compare with your course notes):
- Trusting a tool's verdict without verifying it
- Closing alerts too quickly without enough investigation
- Not documenting what was done and why
- Failing to escalate when unsure
- Assuming "internal means safe"
- Ignoring low-severity alerts that may be part of a bigger story

---

## My own takeaways
- A SOC is people, process, and technology. Tools alone don't protect anyone, and analysts follow a process.
- The SIEM raises the alert, but I'm the one who decides if it's a true or false positive. Context decides it.
- A **false negative** (a real attack with no alert) is the scariest outcome, which is why detection tuning matters.
- Logs are only useful when they are complete, time-synced, and kept long enough.
- EDR gives me what happened *on the endpoint*, and the SIEM gives me what happened *across the environment*. I usually need both.
- Always document my reasoning, because the next analyst (or I, later) has to understand why I closed or escalated.
- Don't trust one data point. Verify with a second source before deciding.

**Quiz score:** _(add yours)_
**What confused me / what I want to revisit:** _(add yours)_

## Related notes
- [Phishing Email Analysis](02-phishing-email-analysis.md)
- [Windows Fundamentals](../career-switch-path/02-windows-fundamentals.md)
