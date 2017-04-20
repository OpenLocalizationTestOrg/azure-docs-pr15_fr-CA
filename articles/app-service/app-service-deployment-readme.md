<properties
    pageTitle="Déploiement d’Applications de Service d’application Azure"
    description="Découvrez comment déployer des applications pour le travail du Service de l’application"
    keywords="application service, azure application de service, déployer, déploiement"
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="dariagrigoriu"/>

# <a name="azure-app-service-deployment-overview"></a>Vue d’ensemble du déploiement de Service d’application Azure

Azure Application Service fournit une fonctionnalité riche et intégrée définie pour prendre en charge la création de workflows de déploiement puissant et flexible. Déploiement d’applications peut tirer parti des options qui incluent l’intégration continue ou un contrôle de source locale de publication, WebDeploy et FTP. La méthode recommandée pour le déploiement d’applications de production est l’échange d’emplacement de déploiement. Les emplacements de déploiement représentent les environnements de test et d’intégration associés à des applications de production. Les emplacements de déploiement peuvent être configurés et ciblés avec le trafic web pour la validation et le trafic peut être transférée à la demande pour le déploiement en production avec aucun temps d’arrêt et automatisé de mise en route. Les étapes d’un workflow de déploiement peuvent être facilement automatisées via le lancement des produits de gestion telles que la gestion de version de Visual Studio. Cela est utile pour la coordination avec les autres ressources de la solution (par exemple, stockent les données,) périodicité et la réplication sur plusieurs unités de déploiement. 

[AZURE.INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]
