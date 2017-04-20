<properties
   pageTitle="Créer des clusters de HDInsight avec le magasin de LAC de données Azure à l’aide de PowerShell | Azure"
   description="Utilisez PowerShell d’Azure pour créer et utiliser des clusters de HDInsight avec le lac de données Azure"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-powershell"></a>Créer un cluster HDInsight avec magasin lac de données à l’aide de PowerShell d’Azure

> [AZURE.SELECTOR]
- [À l’aide du portail](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Utilisation de PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
- [À l’aide du Gestionnaire de ressources](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Découvrez comment utiliser Azure PowerShell pour configurer un cluster HDInsight (Hadoop, HBase ou tempête) disposant d’un accès au magasin de LAC de données Azure. Certaines considérations importantes pour cette version :

* **D’allumage (Linux), les clusters et clusters Hadoop/Storm (Windows et Linux)**, le magasin de LAC de données utilisable uniquement sous la forme d’un compte de stockage supplémentaire. Le compte de stockage par défaut pour les clusters de ce type sera toujours Azure stockage BLOB (WASB).

* **Les clusters pour HBase (Windows et Linux)**, le magasin lac de données peut être utilisé comme un espace de stockage supplémentaire ou de stockage par défaut.

> [AZURE.NOTE] Certains points importants à noter. 
> 
> * Option permettant de créer des clusters de HDInsight disposant d’un accès au magasin de données lac n’est disponible que pour les HDInsight les versions 3.2 et 3.4 (pour les clusters Hadoop, HBase et Storm sur Windows et Linux). Pour les clusters d’allumage sous Linux, cette option est uniquement disponible sur les clusters de HDInsight 3.4.
>
> * Comme mentionné ci-dessus, banque de données lac est disponible sous la forme d’espace de stockage supplémentaire pour les autres types de cluster (Hadoop, étincelle, tempête) et de stockage par défaut pour certains types de cluster (HBase). À l’aide du magasin de données lac comme un compte de stockage supplémentaire n’affecte pas les performances ou la capacité de lecture/écriture pour le stockage du cluster. Dans un scénario où la banque de données lac est utilisé comme espace de stockage supplémentaire, les fichiers liés au cluster (par exemple, les journaux, etc.) sont écrits dans le stockage par défaut (BLOB Azure), tandis que les données que vous souhaitez traiter peuvent être stockées dans un compte de banque de données lac.


Dans cet article, nous mettre en service un cluster Hadoop avec le magasin de données lac comme espace de stockage supplémentaire.

Configuration à utiliser avec le magasin de données lac HDInsight à l’aide de PowerShell implique les étapes suivantes :

* Créer un magasin de LAC de données Azure
* Configurer l’authentification basée sur les rôles accès au magasin de données lac
* Créer un cluster de HDInsight avec l’authentification au magasin de données lac
* Exécuter une tâche de test sur le cluster

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Azure PowerShell 1.0 ou supérieure**. Voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

- **Kit de développement logiciel de Windows**. Vous pouvez l’installer à partir [d’ici](https://dev.windows.com/en-us/downloads). Vous permet de créer un certificat de sécurité.

- **Azure Principal du Service Active Directory**. Étapes de ce tutoriel fournissent des instructions sur la création d’une entité de sécurité de service dans Active Directory Azure. Toutefois, vous devez être un administrateur AD Azure pour être en mesure de créer une entité de sécurité du service. Si vous êtes un administrateur AD Azure, vous pouvez ignorer ces conditions préalables et poursuivre le didacticiel.
    
    **Si vous n’êtes pas un administrateur AD Azure**, vous ne serez pas en mesure d’effectuer les étapes nécessaires à la création d’une entité de sécurité du service. Dans ce cas, votre administrateur AD Azure devez d’abord créer une entité de service avant de pouvoir créer un cluster de HDInsight avec le magasin de données lac. En outre, l’entité du service doit être créée à l’aide d’un certificat, comme indiqué à la [Création d’un service principal avec certificat](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate). 


## <a name="create-an-azure-data-lake-store"></a>Créer un magasin de LAC de données Azure

Suivez ces étapes pour créer un magasin de LAC de données.

1. À partir de votre bureau, ouvrir une nouvelle fenêtre PowerShell d’Azure et entrez l’extrait de code suivant. Lorsque vous êtes invité à ouvrir une session, vérifiez que vous vous connectez sous la forme d’un abonnement admininistrators/du propriétaire de la :

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    >[AZURE.NOTE] Si vous recevez un message d’erreur semblable à `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` lors de l’enregistrement du fournisseur de ressources de banque de données lac, il est possible que votre subsrcription n’est pas autorisés pour le lac Azure Data Store. Assurez-vous que vous activez votre abonnement Azure pour la présentation publique de magasin de données lac en suivant ces [instructions](data-lake-store-get-started-portal.md#signup).

3. Un compte Azure données lac banque est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Créer un groupe de ressources Azure] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")

2. Créez un compte de banque de LAC de données Azure. Le nom de compte que vous spécifiez ne doit contenir que des lettres minuscules et chiffres.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Création d’un compte Azure données lac] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Création d’un compte Azure données lac")

3. Vérifiez que le compte est créé avec succès.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    La sortie de cette doit être **True**.

4. Télécharger des exemples de données dans Azure données lac. Nous allons utiliser ceci plus loin dans cet article pour vérifier que les données sont accessibles à partir d’un cluster de HDInsight. Si vous recherchez des exemples de données à télécharger, vous pouvez obtenir le dossier de **Données d’Ambulance** à partir du [Référentiel Git lac Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).


        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurer l’authentification basée sur les rôles accès au magasin de données lac

Chaque abonnement Azure est associé à un Azure d’Active Directory. Les utilisateurs et services qui accèdent aux ressources de l’abonnement à l’aide du portail classique d’Azure ou l’API du Gestionnaire de ressources Azure doivent tout d’abord s’authentifier avec Azure Active Directory. L’accès aux services et Azure abonnements en leur affectant le rôle approprié sur une ressource d’Azure.  Pour les services, une entité de sécurité du service identifie le service dans le répertoire actif Azure (DAS). Cette section illustre l’octroi d’un service d’application, comme HDInsight, accès à une ressource Azure (le compte Azure données lac magasin créé précédemment) en créant un principal de service pour l’application et en assignant des rôles pour que via PowerShell d’Azure.

Pour configurer l’authentification d’Active Directory du lac de données Azure, vous devez effectuer les tâches suivantes.

* Créer un certificat auto-signé
* Créer une application dans Azure Active Directory et un Service Principal.

### <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

Vérifiez que vous avez le [Kit de développement logiciel Windows](https://dev.windows.com/en-us/downloads) installé avant de poursuivre les étapes de cette section. Vous devez également créer un répertoire, tel que **C:\mycertdir**, où sera créé le certificat.

1. Dans la fenêtre PowerShell, naviguez jusqu'à l’emplacement où vous avez installé le Kit de développement logiciel Windows (en général, `C:\Program Files (x86)\Windows Kits\10\bin\x86` et utilisez le [MakeCert] [ makecert] utilitaire permettant de créer un certificat auto-signé et une clé privée. Utilisez les commandes suivantes.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        $startDate = (Get-Date).ToString('MM/dd/yyyy')
        $endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

    Vous devrez entrer le mot de passe de clé privée. Une fois que la commande s’exécute correctement, vous devez voir un **CertFile.cer** et un **mykey.pvk** dans le répertoire de certificat que vous avez spécifié.

4. Utilisez la [Pvk2Pfx] [ pvk2pfx] utilitaire pour convertir les fichiers .pvk et .cer MakeCert créé vers un fichier .pfx. Exécutez la commande suivante.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Lorsque le système demande le mot de passe de clé privée spécifié antérieurement. La valeur que vous spécifiez pour le paramètre de la **commande d’achat** est le mot de passe qui est associé au fichier .pfx. Une fois la commande terminée, vous devez également voir un CertFile.pfx dans le répertoire de certificat que vous avez spécifié.

###  <a name="create-an-azure-active-directory-and-a-service-principal"></a>Créer un Azure d’Active Directory et une entité de sécurité du service

Dans cette section, vous effectuez les étapes pour créer un service principal d’une application Azure Active Directory, attribuer un rôle à l’entité de sécurité du service et s’authentifier en tant que l’entité de service en fournissant un certificat. Exécutez les commandes suivantes pour créer une application dans Azure Active Directory.

1. Coller les applets de commande suivantes dans la fenêtre de console PowerShell. Assurez-vous que la valeur que vous spécifiez pour la propriété **DisplayName -** est unique. En outre, les valeurs de **- Page d’accueil** et **IdentiferUris -** sont des valeurs d’espace réservé et ne sont pas vérifiés.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
                    -DisplayName "HDIADL" `
                    -HomePage "https://contoso.com" `
                    -IdentifierUris "https://mycontoso.com" `
                    -KeyValue $credential  `
                    -KeyType "AsymmetricX509Cert"  `
                    -KeyUsage "Verify"  `
                    -StartDate $startDate  `
                    -EndDate $endDate

        $applicationId = $application.ApplicationId

2. Créer une entité de service à l’aide de l’ID de l’application.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id

3. Accorder l’accès principal de service pour le magasin de données lac fichier/dossier auxquels vous accéderez à partir du cluster HDInsight. L’extrait de code ci-dessous fournit l’accès à la racine du compte banque de données lac.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

    À l’invite, saisissez **Y** pour confirmer.

## <a name="create-an-hdinsight-cluster-with-authentication-to-data-lake-store"></a>Créer un cluster de HDInsight avec l’authentification au magasin de données lac

Dans cette section, nous créons un cluster HDInsight Hadoop. Pour cette version, le cluster de HDInsight et de la banque de LAC de données doivent être dans le même emplacement (Extrême-Orient US 2).

1. Démarrer avec la récupération de l’ID client d’abonnement. Vous devez que plus tard.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Pour cette version, pour un cluster Hadoop, données lac magasin peut uniquement être utilisé comme un espace de stockage supplémentaire pour le cluster. Le stockage par défaut sera toujours les blobs de stockage Azure (WASB). Par conséquent, nous allons tout d’abord créer le compte de stockage et les conteneurs de stockage requis pour le cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext

3. Créez le cluster de HDInsight. Utiliser les applets de commande suivantes.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $rdpCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Après la fin de l’applet de commande, vous devez voir une sortie comme suit :

        Name                      : hdiadlcluster
        Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
                                    crosoft.HDInsight/clusters/hdiadlcluster
        Location                  : East US 2
        ClusterVersion            : 3.2.7.707
        OperatingSystemType       : Windows
        ClusterState              : Running
        ClusterType               : Hadoop
        CoresUsed                 : 16
        HttpEndpoint              : hdiadlcluster.azurehdinsight.net
        Error                     :
        DefaultStorageAccount     :
        DefaultStorageContainer   :
        ResourceGroup             : hdiadlgroup
        AdditionalStorageAccounts :

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Exécuter des tâches d’essai sur le cluster de HDInsight à utiliser le magasin de LAC de données

Après avoir configuré un cluster HDInsight, vous pouvez exécuter des tâches d’essai sur le cluster pour vérifier que le cluster HDInsight peut accéder aux données lac magasin. Pour ce faire, nous allons exécuter un travail de ruche d’exemple qui crée une table à l’aide de l’exemple de données que vous avez téléchargé précédemment à votre magasin de LAC de données.

### <a name="for-a-linux-cluster"></a>Pour un cluster Linux

Dans cette section, vous allez SSH dans le cluster et exécuter l’exemple de requête ruche. Windows ne fournit pas un client SSH intégré. Nous vous recommandons d’à l’aide de **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Une fois connecté, démarrer l’interface CLI de la ruche à l’aide de la commande suivante :

        hive

2. À l’aide de l’interface CLI, entrez les instructions suivantes pour créer une nouvelle table nommée **véhicules** en utilisant les exemples de données du magasin de données lac :

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Vous devriez voir une sortie semblable à la suivante :

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


### <a name="for-a-windows-cluster"></a>Pour un cluster de Windows

Utiliser les applets de commande suivantes pour exécuter la requête de la ruche. Dans cette requête, nous créer une table à partir des données du magasin de données LAC et puis exécutez une requête select sur la table.

    $queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

    $hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

    Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Cela aura la sortie suivante. **ExitValue** de 0 dans le résultat suggère que la tâche s’est terminée correctement.

    Cluster         : hdiadlcluster.
    HttpEndpoint    : hdiadlcluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1445386885331_0012
    ParentId        :
    PercentComplete :
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done

Récupérer la sortie de la tâche à l’aide de l’applet de commande suivante :

    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

La sortie de projet semblable au suivant :

    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## <a name="access-data-lake-store-using-hdfs-commands"></a>Magasin de LAC de données Access à l’aide des commandes de très

Une fois que vous avez configuré le cluster HDInsight pour utiliser le magasin de données lac, vous pouvez utiliser les commandes de shell très pour accéder au magasin.

### <a name="for-a-linux-cluster"></a>Pour un cluster Linux

Dans cette section vous seront SSH dans le cluster et exécutez les commandes très. Windows ne fournit pas un client SSH intégré. Nous vous recommandons d’à l’aide de **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, reportez-vous à la section [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Une fois connecté, utilisez la commande de système de fichiers très suivante pour répertorier les fichiers du magasin de données lac.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Cette opération doit répertorier le fichier que vous avez téléchargé précédemment à la banque de LAC de données.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Vous pouvez également utiliser le `hdfs dfs -put` commande pour télécharger des fichiers vers la banque de LAC de données, puis utilisez `hdfs dfs -ls` pour vérifier si les fichiers ont été correctement téléchargées.


### <a name="for-a-windows-cluster"></a>Pour un cluster de Windows

1. Ouvrez une session sur le nouveau [Portail Azure](https://portal.azure.com).

2. Cliquez sur **Parcourir**et cliquez sur **HDInsight les clusters**, puis cliquez sur le cluster de HDInsight que vous avez créé.

3. Dans la carte de cluster, cliquez sur **Bureau à distance**et puis dans la lame du **Bureau à distance** , cliquez sur **se connecter**.

    ![À distance dans le cluster HDI] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Créer un groupe de ressources Azure")

    Lorsque vous y êtes invité, entrez les informations d’identification que vous avez fournie pour l’utilisateur de bureau à distance.

4. Dans la session à distance, démarrer Windows PowerShell et les commandes de système de fichiers très permet de répertorier les fichiers dans le magasin de LAC de données Azure.

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    Cette opération doit répertorier le fichier que vous avez téléchargé précédemment à la banque de LAC de données.

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

    Vous pouvez également utiliser le `hdfs dfs -put` commande pour télécharger des fichiers vers la banque de LAC de données, puis utilisez `hdfs dfs -ls` pour vérifier si les fichiers ont été correctement téléchargées.

## <a name="see-also"></a>Voir aussi

* [Portail : Créer un cluster de HDInsight pour utiliser le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
