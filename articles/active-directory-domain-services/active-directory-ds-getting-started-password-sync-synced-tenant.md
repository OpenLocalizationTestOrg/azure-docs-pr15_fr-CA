<properties
    pageTitle="Services de domaine Active Directory Azure : Activer la synchronisation de mot de passe | Microsoft Azure"
    description="Mise en route avec Azure des Services de domaine Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Activer la synchronisation de mot de passe pour les Services de domaine Active Directory de Azure
Dans les tâches précédentes, vous avez activé les Services de domaine Active Directory de Azure pour vos clients AD Azure. La tâche suivante consiste à activer la synchronisation des mots de passe pour les Services de domaine Active Directory de Azure. Une fois la synchronisation des informations d’identification est configurée, les utilisateurs peuvent signer le domaine géré à l’aide de leurs informations d’identification d’entreprise.

Les étapes sont différentes selon que votre organisation dispose d’une annonce Azure cloud uniquement client ou est configuré pour se synchroniser avec votre répertoire local à l’aide d’Azure Connect d’Active Directory.

<br>

> [AZURE.SELECTOR]
- [Annonce d’Azure cloud uniquement client](active-directory-ds-getting-started-password-sync.md)
- [Synchroniser les clients AD Azure](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Tâche 5 : Activer la synchronisation de mot de passe aux Services de domaine DAS pour un Azure synchronisé les clients AD
A synchronisé Azure les clients Active Directory sont configuré pour synchroniser avec l’annuaire de sur site de votre organisation à l’aide d’Azure Connect d’AD. Azure AD connexion ne synchronise pas NTLM et des informations d’identification Kerberos hachages pour Azure AD par défaut. Pour utiliser les Services de domaine Active Directory de Azure, vous devez configurer Azure Connect d’Active Directory pour synchroniser les hachages d’informations d’identification requises pour l’authentification NTLM et Kerberos. Les étapes suivantes permettent la synchronisation des hachages d’informations d’identification requises pour vos clients AD Azure.


### <a name="install-or-update-azure-ad-connect"></a>Installation ou actualisation Azure Connect de publicité
Installez la dernière version recommandée d’Azure Connect d’Active Directory sur un ordinateur faisant partie du domaine. Si vous avez une instance existante du programme d’installation d’Azure Connect d’Active Directory, vous devez mettre à jour pour utiliser la dernière version d’Azure Connect d’Active Directory. Pour éviter les problèmes/bogues connus qui ont déjà été résolus, assurez-vous que vous utilisez toujours la version la plus récente d’Azure Connect d’AD.

**[Connexion de téléchargement AD Azure](http://www.microsoft.com/download/details.aspx?id=47594)**

Recommandé de version : **1.1.281.0** - publié le 7 septembre 2016.

  > [AZURE.WARNING] Vous devez installer la dernière version recommandée d’Azure Connect AD pour activer les informations d’identification de l’ancien mot de passe (requises pour l’authentification NTLM et Kerberos) à synchroniser avec vos clients AD Azure. Cette fonctionnalité n’est pas disponible dans les versions antérieures d’Azure AD Connect ou avec l’outil de synchronisation d’annuaire hérité.

Instructions d’installation pour Azure Connect de publicité sont disponibles dans l’article suivant - [mise en route avec Azure Connect de publicité](../active-directory/active-directory-aadconnect.md)


### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Activer la synchronisation des hachages de d’informations d’identification NTLM et Kerberos dans Azure AD
Exécuter le script PowerShell suivant sur chaque forêt Active Directory, de forcer la synchronisation de mot de passe et activez les hachages de d’informations d’identification de tous les utilisateurs locaux à synchroniser avec vos clients AD Azure. Ce script active les hachages d’informations d’identification requises pour l’authentification NTLM/Kerberos être synchronisé avec vos clients AD Azure.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Selon la taille de votre répertoire (nombre d’utilisateurs, groupes etc.), la synchronisation des hachages d’informations d’identification Active Directory Azure prend du temps. Les mots de passe seront utilisables sur le domaine géré les Services de domaine Active Directory de Azure peu de temps après que les hachages d’informations d’identification ont synchronisé avec Active Directory Azure.


<br>

## <a name="related-content"></a>Contenu associé

- [Activer la synchronisation de mot de passe aux Services de domaine DAS pour un Azure cloud seule annuaire d’Active Directory](active-directory-ds-getting-started-password-sync.md)

- [Administrer un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Participez à une machine virtuelle Windows d’un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Joindre un ordinateur virtuel de Red Hat Enterprise Linux à un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
