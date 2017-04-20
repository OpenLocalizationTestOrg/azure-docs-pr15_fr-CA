<properties
    pageTitle="Apprenez à gérer à l’aide des API de gestion des services web AzureML | Microsoft Azure"
    description="Guide montrant comment gérer à l’aide des API de gestion des services web AzureML."
    keywords="gestion de l’apprentissage, api de machine"
    services="machine-learning"
    documentationCenter=""
    authors="roalexan"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="roalexan" />


# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Apprenez à gérer à l’aide des API de gestion des services web AzureML

##<a name="overview"></a>Vue d’ensemble

Ce guide vous explique comment commencer rapidement à l’aide des API de gestion pour gérer vos services web de AzureML.

##<a name="what-is-azure-api-management"></a>Quelle est la gestion des API Azure ?

La gestion des API Azure est un service Azure qui vous permet de gérer vos points d’entrée API REST en définissant l’accès utilisateur, limitation de l’utilisation et la surveillance de tableau de bord. Cliquez [ici](https://azure.microsoft.com/services/api-management/) pour plus d’informations sur la gestion des API Azure. Cliquez [ici](../api-management/api-management-get-started.md) pour obtenir un guide sur la mise en route avec la gestion des API Azure. Ce guide autre, ce guide est basé sur, couvre plus de rubriques, y compris les configurations de notification, la tarification de niveau, la gestion des réponses, l’authentification des utilisateurs, créer des produits, les abonnements de développeur et dashboarding de l’utilisation.

##<a name="what-is-azureml"></a>Quel est AzureML ?

AzureML est un service Azure pour machine de formation qui vous permet de facilement générer, déployer et partager les solutions d’analytique avancée. Cliquez [ici](https://azure.microsoft.com/services/machine-learning/) pour plus d’informations sur AzureML.

##<a name="prerequisites"></a>Conditions préalables

Pour terminer ce guide, vous devez :

* Un compte Azure. Si vous n’avez pas un compte Azure, cliquez [ici](https://azure.microsoft.com/pricing/free-trial/) pour plus d’informations sur la création d’un compte d’essai gratuit.
* Un compte AzureML. Si vous ne disposez pas d’un AzureML, cliquez [ici](https://studio.azureml.net/) pour plus d’informations sur la création d’un compte d’essai gratuit.
* L’espace de travail, le service et l’api_key pour une expérience AzureML déployée comme un service web. Cliquez [ici](machine-learning-create-experiment.md) pour plus d’informations sur la création d’une expérience de AzureML. Cliquez [ici](machine-learning-publish-a-machine-learning-web-service.md) pour plus d’informations sur la façon de déployer une expérience de AzureML sous la forme d’un service web. Alternativement, l’annexe A contient les instructions créer et tester une expérience AzureML simple et le déployer comme un service web.

##<a name="create-an-api-management-instance"></a>Créez une instance de gestion de l’API

Vous trouverez ci-dessous les étapes pour utiliser l’API de gestion pour gérer votre service web de AzureML. Tout d’abord créer une instance de service. Connectez-vous au [Portail classique](https://manage.windowsazure.com/) et cliquez sur **Nouveau** > **Application Services** > **Gestion des API** > **créer**.

![créer une instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Spécifiez l' **URL**unique. Ce guide utilise **demoazureml** : vous devez choisir un nom différent. Cliquez sur **l’abonnement** et la **région** souhaitée pour votre instance de service. Après avoir effectué vos sélections, cliquez sur le bouton suivant.

![créer-service-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Spécifiez une valeur pour le **Nom de l’organisation**. Ce guide utilise **demoazureml** : vous devez choisir un nom différent. Saisissez votre adresse e-mail dans le champ **e-mail de l’administrateur** . Cette adresse e-mail est utilisée pour les notifications du système de gestion de l’API.

![créer-service-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Cliquez sur la case à cocher pour créer l’instance de service. *Il prend jusqu'à 30 minutes pour un nouveau service à créer*.

##<a name="create-the-api"></a>Créer l’API

Une fois que l’instance de service est créé, l’étape suivante consiste à créer l’API. Une API se compose d’un ensemble d’opérations qui peuvent être appelées à partir d’une application cliente. Opérations de l’API sont transmises aux services web existants. Ce guide crée des API comme proxy pour les services web AzureML RRS et BES existants.

API est créés et configurés à partir du portail de publisher API, qui est accessible via le portail classique d’Azure. Pour atteindre le portail de l’éditeur, sélectionnez votre instance de service.

![Sélectionnez instance de service](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API.

![gestion du service](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Cliquez sur **API** à partir du menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Ajouter des API**.

![API-gestion-menu](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Tapez **AzureML démonstration API** comme le **nom de l’API de Web**. Tapez l' **URL du service Web** **https://ussouthcentral.services.azureml.net** . Tapez le **suffixe d’URL d’API Web** **azureml-démonstration** . Cochez **HTTPS** comme le schéma **d’URL d’API Web** . Activez la **Starter** en tant que **produits**. Lorsque vous avez terminé, cliquez sur **Enregistrer** pour créer l’API.

![Ajouter-nouveau-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##<a name="add-the-operations"></a>Ajouter les opérations

Cliquez sur l' **opération d’ajout de** pour ajouter des opérations à cette API.

![opération Ajouter](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

La **nouvelle** fenêtre s’affiche et la **Signature** est activée par défaut.

##<a name="add-rrs-operation"></a>Opération de RR d’ajout

Tout d’abord créer une opération pour le service AzureML RRS. Sélectionnez le **verbe HTTP** **POST** . Type de **/workspaces/ {espace de travail} /services/ {service} / execute?api-version = {apiversion} & détails = {détails}** comme **modèle d’URL**. Comme le **nom complet**, tapez **Exécuter de RR** .

![Ajouter-RR-opération-signature](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Cliquez sur **réponses** > **Ajouter** sur la gauche, puis sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

![Ajouter-RR-opération-réponse](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##<a name="add-bes-operations"></a>Ajouter des opérations de BES

Captures d’écran ne sont pas inclus pour les opérations d’initialisation qu’ils sont très semblables à celles de l’ajout de l’opération de RR.

###<a name="submit-but-not-start-a-batch-execution-job"></a>Soumettre (mais pas démarré) une tâche d’exécution de traitement par lots

Cliquez sur **l’opération d’ajout** pour ajouter l’opération AzureML BES à l’API. Sélectionnez le **verbe HTTP** **POST** . Type de **/workspaces/ {espace de travail} /services/ {service} / jobs?api-version = {apiversion}** pour le **modèle d’URL**. Pour le **nom complet**, tapez **BES soumettre** . Cliquez sur **réponses** > **Ajouter** sur la gauche, puis sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

###<a name="start-a-batch-execution-job"></a>Démarrage d’une tâche d’exécution de traitement par lots

Cliquez sur **l’opération d’ajout** pour ajouter l’opération AzureML BES à l’API. Sélectionnez le **verbe HTTP** **POST** . Type de **/workspaces/ {espace de travail} /services/ {service} /jobs/ {identificateur} / start?api-version = {apiversion}** pour le **modèle d’URL**. Pour le **nom complet**, tapez **BES démarrer** . Cliquez sur **réponses** > **Ajouter** sur la gauche, puis sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

###<a name="get-the-status-or-result-of-a-batch-execution-job"></a>Obtenir l’état ou le résultat d’un travail de l’exécution par lots

Cliquez sur **l’opération d’ajout** pour ajouter l’opération AzureML BES à l’API. Sélectionnez **obtenir** pour le **verbe HTTP**. Type de **?api version /workspaces/ {d’espace de travail} /services/ {service} /jobs/ {identificateur} = {apiversion}** pour le **modèle d’URL**. Pour le **nom complet**, tapez **BES état** . Cliquez sur **réponses** > **Ajouter** sur la gauche, puis sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

###<a name="delete-a-batch-execution-job"></a>Suppression d’une tâche d’exécution de traitement par lots

Cliquez sur **l’opération d’ajout** pour ajouter l’opération AzureML BES à l’API. Sélectionnez **Supprimer** pour le **verbe HTTP**. Type de **?api version /workspaces/ {d’espace de travail} /services/ {service} /jobs/ {identificateur} = {apiversion}** pour le **modèle d’URL**. Pour le **nom complet**, tapez **BES supprimer** . Cliquez sur **réponses** > **Ajouter** sur la gauche, puis sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

##<a name="call-an-operation-from-the-developer-portal"></a>Appel d’une opération à partir du portail de développement

Les opérations peuvent être appelées directement à partir du portail de développement, ce qui offre un moyen pratique d’afficher et de tester les opérations d’une API. Dans ce guide étape vous appellera la méthode **Exécuter des enregistrements de ressources** qui a été ajoutée à l' **API de démonstration AzureML**. Cliquez sur **portail destiné aux développeurs** dans le menu situé en haut à droite du portail classique.

![portail des développeurs](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Cliquez sur **API** dans le menu supérieur, puis cliquez sur **AzureML démonstration API** pour visualiser les opérations disponibles.

![demoazureml-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Sélectionnez l’option **Exécuter de RR** pour l’opération. Cliquez sur **tester**.

![Try-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Pour les paramètres de demande, tapez votre **espace de travail**, **service**, **2.0** pour **apiversion**et **true** pour plus de **Détails**. Vous pouvez trouver votre **espace de travail** et d’un **service** dans le tableau de bord du service web AzureML (voir le **Test du service web** dans l’annexe A).

Pour les en-têtes de demande, cliquez sur **Ajouter en-tête** et tapez **application/json**et le **Type de contenu** **Ajouter en-tête** et tapez **autorisation** et **PORTEUR <YOUR AZUREML SERVICE API-KEY> **. Vous trouverez votre **clé d’api** dans le tableau de bord du service web AzureML (voir le **Test du service web** dans l’annexe A).

Type de **{« Inputs » : {« input1 » : {« ColumnNames » : [« Col2 »], « valeurs » : [[« C’est une bonne journée »]]}}, « GlobalParameters » : {}}** pour le corps de la demande.

![azureml-démonstration-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Cliquez sur **Envoyer**.

![Envoyer](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Une fois qu’une opération est appelée, le portail de développement affiche l' **URL demandée** dans le service principal, l' **état de réponse**, les **en-têtes de réponse**et tout le **contenu de la réponse**.

![état de la réponse](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##<a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Annexe A : création et test d’un simple AzureML du service web

###<a name="creating-the-experiment"></a>Création de l’expérience

Voici les étapes de la création d’une expérience de AzureML simple et son déploiement sous la forme d’un service web. L’accepte de service web en une colonne de texte arbitraire d’entrée et renvoie un jeu de fonctionnalités représentée sous forme d’entiers. Par exemple :

Texte | Texte haché
--- | ---
Il s’agit d’une bonne journée. | 1 1 2 2 0 2 0 1

Tout d’abord, à l’aide d’un navigateur de votre choix, accédez à : [https://studio.azureml.net/](https://studio.azureml.net/) et entrez vos informations d’identification pour vous connecter. Ensuite, créez un nouvel essai à blanc.

![modèles-expérience de recherche](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Renommez-le **SimpleFeatureHashingExperiment**. Développez les **Groupes de données enregistrées** et **Critiques d’Amazon** faire glisser votre expérience.

![simple-fonction-hachage-expérience](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Développez la **Manipulation** et **Transformation de données** et **Sélectionner les colonnes dans le jeu de données** faire glisser votre expérience. Se connecter **critiques d’Amazon** pour **Sélectionner des colonnes dans un groupe de données**.

![Sélectionnez-colonnes](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Cliquez sur **Sélectionner les colonnes dans un groupe de données** puis cliquez sur **lancer le sélecteur de colonne** et sélectionnez **Col2**. Cliquez sur la case à cocher pour appliquer ces modifications.

![Sélectionnez-colonnes](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Développez **Analytique du texte** , puis faites glisser de la **Fonction de hachage** sur l’expérience. Se connecter **Sélectionner des colonnes dans un groupe de données** à la **fonction de hachage**.

![colonnes de projet se connecter](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Tapez **3** pour la **bitsize de hachage**. Cette opération va créer 8 (23) colonnes.

![bitsize de hachage](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

À ce stade, vous souhaiterez peut-être cliquez sur **exécuter** pour tester l’expérience.

![exécuter](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###<a name="create-a-web-service"></a>Créer un service web

Maintenant, créez un service web. Développez le **Service Web** et faites glisser **Input** sur votre expérience. Connecter **l’entrée** de **fonction de hachage**. Également glisser **sortie** votre expérience. Connectez la **sortie** de la **fonction de hachage**.

![sortie-à-fonctionnalité-hachage](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Cliquez sur **service de publication web**.

![service web publier](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Cliquez sur **Oui** pour publier l’expérience.

![Oui-de publication](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###<a name="test-the-web-service"></a>Tester le service web

Un service web de AzureML se compose de RSS (service de demande/réponse) et les points de terminaison BES (service d’exécution de traitement par lots). RSS est l’exécution synchrone. BES est pour l’exécution de la tâche asynchrone. Pour tester votre service web avec la source de Python exemple ci-dessous, vous devrez télécharger et installer le Kit de développement Azure pour les Python (voir : [comment installer les Python](../python-how-to-install.md)).

Vous devez également **l’espace de travail**, **service**et **api_key** de votre expérience de la source de l’exemple ci-dessous. Vous pouvez trouver l’espace de travail et le service en cliquant sur **Demande/réponse** ou **L’exécution du traitement par lots** pour votre expérience dans le tableau de bord de service web.

![Rechercher-espace de travail-et-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Vous pouvez trouver **api_key** en cliquant sur votre expérience dans le tableau de bord de service web.

![clé de l’api de recherche](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####<a name="test-rrs-endpoint"></a>Point de terminaison de test RR

#####<a name="test-button"></a>Bouton de test

Un moyen simple pour tester le point de terminaison de RR est de cliquer sur **tester** sur le tableau de bord de service web.

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Tapez **Ceci est un bon jour** **col2**. Cliquez sur la coche.

![entrer des données](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Vous verrez quelque chose comme

![sortie de l’exemple](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####<a name="sample-code"></a>Exemples de Code

Il est un autre moyen de tester vos enregistrements de ressources à partir de votre code client. Si vous cliquez sur **demande/réponse** sur le tableau de bord et faites défiler vers le bas, vous verrez des exemples de code pour C#, Python et R. Vous verrez également la syntaxe de la requête de RR, y compris la demande URI, en-têtes et corps.

Ce guide montre un exemple de Python qui fonctionne. Vous devrez modifier à l’aide de **l’espace de travail**, **service**et **api_key** de votre expérience.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

####<a name="test-bes-endpoint"></a>Point de terminaison de test BES
Cliquez sur **l’exécution du traitement par lots** sur le tableau de bord et faites défiler vers le bas. Vous verrez des exemples de code pour C#, Python et R. Vous verrez également la syntaxe des requêtes BES pour soumettre un travail, démarrer un travail, obtenir le statut ou les résultats d’un travail et supprimer un travail.

Ce guide montre un exemple de Python qui fonctionne. Vous devez modifier à l’aide de **l’espace de travail**, **service**et **api_key** de votre expérience. En outre, vous devez modifier le **nom du compte de stockage**, **clé de compte de stockage**et **nom du conteneur de stockage**. Enfin, vous devez modifier l’emplacement du **fichier d’entrée** et l’emplacement du **fichier de sortie**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
