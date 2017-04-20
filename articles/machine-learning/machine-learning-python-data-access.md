<properties 
    pageTitle="Accéder aux groupes de données avec la bibliothèque cliente de Python d’apprentissage Machine | Microsoft Azure" 
    description="Installer et utiliser la bibliothèque cliente de Python pour accéder et gérer les données de formation de Machine Azure en toute sécurité à partir d’un environnement de Python local." 
    services="machine-learning" 
    documentationCenter="python" 
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
    ms.author="huvalo;bradsev" />


#<a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Accès des groupes de données avec les Python à l’aide de la bibliothèque de client Azure Machine Learning Python 

L’aperçu de la bibliothèque de client Microsoft Azure Machine Learning Python permettre un accès sécurisé à vos groupes de données Azure Machine Learning à partir d’un environnement de Python local et permet la création et la gestion des groupes de données dans un espace de travail.

Cette rubrique fournit des instructions sur comment :

* Installez la bibliothèque client de Python de formation de Machine 
* accéder et télécharger des groupes de données, y compris les instructions sur la façon d’obtenir l’autorisation d’accéder à des groupes de données Azure Machine Learning depuis votre environnement Python local
*  accéder aux jeux de données intermédiaires d’expérimentations
*  la bibliothèque cliente de Python permet d’énumérer les groupes de données, accéder aux métadonnées, lire le contenu d’un dataset, créer de nouveaux groupes de données et les mettre à jour les groupes de données existants

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>Conditions préalables

La bibliothèque cliente de Python a été testée dans les environnements suivants :

 - Windows, Mac et Linux
 - Python 2.7, 3.3 et 3.4

Il a une dépendance sur les packages suivants :

 - demandes
 - Python-dateutil
 - pandas

Il est recommandé à l’aide d’une distribution de Python tels que [Anaconda](http://continuum.io/downloads#all) ou de [toile](https://store.enthought.com/downloads/), qui sont fournis avec les Python, IPython et les trois packages répertoriés ci-dessus installée. IPython n’est pas strictement obligatoire, il s’agit d’un environnement idéal pour la manipulation et la visualisation des données de manière interactive.


###<a name="installation"></a>Comment faire pour installer la bibliothèque client de Python de formation de Machine Azure

La bibliothèque cliente de Python de formation Azure Machine doit également être installée pour effectuer les tâches décrites dans cette rubrique. Il est disponible à partir de l' [Index de Package Python](https://pypi.python.org/pypi/azureml). Pour l’installer dans votre environnement de Python, exécutez la commande suivante à partir de votre environnement de Python local :

    pip install azureml

Sinon, vous pouvez télécharger et installer à partir des sources sur [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Si vous avez git installé sur votre ordinateur, vous pouvez utiliser pip pour installer directement à partir du référentiel git :

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>Les extraits de Code de Studio permet d’accéder aux groupes de données

La bibliothèque cliente de Python permet d’accéder par programme à vos groupes de données existants à partir d’expériences qui ont été exécutés.

À partir de l’interface web de Studio, vous pouvez générer des extraits de code qui comprennent toutes les informations nécessaires pour télécharger et désérialiser des groupes de données en tant qu’objets de Pandas DataFrame sur votre ordinateur de l’emplacement.

### <a name="security"></a>Sécurité d’accès aux données

Les extraits de code fournis par Studio pour utilisation avec la bibliothèque de client Python inclut votre id de l’espace de travail et l’autorisation jeton. Ils fournissent un accès complet à votre espace de travail et doivent être protégés comme un mot de passe.

Pour des raisons de sécurité, la fonctionnalité d’extrait de code est uniquement disponible pour les utilisateurs qui ont leur rôle à définir en tant que **propriétaire** de l’espace de travail. Votre rôle s’affiche dans le Studio de formation de Machine Azure dans la page **utilisateurs** sous **paramètres**.

![Sécurité][security]

Si votre rôle n’est pas défini en tant que **propriétaire**, vous pouvez soit demander à la nouvelle en tant que propriétaire, ou demandez au propriétaire de l’espace de travail afin de vous offrir l’extrait de code.

Pour obtenir le jeton d’autorisation, vous pouvez effectuer l’une des opérations suivantes :



- Demander un jeton d’un propriétaire. Accès aux propriétaires leurs jetons d’autorisations à partir de la page Paramètres de leur espace de travail dans un Studio. Sélectionnez **paramètres** dans le volet gauche et cliquez sur **Les jetons de l’autorisation** pour afficher les jetons principales et secondaires.  Bien que les jetons d’autorisation secondaire ou principal peuvent être utilisés dans l’extrait de code, il est recommandé que les propriétaires ne partagent que les jetons d’autorisation secondaire.

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

- Demander à être promu au rôle de propriétaire.  Pour ce faire, un propriétaire de l’espace de travail doit d’abord vous supprimer de l’espace de travail puis vous Réinviter lui en tant que propriétaire.

Une fois que les développeurs ont obtenu l’id de l’espace de travail et l’autorisation jeton, ils sont en mesure d’accéder à l’espace de travail à l’aide de l’extrait de code, quel que soit leur rôle.

Les jetons d’autorisation sont gérés sur la page de **Jetons d’autorisations** sous **paramètres**. Vous pouvez les régénérer, mais cette procédure révoque l’accès au jeton précédent.

### <a name="accessingDatasets"></a>Groupes de données Access à partir d’une application locale de Python

1. Dans un Studio de formation de Machine, cliquez sur **les groupes de données** dans la barre de navigation sur la gauche.

2. Sélectionnez le groupe de données que vous souhaitez accéder. Vous pouvez sélectionner un des jeux de données à partir de la liste de **Mes groupes de données** ou à partir de la liste **d’exemples** .

3. Dans la barre d’outils inférieure, cliquez sur **Générer un Code d’accès aux données**. Si les données sont dans un format incompatible avec la bibliothèque cliente de Python, ce bouton est désactivé.

    ![Groupes de données][datasets]

4. Sélectionnez l’extrait de code dans la fenêtre qui s’affiche et le copie dans le Presse-papiers.

    ![Code d’accès][dataset-access-code]

5. Collez le code dans le bloc-notes de votre application de Python local.

    ![Ordinateur portable][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Accès aux jeux de données intermédiaires à partir des expériences d’apprentissage automatique

Après l’exécution d’une expérience dans le Studio d’apprentissage Machine, il est possible d’accéder aux jeux de données intermédiaires à partir des nœuds de modules de sortie. Jeux de données intermédiaires est des données qui a été créées et utilisées pour les étapes intermédiaires lorsqu’un outil de modèle a été exécuté.

Jeux de données intermédiaires sont accessibles tant que le format de données est compatible avec la bibliothèque cliente de Python.

Les formats suivants sont pris en charge (ces constantes sont dans le `azureml.DataTypeIds` classe) :

 - Texte brut
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

Vous pouvez déterminer le format en plaçant le curseur sur un nœud de sortie de module. Il est affiché avec le nom de nœud dans une info-bulle.

Certains modules, tels que le [fractionnement] [ split] module de sortie dans un format nommé `Dataset`, qui n’est pas pris en charge par la bibliothèque cliente de Python.

![Format du groupe de données][dataset-format]

Vous devez utiliser un module de conversion, tel que de la [convertir au format CSV][convert-to-csv], afin d’obtenir une sortie dans un format pris en charge.

![Format de GenericCSV][csv-format]

Les étapes suivantes montrent un exemple qui crée une expérience, l’exécute et accède à ce jeu de données.

1. Créer une nouvelle expérience.

2. Insérez un module **adulte Classification binaire revenu de recensement de groupe de données** .

3. Insérer un [fractionnement] [ split] module et se connecter à son entrée dans la sortie de module du groupe de données.

4. Insérer une [conversion au format CSV] [ convert-to-csv] module et se connecter à son entrée d’un [fractionnement] de la[ split] module de sortie.

5. Enregistrer l’expérience, l’exécuter et attendre terminer l’exécution.

6. Cliquez sur le nœud de sortie sur le [convertir au format CSV] [ convert-to-csv] module.

7. Lorsque le menu contextuel s’affiche, sélectionnez **Générer un Code d’accès aux données**.

    ![Menu contextuel][experiment]

8. Sélectionnez l’extrait de code et copiez-le dans le Presse-papiers à partir de la fenêtre qui s’affiche.

    ![Code d’accès][intermediate-dataset-access-code]

9. Collez le code dans votre bloc-notes.

    ![Ordinateur portable][ipython-intermediate-dataset]

10. Vous pouvez visualiser les données à l’aide de matplotlib. S’affiche dans un histogramme pour la colonne age :

    ![Histogramme][ipython-histogram]


##<a name="clientApis"></a>La bibliothèque cliente de Python d’apprentissage Machine permet d’accéder, lire, créer et gérer des groupes de données

### <a name="workspace"></a>Espace de travail

L’espace de travail est le point d’entrée pour la bibliothèque cliente de Python. Fournir le `Workspace` jeton de classe avec l’id de l’espace de travail et de l’autorisation de créer une instance :

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Énumérer les groupes de données

Pour énumérer tous les groupes de données dans un espace de travail donné :

    for ds in ws.datasets:
        print(ds.name)

Énumérer seulement les datasets créés par l’utilisateur :

    for ds in ws.user_datasets:
        print(ds.name)

Énumérer seulement les jeux de données d’exemple :

    for ds in ws.example_datasets:
        print(ds.name)

Vous pouvez accéder à un groupe de données par son nom (qui est sensible à la casse) :

    ds = ws.datasets['my dataset name']

Ou bien, vous pouvez y accéder par l’index :

    ds = ws.datasets[0]


### <a name="metadata"></a>Métadonnées

Groupes de données ont des métadonnées, en plus de contenu. (Les jeux de données intermédiaires est une exception à cette règle et n’ont pas de métadonnées.)

Certaines valeurs de métadonnées sont attribuées par l’utilisateur au moment de la création :

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

D’autres sont des valeurs assignées par ML d’Azure :

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consultez le `SourceDataset` classe pour plus d’informations sur les métadonnées disponibles.


### <a name="read-contents"></a>Lire le contenu

Les extraits de code fournis par Machine Learning Studio automatiquement téléchargement et désérialiser le groupe de données à un objet Pandas DataFrame. Cela est effectué avec le `to_dataframe` méthode :

    frame = ds.to_dataframe()

Si vous préférez télécharger les données brutes et procéder vous-même à la désérialisation, qui est une option. Pour le moment, il s’agit de la seule option disponible pour les formats tels que 'ARFF', la bibliothèque cliente de Python ne peut pas désérialiser.

Pour lire le contenu sous forme de texte :

    text_data = ds.read_as_text()

Pour lire le contenu binaire :

    binary_data = ds.read_as_binary()

Vous pouvez également ouvrir un flux vers le contenu :

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Créer un nouveau groupe de données

La bibliothèque cliente de Python vous permet de télécharger des jeux de données à partir de votre programme Python. Ces groupes de données sont alors disponibles pour une utilisation dans votre espace de travail.

Si vous avez vos données dans un DataFrame de Pandas, utilisez le code suivant :

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Si vos données sont a déjà été sérialisées, vous pouvez utiliser :

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

La bibliothèque cliente de Python est en mesure de sérialiser un DataFrame de Pandas dans les formats suivants (ces constantes sont dans le `azureml.DataTypeIds` classe) :

 - Texte brut
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### <a name="update-an-existing-dataset"></a>Mise à jour d’un groupe de données existant

Si vous essayez de télécharger un nouveau groupe de données avec un nom qui correspond à un groupe de données existant, vous devez obtenir une erreur de conflit.

Pour mettre à jour un groupe de données existant, vous devez d’abord obtenir une référence à un dataset existant :

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Puis utilisez `update_from_dataframe` pour sérialiser et remplacez le contenu du dataset dans Azure :

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Si vous souhaitez sérialiser les données dans un format différent, spécifiez une valeur pour l’option `data_type_id` paramètre.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Vous pouvez éventuellement définir une nouvelle description en spécifiant une valeur pour le `description` paramètre.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Vous pouvez éventuellement définir un nouveau nom en spécifiant une valeur pour le `name` paramètre. Par la suite, vous allez récupérer le groupe de données en utilisant le nouveau nom uniquement. Le code suivant met à jour les données, le nom et la description.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Le `data_type_id`, `name` et `description` paramètres sont facultatifs et par défaut pour leur valeur précédente. Le `dataframe` paramètre est toujours requise.

Si vos données sont a déjà été sérialisées, utilisez `update_from_raw_data` au lieu de `update_from_dataframe`. Si vous passez simplement dans `raw_data` au lieu de `dataframe`, il fonctionne de la même façon.



<!-- Images -->
[security]:./media/machine-learning-python-data-access/security.png
[dataset-format]:./media/machine-learning-python-data-access/dataset-format.png
[csv-format]:./media/machine-learning-python-data-access/csv-format.png
[datasets]:./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]:./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]:./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]:./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
