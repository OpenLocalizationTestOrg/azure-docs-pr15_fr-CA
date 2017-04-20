<properties
    pageTitle="Services publics Azure | Microsoft Azure"
    description="Informations sur la gestion de votre abonnement dans Azure gouvernement"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/21/2016"
    ms.author="zakramer" />


#  <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Gestion et la connexion à votre abonnement dans Azure gouvernement

Gouvernement Azure a des URL et des points de terminaison pour gérer votre environnement unique. Il est important d’utiliser les connexions pour gérer votre environnement par le biais du portail ou PowerShell. Une fois que vous êtes connecté à l’environnement du gouvernement d’Azure, les opérations normales de gestion d’un service fonctionne si le composant a été déployé.

## <a name="connecting-via-the-portal"></a>Connexion via le portail
Le portail est le principal moyen de la plupart des gens se connectent au gouvernement d’Azure.  Pour vous connecter, accédez au portail à [https://portal.azure.us](https://portal.azure.us).  L’ancienne version du portail Azure sont accessibles via [https://manage.windowsazure.us](https://manage.windowsazure.us).

Abonnements peuvent être créés pour votre compte en vous connectant à [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Connexion via PowerShell

Si vous utilisez Azure PowerShell pour gérer un abonnement volumineux grâce à des fonctions de script ou d’accès qui ne sont pas actuellement disponibles dans le portail Azure, que vous devez vous connecter au gouvernement Azure au lieu Public Azure.  Si vous avez utilisé PowerShell dans Azure Public, il est essentiellement le même.  Les différences entre les administrations Azure sont :

+ Connexion de votre compte
+ Noms de région

>[AZURE.NOTE] Si vous n’avez pas encore utilisé PowerShell, consultez l' [Introduction à PowerShell d’Azure](../powershell-install-configure.md).

Lorsque vous démarrez PowerShell, vous devez indiquer PowerShell Azure pour se connecter au gouvernement d’Azure en spécifiant un paramètre d’environnement.  Le paramètre permet de s’assurer que PowerShell se connecte aux points de terminaison appropriés.  La collection de points de terminaison est déterminée lorsque vous vous connectez à se connecter à votre compte.  Différentes API requiert différentes versions du commutateur de l’environnement :

Type de connexion | Commande
---|----
Commandes de [Gestion de service](https://msdn.microsoft.com/library/dn708504.aspx) | `Add-AzureAccount -Environment AzureUSGovernment`
Commandes de [Gestion des ressources](https://msdn.microsoft.com/library/mt125356.aspx) | `Add-AzureRmAccount -EnvironmentName AzureUSGovernment`
Commandes [d’Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) | `Connect-MsolService -AzureEnvironment UsGovernment`
[Azure v2 de commande Active Directory](https://msdn.microsoft.com/library/azure/mt757189.aspx) | `Connect-AzureAD -AzureEnvironmentName AzureUSGovernment`

Vous pouvez également utiliser le commutateur de l’environnement lors de la connexion à un compte de stockage à l’aide de New-AzureStorageContext et spécifiez AzureUSGovernment.

### <a name="determining-region"></a>Détermination de la région

Une fois que vous êtes connecté, il existe une différence supplémentaire – les régions utilisées pour cibler un service.  Chaque nuage Azure a des régions différentes.  Vous pouvez les voir répertoriés sur la page de disponibilité du service.  Vous utilisez normalement la région dans le paramètre d’emplacement pour une commande.

Il y a une complication.  Les régions de gouvernement d’Azure doivent être formaté différemment de leurs noms communs :

Nom commun | Commande
---|----
US Gov Virginie | Virginie de USGov
US Gov Iowa | USGov Iowa

>[AZURE.NOTE] Il n’y a pas d’espace entre les États-Unis et Gov lors de l’utilisation du paramètre d’emplacement.

Si vous voulez valider les zones disponibles dans Azure gouvernement, vous pouvez exécuter les commandes suivantes et imprimez la liste en cours :

    Get-AzureLocation

Si vous êtes curieux de savoir les environnements disponibles sur Azure, vous pouvez exécuter :

    Get-AzureEnvironment

## <a name="next-steps"></a>Étapes suivantes

Si vous recherchez plus d’informations, vous pouvez consulter :

+ [Docs de PowerShell sur GitHub](https://github.com/Azure/azure-powershell)
+ [Instructions détaillées sur la connexion à la gestion des ressources](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
+ [Docs de PowerShell Azure sur MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Pour des informations supplémentaires et des mises à jour, abonnez-vous au [Microsoft Azure gouvernement Blog] (https://blogs.msdn.microsoft.com/azuregov/)
