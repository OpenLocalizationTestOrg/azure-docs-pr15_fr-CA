<properties 
    pageTitle="Personnaliser les clusters Hadoop pour le processus de Science de données équipe | Microsoft Azure" 
    description="Modules de Python à disposition personnalisé Azure HDInsight Hadoop clusters les plus courants."
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="hangzh;bradsev" />

# <a name="customize-azure-hdinsight-hadoop-clusters-for-the-team-data-science-process"></a>Personnaliser les clusters Azure HDInsight Hadoop pour le processus de Science de données équipe 

Cet article explique comment personnaliser un cluster HDInsight Hadoop en installant 64 bits Anaconda (Python 2.7) sur chaque nœud lorsque le cluster est configuré comme un service de HDInsight. Il indique également comment accéder à la headnode pour soumettre les tâches personnalisées au cluster. Cette personnalisation permet de nombreux modules Python populaires inclus dans Anaconda facilement disponible pour une utilisation dans les fonctions définies par l’utilisateur (UDF) sont conçus pour traiter les enregistrements de la ruche du cluster. Pour obtenir des instructions sur les procédures utilisées dans ce scénario, voir [comment soumettre des requêtes de la ruche](machine-learning-data-science-move-hive-tables.md#submit).

Le menu ci-dessous des liens vers des rubriques qui décrivent comment configurer différents environnements de science de données utilisées par le [Processus de Science données Team (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]


## <a name="customize"></a>Personnaliser HDInsight Azure Hadoop Cluster

Pour créer un cluster HDInsight Hadoop personnalisée, les utilisateurs doivent ouvrir une session sur le [**Portail classique d’Azure**](https://manage.windowsazure.com/), cliquez sur **Nouveau** dans le coin inférieur gauche, puis sélectionnez les SERVICES de données -> HDINSIGHT -> **Personnalisé créer** pour ouvrir la fenêtre **Détails de Cluster** . 

![Créer l’espace de travail](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

Entrée le nom du cluster pour être créé sur la page de configuration 1 et acceptez les valeurs par défaut pour les autres champs. Cliquez sur la flèche pour passer à la page suivante de la configuration. 

![Créer l’espace de travail](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

Sur la page de configuration 2, entrer le nombre de **Nœuds de données**du **Réseau de la région/VIRTUAL**et sélectionnez la taille du **Serveur de nœud** et le **Nœud de données**. Cliquez sur la flèche pour passer à la page suivante de la configuration.

>[AZURE.NOTE] La **Région/VIRTUAL réseau** doit être identique à celui de la région du compte de stockage qui doit être utilisé pour le cluster HDInsight Hadoop. Dans le cas contraire, dans la quatrième page de configuration, le compte de stockage que les utilisateurs souhaitent utiliser n’apparaît pas sur la liste déroulante **Nom**du compte.

![Créer l’espace de travail](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png)

Sur la page de configuration 3, fournir un nom d’utilisateur et le mot de passe pour le cluster HDInsight Hadoop. **Ne** sélectionnez pas l' _entrée de la ruche/Oozie Metastore_. Puis, cliquez sur la flèche pour passer à la page suivante de la configuration. 

![Créer l’espace de travail](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png)

Sur la page de configuration 4, spécifiez le nom de compte de stockage, le conteneur par défaut du cluster HDInsight Hadoop. Si les utilisateurs sélectionner _créer le conteneur par défaut_ dans la liste déroulante de **Conteneur par défaut** , un conteneur avec le même nom que le cluster sera créé. Cliquez sur la flèche pour passer à la dernière page de configuration.

![Créer l’espace de travail](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png)

Sur la dernière page de configuration des **Actions de Script** , cliquez sur le bouton **Ajouter des actions de script** et remplissez les champs de texte avec les valeurs suivantes.
 
* **Nom** - n’importe quelle chaîne comme nom de l’action de ce script. 
* **TYPE de nœud** - sélectionner **tous les nœuds**. 
* **URI du SCRIPT** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
    * *publicscripts* est un conteneur public dans le compte de stockage 
    * *getgoing* que nous permet de partager des fichiers de script PowerShell pour faciliter le travail des utilisateurs dans Azure. 
* **Paramètres** - (laisser vide)

Enfin, cliquez sur la case à cocher pour démarrer la création du cluster HDInsight Hadoop personnalisé. 

![Créer l’espace de travail](./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a>Accéder au nœud de tête du Cluster d’Hadoop

Les utilisateurs doivent activer l’accès distant au cluster Hadoop dans Azure avant de pouvoir accéder le nœud principal du cluster Hadoop via RDP. 

1. Ouvrir une session sur [**Portail classique d’Azure**](https://manage.windowsazure.com/) **HDInsight** sur la gauche, sélectionnez votre cluster Hadoop à partir de la liste de clusters, cliquez sur l’onglet **CONFIGURATION** et sélectionnez puis cliquez sur l’icône **Activer à distance** du bas de la page.
    
    ![Créer l’espace de travail](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png)

2. Dans la fenêtre **Configurer le Bureau à distance** , renseignez les champs nom d’utilisateur et le mot de passe et sélectionnez la date d’expiration pour l’accès distant. Puis, cliquez sur la case à cocher pour activer l’accès distant pour le nœud de tête du cluster Hadoop.

    ![Créer l’espace de travail](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png)
    
>[AZURE.NOTE] Le nom d’utilisateur et le mot de passe pour l’accès distant ne sont pas le nom d’utilisateur et le mot de passe que vous utilisez lors de la création du cluster Hadoop. Il s’agit d’un ensemble distinct d’informations d’identification. En outre, la date d’expiration de l’accès à distance doit se trouver dans les 7 jours à partir de la date actuelle.

Après l’activation de l’accès distant, cliquez sur **se connecter** au bas de la page à distance dans le nœud de tête. Vous ouvrez une session pour le nœud de tête du cluster Hadoop en entrant les informations d’identification de l’utilisateur d’accès à distance que vous avez spécifié précédemment.

![Créer l’espace de travail](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png)

Les étapes dans le processus analytique avancée sont mappés dans le [Processus de Science données Team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure des étapes de déplacement des données vers HDInsight, de traitent et d’aperçu en vue de l’apprentissage à partir des données avec Azure Machine d’apprentissage.

Pour obtenir des instructions sur l’accès aux modules Python qui sont inclus dans le Anaconda du nœud de tête du cluster dans les fonctions définies par l’utilisateur (UDF) sont utilisés pour traiter des enregistrements de ruche stockés dans le cluster, reportez-vous à la section [comment soumettre des requêtes de la ruche](machine-learning-data-science-move-hive-tables.md#submit) .

 
