<properties
   pageTitle="Créer un cluster autonome avec les ordinateurs virtuels d’Azure exécutant Windows | Microsoft Azure"
   description="Découvrez comment créer et gérer un cluster d’Azure Fabric de Service sur des machines virtuelles Azure exécutant Windows Server."
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
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Créer un cluster de Service Fabric d’autonome de trois nœuds avec des machines virtuelles Azure exécutant Windows Server

Cet article décrit comment créer un cluster sur Windows Azure machines virtuelles (VM), à l’aide du programme d’installation de Fabric de Service autonome pour Windows Server. Il s’agit d’un cas spécial de [créer et gérer un cluster s’exécutant sur Windows Server](service-fabric-cluster-creation-for-windows-server.md) où les ordinateurs virtuels sont [VMs Azure exécutant Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md), vous ne créez pas [un cluster Azure Fabric du Service de nuage](service-fabric-cluster-creation-via-portal.md). La différence est que le cluster de Service Fabric autonome créé par la procédure suivante est entièrement géré par vous lors de l’Azure cloud Service Fabric grappes de serveurs sont gérés et mis à niveau par le fournisseur de ressources de Service Fabric.


## <a name="steps-to-create-the-standalone-cluster"></a>Étapes pour créer le cluster autonome

1. Connectez-vous au portail Azure et créer un nouveau Windows Server 2012 R2 Datacenter ordinateur virtuel dans un groupe de ressources. Lisez l’article [créer un ordinateur virtuel Windows Azure portail](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour plus de détails.
2. Ajouter quelques davantage Windows Server 2012 R2 Datacenter d’ordinateurs virtuels au même groupe de ressources. Assurez-vous que chacun des ordinateurs virtuels a le même nom d’utilisateur administrateur et le mot de passe lors de la création. Une fois créé, vous devez voir tous les trois ordinateurs virtuels sur le même réseau virtuel.
3. Connectez-vous à chacun des ordinateurs virtuels et de désactiver le pare-feu Windows à l’aide du [Gestionnaire de serveur, le serveur Local de tableau de bord](https://technet.microsoft.com/library/jj134147.aspx). Cela garantit que le trafic réseau peut communiquer entre les machines. Lorsque vous êtes connecté à chaque ordinateur, obtenir l’adresse IP en ouvrant une invite de commande et en tapant `ipconfig`. Vous voyez également l’adresse IP de chaque ordinateur en sélectionnant la ressource de réseau virtuel pour le groupe de ressources dans le portail Azure.
4. Se connecter à l’un des ordinateurs virtuels et de test que vous pouvez tester les deux autres VM avec succès.
5. Se connecter à un des ordinateurs virtuels et [Télécharger le package de Service Fabric autonome pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) dans un nouveau dossier sur l’ordinateur et extrayez le package.
6. Ouvrez le fichier *ClusterConfig.Unsecure.MultiMachine.json* dans le bloc-notes et modifier chaque nœud avec trois adresses IP des ordinateurs. Modifier le nom du cluster en haut et enregistrez le fichier.  Vous trouverez ci-dessous un exemple partiel du manifeste cluster.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Ouvrez une [fenêtre de PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Accédez au dossier où vous extrait le package de programme d’installation autonome téléchargé et enregistré le fichier de manifeste du cluster. Exécutez la commande PowerShell suivante.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Vous devez voir le PowerShell exécuter, connectez-vous à chaque ordinateur et créer un cluster. Après environ une minute, vous pouvez vérifier si le cluster est opérationnel en vous connectant à l’Explorateur de tissu de Service sur l’une des adresses IP de l’ordinateur, par exemple en utilisant `http://10.7.0.5:19080/Explorer/index.html`. Dans la mesure où il s’agit d’un cluster autonome à l’aide de machines virtuelles d’Azure, pour assurer sa sécurité, vous devez [déployer des certificats pour les ordinateurs virtuels d’Azure](service-fabric-windows-cluster-x509-security.md) ou définir un des ordinateurs en tant que [contrôleur de Windows Server Active Directory (AD) pour l’authentification Windows](service-fabric-windows-cluster-windows-security.md), comme vous le feriez dans les locaux de.


## <a name="next-steps"></a>Étapes suivantes
- [Créer des clusters de tissu de Service autonome sur un serveur Windows ou Linux](service-fabric-deploy-anywhere.md)
- [Ajouter ou supprimer des nœuds à un cluster de tissu de Service autonome](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Paramètres de configuration de cluster de Windows autonome](service-fabric-cluster-manifest.md)
- [Sécuriser un cluster autonome sous Windows à l’aide de la sécurité de Windows](service-fabric-windows-cluster-windows-security.md)
- [Sécuriser un cluster autonome sous Windows à l’aide de la X509 des certificats](service-fabric-windows-cluster-x509-security.md)
