# IR for Ransomware — Oleg Skulkin

Study notes from *Incident Response Techniques for Ransomware Attacks* by Oleg Skulkin (Packt Publishing). Covers the full attack lifecycle from initial access through recovery, with a focus on how human-operated ransomware groups operate.

## Ransomware Evolution

| Year | Family | Notes |
|------|--------|-------|
| 2016 | SamSam | Targeted manual deployment; exploited JBoss, RDP |
| 2017 | BitPaymer | Dridex banking trojan operators pivoted to ransomware |
| 2018 | Ryuk | Delivered via TrickBot/Emotet; high-value targeting |
| 2019+ | RaaS programs | Affiliate model — operators lease ransomware, share revenue |

## Initial Access Brokers (IABs)

Modern ransomware groups frequently **buy** rather than hack their way in. IABs specialise in gaining enterprise footholds and selling access on dark web forums, typically earning up to 10% commission on successful ransom payments.

Common access vectors sold by IABs:
- Compromised RDP credentials
- VPN accounts with weak or no MFA
- Phishing-obtained access tokens
- Exploited edge infrastructure vulnerabilities

## CVEs Commonly Exploited for Initial Access

| CVE | Product | Type | Impact |
|-----|---------|------|--------|
| CVE-2019-11510 | Pulse Secure VPN | Path traversal | Leaks plaintext usernames and passwords |
| CVE-2018-13379 | Fortinet FortiGate VPN | Path traversal | Retrieves session files with credentials |
| CVE-2019-19781 | Citrix ADC/Gateway | Directory traversal | Unauthenticated RCE |
| CVE-2021-26855 | Microsoft Exchange (ProxyLogon) | SSRF | Authentication bypass leading to RCE |

## Attack Chain

```
IAB sells access (RDP/VPN/phishing)
  └─► Loader stage (Emotet, TrickBot, BazarLoader)
        └─► Post-exploitation framework (Cobalt Strike, Metasploit)
              ├─► Credential harvesting (Mimikatz, DCSync)
              ├─► Lateral movement (PsExec, WMI, SMB)
              ├─► Data exfiltration (double extortion setup)
              └─► Ransomware deployment (Ryuk, Conti, LockBit)
```

## Loaders Used with Ryuk/Conti

- **Emotet** — email-delivered macro dropper; modular botnet
- **TrickBot** — banking trojan repurposed as enterprise loader; modules for credential theft, network recon
- **BazarLoader** — Trickbot group's stealthier alternative; used heavily pre-Conti deployment

## Double Extortion

Post-2019 model: data exfiltrated *before* encryption. Victims face both operational disruption and threatened public release of sensitive data on leak sites (e.g., Conti News, DarkSide blog).

## IR Process Overview

1. **Preparation** — IR plan, playbooks, out-of-band comms, backup integrity checks
2. **Detection and Analysis** — identify patient zero, determine initial access vector, scope blast radius
3. **Containment** — isolate affected systems, rotate credentials (especially AD), preserve forensic evidence
4. **Eradication** — remove persistence mechanisms, patch exploited vulnerabilities, rebuild compromised systems
5. **Recovery** — restore from clean backups, validate integrity before reconnecting to network
6. **Post-Incident** — threat actor TTP documentation, lessons learned, detection rule updates

## Reference

- Oleg Skulkin — *Incident Response Techniques for Ransomware Attacks* (Packt, 2022)
- MITRE ATT&CK Ransomware: T1486 (Data Encrypted for Impact), T1490 (Inhibit System Recovery)
- CISA Ransomware Guide: https://www.cisa.gov/stopransomware
