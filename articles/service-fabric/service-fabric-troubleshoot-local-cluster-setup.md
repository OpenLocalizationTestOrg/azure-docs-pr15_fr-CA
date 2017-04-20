<properties
   pageTitle="Résoudre les problèmes liés à votre programme d’installation de cluster local Service Fabric | Microsoft Azure"
   description="Cet article présente un ensemble de propositions pour le dépannage de votre cluster de développement local"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="troubleshoot-your-local-development-cluster-setup"></a>Résoudre les problèmes de votre configuration de cluster de développement local

Si vous rencontrez un problème lors de l’interaction avec le cluster de développement Azure Fabric de Service local, passez en revue les suggestions suivantes pour les solutions potentielles.

## <a name="cluster-setup-failures"></a>Échecs d’installation de cluster

### <a name="cannot-clean-up-service-fabric-logs"></a>Ne peut pas nettoyer les journaux de Service Fabric

#### <a name="problem"></a>Problème

Lors de l’exécution du script DevClusterSetup, vous voyez ce type d’erreur :

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solution

Fermez la fenêtre PowerShell en cours et ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devez désormais être en mesure d’exécuter correctement le script.

## <a name="cluster-connection-failures"></a>Échecs de connexion de cluster

### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Applets de commande PowerShell de tissu de service ne sont pas reconnus dans Azure PowerShell

#### <a name="problem"></a>Problème

Si vous essayez d’exécuter des applets de commande PowerShell de tissu de Service, tel que `Connect-ServiceFabricCluster` dans une fenêtre PowerShell d’Azure, il échoue, indiquant que l’applet de commande n’est pas reconnu. La raison à cela est que Azure PowerShell utilise la version 32 bits de Windows PowerShell (même sur les versions de système d’exploitation 64 bits), alors que les applets de commande de Service Fabric fonctionnent uniquement dans les environnements 64 bits.

#### <a name="solution"></a>Solution

Toujours exécuter les applets de commande de Service Fabric directement à partir de Windows PowerShell.

>[AZURE.NOTE] La dernière version de Azure PowerShell ne crée pas un raccourci spécial, donc c’est n’est plus le cas.

### <a name="type-initialization-exception"></a>Exception d’initialisation de type

#### <a name="problem"></a>Problème

Lorsque vous vous connectez au cluster dans PowerShell, vous voyez l’erreur TypeInitializationException pour System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solution

Votre variable de chemin d’accès n’est pas définie correctement lors de l’installation. Veuillez quitter Windows et vous connecter de nouveau. Cela permettra d’actualiser totalement votre chemin d’accès.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Échec de la connexion de cluster avec « L’objet est fermé »

#### <a name="problem"></a>Problème

Un appel à ServiceFabricCluster de la connexion échoue avec une erreur comme suit :

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solution

Fermez la fenêtre PowerShell en cours et ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devez désormais être en mesure de se connecter.

### <a name="fabric-connection-denied-exception"></a>Exception de connexion refusée de tissu

#### <a name="problem"></a>Problème

Lors du débogage de Visual Studio, vous obtenez une erreur FabricConnectionDeniedException.

#### <a name="solution"></a>Solution

Cette erreur se produit généralement lorsque vous essayez pour essayez de démarrer un hôte de service traitez manuellement, au lieu de laisser le runtime Service Fabric pour le démarrer.

Assurez-vous que vous n’avez pas les projets de service défini en tant que projets de démarrage dans votre solution. Seuls les projets d’application Fabric du Service doivent être définis en tant que projets de démarrage.

>[AZURE.TIP] Si, après l’installation, votre cluster local commence à se comporter de manière anormale, vous pouvez le réinitialiser à l’aide de l’application de barre d’état système de cluster local manager. Cela supprime le cluster existant et définir un nouveau. Veuillez noter que toutes les applications déploiement et données associées seront supprimées.

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre et résoudre les problèmes de votre cluster avec les rapports état de santé du système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualiser votre cluster Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
