<properties
    pageTitle="Téléchargement des données pour les projets de Hadoop de HDInsight | Microsoft Azure"
    description="Découvrez comment télécharger et accéder aux données pour les projets de Hadoop de HDInsight à l’aide de la CLI d’Azure, Explorateur de stockage Azure, Azure PowerShell, ligne de commande Hadoop ou Sqoop."
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>



#<a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Téléchargement des données pour les projets de Hadoop de HDInsight

HDInsight Azure fournit un système complet de fichier Hadoop distribué (très) sur le stockage des objets Blob Azure. Il est conçu comme une extension très pour offrir une expérience transparente aux clients. Il permet à l’ensemble des composants de l’écosystème Hadoop d’agir directement sur les données qu’il gère. Stockage des objets Blob Azure et très sont des systèmes de fichiers distincts qui sont optimisées pour le stockage des données et des calculs sur ces données. Pour plus d’informations sur les avantages de l’utilisation de stockage des objets Blob Azure, consultez [stockage Blob Azure d’utilisation avec HDInsight][hdinsight-storage].

**Conditions préalables**

Notez les conditions suivantes avant de commencer :

* Un cluster Azure HDInsight. Pour obtenir des instructions, reportez-vous à la section [mise en route avec Azure HDInsight] [ hdinsight-get-started] ou [clusters de disposition HDInsight][hdinsight-provision].

##<a name="why-blob-storage"></a>Stockage de blob pourquoi ?

Azure HDInsight clusters sont généralement déployés pour exécuter des travaux de MapReduce, et les clusters sont supprimés à la fin de ces travaux. Conserver les données dans le très clusters une fois les calculs terminés serait un moyen coûteux pour stocker ces données. Stockage de Blob Azure est une capacité, hautement disponible et évolutive, une option de stockage économiques et partageable pour les données qui doit être traité à l’aide de HDInsight. Stockage de données dans un objet blob permet les clusters HDInsight qui sont utilisés pour le calcul doit être publié en toute sécurité sans perdre de données.

###<a name="directories"></a>Répertoires

Les conteneurs de stockage Blob Azure stockent les données sous forme de paires clé/valeur, et qu’il n’y a aucune hiérarchie de répertoire. Toutefois le caractère « / » peut servir dans le nom de la clé pour le faire apparaître comme si un fichier est stocké dans une structure de répertoire. HDInsight voit ces comme s’ils étaient des répertoires réels.

Par exemple, clé d’un blob peut être *input/log1.txt*. Aucun répertoire « input » réel n’existe, mais en raison de la présence du caractère « / » dans le nom de clé, il a l’apparence d’un chemin d’accès du fichier.

Pour cette raison, si vous utilisez des outils de l’Explorateur d’Azure, vous pouvez remarquer certains fichiers de 0 octet. Ces fichiers servent deux objectifs :

- S’il existe des dossiers vides, ils marquent l’existence du dossier. Stockage de Blob Azure est assez intelligent pour savoir que si un blob appelé foo/barre existe, il existe un dossier appelé **foo**. Mais le seul moyen d’indiquer un dossier vide appelé **foo** est par l’absence de ce fichier spécial de 0 octet.

- Ils contiennent des métadonnées spéciale qui sont requis par le système de fichiers Hadoop, notamment les autorisations et les propriétaires des dossiers.

##<a name="command-line-utilities"></a>Utilitaires de ligne de commande

Microsoft fournit les utilitaires suivants pour travailler avec le stockage des objets Blob Azure :

| Outil | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Interface de ligne de commande Azure][azurecli] | ✔ | ✔ | ✔ |
| [PowerShell Azure][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Commande d’Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Tandis que la CLI d’Azure, Azure PowerShell et AzCopy, peuvent toutes être utilisé à partir de l’extérieur Azure, le Hadoop commande n’est disponible que sur le cluster HDInsight et autorise uniquement le chargement des données du système de fichiers local dans le stockage des objets Blob Azure.

###<a id="xplatcli"></a>CLI Azure

L’infrastructure du langage commun Azure est un outil de multiplates-formes qui vous permet de gérer les services Azure. Utilisez les étapes suivantes pour télécharger des données pour le stockage des objets Blob Azure :

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Installer et configurer l’infrastructure du langage commun Azure pour Mac, Linux et Windows](../xplat-cli-install.md).

2. Ouvrez une invite de commande, bash ou autre shell et utilisez ce qui suit pour s’authentifier sur votre abonnement Azure.

        azure login

    Lorsque vous y êtes invité, entrez le nom d’utilisateur et le mot de passe pour votre abonnement.

3. Entrez la commande suivante pour répertorier les comptes de stockage de votre abonnement :

        azure storage account list

4. Sélectionnez le compte de stockage contenant le blob que vous souhaitez utiliser, puis utilisez la commande suivante pour récupérer la clé pour ce compte :

        azure storage account keys list <storage-account-name>

    Il doit retourner les clés **primaires** et **secondaires** . Copier la valeur de clé **primaire** , car il est utilisé dans les étapes suivantes.

5. Pour récupérer une liste de conteneurs blob dans le compte de stockage, utilisez la commande suivante :

        azure storage container list -a <storage-account-name> -k <primary-key>

6. Utilisez les commandes suivantes pour transférer et télécharger des fichiers à l’objet blob :

    * Pour télécharger un fichier :

            azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

    * Pour télécharger un fichier :

            azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Si vous travaillez toujours avec le même compte de stockage, vous pouvez définir les variables d’environnement au lieu de spécifier le compte et la clé pour chaque commande :
>
> * **AZURE\_stockage\_compte**: le nom de compte de stockage
>
> * **AZURE\_stockage\_accès\_clé**: la clé de compte de stockage

###<a id="powershell"></a>PowerShell Azure

Azure PowerShell est un environnement de script qui vous permet de contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d’informations sur la configuration de votre station de travail pour exécuter PowerShell d’Azure, consultez [installer et configurer Azure PowerShell](../powershell-install-configure.md).

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Pour télécharger un fichier local pour le stockage des objets Blob Azure**

1. Ouvrez la console PowerShell d’Azure, comme indiqué dans [installer et configurer Azure PowerShell](../powershell-install-configure.md).
2. Définissez les valeurs des cinq premières variables dans le script suivant :

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Collez le script dans la console PowerShell d’Azure afin de l’exécuter pour copier le fichier.

Par exemple, scripts PowerShell créés pour travailler avec des HDInsight, voir [Outils de HDInsight](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy est un outil de ligne de commande qui est conçu pour simplifier la tâche de transfert de données vers et à partir d’un compte de stockage Azure. Vous pouvez l’utiliser comme un outil autonome ou incorporer cet outil dans une application existante. [Télécharger AzCopy][azure-azcopy-download].

La syntaxe de AzCopy est :

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Pour plus d’informations, consultez [AzCopy - chargement/téléchargement de fichiers pour les BLOB Azure][azure-azcopy].


###<a id="commandline"></a>Ligne de commande Hadoop

La ligne de commande Hadoop n’est utile pour stocker des données dans le stockage blob lorsque les données sont déjà présentes sur le nœud principal du cluster.

Pour utiliser la commande Hadoop, vous devez vous connecter à la headnode à l’aide d’une des méthodes suivantes :

* **HDInsight de base Windows**: [à l’aide de connexion Bureau à distance](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **Basé sur Linux de HDInsight**: se connecter à l’aide de SSH ([la commande SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) ou [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Une fois connecté, vous pouvez utiliser la syntaxe suivante pour transférer un fichier de stockage.

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

Par exemple,`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Étant donné que le système de fichiers par défaut pour HDInsight est dans le stockage Blob d’Azure, /example/data.txt est en fait dans le stockage des objets Blob Azure. Vous pouvez également consulter le fichier en tant que :

    wasbs:///example/data/data.txt

ou

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Pour une liste des autres commandes d’Hadoop qui fonctionnent avec des fichiers, reportez-vous à la section [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [AZURE.WARNING] Sur les clusters de HBase, la valeur par défaut utilisée lors de l’écriture de données est de 256 Ko de taille de bloc. Cette opération fonctionne correctement lors de l’utilisation de HBase APIs ou autres API utilisant le `hadoop` ou `hdfs dfs` commandes d’écrire plus de ~ 12 Go de données génère une erreur. Consultez la section [exceptions de stockage pour l’écriture sur un objet blob](#storageexception) ci-dessous pour plus d’informations.

##<a name="graphical-clients"></a>Clients de graphiques

Il existe également plusieurs applications qui fournissent une interface graphique permettant de travailler avec le stockage Azure. Voici une liste de quelques-unes de ces applications :

| Client | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Microsoft Visual Studio Tools pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Explorateur de stockage Azure](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Stockage de nuage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Explorer Azure](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) |  | ✔ | ✔ |

###<a name="visual-studio-tools-for-hdinsight"></a>Outils Visual Studio pour HDInsight

Pour plus d’informations, voir [Naviguer dans les ressources liées](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

###<a id="storageexplorer"></a>Explorateur de stockage Azure

*Explorateur de stockage Azure* est un outil très utile permettant d’examiner et de modifier les données dans les objets BLOB. Il s’agit d’un outil gratuit, open source qui peut être téléchargé à partir de [http://storageexplorer.com/](http://storageexplorer.com/). Le code source est disponible à partir de ce lien également.

Avant d’utiliser l’outil, vous devez connaître votre clé de compte et de nom de compte stockage Azure. Pour obtenir des instructions sur l’obtention de ces informations, consultez la « Comment : affichage, de copie et de stockage régénérer accéder aux clés » section de [créer, gérer, ou de supprimer un compte de stockage][azure-create-storage-account].  

1. Exécutez l’Explorateur de stockage Azure. Si c’est la première fois que vous avez exécuté l’Explorateur de stockage, vous serez invité pour les ___nom de compte de stockage__ et de la __clé de compte de stockage__. Si vous avez exécuté avant, utilisez le bouton __Ajouter__ pour ajouter un nouveau nom de compte de stockage et de la clé.

    Entrez le nom et la clé pour le compte de stockage utilisé par votre cluster HDinsight et cliquez sur __Enregistrer et ouvrir__.

    ![HDI. AzureStorageExplorer][image-azure-storage-explorer]

5. Dans la liste des conteneurs à gauche de l’interface, cliquez sur le nom du conteneur qui est associé à votre cluster HDInsight. Par défaut, c’est le nom du cluster HDInsight, mais peut être différent si vous avez entré un nom spécifique lors de la création du cluster.

6. Dans la barre d’outils, sélectionnez l’icône de chargement.

    ![Barre d’outils avec l’icône de téléchargement mis en surbrillance](./media/hdinsight-upload-data/toolbar.png)

7. Spécifier un fichier à télécharger, puis cliquez sur **Ouvrir**. Lorsque vous y êtes invité, sélectionnez __Télécharger__ pour télécharger le fichier à la racine du conteneur de stockage. Si vous souhaitez télécharger le fichier vers un chemin spécifique, entrez le chemin d’accès dans le champ de __Destination__ et sélectionnez __charger__.

    ![Dialogue Téléchargement de fichier](./media/hdinsight-upload-data/fileupload.png)
    
    Une fois le fichier de téléchargement est terminé, vous pouvez l’utiliser à partir de travaux sur le cluster de HDInsight.

##<a name="mount-azure-blob-storage-as-local-drive"></a>Stockage de Blob Azure de monter en tant que lecteur Local

Reportez-vous à la section [stockage Blob Azure de monter un lecteur Local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##<a name="services"></a>Services

###<a name="azure-data-factory"></a>Usine de données Azure

Le service de fabrique de données Azure est un service entièrement géré pour la composition des services de données données, de stockage et de traitement des données de mouvement dans des pipelines de production rationalisée, évolutive et fiable des données.

Azure Data Factory peut servir à déplacer des données dans le stockage des objets Blob Azure, ou pour créer des pipelines de données qui utilisent directement des fonctionnalités HDInsight telles que la ruche et de porcins.

Pour plus d’informations, consultez la [documentation d’Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop est un outil conçu pour transférer des données entre Hadoop et bases de données relationnelles. Vous pouvez l’utiliser pour importer des données à partir d’un système de gestion de base de données relationnelle (SGBDR), tel qu’Oracle dans le système de fichiers distribué de Hadoop (très), SQL Server ou MySQL transformer les données dans Hadoop avec MapReduce ou de la ruche, puis exporter les données dans un SGBDR.

Pour plus d’informations, consultez [Utilisation des Sqoop avec HDInsight][hdinsight-use-sqoop].

##<a name="development-sdks"></a>Kits de développement SDK

Stockage de Blob Azure également accessibles à l’aide d’un kit de développement Azure dans les langages de programmation suivants :

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Pour plus d’informations sur l’installation des kits de développement logiciel Azure, reportez-vous à la section [téléchargements d’Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Résolution des problèmes

### <a id="storageexception"></a>Exception de stockage pour l’écriture de blob

__Symptômes__: lorsque vous utilisez la `hadoop` ou `hdfs dfs` commandes d’écrire des fichiers qui sont d’environ 12 Go ou plus sur un cluster HBase, vous pouvez rencontrer l’erreur suivante : 

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

__Cause__: HBase sur HDInsight clusters par défaut à une taille de bloc de 256 Ko lors de l’écriture dans le stockage Azure. Alors que cela fonctionne pour HBase APIs ou d’autres API, il provoque une erreur lors de l’utilisation du `hadoop` ou `hdfs dfs` des utilitaires de ligne de commande.

__Résolution__: utilisez `fs.azure.write.request.size` pour spécifier une taille de bloc supérieure. Ce faire sur une base individuelle à l’aide de la `-D` paramètre. Voici un exemple d’utilisation de ce paramètre avec la `hadoop` commande :

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

Vous pouvez également augmenter la valeur de `fs.azure.write.request.size` globalement à l’aide de Ambari. La procédure suivante peut être utilisée pour modifier la valeur dans l’interface utilisateur Web de Ambari :

1. Dans votre navigateur, accédez à l’interface utilisateur Web de Ambari pour votre cluster. Il s’agit d’https://CLUSTERNAME.azurehdinsight.net, où __CLUSTERNAME__ est le nom de votre cluster.

    Lorsque vous y êtes invité, entrez le nom d’administrateur et le mot de passe pour le cluster.

2. Dans la partie gauche de l’écran, sélectionnez __très__et puis sélectionnez l’onglet __configurations__ .

3. Dans le champ __filtre__ , saisissez `fs.azure.write.request.size`. Le champ et la valeur actuelle au centre de la page s’affiche.

4. Remplacez la valeur 262144 (256 Ko) à la nouvelle valeur. Par exemple, 4194304 (4 Mo).

![Image de la modification de la valeur par le biais de l’interface utilisateur Web de Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Pour plus d’informations sur l’utilisation de Ambari, reportez-vous à la section [HDInsight de gérer les clusters à l’aide de l’interface utilisateur Web de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez comment extraire des données dans HDInsight, lisez les articles suivants pour savoir comment effectuer une analyse :

* [Mise en route de HDInsight d’Azure][hdinsight-get-started]
* [Soumettre des travaux d’Hadoop par programme][hdinsight-submit-jobs]
* [Utilisez la ruche avec HDInsight][hdinsight-use-hive]
* [Utilisez des porcs avec HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
