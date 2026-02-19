# Ransomware Incident Response Playbook

**Version:** 1.5  
**Last Updated:** 2026-02-19  
**Classification:** Internal Use - IR Team Only

---

## Executive Summary

Ransomware attacks can shut down business operations within hours. This playbook provides step-by-step procedures for rapid containment and recovery.

---

## Pre-Incident Preparation

### Playbook Owner & Team

```
Playbook Owner: CISO / Security Director
Primary Responders:
├─ Incident Response Lead
├─ Security Operations Manager
├─ Network Engineer
├─ System Administrator
├─ Backup/Recovery Specialist
└─ Communications Manager

Legal/Compliance:
├─ General Counsel
├─ Compliance Officer
└─ Insurance Broker (cyber policy)

Executive Sponsors:
├─ CIO (operations impact)
├─ CFO (financial impact)
├─ CEO (reputation impact)
└─ Board of Directors (if critical breach)
```

### Required Tools & Resources

```
Isolation Tools:
├─ Network isolation switch (VLAN separation)
├─ Physical network cables (manual disconnection)
├─ Power backup (ensure no graceful shutdown gives attacker time)
└─ Out-of-band communication (phone, secure app)

Evidence Collection:
├─ Forensic write-blockers
├─ Portable SSD for memory dumps
├─ External USB drives
└─ Chain of custody forms

Response Tools:
├─ EDR console access (isolated from compromised network)
├─ SIEM access (isolated)
├─ Network baseline (stored offline)
├─ Clean rebuild media (pre-staged)
└─ Communication secure phone/encrypted email

Backup/Recovery:
├─ Verified clean backup location (offline)
├─ Recovery step-by-step guide
├─ Priority restore order (critical → important → standard)
└─ Restore timeline estimate (realistic)
```

### Threat Intelligence Integration

```
Ransomware Variants Tracked:

1. Conti Ransomware
   ├─ Encryption: AES-256
   ├─ Speed: 2-5 files/second
   ├─ Extortion: $100K-$2M demand
   ├─ Detection: .CONTI extension
   └─ Response: No negotiation, rebuild from backup

2. LockBit Ransomware
   ├─ Encryption: AES + RSA
   ├─ Speed: Very fast (10+ files/second)
   ├─ Extortion: Aggressive (threatens data release)
   ├─ Detection: .lockbit, .locked extensions
   └─ Response: FBI involvement recommended

3. BlackCat/ALPHV Ransomware
   ├─ Encryption: Modern (Rust-based)
   ├─ Speed: Fast (fully parallel)
   ├─ Extortion: High ($500K-$5M+)
   ├─ Detection: .ALPHV extension
   └─ Response: Multi-agency coordination
```

---

## Phase 1: DETECTION (First 5 Minutes)

### Initial Alert Indicators

```
Alert Source: EDR / SIEM / User Report

Typical Alert:
├─ Alert: "Bulk file encryption detected"
├─ Source: EDR agent on fileserver01
├─ Time: 23:47 UTC (Friday night - intentional timing!)
├─ Process: conti.exe spawned from services.exe
├─ Activity: 5,000+ files modified in 60 seconds

Initial Questions:
├─ Is this a false positive? (Test encryption, unlikely)
├─ Is this ransomware or legitimate activity? (Very unlikely legitimate)
├─ What systems are affected? (Just this one, or network-wide?)
└─ How long has this been running? (Minutes? Hours?)
```

### Immediate Actions (First 5 Minutes)

```
☐ VERIFY alert is real
  ├─ Check EDR agent status (is it active?)
  ├─ Check host is reachable (ping it)
  └─ Take screenshot of alert in EDR console

☐ DO NOT touch infected host
  ├─ DO NOT reboot (loses volatile memory)
  ├─ DO NOT try to clean manually
  ├─ DO NOT run antivirus on infected system
  └─ DO NOT delete malware binary (evidence!)

☐ ISOLATE host immediately
  ├─ Network cable disconnect (physical)
  ├─ Alternative: Disable network interface at BIOS
  ├─ DO NOT trust software disconnection
  └─ Verify: Host can no longer ping anything

☐ NOTIFY incident response team
  ├─ Call on-call IR coordinator
  ├─ Initiate incident bridge/war room
  ├─ Activate critical incident procedures
  └─ Alert executive sponsor (CIO/CEO)

☐ PRESERVE evidence
  ├─ Document exact time of detection
  ├─ Screenshot alert details
  ├─ Save alert/event logs
  └─ Begin chain of custody documentation
```

---

## Phase 2: ASSESSMENT (First 30 Minutes)

### Scope Determination

```
Critical Questions:

1. How many systems affected?
   ├─ SIEM search: Look for file encryption alerts
   ├─ Search: Bulk file modifications (5000+ files/min)
   ├─ Result: If 1 system → contained, if 100+ → full network breach
   └─ Action: Adjust containment strategy

2. What systems were targeted?
   ├─ Search SIEM for related alerts
   ├─ Identify: File servers, databases, workstations
   ├─ Check: Which departments affected (finance? operations?)
   └─ Action: Prioritize recovery based on criticality

3. How did infection start?
   ├─ Search for initial access (phishing email? RDP?)
   ├─ Timeline: When did infection begin?
   ├─ Initial compromised system: Which user/host?
   └─ Action: Understand attack vector for future prevention

4. Is attacker still active?
   ├─ Check SIEM for active C2 connections
   ├─ Search for ongoing lateral movement (4648 events)
   ├─ Check for new user account creation (4720)
   └─ Action: If active, continue containment
```

### Impact Assessment

```
Business Impact Analysis:

1. Operational Impact:
   ├─ What business functions are affected?
   ├─ How many users impacted?
   ├─ Timeline to business stoppage?
   └─ Estimated downtime duration?

2. Financial Impact:
   ├─ Revenue impact (lost sales per hour)
   ├─ Recovery costs (IR team time, external help)
   ├─ Ransom demand (expected $100K-$5M)
   └─ Regulatory fines (possible GDPR, etc.)

3. Reputational Impact:
   ├─ Customer notification required? (GDPR 72-hour rule)
   ├─ Public disclosure risk?
   ├─ Third-party breach notifications?
   └─ Media attention?

Example Impact Report:
┌─ Operation Down: 4 hours (estimated)
├─ Users affected: 500+ (entire office)
├─ Revenue loss: $100K/hour = $400K total
├─ Recovery cost: $50K (IR, external forensics)
├─ Ransom demand: $250K (initial, negotiable to $50K)
├─ Regulatory notification: YES (customer data affected)
└─ Severity: CRITICAL → Activate full IR procedures
```

---

## Phase 3: CONTAINMENT (0-2 Hours)

### Immediate Containment

```
TIER 1 - Immediate Isolation (within 5 minutes):

Infected Systems:
├─ Physically disconnect network cables
├─ Power off (or leave on for forensics, your choice)
├─ Isolate: Move to separate VLAN (air-gapped)
└─ Status: Contained (attacker cannot communicate)

Lateral Movement Prevention:
├─ Block C2 domains/IPs at firewall
├─ Block SMB (port 445) between network segments
├─ Block RDP (port 3389) from production to admin
├─ Block WMI (port 135) lateral movement
└─ Status: Attacker cannot spread further

Credential Revocation:
├─ Force password reset for all domain admins
├─ Force logout of all RDP/SSH sessions
├─ Disable service accounts used by attacker
├─ Reset local admin passwords on all systems
└─ Status: Stolen credentials no longer valid

TIER 2 - Extended Containment (within 1 hour):

Backup Protection:
├─ Disconnect backup systems from network
├─ Verify backup integrity (backup itself not encrypted?)
├─ Test restore from air-gapped backup
├─ Confirm: Can we actually recover from this backup?
└─ Status: Recovery path validated

Communication Security:
├─ Switch to out-of-band communication (not email!)
├─ Use secure phone line
├─ Use encrypted messaging (Signal, not company email)
├─ Reason: Email may be compromised
└─ Status: Communication channel protected
```

### Containment Decision Tree

```
Is ransomware still active?
│
├─ YES: Attacker still active
│  ├─ Continue isolation of all affected systems
│  ├─ Block ALL unnecessary network traffic
│  ├─ Assume lateral movement in progress
│  ├─ Isolate entire VLANs if needed
│  └─ Consider full network reset
│
└─ NO: Ransomware finished encrypting
   ├─ Attacker may be exfiltrating data now
   ├─ Monitor for outbound data transfer
   ├─ Block external IPs in threat intel
   ├─ Prepare for ransom demand (may come later)
   └─ Focus on recovery
```

---

## Phase 4: ERADICATION (2-24 Hours)

### Malware Removal

```
Eradication Strategy:

Option 1: Rebuild from Backup (Recommended)
├─ If backup is clean (pre-incident): Restore completely
├─ Steps:
│  ├─ Boot from clean installation media
│  ├─ Wipe disk completely (remove all files)
│  ├─ Fresh OS installation
│  ├─ Apply latest security patches
│  ├─ Restore data from clean backup (pre-incident)
│  ├─ Verify system functionality
│  └─ Return to production
├─ Time: 4-8 hours per system
├─ Risk: Low (completely clean system)
└─ Cost: Recovery time, not ransom

Option 2: Manual Cleanup (If backup unavailable)
├─ If forced to keep infected system:
├─ Steps:
│  ├─ Boot from Live CD (isolated, clean)
│  ├─ Remove malware executable
│  ├─ Remove persistence (scheduled tasks, services, registry)
│  ├─ Full antivirus scan
│  ├─ Rootkit scan (secondary tool)
│  └─ Monitor closely for re-infection
├─ Time: 2-3 hours per system
├─ Risk: High (may miss persistence)
├─ Cost: Manual effort, risk of incomplete cleanup
└─ Recommendation: AVOID this option if possible

Option 3: Negotiated Decryption (Not Recommended)
├─ If attacker provides decryption key:
├─ Risks:
│  ├─ No guarantee key works
│  ├─ Attacker may have backdoor for re-infection
│  ├─ May require malware to decrypt (risky)
│  ├─ Funds criminal enterprise (enables more attacks)
│  └─ FBI/law enforcement may investigate
├─ Only if: NO backup available AND critical business failure
└─ Recommendation: Avoid paying ransom
```

### Verification of Eradication

```
Post-Cleanup Verification:

1. Malware Scanning:
   ├─ Run multiple antivirus products (different vendors)
   ├─ Run specialized malware scanners (rootkit scanners)
   ├─ Manual file inspection (suspicious processes)
   └─ Result: Clean scan from multiple sources

2. System Behavior:
   ├─ Process execution baseline normal
   ├─ Network connections expected only
   ├─ File system activity normal
   ├─ Resource usage (CPU, memory, disk) normal
   └─ Result: No suspicious activity detected

3. Log Analysis:
   ├─ Check security event logs (no persistence mechanisms)
   ├─ Check web server logs (no re-infection attempts)
   ├─ Check DNS logs (no C2 domains contacted)
   └─ Result: No attack indicators in logs

4. Secondary Scan:
   ├─ Wait 24 hours (if re-infection happens, catch it)
   ├─ Repeat antivirus/rootkit scans
   ├─ Verify no new malware
   └─ Result: System verified clean
```

---

## Phase 5: RECOVERY (2-7 Days)

### Data Restoration

```
Recovery Procedure:

1. Assess Backup Integrity:
   ├─ Restore sample files to test system
   ├─ Verify files are not corrupted/encrypted
   ├─ Verify data is complete (no missing data)
   ├─ Verify backup is from PRE-INCIDENT time
   └─ Confirm: Backup is good for full restoration

2. Priority-Based Restoration:
   ├─ Priority 1 (Critical - 2-4 hours):
   │  ├─ Domain controllers
   │  ├─ Payment/billing systems
   │  ├─ Emergency services systems
   │  └─ Core infrastructure
   │
   ├─ Priority 2 (Important - 4-8 hours):
   │  ├─ File servers (most users)
   │  ├─ Email systems
   │  ├─ Business application servers
   │  └─ Database systems
   │
   └─ Priority 3 (Standard - 1-3 days):
      ├─ User workstations
      ├─ Development systems
      ├─ Test environments
      └─ Non-critical applications

3. Restore Process:
   ├─ System: Restore OS from clean backup
   ├─ Applications: Install/configure (from clean templates)
   ├─ Data: Restore data from backup
   ├─ Verify: Test functionality
   └─ Reintegrate: Return to production VLAN

4. User Data Recovery:
   ├─ Notify users: "Your files are being recovered"
   ├─ Inform: Some recent changes may be lost
   ├─ Timeline: Expect recovery by [DATE/TIME]
   ├─ Access: User home directories restored
   └─ Support: Helpdesk available for questions
```

### Validation & Monitoring

```
Post-Recovery Validation:

1. Functionality Testing:
   ├─ All critical services running?
   ├─ User access working (logins successful)?
   ├─ Data accessible and complete?
   ├─ Performance acceptable?
   └─ Result: Systems operational

2. Security Monitoring:
   ├─ EDR agents active on all systems?
   ├─ SIEM collecting logs?
   ├─ IDS/IPS active?
   ├─ Firewall rules enforced?
   └─ Result: Monitoring operational

3. Intensive Monitoring Period (2 weeks):
   ├─ SIEM alerts checked every hour
   ├─ File integrity monitoring enabled
   ├─ Process behavior monitored
   ├─ Network traffic monitored
   └─ Purpose: Catch any re-infection immediately

4. Phased Return to Normal:
   ├─ Day 1: Critical systems, 24/7 monitoring
   ├─ Day 2: Important systems, 24/7 monitoring
   ├─ Day 3-7: Standard systems, normal monitoring
   ├─ Week 2: Enhanced monitoring (baseline)
   └─ Week 3+: Normal operations
```

---

## Phase 6: POST-INCIDENT (Days 3-30)

### Root Cause Analysis

```
Investigation Questions:

1. How did infection start?
   ├─ Phishing email with malicious attachment?
   ├─ Exposed RDP port (brute forced)?
   ├─ Unpatched vulnerability?
   ├─ Supply chain compromise?
   └─ Insider threat?

2. How long was attacker present?
   ├─ First compromise: When?
   ├─ Ransomware execution: When?
   ├─ Gap: Time attacker had access before encryption?
   └─ Implication: What data accessed in that time?

3. What data was compromised?
   ├─ Customer data? (regulatory notification required)
   ├─ Financial data? (audit impact)
   ├─ Intellectual property? (competitive impact)
   ├─ Personal health information? (HIPAA breach)
   └─ Risk: Determine notification requirements

4. Why did our controls fail?
   ├─ No EDR on file servers
   ├─ Email sandboxing not deployed
   ├─ Backup not disconnected from network
   ├─ RDP not MFA-protected
   └─ Prevention: What controls need improvement?
```

### Lessons Learned & Prevention

```
Example Corrective Actions:

Immediate (Week 1):
├─ ☐ Deploy EDR to ALL systems (including servers)
├─ ☐ Enable email sandboxing for attachments
├─ ☐ Implement backup immutability (air-gapped)
├─ ☐ Force MFA for all RDP access
└─ ☐ Enable file encryption on critical systems

Short-term (Month 1-2):
├─ ☐ Disable RDP on internal systems (use VPN + jump host)
├─ ☐ Implement network segmentation (isolate critical systems)
├─ ☐ Deploy DLP (prevent mass file access)
├─ ☐ Quarterly ransomware simulations
└─ ☐ User training on phishing (90%+ detection rate target)

Long-term (Quarter 2-3):
├─ ☐ Zero-trust architecture implementation
├─ ☐ Full network segmentation (critical, standard, DMZ)
├─ ☐ Enhanced threat intelligence integration
├─ ☐ Incident response process improvements
└─ ☐ Board-level cyber risk reporting
```

---

## Ransom Negotiation (If Considered)

```
WARNING: Ransom payment is NOT recommended

Reasons NOT to pay:
├─ No guarantee decryption key works
├─ Attacker may leave backdoor for re-encryption
├─ Funds criminal enterprise (enables more attacks)
├─ Law enforcement may investigate
├─ Some victims' data released despite payment
├─ Ransom usually 10x higher for known payers
└─ Your insurance may NOT cover if you pay

If forced to negotiate (absolute last resort):

Step 1: Contact law enforcement FIRST
├─ FBI (if US-based)
├─ Local cybercrime unit
├─ Notify: Do NOT pay without approval

Step 2: Insurance notification
├─ Cyber insurance policy review
├─ Coverage verification
├─ Approval for ransom discussion

Step 3: Negotiation (if authorized)
├─ Initial demand: Often 10x recovery cost
├─ Counter-offer: Start at 10% of demand
├─ Negotiation range: 20-50% of initial demand
├─ Maximum: < recovery cost (don't pay more than rebuilding)

Step 4: Payment (if approved)
├─ Cryptocurrency (untraceable)
├─ Escrow service verification
├─ Receive decryption tool
├─ Verify tool effectiveness
└─ Provide to law enforcement

Reality: Most recovered data is NOT through decryption
└─ Recovery usually comes from clean backups, NOT paying
```

---

## Decision Checklist

```
☐ Detection confirmed (real incident, not false alarm)
☐ Incident response team activated
☐ Affected systems isolated
☐ Backup integrity verified
☐ Recovery timeline established
☐ Executive stakeholders notified
☐ Legal/compliance contacted
☐ Insurance broker notified
☐ Law enforcement contacted (if warranted)
☐ Customer notification plan (if required)
☐ External communications prepared
☐ Root cause analysis in progress
☐ Corrective actions identified
☐ All-clear sign-off before return to production
```

---

## Contact & References

- FBI Cybercrime Division: ic3.gov
- CISA Ransomware Resources: cisa.gov/ransomware
- No More Ransom Project: nomoreransom.org (decryption tools)
- Law Enforcement: [Local cybercrime contact]

---

*Playbook Version Control:*
- v1.5 (2026-02-19): Added negotiation warnings
- v1.4 (2026-01-15): Updated ransomware variants
- v1.3 (2025-12-01): Added timelines and metrics
- v1.0 (2025-10-15): Initial playbook creation

*Review Schedule:* Quarterly or after each incident
*Last Tested:* [Date of last IR drill]
*Last Updated By:* [Name], CISO
