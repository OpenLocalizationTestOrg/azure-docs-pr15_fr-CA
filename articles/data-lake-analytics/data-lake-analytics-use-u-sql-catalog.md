<properties
   pageTitle="Introduire le catalogue du lac de données Azure Analytique U-SQL | Azure"
   description="Introduire le catalogue du lac de données Azure Analytique U-SQL"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="use-u-sql-catalog"></a>Ouvrir le catalogue SQL-U

Le catalogue SQL-U permet de structurer les données et le code afin qu’ils puissent être partagés par les scripts SQL de l’U. Le catalogue permet les meilleures performances possibles avec les données dans Azure données lac.

Chaque compte Analytique de LAC de données Azure a exactement un catalogue U-SQL associé. Vous ne pouvez pas supprimer le catalogue SQL de l’U. Actuellement, les catalogues SQL-U ne peut être partagées entre les comptes de la banque de données lac.

Chaque catalogue U-SQL contient une base de données **Master**. Impossible de supprimer la base de données Master.  Chaque catalogue U-SQL peut contenir plusieurs bases de données supplémentaires.

Contient de la base de données de U-SQL :

- Assemblys – partager le code .NET entre les scripts SQL de l’U.
- Fonctions des valeurs de la table : partager du code d’U-SQL entre les scripts SQL de l’U.
- Tables à partager des données entre les scripts SQL de l’U.
- Schémas - partagez des schémas de table entre les scripts SQL de l’U.

## <a name="manage-catalogs"></a>Gestion des catalogues
Chaque compte Analytique de LAC de données Azure a un compte de banque de LAC de données Azure par défaut lui est associé. Ce compte de banque de données lac est désigné comme le compte de la banque de données lac par défaut. Catalogue de U-SQL est stockée dans le compte de la banque de données lac par défaut sous le dossier /catalog. Ne supprimez pas les fichiers dans le dossier /catalog.

### <a name="use-azure-portal"></a>Utilisez le portail Azure

Reportez-vous à la section [Analytique de LAC données gérer à l’aide de portail](data-lake-analytics-manage-use-portal.md#view-u-sql-catalog)


### <a name="use-data-lake-tools-for-visual-studio"></a>Utilisez les outils de LAC de données pour Visual Studio.

Vous pouvez utiliser les outils de LAC de données pour Visual Studio pour gérer le catalogue.  Pour plus d’informations sur les outils, voir [Outils de LAC de données à l’aide de Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**Pour gérer le catalogue**

1. Ouvrez Visual Studio et connectez-vous à azure. Pour les instructions, voir [se connecter à Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
1. Ouvrir **Explorateur de serveurs** , appuyez sur **CTRL + ALT + S**.
2. Dans l' **Explorateur de serveurs**, développez **Azure**, développez **Données lac Analytique**, développez votre compte Analytique lac de données, développez **bases de données**, puis puis **maître**.



    - Pour ajouter une nouvelle base de données, cliquez sur **base de données**, puis cliquez sur **Créer une base de données**.
    - Pour ajouter un nouvel assembly, droit **d’assemblys**, puis cliquez sur **Enregistrer l’Assembly**.
    - Pour ajouter un nouveau schéma, **schémas**d’avec le bouton droit, puis cliquez sur « Create Schema **.
    - Pour ajouter une nouvelle table, cliquez sur **Tables**, puis cliquez sur « "Créer Table **.
    - Pour ajouter une nouvelle fonction de table, consultez [opérateurs pour les tâches de données lac Analytique défini par l’utilisateur de développer le U-SQL](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Parcourir les catalogues de Visual Studio U-SQL](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## <a name="see-also"></a>Voir aussi

- Mise en route
    - [Mise en route de données Analytique de LAC avec Azure portal](data-lake-analytics-get-started-portal.md)
    - [Mise en route de données Analytique de LAC à l’aide de PowerShell d’Azure](data-lake-analytics-get-started-powershell.md)
    - [Mise en route de données Analytique de LAC à l’aide du Kit de développement .NET Azure](data-lake-analytics-get-started-net-sdk.md)
    - [Développer des scripts SQL-U à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Mise en route de la langue du lac de données Azure Analytique U-SQL](data-lake-analytics-u-sql-get-started.md)

- U-SQL & développement
    - [Mise en route de la langue du lac de données Azure Analytique U-SQL](data-lake-analytics-u-sql-get-started.md)
    - [Utilisez les fonctions de fenêtre U-SQL pour les travaux de Azure données lac Analytique](data-lake-analytics-use-window-functions.md)
    - [Développer les opérateurs U-SQL définie par l’utilisateur pour les tâches de données lac Analytique](data-lake-analytics-u-sql-develop-user-defined-operators.md)

- Gestion des
    - [Gérer l’Analytique de LAC de données Azure à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
    - [Gérer l’Analytique de LAC de données Azure à l’aide de PowerShell d’Azure](data-lake-analytics-manage-use-powershell.md)
    - [Surveiller et résoudre les problèmes de travaux Azure données lac Analytique à l’aide d’Azure portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Didacticiel de bout en bout
    - [Utiliser les didacticiels interactifs Azure données lac Analytique](data-lake-analytics-use-interactive-tutorials.md)
    - [Analyser les journaux du site Web à l’aide d’Azure données lac Analytique](data-lake-analytics-analyze-weblogs.md)
