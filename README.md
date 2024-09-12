# Script-Powershell

## Connect to Exchange
Import-Module ExchangeOnlineManagement
Connect-ExchangeOnline


![image](https://github.com/user-attachments/assets/680e1353-4710-42f5-9ad4-d70bcaf17e4d)


## Extract members DL

Install-Module -Name ImportExcel

Get-DistributionGroupMember -Identity "COCOORD"  | Export-Excel -Path 'C:\Scripts\AD-utilisateurs.xlsx'

Get-DistributionGroupMember -Identity "COCOORD"  | Select-Object -Property "DisplayName", "PrimarySmtpAddress"| Export-Excel -Path 'C:\Scripts\AD-utilisateurs1.xlsx'

## Extract members DDL
$membres = Get-DynamicDistributionGroup -Identity "LDD - FR - DIRECTION RH"
Get-Recipient -RecipientPreviewFilter ($membres.RecipientFilter)   |Sort-Object -Property displayname| Format-Table -Property Name, StateOrProvince, CustomAttribute10, CountryOrRegion   | Out-File -FilePath  "C:\Rscripts\LDD_FR_DIRECTION_RH.txt"


## CREATION  d'un FILTER

$Filter  = "((RecipientType -eq 'UserMailbox') -and (((Co -eq 'France') -or (Co -like 'Belgique*') -or (Co -like 'Italie*')) -and ((CustomAttribute3 -eq 'ANIC') -or (CustomAttribute3 -eq 'RABE') -or (CustomAttribute3 -eq 'DABE'))))" 
Get-Recipient -RecipientPreviewFilter $Filter | ft displayname, title


## CREATION  d'un DDL
New-DynamicDistributionGroup -Name "LDD - FR - EXPL - DOMRG1-NOROU - MAINTENANCE" -RecipientFilter {(RecipientTypeDetails -eq 'UserMailbox') -and ((CustomAttribute8 -eq 'DOMRG1-NOROU') -and ((CustomAttribute3 -eq 'MESP') -or (CustomAttribute3 -eq 'MAIN') -or (CustomAttribute3 -eq 'MAINA') -or (CustomAttribute3 -eq 'ESVE') -or (CustomAttribute3 -eq 'REMT') -or (CustomAttribute3 -eq 'CMEV') -or (CustomAttribute3 -eq 'TECM')))}

ou
New-DynamicDistributionGroup -Name "LDD - FR - EXPL - DOMRG6-EST - MAINTENANCE" -RecipientFilter {(RecipientTypeDetails -eq 'UserMailbox') -and ((CustomAttribute8 -eq 'DOMRG6-EST') -and ((CustomAttribute3 -eq 'MESP') -or (CustomAttribute3 -eq 'MAIN') -or (CustomAttribute3 -eq 'MAINA') -or (CustomAttribute3 -eq 'ESVE') -or (CustomAttribute3 -eq 'REMT') -or (CustomAttribute3 -eq 'CMEV') -or (CustomAttribute3 -eq 'TECM')))}





