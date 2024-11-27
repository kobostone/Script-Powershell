### Pour lister toutes les boîtes aux lettres partagées où un utilisateur a des permissions :

Get-Mailbox -RecipientTypeDetails SharedMailbox -ResultSize Unlimited | Get-MailboxPermission | Where-Object { $_.User -like "laura.poulain@domitys.fr" } | Select-Object Identity, User, AccessRights


PS C:\> Get-Mailbox -RecipientTypeDetails SharedMailbox -ResultSize Unlimited | Get-MailboxPermission | Where-Object { $_.User -like "laura.poulain@domitys.fr" } | Select-Object Identity, User, AccessRights

Identity           User                     AccessRights
--------           ----                     ------------
Assistance NOTILUS laura.poulain@domitys.fr {FullAccess}
Base Fournisseur   laura.poulain@domitys.fr {FullAccess}
Factures.Aegide... laura.poulain@domitys.fr {FullAccess}
Retours Fournis... laura.poulain@domitys.fr {FullAccess}




PS C:\> Get-Mailbox -RecipientTypeDetails SharedMailbox | ForEach-Object {
>>     $Mailbox = $_
>>     $Permissions = Get-MailboxPermission -Identity $Mailbox.Identity | Where-Object { $_.User -eq "paula.stefan@domitys.fr" }
>>     if ($Permissions) {
>>         $Mailbox.Identity     }
>>  }
bpack
Assistance NOTILUS
Factures-italie
Factures-maurice
Base Fournisseur
Factures.Aegide-Domitys
Retours Fournisseurs

 Get-Mailbox -RecipientTypeDetails SharedMailbox | ForEach-Object {
>>     $Mailbox = $_
>>     $Permissions = Get-MailboxPermission -Identity $Mailbox.Identity | Where-Object { $_.User -eq "frederique.guichon@domitys.fr" }
>>     if ($Permissions) {
>>         $Mailbox.Identity
>>     }
>> }
compta.fournisseur.reglement
Factures.Aegide-Domitys
Retours Fournisseurs
Départs résidents
Factures Fichiers Integrés
Réclamation Demandes de Remboursement
PS C:\WINDOWS\system32>
PS C:\WINDOWS\system32>


#### Vérifier les permissions d'un utilisateur sur une boîte partagée spécifique :

Get-MailboxPermission -Identity "nom_de_la_boite_partagée" | Where-Object { $_.User -eq "utilisateur@mondomaine.com" }

Get-MailboxPermission -Identity "Réclamation Demandes de Remboursement" | Where-Object { $_.User -eq "frederique.guichon@domitys.fr" }


Identity             User                 AccessRights                                                                                                                                           IsInherited Deny
--------             ----                 ------------                                                                                                                                           ----------- ----
Réclamation Deman... frederique.guicho... {FullAccess}


#### Ajouter les droits Full Access à un utilisateur sur une boîte partagée :


Add-MailboxPermission -Identity "support@mondomaine.com" -User "utilisateur@mondomaine.com" -AccessRights FullAccess -InheritanceType All

### Ajouter les droits Send As à un utilisateur sur une boîte partagée :

Add-RecipientPermission -Identity "support@mondomaine.com" -Trustee "utilisateur@mondomaine.com" -AccessRights SendAs

### Vérifier les permissions :

Pour les droits Full Access :
Get-MailboxPermission -Identity "support@mondomaine.com" | Where-Object { $_.User -eq "utilisateur@mondomaine.com" }

Pour les droits Send As :

Get-RecipientPermission -Identity "support@mondomaine.com" | Where-Object { $_.Trustee -eq "utilisa
