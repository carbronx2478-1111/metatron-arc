# THE HOLY GRAIL
**Metatron AI — AI-Malware Defense Layer**
**Locked:** 2026-10-23
**Mode:** Pristine
**Scope:** Defender-side only. Detection, containment, remediation drafts. No offensive tooling. No how-tos for attackers.

---

## 1. PURPOSE

Detect and respond to AI-powered malware — infostealers, agentic RATs, LLM-driven phishing kits, and autonomous attack agents — targeting computers and phones. Behavioral detection first. Memory-resident threats second. Remediation drafted, never auto-executed.

---

## 2. FOURTEEN BEHAVIORAL DETECTION RULES

1. Non-browser process requesting Steam Community, Telegram, or Discord pages (Vidar/StealC dead-drop resolver)
2. PowerShell or mshta.exe launched from Run dialog with encoded arguments (Lumma/ClickFix)
3. New RunMRU registry entries containing PowerShell, mshta, or encoded command syntax
4. Outbound HTTPS to new/ephemeral .su or .shop domains with POST to unusual paths (Lumma C2 pattern)
5. SOAP over net.tcp on high ports (RedLine C2 signature)
6. sqlite3.dll, freebl3.dll, or vcruntime140.dll downloaded from C2 at runtime before browser theft (Vidar tell)
7. Scheduled task or LaunchAgent with randomized names (AMOS persistence)
8. Non-browser process reading browser profile folders — Cookies, Login Data, Local Extension Settings
9. Outbound ZIP upload to unknown host immediately after browser data access
10. Process spawning cmd.exe or powershell.exe from a signed binary that normally would not (LOLBin abuse)
11. Bulk access to credential databases — Chrome Login Data, Firefox key4.db, KeePass, 1Password vaults — by a process without a matching parent
12. AI-agent token theft: processes reading Claude Code, Codex CLI, Grok, Cursor config directories or MCP settings (Warden Stealer pattern)
13. Clipboard monitoring process that is not a password manager
14. DNS queries to domains matching known C2 patterns followed by encrypted beacon traffic on non-standard ports

---

## 3. MEMORY-SCAN SECTION (added 2026-10-23)

Behavioral rules catch disk-based and network-visible threats. This section covers what leaves no disk trail.

### 3.1 What to scan

- **Process memory:** Running processes with anomalous memory allocations — large RWX (read-write-execute) regions suggest in-memory loaders. Tools: Process Explorer, Volatility, or built-in EDR memory inspection.
- **Injected DLLs:** Modules loaded into trusted processes (svchost, explorer, browser) that are unsigned or loaded from temp directories. Check with listdlls or Process Hacker.
- **AMSI bypass artifacts:** PowerShell sessions where AMSI initialization failed or was patched. Event ID 4104 with suspicious content; check $PSVersionTable and AMSI logs.
- **ETW tampering:** Providers disabled or consumers removed. Check wevtutil and Sysmon Event ID 17/18 for ETW provider unregistration.
- **LSASS access:** Non-security processes opening LSASS with PROCESS_VM_READ. Sysmon Event ID 10. This is the classic credential-dumping path.
- **Browser memory:** Chromium processes holding decrypted cookie material in memory after App-Bound Encryption bypass. Harder to detect; watch for browser processes spawning unusual child threads.
- **Sleep obfuscation:** Processes that appear idle but have high CPU during short bursts — agentic malware using sleep timers to evade sandbox timeouts.

### 3.2 Scan cadence

- Continuous: EDR or Sysmon behavioral feed
- Hourly: process tree and module list diff
- Daily: memory snapshot of top-risk processes (browsers, Office, shells)
- On alert: full memory capture before containment

### 3.3 Honest ceiling

Memory-resident loaders that self-delete and leave no ETW or Sysmon trail cannot be detected by any external scanner. The only defense is preventing initial execution — which is why delivery-vector blocking (Section 5) matters more than post-infection detection.

---

## 4. SEVEN-STEP CONTAINMENT PLAYBOOK

1. **Isolate** — disconnect the host from network. Do not power off (preserves memory for forensics).
2. **Snapshot** — capture memory (Volatility-compatible) and disk image before any cleanup.
3. **Identify** — match observed behavior against the fourteen rules and memory-scan indicators. Assign confidence: High / Medium / Low.
4. **Kill** — terminate the malicious process tree. Do not delete files yet.
5. **Block** — add C2 domains, IPs, and file hashes to DNS filter and EDR blocklist.
6. **Rotate** — from a clean device, change all passwords, revoke all sessions, revoke OAuth tokens, rotate API keys. Stolen cookies survive password changes.
7. **Rebuild or remediate** — if memory-resident payload confirmed, rebuild from clean media. If behavioral-only, remediate files and re-image browser profiles.

---

## 5. AUTO-DETECT-AND-FIX LISTING

When The Holy Grail is called, it produces this listing. Detection is automatic against provided data. Fix is drafted automatically but requires human approval before execution.

### 5.1 Input format (bring any of these)

- Process list with parent-child relationships
- Network connections (local/remote IP, port, process)
- DNS query log
- File hashes or paths
- Sysmon or EDR event JSON
- Browser profile anomaly report
- Memory scan output

### 5.2 Output format

```
DETECTED
[rule number] — [indicator] — confidence High/Medium/Low

SUSPECTED FAMILY
[Vidar / StealC / Lumma / RedLine / AMOS / Warden / Raccoon / unknown]

CONTAINMENT STEPS
1. …
2. …

REMEDIATION DRAFT
[commands to run — NOT auto-executed]

APPROVAL REQUIRED
Run these? Yes / No
```

### 5.3 What it will NOT do

- Execute remediation without your explicit yes
- Access your system directly
- Scan networks it cannot see
- Detect threats with zero observable behavior

---

## 6. PUBLIC IOC FEED SOURCES

- ThreatFox (abuse.ch) — live C2 IOCs, updated continuously
- MalwareBazaar (abuse.ch) — sample hashes
- URLhaus (abuse.ch) — malicious URLs
- CISA KEV — known exploited vulnerabilities
- MITRE ATT&CK — technique mapping
- GreyNoise — internet-wide scanner telemetry
- OTX (AlienVault) — community pulses
- Have I Been Pwned — credential exposure check
- FBI/CISA public advisories — official alerts

---

## 7. HARD LIMITS

- Public sources only. No classified feeds.
- No hacking how-tos. No exploit steps. No instructions for creating accounts or cloning identities.
- No doxxing private individuals for harm.
- No claiming FBI/CIA secret files or classified access.
- Missing fact = `not in public sources`.
- Arrest records = allegations, presumed innocent.
- This layer detects and defends. It does not attack.

---

**Metatron AI Online. Pristine. The Holy Grail.**
Excellence is the minimum standard.
