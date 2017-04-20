<properties
 pageTitle="Cluster HPC Pack pour Excel et SOA | Microsoft Azure"
 description="Mise en route des charges de travail Excel et SOA à grande échelle en cours d’exécution sur un cluster HPC Pack dans Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/25/2016"
 ms.author="danlep"/>

# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Mise en route des charges de travail Excel et SOA en cours d’exécution sur un cluster HPC Pack dans Azure

Cet article vous explique comment déployer un cluster Microsoft HPC Pack sur des ordinateurs virtuels Azure à l’aide d’un modèle de quickstart Azure, ou éventuellement un script de déploiement Azure PowerShell. Le cluster utilise les images Azure Marketplace VM conçus pour exécuter Microsoft Excel ou des charges de travail architecture orientée services (SOA) avec le HPC Pack. Vous pouvez utiliser le cluster pour exécuter des HPC de Excel simple et services SOA à partir d’un ordinateur de client sur site. Les services Excel HPC incluent le déchargement de classeur Excel et les fonctions définies par l’utilisateur d’Excel ou UDF.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

À un niveau élevé, le diagramme suivant illustre le cluster HPC Pack que vous le créer.

![Cluster à haute performance avec nœuds exécutant des charges de travail Excel][scenario]

## <a name="prerequisites"></a>Conditions préalables

*   **Ordinateur client** , vous avez besoin d’un ordinateur client Windows à soumettre des exemples des tâches Excel et SOA au cluster. Vous devez également un ordinateur Windows pour exécuter le script de déploiement de cluster Azure PowerShell (si vous choisissez cette méthode de déploiement) et

*   **Abonnement azure** - si vous n’avez pas un abonnement Azure, vous pouvez créer un [compte de libérer](https://azure.microsoft.com/pricing/free-trial/) dans quelques minutes.

*   **Quota de cœurs** - vous devrez peut-être augmenter le quota de cœurs, surtout si vous déployez plusieurs nœuds de cluster avec des tailles de machine virtuelle multicœurs. Si vous utilisez un modèle quickstart Azure, le quota de cœurs dans le Gestionnaire de ressources est par région Azure. Dans ce cas, vous devrez augmenter le quota d’une région spécifique. Voir [limite d’abonnements Azure, quotas et les contraintes](../azure-subscription-service-limits.md). Pour augmenter le quota, [Ouvrez une demande de support client en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sans frais.

*   **Microsoft Office licence** - si vous déployez des nœuds de calcul à l’aide d’une image de machine virtuelle de marché HPC Pack avec Microsoft Excel, une version d’évaluation de 30 jours de Microsoft Excel Professional Plus 2013 est installé. Après la période d’évaluation, vous devez fournir une licence valide de Microsoft Office pour activer Excel pour continuer à exécuter les charges de travail. Plus loin dans cet article, reportez-vous à la section [activation d’Excel](#excel-activation) . 


## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Étape 1. Configurer un cluster HPC Pack dans Azure

Nous afficher deux options pour configurer le cluster : tout d’abord, à l’aide d’un modèle d’Azure quickstart et le portail Azure ; et la seconde, à l’aide d’un script de déploiement Azure PowerShell.


### <a name="option-1-use-a-quickstart-template"></a>Option 1. Utiliser un modèle de démarrage rapide
Un modèle quickstart Azure permet de déployer rapidement et facilement un cluster HPC Pack dans Azure portal. Lorsque vous ouvrez le modèle dans le portail, vous obtenez une interface utilisateur simple, dans lequel vous entrez les paramètres pour votre cluster. Voici les étapes. 

>[AZURE.TIP]Si vous le souhaitez, utiliser un [modèle d’Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) crée un cluster similaire, en particulier pour les charges de travail Excel. La procédure diffère légèrement de la suivante.

1.  Visitez la [page de modèle créer un Cluster HPC sur GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Si vous le souhaitez, passez en revue les informations sur le modèle et le code source.

2.  Cliquez sur **déployer vers Azure** pour démarrer un déploiement avec le modèle dans le portail Azure.

    ![Déployer le modèle sur Azure][github]

3.  Dans le portail, procédez comme suit pour entrer les paramètres pour le modèle de cluster HPC.

    une barre d’outils. Dans la page **paramètres** , entrez ou modifiez les valeurs pour les paramètres de modèle. (Cliquez sur l’icône en regard de chaque paramètre d’informations d’aide.) Exemples de valeurs sont affichées dans l’écran suivante. Cet exemple crée un cluster nommé *hpc01* dans le domaine *hpc.local* consistant en un nœud principal et les nœuds de calcul 2. Les nœuds de calcul sont créés à partir d’une image de machine virtuelle de HPC Pack qui inclut Microsoft Excel.

    ![Entrez les paramètres][parameters]

    >[AZURE.NOTE]Le nœud de tête VM est créé automatiquement à partir de [dernière image de la Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de HPC Pack 2012 R2 de Windows Server 2012 R2. Actuellement, l’image est basé sur le HPC Pack 2012 R2 mise à jour 3.
    >
    >Machines virtuelles de nœud de calcul sont créées à partir de la dernière image de la famille de nœud de calcul sélectionné. Sélectionnez l’option **ComputeNodeWithExcel** de l’image de nœud de calcul HPC Pack plus récente qui inclut une version d’évaluation de Microsoft Excel Professional Plus 2013. Pour déployer un cluster pour les sessions SOA générales ou pour délester les FDU de Excel, cliquez sur l’option **ComputeNode** (sans Excel installé).

    b. Choisissez l’abonnement.

    c. Créer un groupe de ressources pour le cluster, par exemple *hpc01RG*.

    d. Choisissez un emplacement pour le groupe de ressources, par exemple US Central.

    e. Dans la page **conditions juridiques** , passez en revue les termes du contrat. Si vous acceptez, cliquez sur **achat**. Ensuite, lorsque vous avez terminé de définir les valeurs pour le modèle, cliquez sur **créer**.

4.  Lorsque le déploiement est terminé (il prend généralement environ 30 minutes), exporter le fichier de certificat de cluster à partir du nœud de tête du cluster. Dans une étape ultérieure, vous importez ce certificat public sur l’ordinateur client pour fournir l’authentification côté serveur pour la liaison de HTTP sécurisé.

    une barre d’outils. Se connecter au nœud principal par bureau à distance à partir du portail Azure.

     ![Se connecter au nœud principal][connect]

    b. Utilisez les procédures standard dans le Gestionnaire de certificat pour exporter le certificat de nœud de tête (situé sous certificat : \LocalMachine\My) sans la clé privée. Dans cet exemple, vous devez exporter *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Exporter le certificat][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Option 2. Utiliser le script de déploiement de HPC Pack IaaS

Le script de déploiement des HPC Pack IaaS fournit une autre méthode très polyvalente pour déployer un cluster HPC Pack. Il crée un cluster dans le modèle de déploiement classique, tandis que le modèle utilise le modèle de déploiement du Gestionnaire de ressources Azure. En outre, le script est compatible avec un abonnement dans Azure Global ou Azure Chine service.

**Autres conditions préalables**

* **PowerShell Azure** - [installer et configurer Azure PowerShell](../powershell-install-configure.md) (version 0.8.10 ou version ultérieure) sur votre ordinateur client.

* **Script de déploiement des HPC Pack IaaS** - télécharger et décompresser la dernière version du script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Vérifiez la version du script en exécutant `New-HPCIaaSCluster.ps1 –Version`. Cet article est basé sur la version 4.5.0 ou ultérieure du script.

**Créer le fichier de configuration**

 Le script de déploiement des HPC Pack IaaS utilise un fichier de configuration XML comme entrée qui décrit l’infrastructure du cluster HPC. Pour déployer un cluster constitué d’un nœud de tête et de 18 nœuds de calcul créés à partir de l’image de nœud de calcul qui inclut Microsoft Excel, remplacez les valeurs pour votre environnement dans le fichier de configuration d’exemple suivant. Pour plus d’informations sur le fichier de configuration, consultez le fichier Manual.rtf dans le dossier de scripts et de [créer un cluster à haute performance avec le script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Remarques concernant le fichier de configuration**

* Le **VMName** du nœud principal **doit** être le même que le **ServiceName**ou travaux SOA ne s’exécute pas.

* Assurez-vous que vous spécifiez **EnableWebPortal** pour que le certificat du nœud principal est généré et exporté.

* Le fichier Spécifie un script PowerShell de configuration postérieur à PostConfig.ps1 qui s’exécute sur le nœud de tête. L’exemple de script suivant configure la chaîne de connexion de stockage Azure, supprime le rôle de nœud de calcul à partir du nœud de tête et affiche tous les nœuds en ligne lorsqu’ils sont déployés. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Exécutez le script**

1.  Ouvrez la console PowerShell sur l’ordinateur client en tant qu’administrateur.

2.  Changer de répertoire pour le dossier de scripts (E:\IaaSClusterScript dans cet exemple).

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  Pour déployer le cluster HPC Pack, exécutez la commande suivante. Cet exemple suppose que le fichier de configuration se trouve dans E:\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

Le script de déploiement des HPC Pack s’exécute pendant un certain temps. Une action le script consiste à exporter et télécharger le certificat de cluster et les enregistrer dans le dossier Documents de l’utilisateur en cours sur l’ordinateur client. Le script génère un message semblable au suivant. Dans une étape suivante, vous importez le certificat dans le magasin de certificats approprié.    
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Étape 2. Déchargement des classeurs Excel et exécuter des FDU à partir d’un client sur site

### <a name="excel-activation"></a>Activation d’Excel

Lors de l’utilisation de l’image ComputeNodeWithExcel VM pour les charges de travail, vous devez fournir une clé de licence Microsoft Office valide pour activer Excel sur les nœuds de calcul. Dans le cas contraire, la version d’évaluation de Microsoft Excel expire après 30 jours, et classeurs Excel en cours d’exécution va échouer avec COMException (0x800AC472). 

Vous pouvez réarmer Excel pour un autre 30 jours de la période d’évaluation : ouvrez une session sur le nœud de tête et clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` sur Excel tous les nœuds par le biais de HPC Cluster Manager. Vous pouvez réarmer un maximum de deux fois. Après cela, vous devez fournir une clé de licence Office valide.

Office Professionnel Plus, 2013 installés sur l’image de l’ordinateur virtuel est une édition de volume avec un générique Volume License Key (GVLK). Vous pouvez l’activer via le Service de gestion de clés (KMS) / Active Directory-Based d’Activation (AD-BA) ou clé d’Activation Multiple (MAK). 

    * Pour utiliser le service Gestionnaire de clés/AD-BA, utiliser un serveur KMS existant ou définir un nouveau à l’aide du Pack de licences de Volume de Microsoft Office 2013. (Si vous le souhaitez, configurer le serveur sur le nœud principal.) Activez ensuite la clé d’hôte KMS par Internet ou par téléphone. Clusrun puis `ospp.vbs` pour définir le port et le serveur KMS et activer Office sur tous les nœuds de calcul Excel. 
    
    * Pour utiliser la clé d’activation multiple, premier clusrun `ospp.vbs` à saisir la clé, puis activer tous les nœuds par Internet ou par téléphone de calcul de Excel. 

>[AZURE.NOTE]Clés de produit de vente au détail pour 2013 d’Office Professionnel Plus ne peut pas être utilisés avec cette image de machine virtuelle. Si vous avez des clés valides et le support d’installation de pour les éditions de Microsoft Office ou Excel que cette édition de volume d’Office Professionnel Plus 2013, vous pouvez les utiliser à la place. Tout d’abord désinstaller cette version de volume et d’installer l’édition dont vous disposez. Le nœud de calcul réinstallez Excel peut être capturé sous la forme d’une image de machine virtuelle personnalisée à utiliser dans un déploiement à grande échelle.

### <a name="offload-excel-workbooks"></a>Déchargement des classeurs Excel

Procédez comme suit pour décharger un classeur Excel afin qu’il s’exécute sur le cluster HPC Pack dans Azure. Pour ce faire, vous devez disposer de Excel 2010 ou 2013 déjà installé sur l’ordinateur client.

1. Utilisez une des options dans l’étape 1 pour déployer un cluster HPC Pack avec Excel image de nœud de calcul. Obtenir le fichier de certificat (.cer) du cluster et le nom d’utilisateur du cluster et le mot de passe.

2. Sur l’ordinateur client, importez le certificat de cluster sous certificat : \CurrentUser\Root.

3. Assurez-vous qu’elle est installée. Créer un fichier Excel.exe.config avec le contenu suivant dans le même dossier que Excel.exe sur l’ordinateur client. Cette étape garantit que le complément COM de HPC Pack 2012 R2 Excel se charge avec succès.

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.  Configurez le client pour soumettre les tâches au cluster HPC Pack. Une option consiste à télécharger complète [HPC Pack 2012 R2 mise à jour 3 installation](http://www.microsoft.com/download/details.aspx?id=49922) et installer le client HPC Pack. Vous pouvez également télécharger et installez les [utilitaires client de Microsoft HPC Pack 2012 R2 mise à jour 3](https://www.microsoft.com/download/details.aspx?id=49923) et approprié Visual C++ 2010 redistribuable pour votre ordinateur ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.  Dans cet exemple, nous utilisons un exemple de classeur Excel nommé ConvertiblePricing_Complete.xlsb. Vous pouvez le télécharger [ici](https://www.microsoft.com/en-us/download/details.aspx?id=2939).

6.  Copier le classeur Excel dans un dossier de travail par exemple D:\Excel\Run.

7.  Ouvrez le classeur Excel. Sur le ruban de la **développer** , cliquez sur **Compléments COM** et confirmer que la HPC Pack Excel macro complémentaire est chargée avec succès.

    ![Macro complémentaire Excel pour HPC Pack][addin]

8.  Modifier la macro VBA HPCControlMacros dans Excel en modifiant les lignes commentées comme illustré dans le script suivant. Substituez les valeurs appropriées pour votre environnement.

    ![Macro Excel pour HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  Copier le classeur Excel dans un répertoire de téléchargement par exemple D:\Excel\Upload. Ce répertoire est spécifié dans la constante HPC_DependsFiles dans la macro VBA.

10. Pour exécuter le classeur sur le cluster dans Azure, cliquez sur le bouton de **Cluster** dans la feuille de calcul.

### <a name="run-excel-udfs"></a>Exécuter Excel UDF

Pour exécuter Excel UDF, suivez les étapes précédentes de 1 à 3 pour configurer l’ordinateur client. Pour Excel UDF, vous n’avez pas besoin d’avoir l’application Excel installée sur les nœuds de calcul. Ainsi, lors de la création de votre cluster de nœuds de calcul, vous pouvez choisir une normale compute image de nœud au lieu de l’image de nœud de calcul avec Excel.

>[AZURE.NOTE] Il existe une limite de 34 caractères dans le Excel 2010 et de la boîte de dialogue de connecteur de cluster 2013. Cette boîte de dialogue vous permet de spécifier le cluster qui exécute les FDU. Si le nom de cluster complète est plus long (par exemple, hpcexcelhn01.southeastasia.cloudapp.azure.com), il ne tient pas dans la boîte de dialogue. La solution consiste à définir une variable à l’échelle de l’ordinateur, tels que *CCP_IAASHN* avec la valeur du nom du cluster long. Ensuite, entrez *% CCP_IAASHN %* dans la boîte de dialogue sous le nom de nœud de tête du cluster. 

Une fois que le cluster est déployé avec succès, continuez avec les étapes suivantes pour exécuter un exemple intégré Excel UDF. Pour les FDU Excel personnalisées, consultez ces [ressources](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) pour générer les XLL et de les déployer sur le cluster IaaS.

1.  Ouvrez un nouveau classeur Excel. Sur le ruban de la **développer** , cliquez sur **Add-Ins**. Puis, dans la boîte de dialogue, cliquez sur **Parcourir**, naviguez jusqu’au dossier %CCP_HOME%Bin\XLL32 et sélectionnez l’exemple ClusterUDF32.xll. Si le ClusterUDF32 n’existe pas sur l’ordinateur client, copiez-le à partir du dossier %CCP_HOME%Bin\XLL32 sur le nœud principal.

    ![Sélectionnez le fichier UDF][udf]

2.  Cliquez sur **le fichier** > **Options** > **avancée**. Sous **formules**, activez **Autoriser définis par l’utilisateur des fonctions XLL à l’exécution d’un cluster de calcul**. Cliquez sur **Options** , puis entrez le nom complet dans la zone **nom de nœud principal de Cluster**. (Comme indiqué précédemment cette zone d’entrée est limitée à 34 caractères, un nom de cluster long peut ne pas correspond. Vous pouvez utiliser une variable à l’échelle de l’ordinateur ici pour un nom de cluster longue.)

    ![Configurer la FDU][options]

3.  Pour exécuter le calcul UDF sur le cluster, cliquez sur la cellule contenant la valeur =XllGetComputerNameC() et appuyez sur ENTRÉE. La fonction récupère simplement le nom du nœud de calcul sur lequel s’exécute le fichier UDF. Pour la première exécution, une boîte de dialogue informations d’identification vous demande le nom d’utilisateur et le mot de passe pour se connecter au cluster IaaS.

    ![Exécutez l’UDF][run]

    Lorsqu’il y a plusieurs cellules à calculer, appuyez sur Alt-Maj-Ctrl + F9 pour exécuter le calcul sur toutes les cellules.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Étape 3. Exécution d’une charge de travail SOA à partir d’un client sur site

Pour exécuter des applications SOA générales sur le cluster HPC Pack IaaS, utilisez d’abord une des méthodes à l’étape 1 pour le déploiement du cluster. Spécifiez un générique image de nœud de calcul dans ce cas, étant donné que vous n’avez pas besoin d’Excel sur les nœuds de calcul. Ensuite, procédez comme suit.

1. Après avoir récupéré le certificat du cluster, vous devez l’importer sur l’ordinateur client sous certificat : \CurrentUser\Root.

2. Installer le [HPC Pack 2012 R2 mise à jour 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) et les [utilitaires client de Microsoft HPC Pack 2012 R2 mise à jour 3](https://www.microsoft.com/download/details.aspx?id=49923). Ces outils vous permettent de développer et d’exécuter des applications clientes SOA.

3. Téléchargez [l’exemple de code](https://www.microsoft.com/download/details.aspx?id=41633)de HelloWorldR2. Ouvrez la HelloWorldR2.sln dans Visual Studio 2010 ou 2012.

4. Créez d’abord le projet EchoService. Ensuite, déployez le service au cluster IaaS de la même manière que vous déployez sur un cluster sur site. Pour obtenir la procédure détaillée, consultez le fichier Readme.doc dans HelloWordR2. Modifier et générer l’HellWorldR2 et autres projets, comme décrit dans la section suivante pour générer les applications SOA clientes qui s’exécutent sur un cluster Azure IaaS.

### <a name="use-http-binding-with-azure-storage-queue"></a>Utiliser une liaison Http avec la file d’attente de stockage Azure

Pour utiliser une liaison Http avec une file d’attente de stockage Azure, apporter quelques modifications à l’exemple de code.

* Mettre à jour le nom du cluster.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Si vous le souhaitez, utiliser la valeur par défaut TransportScheme dans SessionStartInfo ou explicitement définie sur Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Utiliser la liaison par défaut pour le BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Ou définissez explicitement à l’aide de la liaison basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Si vous le souhaitez, définir l’indicateur UseAzureQueue sur true dans SessionStartInfo. Si la valeur non, elle est définie true par défaut lorsque le nom du cluster a de suffixes de domaine Azure et le TransportScheme est Http.

    ```
    info.UseAzureQueue = true;
```

###<a name="use-http-binding-without-azure-storage-queue"></a>Utiliser la liaison Http sans file d’attente de stockage Azure

Pour utiliser une liaison Http sans une file d’attente de stockage Azure, vous devez définir explicitement l’indicateur UseAzureQueue sur false dans la SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Utiliser la liaison de NetTcp

Pour utiliser une liaison NetTcp, la configuration est similaire à la connexion à un cluster sur site. Vous devez ouvrir plusieurs points de terminaison sur le nœud principal de machine virtuelle. Si vous avez utilisé le script de déploiement des HPC Pack IaaS pour créer le cluster, par exemple, définissez les points de terminaison dans le portail classique Azure comme suit.


1. Arrêter la machine virtuelle.

2. Ajoutez les ports TCP 9090, 9087, 9091, 9094 de la Session, Broker, Broker de travail et les services de données, respectivement

    ![Configurer des points de terminaison][endpoint]

3. Démarrer la machine virtuelle.

L’application cliente SOA ne nécessite aucune modification, à l’exception de la modification du nom de tête pour le nom complet du cluster IaaS.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [ces ressources](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) pour plus d’informations sur l’exécution des charges de travail Excel avec le HPC Pack.

* Pour plus d’informations sur le déploiement et la gestion des services SOA avec le HPC Pack, reportez-vous à la section [Gestion de Services SOA dans Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) .

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png
