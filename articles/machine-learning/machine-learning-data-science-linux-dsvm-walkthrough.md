<properties 
    pageTitle="Science de données sur la Machine virtuelle Linux données Science | Microsoft Azure" 
    description="Comment effectuer plusieurs tâches de science de données communes avec la machine virtuelle Science de données Linux." 
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
    ms.date="09/12/2016" 
    ms.author="bradsev;paulsh" />


# <a name="data-science-on-the-linux-data-science-virtual-machine"></a>Science de données sur la Machine virtuelle Linux données Science

Cette procédure pas à pas vous montre comment effectuer plusieurs tâches de science de données communes avec la machine virtuelle Science de données Linux. La Machine virtuelle de Science données Linux (DSVM) est une image de machine virtuelle disponible sur Azure qui est préinstallé avec une collection d’outils couramment utilisés pour l’analytique des données et apprentissage automatique. Les composants logiciels clés sont détaillées dans la rubrique de la [disposition de la Machine virtuelle Linux données scientifiques](machine-learning-data-science-linux-dsvm-intro.md) . L’image de machine virtuelle facilite la mise en route effectuant la science des données en quelques minutes, sans avoir à installer et configurer individuellement chacun des outils. Vous pouvez facilement évoluer la machine virtuelle, si nécessaire et l’arrêter lorsqu’elles pas en cours d’utilisation. Cette ressource est donc élastique et rentable. 

Les tâches de science de données illustré dans cette procédure pas à pas suivre les étapes décrites dans le [Processus d’équipe données scientifiques](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Ce processus fournit une approche systématique de la science de données qui permet aux équipes de chercheurs de données pour collaborer efficacement sur le cycle de vie de développement d’applications intelligentes. Le processus de science de données prévoit également une infrastructure itérative science de données qui peut être suivie par un individu.

Nous analysons le dataset [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) dans cette procédure pas à pas. Il s’agit d’un ensemble de messages qui sont marqués comme spam ou jambon (ce qui signifie qu’ils ne sont pas des spams), et contient également des statistiques sur le contenu des e-mails. Les statistiques inclus sont abordées dans une section, mais la prochaine. 


## <a name="prerequisites"></a>Conditions préalables

Avant de pouvoir utiliser un ordinateur virtuel de Linux données Science, vous devez disposer des éléments suivants :

- Un **abonnement Azure**. Si vous n’en avez pas déjà, consultez [créer votre compte Azure gratuit dès aujourd'hui](https://azure.microsoft.com/free/).
- Une [**science de données Linux VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Pour plus d’informations sur la configuration de cet ordinateur virtuel, voir [fourniture de la Machine virtuelle Linux données scientifiques](machine-learning-data-science-linux-dsvm-intro.md). 
- [X2Go](http://wiki.x2go.org/doku.php) installé sur votre ordinateur et ouvert une session XFCE. Pour plus d’informations sur l’installation et la configuration d’un **client de X2Go**, reportez-vous à la section [installation et configuration du client de X2Go](machine-learning-data-science-linux-dsvm-intro.md#Installing-and-configuring-X2Go-client). 
- **Compte de AzureML**. Si vous n’avez pas encore, s’inscrire pour un nouveau à [page d’accueil de AzureML](https://studio.azureml.net/). Il existe un niveau d’utilisation libre pour vous aider à démarrer.


## <a name="download-the-spambase-dataset"></a>Télécharger le dataset spambase

Le groupe de données [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) est un ensemble relativement réduit de données qui contient des 4601 uniquement exemples. Il s’agit d’une taille pratique à utiliser lorsque vous montrez que quelques-unes des fonctionnalités clées de la machine virtuelle à la Science données telle qu’elle conserve les besoins en ressources modestes.

>[AZURE.NOTE] Cette procédure pas à pas a été créé sur un D2 v2 taille données Science Machine virtuelle Linux. Cette taille DSVM est capable de gérer les procédures décrites dans cette procédure pas à pas.

Si vous avez besoin de plus d’espace de stockage, vous pouvez créer des disques supplémentaires et les joindre à votre machine virtuelle. Ces disques utilisent stockage Azure permanent, afin que leurs données sont conservées même si le serveur est configuré en raison de la redimensionner ou est arrêté. Pour ajouter un disque et l’attacher à votre machine virtuelle, suivez les instructions de [Ajouter un disque à un ordinateur virtuel de Linux](../virtual-machines/virtual-machines-linux-add-disk.md). Ces étapes utilisent l’Interface de ligne de commande Azure (CLI Azure), qui est déjà installé sur le DSVM. Par conséquent, ces procédures peuvent être effectuées entièrement à partir de la machine virtuelle elle-même. Une autre option pour augmenter le stockage consiste à utiliser des [fichiers Azure](../storage/storage-how-to-use-files-linux.md).

Pour télécharger les données, ouvrez une fenêtre de terminal et exécutez la commande suivante :

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Le fichier téléchargé n’a pas une ligne d’en-tête, nous pouvons donc créer un autre fichier qui n’est pas un en-tête. Exécutez cette commande pour créer un fichier avec les en-têtes appropriés :

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Puis concaténer les deux fichiers avec la commande :

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Le groupe de données dispose de plusieurs types de statistiques sur chaque e-mail : 

- Colonnes tels que ***word\_freq\_WORD*** indiquer le pourcentage de mots dans le message qui correspond à *WORD*. Par exemple, si *word\_freq\_rendre* est 1, 1 % de tous les mots dans le message électronique ont été *apporter*. 
- Colonnes comme ***char\_freq\_CHAR*** indiquer le pourcentage de tous les caractères ont été *CHAR*dans l’e-mail. 
- ***capital\_exécuter\_longueur\_plus longue*** est la longueur la plus importante d’une séquence de lettres en majuscules. 
- ***capital\_exécuter\_longueur\_moyenne*** est la durée moyenne de toutes les séquences de lettres en majuscules. 
- ***capital\_exécuter\_longueur\_total*** est la longueur totale de toutes les séquences de lettres en majuscules. 
- ***spam*** indique si l’e-mail a été considéré comme du spam ou non (1 = spam, 0 = non-spam).


## <a name="explore-the-dataset-with-microsoft-r-open"></a>Explorer le groupe de données avec Microsoft R ouvert

Nous allons examiner les données et effectuez un ordinateur de base de formation avec R. La Science de données machine virtuelle est fourni avec [Microsoft R ouvrir](https://mran.revolutionanalytics.com/open/) préalablement installé. Les bibliothèques de mathématiques multithread dans cette version de R offrent de meilleures performances que les versions monothread différents. Microsoft R ouvrir fournit également reproductibilité à l’aide d’une capture instantanée du référentiel package CRAN.

Pour obtenir des copies des exemples de code utilisés dans cette procédure pas à pas, cloner le référentiel **Azure Machine-formation-données-Science** à l’aide de git, qui est déjà installé sur l’ordinateur virtuel. À partir de la ligne de commande git, exécutez :

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Ouvrez une fenêtre de terminal et démarrer une nouvelle session de R avec la console interactive R.

>[AZURE.NOTE] Vous pouvez également utiliser RStudio pour les procédures suivantes. Pour installer RStudio, exécutez cette commande dans un terminal :`./Desktop/DSVM\ tools/installRStudio.sh`

Pour importer les données et définir l’environnement, exécutez :

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Pour afficher le résumé des statistiques relatives à chaque colonne :

    summary(data)

Pour obtenir une autre vue des données :

    str(data)

Cela vous indique le type de chaque variable et le premier nombre de valeurs dans le groupe de données. 

La colonne de *spam* a été lue sous la forme d’un entier, mais il est en fait un par catégorie variable (ou facteurs). Pour définir son type :

    data$spam <- as.factor(data$spam)

Pour effectuer une analyse exploratoire, utilisez le package [ggplot2](http://ggplot2.org/) , une bibliothèque de graphique les plus courants pour R qui est déjà installé sur l’ordinateur virtuel. Notez, à partir des données récapitulatives affichées précédemment, que nous avons résumé des statistiques sur la fréquence du caractère point d’exclamation. Nous allons tracer ces fréquences ici avec les commandes suivantes :

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Dans la mesure où la barre de zéro est l’inclinaison de la placette, nous allons la supprimer :

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Il y a une densité supérieure à 1 qui semble intéressant de non triviale. Nous allons voir que ces données :

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Puis la fractionner en ham vs de spam :

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Ces exemples doivent vous permettre de faire des placettes similaires des autres colonnes pour Explorer les données contenues dans les.


## <a name="train-and-test-an-ml-model"></a>Formation et test d’un modèle de ML

Maintenant nous allons former deux modèles de formation des machines de classer les e-mails dans le groupe de données que le conteneur soit étendue ou ham. Nous former un modèle d’arborescence de décision et un modèle de forêt aléatoire dans cette section et ensuite tester leur exactitude de leurs prévisions. 

>[AZURE.NOTE] Le package rpart (partitionnement récursif et Regression Trees) utilisé dans le code suivant est déjà installé sur l’ordinateur virtuel Science de données.


Tout d’abord, nous allons scinder le groupe de données en jeux de formation et de test :

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Puis créez une arborescence de décision pour classer les e-mails.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Voici le résultat :

![1](./media/machine-learning-data-science-linux-dsvm-walkthrough/decision-tree.png)

Pour déterminer comment il effectue sur l’ensemble d’apprentissage, utilisez le code suivant :

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Pour déterminer comment il effectue sur l’ensemble de test :

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Essayons également un modèle de forêt aléatoire. Forêts aléatoires former une multitude d’arborescences de décision et de sortie d’une classe qui est le mode des classifications à partir de toutes les arborescences de décision. Ils fournissent un apprentissage approche comme ils corriger la tendance d’un modèle d’arborescence de décision pour overfit la formation d’un groupe de données de l’ordinateur plus puissant. 

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Déployer un modèle sur Azure ML

[Machine Azure Studio de formation](https://studio.azureml.net/) (AzureML) est un service en nuage qui facilite la génération et le déploiement des modèles de prévision analytique. Parmi les fonctionnalités intéressantes de AzureML est sa capacité à publier n’importe quelle fonction de R sous la forme d’un service web. Le package AzureML R rend le déploiement facile à réaliser à partir de votre session R sur le DSVM. 

Pour déployer le code d’arborescence de décision à partir de la section précédente, vous devez vous connecter à un Studio de formation de Machine Azure. Vous avez besoin de votre ID de l’espace de travail et un jeton d’autorisation sigh dans. Pour rechercher ces valeurs et initialiser les variables d’AzureML avec eux :

Sélectionnez **paramètres** dans le menu de gauche. Notez votre **ID de l’espace de travail**. ![2](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-id.png)

Sélectionnez **Les jetons d’autorisations** à partir du menu généraux et notez votre **Jeton de principal d’autorisation**. ![3](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-token.png)

Charger le package **AzureML** , puis définissez les valeurs des variables avec votre ID de jeton et espace de travail dans votre session R sur le DSVM :


    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Nous allons simplifier le modèle pour faciliter cette démonstration à mettre en œuvre. Choisissez les trois variables dans l’arbre de décision plus proche de la racine et créer une nouvelle arborescence avec uniquement les trois variables :

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Nous avons besoin d’une fonction de prévision qui prend les fonctionnalités comme entrée et retourne les valeurs prévues :

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publier la fonction predictSpam à AzureML à l’aide de la fonction **publishWebService** : 

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Cette fonction utilise la fonction **predictSpam** crée un service web appelé **spamWebService** avec les sorties et les entrées définies et renvoie des informations sur le nouveau point de terminaison.

Afficher les détails du service web publié, y compris son point de terminaison API et accéder aux clés avec la commande :

    spamWebService[[2]]

Essayer le premier 10 lignes du test la valeur :

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Utilisez les autres outils disponibles

Les sections suivantes montrent comment utiliser certains des outils installés sur l’ordinateur virtuel Science de données Linux. Voici la liste des outils présentés :

- XGBoost
- Python
- Jupyterhub
- Hochet
- SQL PostgreSQL & écureuil
- Entrepôt de données de SQL Server


## <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) est un outil qui fournit une implémentation de l’arborescence d’augmentation rapide et précise.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost peut également appeler à partir de python ou une ligne de commande.

## <a name="python"></a>Python

Pour le développement à l’aide de Python, les distributions les Python Anaconda 2.7 et 3.5 ont été installées dans le DSVM. 

>[AZURE.NOTE] La distribution de Anaconda inclut [Condas](http://conda.pydata.org/docs/index.html), qui peut être utilisé pour créer des environnements personnalisés pour les Python qui ont des versions différentes ou des packages installés dans les.

Nous allons lire dans certains du groupe de données spambase et classer les e-mails avec les machines à vecteurs de support dans la scikit-en savoir plus :

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Pour effectuer des prévisions :

    clf.predict(X.ix[0:20, :])

Pour montrer comment publier un point de terminaison AzureML, créons un modèle plus simple trois variables comme nous l’avons fait lorsque nous avons publié le modèle de R précédemment. 

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Pour publier le modèle de AzureML :

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

>[AZURE.NOTE] Ceci est uniquement disponible pour les python 2.7 et n’est pas encore pris en charge sur 3.5. Exécutez **/anaconda/bin/python2.7**.


## <a name="jupyterhub"></a>Jupyterhub

La distribution de Anaconda dans le DSVM est livré avec un bloc-notes de Jupyter, un environnement multiplates-formes pour partager les analyse et code Python, R ou Julia. Le bloc-notes de Jupyter est accessible via JupyterHub. Vous vous connectez à l’aide de votre nom d’utilisateur Linux local et le mot de passe à ***https://\<nom VM DNS ou l’adresse IP\>: 8000 /***. Tous les fichiers de configuration pour JupyterHub sont trouvent dans le répertoire **/etc/jupyterhub**.

Plusieurs exemples d’agendas sont déjà installés sur l’ordinateur virtuel :

- Consultez [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) d’un ordinateur portable Python d’exemple.
- Consultez [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) pour un bloc-notes de **R** exemple.
- Consultez [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) pour un autre ordinateur portable les **Python** exemple.

>[AZURE.NOTE] La langue de Julia est également disponible à partir de la ligne de commande sur l’ordinateur virtuel Science de données Linux.


## <a name="rattle"></a>Hochet

[Vibrer](https://cran.r-project.org/web/packages/rattle/index.html) (la R analytique outil pour apprendre facilement) est un outil graphique de R pour le data mining. Il possède une interface intuitive qui permet de charger, Explorer et transformer les données de build et évaluer des modèles.  L’article [vibrer : A des données de Mining GUI pour R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fournit une procédure pas à pas qui illustre ses fonctionnalités.

Installez et démarrez Hochet avec les commandes suivantes :

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

>[AZURE.NOTE] Installation n’est pas requise sur le DSVM. Mais Hochet peut vous inviter à installer des packages supplémentaires lors de son chargement.

Hochet utilise une interface basée sur un onglet. La plupart des onglets correspondre aux étapes du [Processus de Science des données](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), telles que le chargement de données ou l’exploration. Le processus de la science des données s’enchaîne de gauche à droite des onglets. Mais le dernier onglet contient un journal des commandes R exécuté par Hochet. 


Pour charger et configurer le groupe de données :

- Pour charger le fichier, cliquez sur l’onglet **données** , puis 
- Choisissez le sélecteur en regard du **nom de fichier** , **spambaseHeaders.data**. 
- Pour charger le fichier. Cliquez sur **exécuter** dans la rangée de boutons supérieure. Vous devez voir un résumé de chaque colonne, y compris son type de données identifié, qu’il s’agisse d’une entrée, une cible ou autre type de variable et le nombre de valeurs uniques.
- Hochet a correctement identifié la colonne du **spam** comme la cible. Sélectionnez la colonne du spam, puis définissez le **Type de données cible** pour **Categoric**.

Pour Explorer les données : 

- Sélectionnez l’onglet **Explorer** . 
- Cliquez sur **Résumé**, puis **exécuter**, pour afficher des informations sur les types de variables et des statistiques de résumé. 
- Pour afficher d’autres types de statistiques relatives à chaque variable, sélectionnez autres options, comme la **Description** ou les **Notions de base**.

L’onglet **Explorer** vous permet également de générer de nombreux tracés ingénieuses. Pour tracer un histogramme des données :


- Sélectionnez **Distributions**.
- Vérifiez **l’histogramme** pour **word_freq_remove** et **word_freq_you**.
- Sélectionnez **exécuter**. Vous devez voir les deux tracés de densité dans une seule fenêtre de graphe, où il est clair que le mot « vous » apparaît beaucoup plus fréquemment dans les e-mails de « supprimer ».

Les placettes de corrélation sont également intéressants. Pour en créer un :


- Cliquez sur **corrélation** comme **Type**, puis 
- Sélectionnez **exécuter**. 
- Hochet vous avertit qu’il recommande un maximum de 40 variables. Sélectionnez **Oui** pour afficher le tracé. 

Il y a des corrélations intéressantes qui proviennent des : « technologie » est en corrélation avec « HP » et « laboratoires », par exemple. Il est également fortement en corrélation avec « 650 », car le code de la zone des donneurs de groupe de données est de 650.

Les valeurs numériques pour les corrélations entre les mots sont disponibles dans la fenêtre d’exploration. Il est intéressant de note, par exemple, que la « technologie » est en corrélation négative avec « votre » et « argent ».

Hochet peut transformer le groupe de données pour gérer les problèmes courants. Par exemple, il vous permet de redimensionner des fonctionnalités, imputer des valeurs manquantes, gérer des valeurs aberrantes et supprimer les variables ou les observations et les données manquantes. Hochet peut également identifier des règles d’association entre des observations ou des variables. Ces onglets sont hors de portée pour cette procédure pas à pas d’introduction.

Hochet peut effectuer également une analyse de cluster. Nous allons exclure certaines fonctionnalités pour faciliter la lecture de la sortie. Sous l’onglet **données** , cliquez sur Suivant pour chacune des variables, à l’exception de ces dix éléments **Ignorer** :

- word_freq_hp
- word_freq_technology
- word_freq_george
- word_freq_remove
- word_freq_your
- word_freq_dollar
- word_freq_money
- capital_run_length_longest
- word_freq_business
- courrier indésirable

Puis accédez à l’onglet de **Cluster** , sélectionnez **KMeans**et définir le *nombre de clusters* à 4. Puis **exécuter**. Les résultats sont affichés dans la fenêtre Sortie. Un cluster a haute fréquence de « george » et « hp » et est probablement un e-mail légitime.

Pour créer un modèle d’apprentissage d’arborescence de décision simple machine : 

- Sélectionnez l’onglet **modèle** , 
- Cliquez sur **arborescence** comme **Type**. 
- Cliquez sur **exécuter** pour afficher l’arborescence sous forme de texte dans la fenêtre Sortie. 
- Cliquez sur le bouton **dessiner** pour afficher une version graphique. Cela est très similaire à l’arborescence, que nous avons obtenus précédemment à l’aide de *rpart*.

Parmi les fonctionnalités intéressantes de Hochet est sa capacité à exécuter plusieurs méthodes d’apprentissage machine et de les évaluer rapidement. Voici la procédure :

- Cliquez sur **tout** pour le **Type**. 
- Sélectionnez **exécuter**. 
- Une fois terminé, vous pouvez cliquez sur un seul **Type**, comme **SVM**et afficher les résultats. 
- Vous pouvez également comparer les performances des modèles de la validation à l’aide de l’onglet **évaluation** . Par exemple, la sélection de la **Matrice d’erreur** montre la matrice de confusion, erreur globale et d’erreur de classe moyenne pour chaque modèle sur l’ensemble de validation. 
- Vous pouvez aussi tracer des courbes ROC, effectuer une analyse de sensibilité et autres types d’évaluations de modèle.

Une fois que vous avez terminé la construction de modèles, sélectionnez l’onglet **journal** pour afficher le code R exécuté par Hochet pendant votre session. Vous pouvez sélectionner le bouton **Exporter** pour l’enregistrer. 

>[AZURE.NOTE] Il existe un bogue dans la version actuelle de Hochet. Pour modifier le script, ou utiliser pour répéter les étapes plus tard, vous devez insérer un caractère # avant *d’exporter ce journal...* dans le texte du journal. 


## <a name="postgresql--squirrel-sql"></a>SQL PostgreSQL & écureuil

Le DSVM est livré avec PostgreSQL installé. PostgreSQL est une base de données relationnelle sophistiquée, open source. Cette section indique comment charger notre dataset spam PostgreSQL et ensuite de la requête.

Avant de pouvoir charger les données, vous devez autoriser l’authentification par mot de passe à partir de l’hôte local. À l’invite de commande :

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Au bas du fichier de configuration sont plusieurs lignes qui détaillent les connexions autorisées :

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Modifier la ligne « IPv4 de connexions locales » pour utiliser md5 au lieu de l’identification, donc nous pouvons ouvrir une session en utilisant un nom d’utilisateur et le mot de passe :

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Et redémarrez le service postgres :

    sudo systemctl restart postgresql

Pour lancer psql, un terminal interactif pour PostgreSQL, que l’utilisateur postgres intégré, exécutez la commande suivante à partir d’une invite de commandes :

    sudo -u postgres psql

Créer un nouveau compte d’utilisateur, en utilisant le même nom d’utilisateur que le compte de Linux, vous êtes actuellement connecté en tant qu’et attribuez un mot de passe :

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Puis ouvrez une session psql en tant qu’utilisateur :

    psql

Et importer les données dans une nouvelle base de données :

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Maintenant, nous allons étudier les données et à exécuter des requêtes à l’aide de **Écureuil SQL**, un outil graphique qui vous permet d’interagissent avec les bases de données via un pilote JDBC.

Pour commencer, lancez écureuil SQL à partir du menu d’Applications. Pour configurer le pilote :

- Sélectionnez **Windows**, puis les **pilotes d’affichage**. 
- Avec le bouton droit sur **PostgreSQL** et sélectionnez **Modifier le pilote**. 
- Sélectionnez le **chemin d’accès de classe supplémentaires**, puis sur **Ajouter**. 
- Entrez ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** pour le **nom de fichier** et 
- Cliquez sur **Ouvrir**.
- Choisissez les pilotes de la liste, puis sélectionnez **org.postgresql.Driver** dans le **Nom de la classe**, puis **OK**.

Pour configurer la connexion au serveur local :
 
- Sélectionnez **Windows**, puis **Afficher les alias.** 
- Choisissez le **+** bouton pour créer un nouvel alias. 
- Nom de *base de données de Spam*, cliquez sur dans la liste déroulante **pilote** **PostgreSQL** .
- L’URL la valeur *jdbc:postgresql://localhost/spam*. 
- Entrez votre *nom d’utilisateur* et le *mot de passe*. 
- Cliquez sur **OK**. 
- Pour ouvrir la fenêtre de **connexion** , double-cliquez sur l’alias de la ***base de données de Spam*** . 
- Sélectionnez **se connecter**.

Pour exécuter des requêtes :

- Sélectionnez l’onglet **SQL** .
- Entrez une requête simple telle que `SELECT * from data;` dans la zone de texte requête en haut de l’onglet SQL. 
- Appuyez sur **Ctrl + Entrée** pour l’exécuter. Par défaut, écureuil SQL renvoie les 100 premières lignes de votre requête. 

Il existe beaucoup plus de requêtes que vous pouvez exécuter pour explorer ces données. Par exemple, comment la fréquence de la mot *rendre* diffère-t-il entre le spam et ham ?

    SELECT avg(word_freq_make), spam from data group by spam;

Ou bien, quelles sont les caractéristiques des e-mails qui contiennent fréquemment des *3d*?

    SELECT * from data order by word_freq_3d desc;

La plupart des e-mails qui ont une grande occurrence de *3d* sont apparemment spam, il peut être une fonction très utile pour la création d’un modèle de prévision pour classer les e-mails.

Si vous souhaitez effectuer l’apprentissage de l’ordinateur avec les données stockées dans une base de données PostgreSQL, envisagez d’utiliser [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>Entrepôt de données de SQL Server

Entrepôt de données SQL Azure est une nuage cloisonnée de base de données capable de traiter de très gros volumes de données, relationnelles et non relationnelles. Pour plus d’informations, consultez [Quel est le magasin de données SQL Azure ?](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Pour vous connecter à l’entrepôt de données et créer la table, exécutez la commande suivante à partir d’une invite de commande :

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Puis à l’invite sqlcmd :

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copier des données avec bcp :

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

>[AZURE.NOTE] Les fins de ligne dans le fichier téléchargé sont de type Windows, mais bcp attend de style UNIX, afin que nous devons indiquer que bcp avec l’indicateur - r.

Et de requête de sqlcmd :

    select top 10 spam, char_freq_dollar from spam;
    GO

Vous pouvez également interroger avec écureuil SQL. Suivez les étapes similaires pour PostgreSQL, en utilisant le pilote JDBC de Microsoft Microsoft MSSQL Server, se trouvent dans ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble des rubriques qui vous guident à travers les tâches qui constituent le processus de données scientifiques dans Azure, consultez [Processus d’équipe données scientifiques](http://aka.ms/datascienceprocess).

Pour une description des autres procédures pas à pas à-bout illustrant les étapes du processus de Science de données équipe pour des scénarios spécifiques, consultez [procédures pas à pas du processus d’équipe données scientifiques](data-science-process-walkthroughs.md). Les procédures pas à pas illustrent également comment combiner les outils nuage et sur site et des services dans un flux de travail ou d’un pipeline pour créer une application intelligente.

