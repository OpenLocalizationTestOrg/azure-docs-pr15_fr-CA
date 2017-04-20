<properties
    pageTitle="Mise en route de flux Analytique : détection des fraudes en temps réel | Microsoft Azure"
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

Apprenez à créer une solution de bout en bout pour la détection des fraudes en temps réel avec Azure flux Analytique. Importer des événements dans un concentrateur d’événements Azure, écrire des requêtes d’agrégation ou d’alerte Analytique de flux de données et envoyer les résultats à un récepteur de sortie de comprendre les données avec traitement en temps réel. Détection d’anomalie en temps réel des télécommunications est couverte, mais la technique de l’exemple est aussi bien pour les autres types de détection de fraude, tels que les scénarios de vol de carte de crédit ou d’identité.

Analytique de flux de données est un service entièrement géré de traitement de l’événement complexe de faible latence, hautement disponible et évolutif sur la diffusion en continu de données dans le nuage. Pour plus d’informations, consultez [Introduction aux Azure flux Analytique](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scénario : Télécommunications et SIM la détection des fraudes en temps réel

Une société de télécommunications comprend un volume important de données pour les appels entrants. La société a besoin de ce qui suit à partir de ses données :
* Comparer des données sur un volume facile à gérer et obtenir des conseils sur l’utilisation du client dans le temps et les régions géographiques.
* Détecter les fraudes SIM (plusieurs appels provenant de la même identité en même temps, mais dans des emplacements différents géographiquement) en temps réel afin qu’ils peuvent facilement répondre en informant les clients ou d’arrêt de service.

Dans les scénarios Internet des objets (IoT) canoniques il est une tonne de données télémétriques ou capteur générée – et souhaitent les agréger ou d’alerte sur les anomalies en temps réel.

## <a name="prerequisites"></a>Conditions préalables

- Téléchargez [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) à partir du centre de téléchargement Microsoft 
- Facultatif : Source de code du Générateur d’événements à partir de [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator)

## <a name="create-an-azure-event-hubs-input-and-consumer-group"></a>Créer une entrée de concentrateurs d’événement Azure et un groupe de consommateurs

L’exemple d’application génère les événements et les dirige vers une instance de concentrateur de l’événement pour le traitement en temps réel. Concentrateurs d’événements de Bus de service sont la méthode préférée de l’ingestion d’événement pour l’Analytique de flux et vous pouvez en savoir plus sur les concentrateurs d’événement dans la [documentation du Bus des services Azure](/documentation/services/service-bus/).

Pour créer un concentrateur d’événements :

1.  Dans [Azure portal](https://manage.windowsazure.com/) , cliquez sur **Nouveau** > **Application Services** > **Service Bus** > **Concentrateur d’événements** > **Création rapide**. Fournir un nom, une région et un espace de noms nouveau ou existant pour créer un nouveau concentrateur d’événements.  
2.  Pour obtenir les meilleurs résultats, lisez chaque tâche de flux de données Analytique d’un seul groupe de consommateurs de concentrateur d’événement. Nous vous guiderons à travers le processus de création d’un groupe de consommateurs ci-dessous, et vous pouvez [en savoir plus sur les groupes de consommateurs](https://msdn.microsoft.com/library/azure/dn836025.aspx). Pour créer un groupe de consommateurs, naviguez vers le concentrateur d’événements nouvellement créé et cliquez sur l’onglet **Groupes de consommateurs** , puis cliquez sur **créer** en bas de la page et fournir un nom pour votre groupe de consommateurs.
3.  Pour accorder l’accès au concentrateur d’événements, que nous devons créer une stratégie d’accès partagé.  Cliquez sur l’onglet **configurer** de votre événement de supervision.
4.  Sous **Stratégies d’accès partagé**, créez une nouvelle stratégie avec les autorisations de **gestion** .

    ![Partagé où vous pouvez créer une stratégie avec les autorisations Gérer les stratégies d’accès.](./media/stream-analytics-get-started/stream-ananlytics-shared-access-policies.png)

5.  Cliquez sur **Enregistrer** en bas de la page.
6.  Accédez au **tableau de bord** et cliquez sur **Informations de connexion** en bas de la page, puis copier et enregistrer les informations de connexion.

## <a name="configure-and-start-event-generator-application"></a>Configurer et lancer l’application Générateur d’événements

Nous avons fourni une application cliente qui génère des métadonnées d’appel entrant exemple et poussez-le à concentrateur d’événements. Suivez les étapes ci-dessous pour configurer cette application.  

1.  Téléchargez le [fichier de TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip). Puis décompressez-le dans un répertoire.

    **Remarque**: Windows peut bloquer le fichier zip téléchargé. Cliquez avec le bouton droit sur le fichier et sélectionnez Propriétés. Si le message « ce fichier provenance d’un autre ordinateur et peut être bloqué pour protéger cet ordinateur ». puis cochez la case « Débloquer » et cliquez sur Appliquer dans le fichier zip.

2.  Remplacez les valeurs Microsoft.ServiceBus.ConnectionString et EventHubName dans les **telcodatagen.exe.config** avec votre nom et la chaîne de connexion de concentrateur d’événements.

    **Remarque**: la chaîne de connexion copiées à partir des emplacements portails Azure, le nom de la connexion à la fin. Veillez à supprimer le « ; EntityPath =<value>« à partir de la touche Ajouter = champ.

3.  Démarrez l’application. L’utilisation est la suivante :

   telcodatagen.exe [#NumCDRsPerHour] [probabilité de fraude de carte SIM] [#DurationHours]

L’exemple suivant génère des événements 1000 avec une probabilité de 20 pour cent de la fraude au cours de 2 heures.

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


## <a name="create-stream-analytics-job"></a>Créer la tâche de flux de données Analytique
Maintenant que nous disposons d’un flux d’événements de télécommunications, nous pouvons configurer une tâche de flux de données Analytique pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Configurer une tâche de flux de données Analytique

1.  Dans le portail Azure, cliquez sur **Nouveau > Data Services > flux Analytique > Création rapide**.
2.  Spécifiez les valeurs suivantes, puis cliquez sur **Créer un flux de travail Analytique**:

    * **Nom de la tâche**: permet d’entrer un nom de travail.

    * **Région**: permet de sélectionner la région où vous souhaitez exécuter la tâche. Envisagez de placer le travail et le concentrateur d’événements dans la même région afin de garantir les meilleures performances et pour vous assurer que vous pas ce mode de paiement transférer des données entre les régions.

    * **Compte de stockage**: choisissez le compte de stockage Azure que vous souhaitez utiliser pour stocker les données d’analyse pour tous les travaux d’Analytique de flux de données en cours d’exécution dans cette région. Vous pouvez choisir un compte de stockage existant ou en créer un nouveau.

3.  Dans le volet gauche pour afficher les tâches de flux de données Analytique, cliquez sur **Flux Analytique** .

    ![Icône de service Analytique de flux](./media/stream-analytics-get-started/stream-analytics-service-icon.png)

4.  Le nouveau projet apparaît avec le statut **créé**. Notez que le bouton **Démarrer** en bas de la page est désactivé. Vous devez configurer la tâche entrée, de sortie et requête avant de commencer le travail.

### <a name="specify-job-input"></a>Spécifier une entrée de tâche
1.  Dans votre tâche de flux de données Analytique cliquez sur les **entrées** à partir du haut de la page, puis cliquez sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous guidera à travers un certain nombre d’étapes pour configurer votre entrée.
2.  Sélectionnez le **flux de données**, puis cliquez sur le bouton droit.
3.  Sélectionnez le **Concentrateur d’événements**et puis cliquez sur le bouton de droite.
4.  Tapez ou sélectionnez les valeurs suivantes sur la troisième page :

    * **Alias d’entrée**: entrez un nom convivial pour cette tâche d’entrée tel que *CallStream*. Notez que vous utiliserez ce nom dans la requête ultérieurement.
    * **Concentrateur d’événements**: si l’abonnement de même que la tâche de flux de données Analytique est le concentrateur d’événement que vous avez créé, sélectionnez l’espace de noms est le concentrateur d’événements.

    Si votre concentrateur d’événements se trouve dans un autre abonnement, sélectionnez le **Concentrateur d’événements utilisation à partir d’un autre abonnement** et entrez manuellement les informations de **Namespace de Bus de Service**, **Nom de concentrateur d’événements**, **Nom de concentrateur des événements à l’aide de la stratégie**, **Clé de concentrateur des événements à l’aide de la stratégie**et **Le nombre d’événements concentrateur Partition**.

    * **Nom de concentrateur d’événements**: sélectionnez le nom de l’événement de supervision.

    * **Nom de concentrateur des événements à l’aide de la stratégie**: sélectionnez la stratégie d’événement-hub créée précédemment dans ce didacticiel.

    * **Groupe de consommateur d’événements concentrateur**: tapez le groupe de consommateurs créé précédemment dans ce didacticiel.
5.  Cliquez sur le bouton de droite.
6.  Spécifiez les valeurs suivantes :

    * **Format d’événement sérialiseur**: JSON
    * **Codage**: UTF8
7.  Cliquez sur le bouton de vérification pour ajouter cette source et de vérifier que flux Analytique peut se connecter au concentrateur de l’événement.

### <a name="specify-job-query"></a>Spécifiez la requête de travail

Analytique de flux prend en charge un modèle de requête simple et déclaratif pour décrire les transformations de traitement en temps réel. Pour en savoir plus sur le langage, consultez la [Référence du langage de requête Azure flux Analytique](https://msdn.microsoft.com/library/dn834998.aspx). Ce didacticiel vous permettra de créer et de tester plusieurs requêtes sur votre flux de données en temps réel des données de l’appel.

#### <a name="optional-sample-input-data"></a>Facultative : Exemple de données d’entrée
Pour valider votre requête sur les données de travail réel, vous pouvez utiliser la fonctionnalité **d’Exemples de données** pour extraire les événements à partir de votre flux de données et créer un. Fichier JSON des événements pour le test.  Les étapes suivantes indiquent comment effectuer cette opération, et nous avons également fourni un exemple de fichier [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) pour des fins de test.

1.  Sélectionnez votre entrée d’événement concentrateur et cliquez sur **Exemple de données** au bas de la page.
2.  Dans la boîte de dialogue qui s’affiche, spécifiez une **Heure de début** pour démarrer la collecte de données et une **durée** pour la quantité de données supplémentaire à consommer.
3.  Cliquez sur le bouton de vérification pour démarrer des données d’échantillonnage à partir de l’entrée.  Il peut prendre une minute ou deux pour le fichier de données devant être produites.  Lorsque le processus est terminé, cliquez sur **Détails** et téléchargez et enregistrez la. Fichier JSON qui est généré.

    ![Téléchargez et enregistrez les données traitées dans un fichier JSON](./media/stream-analytics-get-started/stream-analytics-download-save-json-file.png)

#### <a name="passthrough-query"></a>Requête passthrough

Si vous souhaitez archiver tous les événements, vous pouvez utiliser une requête passthrough pour lire tous les champs dans la charge utile de l’événement ou le message. Commencez par le faire une requête simple passthrough qui projette de tous les champs dans un événement.

1.  Cliquez sur la **requête** à partir du haut de la page de tâche de flux Analytique.
2.  Dans l’éditeur de code, ajoutez ce qui suit :

        SELECT * FROM CallStream

    > Assurez-vous que le nom de la source d’entrée correspond au nom de l’entrée que vous avez spécifié précédemment.

3.  Dans l’éditeur de requête, cliquez sur **Test** .
4.  Fournissez un fichier test, soit celui que vous avez créé à l’aide de la procédure précédente ou utilisez [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json).
5.  Cliquez sur le bouton de vérification et consultez les résultats affichés ci-dessous la définition de requête.

    ![Définition de requête](./media/stream-analytics-get-started/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Projection de colonne

Nous allons maintenant raccourcir les champs renvoyés à un ensemble plus petit.

1.  Modifier la requête dans l’éditeur de code :

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Dans l’éditeur de requête pour afficher les résultats de la requête, cliquez sur **réexécuter** .

    ![Sortie de l’éditeur de requête.](./media/stream-analytics-get-started/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Nombre d’entrant appelle par région : fenêtre qui s’écroulent avec agrégation

Pour comparer la quantité que les appels entrants par région nous Tirez un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre d’appels entrants regroupés par SwitchNum toutes les 5 secondes.

1.  Modifier la requête dans l’éditeur de code :

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Cette requête utilise le mot clé **Par estampille** pour spécifier un champ d’horodatage dans la charge utile pour être utilisé dans le calcul temporel. Si ce champ n’a pas été spécifié, l’opération windowing serait réalisée à l’aide de l’heure d’arrivée de chaque événement à concentrateur d’événements. Reportez-vous à la section [« Arrivée Vs Application heure » dans le flux de données Analytique de référence du langage de requête](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Notez que vous pouvez accéder à un horodatage pour la fin de chacune des fenêtres à l’aide de la propriété **System.Timestamp** .

2.  Dans l’éditeur de requête pour afficher les résultats de la requête, cliquez sur **réexécuter** .

    ![Résultats de la requête pour Timestand par](./media/stream-analytics-get-started/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Détection de fraude de carte SIM avec une jointure réflexive

Pour identifier une utilisation potentiellement frauduleuse, nous allons pour les appels provenant d’un même membre, mais dans des emplacements différents en moins de 5 secondes.  Nous [jointure](https://msdn.microsoft.com/library/azure/dn835026.aspx) le flux d’événements d’appel avec lui-même pour vérifier pour ces cas.

1.  Modifier la requête dans l’éditeur de code :

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Dans l’éditeur de requête pour afficher les résultats de la requête, cliquez sur **réexécuter** .

    ![Résultats de la requête d’une jointure](./media/stream-analytics-get-started/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Créer le récepteur de sortie

Maintenant que nous avons défini un flux d’événements, un concentrateur d’événements d’entrée pour recevoir des événements et une requête pour exécuter une transformation sur le flux, la dernière étape consiste à définir un récepteur de sortie pour la tâche.  Nous allons écrire des événements pour comportement frauduleux pour le stockage des objets Blob.

Suivez les étapes ci-dessous pour créer un conteneur pour le stockage des objets Blob, si vous n’en avez pas déjà.

1.  Utiliser un compte de stockage existant ou en créer un nouveau compte de stockage en cliquant sur **Nouveau > DATA SERVICES > stockage > Création rapide** et suivez les instructions.
2.  Sélectionnez le compte de stockage et cliquez sur **les conteneurs** en haut de la page, puis cliquez sur **Ajouter**.
3.  Spécifiez un **nom** pour votre conteneur et définir son **accès** au Public Blob.

## <a name="specify-job-output"></a>Spécifier une sortie de projet

1.  Dans votre tâche de flux de données Analytique sur la **sortie** à partir du haut de la page, puis cliquez sur **Ajouter une sortie**. La boîte de dialogue qui s’ouvre vous guidera à travers un certain nombre d’étapes à suivre pour configurer votre sortie.
2.  Sélectionnez **Stockage BLOB**, puis cliquez sur le bouton droit.
3.  Tapez ou sélectionnez les valeurs suivantes sur la troisième page :

    * **ALIAS de sortie**: entrez un nom convivial pour la sortie de cette tâche.
    * **Abonnement**: si l’abonnement de même que la tâche de flux de données Analytique est le stockage Blob que vous avez créé, sélectionnez **Utiliser le compte de stockage à partir de l’abonnement en cours**. Si votre système de stockage est dans un autre abonnement, sélectionnez **Utiliser le compte de stockage à partir d’un autre abonnement** et entrez manuellement les informations de **Compte de stockage**, **Clé de compte de stockage**, **conteneur**.
    * **Compte de stockage**: sélectionnez le nom du compte de stockage.
    * **Conteneur**: sélectionnez le nom du conteneur.
    * **Préfixe de nom de fichier**: entrez un préfixe de fichier à utiliser lors de l’écriture de sortie de l’objet blob.

4.  Cliquez sur le bouton de droite.
5.  Spécifiez les valeurs suivantes :

    * **FORMAT d’événement sérialiseur**: JSON
    * **Codage**: UTF8

6.  Cliquez sur le bouton de vérification pour ajouter cette source et de vérifier que flux Analytique peut se connecter avec succès pour le compte de stockage.

## <a name="start-job-for-real-time-processing"></a>Démarrer le travail de traitement en temps réel

Dans la mesure où une entrée de tâche, la requête et la sortie ont été spécifiés, nous sommes prêts démarrer la tâche de flux de données Analytique pour la détection des fraudes en temps réel.

1.  Dans le projet de **tableau de bord**, cliquez sur **Démarrer** au bas de la page.
2.  Dans la boîte de dialogue qui s’affiche, sélectionnez **l’heure de début de la tâche** et puis cliquez sur le bouton de vérification sur la partie inférieure de la boîte de dialogue. Le statut de la tâche passera à **démarrage** et passera bientôt à **l’exécution**.

## <a name="view-fraud-detection-output"></a>Sortie de détection de fraude de vue

Utiliser un outil tel que [l’Explorateur de stockage Azure](https://azurestorageexplorer.codeplex.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) pour afficher les événements frauduleux comme elles sont écrites dans la sortie en temps réel.  

![Détection de la fraude : événements frauduleux affichés en temps réel](./media/stream-analytics-get-started/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
