<properties
    pageTitle="Assistant de copie Azure Factory de données | Microsoft Azure"
    description="Obtenir des informations sur l’utilisation de l’Assistant de copie de données en usine Azure pour copier des données à partir de sources de données prises en charge dans les récepteurs."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="spelluru"/>

# <a name="azure-data-factory---copy-wizard"></a>Azure Data Factory - Assistant copie
L’Assistant de copie de fabrique de données Azure est de faciliter le processus de réception des données, qui sont généralement une première étape dans un scénario d’intégration des données de bout en bout. Lorsque vous passez par le biais de l’Assistant de copie de fabrique de données Azure, il est inutile de comprendre toutes les définitions de services liés, des groupes de données et des pipelines de JSON. Toutefois, après avoir effectué toutes les étapes de l’Assistant, l’Assistant crée automatiquement un pipeline pour copier des données à partir de la source de données sélectionnée vers la destination sélectionnée. En outre, l’Assistant copie vous aide à valider les données étant ingérées au moment de la rédaction, ce qui permet de gagner beaucoup de temps, en particulier lorsque vous sont ingestion des données pour la première fois à partir de la source de données. Pour démarrer l’Assistant de copie, cliquez sur la mosaïque de **Copier les données** sur la page d’accueil de votre fabrique de données.

![Assistant copie](./media/data-factory-copy-wizard/copy-data-wizard.png)


## <a name="an-intuitive-wizard-for-copying-data"></a>Un Assistant intuitif pour copier les données
Cet Assistant vous permet de déplacer facilement des données à partir d’un large éventail de sources vers les destinations en quelques minutes. Après avoir couvert par le biais de l’Assistant, un pipeline avec une activité de copie est automatiquement créé pour vous avec des entités de données usine dépendantes (services liés et des groupes de données). Aucune des étapes supplémentaires ne sont requises pour créer le pipeline.   

![Sélectionnez la source de données](./media/data-factory-copy-wizard/select-data-source-page.png)

> [AZURE.NOTE] Reportez-vous à l’article [didacticiel de l’Assistant de copie](data-factory-copy-data-wizard-tutorial.md) pour des instructions pas à pas créer un tuyau exemple pour copier des données à partir d’un Azure blob à une table de base de données de SQL Azure. 

L’Assistant est conçu avec des données volumineuses à l’esprit dès le début. Il est simple et efficace pour créer des pipelines de données usine qui déplacent des centaines de tables à l’aide de l’Assistant de copie de données, des fichiers ou des dossiers. L’Assistant prend en charge les trois fonctionnalités suivantes : aperçu de données automatique, capture du schéma et le mappage et le filtrage de données. 

## <a name="automatic-data-preview"></a>Aperçu de données automatique 
L’Assistant copie vous permet de passer en revue une partie des données à partir de la source de données sélectionnée pour vous permettre de vérifier si les données sont les bonnes données que vous souhaitez copier. En outre, si la source de données est dans un fichier texte, l’Assistant copie analyse le fichier texte pour en savoir plus automatiquement les séparateurs de colonne et de ligne et de schéma. 

![Paramètres de format de fichier](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Mappage et capture de schéma 
Le schéma des données d’entrée peut-être correspond pas le schéma de données de sortie dans certains cas. Dans ce scénario, vous devez mapper les colonnes à partir du schéma de la source aux colonnes du schéma de destination. 

L’Assistant copie mappe automatiquement les colonnes dans le schéma source vers les colonnes dans le schéma de destination. Vous pouvez remplacer les mappages à l’aide de la liste déroulante (ou) spécifier si une colonne doit être ignorée lors de la copie des données.   

![Mappage de schéma](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrage des données  
L’Assistant vous permet de filtrer la source de données pour sélectionner uniquement les données qui doivent être copiés dans le magasin de données de destination/récepteur. Le filtrage permet de réduire le volume de données à copier dans le magasin de données du récepteur et par conséquent améliore le débit de l’opération de copie. Il offre un moyen souple pour filtrer les données dans une base de données relationnelle à l’aide de SQL query language (ou) les fichiers dans un dossier de blob Azure à l’aide de [variables et les fonctions de Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrage des données dans une base de données  
Dans l’exemple, la requête SQL utilise le `Text.Format` fonction et `WindowStart` variable. 

![Valider les expressions](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrage de données dans un dossier de blob Azure
Vous pouvez utiliser des variables dans le chemin d’accès du dossier pour copier des données à partir d’un dossier qui est déterminé au moment de l’exécution en fonction des [variables de système](data-factory-functions-variables.md#data-factory-system-variables). Les variables prises en charge sont : **{year}**, **{mois}**, **{day}**, **{heure}**, **{minute}**et **{personnalisé}**. Exemple : inputfolder / {year} / {mois} / {jour}.

Supposons que vous avez entrée dossiers au format suivant :

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Cliquez sur le bouton **Parcourir** pour le **fichier ou le dossier**, accédez à l’un de ces dossiers (par exemple, 2016 -> 03 -> 01 -> 02), cliquez sur **Choisir**. Vous devriez voir `2016/03/01/02` dans la zone de texte. Maintenant, remplacez **2016** **{year}**, **03** **{**mois}, **01** avec **{day}**et **02** avec **{heure}**et appuyez sur Tab. Vous devriez voir les listes déroulantes pour sélectionner le format de ces quatre variables :

![Utilisation des variables système](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Comme illustré dans la capture d’écran suivante, vous pouvez également utiliser une variable **personnalisée** et une [prise en charge des chaînes de format](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Pour sélectionner un dossier avec cette structure, utilisez le bouton **Parcourir** en premier. Remplacer une valeur **{personnalisé}**, puis appuyez sur Tab pour afficher la zone de texte dans laquelle vous pouvez taper la chaîne de format.     

![À l’aide d’une variable personnalisée](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)


## <a name="support-for-diverse-data-and-object-types"></a>Prise en charge des types d’objets et de données diverses
À l’aide de l’Assistant de copie, vous pouvez déplacer efficacement des centaines de dossiers, des fichiers ou des tables.

![Sélectionnez les tables à partir de laquelle copier les données](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Options de planification
Vous pouvez exécuter l’opération de copie une fois ou selon un planning (toutes les heures, tous les jours, et ainsi de suite). Ces deux options peuvent être utilisés pour la gamme des connecteurs sur site, nuage et de copie de bureau local.

Une opération de copie unique permet le déplacement de données depuis une source vers une destination qu’une seule fois. Il s’applique aux données de n’importe quelle taille et n’importe quel format pris en charge. La copie planifiée vous permet copier des données sur une périodicité prescrite. Vous pouvez utiliser des paramètres riches (comme les nouvelles tentatives, délai d’expiration et les alertes) pour configurer la copie programmée.

![Propriétés de planification](./media/data-factory-copy-wizard/scheduling-properties.png)


## <a name="next-steps"></a>Étapes suivantes
Pour un bref aperçu de l’utilisation de l’Assistant de copie de données en usine pour créer une opportunité avec l’activité de la copie, voir [didacticiel : créer un tuyau à l’aide de l’Assistant de copie](data-factory-copy-data-wizard-tutorial.md).
