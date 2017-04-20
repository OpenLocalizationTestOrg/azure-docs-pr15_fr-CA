<properties
   pageTitle="Flux de données à partir de flux de données Analytique dans le magasin de données lac | Azure"
   description="Utiliser Azure flux Analytique pour répartir des données dans le magasin de LAC de données Azure"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Diffuser les données à partir du Blob de stockage Azure dans le magasin de données lac l’utilisation Analytique des flux de données Azure

Dans cet article, vous apprendrez à utiliser le lac Azure Data Store sous la forme d’une sortie pour une opération Analytique de flux Azure. Cet article illustre un scénario simple qui lit des données à partir d’un blob Azure Storage (entrée) et écrit les données dans le magasin du lac de données (sortie).

>[AZURE.NOTE] À ce stade, création et configuration de magasin de données lac sorties pour les flux de données Analytique est pris en charge uniquement dans le [Portail classique d’Azure](https://manage.windowsazure.com). Par conséquent, certaines parties de ce didacticiel utilise le portail classique d’Azure.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Activer votre abonnement Azure** pour données lac magasin Public Preview. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).

- **Compte de stockage azure**. Vous allez utiliser un conteneur de blob à partir de ce compte d’entrée de données pour un projet de flux de données Analytique. Pour ce didacticiel, supposons que vous créez un compte de stockage nommé **datalakestoreasa** et un conteneur dans le compte appelé **datalakestoreasacontainer**. Une fois que vous avez créé le conteneur, télécharger un exemple de fichier de données à celle-ci. Vous pouvez obtenir un exemple de fichier de données à partir du [Référentiel Git lac Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Vous pouvez utiliser divers clients, tels que l' [Explorateur de stockage Azure](http://storageexplorer.com/), pour télécharger des données à un conteneur d’objet blob.

    >[AZURE.NOTE] Si vous créez le compte à partir du portail d’Azure, assurez-vous que vous le créez avec le modèle **classique** de déploiement. Cela garantit que le compte de stockage sont accessibles à partir du portail classique d’Azure, parce que c’est ce qui nous permet de créer une tâche de flux de données Analytique. Pour obtenir des instructions sur la création d’un compte de stockage à partir du portail Azure en utilisant le déploiement classique, consultez [Création d’un compte de stockage Azure](../storage/storage-create-storage-account/#create-a-storage-account).
    >
    > Ou bien, vous pouvez créer un compte de stockage à partir du portail classique d’Azure.

- **Compte de banque de LAC de données azure**. Suivez les instructions de la section [mise en route de magasin de LAC de données Azure via le portail d’Azure](data-lake-store-get-started-portal.md).  


## <a name="create-a-stream-analytics-job"></a>Créer un flux de travail d’Analytique

Vous démarrez en créant un travail Analytique de flux de données qui inclut une source en entrée et une destination de sortie. Pour ce didacticiel, la source est un conteneur blob Azure, et la destination est le magasin de données lac.

1. Ouvrez une session sur [Azure Portal classique](https://manage.windowsazure.com).

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau**, **Les Services de données** **Analytique de flux**, **Création rapide**. Fournir les valeurs comme indiqué ci-dessous, puis cliquez sur **Créer un flux de travail Analytique**.

    ![Créer un flux de travail d’Analytique] (./media/data-lake-store-stream-analytics/create.job.png "Créer une tâche de flux de données Analytique")

## <a name="create-a-blob-input-for-the-job"></a>Créer une entrée de Blob pour la tâche

1. Ouvrez la page de la tâche de flux de données Analytique et cliquez sur l’onglet **d’entrées** , puis cliquez sur **Ajouter une entrée** pour lancer l’Assistant.

2. Sur la page **Ajouter une entrée à votre travail** , sélectionnez le **flux de données**et puis cliquez sur la flèche vers l’avant.

    ![Ajouter une entrée à votre travail] (./media/data-lake-store-stream-analytics/create.input.1.png "Ajouter une entrée à votre travail")

3. Sur la page **Ajouter un flux de données à votre projet** , sélectionnez le **stockage Blob**et puis cliquez sur la flèche vers l’avant.

    ![Ajouter un flux de données au projet] (./media/data-lake-store-stream-analytics/create.input.2.png "Ajouter un flux de données au projet")

4. Dans la page **paramètres du stockage Blob** , fournir des détails pour le stockage des objets blob que vous allez utiliser comme source de données d’entrée.

    ![Fournir des paramètres de stockage de l’objet blob] (./media/data-lake-store-stream-analytics/create.input.3.png "Fournir des paramètres de stockage de l’objet blob")

    * **Un alias d’entrée d’entrée**. Il s’agit d’un nom unique que vous fournissez pour le travail de saisie.
    * **Sélectionnez un compte de stockage**. Assurez-vous que le compte de stockage est dans la même région que la tâche de flux de données Analytique ou vous entraînera des coûts supplémentaires de déplacement des données entre les régions.
    * **Fournit un conteneur de stockage**. Vous pouvez choisir de sélectionner un conteneur existant ou de créer un nouveau conteneur.

    Cliquez sur la flèche droite.

5. Dans la page **paramètres de sérialisation** , définissez le format de sérialisation **CSV**, délimiteur d' **onglet**, codage **UTF8**et puis cliquez sur la coche.

    ![Fournir les paramètres de sérialisation] (./media/data-lake-store-stream-analytics/create.input.4.png "Fournir les paramètres de sérialisation")

6. Une fois que vous avez terminé avec l’Assistant, l’entrée de l’objet blob est ajoutée sous l’onglet **entrées** et la colonne de **diagnostic** doit afficher **OK**. Vous pouvez également explicitement tester la connexion à l’entrée à l’aide du bouton **Tester la connexion** en bas.

## <a name="create-a-data-lake-store-output-for-the-job"></a>Créer une sortie de magasin de données LAC pour la tâche

1. Ouvrez la page de la tâche de flux de données Analytique et cliquez sur l’onglet **sortie** , puis cliquez sur **Ajouter une sortie** pour lancer l’Assistant.

2. Sur la page **Ajouter une sortie à votre travail** , sélectionnez le **Magasin de données lac**et puis cliquez sur la flèche vers l’avant.

    ![Ajouter une sortie à votre travail] (./media/data-lake-store-stream-analytics/create.output.1.png "Ajouter une sortie à votre travail")

3. Sur la page de **connexion de l’autoriser** , si vous avez déjà créé un compte de banque de données lac, cliquez sur **Autoriser**. Dans le cas contraire, cliquez sur **Je veux m’inscrire maintenant** pour créer un nouveau compte. Dans ce didacticiel, supposons que vous avez déjà un compte de banque de données lac créé (comme indiqué dans la condition préalable). Vous sera automatiquement autorisé en utilisant les informations d’identification avec lesquelles vous signé au portail classique d’Azure.

    ![Autoriser le magasin de données lac] (./media/data-lake-store-stream-analytics/create.output.2.png "Autoriser le magasin de données lac")

4. Dans la page **Paramètres de banque de données lac** , entrez les informations comme illustré dans la capture d’écran ci-dessous.

    ![Paramètres de la banque de spécifier données lac] (./media/data-lake-store-stream-analytics/create.output.3.png "Paramètres de la banque de spécifier données lac")

    * **Entrée d’un alias de sortie**. Il s’agit d’un nom unique que vous fournissez pour la sortie de la tâche.
    * **Spécifiez un compte de banque de données lac**. Vous auriez déjà dû créer, comme indiqué dans la condition préalable.
    * **Spécifier un motif de préfixe de chemin d’accès**. Ceci est nécessaire pour identifier les fichiers de sortie sont écrites au magasin de données lac par la tâche de flux de données Analytique. Étant donné que les titres des sorties écrites par la tâche sont dans un format de GUID, y compris un préfixe permettra d’identifier la sortie écrite. Si vous souhaitez inclure un cachet de date et d’heure dans le cadre du préfixe Assurez-vous d’inclure `{date}/{time}` dans le modèle de préfixe. Si vous incluez cette option, les champs de **Format de Date **et **d’Heure** sont activés et vous pouvez sélectionner le format de choix.

    Cliquez sur la flèche droite.

5. Dans la page **paramètres de sérialisation** , définissez le format de sérialisation **CSV**, délimiteur d' **onglet**, codage **UTF8**et puis cliquez sur la case à cocher.

    ![Spécifier le format de sortie] (./media/data-lake-store-stream-analytics/create.output.4.png "Spécifier le format de sortie")

6. Une fois que vous avez terminé avec l’Assistant, la sortie du magasin de données lac sera ajoutée sous l’onglet **sortie** , et la colonne de **diagnostic** doit afficher **OK**. Vous pouvez également explicitement tester la connexion à la sortie à l’aide du bouton **Tester la connexion** en bas.

## <a name="run-the-stream-analytics-job"></a>Exécuter la tâche de flux de données Analytique

Pour exécuter une tâche de flux de données Analytique, vous devez exécuter une requête à partir de l’onglet requête. Pour ce didacticiel, vous pouvez exécuter l’exemple de requête en remplaçant les espaces réservés avec le travail d’entrée et de sortie des alias, comme illustré dans la capture d’écran ci-dessous.

![Exécuter requête] (./media/data-lake-store-stream-analytics/run.query.png "Exécuter requête")

Cliquez sur **Enregistrer** à partir du bas de l’écran, puis cliquez sur **Démarrer**. À partir de la boîte de dialogue, sélectionnez **l’Heure personnalisée**et puis sélectionnez une date dans le passé, par exemple **1/1/2016**. Cliquez sur la case à cocher pour démarrer le travail. Il peut prendre quelques minutes pour démarrer le travail.

![Définir la durée de la tâche] (./media/data-lake-store-stream-analytics/run.query.2.png "Définir la durée de la tâche")

Après le démarrage de la tâche, cliquez sur l’onglet **Moniteur** pour voir comment les données a été traitées.

![Travail de moniteur] (./media/data-lake-store-stream-analytics/run.query.3.png "Travail de moniteur")

Enfin, vous pouvez utiliser le [Portail Azure](https://portal.azure.com) pour ouvrir votre compte de banque de données LAC et de vérifier si les données ont été correctement écrites au compte.

![Sortie de vérifier] (./media/data-lake-store-stream-analytics/run.query.4.png "Sortie de vérifier")

Dans le volet Explorateur de données, notez que la sortie est écrite dans un dossier spécifié dans le magasin de LAC de données des paramètres de sortie (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Voir aussi

* [Créez un cluster d’HDInsight pour utiliser le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
