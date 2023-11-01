# Overview

### Windows WinRM setup
Run a powershell as Administrator for these commands

```powershell
### ops is normally a domain identity
Add-LocalGroupMember -Group "Administrators" -Member "ops"


### download and run the WinRM setup powershell script (uncomment the one you want to use)

### Opens port 5986 to all sources
#$url = "https://raw.githubusercontent.com/amorris-vk/ansible-winrm-setup/main/ConfigureRemotingForAnsible.ps1"

### Opens port 5986 to jump server on VLAN 40
#$url = "https://raw.githubusercontent.com/amorris-vk/ansible-winrm-setup/main/CAX_VDI_ConfigureRemotingForAnsible.ps1"

$datestr = Get-Date -UFormat "%Y%m%d_%H%M%S"
$file = "{0}\ConfigureRemotingForAnsible_{1}.ps1" -f $env:temp, $datestr

(New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)

powershell.exe -ExecutionPolicy ByPass -File $file -CertValidityDays 3650 -DisableBasicAuth -EnableCredSSP -ForceNewSSLCert


### remove HTTP WinRM listener
Get-ChildItem -Path WSMan:\localhost\Listener | Where-Object { $_.Keys -contains "Transport=HTTP" } | Remove-Item -Recurse -Force

restart-service winrm

### show listeners, should only show a single HTTPS listener
winrm enumerate winrm/config/Listener
```
