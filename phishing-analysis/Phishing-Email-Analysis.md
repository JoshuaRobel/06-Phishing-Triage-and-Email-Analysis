# Phishing Email Analysis Procedures

**Version:** 1.4  
**Last Updated:** 2026-02-19  
**Classification:** Internal Use

---

## Phishing Email Analysis Overview

Phishing emails are the primary attack vector for initial compromise. Rapid analysis enables quick user warnings and security improvements.

---

## Email Header Analysis

### Critical Header Fields

```
From: "CFO" <cfoadmin@targetcompany.xyz> (SPOOFED!)
├─ Display name: "CFO" (social engineering)
├─ Email address: cfoadmin@targetcompany.xyz (attacker domain)
├─ Red flag: Not @targetcompany.com (legitimate domain)
└─ Verdict: SPOOFED - attacker pretending to be CFO

Return-Path: <bounces-attacker@mail-server.xyz>
├─ Where would bounces go?
├─ Should match From: address
├─ If different: May indicate spoofing
└─ This email: Different return path = SPOOFED

DKIM-Signature: v=1; a=rsa-sha256; ...
├─ Digital signature of email
├─ Validates: Email not modified in transit
├─ Missing signature: Email may have been altered
├─ FAIL: Signature doesn't verify = SPOOFED

SPF: Pass / Fail
├─ Sender Policy Framework
├─ Checks: Sending IP authorized by domain
├─ Result: FAIL = Email not from claimed domain
└─ Verdict: Likely spoofed

DMARC: Pass / Fail / None
├─ Domain-based Message Authentication
├─ Policy: How to handle failed SPF/DKIM
├─ Result: FAIL = Domain doesn't allow this email
└─ Verdict: Likely spoofed/unauthorized

TLS: Yes / No
├─ Connection encrypted?
├─ Good: Email not visible to ISP
├─ Not critical for security (not about content)
└─ TLS: Yes (expected for legitimate email)
```

### Analyzing Return-Path

```
Example Email with Spoofing:

From: "Chief Financial Officer" <cfo@targetcompany.xyz>
Return-Path: <bounces@free-email.xyz>

Analysis:
├─ Display From: Looks official (CFO)
├─ Email From: Wrong domain (.xyz, not .com)
├─ Return-Path: Free email service
├─ Combination: Obvious spoofing attempt

How attacker created email:
├─ Registered fake domain: targetcompany.xyz (similar to .com)
├─ Created email account: cfo@targetcompany.xyz
├─ Sent email to employees
├─ Victims: May not notice "xyz" vs "com" in quick glance
└─ Result: Employees trust email from "CFO"

Detection Queries:
PowerShell:
Get-ChildItem -Path "Inbox" |
  Where {$_.From -notcontains "@targetcompany.com"} |
  Where {$_.Subject -contains "Urgent"}

Result: Find all emails from unauthorized domains
└─ Flag: Potential spoofing attempt
```

---

## Content Analysis

### Identifying Malicious Attachments

```
Suspicious Attachment Analysis:

Attachment: "Invoice_2026.zip" (SUSPICIOUS!)

Step 1: Initial Assessment
├─ File extension: .zip (archive, can hide malware)
├─ Filename: Invoice_2026.zip (looks legitimate)
├─ File size: 512 KB (reasonable for document)
├─ Sent from: Unknown external address
└─ Red flag: Archives often hide malware

Step 2: Virtual Environment Extraction
├─ Extract in isolated sandbox: Do NOT open on work computer
├─ Contents: WinRAR should show: "Invoice_2026.exe"
├─ Executable in ZIP: MAJOR RED FLAG
├─ Disguised as: .exe masquerades as invoice (social engineering)
└─ Verdict: MALWARE DROPPER

Step 3: Hash Analysis
├─ Calculate MD5: a1b2c3d4e5f6g7h8
├─ VirusTotal lookup:
│  ├─ Detection: 42/70 engines = MALWARE
│  ├─ Verdict: Confirmed malware
│  └─ Malware name: Emotet banking trojan
└─ Recommendation: Block hash globally

Step 4: Behavioral Analysis
├─ Sandbox execution: Run in Cuckoo
├─ Behavior observed:
│  ├─ Connects to C2: 203.0.113.42:8080
│  ├─ Creates service: "Windows Update Service"
│  ├─ Modifies registry: Persistence mechanisms
│  └─ Verdict: Banking trojan confirmed
└─ Recommendation: Full incident response

Blocking Actions:
├─ Email gateway: Block attachment hash
├─ Endpoint AV: Update signatures
├─ SIEM: Create alert for attachment hash
└─ User warning: "Malware detected in email"
```

### Identifying Phishing Links

```
Suspicious Link Analysis:

Email Body: "Click here to verify your account"
Link: https://account-verify.company-lookalike.xyz/login

Analysis:

Step 1: Link Inspection
├─ Displayed text: "Click here to verify your account"
├─ Actual URL: https://account-verify.company-lookalike.xyz/login
├─ Red flag: Displayed text doesn't match actual URL
├─ Domain: company-lookalike.xyz (NOT company.com)
├─ Verdict: PHISHING LINK (credential harvesting)

Step 2: Domain Analysis
├─ Registrant: GoDaddy (privacy enabled - attacker identity hidden)
├─ Registration date: 2026-02-10 (recent, attacker-registered)
├─ Certificate: Self-signed (not from trusted CA)
├─ Website: Fake login page (copy of company.com)
├─ Purpose: Harvest user credentials
└─ Verdict: Credential phishing confirmed

Step 3: Certificate Analysis
├─ Certificate issuer: Self-signed (red flag)
├─ Valid domain: company-lookalike.xyz (not company.com)
├─ Expiration: 2026-03-10 (short validity, attacker planning)
├─ Browser warning: "Invalid certificate" (users may ignore)
└─ Verdict: Attackers trying to look legitimate

Step 4: Threat Assessment
├─ Objective: Steal username/password
├─ Likely next step: Account compromise + lateral movement
├─ Data at risk: Email, files, sensitive information
├─ Severity: HIGH (credential compromise enables full breach)
└─ Recommendation: User warning + account monitoring

Blocking Actions:
├─ Email gateway: Block all links to company-lookalike.xyz
├─ Web filter: Block domain
├─ DNS: Sinkhole domain
├─ User notification: "Phishing email detected"
├─ Account monitoring: Check for unauthorized access
└─ User training: Educate on domain name verification
```

---

## Phishing Email Triage

### Quick Assessment Template

```
Phishing Email Report Form:

1. SENDER ANALYSIS:
   From: [email address]
   Domain: [extract domain]
   DKIM: [Pass/Fail]
   SPF: [Pass/Fail]
   DMARC: [Pass/Fail]
   Verdict: [Legitimate/Spoofed/Unknown]

2. CONTENT ANALYSIS:
   Subject: [subject line]
   Suspicious phrases: [any urgency/threats?]
   Requests credentials: [Y/N]
   Requests money: [Y/N]
   Requests downloads: [Y/N]
   Verdict: [Phishing/Legitimate/Unknown]

3. ATTACHMENT ANALYSIS:
   Filename: [attachment name]
   Type: [.zip/.exe/.docm/.pdf/etc.]
   Hash (MD5): [hash]
   VirusTotal: [detection rate]
   Verdict: [Malware/Suspicious/Legitimate]

4. LINK ANALYSIS:
   Displayed text: [what user sees]
   Actual URL: [real URL]
   Domain: [extract domain]
   TLS certificate: [Valid/Self-signed/Invalid]
   Verdict: [Phishing/Legitimate/Unknown]

5. OVERALL VERDICT:
   Risk level: [CRITICAL/HIGH/MEDIUM/LOW]
   Recommended action: [Block/Warn/Monitor/None]
   User notification: [Y/N]

6. EVIDENCE:
   Screenshot: [Y/N]
   Header extraction: [Y/N]
   Sample preserved: [Y/N]
```

---

## Real-World Phishing Examples

### Example 1: Credential Phishing Campaign

```
INCIDENT: Phishing campaign targeting employees

Email Details:
├─ From: noreply@microsft-account.xyz (TYPO: microsft, not microsoft)
├─ Subject: "ACTION REQUIRED: Verify Your Microsoft Account"
├─ Body: "Your account will be suspended in 24 hours..."
├─ Link: "https://account-verification.microsft-verify.xyz/login"
└─ Attachment: None

Red Flags:
├─ Domain: "microsft-account.xyz" (typosquatting)
├─ Urgency: "24 hours" (pressure to act fast)
├─ Unusual request: Verify account (already verified)
├─ New domain: microsft-verify.xyz (registered 2026-02-15)
└─ Verdict: Credential phishing

Attack Analysis:
├─ Objective: Harvest email + password
├─ Next step: Lateral movement to Office 365
├─ Data at risk: Email, OneDrive, Teams, Calendar
└─ Impact: Full company email compromise

Response:
├─ Block microsft-account.xyz at firewall
├─ Update DNS: Sinkhole domain
├─ User notification: "Phishing detected"
├─ Reset password: For any users who clicked
├─ Monitor: Office 365 access logs for suspicious activity
└─ Training: Educate on domain verification

Detection Rate:
├─ Emails sent: 500 (entire company)
├─ Users clicked: 12 (2.4% - good awareness)
├─ Users entered credentials: 5 (1% - concerning)
├─ Automated block: 450 (before users saw it)
└─ Verdict: Campaign partially successful despite defenses
```

### Example 2: Spear Phishing - Executive Impersonation

```
INCIDENT: Executive impersonation attack

Email Details:
├─ From: "CEO" <ceo@company-lookalike.xyz>
├─ Subject: "Urgent: Wire Transfer Needed Today"
├─ Body: "Transfer $500K to [attacker bank account]"
├─ CC: (Empty - no verification possible)
└─ Attachment: None

Red Flags:
├─ Domain: company-lookalike.xyz (not company.com)
├─ Urgency: "Needed Today" (pressure tactic)
├─ Unusual request: Wire transfer (financial fraud)
├─ No verification: No CC'd CFO, finance team
├─ Orphan email: No prior conversation
└─ Verdict: CEO impersonation fraud

Attack Analysis:
├─ Objective: Financial theft ($500K)
├─ Target: Finance team member (decision maker)
├─ Social engineering: Trust in CEO authority
├─ Risk: Could result in actual wire transfer
└─ Impact: $500K loss if successful

How It Worked:
├─ Attacker researched company structure
├─ Identified: CEO, Finance Director, CFO
├─ Created spoofed email: ceo@company-lookalike.xyz
├─ Sent directly to: Finance person (decision authority)
├─ Pressure: Urgent deadline (no time to verify)
└─ Threat: Works because it bypasses normal procedures

Response:
├─ Block sender domain
├─ Verify with CEO: Did you send this? (NO!)
├─ Halt any wire transfers (audit outgoing transfers)
├─ User notification: "CEO impersonation attack"
├─ Process improvement: CEO requests for wire require verification call
└─ Training: Educate on CEO fraud tactics

Lessons Learned:
├─ Domain verification critical (company vs company-lookalike)
├─ Never trust display name alone
├─ Financial requests need secondary verification
├─ Pressure tactics signal phishing
└─ CFO/CEO accounts require additional authentication
```

---

## User Training Content

```
What to look for in phishing emails:

RED FLAGS:

1. Domain Mismatch:
   ✗ From: support@compny.xyz (missing 'a')
   ✓ Real: support@company.com
   Lesson: Always check full domain, not just company name

2. Unusual Requests:
   ✗ "Verify your account password" (why?)
   ✓ Real: "You changed your password successfully"
   Lesson: Legitimate companies don't ask for passwords

3. Urgency & Threats:
   ✗ "Your account will be suspended in 24 hours!"
   ✓ Real: Normal tone, no pressure
   Lesson: Phishers create panic to bypass careful thinking

4. Suspicious Attachments:
   ✗ .zip, .exe, .scr files (can hide malware)
   ✓ Real: .pdf, .docx (but verify sender first)
   Lesson: Unexpected attachments are red flag

5. Malformed Grammar:
   ✗ "Plase confirm you're account information"
   ✓ Real: Professional, no typos
   Lesson: Legitimate companies proofread

Best Practices:

✓ DO:
├─ Hover over links before clicking (see real URL)
├─ Verify sender with known contact method
├─ Check email headers for authentication (DKIM, SPF)
├─ Use multi-factor authentication
├─ Report suspicious emails to IT security
└─ Think before clicking (take time to verify)

✗ DON'T:
├─ Click links from unknown senders
├─ Open attachments from untrusted sources
├─ Reply with sensitive information
├─ Disable security warnings
├─ Trust display names (easily spoofed)
└─ Act on urgent requests (take time to verify)
```

---

## References

- SANS Phishing Analysis Guide
- Email Header Analysis Techniques
- Malware Analysis Fundamentals

---

*Document Maintenance:*
- Update phishing tactics as they evolve
- Review real phishing samples quarterly
- Update user training based on new tactics
- Track phishing metrics monthly
