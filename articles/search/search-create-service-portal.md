<properties
    pageTitle="Créer un service de recherche d’Azure via le portail Azure | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Apprenez à configurer un service de recherche d’Azure via le portail d’Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-service-using-the-azure-portal"></a>Créer un service de recherche d’Azure via le portail Azure

Ce guide vous guidera tout au long du processus de création (ou mise en service) un service de recherche d’Azure via le [Portail Azure](https://portal.azure.com/).

Ce guide suppose que vous disposez d’un abonnement Azure déjà et pouvez se connecter au portail d’Azure.

## <a name="find-azure-search-in-the-azure-portal"></a>Dans le portail Azure, rechercher recherche Azure
1. Accédez au [Portail Azure](https://portal.azure.com/) et connectez-vous.
1. Cliquez sur le signe plus (« + ») dans le coin supérieur gauche.
2. Sélectionnez les **données + stockage**.
3. Sélectionnez **Rechercher d’Azure**.

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>Choisissez un nom de service et d’un point de terminaison URL pour votre service
1. Le nom de votre service fera partie de l’URL du point de terminaison de votre service de recherche d’Azure par rapport à laquelle vous effectuerez vos appels d’API pour gérer et utiliser le service de recherche.
2. Dans le champ **URL** , tapez le nom de votre service. Le nom du service :
  * doit contenir uniquement des lettres minuscules, des chiffres ou des tirets («- »)
  * Impossible d’utiliser un tiret («- ») en tant que les 2 premiers caractères ou le dernier caractère
  * ne peut pas contenir de tirets consécutifs («-- »)
  * est compris entre 2 et 60 caractères


## <a name="select-a-subscription-where-you-will-keep-your-service"></a>Sélectionnez un abonnement où vous devrez vous assurer votre service
Si vous avez plus d’un abonnement, vous pouvez sélectionner celui qui inclut ce service de recherche d’Azure.

## <a name="select-a-resource-group-for-your-service"></a>Sélectionnez un groupe de ressources de votre service
Créer un nouveau groupe de ressources ou sélectionnez-en une existante. Un groupe de ressources est un ensemble de services Azure et les ressources qui sont utilisées ensemble. Par exemple, si vous utilisez recherche d’Azure à une base de données SQL d’index, ces deux services doivent être partie du même groupe de ressources.

## <a name="select-the-location-where-your-service-will-be-hosted"></a>Sélectionnez l’emplacement où sera hébergé le service
Comme un service Azure, recherche d’Azure est disponible pour être hébergés dans des centres de données dans le monde entier. Veuillez noter que [les prix peuvent différer](https://azure.microsoft.com/pricing/details/search/) par zone géographique.

## <a name="select-your-pricing-tier"></a>Sélectionnez votre niveau de tarification
[Recherche d’Azure n’est actuellement proposé dans plusieurs niveaux de prix](https://azure.microsoft.com/pricing/details/search/): libre, Standard ou de base. Chaque couche possède ses propres [limites et la capacité](search-limits-quotas-capacity.md). Pour obtenir des instructions, reportez-vous à la section [Choisir un prix ou point de stock](search-sku-tier.md) .

Dans ce cas, nous avons choisi la couche Standard pour notre service.

## <a name="select-the-create-button-to-provision-your-service"></a>Sélectionnez le bouton « Créer » pour configurer votre service

![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>Mettre à l’échelle votre service

Une fois que votre service est créé, vous mettre à l’échelle pour répondre à vos besoins. Si vous avez choisi le niveau Standard pour votre service de recherche d’Azure, vous pouvez mettre à l’échelle votre service en deux dimensions : les partitions et les réplicas. Si vous avez choisi le niveau de base, vous ne pouvez ajouter que des réplicas.

*__Les partitions__* permettent à votre service stocker et rechercher des documents plus.

*__Les doubles__* permettent à votre service gérer une charge plus élevée des requêtes de recherche - [un service nécessite les 2 réplicas pour atteindre un SLA en lecture seule et requiert 3 réplicas pour atteindre un SLA en lecture-écriture](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Accédez à la blade de gestion de votre service de recherche d’Azure dans le portail Azure.
2. De la lame de **paramètres** , sélectionnez **Echelle**.
3. Vous pouvez adapter votre service en ajoutant des duplications ou des Partitions.
  * Vous ne pouvez pas mettre à l’échelle votre service au-delà de recherche 36 unités. Le nombre total d’unités de recherche est le produit de vos duplications et les partitions (réplicas * Partitions = Total des unités de recherche).
  * Si vous avez choisi le niveau de base, vous pouvez uniquement mettre à l’échelle à 3 réplicas. Services de base sont liés à une seule partition.

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>Suivant
Mise en service d’un service de recherche d’Azure, vous serez prêt à [définir un index de recherche d’Azure](search-what-is-an-index.md) afin de télécharger et de rechercher vos données.

Pour obtenir un didacticiel rapide, consultez [mise en route avec Azure la recherche dans le portail](search-get-started-portal.md) .
