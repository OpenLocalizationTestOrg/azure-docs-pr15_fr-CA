<properties
   pageTitle="Script PowerShell pour déployer Linux HPC cluster | Microsoft Azure"
   description="Exécuter un script PowerShell pour déployer un cluster HPC Pack de Linux dans des machines virtuelles Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Créer un Linux haute performance computing clusters (HPC) avec le script de déploiement des HPC Pack IaaS

Exécution du déploiement des HPC Pack IaaS script PowerShell pour déployer un cluster HPC complète pour les charges de travail Linux dans des machines virtuelles Azure. Le cluster comporte un nœud principal associés à Active Directory, Windows Server et Microsoft HPC Pack et des nœuds de calcul qui exécutent l’un des distributions Linux prises en charge par le HPC Pack. Si vous souhaitez déployer un cluster HPC Pack dans les charges de travail Azure pour Windows, reportez-vous à la section [créer un cluster HPC de Windows avec le script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). Vous pouvez également utiliser un modèle de gestionnaire de ressources Azure pour déployer un cluster HPC Pack. Pour obtenir un exemple, consultez [créer un cluster à haute performance avec Linux pour les nœuds de calcul](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Exemple de fichier de configuration

Le fichier de configuration suivant crée un nouveau contrôleur de domaine et la forêt de domaine et les déploie un cluster HPC Pack qui a 1 nœud principal avec les bases de données locales et des nœuds de calcul Linux 10. Tous les services de cloud sont créés directement dans l’emplacement de l’Asie de l’est. Les nœuds de calcul Linux sont créés dans les services en nuage 2 et 2 comptes de stockage (par exemple, _MyLnxCN-0001_ à _MyLnxCN-0005_ dans _MyLnxCNService01_ et _mylnxstorage01_) et _MyLnxCN-0006_ à _MyLnxCN-0010_ dans _MyLnxCNService02_ et _mylnxstorage02_. Les nœuds de calcul sont créés à partir d’une image de Linux CentOS de OpenLogic version 7.0. 

Remplacer par vos propres valeurs pour le nom de votre abonnement et les noms de compte et de service.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Résolution des problèmes

* **Erreur de « VNet n’existe pas »** - si vous exécutez le script de déploiement des HPC Pack IaaS pour déployer plusieurs clusters dans Azure simultanément sous un seul abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « VNet *VNet\_nom* n’existe pas ».
Si cette erreur se produit, exécutez à nouveau le script de déploiement a échoué.

* **Problème d’accès à Internet à partir du réseau virtuel Azure** - si vous créez un cluster HPC Pack avec un contrôleur de domaine en utilisant le script de déploiement, ou que vous promouvez manuellement d’un nœud de tête VM au contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion des ordinateurs virtuels dans le réseau virtuel Azure à Internet. Cela peut se produire si un redirecteur DNS serveur est automatiquement configuré sur le contrôleur de domaine et serveur DNS redirecteur ne résout pas correctement.

    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimer le paramètre de configuration du redirecteur ou configurer un serveur DNS de redirecteur valide. Pour ce faire, dans le Gestionnaire de serveur, cliquez sur **Outils** >
    **DNS** pour ouvrir le Gestionnaire DNS, puis double-cliquez sur **des redirecteurs**.
    
## <a name="next-steps"></a>Étapes suivantes

* Reportez-vous à la section [mise en route avec les nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) pour plus d’informations sur les distributions Linux prises en charge, le déplacement des données et soumettre des tâches à un cluster HPC Pack avec Linux les nœuds de calcul.
* Pour des didacticiels qui utilisent le script pour créer un cluster et exécuter une charge de travail Linux HPC, voir :
    * [Exécutez NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
    * [Exécutez OpenFOAM avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
    * [Exécutez STAR-CCM + Microsoft HPC Pack sous Linux avec des nœuds de calcul dans Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)
