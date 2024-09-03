# Script-Powershell


![image](https://github.com/user-attachments/assets/680e1353-4710-42f5-9ad4-d70bcaf17e4d)


## Extract members DL

Install-Module -Name ImportExcel

Untrusted repository
You are installing the modules from an untrusted repository. If you trust this repository, change its
InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
'PSGallery'?
[O] Oui  [T] Oui pour tout  [N] Non  [U] Non pour tout  [S] Suspendre  [?] Aide (la valeur par défaut est « N ») : O


Get-DistributionGroupMember -Identity "COCOORD"  | Export-Excel -Path 'C:\Scripts\AD-utilisateurs.xlsx'

Get-DistributionGroupMember -Identity "COCOORD"  | Select-Object -Property "DisplayName", "PrimarySmtpAddress"| Export-Excel -Path 'C:\Scripts\AD-utilisateurs1.xlsx'


## CREATION  d'un FILTER******  OK

$Filter  = "((RecipientType -eq 'UserMailbox') -and (((Co -eq 'France') -or (Co -like 'Belgique*') -or (Co -like 'Italie*')) -and ((CustomAttribute3 -eq 'ANIC') -or (CustomAttribute3 -eq 'RABE') -or (CustomAttribute3 -eq 'DABE'))))" 
Get-Recipient -RecipientPreviewFilter $Filter | ft displayname, title






