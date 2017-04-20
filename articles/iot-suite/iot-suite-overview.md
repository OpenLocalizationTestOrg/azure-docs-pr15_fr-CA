<properties
    pageTitle="Vue d’ensemble de Microsoft Azure IoT Suite | Microsoft Azure"
    description="Vue d’ensemble de la façon d’Azure IoT Suite offre internet de solutions préconfigurées de choses pour collecter, analyser, stocker des données, fournir des visualisations et s’intègrent avec d’autres systèmes."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/09/2016"
     ms.author="dobett"/>

# <a name="what-is-azure-iot-suite"></a>Quelle est la Suite de IoT Azure ?

Azure internet de services d’éléments (IoT) offrent une large gamme de fonctions. Ces services de classe entreprise vous permettent de :

- Collecte de données à partir de périphériques
- Analysez le flux dans-déplacement de données
- Stocker et d’interroger les grands ensembles de données
- Visualiser les données historiques et en temps réel
- Intégration avec les systèmes back-office

Pour fournir ces fonctionnalités, les packages d’Azure IoT Suite ensemble plusieurs services Azure avec extensions personnalisées en tant que *solutions préconfigurées*. Ces solutions préconfigurées sont des implémentations de base de modèles de solution IoT communs qui permettant de réduire le temps de que vous prenez pour remettre vos solutions IoT. À l’aide de [kits de développement logiciel IoT][lnk-sdks], vous pouvez personnaliser et étendre ces solutions afin de répondre à vos besoins. Vous pouvez également utiliser ces solutions comme des exemples ou modèles lorsque vous développez de nouvelles solutions IoT.

La vidéo suivante fournit une introduction à la Suite de IoT Azure :

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## <a name="azure-iot-services-in-azure-iot-suite"></a>Services IoT Azure dans Azure IoT Suite

En général, les solutions préconfigurées utilisent les services suivants :

- Noyau pour Azure IoT Suite est le [Concentrateur de IoT Azure] [ lnk-iot-hub] service. Ce service fournit les fonctionnalités de messagerie périphérique-nuage et nuage-dispositif et agit comme la passerelle vers le nuage et autres services clés IoT Suite. Le service vous permet de recevoir des messages à partir de vos périphériques à grande échelle et envoyer des commandes à vos périphériques.

- [Analytique de flux Azure] [ lnk-asa] fournit l’analyse des données en mouvement. IoT Suite tire parti de ce service pour traiter la télémétrie entrant, effectue l’agrégation et détecter des événements. Les solutions préconfigurées utilisent également analytique de flux de données pour traiter les messages d’information qui contiennent des données telles que des réponses de métadonnées ou de la commande à partir de périphériques. Les solutions utilisent des flux Analytique pour traiter les messages de vos périphériques et remettre des messages à d’autres services.

- [Stockage Azure] [ lnk-azure-storage] et [Azure DocumentDB] [ lnk-document-db] fournissent les capacités de stockage de données. Les solutions préconfigurées utilisent stockage blob pour stocker de télémétrie et pour le rendre disponible pour l’analyse. Les solutions utilisent DocumentDB pour stocker les métadonnées de périphérique et d’activer les fonctionnalités de gestion de périphérique des solutions.

- [Les applications Web Azure] [ lnk-web-apps] et la [Puissance de Microsoft BI] [ lnk-power-bi] fournissent les fonctionnalités de visualisation de données. La flexibilité de BI d’alimentation vous permet de créer rapidement vos propres tableaux de bord interactifs qui utilisent des données de IoT Suite.

Pour une vue d’ensemble de l’architecture d’une solution IoT classique, consultez [Microsoft Azure et l’Internet des objets (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Solutions préconfigurées

IoT Suite comprend des solutions préconfigurées qui permettent de rapidement mise en route et d’Explorer les scénarios IoT courants, comme la *surveillance à distance* et *gestion prédictive*, qui permet d’Azure IoT Suite. Vous pouvez déployer ces solutions à votre abonnement Azure et exécutez un scénario IoT complète, de bout en bout.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble de ce que peut faire IoT Suite et quelles sont ses principaux composants, vous pouvez en savoir plus sur les solutions préconfigurées dans IoT Suite, reportez-vous à la section [Quels sont les IoT Azure préconfiguré solutions ?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
