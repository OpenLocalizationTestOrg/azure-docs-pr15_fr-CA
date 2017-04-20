<properties
   pageTitle="Service web de mise à l’échelle | Microsoft Azure"
   description="Obtenir des informations à l’échelle d’un service web par l’augmentation de la concurrence et l’ajout de nouveaux points de terminaison."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="Azure apprentissage de l’ordinateur, les services web, operationalization, mise à l’échelle, le point de terminaison, d’accès concurrentiel"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/05/2016"
   ms.author="neerajkh"/>

# <a name="scaling-a-web-service"></a>Mise à l’échelle d’un service Web

>[AZURE.NOTE] Cette rubrique décrit les techniques applicables à un service Web de formation classique Machine. 

Par défaut, chaque service Web publié est configuré pour prendre en charge les 20 requêtes simultanées et peut être aussi élevée que 200 demandes simultanées. Alors que le portail classique Azure fournit un moyen de définir cette valeur, Azure Machine formation optimise automatiquement le paramètre afin de fournir les meilleures performances pour votre service web et la valeur de portail est ignorée. 

Si vous avez l’intention d’appeler l’API avec une charge supérieure à une valeur maximum d’appels simultanés de 200 prend en charge, vous devez créer plusieurs points de terminaison sur le même service Web. Vous pouvez ensuite distribuer la charge entre tous les au hasard.

## <a name="add-new-endpoints-for-same-web-service"></a>Ajouter les nouveaux points de terminaison pour le même service web

La mise à l’échelle d’un service Web est une tâche courante. Certaines raisons pour mettre à l’échelle sont à prendre en charge de plus de 200 demandes simultanées, augmenter la disponibilité par le biais de plusieurs points de terminaison ou fournir des points de terminaison distincts pour le service web. Vous pouvez augmenter l’échelle en ajoutant des points de terminaison supplémentaires pour le même service Web par le biais de [Azure portal classique](https://manage.windowsazure.com/) ou [Azure Machine formation Web Service](https://services.azureml.net/) portal.

Pour plus d’informations sur l’ajout de nouveaux points de terminaison, consultez [Création de points de terminaison de](machine-learning-create-endpoint.md).

Gardez à l’esprit que l’utilisation d’un nombre élevé d’opérations simultanées peut être préjudiciable si vous appelez pas l’API avec un taux élevé en conséquence. Vous pouvez voir des pics d’activité et/ou de délais d’attente sporadiques dans la latence si vous placez une charge relativement faible sur une API configurée pour une charge élevée.

Les API synchrones sont généralement utilisés dans les situations où un faible temps de latence est souhaitée. Latence ici implique le temps nécessaire pour l’API d’une demande et prise en compte des retards de réseau. Supposons que vous disposez d’une API avec une latence de 50 ms. Pour consommer entièrement de la capacité disponible avec le niveau de limitation haute et un maximum d’appels simultanés = 20, vous devez appeler cette API 20 * 1000 / 50 = 400 fois par seconde. Extension supplémentaire, un maximum d’appels simultanés de 200 permet à appeler l’API 4000 fois par seconde, en supposant une latence de 50 ms.

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png
