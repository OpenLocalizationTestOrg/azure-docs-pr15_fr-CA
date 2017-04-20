<properties
  pageTitle="IoT Azure Suite FAQ | Microsoft Azure"
  description="Forum aux questions sur la Suite de IoT"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="09/26/2016"
  ms.author="araguila"/>
   
# <a name="frequently-asked-questions-for-iot-suite"></a>Forum aux questions sur la Suite de IoT

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Quelle est la différence entre la suppression d’un groupe de ressources dans le portail Azure et en cliquant sur Supprimer une solution préconfigurée dans azureiotsuite.com ?

- Si vous supprimez la solution préconfigurée dans [azureiotsuite.com][lnk-azureiotsuite], vous supprimez toutes les ressources qui ont été mis en service lorsque vous avez créé la solution préconfigurée ; Si vous avez ajouté des ressources supplémentaires au groupe de ressources, ils sont également supprimés. 

- Si vous supprimez le groupe de ressources dans [Azure portal][lnk-azure-portal], vous supprimez uniquement les ressources de ce groupe de ressources ; Vous devez également supprimer l’application Azure Active Directory associée à la solution préconfigurée dans [Azure portal classique][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Le nombre d’instances IoT concentrateur puis-je configurer un abonnement ? 

Dix. Vous pouvez créer une [Azure prend en charge le ticket] [ link-azuresupportticket] pour augmenter cette limite, mais par défaut, vous seulement pouvez configurer les dix concentrateurs IoT par abonnement, comme indiqué dans les [limites de l’abonnement Azure][link-azuresublimits]. Par conséquent, dans la mesure où les dispositions de chaque solution préconfigurée un nouveau concentrateur IoT, vous pouvez uniquement prévoir jusqu'à dix solutions préconfigurées pour un abonnement donné. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Le nombre d’instances DocumentDB puis-je configurer un abonnement ?

Cinquante. Vous pouvez créer une [Azure prend en charge le ticket] [ link-azuresupportticket] pour augmenter cette limite, mais par défaut, vous pouvez configurer uniquement les cinquante instances de DocumentDB par abonnement. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Combien API de cartes Bing libre puis-je configurer un abonnement ?

Deux. Vous pouvez créer uniquement deux interne Transactions niveau 1 Bing Maps pour les plans de l’entreprise dans un abonnement Azure. La solution de surveillance à distance est configurée par défaut avec le plan interne les Transactions niveau 1. Par conséquent, vous ne pouvez allouer jusqu'à deux solutions surveillance à distance dans un abonnement sans modifications.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>J’ai un déploiement de solution de surveillance à distance avec un mappage statique, comment pour ajouter une carte Bing interactive ? 
1. Obtenez votre API Bing Maps pour entreprise QueryKey [d’Azure portal][lnk-azure-portal]: 
 1. Naviguez jusqu’au groupe de ressources où votre API Bing Maps pour entreprise est dans [Azure portal][lnk-azure-portal].
 2. Cliquez sur tous les paramètres, puis gestion. 
 3. Vous remarquerez que les deux clés : clé principale et QueryKey. Copiez la valeur de QueryKey.

     > [AZURE.NOTE] Vous n’avez une API Bing Maps pour le compte de l’entreprise ? Créer un [portail Azure] [ lnk-azure-portal] en cliquant + nouveau, recherche de l’API Bing Maps pour entreprise et suivez les invites pour créer.

2. Déroulez le dernier code d' [Azure-IoT-de surveillance à distance][lnk-remote-monitoring-github].

3. Exécuter un local ou en nuage déploiement suivant les instructions de déploiement de ligne de commande dans le dossier /docs/ dans le référentiel. 

4. Une fois que vous avez exécuté un local ou en nuage déploiement, recherchez dans votre dossier racine pour le *. fichier user.config créé pendant le déploiement. Ouvrez ce fichier dans un éditeur de texte. 

5. Modifiez la ligne suivante pour inclure la valeur que vous avez copié à partir de votre QueryKey : 
   
  `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Puis-je créer une solution préconfigurée si je dispose de Microsoft Azure pour DreamSpark ?
À ce stade, vous ne pouvez pas créer une solution préconfigurée avec un [Microsoft Azure pour DreamSpark] [ lnk-dreamspark] compte. Toutefois, vous pouvez créer un [compte d’évaluation gratuit pour Azure] [ lnk-30daytrial] dans les quelques minutes qui permettront de vous créez une solution préconfigurée.

### <a name="how-do-i-delete-an-aad-tenant"></a>Comment pour supprimer un locataire DAS ?

Consultez blog d’Eric Golpe [procédure pas à pas de la suppression d’un client de publicité Azure][lnk-delete-aad-tennant].

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également Explorer les capacités des solutions Suite IoT préconfiguré et certaines autres fonctionnalités :

- [Présentation de la solution préconfigurée de maintenance PREVENTIVE][lnk-predictive-overview]
- [Sécurité IoT de bas en haut][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
