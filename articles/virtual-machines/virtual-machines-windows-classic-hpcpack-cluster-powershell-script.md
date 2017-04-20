<properties
   pageTitle="Script PowerShell pour déployer Windows HPC cluster | Microsoft Azure"
   description="Exécuter un script PowerShell pour déployer un cluster Windows HPC Pack dans Azure des machines virtuelles"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Créer un cluster de l’informatique hautes performances (HPC) de Windows avec le script de déploiement des HPC Pack IaaS

Exécution du déploiement des HPC Pack IaaS script PowerShell pour déployer un cluster HPC complète pour les charges de travail de Windows dans des machines virtuelles Azure. Le cluster est constitué d’un nœud principal associés à Active Directory, Windows Server et Microsoft HPC Pack et Windows supplémentaires calculent les ressources que vous spécifiez. Si vous souhaitez déployer un cluster HPC Pack dans Azure pour les charges de travail Linux, voir [créer un cluster HPC de Linux avec le script de déploiement des HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Vous pouvez également utiliser un modèle de gestionnaire de ressources Azure pour déployer un cluster HPC Pack. Pour obtenir des exemples, consultez [créer un cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) et [créer un cluster à haute performance avec un fichier image de nœud de calcul](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Exemples de fichiers de configuration

Dans les exemples suivants, remplacer par vos propres valeurs pour votre Id d’abonnement ou nom et les noms de compte et de service.

### <a name="example-1"></a>Exemple 1

Le fichier de configuration suivant déploie un cluster HPC Pack qui a un nœud de tête avec les bases de données locales et cinq nœuds exécutant le système d’exploitation Windows Server 2012 R2. Tous les services de cloud sont créés directement dans l’emplacement des États-Unis Ouest. Le nœud principal agit en tant que contrôleur de domaine de la forêt de domaine.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Exemple 2

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaine existante. Le cluster a 1 nœud principal avec les bases de données locales et de 12 nœuds de calcul avec l’extension BGInfo VM appliquée.
Installation automatique des mises à jour Windows est désactivée pour tous les ordinateurs virtuels dans la forêt de domaine. Tous les services de cloud sont créés directement dans l’emplacement de l’Asie de l’est. Les nœuds de calcul sont créés dans les trois services en nuage et trois comptes de stockage : _MyHPCCN-0001_ à _MyHPCCN-0005_ dans _MyHPCCNService01_ et _mycnstorage01_; _MyHPCCN-0006_ à _MyHPCCN0010_ dans _MyHPCCNService02_ et _mycnstorage02_; et _MyHPCCN-0011_ en _MyHPCCN-0012_ dans _MyHPCCNService03_ et _mycnstorage03_). Les nœuds de calcul sont créés à partir d’une image existante privée, capturée à partir d’un nœud de calcul. Agrandissement et la réduction automatique service est activé par défaut agrandissement et la réduction des intervalles.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Exemple 3

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaine existante. Le cluster contient un nœud principal, serveur d’une base de données avec un disque de 500 Go de données, 2 nœuds broker exécutant le système d’exploitation Windows Server 2012 R2 et cinq nœuds de calcul exécutant le système d’exploitation Windows Server 2012 R2. Le service en nuage MyHPCCNService est créé dans le groupe d’affinité *MyIBAffinityGroup*, et les autres services en nuage sont créés dans le groupe d’affinité *MyAffinityGroup*. L’API REST de HPC tâche du planificateur et le portail web HPC sont activés sur le nœud de tête.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Exemple 4

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaine existante. Le cluster a deux nœud principal avec les bases de données locales, deux modèles de nœud Azure sont créés et trois nœuds de taille moyenne Azure sont créées pour le modèle de nœud Azure _AzureTemplate1_. Un fichier de script s’exécute sur le nœud principal après avoir configuré le nœud de tête.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Résolution des problèmes


* **Erreur de « VNet n’existe pas »** - si vous exécutez le script pour déployer plusieurs clusters dans Azure simultanément sous un seul abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « VNet *VNet\_nom* n’existe pas ».
Si cette erreur se produit, exécutez le script à nouveau pour le déploiement non réussi.

* **Problème d’accès à Internet à partir du réseau virtuel Azure** - si vous créez un cluster avec un contrôleur de domaine en utilisant le script de déploiement, ou que vous promouvez manuellement d’un nœud de tête VM au contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion des ordinateurs virtuels à Internet. Ce problème peut se produire si un redirecteur DNS serveur est automatiquement configuré sur le contrôleur de domaine et serveur DNS redirecteur ne résout pas correctement.

    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimer le paramètre de configuration du redirecteur ou configurer un serveur DNS de redirecteur valide. Pour configurer ce paramètre, dans le Gestionnaire de serveur, cliquez sur **Outils** >
    **DNS** pour ouvrir le Gestionnaire DNS, puis double-cliquez sur **des redirecteurs**.

* **Problèmes d’accès réseau RDMA à partir d’ordinateurs virtuels de calcul intensif** - si vous ajoutez compute de Windows Server ou broker VMs de nœud à l’aide d’une taille compatible RDMA A8 ou A9, vous pouvez rencontrer des problèmes de connexion des ordinateurs virtuels sur le réseau d’application RDMA. Ce problème se produit est si l’extension de HpcVmDrivers n’est pas installée correctement lorsque les ordinateurs virtuels sont ajoutés au cluster. Par exemple, l’extension peut être bloquée à l’état de l’installation.

    Pour contourner ce problème, vérifiez d’abord l’état de l’extension dans les ordinateurs virtuels. Si l’extension n’est pas installée correctement, essayez de supprimer les nœuds du cluster HPC et puis ajoutez de nouveau les nœuds. Par exemple, vous pouvez ajouter le nœud de calcul ordinateurs virtuels en exécutant le script HpcIaaSNode.ps1-ajouter sur le nœud principal.
    
## <a name="next-steps"></a>Étapes suivantes

* Essayez d’exécuter un test de charge sur le cluster. Pour obtenir un exemple, consultez le HPC Pack [guide Mise en route](https://technet.microsoft.com/library/jj884144).

* Pour voir un didacticiel pour le déploiement de cluster de script et exécuter une charge de travail informatique hautes performances, [mise en route avec un cluster HPC Pack dans Azure pour exécuter des charges de travail Excel et SOA](virtual-machines-windows-excel-cluster-hpcpack.md).

* Essayez les outils de Microsoft HPC Pack pour démarrer, arrêter, ajouter et supprimer des nœuds de calcul à partir d’un cluster que vous créez. Reportez-vous à la section [Gérer les nœuds dans un cluster HPC Pack dans Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Afin d’utiliser pour soumettre les tâches au cluster à partir d’un ordinateur local, consultez [HPC de soumettre des tâches à partir d’un ordinateur local à un cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).
