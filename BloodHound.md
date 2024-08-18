---
tags:
  - domain-enumeration
---
BloodHound Legacy: https://github.com/BloodHoundAD/BloodHound
BloodHound CE (Community Edition): https://github.com/SpecterOps/BloodHound

```powershell title:"Supply data to BloodHound (Remember to bypass .NET AMSI)"
. C:\AD\Tools\BloodHound-master\Collectors\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All
```

Or just use SharpHound.exe

```powershell title:"Make collection stealthy. Removes noisy collection methods like RDP,DCOM,PSRemote and LocalAdmin"
Invoke-BloodHound -Stealth
```
```batch
SharpHound.exe --Stealth
```

```powershell title:"To avoid detections like MDI"
Invoke-BloodHound -ExcludeDCs
```

- Run the collector on your foothold and a zip file will be generated
- Import the zip file in BloodHound