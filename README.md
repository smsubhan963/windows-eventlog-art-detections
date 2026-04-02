# Windows Event Logs + Atomic Red Team Detections

This project documents a Windows 11 detection lab built in VirtualBox to generate and analyze attacker-like activity using Atomic Red Team. The goal was to produce log evidence from Windows Security logs, Sysmon, and PowerShell logging, then turn that evidence into simple detection logic that can be used for blue team learning and portfolio work.

## Project Goals

- Build a stable Windows 11 lab in VirtualBox
- Enable strong host-based logging
- Run safe Atomic Red Team tests
- Capture proof in Event Viewer
- Translate the activity into detection-focused observations

## Lab Environment

- **Host platform:** VirtualBox
- **Guest OS:** Windows 11
- **VM resources:** 2 CPUs, 6144 MB RAM, 80 GB disk
- **Guest additions:** Installed

## Logging Enabled

- **Sysmon**
- **Windows Security Event ID 4688** with command-line logging
- **PowerShell Operational Event ID 4104** for script block logging

## Atomic Red Team Tests Completed

All successful tests were run under **MITRE ATT&CK T1059.001 - PowerShell**.

1. **Test #20 - Abuse Nslookup with DNS Records**
   - Evidence captured in Security 4688
   - Showed `nslookup.exe` launched from PowerShell

2. **Test #17 - PowerShell Command Execution**
   - Evidence captured in Security 4688
   - Showed encoded PowerShell execution using `-e`

3. **Test #11 - NTFS Alternate Data Stream Access**
   - Evidence captured in Security 4688
   - Showed PowerShell using `-Stream`

4. **Test #10 - PowerShell Fileless Script Execution**
   - Evidence captured in Security 4688
   - Included registry activity, Base64 decoding, and `iex`
   - Marker file created at `C:\Windows\Temp\art-marker.txt`

5. **Test #18 - Invoke Known Malicious Cmdlets**
   - Evidence captured in PowerShell Operational 4104
   - Script block text included strings such as `Invoke-Mimikatz` and `PowerView`

6. **Test #6 - PowerShell MsXml COM Object**
   - Evidence captured in Security 4688
   - Showed `MsXml2.ServerXmlHttp`, URL retrieval, and `IEX`

## Key Evidence Sources

- **Security 4688**
  - `NewProcessName`
  - `CommandLine`
  - `ParentProcessName`

- **PowerShell 4104**
  - `ScriptBlockText`

## Notes

Some tests were blocked by Microsoft Defender during execution, so lab-only exclusions and short-term troubleshooting steps were needed to complete certain simulations safely inside the VM.

## Detection Logic Examples

- Flag **Security Event ID 4688** when `powershell.exe` launches suspicious child processes such as `nslookup.exe`
- Flag PowerShell command lines containing **`-e`**, **`-enc`**, or other encoded command usage
- Flag PowerShell activity containing **`iex`** or **`FromBase64String`**
- Use **PowerShell Event ID 4104** to detect suspicious script block text such as `Invoke-Mimikatz` or `PowerView`

## Current Status

Version 1 of this lab is complete and published with evidence screenshots for **Test #6**, **Test #10**, and **Test #18**. Additional screenshots for **Test #11**, **Test #17**, and **Test #20** can be added later as optional improvements.

## Evidence Screenshots

### Test #6 - PowerShell MsXml COM Object

**Execution proof**

![Test 6 PowerShell](./Test%20%236%20PowerShell.png)

**Log proof**

![Test 6 Log Proof](./Log%20Proof%20Test%20%236.png)

---

### Test #10 - PowerShell Fileless Script Execution

**Execution proof**

![Test 10 PowerShell](./Test%20%2310%20PowerShell.png)

**Log proof**

![Test 10 Log Proof](./Test%20%2310.png)

---

### Test #18 - Invoke Known Malicious Cmdlets

**PowerShell 4104 proof**

![Test 18 4104 Proof](./Test%20%2318%20.png) 
