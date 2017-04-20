<properties
    pageTitle="Scénarios pour l’Analytique avancée processus et des technologies dans l’apprentissage automatique Azure | Microsoft Azure"
    description="Sélectionnez les scénarios appropriés pour cela avancée prédictive analytique avec le processus de Science de données équipe."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na" 
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scénarios pour l’analytique avancée dans l’apprentissage automatique de Azure

Cet article présente la diversité des exemples de sources de données et les scénarios cible qui peuvent être gérés par l' [Équipe de données Science processus (TDSP)](data-science-process-overview.md). Le TDSP offre une approche systématique permettant aux équipes de collaborer sur la création d’applications intelligentes. Les scénarios présentés ici illustrent les options disponibles dans le flux de travail de traitement des données qui varient selon les caractéristiques des données, les emplacements source et référentiels cibles dans Azure.

L' **arbre de décision** permettant de sélectionner les exemples de scénarios qui est adapté à vos données et votre objectif est présentée dans la dernière section.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Chacune des sections ci-dessous présente un exemple de scénario. Pour chaque scénario, une science de données possibles ou l’analytique avancée de flux et de prise en charge de ressources Azure sont répertoriés.

>[AZURE.NOTE]**Pour tous les scénarios suivants, vous devez :**
><br/>
>* [Créer un compte de stockage](../storage/storage-create-storage-account.md)
><br/>
>* [Créer un espace de travail formation de Machine Azure](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Scénario \#1 : petites à moyenne dataset sous forme de tableau dans un fichier local

![Petites et moyennes fichiers locaux][1]

#### <a name="additional-azure-resources-none"></a>Autres ressources Azure : aucun

1.  Se connecter à [Machine Azure Studio de formation](https://studio.azureml.net/).

2.  Téléchargement d’un groupe de données.

3.  Un flux d’expérience pédagogique de Machine Azure jeu téléchargé à partir de la génération.

## <a name="smalllocalprocess"></a>Scénario \#2 : petite à moyenne dataset de fichiers locaux qui nécessitent un traitement

![Petites et moyennes fichiers locaux avec traitement][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Autres ressources Azure : Azure Virtual Machine (serveur IPython bloc-notes)

1.  Créer un ordinateur virtuel de Azure IPython bloc-notes en cours d’exécution.

2.  Télécharger les données vers un conteneur de stockage Azure.

3.  Prétraitement et nettoyer des données IPython bloc-notes, l’accès aux données à partir du conteneur de stockage Azure.

4.  Transformer les données nettoyées, sous forme de tableau.

5.  Enregistrer les données transformées dans le BLOB Azure.

6.  Se connecter à [Machine Azure Studio de formation](https://studio.azureml.net/).

7.  Lire les données de BLOB Azure en utilisant les [Données d’importation] [ import-data] module.

8. Créer un flux d’expérience pédagogique de Machine Azure commençant par jeu ingérés.

## <a name="largelocal"></a>Scénario \#3 : ensemble de données volumineux de fichiers locaux, ciblant les BLOB Azure

![Grands fichiers locaux][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Autres ressources Azure : Azure Virtual Machine (serveur IPython bloc-notes)

1.  Créer un ordinateur virtuel de Azure IPython bloc-notes en cours d’exécution.

2.  Télécharger les données vers un conteneur de stockage Azure.

3.  Prétraitement et nettoyer des données IPython bloc-notes, accès aux données d’objets BLOB Azure.

4.  Transformation de données nettoyées, sous forme de tableau, si nécessaire.

5.  Explorer les données et créer des fonctions selon les besoins.

6.  Extraire un échantillon de données de petite à moyenne.

7.  Enregistrer des données échantillonnées dans le BLOB Azure.

8. Se connecter à [Machine Azure Studio de formation](https://studio.azureml.net/).

9. Lire les données de BLOB Azure en utilisant les [Données d’importation] [ import-data] module.

10. Générer des flux d’expérimentation Azure Machine Learning commençant par jeu ingérés.


## <a name="smalllocaltodb"></a>Scénario \#4 : petite à moyenne dataset de fichiers locaux, ciblage de SQL Server dans une Machine de Virtal Azure

![Petites et moyennes fichiers locaux vers la base de données SQL dans Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Autres ressources Azure : Azure Virtual Machine (SQL Server / serveur de IPython bloc-notes)

1.  Créer un ordinateur virtuel de Azure exécutant SQL Server + IPython bloc-notes.

2.  Télécharger les données vers un conteneur de stockage Azure.

3.  Prétraitement et nettoyez les données dans le conteneur de stockage Azure à l’aide du bloc-notes de IPython.

4.  Transformation de données nettoyées, sous forme de tableau, si nécessaire.

5.  Enregistrer les données dans les fichiers locaux de la machine virtuelle (IPython portable est en cours d’exécution sur l’ordinateur virtuel, les lecteurs locaux font référence aux lecteurs de l’ordinateur virtuel).

6.  Charger des données à la base de données SQL Server en cours d’exécution sur un ordinateur virtuel d’Azure.

    Option \#1 : à l’aide de SQL Server Management Studio.

    - Connexion à SQL Server VM
    - Exécution de SQL Server Management Studio.
    - Créer des tables de base de données et de la cible.
    - Utilisez une de l’ensemble importer des méthodes pour charger les données à partir de fichiers locaux de la machine virtuelle.

    Option \#2 : à l’aide de IPython portable – pas recommandé pour les grandes et moyennes des groupes de données<!-- -->    
    - Chaîne de connexion ODBC permet d’accéder à SQL Server sur une machine virtuelle.
    - Créer des tables de base de données et de la cible.
    - Utilisez une de l’ensemble importer des méthodes pour charger les données à partir de fichiers locaux de la machine virtuelle.

7.  Explorer les données, créer des fonctions selon les besoins. Remarque Les fonctionnalités ne devant pas être matérialisé dans les tables de la base de données. Seulement Notez la requête nécessaire pour les créer.

8. Choisissez une taille d’échantillon de données, si nécessaire ou vous le souhaitez.

9. Se connecter à [Machine Azure Studio de formation](https://studio.azureml.net/).

10. Lire les données directement à partir du SQL Server en utilisant les [Données d’importation] [ import-data] module. Collez la requête nécessaire qui extrait les champs d’et crée les fonctions échantillonne les données si nécessaire directement dans les [Données d’importation] [ import-data] requête.

11. Générer des flux d’expérimentation Azure Machine Learning commençant par jeu ingérés.

## <a name="largelocaltodb"></a>Scénario \#5 : ensemble de données volumineux dans des fichiers locaux, cible de SQL Server dans Azure VM

![Fichiers volumineux locales DB SQL dans Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Autres ressources Azure : Azure Virtual Machine (SQL Server / serveur de IPython bloc-notes)

1.  Créer un ordinateur virtuel de Azure exécutant SQL Server et le serveur du carnet d’IPython.

2.  Télécharger les données vers un conteneur de stockage Azure.

3.  (Facultatif) Données de prétraitement et propres.

    une barre d’outils.  Prétraitement et nettoyer des données IPython bloc-notes, accès aux données d’objets BLOB Azure.

    b.  Transformation de données nettoyées, sous forme de tableau, si nécessaire.

    c.  Enregistrer les données dans les fichiers locaux de la machine virtuelle (IPython portable est en cours d’exécution sur l’ordinateur virtuel, les lecteurs locaux font référence aux lecteurs de l’ordinateur virtuel).

4.  Charger des données à la base de données SQL Server en cours d’exécution sur un ordinateur virtuel d’Azure.

    une barre d’outils.  Connexion à SQL Server VM.

    b.  Si les données n'enregistrées pas déjà, télécharger des fichiers de données à partir du conteneur de stockage Azure pour dossier de local-VM.

    c.  Exécution de SQL Server Management Studio.

    d.  Créer des tables de base de données et de la cible.

    e.  Utilisez une de l’essentiel méthodes pour charger les données d’importation.

    f.  Si les jointures de table sont nécessaires, créer des index pour accélérer les jointures.

     > [AZURE.NOTE] Pour accélérer le chargement de tailles de données de grande taille, il est recommandé que vous créez les tables partitionnées et les données en parallèle d’importation en bloc. Pour plus d’informations, consultez [Importation de données parallèle pour les Tables partitionnées SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorer les données, créer des fonctions selon les besoins. Remarque Les fonctionnalités ne devant pas être matérialisé dans les tables de la base de données. Seulement Notez la requête nécessaire pour les créer.

6.  Choisissez une taille d’échantillon de données, si nécessaire ou vous le souhaitez.

7.  Se connecter à [Machine Azure Studio de formation](https://studio.azureml.net/).

8. Lire les données directement à partir du SQL Server en utilisant les [Données d’importation] [ import-data] module. Collez la requête nécessaire qui extrait les champs d’et crée les fonctions échantillonne les données si nécessaire directement dans les [Données d’importation] [ import-data] requête.

9. Flux d’expérience Azure Machine Learning simple commençant par le groupe de données téléchargée

## <a name="largedbtodb"></a>Scénario \#6 : ensemble de données volumineux dans un SQL Server de base de données sur-prem, ciblage de SQL Server dans une Machine virtuelle Azure

![Grand SQL DB-prem DB SQL dans Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Autres ressources Azure : Azure Virtual Machine (SQL Server / serveur de IPython bloc-notes)

1.  Créer un ordinateur virtuel de Azure exécutant SQL Server et le serveur du carnet d’IPython.

2.  Utilisez une des données Exporter les méthodes pour exporter les données à partir de SQL Server vers des fichiers de vidage.

    > [AZURE.NOTE] Si vous décidez de déplacer toutes les données à partir de la base de données sur prem, une autre méthode (plus rapide) pour déplacer la base de données complète à l’instance de SQL Server dans Azure. Ignorez les étapes pour exporter les données, créer la base de données et charge/importer des données à la base de données cible et suivre la méthode de remplacement.

3.  Télécharger les fichiers de vidage au conteneur de stockage Azure.

4.  Charger les données à une base de données SQL Server en cours d’exécution sur une Machine virtuelle de Azure.

    une barre d’outils.  Connexion à la machine virtuelle de SQL Server.

    b.  Télécharger les fichiers de données à partir d’un conteneur de stockage Azure dans le dossier local-VM.

    c.  Exécution de SQL Server Management Studio.

    d.  Créer des tables de base de données et de la cible.

    e.  Utilisez une de l’essentiel méthodes pour charger les données d’importation.

    f.  Si les jointures de table sont nécessaires, créer des index pour accélérer les jointures.

    > [AZURE.NOTE] Pour accélérer le chargement de tailles de données volumineuses, créez les tables partitionnées et en bloc importer les données en parallèle. Pour plus d’informations, consultez [Importation de données parallèle pour les Tables partitionnées SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorer les données, créer des fonctions selon les besoins. Remarque Les fonctionnalités ne devant pas être matérialisé dans les tables de la base de données. Seulement Notez la requête nécessaire pour les créer.

6.  Choisissez une taille d’échantillon de données, si nécessaire ou vous le souhaitez.

7.  Se connecter à [Machine Azure Studio de formation](https://studio.azureml.net/).

8. Lire les données directement à partir du SQL Server en utilisant les [Données d’importation] [ import-data] module. Collez la requête nécessaire qui extrait les champs d’et crée les fonctions échantillonne les données si nécessaire directement dans les [Données d’importation] [ import-data] requête.

9. Flux d’expérience simple Azure Machine Learning commençant par le groupe de données téléchargée.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Autre méthode pour copier une base de données complète à partir d’une de SQL Server en local à la base de données de SQL Azure

![Détachez la base de données locale et l’attacher à la base de données SQL dans Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Autres ressources Azure : Azure Virtual Machine (SQL Server / serveur de IPython bloc-notes)

Pour répliquer la base de données SQL Server complète de votre ordinateur virtuel de SQL Server, vous devez copier une base de données à partir d’un seul emplacement/serveur à l’autre, en supposant que la base de données peut être déconnecté temporairement. Pour cela, dans l’Explorateur d’objets SQL Server Management Studio ou en utilisant les commandes Transact-SQL équivalentes.

1. Détachez la base de données à l’emplacement source. Pour plus d’informations, consultez [détachement d’une base de données](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. Dans la fenêtre de l’Explorateur Windows ou l’invite de commandes Windows, copiez le fichier de base de données détachée ou fichiers et fichier journal ou dans l’emplacement cible sur l’ordinateur virtuel de SQL Server dans Azure.
3. Joindre les fichiers copiés à l’instance de SQL Server cible. Pour plus d’informations, voir [association d’une base de données](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Déplacer une base de données à l’aide de détacher et d’attacher (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Scénario \#7 : données volumineuses dans des fichiers locaux, ciblent la base de données de la ruche dans Azure HDInsight Hadoop clusters

![Données volumineuses dans la ruche de cible locale][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Autres ressources Azure : Cluster d’Hadoop HDInsight Azure et d’Azure Virtual Machine (serveur IPython bloc-notes)

1.  Créer un ordinateur virtuel de Azure exécutant server du carnet d’IPython.

2.  Créer un cluster Azure HDInsight Hadoop.

3.  (Facultatif) Données de prétraitement et propres.

    une barre d’outils.  Prétraitement et nettoyer des données IPython bloc-notes, accès aux données d’objets BLOB Azure.

    b.  Transformation de données nettoyées, sous forme de tableau, si nécessaire.

    c.  Enregistrer les données dans les fichiers locaux de la machine virtuelle (IPython portable est en cours d’exécution sur l’ordinateur virtuel, les lecteurs locaux font référence aux lecteurs de l’ordinateur virtuel).

4.  Télécharger les données vers le conteneur par défaut du cluster Hadoop sélectionné à l’étape 2.

5.  Charger des données à la base de données de la ruche de cluster d’Azure HDInsight Hadoop.

    une barre d’outils.  Ouvrir une session sur le nœud principal du cluster Hadoop

    b.  Ouvrez la ligne de commande Hadoop.

    c.  Entrez le répertoire racine de ruche par commande `cd %hive_home%\bin` depuis la ligne de commande de Hadoop.

    d.  Exécuter les requêtes de la ruche pour créer la base de données et les tables et charger des données depuis le stockage blob pour les tables de la ruche.

    > [AZURE.NOTE] Si les données sont volumineux, les utilisateurs peuvent créer la table de la ruche avec des partitions. Ensuite, les utilisateurs peuvent utiliser un `for` boucle dans Hadoop ligne de commande sur le nœud principal pour charger des données dans la table de ruche partitionnée par partition.

6.  Explorer les données et créer des fonctions de ligne de commande Hadoop le cas échéant. Remarque Les fonctionnalités ne devant pas être matérialisé dans les tables de la base de données. Seulement Notez la requête nécessaire pour les créer.

    une barre d’outils.  Ouvrir une session sur le nœud principal du cluster Hadoop

    b.  Ouvrez la ligne de commande Hadoop.

    c.  Entrez le répertoire racine de ruche par commande `cd %hive_home%\bin` depuis la ligne de commande de Hadoop.

    d.  Exécutez les requêtes ruche depuis la ligne de commande de Hadoop sur le nœud principal du cluster Hadoop pour Explorer les données et créer des fonctions selon les besoins.

7.  Si nécessaire, ni souhaitée, échantillonner les données pour tenir dans un Studio de formation de Machine Azure.

8.  Se connecter à [Machine Azure Studio de formation](https://studio.azureml.net/).

9. Lire les données directement à partir de la `Hive Queries` à l’aide de l' [Importation des données] [ import-data] module. Collez la requête nécessaire qui extrait les champs d’et crée les fonctions échantillonne les données si nécessaire directement dans les [Données d’importation] [ import-data] requête.

10. Flux d’expérience simple Azure Machine Learning commençant par le groupe de données téléchargée.

## <a name="decisiontree"></a>Arbre de décision pour la sélection de scénario
------------------------

Le diagramme suivant résume les scénarios décrits ci-dessus et avancée des processus Analytique et choix de la technologie qui fait de vous prendre pour chacun des scénarios détaillés. Notez que le traitement des données, l’exploration, ingénierie de fonctionnalité et échantillonnage peuvent prendre placer dans un ou plusieurs méthode à l’environnement, à la source, intermédiaire, et/ou les environnements cibles – et peut se poursuivre de manière itérative en fonction des besoins. Le schéma constitue une illustration des flux possibles uniquement et ne fournit pas une énumération exhaustive.

![Exemples de scénarios de procédure pas à pas processus DS][8]

### <a name="advanced-analytics-in-action-examples"></a>Advanced Analytique en action, exemples

De bout en bout pour formation de Machine Azure procédures pas à pas qui emploient l’avancée des processus Analytique et la technologie à l’aide de groupes de données publics, voir :


* [Des processus Science de données d’équipe en action : à l’aide de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
* [Des processus Science de données d’équipe en action : à l’aide de HDInsight Hadoop clusters](machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
