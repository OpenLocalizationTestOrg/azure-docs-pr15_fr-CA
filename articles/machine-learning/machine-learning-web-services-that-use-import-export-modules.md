<properties
    pageTitle="Déploiement de services web Azure ML qui utilisent des modules de données importation et exportation de données | Microsoft Azure"
    description="Découvrez comment utiliser les modules des données d’importation et d’exportation de données pour envoyer et recevoir des données à partir d’un service web."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="v-donglo"/>



# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Déploiement de services web Azure ML qui utilisent l’importation de données et les modules d’exportation de données 

Lorsque vous créez une expérience prédictive, vous ajoutez une entrée de service web et de la sortie. Lorsque vous déployez l’expérience, les consommateurs peuvent envoyer et recevoir des données à partir du service web via les entrées et les sorties. Pour certaines applications, les données d’un consommateur peuvent être disponible à partir d’un flux de données ou se trouvent déjà dans la source de données externe, tels que le stockage des objets Blob Azure. Dans ces cas, ils n’avez pas besoin de lire et d’écrire des données à l’aide de sorties et les entrées de service web. Au lieu de cela, ils peuvent utiliser le Service d’exécution de lot (BES) pour lire les données à partir de la source de données à l’aide d’un module d’importation de données et écrire les résultats de la notation à un emplacement de données différentes à l’aide d’un module d’exportation de données.

L’importation de données et les modules d’exportation données, peut lire et écrire à un certain nombre de données fournissent des emplacements, par exemple, une URL Web via HTTP, une requête de la ruche, de la base de données SQL d’Azure, stockage de Table d’Azure, stockage Azure Blob, un flux de données ou une base de données SQL sur site.

Cette rubrique utilise le « exemple 5 : évaluation de Train, Test, pour le classement binaire : Dataset pour adultes » exemple et suppose que le groupe de données a déjà été chargé dans une table Azure SQL nommée censusdata.

## <a name="create-the-training-experiment"></a>Créer l’expérience de formation 
 
Lorsque vous ouvrez le « exemple 5 : évaluation de Train, Test, pour le classement binaire : Dataset pour adultes » exemple il utilise l’exemple de dataset pour adultes Classification binaire revenu de recensement. Et de l’expérience dans la zone de dessin ressemblera à l’image suivante.

![Configuration initiale de l’expérience.](./media/machine-learning-web-services-that-use-import-export-modules/initial-look-of-experiment.png)
  

Pour lire les données de la table Azure SQL :

1.  Supprimer le module du groupe de données.
2.  Dans la zone de recherche de composants, tapez import.
3.  À partir de la liste des résultats, ajoutez un module *d’Importation de données* pour la toile de l’expérimentation.
4.  Connectez la sortie du module *Importer les données de* l’entrée du module de *Nettoyage des données manquantes* .
5.  Dans le volet des propriétés, sélectionnez la **Base de données de SQL Azure** dans la liste déroulante de **Source de données** .
6.  Dans le champ **nom du serveur de base de données**, le **nom de base de données**, **nom d’utilisateur**et les champs de **mot de passe** , entrez les informations appropriées pour votre base de données.
7.  Dans le champ de requête de base de données, entrez la requête suivante.

        select [age],
           [workclass],
           [fnlwgt],
           [education],
           [education-num],
           [marital-status],
           [occupation],
           [relationship],
           [race],
           [sex],
           [capital-gain],
           [capital-loss],
           [hours-per-week],
           [native-country],
           [income]
        from dbo.censusdata;

8.  En bas de l’expérimentation la zone de dessin, cliquez sur **exécuter**.

## <a name="create-the-predictive-experiment"></a>Créez l’expérience prédictive

Ensuite, vous configurez l’expérience prédictive à partir de laquelle vous allez déployer votre service web.

1.  Au bas de la zone d’essai, cliquez sur **La valeur d’un Service Web** et sélectionnez **Prédictive Web Service [recommandé]**.
2.  Supprimer *l’Entrée du Service Web* et les *modules de sortie du Service Web* à partir de l’expérience prédictive. 
3.  Dans la zone de recherche de composants, tapez l’exportation.
4.  À partir de la liste des résultats, ajoutez un module *d’Exportation de données* pour la toile de l’expérimentation.
5.  Connectez la sortie du module *Modèle de Score de* l’entrée du module *d’Exportation de données* . 
6.  Dans le volet des propriétés, sélectionnez la **Base de données de SQL Azure** dans la liste déroulante destination de données.
7.  Dans le champ **nom du serveur de base de données**, **nom de la base de données**, **nom de compte utilisateur de serveur**et les champs de **mot de passe utilisateur serveur** , entrez les informations appropriées pour votre base de données.
8.  Dans le champ de **la liste des colonnes doit être enregistré séparées par des virgules** , tapez un score des étiquettes.
9.  Dans le **champ nom de la table de données**, tapez dbo. ScoredLabels. Si la table n’existe pas, il est créé lors de l’exécution de l’essai ou le service web est appelé.
10. Dans le champ de **la liste des colonnes de la table de données séparées par des virgules** , tapez ScoredLabels.

Lorsque vous écrivez une application qui appelle le service web final, vous souhaiterez peut-être spécifier une autre requête d’entrée ou la table de destination en cours d’exécution. Pour configurer ces entrées et sorties, vous pouvez utiliser la fonctionnalité paramètres de Service Web pour définir la propriété de *source de données* du module *Importation de données* et la propriété de destination de données mode *Exporter les données* .  Pour plus d’informations sur les paramètres de Service Web, consultez l' [entrée paramètres de Service Web AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) sur l’Intelligence de Cortana et le Blog d’apprentissage Machine.

Pour configurer les paramètres du Service Web pour la requête de l’importation et de la table de destination :

1.  Dans le volet des propriétés pour le module *d’Importation de données* , cliquez sur l’icône en haut à droite du champ de **requête de base de données** et **définir en tant que paramètre du service web**.
2.  Dans le volet des propriétés pour le module *d’Exportation de données* , cliquez sur l’icône en haut à droite du **nom de la table de données** de champ et **définir en tant que paramètre du service web**.
3.  En bas du volet de propriétés *d’Exporter les données de* module dans la section **Paramètres du Service Web** , cliquez sur la requête de base de données et renommez-le requête.
4.  Cliquez sur le **nom de la table de données** et le renommer **Table**.

Lorsque vous avez terminé, votre expérience doit ressembler à l’image suivante.
 
![Aspect final d’expérimentation.](./media/machine-learning-web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Vous pouvez maintenant déployer l’expérience sous la forme d’un service web.

## <a name="deploy-the-web-service"></a>Déployer le service web 
Vous pouvez déployer sur un classique ou un nouveau service web.

### <a name="deploy-a-classic-web-service"></a>Déployer un Service Web classique

Pour déployer un service Web classique et créer une application pour l’utiliser :

1.  En bas de la zone d’essai, cliquez sur Exécuter.
2.  Une fois l’exécution terminée, cliquez sur **Déployer le Service Web** et sélectionnez le **Service Web de déploiement [standard]**.
3.  Sur le tableau de bord de service web, recherchez la clé API. Copier et l’enregistrer pour une utilisation ultérieure.
4.  Dans le tableau du **Point de terminaison par défaut** , cliquez sur le lien de **L’exécution du lot** pour ouvrir la Page d’aide de l’API.
5.  Dans Visual Studio, créez une application de console C#.
6.  Sur la Page d’aide API, recherchez la section de **l’Exemple de Code** au bas de la page.
7.  Copiez et collez l’exemple de code C# dans le fichier Program.cs et supprimez toutes les références pour le stockage des objets blob.
8.  Mettre à jour la valeur de la variable *apiKey* avec la clé API enregistrée précédemment.
9.  Recherchez la déclaration de demande et de mettre à jour les valeurs des paramètres de Service Web qui sont passés aux modules de *Données d’importation* et *d’Exportation de données* . Dans ce cas, vous va utiliser la requête d’origine, mais définir un nouveau nom de table.

        var request = new BatchExecutionRequest() 
        {   
            GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
            }
        };

10. Exécutez l’application. 

À la fin de l’exécution, une nouvelle table est ajoutée à la base de données contenant les résultats du score.

### <a name="deploy-a-new-web-service"></a>Déployer un nouveau Service Web

Pour déployer un Service Web et création d’une application pour l’utiliser :

1.  En bas de l’expérimentation la zone de dessin, cliquez sur **exécuter**.
2.  Une fois l’exécution terminée, cliquez sur **Déployer le Service Web** et sélectionnez **Web Service de déploiement [nouveau]**.
3.  Dans la page déployer l’expérimentation, entrez un nom pour votre service web et sélectionnez un plan de tarification, puis cliquez sur **déployer**.
4.  Dans la page de **démarrage rapide** , cliquez sur **consommer**.
5.  Dans la section de **l’Exemple de Code** , cliquez sur **traitement par lots**.
6.  Dans Visual Studio, créez une application de console C#.
7.  Copiez et collez l’exemple de code C# dans le fichier Program.cs.
8.  Mettre à jour la valeur de la variable *apiKey* avec la **Clé primaire** qui se trouve dans la section **informations de base de la consommation** .
9.  Recherchez la déclaration de *scoreRequest* et de mettre à jour les valeurs des paramètres de Service Web qui sont passés aux modules de *Données d’importation* et *d’Exportation de données* . Dans ce cas, vous va utiliser la requête d’origine, mais définir un nouveau nom de table.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                 { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };

10. Exécutez l’application. 
 

