---
category: Uncategorized
tags: []
---
# Check applocker rules/policy
```powershell
Get-AppLockerPolicy -Effective | select -expandproperty rulecollections
```