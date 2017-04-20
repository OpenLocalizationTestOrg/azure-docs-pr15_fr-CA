<properties
    pageTitle="Publier des applications de HDInsight | Microsoft Azure"
    description="Apprenez à créer et publier des applications de HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/18/2016"
    ms.author="jgao"/>

# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Publier des applications HDInsight dans Azure Marketplace

Une application de HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight de fonctionnant sous Linux. Ces applications peuvent être développées par Microsoft, des éditeurs de logiciels indépendants (ISV) ou par vous-même. Dans cet article, vous apprendrez comment publier une application HDInsight dans Azure Marketplace.  Pour plus d’informations sur la publication sur le marché d’Azure, consultez [publier une offre sur le marché d’Azure](../marketplace-publishing/marketplace-publishing-getting-started.md).

HDInsight les applications utilisent le modèle de *Mettre votre propre licence (BYOL)* , où fournisseur de l’application est responsable de la gestion des licences aux utilisateurs finaux de l’application, et les utilisateurs finaux ne paient par Azure les ressources qu’ils créent, par exemple le cluster de HDInsight et de ses machines virtuelles/nœuds. À ce stade, la facturation de l’application elle-même n’est pas effectuée grâce à Azure.

Autre application HDInsight liée à l’article :

- [HDInsight d’installer les applications](hdinsight-apps-install-applications.md): Découvrez comment installer une application de HDInsight sur des clusters.
- [Installer des applications HDInsight](hdinsight-apps-install-custom-applications.md): Apprenez à installer et tester des applications HDInsight personnalisées.

 
## <a name="prerequisites"></a>Conditions préalables

Afin de soumettre votre application personnalisée sur le marché, vous devez avoir créé et testé votre application personnalisée. Consultez les articles suivants :

- [Installer des applications HDInsight](hdinsight-apps-install-custom-applications.md): Apprenez à installer et tester des applications HDInsight personnalisées.

Vous devez avoir également enregistrer votre compte de développeur. Reportez-vous à la section [publier une offre sur le marché d’Azure](../marketplace-publishing/marketplace-publishing-getting-started.md) et [créer un compte de développeur de Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-application"></a>Définir l’application

Il y a deux étapes impliquées pour la publication d’applications vers Azure Marketplace.  D’abord, vous définissez un fichier **createUiDef.json** pour indiquer les clusters de votre application est compatible avec ; et que vous publiez le modèle à partir du portail Azure. Voici un exemple de fichier createUiDef.json.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|Champ  | Description   | Valeurs possibles|
|-------|---------------|----------------|
|types de  | Les types de cluster que l’application est compatible avec.    |Hadoop, HBase, tempête, allumage (ou toute combinaison de ces)|
|niveaux  | Les niveaux de cluster qui n’est compatible avec l’application.    |Standard, Premium (ou les deux)|
|versions|  Les types de cluster HDInsight que l’application est compatible avec.    |3.4|

## <a name="package-application"></a>Application du package

Créer un fichier zip qui contient tous les fichiers requis pour l’installation de vos applications HDInsight. Vous devez le fichier zip dans une [application de publication](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Consultez un exemple à [installer des applications de HDInsight](hdinsight-apps-install-custom-applications.md).

    >[AZURE.IMPORTANT] Le nom, le nom de script d’installation application doit être unique pour un cluster avec le format ci-dessous. En outre les installer et désinstaller des actions de script doivent être idempotents, ce qui signifie que des scripts peut être appelé repeatly tout en produisant le même résultat.
    
    >   » nom : « [concat (' teinte-installation-v0 ','-', uniquestring('applicationName')] »
        
    >Remarque Il existe trois parties au nom du script :
        
    >   1. Un préfixe du nom de script, qui comprend le nom de l’application ou un nom pertinent pour l’application.
    >   2. Un «- » pour une meilleure lisibilité.
    >   3. Fonction de chaîne unique avec le nom de l’application comme paramètre.

    >   Un exemple est le finit par devenir ci-dessus : teinte-installation-v0-4wkahss55hlas dans la liste action de script persistant. Pour une charge utile JSON d’exemple, consultez [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Scripts obligatoires.

> [AZURE.NOTE] Les fichiers d’application (y compris les fichiers d’application web s’il en existe un) peut se trouver sur n’importe quel point de terminaison accessible publiquement.

## <a name="publish-application"></a>Publication d’application

Suivez les étapes ci-dessous pour publier une application HDInsight :

1. Ouvrez une session sur le [portail de publication Azure](https://publish.windowsazure.com/).
2. Cliquez sur **modèles de solutions** à partir de la gauche pour créer un nouveau modèle de solution.
3. Entrez un titre, puis cliquez sur **créer un nouveau modèle de solution**.
3. Cliquez sur **Centre de développement de créer de compte et de participer au programme d’Azure** pour inscrire votre société si vous n’avez pas fait.  Voir [Création d’un compte de développeur de Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Cliquez sur **définir certaines Topologies de mise en route**. Un modèle de solution est un « parent » à toutes les topologies. Vous pouvez définir plusieurs topologies dans un modèle d’offre/solution. Lorsqu’une offre est poussée vers la mise en œuvre, il est poussé avec tous ses topologies. 
4. Entrez un nom de topologie, puis cliquez sur le signe plus.
5. Entrez une nouvelle version, puis cliquez sur le signe Plus.
6. Téléchargez le fichier zip préparé en [application du Package](#package-application).  
7. Cliquez sur **demande de Certification**. L’équipe de certification Microsoft va passer en revue les fichiers et certifie la topologie.

## <a name="next-steps"></a>Étapes suivantes

- [HDInsight d’installer les applications](hdinsight-apps-install-applications.md): Découvrez comment installer une application de HDInsight sur des clusters.
- [Installer des applications HDInsight](hdinsight-apps-install-custom-applications.md): Découvrez comment déployer une application non publiée de HDInsight à HDInsight.
- [Clusters basés sur Linux de personnaliser la HDInsight à l’aide de Script Action](hdinsight-hadoop-customize-cluster-linux.md): Apprenez à utiliser l’Action de Script pour installer des applications supplémentaires.
- [Hadoop de basé sur Linux de créer des clusters dans HDInsight à l’aide des modèles du Gestionnaire de ressources Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Apprenez à appeler le Gestionnaire de ressources des modèles pour créer des clusters de HDInsight.
- [Utilisez les noeuds vides dans HDInsight](hdinsight-apps-use-edge-node.md): Apprenez à utiliser un nœud vide pour accéder au cluster de HDInsight, tester des applications HDInsight et hébergement d’applications de HDInsight.

