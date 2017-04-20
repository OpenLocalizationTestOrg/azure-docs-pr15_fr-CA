<properties
pageTitle="Restreindre l’accès de HDInsight de données à l’aide de Signatures de l’accès partagé"
description="Apprenez à utiliser des Signatures de l’accès partagé pour restreindre l’accès de HDInsight aux données stockées dans les objets BLOB de stockage Azure."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/11/2016"
ms.author="larryfr"/>

#<a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>Signatures de Azure stockage partagés accès permet de restreindre l’accès aux données avec HDInsight

HDInsight utilise le stockage Azure BLOB pour le stockage de données. Alors que HDInsight doivent avoir un accès complet à l’objet blob utilisé comme espace de stockage par défaut pour le cluster, vous pouvez restreindre les autorisations aux données stockées dans d’autres objets BLOB utilisé par le cluster. Par exemple, vous souhaiterez peut-être mettre des données en lecture seule. Pour cela, à l’aide de Signatures de l’accès partagé.

Signatures d’accès partagé (SAS) sont une fonctionnalité de comptes de stockage Azure qui vous permet de limiter l’accès aux données. Par exemple, accès en lecture seule aux données. Dans ce document, vous apprendrez comment utiliser des associations de sécurité pour activer l’accès en lecture et liste uniquement à un conteneur d’objet blob à partir de HDInsight.

##<a name="requirements"></a>Configuration requise

* Un abonnement Azure

* C# ou les Python. Exemple de code C# est fourni sous la forme d’une solution Visual Studio.

    * Visual Studio doit être de version 2013 ou 2015
    
    * Python doit être de version 2.7 ou ultérieure

* Un HDInsight basé sur Linux cluster du ou [Azure PowerShell] [ powershell] -si vous avez un cluster existant fonctionnant sous Linux, vous pouvez utiliser Ambari pour ajouter une Signature à accès partagé pour le cluster. Si ce n’est pas le cas, vous pouvez utiliser PowerShell d’Azure pour créer un nouveau cluster et ajouter une Signature à accès partagé lors de la création du cluster.

* Les exemples de fichiers à partir de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Ce référentiel comprend les éléments suivants :

    * Un projet Visual Studio capable de créer un conteneur de stockage, stratégie stockée et SAS pour une utilisation avec HDInsight
    
    * Un script Python qui peut créer un conteneur de stockage, stratégie stockée et SAS pour une utilisation avec HDInsight
    
    * Un script PowerShell qui peut créer un nouveau cluster HDInsight et configurez-le pour utiliser les associations de sécurité.

##<a name="shared-access-signatures"></a>Signatures d’accès partagé

Il existe deux formes de Signatures de l’accès partagé :

* Ad hoc : l’heure de début, date d’expiration et autorisations pour les associations de sécurité sont tous spécifiée sur l’URI de SAS (explicite ou implicite, dans le cas où l’heure de début est omis).

* La stratégie d’accès stockée : une stratégie d’accès stockée est définie sur un conteneur de ressources - un conteneur blob, table, file d’attente ou partage de fichiers - et peut être utilisée pour gérer les contraintes pour une ou plusieurs signatures d’accès partagé. Lorsque vous associez un SAS à une stratégie d’accès stockée, les associations de sécurité hérite les contraintes - l’heure de début, date d’expiration et les autorisations - définies pour la stratégie d’accès stockée.

La différence entre les deux formes est importante pour un scénario clé : révocation. Un SAS est une URL, afin que les personnes qui obtiennent les associations de sécurité peuvent, que qui a demandé dans un premier temps. Si un SAS est publiée, il peut être utilisé par n’importe qui dans le monde. Un SAS est distribué est valide jusqu'à ce qu’un des quatre événements se produit :

1. Le délai d’expiration spécifié sur les associations de sécurité est atteinte.

2. Le délai d’expiration spécifié sur la stratégie d’accès stockées référencée par les associations de sécurité est atteinte (si une stratégie d’accès stockée est référencée et si elle spécifie un délai d’expiration). Cette erreur peut se produire soit parce que l’intervalle s’écoule ou parce que vous avez modifié la stratégie d’accès stockée pour avoir un délai d’expiration dans le passé, ce qui est une façon de retirer les associations de sécurité.

3. La stratégie d’accès stockées référencée par les associations de sécurité est supprimée, ce qui est un autre moyen de supprimer les associations de sécurité. Notez que si vous recréez la stratégie d’accès stockée avec exactement le même nom, tous les jetons d’associations de sécurité existants à nouveau sera valides en fonction des autorisations associées à cette stratégie d’accès stockées (en supposant que le délai d’expiration sur les associations de sécurité n’a pas été). Si vous avez l’intention de révoquer les associations de sécurité, veillez à utiliser un nom différent si vous recréez la stratégie d’accès avec un délai d’expiration dans le futur.

4. Le compte qui a été utilisé pour créer l’association de sécurité est régénérée. Notez que cela entraîne tous les composants de l’application à l’aide de cette clé de compte pour ne parviennent pas à s’authentifier tant qu’ils sont mis à jour pour utiliser l’autre clé de compte valide ou la clé du compte nouvellement régénéré.

> [AZURE.IMPORTANT] Une signature accès partagé URI est associé à la clé du compte utilisée pour créer la signature et associé stocké une stratégie d’accès (le cas échéant). Si aucune stratégie d’accès stockées n’est spécifié, la seule manière d’annuler une signature accès partagé est de modifier la clé de compte. 

Il est recommandé de toujours utiliser des stratégies d’accès stockées, afin que vous pouvez révoquer les signatures ou étendre la date d’expiration si nécessaire. Les étapes décrites dans ce document utilisent stockées les stratégies d’accès pour générer une séquence SAS.

Pour plus d’informations sur les Signatures de l’accès partagé, voir [Présentation du modèle d’associations de sécurité](../storage/storage-dotnet-shared-access-signature-part-1.md).

##<a name="create-a-stored-policy-and-generate-a-sas"></a>Créer une stratégie stockée et générer un SAS

Actuellement, vous devez créer une stratégie stockée par programme. Vous pouvez trouver le C# et exemple Python de création d’une stratégie stockée et un SAS à [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

###<a name="create-a-stored-policy-and-sas-using-c"></a>Créer une stratégie stockée et les associations de sécurité à l’aide de C\#

1. Ouvrez la solution dans Visual Studio.

2. Dans l’Explorateur de solutions, avec le bouton droit sur le projet __SASToken__ et sélectionnez __Propriétés__.

3. Sélectionnez les __paramètres__ et ajouter des valeurs pour les entrées suivantes :

    * StorageConnectionString : La chaîne de connexion pour le compte de stockage que vous souhaitez créer une stratégie stockée et un SAS pour. Le format doit être `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` où `myaccount` est le nom de votre compte de stockage et `mykey` est la clé pour le compte de stockage.
    
    * ContainerName : Le conteneur du compte de stockage que vous souhaitez restreindre l’accès.
    
    * SASPolicyName : Le nom à utiliser pour la stratégie stockée qui va être créée.
    
    * FileToUpload : Le chemin d’accès à un fichier qui sera téléchargé vers le conteneur.
    
4. Exécutez le projet. Une fenêtre de console s’affiche et informations semblables au suivant seront affiche une fois les associations de sécurité a été générée :

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    Enregistrer le jeton de stratégie SAS, car vous en aurez besoin lors de l’association du compte de stockage à votre cluster HDInsight. Vous devez également le nom de compte de stockage et le nom du conteneur.
    
###<a name="create-a-stored-policy-and-sas-using-python"></a>Créer une stratégie stockée et les associations de sécurité en utilisant les Python

1. Ouvrez le fichier SASToken.py et modifiez les valeurs suivantes :

    * stratégie de\_nom : le nom à utiliser pour la stratégie stockée qui va être créée.
    
    * stockage\_compte\_nom : le nom de votre compte de stockage.
    
    * stockage\_compte\_clé : la clé pour le compte de stockage.
    
    * stockage\_conteneur\_nom : le conteneur dans le compte de stockage que vous souhaitez restreindre l’accès.
    
    * exemple\_fichier\_chemin d’accès : le chemin d’accès à un fichier qui sera téléchargé vers le conteneur

2. Exécutez le script. Le jeton d’associations de sécurité semblable au suivant s’affiche lorsque le script se termine :

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    Enregistrer le jeton de stratégie SAS, car vous en aurez besoin lors de l’association du compte de stockage à votre cluster HDInsight. Vous devez également le nom de compte de stockage et le nom du conteneur.
    
##<a name="use-the-sas-with-hdinsight"></a>Utiliser les associations de sécurité avec HDInsight

Lors de la création d’un cluster de HDInsight, vous devez spécifier un compte de stockage principal et vous pouvez éventuellement spécifier des comptes de stockage supplémentaires. Ces deux méthodes d’ajout de stockage nécessitent un accès complet aux comptes de stockage et les récipients qui sont utilisés.

Pour utiliser une Signature accès partagé pour limiter l’accès à un conteneur, vous devez ajouter une entrée personnalisée à la configuration du __site principal__ pour le cluster.

* Pour les clusters sous __Windows__ ou __sous Linux__ de HDInsight, cela lors de la création du cluster à l’aide de PowerShell.

* Pour les clusters d’HDInsight __sous Linux__ , vous modifiez la configuration après la création du cluster à l’aide de Ambari.

###<a name="create-a-new-cluster-that-uses-the-sas"></a>Créer un nouveau cluster qui utilise les associations de sécurité

Un exemple de création d’un cluster de HDInsight qui utilise les associations de sécurité est inclus dans le `CreateCluster` répertoire du référentiel. Pour l’utiliser, procédez comme suit :

1. Ouvrir le `CreateCluster\HDInsightSAS.ps1` de fichiers dans un éditeur de texte et modifiez les valeurs suivantes au début du document.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    Par exemple, remplacez `'mycluster'` le nom du cluster que vous souhaitez créer. Les valeurs SAS doivent correspondre les valeurs des étapes précédentes lors de la création d’un compte de stockage et un jeton d’associations de sécurité.
    
    Une fois que vous avez modifié les valeurs, enregistrez le fichier.

1. Ouvrez une nouvelle invite de PowerShell d’Azure. Si vous n’êtes pas familiarisé avec PowerShell d’Azure, ou que vous ne le n'avez pas installé, reportez-vous à la section [installer et configurer Azure PowerShell][powershell].

2. À partir de l’invite de commandes, utilisez la suivante pour s’authentifier sur votre abonnement Azure :

        Login-AzureRmAccount
    
    Lorsque vous y êtes invité, connectez-vous avec le compte de votre abonnement Azure.
    
    Si votre nom d’utilisateur est associé à plusieurs abonnements Azure, vous devez utiliser `Select-AzureRmSubscription` pour sélectionner l’abonnement que vous souhaitez utiliser.

2. À partir de l’invite de commandes, changez de répertoire pour le `CreateCluster` répertoire qui contient le fichier HDInsightSAS.ps1. Puis utilisez ce qui suit pour exécuter le script
        
        .\HDInsightSAS.ps1
    
    Que le script s’exécute, il consignera sortie à l’invite de PowerShell lorsqu’il crée des comptes de stockage et le groupe de la ressource. Vous êtes alors invité à entrer l’utilisateur HTTP pour l’HDInsight du cluster. Il s’agit du compte d’utilisateur utilisé pour sécuriser l’accès HTTP/s pour le cluster.
    
    Si vous créez un cluster Linux, vous également demandera un mot de passe et un nom de compte d’utilisateur SSH. Cela est utilisé pour la connexion d’accès à distance au cluster.
    
    > [AZURE.IMPORTANT] Lorsque vous êtes invité le HTTP/s ou SSH nom d’utilisateur et mot de passe, vous devez fournir un mot de passe qui répond aux critères suivants :
    >
    > - Doit être au moins de 10 caractères
    > - Doit contenir au moins un chiffre
    > - Doit contenir au moins un caractère non alphanumérique
    > - Doit contenir au moins une majuscule ou une minuscule


Il prendra un certain temps pour que ce script terminé, généralement environ 15 minutes. Lorsque le script se termine sans erreur, le cluster a été créé.

###<a name="update-an-existing-cluster-to-use-the-sas"></a>Mise à jour d’un cluster existant pour utiliser les associations de sécurité

Si vous disposez d’un cluster existant fonctionnant sous Linux, vous pouvez ajouter les associations de sécurité à la configuration du __site principal__ en procédant comme suit :

1. Ouvrez l’interface utilisateur du web Ambari pour votre cluster. L’adresse de cette page est https://YOURCLUSTERNAME.azurehdinsight.net. Lorsque vous y êtes invité, s’authentifier sur le cluster en utilisant le nom d’administrateur (admin) et un mot de passe utilisé lors de la création du cluster.

2. À partir de la partie gauche de l’interface utilisateur du web Ambari, sélectionnez __très__ , puis l’onglet __configurations__ au milieu de la page.

3. Sélectionnez l’onglet __Avancé__ et faites défiler jusqu'à la section __personnalisée core-site__ .

4. Développez la section __personnalisée core-site__ , puis faites défiler jusqu'à la fin et cliquez sur le lien __Ajouter une propriété...__ . Utilisez les valeurs suivantes pour les champs de __clé__ et la __valeur__ :

    * __Clé__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __Valeur__: The SAS retournée par l’application C# ou Python, vous avez exécuté précédemment
    
    Avec le nom de conteneur que vous avez utilisé le C# ou application de SAS, remplacez __CONTAINERNAME__ . Remplacez __STORAGEACCOUNTNAME__ par le nom de compte de stockage utilisé.

5. Cliquez sur le bouton __Ajouter__ pour enregistrer cette clé et la valeur, puis cliquez sur le bouton __Enregistrer__ pour enregistrer les modifications de configuration. Lorsque vous y êtes invité, ajoutez une description de la modification (« ajout d’accès de stockage SAS » par exemple), puis cliquez sur __Enregistrer__.

    Cliquez sur __OK__ lorsque les modifications ont été effectuées.

    > [AZURE.IMPORTANT] Cela enregistre les modifications de configuration, mais vous devez redémarrer plusieurs services avant que la modification prenne effet.

6. Dans l’interface utilisateur du site web d’Ambari, sélectionnez __très__ à partir de la liste sur la gauche et puis sélectionnez __Redémarrer toutes les__ dans la __Actions de Service__ liste déroulante de droite. Lorsque vous y êtes invité, sélectionnez __Activer le mode de maintenance__ et puis sélectionnez __Conform redémarrer toutes les ».

    Répétez ce processus pour les entrées MapReduce2 et les fils de la liste sur la gauche de la page.

7. Une fois qu’ils ont redémarré chacune d’elles et sélectionnez Désactiver le mode maintenance à partir des __Actions de Service__ de liste déroulante.

##<a name="test-restricted-access"></a>Tester l’accès restreint

Pour vérifier que vous avez limité l’accès, appliquez les méthodes suivantes :

* Pour les clusters de HDInsight __fonctionnant sous Windows__ , utilisez le Bureau à distance pour se connecter au cluster. Pour plus d’informations, reportez-vous à la section [pu se connecter à HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) .

    Une fois connecté, vous pouvez utiliser l’icône de la __ligne de commande Hadoop__ sur le bureau pour ouvrir une invite de commande.

* Pour les clusters de HDInsight __basé sur Linux__ , utiliser le protocole SSH pour se connecter au cluster. Consultez une des options suivantes pour plus d’informations sur l’utilisation de SSH avec clusters basés sur Linux :

    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Une fois connecté au cluster, procédez comme suit pour vérifier que vous pouvez uniquement des éléments de liste et de la lecture sur le compte de stockage SAS :

1. À partir de l’invite de commandes, tapez ce qui suit. Remplacez __SASCONTAINER__ par le nom du conteneur créé pour le compte de stockage SAS. Remplacez __SASACCOUNTNAME__ par le nom du compte de stockage utilisé pour les associations de sécurité :

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    Cette liste le contenu du conteneur, qui doit inclure le fichier qui a été téléchargé lorsque les associations de sécurité et le conteneur a été créé.
    
2. Utilisez ce qui suit afin de vérifier que vous pouvez lire le contenu du fichier. Remplacez le __SASCONTAINER__ et le __SASACCOUNTNAME__ comme dans l’étape précédente. Remplacez le __nom de fichier__ avec le nom du fichier affiché dans la commande précédente :

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    Cette liste le contenu du fichier.
    
3. Pour télécharger le fichier sur le système de fichiers local, utilisez ce qui suit :

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    Il télécharge le fichier dans un fichier local nommé __testfile.txt__.

4. Pour télécharger le fichier local dans un nouveau fichier nommé __testupload.txt__ sur le stockage SAS, utilisez ce qui suit :

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    Vous recevrez un message semblable au suivant :
    
        put: java.io.IOException
        
    Cette erreur se produit car l’emplacement de stockage est lecture + liste uniquement. Pour sauvegarder les données sur le stockage par défaut pour le cluster, qui est accessible en écriture, utilisez ce qui suit :
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    Cette fois, l’opération doit se terminer normalement.
    
##<a name="troubleshooting"></a>Résolution des problèmes

###<a name="a-task-was-canceled"></a>Une tâche a été annulée.

__Symptômes__: lorsque vous créez un cluster en utilisant le script PowerShell, le message d’erreur suivant peut s’afficher :

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__Cause__: cette erreur peut se produire si vous utilisez un mot de passe pour l’utilisateur admin/HTTP pour le cluster, ou (pour les clusters basés sur Linux,) l’utilisateur SSH.

__Résolution__: utilisez un mot de passe qui répond aux critères suivants :

- Doit être au moins de 10 caractères
- Doit contenir au moins un chiffre
- Doit contenir au moins un caractère non alphanumérique
- Doit contenir au moins une majuscule ou une minuscule

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment ajouter un stockage à accès limité à votre cluster HDInsight, découvrir d’autres façons de travailler avec des données sur votre cluster :

* [Utilisez la ruche avec HDInsight](hdinsight-use-hive.md)

* [Utilisez des porcs avec HDInsight](hdinsight-use-pig.md)

* [Utilisez MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md
