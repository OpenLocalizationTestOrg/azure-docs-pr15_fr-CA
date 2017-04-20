<properties
   pageTitle="Ajouter ou supprimer des nœuds à un cluster de Service Fabric autonome | Microsoft Azure"
   description="Apprenez à ajouter ou supprimer des nœuds à un cluster d’Azure Fabric de Service sur un ordinateur physique ou virtuel exécutant Windows Server, qui peut être local ou dans un nuage."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Ajouter ou supprimer des nœuds à un cluster de Service Fabric autonome s’exécutant sur Windows Server

Après avoir [créé votre cluster à structure de Service autonome sur les ordinateurs Windows Server](service-fabric-cluster-creation-for-windows-server.md), les besoins de votre entreprise peuvent changer afin que vous devrez peut-être ajouter ou supprimer plusieurs nœuds de votre cluster. Cet article fournit des étapes détaillées pour atteindre cet objectif.


## <a name="add-nodes-to-your-cluster"></a>Ajouter des nœuds de votre cluster.

1. Préparer la machine virtuelle/machine vous souhaitez ajouter à votre cluster en suivant les étapes mentionnées dans la section [préparer les machines pour remplir les conditions préalables pour le déploiement du cluster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Planifier les pannes domaine et mise à niveau vous vous apprêtez à ajouter cette machine virtuelle/machine.
3. Bureau à distance (RDP) à la VM/la machine que vous souhaitez ajouter au cluster.
4. Copie ou [Télécharger le package autonome pour le Service Fabric de Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) pour cette machine virtuelle/machine et décompressez le package.
5. Exécution de Powershell en tant qu’administrateur et accédez à l’emplacement du package décompressé.
6. Exécutez *AddNode.ps1* Powershell avec les paramètres décrivant le nouveau nœud à ajouter. L’exemple ci-dessous ajoute un nouveau nœud appelé VM5, avec le type NodeType0, l’adresse IP 182.17.34.52 dans UD1 et FD1. *ExistingClusterConnectionEndPoint* est un point de terminaison de connexion pour un nœud dans le cluster existant. Pour ce point de terminaison, vous pouvez choisir l’adresse IP de *n’importe quel* nœud dans le cluster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Supprimer des nœuds de votre cluster.

1. Selon le niveau de fiabilité choisi pour le cluster, vous ne pouvez pas supprimer les premiers nœuds (3/5/7/9) n du type de nœud principal
2. RemoveNode commande en cours d’exécution sur un cluster de développement n’est pas pris en charge.
2. Bureau à distance (RDP) à la VM/la machine que vous voulez supprimer du cluster.
2. Copie ou [Télécharger le package autonome pour le Service Fabric pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package pour cette machine virtuelle/machine.
3. Exécution de Powershell en tant qu’administrateur et accédez à l’emplacement du package décompressé.
4. Exécutez *RemoveNode.ps1* dans PowerShell. L’exemple ci-dessous supprime le nœud actif du cluster. *ExistingClientConnectionEndpoint* est un point de terminaison de connexion de client pour n’importe quel nœud qui reste dans le cluster. Sélectionnez l’adresse IP et le port du point de terminaison de *n’importe quel* **autre nœud** dans le cluster. Cette **autre nœud** sera à son tour mise à jour de la configuration du cluster pour le nœud supprimé. 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

Veuillez noter que même après la suppression d’un nœud, il apparaissent comme étant hors service dans les requêtes et SFX. Ceci est un défaut connu et sera corrigé dans une prochaine version. 


## <a name="next-steps"></a>Étapes suivantes
- [Paramètres de configuration de cluster de Windows autonome](service-fabric-cluster-manifest.md)
- [Sécuriser un cluster autonome sous Windows à l’aide de la sécurité de Windows](service-fabric-windows-cluster-windows-security.md)
- [Sécuriser un cluster autonome sous Windows à l’aide de la X509 des certificats](service-fabric-windows-cluster-x509-security.md)
- [Créer un cluster de Service Fabric d’autonome avec les ordinateurs virtuels d’Azure exécutant Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
