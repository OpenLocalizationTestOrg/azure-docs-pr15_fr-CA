<properties
    pageTitle="Quel est le Traffic Manager | Microsoft Azure"
    description="Cet article vous aidera à comprendre ce qu’est le Traffic Manager, et s’il est le choix de routage de trafic droit pour votre application"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-traffic-manager"></a>Vue d’ensemble du Gestionnaire de trafic

Microsoft Azure Traffic Manager vous permet de contrôler la distribution du trafic d’utilisateur pour les points de terminaison de service dans différents centres de données. Points de terminaison de service pris en charge par le Gestionnaire de trafic incluent Azure VM, applications Web et les services en nuage. Vous pouvez également utiliser le Gestionnaire de trafic avec les points de terminaison externes, non-Azure.

Traffic Manager utilise le système de nom de domaine (DNS) pour diriger les demandes des clients vers le point de terminaison plus approprié en fonction de la [méthode de routage de trafic](traffic-manager-routing-methods.md) et de l’état de santé des points de terminaison. Traffic Manager fournit une gamme de méthodes de routage de trafic en fonction des besoins de différentes applications, de point de terminaison de l’état [de surveillance](traffic-manager-monitoring.md)et de basculement automatique. Traffic Manager est résiliente à la défaillance, y compris l’échec d’une région entière de Azure.

## <a name="traffic-manager-benefits"></a>Avantages du Gestionnaire de trafic

Traffic Manager peut vous aider à :

- **Améliorer la disponibilité des applications critiques**

    Traffic Manager offre une disponibilité élevée de vos applications en analyse vos points de terminaison et grâce à un basculement automatique lorsqu’un point de terminaison tombe en panne.

- **Améliorer la réactivité pour des applications haute performance**

    Azure vous permet d’exécuter des services en nuage ou sites Web dans les centres de données dans le monde entier. Traffic Manager améliore la réactivité des applications en dirigeant le trafic vers le point de terminaison avec la latence du réseau le plus bas pour le client.

- **Effectuer la maintenance de service sans interruption de service**

    Vous pouvez effectuer des opérations de maintenance planifiée sur vos applications sans interruption de service. Traffic Manager dirige le trafic vers d’autres points de terminaison lors de la maintenance est en cours.

- **Combiner des locaux et des applications basées sur le nuage**

    Traffic Manager prend en charge externe, les points de terminaison non-Azure en lui permettant d’être utilisé avec hybride en nuage et les déploiements, y compris le « burst-nuage, » « migration-nuage, » et « basculement-à-nuage » scénarios locaux.

- **Répartir le trafic pour les déploiements de grande taille, complexes**

    À l’aide de [profils de Traffic Manager imbriqués](traffic-manager-nested-profiles.md), les méthodes de routage de trafic peuvent être combinés pour créer des règles sophistiquées et flexibles pour répondre aux besoins des déploiements plus grands et plus complexes.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le [fonctionnement du Gestionnaire de trafic](traffic-manager-how-traffic-manager-works.md).

- Apprenez à développer des applications à haute disponibilité à l’aide du [Gestionnaire de trafic contrôle de point de terminaison](traffic-manager-monitoring.md).

- Pour en savoir plus sur les [méthodes de routage de trafic](traffic-manager-routing-methods.md) pris en charge par le Gestionnaire de trafic.

- [Créer un profil de gestionnaire de trafic](traffic-manager-manage-profiles.md).

