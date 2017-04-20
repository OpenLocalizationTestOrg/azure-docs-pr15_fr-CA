<properties
    pageTitle="Des analyses de sentiment Twitter en temps réel avec les flux de données Analytique | Microsoft Azure"
    description="Apprenez à utiliser les flux Analytique pour des analyses de sentiment Twitter en temps réel. Instructions de génération d’événements aux données d’un tableau de bord actif."
    keywords="analyse des tendances en temps réel twitter, analyse de sentiment, analyse de réseaux sociaux, exemple d’analyse de tendances"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analyse des médias sociaux : Twitter d’en temps réel des analyses de sentiment dans Azure flux Analytique

Apprenez à créer une solution d’analyse sentiment d’analytique de réseaux sociaux en mettant les événements en temps réel de Twitter Azure événement concentrateurs. Vous allez écrire une requête Analytique de flux Azure pour analyser les données. Vous allez ensuite stocker les résultats et consulter plus tard ou utilisez un tableau de bord et de la [BI de puissance](https://powerbi.com/) pour fournir des analyses en temps réel.

Outils de support de réseaux sociaux analytique aident les entreprises à comprendre les tendances rubriques, c'est-à-dire des sujets et des attitudes ayant un volume élevé de publications dans des réseaux sociaux. Analyse de sentiment, qui est également appelée *mining d’avis*, utilise des outils d’analytique de médias sociaux pour déterminer l’attitude d’un produit, idée et ainsi de suite. Analyse des tendances en temps réel Twitter est un bon exemple car le modèle d’abonnement hashtag vous permet d’écouter des mots clés spécifiques et développement de l’analyse de sentiment dans le flux.

## <a name="scenario-sentiment-analysis-in-real-time"></a>Scénario : Analyse de Sentiment en temps réel

Une société qui possède un site Web de médias est intéressé par l’obtention d’un avantage sur ses concurrents en intégrant le contenu du site est immédiatement pertinent pour ses lecteurs. La société utilise l’analyse des réseaux sociaux sur les rubriques relatives aux lecteurs en effectuant l’analyse en temps réel sentiment sur Twitter des données. En particulier, pour identifier les rubriques des tendances en temps réel sur Twitter, la société doit analytique en temps réel sur le volume de tweet et le sentiment de rubriques clés. Ainsi, en gros, le besoin est un sentiment analytique le moteur d’analyse basé sur ce média social d’alimentation.

## <a name="prerequisites"></a>Conditions préalables
-   Twitter de compte et un [jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
-   [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) à partir du centre de téléchargement Microsoft
-   Facultatif : Code Source client twitter à partir de [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Créer un concentrateur d’événements d’entrée et un groupe de consommateurs

L’exemple d’application génère les événements et les dirige vers une instance d’événement concentrateurs (concentrateur un événement, en abrégé). Concentrateurs d’événement Bus de service sont la méthode préférée de l’ingestion d’événement pour l’Analytique des flux de données. Consultez la documentation de concentrateurs d’événement dans [Le Bus des services](/documentation/services/service-bus/).

Utilisez les étapes suivantes pour créer un concentrateur d’événements.

1.  Dans le portail Azure, cliquez sur **Nouveau** > **Application SERVICES** > **BUS DE SERVICE** > **Concentrateur d’événements** > **Création rapide**et de fournir un nom, une région et un espace de noms nouveau ou existant.  
2.  Pour obtenir les meilleurs résultats, chaque tâche de flux de données Analytique doit lire à partir d’un seul groupe de consommateur d’événements concentrateurs. Nous vous guidera à travers le processus de création d’un groupe de consommateurs ultérieurement. Pour plus d’informations sur les groupes de consommateurs à la [vue d’ensemble des concentrateurs d’événement Azure](https://msdn.microsoft.com/library/azure/dn836025.aspx). Pour créer un groupe de consommateurs, allez au concentrateur événement nouvellement créé, cliquez sur l’onglet **Groupes de consommateurs** et cliquez sur **créer** en bas de la page, puis entrez un nom pour votre groupe de consommateurs.
3.  Pour accorder l’accès pour le concentrateur d’événements, nous devrons créer une stratégie d’accès partagé. Cliquez sur l’onglet **configurer** de concentrateur de votre événement.
4.  Sous **Stratégies d’accès partagé**, créez une nouvelle stratégie avec les autorisations de **gestion** .

    ![Partagé où vous pouvez créer une stratégie avec les autorisations Gérer les stratégies d’accès.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.  Cliquez sur **Enregistrer** en bas de la page.
6.  Accédez au **tableau de bord**, cliquez sur **Informations de connexion** en bas de la page, puis copier et enregistrer les informations de connexion. (Utilisez l’icône de copie qui apparaît sous l’icône de recherche.)

## <a name="configure-and-start-the-twitter-client-application"></a>Configurer et démarrer l’application de client Twitter

Nous avons fourni une application cliente qui se connecte aux données Twitter via l' [API de transmission en continu de Twitter](https://dev.twitter.com/streaming/overview) pour collecter des événements Tweet sur un ensemble paramétré de rubriques. L’outil open source [Sentiment140](http://help.sentiment140.com/) est utilisé pour assigner une valeur de sentiment à chaque tweet.

- 0 = négatif
- 2 = neutral
- 4 = positif

Ensuite, les événements de Tweet sont diffusés sur le concentrateur d’événements.  

Suivez ces étapes pour configurer l’application :

1.  [Téléchargement de la solution TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2.  Ouvrez TwitterClient.exe.config et remplacez de jetons Twitter qui ont les valeurs oauth_consumer_key, oauth_consumer_secret, oauth_token et oauth_token_secret.  

    [Étapes pour générer un jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

    Notez que vous devez créer une application vide pour générer un jeton.  
3.  Remplacez les valeurs de EventHubConnectionString et de EventHubName de TwitterClient.exe.config avec la chaîne de connexion et le nom de votre concentrateur d’événements. La chaîne de connexion que vous avez copié précédemment vous offre à la fois la chaîne de connexion et le nom de votre concentrateur d’événements, veillez à les séparer et placer chacune dans le champ approprié. Par exemple, considérez la chaîne de connexion suivante :

        Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

    Le fichier TwitterClient.exe.config doit contenir les paramètres comme dans l’exemple suivant :

        add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
        add key="EventHubName" value="yourhub"

    Il est important de noter que le texte « EntityPath = » est __pas__ apparaissent dans la valeur EventHubName.

4.  *Facultatif :* Ajuster les mots clés à rechercher.  Par défaut, cette application cherche « Azure, Skype, XBox, Microsoft, Seattle ».  Vous pouvez ajuster les valeurs de **twitter_keywords** dans TwitterClient.exe.config, si vous le souhaitez.
5.  Exécutez TwitterClient.exe pour démarrer votre application. Vous verrez les événements Tweet avec les valeurs **SentimentScore** , **créédans**et **rubrique**envoyées à votre concentrateur d’événements.

    ![Analyse de sentiment : valeurs SentimentScore envoyés à un concentrateur d’événements.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Créer une tâche de flux de données Analytique

Maintenant que les événements Tweet sont en flux continu en temps réel à partir de Twitter, nous pouvons configurer une tâche de flux de données Analytique pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Configurer une tâche de flux de données Analytique

1.  Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **Nouveau** > **DATA SERVICES** > **Analyse de flux** > **Création rapide**.
2.  Spécifiez les valeurs suivantes, puis cliquez sur **Créer un flux de travail ANALYTICS**:

    * **Nom de la tâche**: permet d’entrer un nom de travail.
    * **Région**: permet de sélectionner la région où vous souhaitez exécuter la tâche. Envisagez de placer le travail et le concentrateur d’événements dans la même région afin de garantir les meilleures performances et pour vous assurer que vous pas ce mode de paiement transférer des données entre les régions.
    * **Compte de stockage**: choisissez le compte de stockage Azure que vous souhaitez utiliser pour stocker les données de tous les travaux d’Analytique de flux qui s’exécutent dans cette région. Vous pouvez choisir un compte de stockage existant ou en créer un nouveau.

3.  Dans le volet gauche pour afficher les tâches de flux de données Analytique, cliquez sur **Analyse de flux de données** .  
    ![Icône de service Analytique de flux](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

    Le nouveau projet apparaît avec le statut **créé**. Notez que le bouton **Démarrer** en bas de la page est désactivé. Vous devez configurer la tâche entrée, de sortie et requête avant de commencer le travail.


### <a name="specify-job-input"></a>Spécifier une entrée de tâche
1.  Dans votre travail Analytique de flux de données, cliquez sur les **entrées** à partir du haut de la page, puis cliquez sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous guidera à travers un certain nombre d’étapes pour configurer votre entrée.
2.  Cliquez sur le **flux de données**, puis cliquez sur le bouton droit.
3.  Cliquez sur **Événement concentrateur**et puis cliquez sur le bouton de droite.
4.  Tapez ou sélectionnez les valeurs suivantes sur la troisième page :

    * **ALIAS d’entrée**: entrez un nom convivial pour cette tâche d’entrée, tel que *TwitterStream*. Notez que vous utiliserez ce nom dans la requête ultérieurement.
    **Concentrateur d’événements**: si l’abonnement de même que la tâche de flux de données Analytique est le concentrateur d’événements que vous avez créé, sélectionnez l’espace de noms est le concentrateur d’événements.

        Si votre concentrateur d’événements se trouve dans un autre abonnement, cliquez sur le **Concentrateur d’événements utilisation à partir d’un autre abonnement**et puis entrez manuellement les informations **d’Espace de noms de BUS DE SERVICE**, **Nom de concentrateur d’événements**, **Nom de concentrateur des événements à l’aide de la stratégie**, **Clé de concentrateur des événements à l’aide de la stratégie**et **Le nombre d’événements concentrateur PARTITION**.

    * **Nom de concentrateur d’événements**: sélectionnez le nom du concentrateur d’événements.

    * **Nom de concentrateur des événements à l’aide de la stratégie**: sélectionnez la stratégie de concentrateur d’événements que vous avez créé précédemment dans ce didacticiel.

    * **Groupe de consommateurs de concentrateur événement**: taper le nom du groupe de consommateur que vous avez créé précédemment dans ce didacticiel.
5.  Cliquez sur le bouton de droite.
6.  Spécifiez les valeurs suivantes :

    * **FORMAT d’événement sérialiseur**: JSON
    * **Codage**: UTF8

7.  Cliquez sur le bouton **Rechercher** pour ajouter cette source et de vérifier que flux Analytique peut se connecter au concentrateur de l’événement.

### <a name="specify-job-query"></a>Spécifiez la requête de travail

Analytique de flux prend en charge un modèle de requête simple et déclaratif qui décrit les transformations. Pour en savoir plus sur le langage, consultez la [Référence du langage de requête Azure flux Analytique](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ce didacticiel vous permettra de créer et de tester plusieurs requêtes sur les données de Twitter.

#### <a name="sample-data-input"></a>Exemple d’entrée de données

Pour valider votre requête sur les données de travail réel, vous pouvez utiliser la fonctionnalité **d’Exemples de données** pour extraire les événements à partir de votre flux de données et créer un fichier .json des événements pour le test.

1.  Sélectionnez votre entrée de concentrateur d’événements, puis cliquez sur **Exemple de données** au bas de la page.
2.  Dans la boîte de dialogue qui s’ouvre, spécifiez une **heure de début** pour commencer à collecter des données et la **durée** de la quantité de données supplémentaire à consommer.
3.  Cliquez sur le bouton **Détails** , puis cliquez sur le lien **Cliquez ici** pour télécharger et enregistrer le fichier .json généré.

#### <a name="pass-through-query"></a>Requête directe
Pour commencer, nous ferons une simple requête SQL directe qui projette de tous les champs d’un événement.

1.  Cliquez sur la **requête** en haut de la page de tâche de flux Analytique.
2.  Dans l’éditeur de code, remplacez le modèle de requête initiale par le suivant :

        SELECT * FROM TwitterStream

    Assurez-vous que le nom de la source d’entrée correspond au nom de l’entrée que vous avez spécifié précédemment.

3.  Dans l’éditeur de requête, cliquez sur **Test** .
4.  Accédez à votre exemple de fichier .json.
5.  Cliquez sur le bouton **Rechercher** et afficher les résultats sous la définition de la requête.

    ![Résultats affichés sous définition de la requête](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>Nombre de tweet par sujet : fenêtre qui s’écroulent avec agrégation

Pour comparer le nombre de mentions entre les rubriques, nous allons utiliser un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre de mentions par rubrique toutes les cinq secondes.

1.  Modifier la requête dans l’éditeur de code :

        SELECT System.Timestamp as Time, Topic, COUNT(*)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

    Cette requête utilise le mot clé **Par estampille** pour spécifier un champ d’horodatage dans la charge utile pour être utilisé dans le calcul temporel. Si ce champ n’a pas été spécifié, l’opération windowing serait réalisée à l’aide de l’heure de chaque événement arrivé au niveau du concentrateur de l’événement.  Pour en savoir plus dans la section « Arrivée Vs Application heure » de la [Référence de requête Analytique de flux de données](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Cette requête accède également à un horodatage pour la fin de chacune des fenêtres à l’aide de la propriété **System.Timestamp** .

2.  Dans l’éditeur de requête pour afficher les résultats de la requête, cliquez sur **réexécuter** .

#### <a name="identify-trending-topics-sliding-window"></a>Identifier les sujets des tendances : fenêtre coulissante

Pour identifier les tendances des rubriques, nous allons voir les rubriques qui passent par une valeur seuil pour mentionne dans un temps donné. Pour les besoins de ce didacticiel, nous allons vérifier les rubriques mentionnées à l’aide d’un [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx)plus de 20 fois au cours des cinq dernières secondes.

1.  Modifier la requête dans l’éditeur de code : sélectionnez le System.Timestamp en tant qu’heure, rubrique, nombre (*) comme le mentionne à partir TwitterStream TIMESTAMP par créédans groupe par SLIDINGWINDOW(s, 5), rubrique à compter (*) > 20

2.  Dans l’éditeur de requête pour afficher les résultats de la requête, cliquez sur **réexécuter** .

    ![Résultat de la requête fenêtre coulissante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>Nombre de mentions et sentiment : fenêtre qui s’écroulent avec agrégation
La requête finale que nous testerons utilise **TumblingWindow** pour obtenir le nombre de mentions, moyenne, minimum, maximum et écart-type de score de sentiment pour chaque rubrique toutes les cinq secondes.

1.  Modifier la requête dans l’éditeur de code :

        SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
        Max(SentimentScore), STDEV(SentimentScore)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.  Dans l’éditeur de requête pour afficher les résultats de la requête, cliquez sur **réexécuter** .
3.  Il s’agit de la requête que nous utiliserons pour notre tableau de bord.  Cliquez sur **Enregistrer** en bas de la page.


## <a name="create-output-sink"></a>Créer le récepteur de sortie

Maintenant que nous avons défini un flux d’événements, un concentrateur d’événements d’entrée pour recevoir des événements et une requête pour exécuter une transformation sur le flux, la dernière étape consiste à définir un récepteur de sortie pour la tâche.  Nous écrirons les événements tweet agrégées à partir de notre requête de travail pour le stockage des objets Blob Azure.  Vous pourriez pousser également les résultats de la base de données SQL Azure, le stockage Azure Table, ou événement concentrateurs, en fonction de votre application a besoin.

Utilisez les étapes suivantes pour créer un conteneur pour le stockage des objets Blob, si vous n’en avez pas déjà :

1.  Utiliser un compte de stockage existant ou en créer un nouveau compte de stockage en cliquant sur **Nouveau** > **DATA SERVICES** > **stockage** > **Création rapide**et puis suivez les instructions à l’écran.
2.  Sélectionnez le compte de stockage et cliquez sur **les conteneurs** en haut de la page, puis cliquez sur **Ajouter**.
3.  Spécifiez un **nom** pour votre conteneur et définir son **accès** au **Public Blob**.

## <a name="specify-job-output"></a>Spécifier une sortie de projet

1.  Dans votre travail Analytique de flux de données, cliquez sur **sortie** du haut de la page, puis cliquez sur **Ajouter une sortie**. La boîte de dialogue qui s’ouvre vous guidera tout au long des différentes étapes pour configurer votre sortie.
2.  Cliquez sur **Stockage BLOB**, puis cliquez sur le bouton droit.
3.  Tapez ou sélectionnez les valeurs suivantes sur la troisième page :

    * **ALIAS de sortie**: entrez un nom convivial pour la sortie de cette tâche.

    * **Abonnement**: si le stockage Blob que vous avez créé dans l’abonnement de même que la tâche de flux de données Analytique, cliquez sur **Utiliser le compte de stockage à partir de l’abonnement en cours**. Si votre système de stockage est dans un autre abonnement, cliquez sur **Utiliser le compte de stockage à partir d’un autre abonnement**et entrer manuellement les informations de **Compte de stockage**, **Clé de compte de stockage**et un **conteneur**.

    * **Compte de stockage**: sélectionnez le nom du compte de stockage.

    * **Conteneur**: sélectionnez le nom du conteneur.

    * **Préfixe de nom de fichier**: tapez un préfixe de fichier à utiliser lors de l’écriture de sortie de l’objet blob.

4.  Cliquez sur le bouton de droite.
5.  Spécifiez les valeurs suivantes :
    * **FORMAT d’événement sérialiseur**: JSON
    * **Codage**: UTF8
6.  Cliquez sur le bouton **Rechercher** pour ajouter cette source et de vérifier que flux Analytique peut se connecter avec succès pour le compte de stockage.

## <a name="start-job"></a>Tâche de début

Car une entrée de tâche, la requête et la sortie ont été spécifiés, nous sommes prêts à démarrer la tâche de flux de données Analytique.

1.  Dans le projet de **tableau de bord**, cliquez sur **Démarrer** au bas de la page.
2.  Dans la boîte de dialogue qui s’ouvre, cliquez sur **l’heure de début de la tâche**et puis cliquez sur le bouton **Rechercher** dans la partie inférieure de la boîte de dialogue. Le statut de la tâche passera à **démarrage** et que peu de temps **en cours d’exécution**.


## <a name="view-output-for-sentiment-analysis"></a>Sortie d’affichage pour l’analyse de sentiment

Une fois votre travail est en cours d’exécution et de traitement du flux Twitter en temps réel, choisissez comment vous souhaitez afficher la sortie pour l’analyse de sentiment. Utilisez un outil tel que [l’Explorateur de stockage Azure](https://azurestorageexplorer.codeplex.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) pour afficher la sortie de votre projet en temps réel. À partir de là, vous pouvez utiliser la [BI de puissance](https://powerbi.com/) pour étendre votre application pour inclure un tableau de bord personnalisé comme celle de la capture d’écran suivante.

![Analyse des médias sociaux : flux Analytique sentiment (exploration avis) sortie dans un tableau de bord d’alimentation.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
