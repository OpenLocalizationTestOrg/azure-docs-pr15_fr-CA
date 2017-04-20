<properties 
    pageTitle="Quel type de collection avez-vous besoin pour Azure RemoteApp ? | Microsoft Azure" 
    description="Obtenir des informations sur les types de collections disponibles avec Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>Quel type de collection avez-vous besoin pour Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Azure RemoteApp vous permet de partager des applications et des ressources avec les utilisateurs sur n’importe quel périphérique. Nous en créant des collections pour stocker les applications et les ressources, et puis vous partagez ces collections avec les utilisateurs. Il existe 2 options de collection différents, avec différente options réseau et d’authentification - qui vous convient ?

Examinons les différentes considérations et les choix que vous devez faire tirer le meilleur parti de votre collection Azure RemoteApp. 


## <a name="quick-differences-between-the-collection-types"></a>Rapides différences entre les types de collection

|           | Nuage | Hybride |
|-----------|-------|--------|
|Utilisez un VNET existant| Oui| Oui|
|Requiert que les comptes de connexion AD (DirSync)| N°| Oui|
|Requiert la jonction de domaine| N°| Oui|
|Nécessite le contrôleur de domaine accessible à VNET| N°| Oui|

## <a name="cloud-collections"></a>Collections de nuage
- Rapide à créer - la collection est rapidement mis en service, ce qui signifie l’obtenir de vos applications aux utilisateurs plus rapides.
- Mettre vos propres applications ou partager le nôtre. Vous pouvez utiliser une image personnalisée (généré à partir d’un ordinateur virtuel d’Azure) ou une des images inclus dans votre abonnement.
- Vous n’avez pas besoin de configurer une connexion entre votre collection et de votre domaine local.
- Mais vous pouvez éventuellement utiliser vos propres VNET Azure pour fournir l’accès à votre environnement sur site pour partager des données ou pour utiliser l’authentification Windows non en ressources, telles que SQL Server (à l’aide de l’authentification de base de données).


OK, comment en créer une ?

- Nuage ? Création avec l’option de **Création rapide** dans le portail.
- Nuage + VNET ? Créer à l’aide de l’option **créer avec VNET** , mais ne sélectionnez pas rejoindre un domaine.

## <a name="hybrid-collections"></a>Collections hybride
- Fournir un accès total au réseau de locaux + VNET Azure.
- Comprend un accès de jointure de domaine pour les applications et les données. Applications distantes peuvent l’authentification par rapport à Active Directory sur site, ils peuvent accéder aux ressources de votre domaine.
- Activer les options avancées de surveillance et de gestion avec les solutions System Center existantes et les stratégies de groupe Windows (via une image personnalisée basée sur Windows Server 2012 R2)
- Prise en charge [ExpressRoute](https://azure.microsoft.com/services/expressroute/) pour votre VNET Azure de se connecter à votre VNET local.

Créer à l’aide de l’option **créer avec VNET** et décidez de rejoindre un domaine.

## <a name="authentication-options"></a>Options d’authentification
RemoteApp Azure prend en charge les comptes de Microsoft et Azure Active Directory, mais pas de toutes les collections prennent en charge toutes les méthodes. 

| Type de compte                      |                                                             | Nuage | Nuage + VNET | Hybride |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Compte Microsoft                 |                                                             | Oui   | Oui          | N°     |
| Azure Active Directory (AD Azure) |                                                             |       |              |        |
|                                   | Azure annonce uniquement                                               | Oui   | Oui          | N°     |
|                                   | Connexion d’Active Directory avec la synchronisation de mot de passe                               | Oui   | Oui          | Oui    |
|                                   | Connexion d’Active Directory sans la synchronisation de mot de passe                            | Oui   | Oui          | N°     |
|                                   | Active Directory se connecter avec AD FS                                       | Oui   | Oui          | Oui    |
|                                   | fournisseurs d’identité de prise en charge Azure 3 rd-party (par exemple, Ping) | Oui   | Oui          | Oui    |
| Plusieurs facteurs d’authentification       |                                                             | Oui   | Oui          | Oui    |



### <a name="cloud-and-cloud--vnet"></a>Cloud et nuage + VNET 
Avec les collections du nuage, vous pouvez utiliser Microsoft comptes, les comptes Active Directory Azure ou un mélange des deux. Utiliser les comptes qui fonctionnent le mieux pour vos utilisateurs.

Il n’existe aucune exigence particulière pour l’utilisation de comptes de Microsoft. 

Si vous souhaitez utiliser les comptes Active Directory Azure, vous devez vous assurer que vos clients AD Azure correspond à celui associé à votre abonnement. Lorsque vous avez créé votre abonnement Azure RemoteApp, le locataire AD Azure que vous utilisez a été automatiquement associé à votre abonnement. N’importe quel utilisateur AD Azure à que vous autorisez doit être de ce même client. Si nécessaire, vous pouvez [Modifier le locataire AD Azure](remoteapp-changetenant.md) associé à votre abonnement.
 
### <a name="hybrid-or-cloud--azure-ad--ad"></a>Hybride (ou nuage + Azure AD + AD)

À l’aide d’Azure AD + sur site Active Directory est une condition préalable pour une collection hybride. Vous devez utiliser la connexion d’Active Directory pour intégrer les deux annuaires. Mais vous avez certains choix lorsqu’il s’agit de la configuration de connexion d’Active Directory. 

Il y a 2 AD scénarios de connexion - à l’aide de la synchronisation de mot de passe ou fédération d’Active Directory. Vérifiez les [informations de connexion d’Active Directory](../active-directory/active-directory-aadconnect.md) pour déterminer lequel de ces fonctionne mieux pour vous.

Vous pouvez également utiliser Active Directory Azure + AD avec une collection de nuage. Assurez-vous que vous suivez le même paramétrage d’étapes.

Extraire [AD Azure + Active Directory requise pour Azure RemoteApp](remoteapp-ad.md) pour connaître les étapes nécessaires à la configuration Active Directory Azure et Active Directory.

## <a name="go-create-your-collection"></a>Allez créer votre collection !
OK, je pense que nous avons trouvé maintenant, il est plus qu’une chose qui reste à faire - permet de créer votre première collection Azure RemoteApp.

[Créer une collection de nuage](remoteapp-create-cloud-deployment.md) ou [créer une collection hybride](remoteapp-create-hybrid-deployment.md) - simplement obtenir la création.
