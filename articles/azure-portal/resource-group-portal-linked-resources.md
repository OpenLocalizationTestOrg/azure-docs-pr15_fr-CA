<properties 
    pageTitle="Ressources liées et liés dans la galerie de mosaïque" 
    description="Découvrez des ressources liées et liées qui sont affichent dans la galerie de la mosaïque du portail Azure aperçu." 
    services="azure-portal" 
    documentationCenter="" 
    authors="adamabdelhamed" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="adamab"/>

# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Ressources liées et liés dans la galerie de mosaïque

La galerie de mosaïque vous permet de rechercher fragments pour une ressource particulière et faites-les glisser vers votre carte actuelle. À l’aide de la galerie de mosaïque, vous pouvez créer des vues de gestion qui s’étendent sur les ressources. Pour n’importe quelle ressource spécifié, les ressources associées incluent toutes les ressources qui partagent le même groupe de ressources comme ressource et toutes les ressources qui renvoient vers ou à partir de la ressource.

## <a name="linked-resources-in-azure-resource-manager"></a>Ressources liées dans le Gestionnaire de ressources Azure

La liaison est une fonctionnalité du Gestionnaire de ressources Azure.  Il vous permet de déclarer les relations entre les ressources même si elles ne résident pas dans le même groupe de ressources. La liaison n’a aucun impact sur l’exécution de vos ressources, sans incidence sur la facturation et aucun impact sur l’accès par rôle.  Il est simplement un mécanisme que vous pouvez utiliser pour représenter les relations de sorte que les outils tels que la galerie de mosaïque peut fournir une gestion riche expérience.  Vos outils peuvent inspecter les liens à l’aide de l’API des liens et fournir ainsi des expériences de gestion de la relation personnalisée. 

## <a name="how-do-i-link-my-resources"></a>Comment pour lier mes ressources ?

Lorsque vous créez des ressources via le portail ou par le déploiement d’un modèle grâce à Azure PowerShell ou CLI d’Azure, les liens sont créés automatiquement pour certaines ressources dépendantes. Vous pouvez également programmer lier les ressources à l’aide de l' [API d’autres ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx) ou en déclarant les relations dans le modèle. Pour une description complète de l’utilisation de ressources liées, consultez [ressources de liaison dans le Gestionnaire de ressources Azure](../resource-group-link-resources.md).

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez besoin d’une introduction à l’écriture de modèles du Gestionnaire de ressources Azure, consultez [modèles de création](../resource-group-authoring-templates.md).
- Pour plonger dans plus de détails sur la création de liens entre les ressources, consultez [ressources de liaison dans le Gestionnaire de ressources Azure](../resource-group-link-resources.md).
- Pour savoir plus sur l’utilisation des groupes de ressources via le portail de l’aperçu, voir [l’aide du portail d’aperçu Azure pour gérer vos ressources Azure](resource-group-portal.md).
