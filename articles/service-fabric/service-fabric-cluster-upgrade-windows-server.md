<properties
   pageTitle="Mise à niveau d’un cluster à structure de Service autonome sur Windows Server | Microsoft Azure"
   description="Mettre à niveau le code de Service Fabric et/ou la configuration qui exécute un cluster autonome Fabric du Service, y compris la définition du mode de mise à jour de cluster"
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
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Mise à niveau de votre cluster de Service Fabric autonome sur Windows Server

> [AZURE.SELECTOR]
- [Cluster Azure](service-fabric-cluster-upgrade.md)
- [Cluster autonome](service-fabric-cluster-upgrade-windows-server.md)

Pour les systèmes modernes, conception de la mise à jour est la clé pour atteindre un succès à long terme de votre produit. Un cluster de tissu de Service est une ressource qui vous appartient. Cet article décrit comment vous pouvez vous assurer que le cluster s’exécute toujours les versions prises en charge de la fabric service code et les configurations.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Contrôle de la version de fabric qui s’exécute sur votre Cluster.

Vous pouvez définir votre cluster afin de télécharger les mises à jour de service fabric, lorsque Microsoft publie une nouvelle version ou cliquez sur pour sélectionner une version de fabric pris en charge que vous souhaitez que votre cluster sur. 

Pour cela, en définissant la configuration du cluster « fabricClusterAutoupgradeEnabled » à la valeur true ou false.


>[AZURE.NOTE] Assurez-vous de conserver votre toujours exécutant une version prise en charge de la Fabric du Service de cluster. Au fur et annonce de la sortie d’une nouvelle version de tissu de service, la version précédente est marquée pour la fin de la prise en charge après un délai minimal de soixante jours à partir de cette date. Les nouvelles versions sont annoncés [sur le blog de l’équipe service fabric](https://blogs.msdn.microsoft.com/azureservicefabric/ ). La nouvelle version est disponible, puis de choisir. 


Vous pouvez mettre à niveau votre cluster vers la nouvelle version que si vous utilisez une configuration de nœud de production-style, où chaque nœud Service Fabric est alloué sur un autre ordinateur physique ou virtuel. Si vous disposez d’un cluster de développement, dans le cas où il existe plusieurs nœuds de tissu de service sur un seul ordinateur physique ou virtuel, vous devez détruire votre cluster et recréer avec la nouvelle version.


Il existe deux flux de travail distinct pour la mise à niveau de votre cluster en plus tard ou d’une version de tissu de service pris en charge. Pour les clusters dotés de connectivité pour télécharger la dernière version automatiquement et le deuxième pour les clusters aucune connectivité à télécharger la dernière version de Service Fabric.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Mise à niveau des clusters avec connectivité pour télécharger les derniers code et configuration 

Procédez à la mise à niveau de votre cluster vers une version prise en charge, si vos nœuds de cluster sont connecter à internet à [http://download.microsoft.com](http://download.microsoft.com) 

Pour les clusters qui possèdent une connectivité [http://download.microsoft.com](http://download.microsoft.com), nous périodiquement vérifier la disponibilité de nouvelles versions de tissu de service.


Lorsqu’une nouvelle version de tissu de service n’est disponible, le package est téléchargé localement sur le cluster et mis en service pour la mise à niveau. En outre pour informer le client de cette nouvelle version, le système place un avertissement de santé cluster explicite semblable à la suivante :

« La cluster version actuelle (version #) [Date] met fin à la prise en charge. », une fois que le cluster est en cours d’exécution la plus récente, l’avertissement disparaît.


#### <a name="cluster-upgrade-workflow"></a>Workflow de mise à niveau de cluster.
 
Une fois que vous voyez l’avertissement de la santé du cluster, vous devez effectuer les opérations suivantes :

1. Connectez-vous au cluster à partir de n’importe quel ordinateur qui dispose d’un accès administrateur à tous les ordinateurs répertoriés comme des nœuds dans le cluster. Ce script est exécuté sur l’ordinateur ne doit pas faire partie du cluster

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. D’obtenir la liste de tissu de service que vous pouvez mettre à niveau vers les versions

    ```powershell

    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Vous devez obtenir une sortie similaire à ceci :

    ![obtenir des versions de tissu][getfabversions]

3. Lancer une mise à niveau de cluster à l’une des versions est disponible à l’aide de la [ServiceFabricClusterUpgrade-démarrer PowerShell cmd](https://msdn.microsoft.com/library/mt125872.aspx)

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Vous pouvez surveiller la progression de la mise à niveau dans l’Explorateur de tissu de Service ou en exécutant la commande shell de puissance à l’adresse suivante

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the Start-ServiceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Une fois que vous avez résolu les problèmes qui ont abouti à la restauration, vous devez lancer de nouveau, la mise à niveau en suivant la même procédure qu’avant.


### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Mise à niveau des clusters avec <U>aucune connectivité</u> pour télécharger le dernier code et la configuration

Procédez à la mise à niveau de votre cluster vers une version prise en charge, si votre cluster nœuds **n’ont pas** la connectivité internet à [http://download.microsoft.com](http://download.microsoft.com) 


>[AZURE.NOTE]Si vous exécutez un cluster qui n’est pas connectés à internet, vous devez surveiller le blog de l’équipe service fabric pour être averti d’une nouvelle version. Le système **n’a pas** de placer tout avertissement de santé cluster pour vous avertir de celui-ci.  

1. Modifier votre configuration de cluster pour définir la propriété suivante sur false.

        "fabricClusterAutoupgradeEnabled": false,

et de lancer une mise à niveau de la configuration. Pour plus de détails, voir la rubrique [Start-ServiceFabricClusterUpgrade PS cmd](https://msdn.microsoft.com/library/mt125872.aspx) . La version de manifeste de cluster est la version qui se trouve dans le clusterConfig.JSON. Veillez à mettre à jour avant lancement la mise à niveau de la configuration.

```powershell

    Start-ServiceFabricClusterUpgrade [-Config] [-ClusterConfigVersion] -FailureAction Rollback -Monitored 

```

#### <a name="cluster-upgrade-workflow"></a>Workflow de mise à niveau de cluster.
 


1. Télécharger la version la plus récente du package à partir du document de [créer un cluster service fabric pour windows server](service-fabric-cluster-creation-for-windows-server.md) 


1. Connectez-vous au cluster à partir de n’importe quel ordinateur qui dispose d’un accès administrateur à tous les ordinateurs répertoriés comme des nœuds dans le cluster. Ce script est exécuté sur l’ordinateur ne doit pas faire partie du cluster 

    ```powershell

    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Copiez le package téléchargé dans le magasin d’image de cluster.

    ```powershell

    ###### Get the list of available service fabric versions 
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"


    ```

2. Enregistrer le package copié 

    ```powershell

    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 

    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```


3. Lancer une mise à niveau de cluster à l’une des versions est disponible. 

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Vous pouvez surveiller la progression de la mise à niveau dans l’Explorateur de tissu de Service ou en exécutant la commande shell de puissance à l’adresse suivante

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the start-serviceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Une fois que vous avez résolu les problèmes qui ont abouti à la restauration, vous devez lancer de nouveau, la mise à niveau en suivant la même procédure qu’avant.



## <a name="next-steps"></a>Étapes suivantes
- Apprenez à personnaliser certains [paramètres de fabric cluster service fabric](service-fabric-cluster-fabric-settings.md)
- Découvrez comment faire [évoluer votre cluster en entrée et en sortie](service-fabric-cluster-scale-up-down.md)
- En savoir plus sur les [mises à niveau de l’application](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
