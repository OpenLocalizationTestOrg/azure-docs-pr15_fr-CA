<properties
    pageTitle="Services de domaine ActiveDirectory Azure : Guide de dépannage | Microsoft Azure"
    description="Guide de dépannage pour les Services de domaine Active Directory de Azure"
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
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Services de domaine Active Directory Azure - guide de dépannage
Cet article donne des conseils de dépannage pour les problèmes que vous pouvez rencontrer lors de la configuration ou la gestion des Services de domaine Azure Active Directory (AD).


## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Vous ne pouvez pas activer les Services de domaine Active Directory de Azure de votre annuaire AD Azure
Cette section vous aide à résoudre les erreurs lorsque vous essayez d’activer les Services de domaine Active Directory de Azure pour votre répertoire, et qu’il tombe en panne ou obtient basculée vers 'Désactivé'.

Sélectionnez les étapes de dépannage qui correspondent au message d’erreur que vous rencontrez.

|**Message d’erreur**|**Résolution**|
|---|:---|
|*Le nom contoso100.com est déjà utilisé sur ce réseau. Spécifiez un nom qui n’est pas en cours d’utilisation.*|[Conflit de nom de domaine dans le réseau virtuel](active-directory-ds-troubleshooting.md#domain-name-conflict)
|*Les Services de domaine n’a pas peuvent être activés dans cette locataire AD Azure. Le service ne dispose pas des autorisations appropriées à l’application appelée « Sync Services de domaine Active Directory de Azure ». Supprimer l’application appelée « Sync Services de domaine Active Directory de Azure » et essayez ensuite d’activer les Services de domaine pour vos clients AD Azure.*|[Les Services de domaine n’a pas les autorisations appropriées à l’application de synchronisation de Services de domaine Active Directory de Azure](active-directory-ds-troubleshooting.md#inadequate-permissions)|
|*Les Services de domaine n’a pas peuvent être activés dans cette locataire AD Azure. L’application de Services de domaine dans votre client AD Azure n’a pas les autorisations requises pour activer les Services de domaine. Supprimer l’application avec le d87dcbc6-a371-462e-88e3-28ad15ec4e64 d’identificateur d’application et essayez ensuite d’activer les Services de domaine pour vos clients AD Azure.*|[L’application de Services de domaine n’est pas configurée correctement dans vos clients](active-directory-ds-troubleshooting.md#invalid-configuration)|
|*Les Services de domaine n’a pas peuvent être activés dans cette locataire AD Azure. L’application Microsoft Azure AD est désactivée dans votre client AD Azure. Permettre à l’application avec le 00000002-0000-0000-c000-000000000000 d’identificateur d’application et essayez ensuite d’activer les Services de domaine pour vos clients AD Azure.*|[L’application de Microsoft Graph est désactivée dans votre client AD Azure](active-directory-ds-troubleshooting.md#microsoft-graph-disabled)|


### <a name="domain-name-conflict"></a>Conflit de nom de domaine
**Message d’erreur :**

*Le nom contoso100.com est déjà utilisé sur ce réseau. Spécifiez un nom qui n’est pas en cours d’utilisation.*

**Mise à jour :**

Assurez-vous que vous n’avez pas d’un domaine existant portant le même nom de domaine disponible sur ce réseau virtuel. Par exemple, supposons que vous avez un domaine appelé « contoso.com » déjà disponibles sur le réseau virtuel sélectionné. Par la suite, vous essayez d’activer un domaine géré de Services de domaine Active Directory de Azure portant le même nom de domaine (c'est-à-dire, « contoso.com ») sur ce réseau virtuel. Vous rencontrez une erreur lorsque vous tentez d’activer les Services de domaine Active Directory de Azure.

Cet échec est dû à des conflits de noms pour le nom de domaine du réseau virtuel. Dans ce cas, vous devez utiliser un nom différent pour définir votre domaine géré les Services de domaine Active Directory de Azure. Alternativement, vous pouvez mettre hors service le domaine existant et continuez à activer les Services de domaine Active Directory de Azure.


### <a name="inadequate-permissions"></a>Autorisations insuffisantes
**Message d’erreur :**

*Les Services de domaine n’a pas peuvent être activés dans cette locataire AD Azure. Le service ne dispose pas des autorisations appropriées à l’application appelée « Sync Services de domaine Active Directory de Azure ». Supprimer l’application appelée « Sync Services de domaine Active Directory de Azure » et essayez ensuite d’activer les Services de domaine pour vos clients AD Azure.*

**Mise à jour :**

Vérifiez s’il existe une application avec le nom « Domaine AD de Azure Services Sync » dans votre répertoire AD Azure. Si cette application existe, supprimez-le et réactivez les Services de domaine Active Directory de Azure.

Effectuez les étapes suivantes pour vérifier la présence de l’application et de le supprimer, si l’application existe :

  1. Accédez au **portail de classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Sélectionnez le nœud **Active Directory** dans le volet gauche.
  3. Sélectionnez le locataire Azure d’Active Directory (répertoire) pour lequel vous souhaitez activer les Services de domaine Active Directory de Azure.
  4. Accédez à l’onglet **Applications** .
  5. Dans la liste déroulante, sélectionnez l’option **Mon entreprise possède des Applications** .
  6. Vérifier une application appelée **synchronisation de Services de domaine Active Directory de Azure**. Si l’application existe, passez à la supprimer.
  7. Une fois que vous avez supprimé l’application, essayez d’activer les Services de domaine Active Directory de Azure une fois de plus.


### <a name="invalid-configuration"></a>Configuration non valide
**Message d’erreur :**

*Les Services de domaine n’a pas peuvent être activés dans cette locataire AD Azure. L’application de Services de domaine dans votre client AD Azure n’a pas les autorisations requises pour activer les Services de domaine. Supprimer l’application avec le d87dcbc6-a371-462e-88e3-28ad15ec4e64 d’identificateur d’application et essayez ensuite d’activer les Services de domaine pour vos clients AD Azure.*

**Mise à jour :**

Vérifiez si vous disposez d’une application avec le nom « AzureActiveDirectoryDomainControllerServices » (avec un identificateur d’application de d87dcbc6-a371-462e-88e3-28ad15ec4e64) dans votre répertoire AD Azure. Si cette application existe, vous devez supprimer et réactivez les Services de domaine Active Directory de Azure.

Utilisez le script PowerShell suivant pour trouver l’application et le supprimer.

> [AZURE.NOTE] Ce script utilise des applets de commande **PowerShell de publicité Azure version 2** . Pour une liste complète de toutes les cmdlets disponibles et pour télécharger le module, lisez la [documentation de référence de AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph désactivé
**Message d’erreur :**

Les Services de domaine n’a pas peuvent être activés dans cette locataire AD Azure. L’application Microsoft Azure AD est désactivée dans votre client AD Azure. Permettre à l’application avec le 00000002-0000-0000-c000-000000000000 d’identificateur d’application et essayez ensuite d’activer les Services de domaine pour vos clients AD Azure.

**Mise à jour :**

Vérifiez si vous avez désactivé une application avec l’identificateur 00000002-0000-0000-c000-type "000000000000". Cette application est l’application Microsoft Azure AD et fournit un accès graphique API à vos clients AD Azure. Azure Services de domaine Active Directory doit être activée pour synchroniser vos clients AD Azure pour votre domaine géré de cette application.

Pour résoudre cette erreur, activer cette application et essayez ensuite d’activer les Services de domaine pour vos clients AD Azure.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Les utilisateurs sont Impossible de se connecter au domaine des Services de domaine Active Directory de Azure managé
Si un ou plusieurs utilisateurs dans votre client AD Azure sont Impossible de se connecter au domaine géré nouvellement créé, procédez comme suit :

- **Connexion à l’aide du format UPN :** Essayez de vous connecter en utilisant le format UPN (par exemple, 'joeuser@contoso.com') et non au format SAMAccountName ('CONTOSO\joeuser'). SAMAccountName peut être généré automatiquement pour les utilisateurs dont le préfixe UPN est trop long ou qu’il est identique à un autre utilisateur sur le domaine géré. Le format UPN est garanti pour être unique au sein d’un locataire AD Azure.

> [AZURE.NOTE] Nous recommandons d’utiliser le format UPN pour vous connecter au domaine des Services de domaine Active Directory de Azure managé.

- Assurez-vous d’avoir [activé la synchronisation de mot de passe](active-directory-ds-getting-started-password-sync.md) , conformément à la procédure décrite dans le guide Mise en route.

- **Des comptes externes :** Assurez-vous que le compte d’utilisateur concerné n’est pas un compte externe dans le locataire AD Azure. Exemples de comptes externes des comptes de Microsoft (par exemple, 'joe@live.com') ou des comptes d’utilisateur d’un autre répertoire de publicité Azure. Dans la mesure où les Services de domaine Active Directory de Azure ne possède pas d’informations d’identification pour ces comptes d’utilisateur, ces utilisateurs Impossible de se connecter au domaine géré.

- **Synchronisée comptes :** Si les comptes d’utilisateur concernés sont synchronisés à partir d’un répertoire local, vérifiez que :
    - Vous avez déployé ou mis à jour vers la [version plus récente recommandée d’Azure Connect d’Active Directory](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - Vous avez configuré Azure Connect d’Active Directory pour [effectuer une synchronisation complète](active-directory-ds-getting-started-password-sync.md).

    - Selon la taille de votre répertoire, peut prendre un certain temps pour les comptes d’utilisateurs et d’informations d’identification de hachages pour être disponibles dans les Services de domaine Active Directory de Azure. Assurez-vous que vous attendez suffisamment longtemps avant la nouvelle tentative d’authentification (en fonction de la taille du répertoire - quelques heures pour un jour ou deux pour les grands répertoires).

    - Si le problème persiste après avoir vérifié les étapes précédentes, essayez de redémarrer le Service de synchronisation d’annonces Microsoft Azure. À partir de votre ordinateur de la synchronisation, lancez une invite de commande et exécutez les commandes suivantes :
      1. net stop 'Microsoft Azure AD Sync'
      2. Net start 'Microsoft Azure AD Sync'

- **Comptes de nuage uniquement**: si le compte d’utilisateur affecté est un compte d’utilisateur de nuage uniquement, assurez-vous que l’utilisateur a changé son mot de passe une fois que vous avez activé les Services de domaine Active Directory de Azure. Cette étape entraîne des hachages requis pour les Services de domaine Active Directory de Azure à générer les informations d’identification.


## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Les utilisateurs supprimés à partir de votre client AD Azure ne sont pas supprimés de votre domaine géré
Annonce Azure vous protège contre la suppression accidentelle d’objets utilisateur. Lorsque vous supprimez un compte d’utilisateur de vos clients AD Azure, l’objet utilisateur correspondant est déplacé vers la Corbeille. Lors de cette opération de suppression est synchronisée avec votre domaine géré, il provoque le compte d’utilisateur correspondant à être marqué comme désactivé. Cette fonctionnalité vous permet de récupérer ou de restaurer le compte d’utilisateur plus loin.

Pour supprimer entièrement le compte d’utilisateur de votre domaine géré, supprimer définitivement l’utilisateur à partir de votre client AD Azure. Utilisez l’applet de commande Remove-MsolUser PowerShell avec le '-RemoveFromRecycleBin' option, comme décrit dans cet [article MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit Azure des Services de domaine Active Directory [part de commentaires ou de prise en charge] (active-directory-ds-contact-us.md).
