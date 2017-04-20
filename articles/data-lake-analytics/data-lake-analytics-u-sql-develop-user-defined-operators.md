<properties 
   pageTitle="Développer les opérateurs U-SQL définie par l’utilisateur pour les travaux d’Analytique de LAC de données Azure | Azure" 
   description="Apprenez à développer des opérateurs définis par l’utilisateur pour être utilisés et réutilisés dans les traitements de données lac Analytique. " 
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


# <a name="develop-u-sql-user-defined-operators-for-azure-data-lake-analytics-jobs"></a>Développer les opérateurs U-SQL définie par l’utilisateur pour les travaux d’Azure données lac Analytique

Apprenez à développer des opérateurs définis par l’utilisateur pour être utilisés et réutilisés dans les traitements de données lac Analytique. Vous allez développer un opérateur personnalisé pour convertir les noms de pays.

##<a name="prerequisites"></a>Conditions préalables

- 2015 de Visual Studio, Visual Studio 2013 mise à jour 4, ou Visual Studio 2012 avec Visual C++ installé 
- Microsoft Azure SDK pour .NET version 2.5 ou supérieure.  Installer à l’aide de Web platform installer.
- Un compte Analytique lac de données.  Reportez-vous à la section [Mise en route d’Analytique de LAC de données Azure à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).
- Passez en revue le didacticiel [mise en route de Studio de U-SQL Azure données lac Analytique](data-lake-analytics-u-sql-get-started.md) .
- Se connecter à Azure, reportez-vous à la section [mise en route de Studio de U-SQL Azure données lac Analytique](data-lake-analytics-u-sql-get-started.md#connect-to-azure). 
- Télécharger la source de données, consultez [mise en route de Studio de U-SQL Azure données lac Analytique](data-lake-analytics-u-sql-get-started.md#upload-source-data-files). 

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>Définir et utiliser un opérateur de défini par l’utilisateur dans SQL-U

**Pour créer et soumettre un travail U-SQL** 

1. Dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.
2. Sélectionnez le type de **Projet de U-SQL** .

    ![nouveau projet de Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Cliquez sur **OK**. Visual studio crée une solution avec un fichier Script.usql.
4. À partir **L’Explorateur de solutions**, développez Script.usql, puis double-cliquez sur **Script.usql.cs**.
5. Collez le code suivant dans le fichier :

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;
        
        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };
        
                public override IRow Process(IRow input, IUpdatableRow output)
                {
        
                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");
        
                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);
        
                    return output.AsReadOnly();
                }
            }
        }

5. Ouvrez Script.usql et collez le script U-SQL suivant :

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);
        
        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    
        
        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);

6. À partir **L’Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Générer un Script**.
6. À partir **L’Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Envoyer le Script**.
7. Si vous n’avez pas à vous connecter à votre abonnement Azure, il se peut que vous ne serez invite à entrer vos informations d’identification de compte Azure.
7. Cliquez sur **Envoyer**. Résultats de l’envoi et le lien de tâche sont disponibles dans la fenêtre de résultats lorsque la présentation est terminée.
8. Vous devez cliquer sur le bouton Actualiser pour afficher le dernier état de la tâche et d’actualiser l’écran.

**Pour afficher la sortie de la tâche**

1. À partir de l' **Explorateur de serveurs**, développez **Azure**, développez **Données lac Analytique**, développez votre compte Analytique lac de données, développez des **Comptes de stockage**, droit le stockage par défaut, puis cliquez sur **Explorer**. 
2. Développez des échantillons, sorties et puis double-cliquez sur **Drivers.csv**.


##<a name="see-also"></a>Voir aussi

- [Mise en route de données Analytique de LAC à l’aide de PowerShell](data-lake-analytics-get-started-powershell.md)
- [Mise en route de données Analytique de LAC via le portail Azure](data-lake-analytics-get-started-portal.md)
- [Utilisez les outils de LAC de données de Visual Studio pour développer des applications SQL-U](data-lake-analytics-data-lake-tools-get-started.md)
