<properties
    pageTitle="Analytique de flux : La détection des fraudes en temps réel | Microsoft Azure"
    description="Apprenez à créer une solution de détection de fraude en temps réel avec flux Analytique. Utilisez un concentrateur de l’événement pour le traitement des événements en temps réel."
    keywords="détection des anomalies, la détection des fraudes, détection d’anomalie en temps réel"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok" />



# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Mise en route à l’aide d’Analytique de flux Azure : détection des fraudes en temps réel

Apprenez à créer une solution de bout en bout pour la détection des fraudes en temps réel avec Azure flux Analytique. Importer des événements dans les concentrateurs d’événement Azure, écrire des requêtes d’agrégation ou d’alerte Analytique de flux de données et envoyer les résultats à un récepteur de sortie pour obtenir des idées sur des données avec traitement en temps réel. Détection d’anomalie en temps réel des télécommunications est expliquée, mais la technique de l’exemple est aussi bien pour les autres types de détection de fraude, tels que les scénarios de vol de carte de crédit ou d’identité.

Flux de données Analytique est un service entièrement géré qui fournit le traitement de l’événement de faible latence, disponibilité, évolutif, complexes sur la diffusion en continu de données dans le nuage. Pour plus d’informations, consultez [Introduction aux Azure flux Analytique](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scénario : Télécommunications et SIM la détection des fraudes en temps réel

Une société de télécommunications comprend un volume important de données pour les appels entrants. La société a besoin de ce qui suit à partir de ses données :

* Réduire les données à un montant plus faciles à gérer et obtenir des conseils sur l’utilisation du client dans le temps et dans les régions géographiques.
* Détecter une fraude de carte SIM (plusieurs appels provenant de la même identité en même temps, mais dans des emplacements différents géographiquement) en temps réel afin que la société puisse répondre facilement en informer les clients ou d’arrêt de service.

Les scénarios Internet des objets (IoT) canoniques ont une tonne de télémétrie ou des données provenant des capteurs. Les clients veulent recevoir les alertes concernant les anomalies en temps réel ou de regrouper les données.

## <a name="prerequisites"></a>Conditions préalables

- Téléchargez [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) à partir du centre de téléchargement Microsoft
- Facultatif : Source de code du Générateur d’événements à partir de [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Créer groupe d’entrée et de consommateur de concentrateurs d’événement Azure

L’exemple d’application génère les événements et les dirige vers une instance de concentrateurs d’événement pour le traitement en temps réel. Concentrateurs d’événements de Bus de service sont la méthode préférée de l’ingestion d’événement pour l’Analytique des flux de données. Pour plus d’informations sur les concentrateurs d’événement dans la [documentation du Bus des services Azure](/documentation/services/service-bus/).

Pour créer un concentrateur d’événements :

1.  Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **Nouveau** > **Application SERVICES** > **SERVICE BUS** > **Concentrateur d’événements** > **Création rapide**. Fournir un nom, une région et un espace de noms nouveau ou existant pour créer un nouveau concentrateur d’événements.  
2.  Pour obtenir les meilleurs résultats, chaque tâche de flux de données Analytique doit lire à partir d’un seul hub consommateur groupe d’événements. Nous vous guidera à travers le processus de création d’un groupe de consommateurs ultérieurement. [En savoir plus sur les groupes de consommateurs](https://msdn.microsoft.com/library/azure/dn836025.aspx). Pour créer un groupe de consommateurs, allez au concentrateur événement nouvellement créé, cliquez sur l’onglet **Groupes de consommateurs** et cliquez sur **créer** en bas de la page, puis entrez un nom pour votre groupe de consommateurs.
3.  Pour accorder l’accès pour le concentrateur d’événements, nous devrons créer une stratégie d’accès partagé. Cliquez sur l’onglet **configurer** de concentrateur de votre événement.
4.  Sous **Stratégies d’accès partagé**, créez une nouvelle stratégie qui possède des autorisations de **gestion** .

    ![Partagé où vous pouvez créer une stratégie avec les autorisations Gérer les stratégies d’accès.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.  Cliquez sur **Enregistrer** en bas de la page.
6.  Accédez au **tableau de bord**, cliquez sur **Informations de connexion** en bas de la page, puis copier et enregistrer les informations de connexion.

## <a name="configure-and-start-the-event-generator-application"></a>Configurer et démarrer l’application Générateur d’événements

Nous avons fourni une application cliente qui génère des métadonnées d’appel entrant exemple et envoyer aux concentrateurs de l’événement. Utilisez les étapes suivantes pour configurer cette application.  

1.  Téléchargez le [fichier de TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)et décompressez-le dans un répertoire.

    > [AZURE.NOTE] Windows peut bloquer le fichier zip téléchargé. Cliquez sur le fichier, puis sélectionnez **Propriétés**. Si vous voyez le message « ce fichier provenance d’un autre ordinateur et peut être bloqué pour protéger cet ordinateur », activez la case **Débloquer** , puis cliquez sur Appliquer dans le fichier zip.

2.  Remplacez les valeurs Microsoft.ServiceBus.ConnectionString et EventHubName dans les telcodatagen.exe.config avec votre chaîne de connexion concentrateur d’événement et le nom.

    La chaîne de connexion que vous avez copié à partir du portail Azure place le nom de la connexion à la fin. Veillez à supprimer « ; EntityPath =<value>» à partir de la « Ajouter clé = » champ.

3.  Démarrez l’application. L’utilisation est la suivante :

    telcodatagen.exe [#NumCDRsPerHour] [probabilité de fraude de carte SIM] [#DurationHours]

L’exemple suivant génère 1 000 événements avec une probabilité de 20 pour cent de la fraude au cours de deux heures.

    telcodatagen.exe 1000 .2 2

Vous verrez les enregistrements envoyés à votre concentrateur d’événements. Certains champs clés que nous utiliserons dans cette application de détection de fraude en temps réel sont définies ici :

| Enregistrement | Définition |
| ------------- | ------------- |
| CallrecTime | Horodatage de l’heure de début d’appel. |
| SwitchNum | Commutateur téléphonique utilisé pour la connexion de l’appel. |
| CallingNum | Numéro de téléphone de l’appelant. |
| CallingIMSI | Identité de l’abonné Mobile international (IMSI).  Identificateur unique de l’appelant. |
| CalledNum | Numéro de téléphone du destinataire de l’appel. |
| CalledIMSI | Identité de l’abonné Mobile international (IMSI).  Identificateur unique du destinataire de l’appel. |


## <a name="create-a-stream-analytics-job"></a>Créer une tâche de flux de données Analytique
Maintenant que nous disposons d’un flux d’événements de télécommunications, nous pouvons configurer une tâche de flux de données Analytique pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Configurer une tâche de flux de données Analytique

1.  Dans le portail Azure, cliquez sur **Nouveau** > **DATA SERVICES** > **Analyse de flux** > **Création rapide**.
2.  Spécifiez les valeurs suivantes, puis cliquez sur **Créer un flux de travail ANALYTICS**:

    * **Nom de la tâche**: permet d’entrer un nom de travail.

    * **Région**: permet de sélectionner la région où vous souhaitez exécuter la tâche. Envisagez de placer le travail et le concentrateur d’événements dans la même région afin de garantir les meilleures performances et pour vous assurer que vous pas ce mode de paiement transférer des données entre les régions.

    * **Compte de stockage**: choisissez le compte de stockage Azure que vous souhaitez utiliser pour stocker les données de tous les travaux d’Analytique de flux qui s’exécutent dans cette région. Vous avez l’option à choisir un compte de stockage existant ou créez-en un nouveau.

3.  Dans le volet gauche pour afficher les tâches de flux de données Analytique, cliquez sur **Analyse de flux de données** .

    ![Icône de service Analytique de flux](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

    Le nouveau projet apparaît avec le statut **créé**. Notez que le bouton **Démarrer** en bas de la page est désactivé. Vous devez configurer la tâche entrée, de sortie et requête avant de commencer le travail.

### <a name="specify-job-input"></a>Spécifier une entrée de tâche
1.  Dans votre travail Analytique de flux de données, cliquez sur les **entrées** en haut de la page, puis cliquez sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous guidera tout au long des différentes étapes pour configurer votre entrée.
2.  Cliquez sur le **flux de données**, puis cliquez sur le bouton droit.
3.  Cliquez sur **Événement concentrateur**et puis cliquez sur le bouton de droite.
4.  Tapez ou sélectionnez les valeurs suivantes sur la troisième page :

    * **Entrée d’ALIAS**: entrez un nom convivial, tel que *CallStream*, pour cette tâche. Notez que vous utiliserez ce nom dans la requête ultérieurement.

    * **Concentrateur d’événements**: si l’abonnement de même que la tâche de flux de données Analytique est le concentrateur d’événements que vous avez créé, sélectionnez l’espace de noms est le concentrateur d’événements.

        Si votre concentrateur d’événements se trouve dans un autre abonnement, sélectionnez le **Concentrateur d’événements utilisation à partir d’un autre abonnement**et puis entrez manuellement les informations **d’Espace de noms de BUS DE SERVICE**, **Nom de concentrateur d’événements**, **Nom de concentrateur des événements à l’aide de la stratégie**, **Clé de concentrateur des événements à l’aide de la stratégie**et **Le nombre d’événements concentrateur PARTITION**.

    * **Nom de concentrateur d’événements**: sélectionnez le nom du concentrateur d’événements.

    * **Nom de concentrateur des événements à l’aide de la stratégie**: sélectionnez la stratégie de concentrateur d’événements que vous avez créé précédemment dans ce didacticiel.

    * **Groupe de consommateurs de concentrateur événement**: taper le nom du groupe de consommateur que vous avez créé précédemment dans ce didacticiel.

5.  Cliquez sur le bouton de droite.
6.  Spécifiez les valeurs suivantes :

    * **FORMAT d’événement sérialiseur**: JSON
    * **Codage**: UTF8
7.  Cliquez sur le bouton **Rechercher** pour ajouter cette source et de vérifier que flux Analytique peut se connecter au concentrateur de l’événement.

### <a name="specify-job-query"></a>Spécifiez la requête de travail

Analytique de flux prend en charge un modèle de requête simple et déclaratif qui décrit les transformations de traitement en temps réel. Pour en savoir plus sur le langage, consultez la [Référence du langage de requête Azure flux Analytique](https://msdn.microsoft.com/library/dn834998.aspx). Ce didacticiel vous permettra de créer et de tester plusieurs requêtes sur votre flux de données en temps réel des données de l’appel.

#### <a name="optional-sample-input-data"></a>Facultative : Exemple de données d’entrée
Pour valider votre requête sur les données de travail réel, vous pouvez utiliser la fonctionnalité **d’Exemples de données** pour extraire les événements à partir de votre flux de données et créer un. Fichier JSON des événements pour le test.  Les étapes suivantes indiquent comment effectuer cette opération. Nous avons également fourni un exemple de fichier [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) pour des fins de test.

1.  Sélectionnez votre entrée de concentrateur d’événements, puis cliquez sur **Exemple de données** au bas de la page.
2.  Dans la boîte de dialogue qui s’ouvre, spécifiez une **heure de début** pour commencer à collecter des données et la **durée** de la quantité de données supplémentaire à consommer.
3.  Cliquez sur le bouton **Rechercher** pour démarrer des données d’échantillonnage à partir de l’entrée.  Il peut prendre une minute ou deux pour le fichier de données devant être produites.  Lorsque le processus est terminé, cliquez sur **Détails**, le téléchargement généré. JSON de fichier et l’enregistrer.

    ![Téléchargez et enregistrez les données traitées dans un fichier JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Requête directe

Si vous souhaitez archiver tous les événements, vous pouvez utiliser une requête SQL directe pour lire tous les champs dans la charge utile de l’événement ou le message. Pour démarrer, effectuez une requête par passage de données simple qui projette de tous les champs d’un événement.

1.  Cliquez sur la **requête** à partir du haut de la page de tâche de flux Analytique.
2.  Dans l’éditeur de code, ajoutez ce qui suit :

        SELECT * FROM CallStream

    > [AZURE.IMPORTANT] Assurez-vous que le nom de la source d’entrée correspond au nom de l’entrée que vous avez spécifié précédemment.

3.  Dans l’éditeur de requête, cliquez sur **Test** .
4.  Fournir un fichier de test. Utilisez un que vous avez créé à l’aide de la procédure précédente, ou [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5.  Cliquez sur le bouton **Vérifier** et consultez les résultats affichés ci-dessous la définition de requête.

    ![Définition de requête](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Projection de colonne

Nous allons maintenant réduire les champs renvoyés un plus petit ensemble.

1.  Modifier la requête dans l’éditeur de code :

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Dans l’éditeur de requête pour afficher les résultats de la requête, cliquez sur **réexécuter** .

    ![Sortie de l’éditeur de requête.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Nombre d’entrant appelle par région : fenêtre qui s’écroulent avec agrégation

Pour comparer le nombre d’appels entrants par région, nous allons utiliser un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre d’appels entrants regroupés par **SwitchNum** toutes les cinq secondes.

1.  Modifier la requête dans l’éditeur de code :

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Cette requête utilise le mot clé **Par estampille** pour spécifier un champ d’horodatage dans la charge utile pour être utilisé dans le calcul temporel. Si ce champ n’a pas été spécifié, l’opération windowing serait réalisée à l’aide de l’heure de chaque événement arrivé au niveau du concentrateur de l’événement. Reportez-vous à la section [« Arrivée Vs Application heure » dans le flux de données Analytique de référence du langage de requête](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Notez que vous pouvez accéder à un horodatage pour la fin de chacune des fenêtres à l’aide de la propriété **System.Timestamp** .

2.  Dans l’éditeur de requête pour afficher les résultats de la requête, cliquez sur **réexécuter** .

    ![Résultats de la requête pour Timestand par](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Détection de fraude de carte SIM avec une jointure réflexive

Pour identifier une utilisation potentiellement frauduleuse, nous allons voir des appels qui proviennent de l’utilisateur, mais dans des emplacements différents en moins de 5 secondes.  Nous [jointure](https://msdn.microsoft.com/library/azure/dn835026.aspx) le flux d’événements d’appel avec lui-même pour vérifier pour ces cas.

1.  Modifier la requête dans l’éditeur de code :

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Dans l’éditeur de requête pour afficher les résultats de la requête, cliquez sur **réexécuter** .

    ![Résultats de la requête d’une jointure](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Créer le récepteur de sortie

Maintenant que nous avons défini un flux d’événements, un concentrateur d’événements d’entrée pour recevoir des événements et une requête pour exécuter une transformation sur le flux, la dernière étape consiste à définir un récepteur de sortie pour la tâche. Nous allons écrire des événements pour le comportement frauduleux pour le stockage des objets Blob Azure.

Utilisez les étapes suivantes pour créer un conteneur pour le stockage des objets Blob, si vous n’en avez pas déjà.

1.  Utiliser un compte de stockage existant ou en créer un nouveau compte de stockage en cliquant sur **Nouveau > DATA SERVICES > stockage > Création rapide**et suivez les instructions.
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

## <a name="start-job-for-real-time-processing"></a>Démarrer le travail de traitement en temps réel

Car une entrée de tâche, la requête et la sortie ont été spécifiés, nous sommes prêts à démarrer le travail Analytique de flux pour la détection des fraudes en temps réel.

1.  Dans le projet de **tableau de bord**, cliquez sur **Démarrer** au bas de la page.
2.  Dans la boîte de dialogue qui s’ouvre, cliquez sur **l’heure de début de la tâche**et puis cliquez sur le bouton **Rechercher** dans la partie inférieure de la boîte de dialogue. Le statut de la tâche passera à **démarrage** et que peu de temps **en cours d’exécution**.

## <a name="view-fraud-detection-output"></a>Sortie de détection de fraude de vue

Utiliser un outil tel que [l’Explorateur de stockage Azure](http://storageexplorer.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) pour afficher les événements frauduleux comme elles sont écrites dans la sortie en temps réel.  

![Détection de la fraude : événements frauduleux affichés en temps réel](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
