<properties 
    pageTitle="Configurer l’ordinateur virtuel de Science données Microsoft | Microsoft Azure" 
    description="Configurez et créez un ordinateur virtuel de Science données sur Azure analytique et formation de l’ordinateur." 
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
    ms.date="09/07/2016" 
    ms.author="bradsev" />


# <a name="provision-the-microsoft-data-science-virtual-machine"></a>Configurer l’ordinateur virtuel de la Science des données de Microsoft

La Machine virtuelle Microsoft données Science est une image de Azure virtual machine (VM) déjà installé et configuré avec plusieurs outils les plus courants qui sont utilisés pour l’analytique des données et apprentissage automatique. Les outils inclus sont :

- Microsoft R Server Édition développeur
- Distribution de Python anaconda
- Jupyter ordinateur portable (R, noyaux de Python)
- Édition de communauté Visual Studio
- Bureau de BI de puissance
- SQL Server 2016 Developer Edition
- Outils d’apprentissage machine
    - [Shared Computer Toolkit de réseau informatique (CNTK)](https://github.com/Microsoft/CNTK): une profondeur de formation kit logiciel de Microsoft Research.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): un ordinateur rapide système prenant en charge des techniques d’allreduce en ligne, le hachage, réductions, learning2search, actif, de formation et d’apprentissage interactif.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): un outil offrant une implémentation rapide et précise arborescence amplifiées.
    - [Vibrer](http://rattle.togaware.com/) (la R analytique outil pour apprendre facilement) : un outil qui facilite la mise en route avec analytique des données et l’ordinateur R simple avec l’exploration de données basées sur une interface utilisateur graphique, de formation et de modélisation avec la génération de code automatique R.
    - [mxnet](https://github.com/dmlc/mxnet): un cadre de formation approfondie conçu pour l’efficacité et la flexibilité
- Bibliothèques dans R et les Python pour utilisent dans la formation de Machine d’Azure et d’autres services Azure
- GIT y compris Git Bash pour travailler avec les référentiels de code source, y compris les GitHub, Visual Studio Team Services
- Les ports Windows de plusieurs utilitaires très appréciés Linux de ligne de commande (y compris awk, sed, perl, grep, recherche, wget, curl etc.) accessible via l’invite de commande. 


Effectuant la science des données implique une itération sur une séquence de tâches : données de recherche, de chargement et de pré-traitement, création et test des modèles de déploiement de modèles destinés à la consommation dans des applications intelligentes. Les scientifiques de données utilisent une variété d’outils pour effectuer ces tâches. Il peut être beaucoup de temps à rechercher les versions appropriées du logiciel et puis de les télécharger et les installer. La Machine virtuelle Microsoft données Science peut faciliter cette charge en fournissant une image prête à l’emploi, qui peut être configurée sur Azure avec tous les outils populaires plusieurs préinstallés et configurés. 

La Machine virtuelle Microsoft données Science jump-starts votre projet analytique. Il vous permet de travailler sur des tâches dans différents langages, y compris les R, Python, SQL et C#. Visual Studio fournit une interface IDE pour développer et tester votre code qui est facile à utiliser. Le SDK Azure incluses dans la machine virtuelle vous permet de créer des applications à l’aide de divers services de plate-forme de nuage de Microsoft. 

Il n’y a pas de frais logiciels pour cette image de machine virtuelle de données scientifiques. Vous ne payez que pour les frais d’utilisation Azure qui dépend de la taille de la machine virtuelle que vous mettez en service. Vous trouverez plus de détails sur les frais de calcul dans la section de détails de tarification sur la page de [données Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) . 


## <a name="prerequisites"></a>Conditions préalables

Avant de pouvoir créer une Machine virtuelle de Microsoft données Science, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**: pour en obtenir une, consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Compte de stockage un Azure**: pour en créer une, reportez-vous à la section [Création d’un compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account). Du compte de stockage peut également être créé dans le cadre du processus de création de la machine virtuelle si vous ne souhaitez pas utiliser un compte existant.


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Créez votre ordinateur virtuel de Science données Microsoft

Voici la procédure pour créer une instance de la Machine virtuelle Microsoft données scientifiques :

1.  Accédez à la machine virtuelle mise en vente sur [Azure portal](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.   Cliquez sur le bouton **Création** en bas à prendre dans un Assistant. ![configurer-données-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.   L’Assistant permet de créer la Machine virtuelle Microsoft données Science requiert des **entrées** pour chacun des **cinq étapes** énumérées sur la droite de la figure. Voici les intrants nécessaires à la configuration de chacune de ces étapes :
    
    1.   **Notions de base**
        1.   **Nom**: nom de votre serveur de science de données vous sont en train de créer.
        2.   **Nom d’utilisateur**: id de connexion de compte Admin.
        3.   **Mot de passe**: mot de passe du compte administrateur.
        4.   **Abonnement**: Si vous avez plus d’un abonnement, sélectionnez celui sur lequel l’ordinateur doit être créé et facturé.
        5.   **Groupe de ressources**: vous pouvez créer un autre ou utiliser un groupe existant.
        6.   **Emplacement**: sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui comporte la plupart de vos données ou qui est plus proche de votre emplacement physique pour les accès réseau le plus rapide.
         
    2.   **Taille**: sélectionnez un des types de serveur qui répond à vos exigences fonctionnelles et les contraintes de coût. Vous pouvez obtenir le plus grand choix de tailles de machine virtuelle en sélectionnant « Afficher tout ».
    
    3.   **Paramètres**:
        1.   **Type de disque**: prime de choisir si vous préférez un lecteur à l’état solide (SSD), sinon cliquez sur « Standard ».
        2.   **Compte de stockage**: vous pouvez créer un nouveau compte de stockage Azure dans votre abonnement ou utiliser un existant dans le même *emplacement* a été choisi à l’étape de **Notions de base** de l’Assistant.
        3.   **Autres paramètres**: généralement vous venez d’utiliser les valeurs par défaut. Vous pouvez pointer sur le lien d’information de l’aide sur les champs spécifiques au cas où vous souhaiteriez envisagez d’utiliser les valeurs par défaut.

    4.   **Résumé**: Vérifiez que toutes les informations que vous avez entré sont correctes.
    5.   **Acheter**: cliquez sur **acheter** pour démarrer la mise en service. Un lien est fourni avec les termes de la transaction. La machine virtuelle n’a pas d’éventuels frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisi à l’étape de **taille** . 


>[AZURE.NOTE] La mise en service le diplôme environ 10 à 20 minutes. L’état de la mise en service est affiché sur le portail Azure.

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Comment faire pour accéder à la Machine virtuelle Microsoft données Science

Une fois que la machine virtuelle est créée, vous pouvez Bureau à distance à l’aide des informations de compte d’utilisateur que vous avez configuré dans la section précédente de la **base** . 

Une fois votre ordinateur virtuel est créé et mis en service, vous êtes prêt à commencer à utiliser les outils qui sont installés et configurés sur celui-ci. Il existe des mosaïques du menu Démarrer et les icônes du bureau de nombreux outils. 

## <a name="how-to-create-a-strong-password-on-the-jupyter-notebook-server"></a>Comment faire pour créer un mot de passe sur le serveur de carnet de Jupyter 

Pour créer votre propre mot de passe fort pour le serveur de bloc-notes Jupyter installé sur l’ordinateur, exécutez la commande suivante à partir d’une invite de commande sur l’ordinateur virtuel de données scientifiques.

    c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Choisissez un mot de passe lorsque vous y êtes invité.

Vous consultez le hachage de mot de passe dans le format « sha1:xxxxxx » dans la sortie. Copier ce hachage de mot de passe et de remplacer le hachage existant qui se trouve dans votre fichier de configuration d’ordinateur portable situé à : **C:\ProgramData\jupyter\jupyter_notebook_config.py** avec un nom de paramètre ***c.NotebookApp.password***.

Remplacer uniquement la partie (xxxxxx) de la valeur de hachage existant qui est entre guillemets. Les guillemets et le ***sha1 :*** préfixe de la valeur du paramètre les deux doivent être conservées.

Enfin, vous devez arrêter et redémarrer le serveur Jupyter, qui est en cours d’exécution sur l’ordinateur virtuel en tant que tâche planifiée windows appelée **Start_IPython_Notebook**. Si votre nouveau mot de passe après le redémarrage de cette tâche n’est pas accepté, essayez la mise à mort de tous les processus de python en cours d’exécution à partir du Gestionnaire des tâches, puis redémarrez la tâche planifiée. Alternativement, essayez de redémarrer l’ordinateur virtuel.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Outils installés sur la Machine virtuelle Microsoft données Science

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Édition développeur
Si vous souhaitez utiliser R pour votre analytique, la machine virtuelle a édition développeur Microsoft R Server installée. Microsoft R Server est une plate-forme d’analytique de largement déployables à l’échelle de l’entreprise en fonction de R qui est pris en charge, évolutive et sécurisée. Prise en charge des statistiques des données volumineuses, modélisation prédictive et apprentissage des fonctionnalités de l’ordinateur, R Server prend en charge la gamme complète des analytique – exploration, d’analyse, de visualisation et de modélisation. En utilisant et extension open source R, Microsoft R Server est entièrement compatible avec les scripts de R, les fonctions et les packages CRAN, pour analyser les données à l’échelle de l’entreprise. Il aborde également les limitations de mémoire de Open Source R en ajoutant le traitement parallèle et mémorisé en bloc de données. Cela vous permet d’exécuter analytique de données plus volumineuse que tout ce qui tient dans la mémoire principale.  Visual Studio Community Edition est inclus dans la machine virtuelle contient les outils R pour l’extension Visual Studio qui fournit un IDE complet pour l’utilisation de R. Vous pouvez également télécharger et utiliser d’autres IDE ainsi par exemple [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Pour le développement à l’aide de Python, distribution les Python Anaconda 2.7 et 3.5 a été installée. Cette distribution contient les Python de base ainsi qu’environ 300 des packages analytique plus populaires mathématiques, d’ingénierie et de données. Vous pouvez utiliser les outils de Python pour les commandes Visual Studio les (PTVS) qui est installé dans l’édition Visual Studio communautaire de 2015 ou l’un de l’IDE fourni avec Anaconda comme inactif ou Spyder. Vous pouvez lancer une recherche sur la barre de recherche (**Win** + **S** clé).

>[AZURE.NOTE] Pour pointer les Python outils pour Visual Studio à Anaconda Python 2.7 et 3.5, vous devez créer des environnements personnalisés pour chaque version. Pour définir ces chemins d’accès de l’environnement dans l’édition de communauté 2015 Visual Studio, accédez à **Outils** -> **Outils de Python** -> **Environnements de Python** , puis cliquez sur **+ personnalisé**. 

Anaconda Python 2.7 est installé sous C:\Anaconda et Anaconda Python 3.5 est installé sous c:\Anaconda\envs\py35. Pour obtenir la procédure détaillée, consultez la [documentation de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) . 

### <a name="jupyter-notebook"></a>Jupyter portable
Distribution de anaconda est également fourni avec un bloc-notes de Jupyter, un environnement de partage de code et analyse. Un serveur de bloc-notes Jupyter a été configuré avant avec les Python 2, 3 de Python et noyaux de R. Il existe une icône du bureau nommée « Jupyter bloc-notes à lancer le navigateur pour accéder au serveur de l’ordinateur portable. Si vous êtes sur la machine virtuelle via le Bureau à distance, vous pouvez également visiter [https://localhost:9999 /](https://localhost:9999/) pour accéder au serveur de bloc-notes Jupyter lorsque connecté à la machine virtuelle.
 
>[AZURE.NOTE] Continuer si vous obtenez tous les avertissements de certificat. 

Nous avons empaqueté des exemples d’agendas dans les Python et R. Les portables de Jupyter afficher comment travailler avec Microsoft R Server, SQL Server 2016 R Services (analytique dans base de données), Python et autres technologies d’Azure, une fois que vous vous connectez à Jupyter. Vous pouvez voir le lien vers les exemples sur la page d’accueil portable après que vous être authentifié dans le carnet de Jupyter en utilisant le mot de passe que vous avez créée à une étape précédente. 

### <a name="visual-studio-2015-community-edition"></a>Édition de 2015 Communauté Visual Studio
Édition de communauté Visual Studio installée sur l’ordinateur virtuel. Il s’agit d’une version gratuite de l’IDE populaires de Microsoft que vous pouvez utiliser à des fins d’évaluation et pour de petites équipes. Vous pouvez consulter les termes de licence [ici](https://www.visualstudio.com/support/legal/mt171547).  Ouvrez Visual Studio en double-cliquant sur l’icône du bureau ou du menu **Démarrer** . Vous pouvez également rechercher des programmes avec **Win** + **S** et saisissez « Visual Studio ». Une fois qu’il vous pouvez créer des projets dans les langages tels que C#, Python, R, node.js. Plug-ins sont installés également qui le rendent pratique de travailler avec des services Azure comme catalogue de données Azure, Azure HDInsight (Hadoop, commandé) et lac de données Azure. 

>[AZURE.NOTE] Vous pouvez obtenir un message indiquant que votre période d’évaluation a expiré. Entrez vos informations d’identification de compte Microsoft ou créer un nouveau compte gratuit pour accéder à l’édition de communauté de Visual Studio. 

### <a name="sql-server-2016-developer-edition"></a>Édition développeur de SQL Server 2016
Une version développeur de 2016 de SQL Server avec les Services de R pour s’exécuter dans base de données analytique est disponible sur la machine virtuelle. R Services fournissent une plate-forme pour développer et déployer des applications intelligentes. Pour créer les modèles et générer des prévisions pour vos données de SQL Server, vous pouvez utiliser le langage de R rich et puissant et de plusieurs packages à partir de la Communauté. Vous pouvez conserver analytique proche des données, car les Services de R (de bases de données) intègrent la langue R de SQL Server. Ceci élimine les coûts et les risques associés au transfert de données.

>[AZURE.NOTE] L’édition développeur de SQL Server 2016 utilisable uniquement pour des fins de test et de développement. Vous avez besoin d’une licence pour l’exécuter dans la production. 

Vous pouvez accéder au serveur SQL en lançant **SQL Server Management Studio**. Le nom de votre machine virtuelle est rempli comme nom de serveur. Utiliser l’authentification Windows lorsque connecté en tant que l’administrateur de Windows. Une fois que vous êtes dans SQL Server Management Studio vous pouvez créer d’autres utilisateurs, créer des bases de données, importer des données et exécuter des requêtes SQL. 

Pour activer l’analytique dans base de données à l’aide de Microsoft R, exécutez la commande suivante en tant que temps action dans management studio de SQL Server après la connexion en tant que l’administrateur du serveur. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure 
Plusieurs outils Azure sont installés sur l’ordinateur virtuel :

- Il existe un raccourci bureau pour accéder à la documentation du Kit de développement logiciel Azure. 
- **AzCopy**: permet de déplacer des données vers et depuis votre compte de stockage Microsoft Azure. Pour visualiser l’utilisation, tapez **Azcopy** à l’invite de commande pour afficher l’utilisation. 
- **Explorateur de stockage Microsoft Azure**: utilisé pour parcourir les objets que vous avez stockés dans vos données de compte de stockage Azure et transfert vers et depuis le stockage Azure. Vous pouvez tapez **Explorateur de stockage** dans Rechercher ou rechercher dans le menu Démarrer de Windows pour accéder à cet outil. 
- **Adlcopy**: permet de déplacer les données vers Azure données lac. Pour visualiser l’utilisation, tapez **adlcopy** dans une invite de commande. 
- **dtui**: permet de déplacer des données vers et à partir de DocumentDB d’Azure, une base de données NoSQL sur le nuage. À l’invite de commande, tapez **dtui** . 
- **Passerelle de gestion des données Microsoft**: permet de déplacer des données entre des sources de données en local et le nuage. Il est utilisé dans des outils tels que Azure Data Factory. 
- **Microsoft Azure Powershell**: un outil permettant de gérer vos ressources Azure dans le langage de script est également installé sur votre ordinateur virtuel de Powershell. 

###<a name="power-bi"></a>Alimentation BI

Pour vous aider à créer des tableaux de bord et des visualisations, le **Bureau de BI d’alimentation** a été installé. Utilisez cet outil pour extraire des données à partir de différentes sources, de créer vos tableaux de bord et les rapports et pour les publier sur le nuage. Pour plus d’informations, consultez le site de [l’Alimentation BI](http://powerbi.microsoft.com) . Vous pouvez trouver le bureau de puissance BI dans le menu Démarrer. 

>[AZURE.NOTE] Vous avez besoin d’un compte Office 365 à alimentation BI. 

## <a name="additional-microsoft-development-tools"></a>Autres outils de développement Microsoft
[**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) permet de découvrir et de télécharger d’autres outils de développement Microsoft. Il existe également un raccourci vers l’outil fourni sur le bureau de la Machine virtuelle de Microsoft données scientifiques.  

## <a name="important-directories-on-the-vm"></a>Répertoires importants sur la machine virtuelle

| Élément                          | Répertoire |
| ------------------------------| ---------------- |
|Configurations de serveur Jupyter portable | C:\ProgramData\jupyter |
|Répertoire des exemples de Jupyter portable| c:\dsvm\notebooks|
|Autres exemples | c:\dsvm\samples|
| Anaconda (par défaut : Python 2.7) | c:\Anaconda |
| Environnement de Python 3.5 anaconda | c:\Anaconda\envs\py35|
|Répertoire d’instance R serveur autonome (instance par défaut de R) | C:\Program Files\Microsoft SQL Server\130\R_SERVER |
| Répertoire d’instance de base de données serveur de R | C:\Program Files\Microsoft SQL Server\MSSQL13. MSSQLSERVER\R_SERVICES |
| Divers outils | c:\dsvm\tools|

>[AZURE.NOTE] Instances de Microsoft données Science Virtual Machine créé avant 1.5.0 (avant le 3 septembre 2016) utilisé une structure de répertoire légèrement différent de celui spécifié dans le tableau précédent. 

## <a name="next-steps"></a>Étapes suivantes
Voici quelques étapes pour continuer votre formation et l’exploration. 

* Explorez les différents outils de science de données sur la science de données VM en cliquant sur le menu Démarrer et en vérifiant les outils répertoriés dans le menu.
* Accédez à **C:\Program Files\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts** pour les échantillons à l’aide de la bibliothèque RevoScaleR R prenant en charge analytique des données à l’échelle de l’entreprise.  
* Lisez l’article intitulé : [10 choses à faire sur la science de données Machine virtuelle](http://aka.ms/dsvmtenthings)
* Apprenez à créer des solutions analytiques de bout en bout systématiquement en utilisant le [Processus d’équipe données scientifiques](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visitez la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com) machine learning et données analytique exemples qui utilisent l’Intelligence Suite de Cortana. Nous avons également une icône dans le menu **Démarrer** et sur le bureau de l’ordinateur virtuel à cette galerie.

