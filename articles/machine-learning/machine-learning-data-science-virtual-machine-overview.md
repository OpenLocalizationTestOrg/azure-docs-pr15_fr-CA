<properties
    pageTitle="Ce qu’est un ordinateur virtuel de Science données ? | Microsoft Azure"
    description="Découvrez les scénarios clés, fonctionnalités et la mise en route avec données scientifiques Machines virtuelles, un environnement et un kit prêt pour l’analytique."
    keywords="outils de données scientifiques, machine virtuelle de science données, outils de science données, science de données linux"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="bradsev" />


# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introduction au nuage données Science Virtual Machine pour Linux et Windows

La Machine virtuelle de Science données est une image personnalisée de la machine virtuelle de Microsoft Azure cloud spécialement conçus pour faire la science de données. Il a de nombreuses science de données courants et d’autres outils préinstallés et préconfigurés pour lancer la création d’applications intelligentes pour analytique avancée. Il est disponible sur Windows Server 2012 ou sur les versions de Linux basée sur CentOS de 7.2 OpenLogic. 

Cette rubrique explique ce que vous pouvez faire avec la Science de données machine virtuelle présente certains des scénarios clés pour l’utilisation de la machine virtuelle, détaille les fonctionnalités clées disponibles sur les versions de Windows et Linux et fournit des instructions sur la manière de commencer à les utiliser.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Que puis-je faire avec la Machine virtuelle de données scientifiques ?

L’objectif de la Machine virtuelle de données scientifiques est de fournir des professionnels des données à tous les niveaux de compétence et les rôles avec un environnement informatique de données exemptes de frottement. Cette machine virtuelle vous permet de gagner un temps considérable que vous passeriez si vous aviez déployé un environnement comparable sur votre propre. À la place, commencer immédiatement votre projet scientifique de données dans une instance nouvellement créée de la machine virtuelle. 

La Science de données machine virtuelle est conçue et configurée pour l’utilisation de des scénarios d’utilisation de large. Vous pouvez adapter à votre environnement vers le haut ou vers le bas en fonction des besoins votre projet. Vous êtes en mesure d’utiliser la langue de votre choix pour programmer des tâches de données scientifiques. Vous pouvez installer d’autres outils et de le personnaliser pour vos besoins.
 
## <a name="key-scenarios"></a>Scénarios clés
Cette section propose quelques scénarios clés pour lesquels la machine virtuelle Science de données peuvent être déployée.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Bureau préconfiguré analytique dans le nuage

La Science de données machine virtuelle fournit une configuration de référence pour les équipes de science de données cherchant à remplacer leurs postes de travail locaux avec un bureau de nuage gérés. Cette configuration de référence permet de s’assurer qu’une installation cohérente permettant de vérifier les expériences et à favoriser la collaboration entre les chercheurs de toutes les données d’une équipe. Il permet également de réduire les coûts en réduisant la charge de sysadmin et en enregistrant le temps nécessaire pour évaluer, d’installer et de mettre à jour les différents packages de logiciels nécessaires à analytique avancée.  

### <a name="data-science-training-and-education"></a>Éducation et formation de science de données

Formateurs d’entreprise et les enseignants qui enseignent science de données classes fournissent généralement une image de machine virtuelle pour veiller à ce que leurs étudiants une installation cohérente et que les exemples fonctionnent de manière prévisible. La machine virtuelle Science de données crée un environnement d’à la demande avec une configuration cohérente qui simplifie les défis de la prise en charge et d’incompatibilité. Les cas où ces environnements doivent être généré fréquemment, en particulier pour les classes de formation plus courtes, bénéficient considérablement.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>À la demande capacité élastique pour les projets à grande échelle

Données science hackathons/concours ou la modélisation des données à grande échelle et l’exploration nécessitent la mise à l’échelle des capacités du matériel, généralement de courte durée. La machine virtuelle Science de données peut aider à répliquer l’environnement informatique de données rapidement sur la demande, sur la mise à l’échelle des serveurs qui permettent des expériences de ressources informatique puissante à exécuter.

### <a name="short-term-experimentation-and-evaluation"></a>Évaluation et l’expérimentation à court terme

La Science de données machine virtuelle peut être utilisée pour évaluer ou Découvrez des outils tels que Microsoft R Server, SQL Server, outils de Visual Studio, Jupyter, formation approfondie / ML de kits de ressources et de nouveaux outils populaires dans la Communauté, avec un minimum d’effort le programme d’installation. Étant donné que la machine virtuelle Science de données permet de configurer rapidement, il peut être appliqué dans d’autres scénarios d’utilisation à court terme tels que la réplication des expériences publiées, l’exécution des démonstrations, des procédures pas à pas suivantes dans les sessions en ligne ou des didacticiels de la conférence.


## <a name="whats-included-in-the-data-science-vm"></a>Ce qui est inclus dans la machine virtuelle Science de données ?

La Machine virtuelle de Science données comporte de nombreux outils de science de données courants déjà installé et configuré. Il inclut également des outils qui facilitent le travail fonctionner avec divers produits Azure analytique et de données. Vous pouvez Explorer et construire des modèles prédictifs sur des ensembles de données à grande échelle à l’aide de Microsoft Server R ou 2016 de SQL Server. Un hôte d’autres outils à partir de la Communauté open source et de Microsoft sont également inclus, ainsi que des exemples de code et les ordinateurs portables. Le tableau suivant détaille et compare les principaux composants inclus dans les éditions de Windows et Linux de la Machine virtuelle de données scientifiques.


|**Édition de Windows** | **Édition de Linux** |
|----------------|---------------|
|Microsoft R Server Édition développeur | Microsoft R Server Édition développeur |
|Anaconda Python 2.7, 3.5 | Anaconda Python 2.7, 3.5 |
|Serveur Jupyter bloc-notes (R, Python) | JupyterHub : Jupyter de la multi-utilisateurs portables (R, les Python, Ariane) |
|2016 de SQL Server Developer Edition : Analytique de base de données évolutive avec les services de R | Postgres, écureuil SQL (outil de base de données), les pilotes de SQL Server et la ligne de commande (bcp, sqlcmd) |
|Édition de communauté de Visual Studio (IDE) de 2015 </br> -HDInsight azure (Hadoop), lac de données, outils de données de SQL Server </br> -Outils Node.js, Python et R pour Visual Studio |IDE et les éditeurs </br> -Eclipse avec le plug-in de la trousse à outils Azure </br> -Emacs (avec ESS, auctex) gedit |
|Bureau de BI de puissance | -- |
|Outils d’apprentissage machine </br> -Intégration avec apprentissage automatique Azure </br> -CNTK (formation approfondie/AI) </br> -Xgboost (outil populaire de ML dans le concours de scientifiques de données) </br> -Vowpal Wabbit (apprentissage rapide en ligne) </br> -Vibrer (données de démarrage rapide visual et outil d’analytique) </br> -Mxnet (formation approfondie/AI) | Outils d’apprentissage machine </br> -Intégration d’apprentissage automatique Azure </br> -CNTK (formation approfondie/AI) </br> -Xgboost (outil populaire de ML dans le concours de scientifiques de données) </br> -Vowpal Wabbit (apprentissage rapide en ligne) </br> -Vibrer (données de démarrage rapide visual et outil d’analytique)  |
| Kits de développement logiciel pour accéder aux Azure et Cortana Intelligence Suite de services | Kits de développement logiciel pour accéder aux Azure et Cortana Intelligence Suite de services |
| Outils pour le déplacement des données et de gestion des ressources de Azure et données Big : Explorateur de stockage Azure, CLI, PowerShell, AdlCopy (lac de données Azure), AzCopy, dtui (pour DocumentDB), passerelle de gestion des données Microsoft | Outils pour le déplacement des données et de gestion des ressources de Azure et données Big : Explorateur de stockage Azure, CLI |
| GIT, du plug-in Visual Studio Team Services | GIT |
| Port de Windows les plus populaires utilitaires de ligne de commande de Linux/Unix accessible via l’invite de commande GitBash | -- |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>La mise en route avec la machine virtuelle Science de données Windows

- Créez une instance de la machine virtuelle sur Windows en accédant à [cette page](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) et en sélectionnant le bouton vert de **créer un ordinateur virtuel** .
- Connectez-vous à la machine virtuelle à partir de votre Bureau à distance en utilisant les informations d’identification que vous avez spécifié lorsque vous avez créé l’ordinateur virtuel.
- Pour découvrir et lancer les outils disponibles, cliquez sur le menu **Démarrer** .


## <a name="get-started-with-the-linux-data-science-vm"></a>Mise en route de la machine virtuelle Science de données Linux

- Pour créer une instance de la machine virtuelle sous Linux (basé sur le OpenLogic CentOS) en accédant à [cette page](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) et en cliquant sur le bouton **créer un ordinateur virtuel** .
- Connectez-vous à l’ordinateur virtuel à partir d’un client SSH tel que Putty ou commande SSH, en utilisant les informations d’identification que vous avez spécifié lors de la création de la machine virtuelle.
- Dans l’invite de commandes, entrez dsvm-plus-info.
- Pour un graphique Bureau, télécharger le client de X2Go pour votre plate-forme client [ici](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) et suivez les instructions fournies dans le document de VM de Science de données Linux [disposition la Machine virtuelle Linux données scientifiques](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).


## <a name="next-steps"></a>Étapes suivantes

### <a name="for-the-windows-data-science-vm"></a>Pour la Science de données machine virtuelle Windows

- Pour plus d’informations sur l’exécution des outils spécifiques disponibles sur la version de Windows, reportez-vous à la section [disposition de la Machine virtuelle Microsoft données Science](machine-learning-data-science-provision-vm.md) et
-  Pour plus d’informations sur la façon d’effectuer diverses tâches nécessaires pour votre projet de science de données sur la machine virtuelle de Windows, consultez les [dix choses à que faire sur la science de données Machine virtuelle](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Pour une machine virtuelle Linux données Science

- Pour plus d’informations sur l’exécution des outils spécifiques disponibles sur la version de Linux, voir [fourniture de la Machine virtuelle Linux données scientifiques](machine-learning-data-science-linux-dsvm-intro.md).
- Pour une procédure pas à pas qui montre comment effectuer plusieurs tâches de science de données commune avec le VM Linux, consultez [science de données sur la Machine virtuelle Linux données scientifiques](machine-learning-data-science-linux-dsvm-walkthrough.md).
