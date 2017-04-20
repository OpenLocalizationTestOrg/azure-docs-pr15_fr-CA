<properties
    pageTitle="Configurer l’ordinateur virtuel de Science Linux données | Microsoft Azure"
    description="Configurez et créez un ordinateur virtuel de Linux données scientifiques sur Azure analytique et formation de l’ordinateur."
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
    ms.date="09/12/2016"
    ms.author="bradsev" />

# <a name="provision-the-linux-data-science-virtual-machine"></a>Configurer l’ordinateur virtuel de Science données de Linux

La Machine virtuelle Linux données Science est une machine virtuelle Azure qui est fourni avec un ensemble d’outils préalablement installés. Ces outils sont couramment utilisés pour effectuer l’analytique des données et formation de l’ordinateur. Les composants logiciels de clé inclus sont les suivants :

- Microsoft R Server Édition développeur
- Distribution de Python anaconda (versions 2.7 et 3.5), y compris les bibliothèques d’analyse de données courants
- JupyterHub - un serveur multi-utilisateur de bloc-notes Jupyter R, Python, noyaux de Julia de prise en charge
- Explorateur de stockage Azure
- Interface de ligne de commande Azure (CLI) pour la gestion des ressources Azure
- Base de données de PostgresSQL
- Outils d’apprentissage machine
    - [Shared Computer Toolkit de réseau informatique (CNTK)](https://github.com/Microsoft/CNTK): une profondeur de formation kit logiciel de Microsoft Research.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): un ordinateur rapide système prenant en charge des techniques d’allreduce en ligne, le hachage, réductions, learning2search, actif, de formation et d’apprentissage interactif.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): un outil offrant une implémentation rapide et précise arborescence amplifiées.
    - [Vibrer](http://rattle.togaware.com/) (la R analytique outil pour apprendre facilement) : un outil qui facilite la mise en route avec analytique des données et l’ordinateur R simple avec l’exploration de données basées sur une interface utilisateur graphique, de formation et de modélisation avec la génération de code automatique R.
- SDK Azure dans Java, Python, node.js, Ruby, PHP
- Bibliothèques dans R et les Python pour utilisent dans la formation de Machine d’Azure et d’autres services Azure
- Outils de développement et les éditeurs (Eclipse, Emacs, gedit, vi)

Science de données consiste à itérer sur une séquence de tâches :

1. Recherche, de chargement et de traitement des données
2. Créer et tester des modèles
3. Déploiement de modèles destinés à la consommation dans des applications intelligentes

Les scientifiques de données utilisent divers outils pour effectuer ces tâches. Il peut prendre un temps considérable pour rechercher les versions appropriées du logiciel, puis pour télécharger, compiler et installer ces versions.

La Machine virtuelle Linux données scientifiques peuvent faciliter ce sensiblement. Utilisez-le pour démarrer votre projet analytique. Il vous permet de travailler sur les tâches dans les différents langages, notamment R, Python, SQL, Java et C++. Eclipse fournit un IDE pour développer et tester votre code qui est facile à utiliser. L’Azure SDK inclus dans la machine virtuelle vous permet de créer des applications à l’aide de plusieurs services sur Linux pour la plateforme de cloud de Microsoft. En outre, vous avez accès à d’autres langages tels que Ruby, Perl, PHP et node.js également préinstallé.

Il n’y a pas de frais logiciels pour cette image de machine virtuelle de données scientifiques. Vous payez uniquement les frais d’utilisation matériel Azure qui sont évalués en fonction de la taille de la machine virtuelle que vous configurez avec l’image de machine virtuelle. Vous trouverez plus de détails sur les frais de calcul sur la [machine virtuelle de la liste de page sur Azure Marketplace ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).


## <a name="prerequisites"></a>Conditions préalables

Avant de pouvoir créer un ordinateur virtuel de Linux données Science, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**: pour en obtenir une, consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/free/).
- **Compte de stockage un Azure**: pour en créer une, reportez-vous à la section [Création d’un compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account). Le compte de stockage peut également être créé dans le cadre du processus de création de la machine virtuelle, si vous ne souhaitez pas utiliser un compte existant.


## <a name="create-your-linux-data-science-virtual-machine"></a>Créez votre ordinateur virtuel de Science données de Linux

Voici la procédure pour créer une instance de la Machine virtuelle Linux données scientifiques :

1.  Accédez à la machine virtuelle mise en vente sur le [portail Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2.   Cliquez sur **créer** (en bas) pour accéder à l’Assistant. ![configurer-données-science-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.   Les sections suivantes fournissent les entrées pour chaque étape de l’Assistant (énumérées sur la droite de la figure précédente) utilisées pour créer la Machine virtuelle Microsoft données scientifiques. Voici les intrants nécessaires à la configuration de chacune de ces étapes :

    une barre d’outils. **Notions de base**:

  - **Nom**: nom de votre serveur de science de données vous sont en train de créer.
  - **Nom d’utilisateur**: compte premier signe de code.
  - **Mot de passe**: mot de passe compte premier (vous pouvez utiliser les clé publique SSH au lieu du mot de passe).
  - **Abonnement**: Si vous avez plus d’un abonnement, sélectionnez celui sur lequel l’ordinateur doit être créé et facturé. Vous devez disposer des privilèges de création de ressource pour cet abonnement.
  - **Groupe de ressources**: vous pouvez créer un autre ou utiliser un groupe existant.
  - **Emplacement**: sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui a la plupart de vos données, ou qui est plus proche de votre emplacement physique pour les accès réseau le plus rapide.

    b. **Taille**:

  - Sélectionnez un des types de serveur qui répond à vos exigences fonctionnelles et les contraintes de coût. Sélectionnez **Afficher tout** pour afficher plus de choix de tailles de machine virtuelle.

    c. **Paramètres**:

  - **Type de disque**: choisissez la **prime** si vous préférez un lecteur à l’état solide (SSD). Dans le cas contraire, cliquez sur **Standard**.
  - **Compte de stockage**: vous pouvez créer un nouveau compte de stockage Azure dans votre abonnement, ou utiliser un existant dans le même emplacement que celui qui a été choisi à l’étape de **Notions de base** de l’Assistant.
  - **Autres paramètres**: dans la plupart des cas, vous utilisez simplement les valeurs par défaut. Pour prendre en compte les valeurs par défaut, pointez sur le lien d’information de l’aide sur les champs spécifiques.

    d. **Résumé**:

  - Vérifiez que toutes les informations que vous avez entré sont correctes.

    e. **Acheter**:

  - Pour démarrer la mise en service, cliquez sur **acheter**. Un lien est fourni avec les termes de la transaction. La machine virtuelle n’a pas d’éventuels frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisi à l’étape de **taille** .

La mise en service le diplôme environ 10 à 20 minutes. L’état de la mise en service est affiché sur le portail Azure.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Comment faire pour accéder à la Machine virtuelle Linux données Science

Une fois que la machine virtuelle est créée, vous pouvez vous connecter lui à l’aide de SSH. Utilisez les informations d’identification de compte que vous avez créé dans la section **principes de base** de l’étape 3 pour l’interface de shell de texte. Sous Windows, vous pouvez télécharger un outil de client SSH comme [Putty](http://www.putty.org). Si vous préférez un bureau graphique (X Windows System), vous pouvez utiliser le X11 transfert sur Putty ou installer le client X2Go.

>[AZURE.NOTE] Le client X2Go effectué beaucoup mieux que le transfert dans le test de X11. Nous vous recommandons d’utiliser le client X2Go pour une interface graphique de bureau.


## <a name="installing-and-configuring-x2go-client"></a>Installation et configuration de X2Go client

Le VM Linux est déjà mis en service avec le serveur de X2Go et prêt à accepter des connexions client. Pour vous connecter au bureau Linux VM graphique, procédez comme suit sur le client :

1. Téléchargez et installez le client de X2Go pour votre plate-forme client à partir de [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Exécutez le client X2Go et sélectionnez la **Nouvelle Session**. Il ouvre une fenêtre de configuration avec plusieurs onglets. Entrez les paramètres de configuration suivants :
    * **Onglet de session**:
        - **Hôte**: le nom d’hôte ou l’adresse IP de votre machine virtuelle Science de données Linux.
        - **Ouverture de session**: nom d’utilisateur sur la VM Linux.
        - **Voie SSH**: laisser à 22, la valeur par défaut.
        - **Type de session**: modifier la valeur de XFCE. Actuellement la VM Linux prend uniquement en charge le bureau XFCE.
    * **Onglet média**: vous pouvez désactiver la prise en charge audio et le client d’impression si vous n’avez pas besoin de les utiliser.
    * **Dossiers partagés**: Si vous souhaitez des répertoires à partir de vos machines client montés sur la VM Linux, ajoutez les répertoires de l’ordinateur client que vous souhaitez partager avec la machine virtuelle sous cet onglet.

Après que vous être connecté à la machine virtuelle en utilisant le client SSH ou bureau de graphique XFCE via le client X2Go, vous êtes prêt à commencer à utiliser les outils qui sont installés et configurés sur l’ordinateur virtuel. Sur XFCE, vous pouvez voir les icônes et les raccourcis du menu applications pour la plupart des outils.


## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Outils installés sur la Machine virtuelle Linux données Science

### <a name="microsoft-r-open"></a>Microsoft R ouvrir
R est un des langages plus employés pour l’analyse des données et apprentissage automatique. Si vous souhaitez utiliser R pour votre analytique, la machine virtuelle a Microsoft R ouvert (MRO) avec Math Kernel Library (MKL). La MKL optimise les opérations mathématiques courantes dans les algorithmes d’analyse. MRO est 100 % compatible avec CRAN-R, et une des bibliothèques R parus CRAN peut être installée sur le MRO. Vous pouvez modifier vos programmes R dans un des éditeurs par défaut, comme gedit, vi ou Emacs. Vous pouvez également télécharger et utiliser d’autres IDE, tel que [RStudio](http://www.rstudio.com). Pour votre commodité, un simple script (installRStudio.sh) est fourni dans le répertoire **/dsvm/tools** qui installe des RStudio. Si vous utilisez l’éditeur Emacs, remarque que l’Emacs package ESS (Emacs parle des statistiques), qui simplifie la R des fichiers dans l’éditeur Emacs, a été préinstallé.

Pour lancer la R, vous tapez **R** dans le shell. Vous accédez alors à un environnement interactif. Pour développer votre programme R, utilisent généralement un éditeur tel que gedit ou, vi ou Emacs et ensuite exécuter les scripts dans R. Si vous installez RStudio, vous avez un environnement IDE graphique complète pour le développement de votre programme de R.

Il existe également un script R pour vous d’installer les [packages de haut 20 R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) si vous le souhaitez. Ce script peut être exécuté une fois que vous vous trouvez dans l’interface interactive R, qui peut être entré (comme indiqué) en tapant **R** dans le shell.  

### <a name="python"></a>Python
Pour le développement à l’aide de Python, distribution les Python Anaconda 2.7 et 3.5 a été installée. Cette distribution contient les Python de base ainsi qu’environ 300 des packages analytique plus populaires mathématiques, d’ingénierie et de données. Vous pouvez utiliser les éditeurs de texte par défaut. En outre, vous pouvez utiliser Spyder, un IDE de Python qui est fourni avec les distributions les Python Anaconda. Spyder a besoin d’un bureau de graphique ou de X11 de transfert. Un raccourci vers Spyder est fourni dans le bureau de graphique.

Puisque nous avons les Python 2.7 et 3.5, vous devez activer spécifiquement la version Python souhaitée que vous souhaitez utiliser dans la session en cours. Le processus d’activation définit la variable de chemin d’accès vers la nouvelle version de Python.

Pour activer les Python 2.7, exécutez la commande suivante à partir du shell :

    source /anaconda/bin/activate root

Python 2.7 est installé en */anaconda/bin*.

Pour activer les Python 3.5, exécutez la commande suivante à partir du shell :

    source /anaconda/bin/activate py35


Python 3.5 est installé dans */anaconda/envs/py35/bin*.

Pour appeler une session interactive de Python, tapez simplement les **python** dans le shell. Si vous êtes sur une interface graphique ou que vous avez X11 ensemble de transfert des, vous pouvez taper **spyder** pour lancer l’IDE Python.

### <a name="jupyter-notebook"></a>Jupyter portable

La distribution de Anaconda est également fourni avec un bloc-notes de Jupyter, un environnement de partage de code et analyse. Le bloc-notes de Jupyter est accessible via JupyterHub. Vous vous connectez à l’aide de votre nom d’utilisateur Linux local et le mot de passe.

Le serveur de carnet de Jupyter a été configuré avant avec les Python 2, 3 de Python et noyaux de R. Il existe une icône du bureau nommée « Jupyter bloc-notes » pour lancer le navigateur pour accéder au serveur de l’ordinateur portable. Si vous êtes sur l’ordinateur virtuel via un client SSH ou X2Go, vous pouvez également visiter [https://localhost:8000 /](https://localhost:8000/) pour accéder au serveur de bloc-notes Jupyter.

>[AZURE.NOTE] Continuer si vous obtenez tous les avertissements de certificat.

Vous pouvez accéder au serveur de bloc-notes de Jupyter à partir de n’importe quel hôte. Tapez simplement *https://\<nom VM DNS ou l’adresse IP\>: 8000 /*

>[AZURE.NOTE] Port 8000 est ouvert dans le pare-feu par défaut lors de la mise en service de la machine virtuelle.

Nous avons empaqueté des exemples d’agendas--les Python en un et l’autre dans R. Vous pouvez voir le lien vers les exemples sur la page d’accueil portable après que vous être authentifié dans le carnet de Jupyter à l’aide de votre nom d’utilisateur Linux local et le mot de passe. Vous pouvez créer un nouveau bloc-notes, sélectionnez **Nouveau**, puis sur le noyau de la langue appropriée. Si vous ne voyez pas le bouton **Nouveau** , cliquez sur l’icône **Jupyter** dans le coin supérieur gauche pour accéder à la page d’accueil du serveur de l’ordinateur portable.


### <a name="ides-and-editors"></a>IDE et les éditeurs

Vous avez la possibilité de plusieurs éditeurs de code. Cela inclut le vi/VIM, Emacs, gEdit et Eclipse. gEdit Eclipse sont des éditeurs graphiques et vous devrez être connecté à un bureau de graphique pour les utiliser. Ces éditeurs ont des ordinateurs de bureau et applications à lancer les raccourcis dans le menu.

**VIM** et **Emacs** sont des éditeurs de texte. Dans Emacs, nous avons installé un package de complément appelé Emacs parle statistiques (ESS) qui permet de travailler avec R plus facile au sein de l’éditeur Emacs. Vous trouverez plus d’informations sur [l’ESS](http://ess.r-project.org/).

**Eclipse** est une source ouverte, IDE extensible qui prend en charge plusieurs langues. L’édition de développeurs Java est l’instance installée sur l’ordinateur virtuel. Plug-ins sont disponibles pour plusieurs langues courantes qui peuvent être installés pour étendre l’environnement Eclipse. Nous avons également un plug-in installé sur Eclipse appelé **Shared Computer Toolkit Azure pour Eclipse**. Il vous permet de créer, de développer, de tester et de déployer des applications Azure à l’aide de l’environnement de développement Eclipse qui prend en charge les langages tels que Java. Il existe également un **Azure SDK pour Java** qui permet d’accéder à différents services Azure depuis un environnement Java. Vous trouverez plus d’informations sur la trousse à outils Azure Eclipse à [Shared Computer Toolkit Azure pour Eclipse](../azure-toolkit-for-eclipse.md).

**LaTex** est installé par le package de texlive avec Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) package complémentaire, qui simplifie la création de vos documents de LaTex dans Emacs.  

### <a name="databases"></a>Bases de données

#### <a name="postgres"></a>Postgres
La base de données **Postgres** de l’open source est disponible sur la machine virtuelle, avec les services en cours d’exécution et de l’initdb déjà terminée. Vous devez toujours créer des bases de données et les utilisateurs. Pour plus d’informations, consultez la [documentation de Postgres](https://www.postgresql.org/docs/).  

####  <a name="graphical-sql-client"></a>Client graphique de SQL
**Écureuil SQL**, un client graphique de SQL, a été fourni pour se connecter à des bases de données différentes (par exemple, Microsoft SQL Server, MySQL et Postgres) et pour exécuter des requêtes SQL. Vous pouvez effectuer cette opération à partir d’une session de bureau graphique (en utilisant le client X2Go, par exemple). Pour appeler un écureuil SQL, vous pouvez lancer à partir de l’icône sur le bureau ou exécutez la commande suivante sur la coque.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Avant la première utilisation, configurez vos pilotes et d’un alias de base de données. Les pilotes JDBC sont situés à :

*/usr/share/Java/jdbcdrivers*

Pour plus d’informations, consultez [Écureuil SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Outils de ligne de commande pour accéder à Microsoft SQL Server

Le package du pilote ODBC pour SQL Server est également fourni avec deux outils de ligne de commande :

**bcp**: l’essentiel d’utilitaire bcp copie des données entre une instance de Microsoft SQL Server et un fichier de données dans un format spécifié par l’utilisateur. L’utilitaire bcp peut être utilisé pour importer un grand nombre de nouvelles lignes dans des tables SQL Server, ou pour exporter des données de tables dans les fichiers de données. Pour importer des données dans une table, vous devez utiliser un fichier de format créé pour cette table, ou comprendre la structure de la table et les types de données valides pour ses colonnes.

Pour plus d’informations, consultez [connexion avec bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**SQLCMD**: vous pouvez entrer les instructions Transact-SQL avec l’utilitaire sqlcmd, ainsi que des procédures système et les fichiers à l’invite de commande de script. Cet utilitaire utilise ODBC pour exécuter des lots de Transact-SQL.

Pour plus d’informations, consultez [connexion avec sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

>[AZURE.NOTE] Il existe quelques différences dans cet utilitaire entre plates-formes Linux et Windows. Consultez la documentation pour plus de détails.


#### <a name="database-access-libraries"></a>Bibliothèques d’accès aux base de données

Les bibliothèques sont disponibles en R et les Python pour les bases de données access.

- R, le package de la **RODBC** ou **dplyr** vous permet d’interroger ou d’exécuter les instructions SQL sur le serveur de base de données.
- Dans les Python, la bibliothèque **pyodbc** fournit l’accès de base de données avec ODBC en tant que calque sous-jacent.  

Pour accéder à **Postgres**:

- À partir de r : Utilisez le package **RPostgreSQL**.
- À partir de Python : Utiliser la bibliothèque **psycopg2** .


### <a name="azure-tools"></a>Outils Azure
Les outils Azure suivants sont installés sur l’ordinateur virtuel :

- **Interface de ligne de commande Azure**: le CLI Azure permet vous permet de créer et de gérer des ressources Azure via des commandes shell. Pour appeler les outils Azure, tapez **aide azure**. Pour plus d’informations, consultez la [page de documentation Azure CLI](../virtual-machines-command-line-tools.md).
- **Explorateur de stockage Microsoft Azure**: Microsoft Azure Storage Explorer est un outil graphique qui est utilisé pour parcourir les objets que vous avez stockés dans votre compte de stockage Azure et pour transférer et télécharger des données vers et à partir d’objets BLOB Azure. Vous pouvez accéder à Explorateur de stockage à partir de l’icône de raccourci sur le bureau. Vous pouvez l’appeler à partir d’une invite de commandes en tapant **StorageExplorer**. Vous devez être connecté à partir d’un client de X2Go ou ont X11 ensemble de transfert des.
- **Bibliothèques d’Azure**: suivantes sont certaines des bibliothèques préalablement installés.

 - **Python**: les bibliothèques de l’Azure liées dans les Python qui sont installés sont **azure**, **azureml**, **pydocumentdb**et **pyodbc**. Avec les trois premières bibliothèques, vous pouvez accéder des services de stockage Azure, l’apprentissage de Machine Azure et Azure DocumentDB (une base de données NoSQL sur Azure). La bibliothèque de quatrième, pyodbc (avec le pilote ODBC de Microsoft pour SQL Server), permet l’accès à SQL Server, base de données de SQL Azure et entrepôt de données SQL Azure à partir de Python à l’aide de l’interface ODBC. Entrez la **liste des pip** pour afficher toutes les bibliothèques répertoriées. Veillez à exécuter cette commande dans les Python 2.7 et environnements de 3,5.
 - **R**: les bibliothèques liées à l’Azure dans R installés sont **AzureML** et **RODBC**.
 - **Java**: la liste des bibliothèques d’Azure Java peut être trouvée dans le répertoire **/dsvm/sdk/AzureSDKJava** sur l’ordinateur virtuel. Les bibliothèques principales sont Azure stockage et gestion des API DocumentDB, pilotes et JDBC pour SQL Server.  

Vous pouvez accéder au [portail Azure](https://portal.azure.com) à partir du navigateur Firefox préinstallé. Sur le portail Azure, vous pouvez créer, gérer et surveiller les ressources Azure.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure

L’apprentissage Machine Azure est un service en nuage entièrement géré qui vous permet de générer, de déployer et de partager des solutions d’analytique prédictive. Vous générez vos expériences et les modèles à partir de Studio de formation de Machine Azure. Il est accessible à partir d’un navigateur web sur l’ordinateur virtuel de science données en visitant [Microsoft Azure Machine Learning](https://studio.azureml.net).

Après que vous être connecté à un Studio de formation de Machine Azure, vous avez accès à une zone d’expérimentation où vous pouvez créer un flux logique pour l’ordinateur que les algorithmes d’apprentissage. Vous également avez accès à un ordinateur portable de Jupyter hébergé sur Azure Machine Learning et fonctionne de façon transparente avec les expériences dans le Studio de formation de Machine. Mettre la machine d’apprentissage des modèles que vous avez créés en les encapsulant dans une interface de service web. Ainsi, les clients écrits dans tout langage appeler des prévisions à partir de l’ordinateur, modèles d’apprentissage. Pour plus d’informations, consultez la [documentation de formation de l’ordinateur](https://azure.microsoft.com/documentation/services/machine-learning/).

Vous pouvez également créer vos modèles de R ou les Python sur la machine virtuelle et puis le déployer en production sur Azure Machine Learning. Nous avons installé les bibliothèques en R (**AzureML**) et les Python (**azureml**) pour activer cette fonctionnalité.

Pour plus d’informations sur le déploiement des modèles de R et les Python dans Azure Machine Learning, consultez [dix choses à faire sur la science de données Machine virtuelle](machine-learning-data-science-vm-do-ten-things.md) (en particulier, la section « générer des modèles à l’aide de R ou les Python et effectuent les à l’aide de la formation de Machine Azure »).

>[AZURE.NOTE] Ces instructions ont été écrites pour la version Windows de l’ordinateur virtuel Science de données. Mais les informations fournies sur le déploiement de modèles pour la formation de Machine Azure est applicable pour la VM Linux.

### <a name="machine-learning-tools"></a>Outils d’apprentissage machine

La machine virtuelle est fourni avec l’ordinateur de quelques outils et les algorithmes qui ont été précompilés et pré-installé localement de formation. Celles-ci comprennent :

* **CNTK** (Calcul réseau Shared Computer Toolkit de Microsoft Research) : un kit de formation de profondeur.
* **Vowpal Wabbit**: un algorithme d’apprentissage rapide en ligne.
* **xgboost**: un outil qui fournit des algorithmes d’arborescence optimisée, augmentée.
* **Python**: les Python Anaconda est livré avec les algorithmes d’apprentissage machine avec des bibliothèques comme Scikit-en savoir plus. Vous pouvez installer les autres bibliothèques à l’aide de la `pip install` commande.
* **R**: une riche bibliothèque de fonctions d’apprentissage machine est disponible pour R. Certaines des bibliothèques qui sont pré-installés sont lm, glm, randomForest, rpart. Autres bibliothèques peuvent être installées en exécutant :

        install.packages(<lib name>)

Voici quelques informations supplémentaires sur les outils de formation de machine trois premier dans la liste.

#### <a name="cntk"></a>CNTK
Il s’agit d’une source ouverte, complète le Kit d’outils de formation. Il est un outil de ligne de commande (cntk) et est déjà dans le chemin d’accès.

Pour exécuter un exemple de base, exécutez les commandes suivantes dans le shell :

    # Copy samples to your home directory and execute cntk
    cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo
    cd cntkdemo/Data
    cntk configFile=../Config/Simple.cntk

La sortie de modèle est *~/cntkdemo/Output/Models*.

Pour plus d’informations, reportez-vous à la section CNTK de [GitHub](https://github.com/Microsoft/CNTK)et le [wiki CNTK](https://github.com/Microsoft/CNTK/wiki).


#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit est un ordinateur qui utilise des techniques d’allreduce en ligne, le hachage, réductions, learning2search, actif, système de formation et d’apprentissage interactif.

Pour exécuter l’outil sur un exemple très simple, effectuez les opérations suivantes :

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Il existe des démonstrations d’autres, la plus grandes dans ce répertoire. Pour plus d’informations sur VW, consultez [Cette section de GitHub](https://github.com/JohnLangford/vowpal_wabbit)et le [wiki de Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Il s’agit d’une bibliothèque qui est conçue et optimisée pour les algorithmes amplifiée (arborescence). L’objectif de cette bibliothèque est à pousser les limites de calcul des ordinateurs pour les extrêmes nécessaires pour fournir l’arborescence à grande échelle de renforcement qui est évolutive, portable et précis.

Il est fourni sous la forme d’une ligne de commande, ainsi qu’une bibliothèque de R.

Pour utiliser cette bibliothèque dans R, vous pouvez démarrer une session interactive de R (en tapant **R** dans l’interpréteur de commandes) et de charger la bibliothèque.

Voici un exemple simple, que vous pouvez exécuter dans une invite de R :

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Pour exécuter la ligne de commande xgboost, voici les commandes à exécuter dans le shell :

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Un fichier .model est écrit dans le répertoire spécifié. Vous trouverez des informations sur cet exemple de démo [sur GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Pour plus d’informations sur xgboost, reportez-vous à la [page de documentation xgboost](https://xgboost.readthedocs.org/en/latest/)et son [référentiel de Github](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Hochet
Hochet ( **R** **A**nalytical **T**outil **T**o **L**gagner **E**asily) utilise la modélisation et l’exploration de données basé sur l’interface utilisateur. Il présente des statistiques et visual synthèses des données, les transformations des données qui peuvent être facilement modélisées, crée des modèles qui fonctionne sans surveillance et surveillées à partir des données, présente les performances des modèles sous forme de graphique, et jeux de données de nouveaux scores. Il génère également du code R, répliquer les opérations dans l’interface utilisateur qui peuvent être exécutées directement dans R ou utilisées comme point de départ pour une analyse ultérieure.

Pour exécuter Hochet, vous devez être dans une graphique ouverture de session session du bureau. Sur le terminal, tapez ```R``` à l’environnement R. À l’invite de R, entrez les commandes suivantes :

    library(rattle)
    rattle()

Désormais une interface graphique s’ouvre avec un ensemble d’onglets. Voici les étapes de démarrage rapide dans Hochet nécessaire pour utiliser un ensemble de données exemple météo et créez un modèle. Dans certaines des étapes ci-dessous, vous êtes invité à installer et charger certains packages R requis qui ne sont pas déjà sur le système automatiquement.

>[AZURE.NOTE] Si vous n’avez pas accès pour installer le package dans le répertoire système (par défaut), vous pouvez recevoir un message sur la fenêtre de console R pour installer des packages à votre bibliothèque personnelle. Répondez *y* si vous consultez ces invites.

1. Cliquez sur **exécuter**.
2. Une boîte de dialogue s’affiche, vous demandant si vous souhaitez utiliser le jeu de données d’exemple météo. Cliquez sur **Oui** pour charger l’exemple.
3. Cliquez sur l’onglet **modèle** .
4. Cliquez sur **Execute** pour créer une arborescence de décision.
5. Cliquez sur le **dessin** pour afficher l’arbre de décision.
6. Cliquez sur le bouton radio de **forêt** , puis cliquez sur **Execute** pour créer une forêt aléatoire.
7. Cliquez sur l’onglet de **l’évaluer** .
8. Cliquez sur le bouton radio de **risques** et cliquez sur **exécuter** pour afficher les deux tracés de performances risque (Cumulative).
9. Cliquez sur l’onglet **journal** pour afficher le code de génération R pour les opérations précédentes.
(En raison d’un bogue dans la version actuelle de Hochet, vous devez insérer un *#* caractère devant *exporter ce journal...* dans le texte du journal.)
10. Cliquez sur le bouton **Exporter** pour enregistrer le fichier de script de R nommé *weather_script. R* vers le dossier de base.

Vous pouvez quitter Hochet et R. Vous pouvez maintenant modifier le script généré de R, ou l’utiliser tel qu’il doit l’exécuter à tout moment pour répéter tout ce qui a été fait dans l’interface utilisateur de vibrer. En particulier pour les débutants en R, il s’agit d’un moyen facile pour effectuer une analyse rapidement et de formation dans une interface graphique simple, lors de la génération automatique de code R pour modifier ou obtenir des informations de l’ordinateur.


## <a name="next-steps"></a>Étapes suivantes
Voici comment vous pouvez continuer votre formation et exploration :

* La procédure pas à pas la [science de données sur la Machine virtuelle Linux données Science](machine-learning-data-science-linux-dsvm-walkthrough.md) vous montre comment effectuer plusieurs tâches communes de la science des données avec la VM de Science de données Linux configuré ici. 
* Explorez les différents outils de science de données sur la science de données VM en essayant des outils décrits dans cet article. Vous pouvez également exécuter *dsvm-plus-info* sur l’interpréteur de commandes sur l’ordinateur virtuel pour une introduction de base et des pointeurs pour plus d’informations sur les outils installés sur l’ordinateur virtuel.  
* Apprenez à créer des solutions analytiques de bout en bout de systématiquement en utilisant le [Processus d’équipe données scientifiques](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visitez la [Galerie d’Analytique Cortana](http://gallery.cortanaanalytics.com) machine learning et données analytique exemples qui utilisent la Suite Analytique Cortana.
