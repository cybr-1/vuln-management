# Overview

On December 10th of 2013, [CVE-2013-3900](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2013-3900) was published pertaining to a vulnerability in WinVerifyTrust Signature Validation, which allows attackers to exploit the padding of a Windows Authenticode signature to gain control of a system.  
  
Microsoft's solution was to offer a 'opt-in' solution that could be activated by adding a pair of registry keys.  
  
This change was originally planned by Microsoft to become the default option, but that plan was officially abandoned on July 29, 2014, likely because the changes caused issues installing software that had been signed with Windows Authenticode.  
  
Over time, various bad actors have used this commonly open vulnerability for their own purposes, including the installation of malware and ransomware, to the point that it was [included in CISA's Known Exploited Vulnerabilities Catalog](https://www.cisa.gov/uscert/ncas/current-activity/2022/01/10/cisa-adds-15-known-exploited-vulnerabilities-catalog). By January 21st, 2022, Microsoft was forced to reissue its guidance for all existing Windows systems (including Windows 10 and 11 systems)  and noted that setting a non-default configuration fell to the users, not Microsoft itself.

---
# Remediation

## Deployment

```Powershell
# Declare Variables
$32BitPath = 'HKLM:\Software\Microsoft\Cryptography\Wintrust\Config'
$64BitPath = 'HKLM:\Software\Wow6432Node\Microsoft\Cryptography\Wintrust\Config'
$Item = 'EnableCertPaddingCheck'
$Value = '1'

#Execution
if ((Get-WmiObject win32_operatingsystem | Select-Object osarchitecture).osarchitecture -eq "64-bit")
{
    #64 bit logic here
    New-Item -Path $64BitPath -Force 
    New-ItemProperty -Path $64BitPath -Name $Item -Value $Value -PropertyType STRING -Force
}
else
{
    #32 bit logic here
    New-Item -Path $32BitPath -Force
    New-ItemProperty -Path $32BitPath -Name $Item -Value $Value -PropertyType STRING -Force
}	
```

## Rollback

```powershell
# Declare Variables
$32BitPath = 'HKLM:\Software\Microsoft\Cryptography\Wintrust\Config'
$64BitPath = 'HKLM:\Software\Wow6432Node\Microsoft\Cryptography\Wintrust\Config'
$Item = 'EnableCertPaddingCheck'

#Execution
if ((Get-WmiObject win32_operatingsystem | Select-Object osarchitecture).osarchitecture -eq "64-bit")
{
    #64 bit logic here
    Remove-ItemProperty -Path $64BitPath -Name $Item
}
else
{
    #32 bit logic here
    Remove-ItemProperty -Path $32BitPath -Name $Item
}
```

---
# Resources

- [CVE-2013-3900](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2013-3900)

