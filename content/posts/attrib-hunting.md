+++
categories = ["Threat Hunting"]
date = "2026-04-03"
description = ""
featured = ""
featuredalt = ""
featuredpath = "date"
linktitle = ""
title = ""
slug = ""
type = "post"
tag = "SIEM"
+++

## Checking for processes that are hiding files in Windows OS

This is to help with threat hunting the recent PCPcats TTP.

```kql
DeviceProcessEvents
| where FileName =~ "attrib.exe" or ProcessVersionInfoOriginalFileName =~ "attrib.exe"
| where ProcessCommandLine has "+h"
| where InitiatingProcessFolderPath has_any (
    @"windows\system32",
    @"windows\syswow64",
    @"program files",
    @"program files (x86)",
    @"windows\"
)
| extend suspicious_path = case(
    ProcessCommandLine matches regex @"(?i)\\Users\\.*\+h",   
    ProcessCommandLine matches regex @"(?i)\\Temp\\.*\+h",        
    ProcessCommandLine matches regex @"(?i)\\AppData\\.*\+h",     
    ProcessCommandLine matches regex @"(?i)\\ProgramData\\.*\+h",
    ProcessCommandLine matches regex @"(?i)\\Windows\\.*\+h", 
    "Other - Low"
)
| project
    TimeGenerated,
    DeviceName,
    AccountName,
    FolderPath,
    ProcessCommandLine,
    InitiatingProcessFolderPath,
    InitiatingProcessCommandLine,
    suspicious_path
| sort by TimeGenerated desc
```