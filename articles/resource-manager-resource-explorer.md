<properties
   pageTitle="Explorateur de ressources Azure | Microsoft Azure"
   description="Décrit l’Explorateur de ressources Azure et comment elle peut être utilisée pour afficher et mettre à jour les déploiements via le Gestionnaire de ressources Azure"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# <a name="use-azure-resource-explorer-to-view-and-modify-resources"></a>Utilisez l’Explorateur de ressources Azure pour afficher et modifier des ressources
L' [Explorateur de ressources Azure](https://resources.azure.com) est un excellent outil pour la recherche de ressources que vous avez déjà créés dans votre abonnement. À l’aide de cet outil, vous pouvez comprendre comment les ressources sont structurés et voir les propriétés affectées à chaque ressource. Vous pouvez obtenir des informations sur les opérations de l’API REST et les applets de commande PowerShell disponibles pour un type de ressource, et vous pouvez émettre des commandes par le biais de l’interface. Explorateur de ressources peut être particulièrement utile lorsque vous créez des modèles du Gestionnaire de ressources, car il vous permet d’afficher les propriétés des ressources existantes.

La source de l’outil Explorateur de ressources est disponible sur [github](https://github.com/projectkudu/ARMExplorer), qui fournit une référence précieuse si vous souhaitez implémenter un comportement similaire dans vos propres applications.

## <a name="view-resources"></a>Affichage des ressources
Accédez à [https://resources.azure.com](https://resources.azure.com) et connectez-vous avec les mêmes références que vous utiliseriez pour le [Portail Azure](https://portal.azure.com).

Une fois chargé, l’arborescence sur la gauche vous permet de consulter vos abonnements et les groupes de ressources :

![TreeView](./media/resource-manager-resource-explorer/are-01-treeview.png)

Lorsque vous accédez à un groupe de ressources, vous verrez les fournisseurs pour lesquels il y a ressources dans ce groupe :

![fournisseurs](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

À partir de là, vous pouvez démarrer perçage dans les instances de la ressource. Dans la capture d’écran ci-dessous, vous pouvez voir les `sltest` instance SQL Server dans le contrôle treeview. Sur le côté droit, vous pouvez voir des informations sur les requêtes de l’API REST que vous pouvez utiliser avec cette ressource. En naviguant vers le nœud d’une ressource, Explorateur de ressources effectue automatiquement la requête GET pour extraire des informations sur la ressource. Dans la zone de texte ci-dessous l’URL, vous verrez la réponse de l’API. 

Lorsque vous vous serez familiarisé avec les modèles du Gestionnaire de ressources le contenu du corps commence à quelque chose ! La section **Propriétés** de la réponse correspond aux valeurs que vous pouvez fournir dans la section **Propriétés** de votre modèle.

![SQL server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

Explorateur de ressources permet à conserver descente d’Explorer les ressources enfants, en ce qui concerne le serveur de base de données SQL, ressources enfants d’éléments tels que des bases de données et des règles de pare-feu.

Exploration d’une base de données nous montre les propriétés de cette base de données. Dans la capture d’écran ci-dessous, nous pouvons voir que la base de données `edition` est `Standard` et `serviceLevelObjective` (ou au niveau de la base de données) est `S1`.

![base de données SQL](./media/resource-manager-resource-explorer/are-04-database-get.png)

## <a name="change-resources"></a>Modifier des ressources

Une fois que vous avez accédé à une ressource, vous pouvez sélectionner le bouton Modifier pour modifier le contenu JSON. Vous pouvez ensuite utiliser l’Explorateur de ressources pour modifier le JSON et envoyer une requête PUT pour modifier la ressource. Par exemple, l’illustration ci-dessous indique le niveau de base de données en `S0`:

![base de données - demande PUT](./media/resource-manager-resource-explorer/are-05-database-put.png)

En sélectionnant **mettre** vous soumettez la demande. 

Une fois que la demande a été soumise Explorateur de ressources retransmet la requête GET pour actualiser le statut. Dans ce cas, nous pouvons voir que la `requestedServiceObjectiveId` a été mis à jour et est différente de la `currentServiceObjectiveId` indiquant qu’une opération de mise à l’échelle est en cours. Vous pouvez cliquer sur le bouton GET pour actualiser le statut manuellement.

![base de données - GET MAX2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## <a name="performing-actions-on-resources"></a>Exécution d’Actions sur les ressources

L’onglet **Actions** vous permet de voir et d’effectuer d’autres opérations de reste. Par exemple, lorsque vous avez sélectionné une ressource de site web, l’onglet Actions présente une longue liste d’opérations disponibles, dont certaines sont présentées ci-dessous.

![Web - requête POST](./media/resource-manager-resource-explorer/are-web-post.png)

## <a name="invoking-the-api-via-powershell"></a>L’appel de l’API via PowerShell
L’onglet PowerShell dans l’Explorateur de ressources affiche les applets de commande à utiliser pour interagir avec les ressources qui vous sont en train d’Explorer. Selon le type de ressource que vous avez sélectionnée, le script PowerShell affiché peut être comprise entre les applets de commande simples (tel que `Get-AzureRmResource` et `Set-AzureRmResource`) pour les applets de commande plus complexe (par exemple la permutation des emplacements sur un site web). 

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Pour plus d’informations sur les applets de commande PowerShell de Azure les consultez [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](powershell-azure-resource-manager.md)

## <a name="summary"></a>Résumé
Lorsque vous travaillez avec le Gestionnaire de ressources, l’Explorateur de ressources peut être un outil extrêmement utile. Il est idéal pour trouver des moyens d’utiliser PowerShell pour interroger et apporter des modifications. Si vous travaillez avec l’API REST, il est idéal pour découvrir et tester rapidement des appels d’API avant de commencer à écrire du code. Et si vous écrivez les modèles, qu'il peut être un excellent moyen de comprendre la hiérarchie des ressources et déterminer où placer la configuration - vous pouvez apporter une modification dans le portail, puis les entrées correspondantes dans l’Explorateur de ressources !

Pour plus d’informations, regardez la [vidéo avec Scott Hanselman et David Ebbo de Channel 9](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)


