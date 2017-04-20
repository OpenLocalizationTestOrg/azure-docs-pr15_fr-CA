<properties
pageTitle="Ajouter des bibliothèques de la ruche lors de la création du cluster HDInsight | Azure"
description="Découvrez comment ajouter des bibliothèques de ruche (fichiers jar) à un cluster d’HDInsight lors de la création du cluster."
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
ms.date="09/20/2016"
ms.author="larryfr"/>

#<a name="add-hive-libraries-during-hdinsight-cluster-creation"></a>Ajouter des bibliothèques de la ruche lors de la création du cluster HDInsight

Si vous avez des bibliothèques que vous utilisez fréquemment à la ruche sur HDInsight, ce document contient des informations sur l’utilisation d’une Action de Script pour charger les bibliothèques lors de la création du cluster. Ainsi, les bibliothèques globalement disponible ruche (inutile d’utiliser [Ajouter JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) de les charger.)

##<a name="how-it-works"></a>Mode de fonctionnement

Lors de la création d’un cluster, vous pouvez éventuellement spécifier une Action de Script qui exécute un script sur les nœuds de cluster lors de leur création. Le script dans ce document accepte un seul paramètre, qui est un emplacement de WASB qui contient les bibliothèques (stockés sous forme de fichiers jar) qui va être préchargés.

Lors de la création du cluster, le script énumère les fichiers, les copie à la `/usr/lib/customhivelibs/` répertoire sur des nœuds de tête et de travail, puis les ajoute à la `hive.aux.jars.path` propriété dans la `core-site.xml` fichier. Sur les clusters basés sur Linux, il met également à jour la `hive-env.sh` le fichier avec l’emplacement des fichiers.

> [AZURE.NOTE] Les actions de script dans cet article peut rendre les bibliothèques disponible dans les scénarios suivants :
>
> * __Basé sur Linux de HDInsight__ - lors de l’utilisation de la __ligne de commande de la ruche__ __WebHCat__ (Templeton) et __HiveServer2__.
> * __HDInsight de base Windows__ - lors de l’utilisation de la __ligne de commande de la ruche__ et la __WebHCat__ (Templeton).

##<a name="the-script"></a>Le script

__Emplacement des scripts__

Pour les __clusters basés sur Linux__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Pour les __clusters basés sur Windows__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Configuration requise__

* Les scripts doivent être appliqués aux __nœuds de tête__ et __les nœuds de traitement__.

* Les fichiers JAR que vous souhaitez installer doivent être stockés dans le stockage Blob Azure dans un __conteneur unique__. 

* Le compte de stockage contenant la bibliothèque de fichiers de fichier jar __doit__ être lié au cluster HDInsight lors de la création. Pour ce faire de deux façons :

    * En étant dans un conteneur sur le compte de stockage par défaut pour le cluster.
    
    * En étant dans un conteneur sur un conteneur de stockage. Par exemple, dans le portail, vous pouvez utiliser __Configuration facultative__, __comptes de stockage lié__ à ajouter un stockage supplémentaire.

* Le chemin d’accès WASB le conteneur doit être spécifié en tant que paramètre à l’Action du Script. Par exemple, si les fichiers JAR sont stockés dans un conteneur nommé __libs__ sur un compte de stockage nommé __mystorage__, le paramètre serait __wasbs://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] Ce document suppose que vous avez créer déjà d’un compte de stockage, conteneur de tache et téléchargé les fichiers lui. 
    >
    > Si vous n’avez pas créé un compte de stockage, vous pouvez pour cela via le [portail Azure](https://portal.azure.com). Vous pouvez ensuite utiliser un utilitaire tel que [Explorateur de stockage Azure](http://storageexplorer.com/) pour créer un nouveau conteneur dans le compte et télécharger des fichiers vers elle.

##<a name="create-a-cluster-using-the-script"></a>Créer un cluster en utilisant le script

> [AZURE.NOTE] Les étapes suivantes créent un nouveau cluster basé sur Linux de HDInsight. Pour créer un nouveau cluster fonctionnant sous Windows, sélectionnez __Windows__ comme le système d’exploitation de cluster lors de la création du cluster et utiliser le script Windows (PowerShell) au lieu du script bash.
> 
> Vous pouvez également utiliser Azure PowerShell ou le Kit de développement .NET HDInsight pour créer un cluster à l’aide de ce script. Pour plus d’informations sur l’utilisation de ces méthodes, voir [clusters de personnaliser la HDInsight avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrer la mise en service d’un cluster à l’aide de la procédure dans les [clusters de disposition HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mais n’effectuez pas de mise en service.

2. Sur la lame de **Configuration facultatives** , sélectionnez les **Actions de Script**et fournir les informations comme indiqué ci-dessous :

    * __Nom__: entrez un nom convivial pour l’action du script.
    * __URI du SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __Tête__: Activez cette case à cocher
    * __Travailleur__: Activez cette option.
    * __SOIGNEUR__: laissez ce champ vide.
    * __Paramètres__: permet d’entrer l’adresse WASB pour le compte de stockage et de conteneur qui contient les fichiers JAR. Par exemple, __wasbs://libs@mystorage.blob.core.windows.net/__.

3. Au bas des **Actions de Script**, utilisez **le bouton** enregistrer la configuration.

4. Sur la lame de **Configuration facultative** , permet de sélectionner __Les comptes de stockage lié__ et cliquez sur le lien __Ajouter une clé de stockage__ . Sélectionnez le compte de stockage qui contient les fichiers JAR, puis utilisez les boutons __Sélectionner__ pour enregistrer les paramètres et retourner la lame de __Configuration facultatives__ .

5. Utilisez le bouton **Sélectionner** en bas de la lame de **Configuration facultative** pour enregistrer les informations de configuration facultatives.

6. Continuer la mise en service du cluster, comme décrit dans [HDInsight de fourniture des clusters sous Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Une fois la fin de la création du cluster, vous serez en mesure d’utiliser les fichiers JAR ajoutés par le biais de ce script à partir de la ruche sans utiliser le `ADD JAR` instruction.

##<a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à ajouter des bibliothèques de ruche contenues dans les fichiers jar à un cluster d’HDInsight lors de la création du cluster. Pour plus d’informations sur l’utilisation de la ruche, voir [Utilisation de la ruche avec HDInsight](hdinsight-use-hive.md)
