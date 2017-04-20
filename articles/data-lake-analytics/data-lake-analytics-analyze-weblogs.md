<properties
   pageTitle="Analyser les journaux du site Web à l’aide d’Analytique de LAC de données Azure | Azure"
   description="Découvrez comment analyser les journaux du site Web à l’aide de données lac Analytique. "
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

# <a name="tutorial-analyze-website-logs-using-azure-data-lake-analytics"></a>Didacticiel : Analyser les journaux du site Web à l’aide d’Azure données lac Analytique

Découvrez comment analyser les journaux du site Web à l’aide de données Analytique de LAC, en particulier sur les trouver les points d’accès rencontré des erreurs lorsqu’ils ont essayé de visiter le site Web.

>[AZURE.NOTE] Si vous souhaitez simplement voir l’application, il fait gagner du temps à passer par [utilisation Azure données lac Analytique des didacticiels interactifs](data-lake-analytics-use-interactive-tutorials.md). Ce didacticiel est basé sur le même scénario et le même code. Ce didacticiel vise à permettre aux développeurs de l’expérience de création et d’exécution d’une application de données lac Analytique de bout en bout.

## <a name="prerequisites"></a>Conditions préalables :

- **2015 de Visual Studio, Visual Studio 2013 mise à jour 4, ou Visual Studio 2012 avec l’installation de Visual C++**.
- **Kit de développement Microsoft Azure pour .NET version 2.5 ou supérieure**.  Installez-le à l’aide du [programme d’installation de la plate-forme Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Données lac Tools pour Visual Studio](http://aka.ms/adltoolsvs)**.

    Une fois que les données des outils de LAC pour Visual Studio est installé, vous verrez un menu **Lac de données** dans Visual Studio :

    ![Menu de Visual Studio de U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Connaissances de base de données lac Analytique et les outils du lac de données pour Visual Studio**. Pour commencer, consultez :

    - [Mise en route d’Analytique de LAC de données Azure à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).
    - [Script de développer U-SQL à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

- **Un compte Analytique lac de données.**  Voir [Création d’un compte Analytique de LAC de données Azure](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

    Les outils lac de données ne prend pas en charge la création de comptes de données lac Analytique.  Vous devez donc créer à l’aide du Azure Portal, PowerShell d’Azure, le Kit de développement .NET ou Azure CLI.
- **Télécharger les exemples de données pour le compte de données lac Analytique.** Voir [SearchLog.tsv de téléchargement pour le compte de stockage lac de données par défaut](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Pour exécuter une tâche de données lac Analytique, vous aurez besoin de certaines données. Même si les outils de LAC de données prend en charge les données de téléchargement, vous allez utiliser le portail pour télécharger les exemples de données pour faciliter la suivre ce didacticiel.

## <a name="connect-to-azure"></a>Se connecter à Azure

Avant de pouvoir générer et tester des scripts SQL-U, vous devez d’abord vous connecter à Azure.

**Pour vous connecter à des données lac Analytique**

1. Ouvrez Visual Studio.
2. Dans le menu **Données lac** , cliquez sur **Options et les paramètres**.
4. Cliquez sur la **Connexion**, ou **Changer d’utilisateur** si un utilisateur est connecté et suivez les instructions.
5. Cliquez sur **OK** pour fermer la boîte de dialogue Options et les paramètres.

**Pour parcourir vos comptes de données lac Analytique**

1. À partir de Visual Studio, ouvrez **l’Explorateur de serveurs** par appuyer sur **CTRL + ALT + S**.
2. Dans l' **Explorateur de serveurs**, puis développez **Azure**et **Données lac Analytique**. Est d’afficher la liste de vos comptes de données lac Analytique s’il en existe. Impossible de créer des comptes de données lac Analytique à partir du studio. Pour créer un compte, consultez [Mise en route d’Analytique de LAC de données Azure à l’aide du portail Azure](data-lake-analytics-get-started-portal.md) ou [Mettre en route Analytique de LAC de données Azure à l’aide de PowerShell d’Azure](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Développement d’application d’U-SQL

Une application SQL-U est principalement un script SQL de l’U. Pour en savoir plus sur U-SQL, reportez-vous à la section [mise en route de SQL-U](data-lake-analytics-u-sql-get-started.md).

Vous pouvez ajouter des opérateurs définis par l’utilisateur d’addition à l’application.  Pour plus d’informations, consultez [opérateurs pour les tâches de données lac Analytique défini par l’utilisateur de développer le U-SQL](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Pour créer et soumettre une tâche de données lac Analytique**

1. Dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.
2. Sélectionnez le type de projet de U-SQL.

    ![nouveau projet de Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Cliquez sur **OK**. Visual studio crée une solution avec un fichier Script.usql.
4. Entrez le script suivant dans le fichier Script.usql :

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Pour comprendre l’U-SQL, reportez-vous à la section [mise en route de données lac Analytique U-SQL language](data-lake-analytics-u-sql-get-started.md).    

5. Ajouter un nouveau script SQL-U à votre projet et entrez les informations suivantes :

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();

6. Basculer vers le premier script SQL-U et en regard du bouton **Envoyer** , spécifiez votre compte Analytique.
7. À partir **L’Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Générer un Script**. Vérifier les résultats dans le volet de sortie.
8. À partir **L’Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Envoyer le Script**.
9. Vérifiez que le **Compte d’Analytique** est celui où vous souhaitez exécuter la tâche, puis cliquez sur **Envoyer**. Résultats de l’envoi et le lien de tâche sont disponibles dans les outils du lac de données de la fenêtre des résultats de Visual Studio lorsque la présentation est terminée.
10. Attendez que la tâche est terminée avec succès.  Si la tâche a échoué, le fichier source est probablement manquant.  Reportez-vous à la section Configuration requise de ce didacticiel. Pour plus d’informations, consultez [moniteur et résoudre les problèmes de travaux d’Azure données lac Analytique](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Lorsque la tâche est terminée, vous doit voir l’écran suivante :

    ![analytique de LAC données analyse les journaux de site Web de blogs](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Répétez les étapes 7 à 10 maintenant pour **Script1.usql**.

>[AZURE.NOTE]Vous ne pouvez pas lire ou écrire dans une table de U-SQL qui a été créée ou modifiée dans le même script.  C’est pourquoi utiliser deux scripts de cet exemple.

**Pour afficher la sortie de la tâche**

1. À partir de l' **Explorateur de serveurs**, développez **Azure**, développez **Données lac Analytique**, développez votre compte Analytique lac de données, développez des **Comptes de stockage**, droit sur le compte de stockage lac de données par défaut, puis cliquez sur **Explorer**.
2.  Double-cliquez sur **échantillons** pour ouvrir le dossier et ensuite double-cliquez sur **sorties**.
3.  Double-cliquez sur **UnsuccessfulResponsees.log**.
4.  Vous pouvez également double-cliquer sur le fichier de sortie à l’intérieur de l’affichage graphique de la tâche pour accéder directement à la sortie.

## <a name="see-also"></a>Voir aussi

Pour vous familiariser avec Analytique de LAC de données à l’aide de différents outils, voir :

- [Mise en route d’Analytique de LAC de données à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Mise en route de données Analytique de LAC à l’aide de PowerShell d’Azure](data-lake-analytics-get-started-powershell.md)
- [Mise en route de données Analytique de LAC à l’aide du Kit de développement .NET](data-lake-analytics-get-started-net-sdk.md)

Pour afficher davantage de rubriques de développement :

- [Développer des scripts SQL-U à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Mise en route de la langue du lac de données Azure Analytique U-SQL](data-lake-analytics-u-sql-get-started.md)
- [Développer les opérateurs U-SQL définie par l’utilisateur pour les tâches de données lac Analytique](data-lake-analytics-u-sql-develop-user-defined-operators.md)
