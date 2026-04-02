# Cloud Armor: ACE Exam Study Guide (2026)

## 1. Cloud Armor Overview

Cloud Armor is Google Cloud's network security service that provides Web Application Firewall (WAF) and Distributed Denial of Service (DDoS) protection at scale.

- **Primary Purpose:** Protect web applications and services from common internet-based threats, including DDoS attacks and application-layer (Layer 7) attacks.
- **Integration:** Cloud Armor security policies are applied to **Backend Services** of a **Global External HTTP(S) Load Balancer** (Classic or Envoy-based).
- **Enforcement:** Traffic is inspected and filtered at the Google Cloud edge, before it reaches your backend instances.
- **Gemini in Cloud Armor:** Can help analyze attack patterns, suggest security policy improvements, and assist in interpreting security logs.

## 2. Security Policies and Rules

A security policy is a container for rules that define how to filter traffic.

- **Policy Types:**
  - **Backend Security Policy:** Applied to traffic reaching backend services.
  - **Edge Security Policy:** Applied to traffic at the Google Cloud edge (e.g., for filtering traffic to Cloud Storage buckets behind a Load Balancer).
- **Rule Components:**
  - **Priority:** Rules are evaluated from lowest to highest numerical value (0 is the highest priority).
  - **Match Condition:** Can be an IP address/range, or a complex expression (using Common Expression Language - CEL).
  - **Action:** `allow`, `deny` (403, 404, 502), `redirect`, or `throttle`.
  - **Preview Mode:** Allows you to test the rule without actually blocking traffic (logs are generated, but the rule action is not enforced).

## 3. Web Application Firewall (WAF) Capabilities

Cloud Armor includes preconfigured WAF rules to protect against common web attacks:

- **SQL Injection (SQLi)**
- **Cross-Site Scripting (XSS)**
- **Remote File Inclusion (RFI)**
- **Local File Inclusion (LFI)**
- **Remote Code Execution (RCE)**
- **Protocol Attack / Scanner Detection**
- **Exam Tip:** You should know that Cloud Armor can mitigate the "OWASP Top 10" risks using these preconfigured rule sets.

## 4. Managed Protection Tiers

- **Cloud Armor Standard:**
  - Pay-as-you-go pricing.
  - Always-on DDoS protection for Layer 3 and Layer 4 attacks.
  - Access to WAF rules and IP filtering.
- **Cloud Armor Enterprise (Managed Protection Plus):**
  - Subscription-based pricing.
  - Advanced DDoS protection (including Layer 7 protection).
  - **Adaptive Protection:** Uses machine learning to detect and mitigate anomalous traffic patterns.
  - DDoS cost protection (billing credits for traffic spikes caused by DDoS).
  - Bot Management integration (reCAPTCHA Enterprise).

## 5. Monitoring and Logging

- **Cloud Logging:** Every decision made by Cloud Armor (allow/deny) is logged.
- **Security Policy Logs:** Contain information about the rule that matched, the source IP, and the action taken.
- **Cloud Monitoring:** Dashboards showing request rates, blocked requests, and attack trends.

## 6. Key Exam Scenarios

- **DDoS Mitigation:** If a question asks how to protect a web app from a massive volume of traffic, Cloud Armor is the answer.
- **IP Whitelisting/Blacklisting:** Use Cloud Armor security policies to allow only specific corporate IP ranges to access a backend service.
- **Geo-fencing:** Creating a rule to deny traffic from specific countries using the `origin.region_code` attribute.
- **Troubleshooting:** If a legitimate user is getting a 403 error, check the Cloud Armor logs to see if a WAF rule is incorrectly blocking the traffic (false positive).

## 7. Essential gcloud Commands

- **Create a Security Policy:** `gcloud compute security-policies create [NAME] --description="[DESC]"`
- **Add an IP Rule:** `gcloud compute security-policies rules create [PRIORITY] --security-policy=[POLICY] --src-ip-ranges="[IP_RANGE]" --action="deny-403"`
- **Add a Preconfigured WAF Rule:** `gcloud compute security-policies rules create [PRIORITY] --security-policy=[POLICY] --expression="evaluatePreconfiguredExpr('sqli-stable')" --action="deny-403"`
- **Update a Rule:** `gcloud compute security-policies rules update [PRIORITY] --security-policy=[POLICY] --action="allow"`
- **Attach to Backend Service:** `gcloud compute backend-services update [BACKEND_SERVICE] --security-policy=[POLICY] --global`
