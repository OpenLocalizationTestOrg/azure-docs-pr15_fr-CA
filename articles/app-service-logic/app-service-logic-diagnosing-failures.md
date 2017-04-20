<properties
   pageTitle="Diagnostiquer les défaillances d’applications logique | Microsoft Azure"
   description="Approches communes pour comprendre où logique applications échouent"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="diagnosing-logic-app-failures"></a>Diagnostiquer les défaillances d’application logique

Si vous rencontrez des problèmes ou des échecs avec la fonctionnalité des applications de logique de Service d’application Azure, quelques approches peuvent vous aider à mieux comprendre la provenant les échecs.  

## <a name="azure-portal-tools"></a>Outils de portail Azure

Le portail Azure fournit de nombreux outils pour diagnostiquer chaque application logique à chaque étape.

### <a name="trigger-history"></a>Historique de déclencheur

Chaque application logique a au moins un déclencheur. Si vous remarquez que les applications ne sont pas déclencher, le premier endroit où rechercher des informations supplémentaires est l’historique de déclencheur. Vous pouvez accéder à l’historique de déclencheur sur la lame principale application de logique.

![Localisation de l’historique de déclencheur][1]

Répertorie toutes les tentatives de déclencheur que votre application logique a apportées. Vous pouvez cliquer sur chaque tentative de déclencheur pour obtenir le niveau suivant de détails (plus précisément, les entrées ou les sorties que la tentative de déclencheur générée). Si vous voyez des déclencheurs ayant échouées, cliquez sur la tentative de déclencheur et affiner le lien **sorties** pour afficher des messages d’erreur qui a peut-être été générés (par exemple, pour les informations d’identification FTP non valides).

Les différents statuts que vous pouvez voir sont :

* **Ignoré**. Il interrogé le point de terminaison pour vérifier des données et a reçu une réponse qu’aucune donnée n’est disponible.
* **A réussi**. Le déclencheur a reçu une réponse que les données étaient disponibles. Il peut s’agir d’un déclenchement manuel, un déclencheur de périodicité ou d’un déclencheur d’interrogation. Cela probablement est accompagné d’un statut de **déclenchement**, mais il peut pas si vous avez une condition ou une commande de SplitOn en mode code qui n’a pas été satisfaite.
* **A échoué**. Une erreur a été générée.

#### <a name="starting-a-trigger-manually"></a>Démarrage manuel de déclencheur

Si vous souhaitez que l’application de la logique à vérifier pour un déclencheur disponible immédiatement (sans attendre la prochaine répétition), vous pouvez cliquer sur **Sélectionner le déclencheur** sur la lame principale pour forcer une vérification. Par exemple, en cliquant sur ce lien avec un déclencheur d’échange provoque le flux de travail pour l’interrogation immédiatement des échange de nouveaux fichiers.

### <a name="run-history"></a>Historique de l’exécution

Chaque déclencheur est déclenché des résultats lors de l’exécution. Vous pouvez accéder à des informations exécution du serveur lame principale, qui contient un grand nombre d’informations qui peuvent être utiles de comprendre ce qui s’est passé pendant le flux de travail.

![Localisation de l’historique d’exécution][2]

Une série de tests affiche l’un des statuts suivants :

* **A réussi**. Toutes les actions a réussi, ou, s’il y a une défaillance, il a été géré par une action qui s’est produite plus loin dans le flux de travail. Autrement dit, il a été géré par une action qui a été définie pour s’exécuter après une action qui a échoué.
* **A échoué**. Au moins une action a eu une défaillance qui n’a pas été traitée par une action plus loin dans le flux de travail.
* **Annulé**. Le flux de travail en cours d’exécution, mais a reçu une demande d’annulation.
* **En cours d’exécution**. Le flux de travail est en cours d’exécution. Cela peut se produire pour les workflows qui sont limitées, ou le plan de Service de l’application en cours. Consultez les limites d’action sur la [page de tarification](https://azure.microsoft.com/pricing/details/app-service/plans/) pour plus d’informations. Configuration des diagnostics (les graphiques ci-dessous de l’historique d’exécution) peuvent également fournir des informations sur les événements de gaz qui se produisent.

Lorsque vous examinez un historique d’exécution, vous pouvez accéder pour plus de détails.  

#### <a name="trigger-outputs"></a>Sorties de déclencheur

Sorties de déclencheur affichent les données qui a été reçues à partir du déclencheur. Cela peut vous aider à déterminer si toutes les propriétés renvoyées comme prévu.

>[AZURE.NOTE] Il peut être utile de comprendre comment les applications de la logique de fonctionnalité [gère les différents types de contenu](app-service-logic-content-type.md) si vous voyez tout le contenu que vous ne comprenez pas.

![Exemples de sortie de déclencheur][3]

#### <a name="action-inputs-and-outputs"></a>Entrées et sorties action

Vous pouvez affiner les entrées et les sorties reçue par une action. Cela est utile pour comprendre la taille et la forme des sorties, ainsi que pour afficher des messages d’erreur qui ont été générés.

![Entrées et sorties action][4]

## <a name="debugging-workflow-runtime"></a>Débogage du runtime de workflow

Outre la surveillance les entrées, les sorties et les déclencheurs d’une exécution, il peut être utile d’ajouter quelques étapes au sein d’un flux de travail pour faciliter le débogage. [RequestBin](http://requestb.in) est un outil puissant que vous pouvez ajouter une étape dans un flux de travail. À l’aide de RequestBin, vous pouvez configurer un inspecteur de la demande HTTP pour déterminer la taille exacte, la forme et le format d’une demande HTTP. Vous pouvez créer un nouveau RequestBin et coller l’URL dans une application de logique action de publication HTTP avec le contenu du corps que vous souhaitez tester (par exemple, une expression ou une autre étape de sortie). Après l’exécution de l’application logique, vous pouvez actualiser votre RequestBin pour voir comment la requête a été formée comme il a été généré à partir du moteur d’applications de logique.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG
