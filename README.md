# Digital Forensics & Incident Response

Structured incident response documentation, forensic analysis, and security playbooks.

## Incident Reports

Complete incident documentation following industry-standard formats.

| Case ID | Type | Severity | Status | Key Techniques |
|---------|------|----------|--------|----------------|
| [CASE-001](./incident-reports/CASE-001.md) | Brute Force → Compromise | High | Escalated | T1110, T1078, T1041 |
| [CASE-002](./incident-reports/CASE-002.md) | Ransomware (Hive) | Critical | Contained | T1486, T1490 |
| CASE-003 | Insider Threat | Medium | Closed | T1083, T1567 |
| CASE-004 | Malware Infection | Critical | Contained | T1204, T1059 |
| CASE-005 | Credential Dumping | High | Escalated | T1003, T1005 |

### Report Structure

Each incident report includes:

1. **Executive Summary**
   - 3-sentence brief for management
   - Business impact statement
   - Immediate actions taken

2. **Incident Timeline**
   - Minute-by-minute attack progression
   - Detection and response timestamps
   - Evidence of attacker activity

3. **Technical Analysis**
   - Initial access vector
   - Lateral movement path
   - Persistence mechanisms
   - Data access and exfiltration

4. **Indicators of Compromise (IOCs)**
   - Network indicators (IPs, domains)
   - Host indicators (file hashes, registry keys)
   - Behavioral indicators (processes, commands)

5. **Impact Assessment**
   - Systems affected
   - Data at risk or exfiltrated
   - Business disruption
   - Regulatory implications

6. **Containment & Eradication**
   - Immediate containment actions
   - System isolation decisions
   - Malware removal
   - Account remediation

7. **Recovery**
   - Service restoration
   - Verification of clean state
   - Return to normal operations

8. **Lessons Learned**
   - Detection gaps identified
   - Process improvements
   - Recommended controls

## Phishing Analysis

Email security investigations and triage documentation.

| Case ID | Type | Verdict | Complexity |
|---------|------|---------|------------|
| [PHISH-001](./phishing-analysis/PHISH-001.md) | Credential Harvesting | Malicious | Medium |
| [PHISH-002](./phishing-analysis/PHISH-002.md) | Malicious Attachment | Malicious | High |
| [PHISH-003](./phishing-analysis/PHISH-003.md) | Business Email Compromise | Suspicious | Medium |
| PHISH-004 | Fake Invoice | Benign | Low |
| PHISH-005 | QR Code Phishing | Malicious | Medium |

### Analysis Methodology

**1. Header Analysis**
- SPF (Sender Policy Framework) validation
- DKIM (DomainKeys Identified Mail) verification
- DMARC (Domain-based Message Authentication) alignment
- Received chain analysis (routing path)
- X-originating IP identification

**2. Content Review**
- Urgency tactics and social engineering
- Grammar and spelling indicators
- Impersonation signs (display name vs email address)
- Link inspection and redirect chains
- Attachment analysis

**3. Infrastructure Analysis**
- Domain age and registration details
- Hosting provider reputation
- URL redirect chains
- SSL certificate analysis

**4. Attachment Analysis**
- File hash calculation
- Sandbox detonation (Any.Run, Hybrid Analysis)
- Macro analysis (VBA deobfuscation)
- Static analysis indicators

### Verdict Classifications

| Verdict | Definition | Action |
|---------|------------|--------|
| **Benign** | Legitimate email | Deliver to recipient |
| **Spam** | Unwanted but not malicious | Quarantine, optional user notification |
| **Suspicious** | Potential threat requiring review | Quarantine, analyst investigation |
| **Malicious** | Confirmed threat | Delete, block IOCs, user training |

## Malware Triage

Initial malware analysis and classification.

**Triage Levels:**
- **Level 1:** Automated analysis (sandbox, hash lookup)
- **Level 2:** Static analysis (strings, PE headers, imports)
- **Level 3:** Dynamic analysis (behavior in sandbox)
- **Level 4:** Reverse engineering (disassembly, debugging)

**Analysis Process:**
1. Sample intake and hash verification
2. Reputation checking (VirusTotal, etc.)
3. Sandbox detonation and behavior analysis
4. String extraction and IOC identification
5. Network traffic analysis
6. Persistence mechanism identification
7. Remediation guidance

## Forensic Artifacts

Preserved evidence and forensic indicators.

**Artifact Types:**
- Memory dumps
- Disk images
- Log files (preserved)
- Malware samples
- Network captures
- Registry hives
- File system metadata

**Chain of Custody:**
- Collection timestamp
- Collector identification
- Storage location
- Access logging
- Hash verification

## Response Playbooks

Standardised response procedures for common incident types.

**Available Playbooks:**
- Phishing response
- Malware containment
- Ransomware response
- Credential compromise
- Insider threat
- Data exfiltration
- Business Email Compromise (BEC)

**Playbook Components:**
1. Detection criteria
2. Initial response steps
3. Investigation procedures
4. Containment options
5. Eradication checklist
6. Recovery verification
7. Communication templates
8. Lessons learned capture

---

*Incident response is about controlled chaos. Preparation and documentation turn panic into process.*
