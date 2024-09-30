# Script-Powershell

## Connect to Exchange
Import-Module ExchangeOnlineManagement
Connect-ExchangeOnline


![image](https://github.com/user-attachments/assets/680e1353-4710-42f5-9ad4-d70bcaf17e4d)


## Extract members DL

Install-Module -Name ImportExcel

Get-DistributionGroupMember -Identity "COCOORD"  | Export-Excel -Path 'C:\Scripts\AD-utilisateurs.xlsx'

Get-DistributionGroupMember -Identity "COCOORD"  | Select-Object -Property "DisplayName", "PrimarySmtpAddress"| Export-Excel -Path 'C:\Scripts\AD-utilisateurs1.xlsx'

**************


 
Try     {    
    #Connect to Exchange Online    
    Connect-ExchangeOnline -ShowBanner:$False
 
    #Get all Distribution Lists
    $Result=@()   
    $DistributionGroups = Get-DistributionGroup -ResultSize Unlimited | Select-Object -Property "DisplayName", "PrimarySmtpAddress", "ManagedBy" 
    $GroupsCount = $DistributionGroups.Count
    $Counter = 1
    $DistributionGroups | ForEach-Object {
        Write-Progress -Activity "Processing Distribution List: $($_.DisplayName)" -Status "$Counter out of $GroupsCount completed" -PercentComplete (($Counter/$GroupsCount)*100)
        $Group = $_
        Get-DistributionGroupMember -Identity $Group.DisplayName  -ResultSize Unlimited | ForEach-Object {
            $member = $_
            $Result += New-Object PSObject -property @{
            GroupName = $Group.DisplayName
            GroupEmail = $Group.PrimarySmtpAddress
            Member = $Member.DisplayName
            EmailAddress = $Member.PrimarySMTPAddress
            RecipientType= $Member.RecipientType
		    ManBy = [string]$Group.ManagedBy
            }
        }
    $Counter++
    }
    #Get Distribution List Members and Exports to xlsx
    $Result | Export-Excel -Path 'C:\Distribution-Group-Members06.xlsx'
}      
Catch {     
    write-host -f Red "Error:" $_.Exception.Message
}



***********



## Extract members DDL

****** SUR
$membres = Get-DynamicDistributionGroup -Identity "LDD - FR - EXPL DOMITYS RSS - DIRECTEURS ADJOINTS" 
Get-Recipient -RecipientPreviewFilter ($membres.RecipientFilter) | Format-Table -Property Name, location, CustomAttribute10, CountryOrRegion  | Out-File -FilePath  "C:\Rscripts\LDD_FR_RSS_DIRECTEURS_ADJOINTS2.txt"


## Extract members DDL

$membres = Get-DynamicDistributionGroup -Identity "LDD - FR - DIRECTION RH"
Get-Recipient -RecipientPreviewFilter ($membres.RecipientFilter)   |Sort-Object -Property displayname| Format-Table -Property Name, StateOrProvince, CustomAttribute10, CountryOrRegion   | Out-File -FilePath  "C:\Rscripts\LDD_FR_DIRECTION_RH.txt"
------- 
$membres = Get-DynamicDistributionGroup -Identity "LDD - FR - EXPL - CHARGÉS MAINTENANCE" 
Get-Recipient -RecipientPreviewFilter ($membres.RecipientFilter) |Sort-Object -Property displayname| Format-Table -Property displayname, title, CustomAttribute3, CustomAttribute8 | Out-File -FilePath "C:\Rscripts\LDD_FR_EXPL_CHARGES_MAINTENANCE.txt"
------
$membres = Get-DynamicDistributionGroup -Identity "LDD - FR - EXPL - DOMRG1-NOROU - MAINTENANCE" 
Get-Recipient -RecipientPreviewFilter ($membres.RecipientFilter) |Sort-Object -Property displayname| Format-Table -Property displayname, title, CustomAttribute3, CustomAttribute8 | Out-File -FilePath "C:\Rscripts\LDD_FR_EXPL_DOMRG1_NOROU_MAINTENANCE.txt"

_________________________________________________________________________________
## CREATION  d'un FILTER

$Filter  = "((RecipientType -eq 'UserMailbox') -and (((Co -eq 'France') -or (Co -like 'Belgique*') -or (Co -like 'Italie*')) -and ((CustomAttribute3 -eq 'ANIC') -or (CustomAttribute3 -eq 'RABE') -or (CustomAttribute3 -eq 'DABE'))))" 
Get-Recipient -RecipientPreviewFilter $Filter | ft displayname, title
------------------------------------------------------------------------
$Filter  = "((RecipientType -eq 'UserMailbox') -and ((CustomAttribute8 -eq 'DOMRG1-NOROU') -and ((CustomAttribute3 -eq 'MESP') -or (CustomAttribute3 -eq 'MAIN') -or (CustomAttribute3 -eq 'MAINA') -or (CustomAttribute3 -eq 'ESVE') -or (CustomAttribute3 -eq 'REMT') -or (CustomAttribute3 -eq 'CMEV') -or (CustomAttribute3 -eq 'TECM') )))" 
Get-Recipient -RecipientPreviewFilter $Filter |Sort-Object -Property displayname | ft displayname, title, PrimarySmtpAddress, CustomAttribute3, CustomAttribute8 

______________________________________________________________________________
## CREATION  d'un DDL
New-DynamicDistributionGroup -Name "LDD - FR - EXPL - DOMRG1-NOROU - MAINTENANCE" -RecipientFilter {(RecipientTypeDetails -eq 'UserMailbox') -and ((CustomAttribute8 -eq 'DOMRG1-NOROU') -and ((CustomAttribute3 -eq 'MESP') -or (CustomAttribute3 -eq 'MAIN') -or (CustomAttribute3 -eq 'MAINA') -or (CustomAttribute3 -eq 'ESVE') -or (CustomAttribute3 -eq 'REMT') -or (CustomAttribute3 -eq 'CMEV') -or (CustomAttribute3 -eq 'TECM')))}

ou
New-DynamicDistributionGroup -Name "LDD - FR - EXPL - DOMRG6-EST - MAINTENANCE" -RecipientFilter {(RecipientTypeDetails -eq 'UserMailbox') -and ((CustomAttribute8 -eq 'DOMRG6-EST') -and ((CustomAttribute3 -eq 'MESP') -or (CustomAttribute3 -eq 'MAIN') -or (CustomAttribute3 -eq 'MAINA') -or (CustomAttribute3 -eq 'ESVE') -or (CustomAttribute3 -eq 'REMT') -or (CustomAttribute3 -eq 'CMEV') -or (CustomAttribute3 -eq 'TECM')))}





