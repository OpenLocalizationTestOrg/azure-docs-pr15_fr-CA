<properties 
   pageTitle="Surveiller l’utilisation de statistiques et dans un service de recherche d’Azure | Microsoft Azure | Service de recherche de nuage hébergé" 
   description="Suivre la taille d’index et de la consommation de ressource de recherche d’Azure, un service de recherche de nuage hébergé sur Microsoft Azure." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="jhubbard" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="05/17/2016"
   ms.author="heidist"/>

# <a name="monitor-usage-and-statistics-in-an-azure-search-service"></a>Surveiller l’utilisation et des statistiques dans un service de recherche d’Azure

Suivi de la croissance d’index et de taille de document peut vous aider à ajuster proactivement la capacité avant d’atteindre la limite supérieure que vous avez défini pour votre service. 

Pour surveiller l’utilisation des ressources, nombres et les statistiques de votre service sont facilement affichés dans le [Portail Azure](https://portal.azure.com), mais vous pouvez également obtenir les informations par programme si vous créez un outil d’administration de service personnalisé. Cet article décrit les étapes à suivre pour ces deux techniques.

Vous pouvez également utiliser la fonction de recherche du trafic analytique pour une vue d’ensemble de l’activité au niveau de l’index. Visitez [Analytique le trafic de recherche pour la recherche d’Azure](search-traffic-analytics.md) pour commencer.

##<a name="view-counts-and-metrics-in-the-portal"></a>Afficher des nombres et des mesures dans le portail 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Ouvrez le panneau de commandes de service de votre service de recherche d’Azure. Mosaïques pour le service sont disponibles sur la page d’accueil, ou vous pouvez parcourir le service de navigation sur le JumpBar. Pour obtenir des instructions détaillées, voir [Création d’un service](search-create-service-portal.md) .

La section Utilisation comprend un compteur qui indique quelle partie des ressources disponibles sont en cours d’utilisation.

  ![][1]

Rappelez-vous qu’un maximum d’un réplica du service partagé et chaque partition. En outre, il peut uniquement prendre en charge 10 000 documents total ou 50 Mo de données, selon ce qui se produit en premier.

##<a name="get-index-statistics-using-the-rest-api"></a>Obtenir des statistiques d’index à l’aide de l’API REST

À la fois le Kit de développement .NET et l’API REST de recherche Azure fournissent un accès par programme aux métriques de service.  Si vous utilisez des [indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx) pour charger un index à partir de la base de données de SQL Azure ou DocumentDB, une API supplémentaire n’est disponible pour obtenir les numéros que vous avez besoin. 

  + [Obtenir des statistiques d’Index](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Nombre de Documents](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Obtenir le statut de l’indexation](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les [limites et la capacité](search-limits-quotas-capacity.md) pour déterminer la combinaison de partitions et duplications, que vous aurez besoin si la capacité existante est insuffisante. 

Pour plus d’informations sur l’administration de service, consultez [Gérer votre service de recherche sur Microsoft Azure](search-manage.md) .

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 
