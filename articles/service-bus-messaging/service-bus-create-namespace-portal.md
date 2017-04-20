<properties
    pageTitle="Créer un espace de noms du Bus des services via le portail Azure | Microsoft Azure"
    description="Pour vous familiariser avec le Bus de Service, vous aurez besoin d’un espace de noms. Voici comment en créer une avec le portail Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Créer un espace de noms du Bus des services via le portail Azure

Un espace de noms est un conteneur commun pour tous les composants de votre messagerie. Plusieurs files d’attente et les rubriques peuvent résider dans un espace de noms, et les espaces de noms servent souvent de conteneurs de l’application. Il existe actuellement deux façons de créer un espace de noms du Bus de Service.

1.  Azure portal (cet article)

2.  [Modèles du Gestionnaire de ressources][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Créer un espace de noms dans le portail Azure

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Félicitations ! Vous avez maintenant créé un espace de noms de messagerie de Bus de Service.

## <a name="next-steps"></a>Étapes suivantes

Consultez nos [exemples de GitHub] [ github-samples] qui présentent certaines des fonctionnalités plus avancées de la messagerie de Bus de Service Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples