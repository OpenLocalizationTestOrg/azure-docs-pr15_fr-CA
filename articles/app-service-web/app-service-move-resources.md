<properties
    pageTitle="Déplacer des ressources d’application Web à un autre groupe de ressources"
    description="Décrit les scénarios où vous pouvez déplacer des applications Web et des Services d’application à partir d’un groupe de ressources à un autre."
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/04/2016"
    ms.author="zarizvi"/>
    
# <a name="supported-move-configurations"></a>Configurations de transfert pris en charge

Vous pouvez déplacer les ressources Azure Web App à l’aide de l' [Api de ressources déplacer ARM](../resource-group-move-resources.md).

Les applications Web Azure prend actuellement en charge les scénarios de déplacement suivantes :

* Déplacement de tout le contenu d’un groupe de ressources (applications web, les plans de service application et certificats) à un autre groupe de ressources 
    * Remarque : Le groupe de ressources de destination ne peut pas contenir toutes les ressources Microsoft.Web dans ce scénario
* Déplacer des applications web individuels à un groupe de ressources différent, tout en les hébergeant dans leur plan de service application actuel (le plan de service d’application reste dans l’ancien groupe de ressource)
