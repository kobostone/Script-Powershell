 # Lister les utilisateurs ayant un accès (délégation ou droits directs) aux boîtes mail suivantes dans Exchange Online (Microsoft 365) : 

# Connexion à Exchange Online (si non déjà connecté)
Connect-ExchangeOnline

# Liste des boîtes à analyser
$mailboxes = @(
    "sirh@ade.fr",
    "service.paie.exploitation@ade.fr",
    "service.paie.siege@ade.fr"
)

foreach ($mbx in $mailboxes) {
    Write-Host "`n--- Accès à la boîte : $mbx ---" -ForegroundColor Cyan

    # Délégations "Full Access"
    $fullAccess = Get-MailboxPermission -Identity $mbx -ErrorAction SilentlyContinue | 
        Where-Object { $_.User -notlike "NT AUTHORITY\SELF" -and $_.IsInherited -eq $false }

    if ($fullAccess) {
        Write-Host "📬 Accès complet (Full Access):" -ForegroundColor Green
        $fullAccess | ForEach-Object {
            Write-Host " - $($_.User)"
        }
    } else {
        Write-Host "Aucun accès complet trouvé." -ForegroundColor DarkGray
    }

    # Accès à la boîte aux lettres via les autorisations de boîte aux lettres (Send As)
    $sendAs = Get-RecipientPermission -Identity $mbx -ErrorAction SilentlyContinue

    if ($sendAs) {
        Write-Host "✉️ Autorisation 'Send As' :" -ForegroundColor Yellow
        $sendAs | ForEach-Object {
            Write-Host " - $($_.Trustee)"
        }
    } else {
        Write-Host "Aucune autorisation 'Send As' trouvée." -ForegroundColor DarkGray
    }

    # Accès "Send on Behalf" (envoyer pour)
    $mbxDetails = Get-Mailbox -Identity $mbx
    if ($mbxDetails.GrantSendOnBehalfTo.Count -gt 0) {
        Write-Host "📤 Autorisation 'Send on Behalf':" -ForegroundColor Magenta
        $mbxDetails.GrantSendOnBehalfTo | ForEach-Object {
            Write-Host " - $_"
        }
    } else {
        Write-Host "Aucune autorisation 'Send on Behalf' trouvée." -ForegroundColor DarkGray
    }
}

# Déconnexion propre
# Disconnect-ExchangeOnline -Confirm:$false





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
