# Script-Powershell

## Connect to Exchange
Import-Module ExchangeOnlineManagement

Connect-ExchangeOnline



## Extract members DL

Install-Module -Name ImportExcel

Get-DistributionGroupMember -Identity "COCOORD"  | Export-Excel -Path 'C:\Rscripts\AD-utilisateurs.xlsx'

Get-DistributionGroupMember -Identity "COCOORD"  | Select-Object -Property "DisplayName", "PrimarySmtpAddress"| Export-Excel -Path 'C:\Rscripts\AD-utilisateurs1.xlsx'

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
    $Result | Export-Excel -Path 'C:\Rscripts\Distribution-Group-Members06.xlsx'
}      
Catch {     
    write-host -f Red "Error:" $_.Exception.Message      
}



***********
# Création et gestion des listes de distribution 

Dans Office 365, les listes de distribution sont un moyen efficace pour communiquer avec un groupe de personnes. 
Elles permettent d’envoyer des courriels à plusieurs destinataires simultanément sans avoir à ajouter chaque contact individuellement. 
Pour créer une liste de distribution, allez dans le Centre d’administration Exchange et suivez ces étapes :

Accédez à la section « Destinataires », puis cliquez sur « Groupes ».
Cliquez sur « Nouveau » (+), puis sélectionnez « Groupe de distribution ».
Complétez le formulaire avec les informations requises : nom, alias, description, et membres.
Enregistrez vos modifications.
Pour gérer les listes de distribution existantes, vous pouvez ajouter ou supprimer des membres, modifier des informations de groupe, ou même supprimer totalement une liste en accédant aux propriétés du groupe concerné toujours via le Centre d’administration Exchange.

Avantages des listes de distribution pour la collaboration d’équipe
Les listes de distribution offrent de nombreux avantages pour la collaboration au sein des équipes de travail. 
Elles facilitent la communication en permettant l’envoi d’annonces de groupe, la coordination des projets communs et l’organisation des réunions. 

Voici quelques-uns des avantages clés :

Efficacité de la communication : réduisez le temps passé à chercher des adresses email individuelles.
Amélioration de la productivité : envoyez des mises à jour de projet à toute l’équipe en un seul clic.
Flexibilité : ajoutez ou retirez facilement des membres au fur et à mesure que votre équipe change.

Les listes permettent également d’assurer que tout le monde reçoit la même information en même temps, réduisant ainsi les risques de malentendus ou de manque d’information.

Différences entre listes de distribution, groupes Office 365 et groupes dynamiques

![image](https://github.com/user-attachments/assets/f6f3d55c-9bd2-46b3-983f-9610217166a1)


Fonctionnalité	Listes de Distribution	Groupes Office 365	Groupes Dynamiques
Communication	Envoi d’e-mails uniquement	Collaboration étendue avec fichiers partagés, calendriers, etc.	Envoi d’e-mails basé sur des règles dynamiques
Gestion des Membres	Manuelle	Manuelle ou automatique via les services en ligne comme Planner ou Teams	Automatique selon les attributs des utilisateurs
Type d’Usage	Convenable pour les e-mails de groupe simples	Ideal pour la collaboration et le travail d’équipe	Parfait pour les listes d’utilisateurs changeant fréquemment
En somme, alors que les listes de distribution sont idéales pour des communications par e-mail simples et statiques, les groupes Office 365 sont conçus pour une collaboration plus complète et interactive. Les groupes dynamiques, quant à eux, s’ajustent automatiquement en fonction des critères définis, offrant une gestion des membres flexible et sans effort.

Quelles sont les étapes à suivre pour créer une liste de distribution dans Office 365?
Pour créer une liste de distribution dans Office 365, vous devez:

1. Connectez-vous à l’Administration d’Office 365.
2. Accédez à la section Groupes dans le centre d’administration.
3. Cliquez sur Ajouter un groupe.
4. Choisissez le type de groupe Liste de distribution.
5. Remplissez les informations nécessaires comme le nom, la description et l’adresse e-mail.
6. Ajoutez les membres.
7. Cliquez sur Enregistrer ou Créer.

Votre liste de distribution est maintenant prête à être utilisée.

Comment peut-on gérer les membres d’une liste de distribution Office 365?
Pour gérer les membres d’une liste de distribution Office 365, vous devez accéder au Centre d’administration Microsoft 365. Ensuite, allez dans la section Groupes, choisissez le groupe à modifier et vous pourrez ajouter ou supprimer des membres. Vous pouvez également utiliser PowerShell pour une gestion plus avancée.

Est-il possible de convertir une liste de distribution existante en groupe Office 365, et si oui, comment?
Oui, il est possible de convertir une liste de distribution existante en groupe Office 365. Pour cela, vous pouvez utiliser l’interface d’administration de Microsoft 365 ou PowerShell. Voici les étapes à suivre :

1. Connectez-vous au Centre d’administration Exchange.
2. Allez à la section « Destinataires », puis à l’onglet « Groupes ».
3. Sélectionnez la liste de distribution que vous souhaitez convertir.
4. Cliquez sur l’option « Convertir« .

Pour utiliser PowerShell, vous lancez la commande suivante :

Set-DistributionGroup -Identity « NomDeLaListeDeDistribution » -PrimarySmtpAddress « adresseemail@votredomaine.com » -Type « Office365 »

N’oubliez pas de vérifier les prérequis, comme les autorisations nécessaires, avant de procéder.



********
# Afficher les membres d’un seul groupe de distribution dynamique:

Get-Recipient -RecipientPreviewFilter (get-dynamicdistributiongroup "LDD - FR - EXPL DOMITYS RSS - DIRECTEURS ADJOINTS").RecipientFilter -OrganizationalUnit $_.RecipientContainer

Vous pouvez également exporter facilement les membres de n’importe quel groupe de distribution vers un fichier CSV en exécutant :

Get-Recipient -RecipientPreviewFilter (get-dynamicdistributiongroup <name of group>).RecipientFilter -OrganizationalUnit $_.RecipientContainer | Select Displayname | Export-Csv "<path of target CSV file>"

## Extract members DDL

# Générer une liste de membres de groupe dynamiques avec leur appartenance au groupe indiquée dans une colonne distincte :

foreach ($group in (Get-DynamicDistributionGroup)) {Get-Recipient -RecipientPreviewFilter $group.RecipientFilter -OrganizationalUnit $group.RecipientContainer -ResultSize Unlimited| Select DisplayName,@{n="Group";e={$group.name}}}

# Exporter le résultat du script dans un fichier séparé par des virgules :

foreach ($group in (Get-DynamicDistributionGroup)) {Get-Recipient -RecipientPreviewFilter $group.RecipientFilter -OrganizationalUnit $group.RecipientContainer -ResultSize Unlimited | Select DisplayName,@{n="Group";e={$group.name}} | Export-Csv "C:\Rscripts\LDD_members.csv" -Append} 

# Exporter les membres de groupes de distribution dynamique distincts vers des fichiers CSV distincts :

foreach ($group in (Get-DynamicDistributionGroup)) {Get-Recipient -RecipientPreviewFilter $group.RecipientFilter -OrganizationalUnit $group.RecipientContainer -ResultSize Unlimited| Select DisplayName | Export-Csv "c:\Rscripts\$group.members.csv"}

**
L’affichage des membres de tous les groupes de distribution dynamique n’est pas beaucoup plus difficile. 

foreach ($group in (Get-DynamicDistributionGroup)) {Get-Recipient -RecipientPreviewFilter $group.RecipientFilter -OrganizationalUnit $group.RecipientContainer | ft @{Expression={$_.displayname};Label=($group).name}}


***

Les résultats de la commande ci-dessus peuvent être exportés dans un fichier, mais comme ils sont déjà formatés, il ne peut pas s’agir d’un fichier CSV.

foreach ($group in (Get-DynamicDistributionGroup)) {Get-Recipient -RecipientPreviewFilter $group.RecipientFilter -OrganizationalUnit $group.RecipientContainer | ft @{Expression={$_.displayname};Label=($group).name} | Out-File "c:\<path>.txt" -Append}

***

$membres = Get-DynamicDistributionGroup -Identity "LDD - FR - EXPL DOMITYS RSS - DIRECTEURS ADJOINTS" 
Get-Recipient -RecipientPreviewFilter ($membres.RecipientFilter) | Format-Table -Property Name, location, CustomAttribute10, CountryOrRegion  | Out-File -FilePath  "C:\Rscripts\LDD_FR_RSS_DIRECTEURS_ADJOINTS2.txt"

 Get-Recipient -RecipientPreviewFilter (get-dynamicdistributiongroup "LDD - EU - EXPL DOMITYS RSS - DIRECTEURS").RecipientFilter -OrganizationalUnit $_.RecipientContainer

Name                                 RecipientType
----                                 -------------
Mylène MAROLLEAU                     UserMailbox
Aurélie LANCON                       UserMailbox
Cécile MONTAGNE                      UserMailbox
Cédric FAYOLLE


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


---------------------------------

Modifier un DDL

# Forcer l'actualisation des membres de "LDD - EU - EXPL DOMITYS RSS - DIRECTEURS"
Set-DynamicDistributionGroup -Identity "LDD - EU - EXPL DOMITYS RSS - DIRECTEURS" -ForceMembershipRefresh

Set-DynamicDistributionGroup -Identity "LDD - FR - PARIS" -ForceMembershipRefresh

# Permettre à "Denis BABONNEAU d'envoyer des mails à "LDD - EU - EXPL DOMITYS RSS - DIRECTEURS"
Set-DynamicDistributionGroup  -Identity "LDD - EU - EXPL DOMITYS RSS - DIRECTEURS" -AcceptMessagesOnlyFrom @{add="Denis BABONNEAU"}

Pour ajouter ou supprimer des expéditeurs sans affecter les autres entrées existantes, utilisez la syntaxe suivante : @{Add="Sender1","Sender2"...; Remove="Sender3","Sender4"...}

$extAtrValue="Contoso"

Set-DynamicDistributionGroup -Identity Developers -RecipientFilter "ExtensionCustomAttribute1 -eq '$extAtrValue'"


*****************
MAILBOX

# Accepter uniquement les messages provenant des utilisateurs Lori Penor, Jeff Phillips et des membres du groupe de distribution Legal Team 1.

Set-Mailbox -Identity "Robin Wood" -AcceptMessagesOnlyFrom "Lori Penor","Jeff Phillips" -AcceptMessagesOnlyFromDLMembers "Legal Team 1"

# Ajouter l'utilisateur David Pelton à la liste des utilisateurs dont les messages doivent être acceptés par la boîte aux lettres de Robin Wood.

Set-Mailbox -Identity "Robin Wood" -AcceptMessagesOnlyFrom @{add="David Pelton"}


# Configurer la boîte mail de Robin Wood pour refuser les messages provenant de Joe Healy, Terry Adams et des membres du groupe de distribution Legal Team 2.
Set-Mailbox -Identity "Robin Wood" -RejectMessagesFrom "Joe Healy","Terry Adams" -RejectMessagesFromDLMembers "Legal Team 2"


******
# VERIFICATION

Comment vérifier que l'opération a fonctionné ?
La commande suivante affiche toutes les restrictions de remise de messages configurées pour la boîte aux lettres de LDD - EU - EXPL DOMITYS RSS - DIRECTEURS:

Get-DynamicDistributionGroup -Identity "LDD - EU - EXPL DOMITYS RSS - DIRECTEURS" | Format-List AcceptMessagesOnlyFrom,AcceptMessagesOnlyFromDLMembers


# Retention d'une boite mail

![image](https://github.com/user-attachments/assets/680e1353-4710-42f5-9ad4-d70bcaf17e4d)


# Traduire le SID en nom d’utilisateur 


$objSID = New-Object System.Security.Principal.SecurityIdentifier("bcb04422-7a9e-4a83-9005-13de8238515d")
$objUser = $objSID.Translate([System.Security.Principal.NTAccount])
$userName = $objUser.Value
Write-Host "Nom d'utilisateur associé au SID : $userName"

# Renommez le compte local sur un ordinateur distant à l’aide d’informations d’identification stockées.
$userName = 'administrator@tech.local'
$password = '123qwe..'
[SecureString]$securepassword = $password | ConvertTo-SecureString -AsPlainText -Force 
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList $username, $securepassword
Invoke-Command -ComputerName 10.10.10.10 -ScriptBlock { Rename-LocalUser -Name "gohan" -NewName "trunks" } -credential $credential
**

$credential = New-Object System.Management.Automation.PSCredential -ArgumentList @('administrator@tech.local',(ConvertTo-SecureString -String '123qwe..' -AsPlainText -Force))
Invoke-Command -ComputerName 10.10.10.10 -ScriptBlock { Rename-LocalUser -Name "gohan" -NewName "trunks" } -credential $credential

