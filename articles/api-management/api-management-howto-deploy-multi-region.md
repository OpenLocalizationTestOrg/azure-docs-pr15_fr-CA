<properties
    pageTitle="Comment déployer une instance de service de gestion d’API Azure à plusieurs régions Azure"
    description="Découvrez comment déployer une instance de service de gestion d’API Azure à plusieurs régions Azure." 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Comment déployer une instance de service de gestion d’API Azure à plusieurs régions Azure

API de gestion prend en charge le déploiement de plusieurs région qui permet aux éditeurs d’API distribuer un service de gestion d’API unique sur n’importe quel nombre de régions de Azure souhaités. Cela permet de réduire la demande latence perçue par consommateurs d’API séparés géographiquement et améliore également la disponibilité de service si une région est hors connexion. 

Lorsqu’un service de gestion de l’API est créé initialement, il contient une seule [unité][] et se trouve dans une région d’Azure unique, ce qui est désignée en tant que la zone primaire. Régions supplémentaires peuvent être ajoutées facilement via le portail classique d’Azure. Serveur de passerelle API gestion est déployé pour chaque région et le trafic d’appel doit être routé vers la passerelle la plus proche. Si une région est déconnectée, le trafic est redirigé automatiquement vers la passerelle plus proche suivante. 

> [AZURE.IMPORTANT] Déploiement de plusieurs région est disponible uniquement dans la couche de la **[prime][]** .

## <a name="add-region"> </a>Déployer une instance de service de gestion de l’API dans une nouvelle région

Pour commencer, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API. Cela vous amène sur le portail d’éditeur de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Accédez à l’onglet **échelle** de portail classique d’Azure pour votre instance de service de gestion de l’API. 

![Onglet échelle][api-management-scale-service]

Pour déployer vers une nouvelle zone, cliquez sur la liste déroulante sous la région principale et sélectionnez une région dans la liste.

![Ajouter la région][api-management-add-region]

Une fois que la zone est sélectionnée, choisissez le nombre d’unités pour la nouvelle zone dans la liste déroulante.

![Spécifier des unités][api-management-select-units]

Une fois que les zones de votre choix et les unités sont configurées, cliquez sur **Enregistrer**.

## <a name="remove-region"> </a>Supprimer une instance de service de gestion de l’API à partir d’une région

Pour supprimer une instance de service de gestion de l’API à partir d’une région, accédez à l’onglet **échelle** de portail classique d’Azure pour votre instance de service de gestion de l’API. 

![Onglet échelle][api-management-scale-service]

Cliquez sur le **X** vers la droite de la zone de votre choix à supprimer.  

![Supprimer la région][api-management-remove-region]

Une fois supprimés les zones de votre choix, cliquez sur **Enregistrer**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Mise en route de la gestion des API Azure]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unité]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

