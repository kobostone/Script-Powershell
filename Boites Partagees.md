### Pour lister toutes les boîtes aux lettres partagées où un utilisateur a des permissions :


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
