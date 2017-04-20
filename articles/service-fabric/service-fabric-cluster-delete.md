<properties
   pageTitle="Supprimer un cluster Azure et ses ressources | Microsoft Azure"
   description="Découvrez comment faire pour supprimer complètement un Fabric du Service de cluster en le supprimant du groupe de ressource contenant le cluster ou par la suppression des ressources de manière sélective."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Supprimer un cluster du Service Fabric sur Azure et les ressources qu’il utilise

Un cluster de Service Fabric est constitué par de nombreuses autres ressources Azure en plus de la ressource de cluster lui-même. Ainsi, pour supprimer totalement un cluster Service Fabric vous devrez également supprimer toutes les ressources qu’il est constitué.
Vous disposez de deux options : soit supprimer le groupe de ressources figurant dans le cluster (ce qui supprime la ressource de cluster et d’autres ressources du groupe de ressources) ou en particulier supprimer la ressource de cluster, et il est associé à des ressources (mais pas d’autres ressources du groupe de ressources).

>[AZURE.NOTE] Suppression de la ressource de cluster **n’a pas** supprimer toutes les autres ressources composé de votre cluster Service Fabric.

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Supprimer le groupe de ressource entier (RG) le Fabric du Service de cluster est

Il s’agit de la façon la plus simple pour vous assurer que vous supprimez toutes les ressources associées à votre cluster, y compris le groupe de ressources. Vous pouvez supprimer le groupe de ressources à l’aide de PowerShell ou via le portail Azure. Si votre groupe de ressources comporte des ressources qui ne sont pas liées au Service de cluster de fabric, vous pouvez supprimer des ressources spécifiques.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Supprimer le groupe de ressources à l’aide de PowerShell d’Azure

Vous pouvez également supprimer le groupe de ressources en exécutant les applets de commande PowerShell de Azure suivant. Assurez-vous que Azure PowerShell 1.0 ou ultérieur est installé sur votre ordinateur. Si vous n’avez pas fait auparavant, suivez la procédure décrite dans [Comment faire pour installer et configurer le PowerShell Azure.](../powershell-install-configure.md)

Ouvrez une fenêtre PowerShell et exécuter les applets de commande PS suivantes :

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Vous obtiendrez une invite à confirmer la suppression, si vous n’avez pas utilisé le *-Force* option. Confirmation de la passerelle résidentielle et toutes les ressources qu’il contient sont supprimés.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Supprimer un groupe de ressources dans le portail Azure  

1. Connexion au [portail Azure](https://portal.azure.com).
2. Naviguez vers le cluster de Fabric du Service que vous souhaitez supprimer.
3. Cliquez sur le nom de groupe de ressources sur la page essentials de cluster.
4. Ceci fait apparaître la page **Ressources groupe Essentials** .
5. Cliquez sur **Supprimer**.
6. Suivez les instructions sur cette page pour terminer la suppression du groupe de ressources.

![Suppression du groupe de ressources][ResourceGroupDelete]


## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Supprimer la ressource de cluster et les ressources qu’il utilise, mais pas d’autres ressources du groupe de ressources

Si votre groupe de ressources a uniquement les ressources associées à du cluster Service Fabric que vous souhaitez supprimer, il est plus facile de supprimer le groupe de ressource entier. Si vous souhaitez supprimer de manière sélective les ressources une par une dans votre groupe de ressources, puis procédez comme suit.

Si vous avez déployé votre cluster à l’aide du portail ou à l’aide d’un des modèles de Service Fabric Manager de ressource à partir de la galerie de modèles, toutes les ressources que le cluster utilise sont balisés avec les deux balises suivantes. Vous pouvez les utiliser pour déterminer les ressources que vous souhaitez supprimer.

***De la balise #1 :*** Clé = nom_cluster, valeur = « nom du cluster »

***De la balise n° 2 :*** Clé = resourceName, valeur = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Supprimer des ressources spécifiques dans le portail Azure

1. Connexion au [portail Azure](https://portal.azure.com).
2. Naviguez vers le cluster de Fabric du Service que vous souhaitez supprimer.
3. Accédez à **tous les paramètres** sur la lame essentials.
4. Cliquez sur **balises** sous **Gestion des ressources** dans la lame de paramètres.
5. Cliquez sur l’une des **balises** de la lame de balises pour obtenir une liste de toutes les ressources avec cette balise.

    ![Balises de ressources][ResourceTags]

6. Une fois que la liste des ressources marquées, cliquez sur chacune des ressources et de les supprimer.

    ![Ressources marquées][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Supprimer les ressources à l’aide de PowerShell d’Azure

Vous pouvez supprimer les ressources une par une en exécutant les applets de commande PowerShell de Azure suivant. Assurez-vous que Azure PowerShell 1.0 ou ultérieur est installé sur votre ordinateur. Si vous n’avez pas fait auparavant, suivez la procédure décrite dans [Comment faire pour installer et configurer le PowerShell Azure.](../powershell-install-configure.md)

Ouvrez une fenêtre PowerShell et exécuter les applets de commande PS suivantes :

```powershell
Login-AzureRmAccount
```
Pour chacune des ressources que vous souhaitez supprimer, exécutez la commande suivante :

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Pour supprimer la ressource de cluster, exécutez la commande suivante :

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Étapes suivantes
Lisez également obtenir des informations sur la mise à niveau d’un cluster et le partitionnement des services suivants :

- [En savoir plus sur les mises à niveau de cluster](service-fabric-cluster-upgrade.md)
- [Obtenir des informations sur le partitionnement avec état services pour échelle maximale](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
