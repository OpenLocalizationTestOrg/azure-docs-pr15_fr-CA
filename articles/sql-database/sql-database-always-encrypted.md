<properties
    pageTitle="Toujours crypté : Protéger des données sensibles dans la base de données de SQL Azure avec cryptage de la base de données | Microsoft Azure"
    description="Protéger les données sensibles de votre base de données SQL en quelques minutes."
    keywords="crypter des données, cryptage sql, cryptage de base de données, des données sensibles, toujours crypté"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="sstein"/>

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Toujours cryptées : Protéger les données sensibles dans la base de données de SQL et de stocker vos clés de chiffrement dans le magasin de certificats Windows

> [AZURE.SELECTOR]
- [Chambre forte de clé Azure](sql-database-always-encrypted-azure-key-vault.md)
- [Magasin de certificats Windows](sql-database-always-encrypted.md)


Cet article vous montre comment sécuriser des données sensibles dans une base de données SQL avec le cryptage de la base de données à l’aide de l' [Toujours les Assistant cryptés](https://msdn.microsoft.com/library/mt459280.aspx) dans [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Il vous montre également comment stocker vos clés de chiffrement dans le magasin de certificats Windows.

Toujours crypté est une nouvelle technologie de chiffrement de données dans la base de données de SQL Azure et SQL Server qui vous aide à protéger les données sensibles au repos sur le serveur, pendant le mouvement entre le client et le serveur pendant que les données sont en cours d’utilisation, garantissant que les données sensibles ne s’affiche en texte brut dans le système de base de données. Après avoir crypté les données, uniquement des applications clientes ou des serveurs d’applications qui ont accès à l’aide des touches peuvent accéder aux données de texte brut. Pour plus d’informations, consultez [Toujours cryptées (moteur de base de données)](https://msdn.microsoft.com/library/mt163865.aspx).

Après avoir configuré la base de données pour utiliser toujours cryptées, vous allez créer une application cliente dans C# avec Visual Studio pour travailler avec les données chiffrées.

Suivez les étapes décrites dans cet article pour savoir comment configurer une base de données SQL d’Azure toujours cryptées. Dans cet article, vous allez apprendre à effectuer les tâches suivantes :

- Utilisez l’Assistant toujours cryptées dans SSMS pour créer des [Clés de cryptage toujours](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Créer une [Clé principale de la colonne de (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Créer une [clé de cryptage de colonne (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Créer une table de base de données et de crypter des colonnes.
- Créer une application qui insère, sélectionne et affiche les données issues des colonnes cryptés.

## <a name="prerequisites"></a>Conditions préalables

Pour ce didacticiel, vous avez besoin :

- Un compte Azure et abonnement. Si vous n’en avez pas, inscrivez-vous pour une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) version 13.0.700.242 ou une version ultérieure.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou version ultérieure (sur l’ordinateur client).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).



## <a name="create-a-blank-sql-database"></a>Créer une base de données SQL vide
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Nouveau** > **données + stockage** > **base de données SQL**.
3. Créer une base de données **vide** nommée **Clinic** sur un serveur existant ou nouveau. Pour obtenir des instructions détaillées sur la création d’une base de données dans le portail Azure, voir [Création d’une base de données SQL en quelques minutes](sql-database-get-started.md).

    ![Créer une base de données vide](./media/sql-database-always-encrypted/create-database.png)

Plus loin dans le didacticiel, vous devez la chaîne de connexion. Une fois la base de données est créé, accédez à la nouvelle base de données du cours pratique et copier la chaîne de connexion. Vous pouvez obtenir la chaîne de connexion à tout moment, mais il est facile de copier lorsque vous êtes dans le portail Azure.

1. Cliquez sur **bases de données SQL** > **cours pratique** > **afficher des chaînes de connexion de base de données**.
2. Copier la chaîne de connexion **ADO.NET**.

    ![Copier la chaîne de connexion](./media/sql-database-always-encrypted/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>Se connecter à la base de données avec SSMS

Ouvrez les SSMS et connectez-vous au serveur avec la base de données du cours pratique.


1. Ouvrez SSMS. (Cliquez sur **se connecter** > **Moteur de base de données** pour ouvrir la fenêtre **connexion au serveur** s’il n’est pas ouvert).
2. Entrez votre nom de serveur et les informations d’identification. Le nom du serveur se trouve sur la lame de la base de données SQL et la chaîne de connexion que vous avez copié précédemment. Tapez le nom complet du serveur, y compris *database.windows.net*.

    ![Copier la chaîne de connexion](./media/sql-database-always-encrypted/ssms-connect.png)

Si la fenêtre **Nouvelle règle de pare-feu** s’ouvre, connectez-vous à Azure et SSMS vous permettent de créer une nouvelle règle de pare-feu pour vous.


## <a name="create-a-table"></a>Créer une table

Dans cette section, vous allez créer une table pour contenir les données des patients. Ce sera une table normale au départ, vous allez configurer le cryptage dans la section suivante.

1. Développez **bases de données**.
1. Avec le bouton droit de la base de données du **cours pratique** , puis cliquez sur **Nouvelle requête**.
2. Coller le suivant Transact-SQL (T-SQL) dans la nouvelle fenêtre de requête et **d’exécution** il.


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Crypter les colonnes (configurez toujours cryptées)

SSMS fournit un Assistant pour configurer facilement toujours cryptées en définissant le CMK, CEK et colonnes cryptés pour vous.

1. Développez **bases de données** > **cours pratique** > **Tables**.
2. Avec le bouton droit de la table **Patients** et sélectionnez **Chiffrer les colonnes** pour ouvrir l’Assistant toujours cryptées :

    ![Crypter les colonnes](./media/sql-database-always-encrypted/encrypt-columns.png)

L’Assistant toujours cryptées comprend les sections suivantes : **Sélection de la colonne**, la **Configuration de la clé principale** (CMK), **Validation**et **Résumé**.

### <a name="column-selection"></a>Sélection de la colonne ###

Sur la page **d’Introduction** pour ouvrir la page de **Sélection de la colonne** , cliquez sur **suivant** . Sur cette page, vous sélectionnez les colonnes que vous souhaitez crypter, [le type de cryptage et quelle clé de cryptage de colonne (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) à utiliser.

Chiffrer les informations de **numéro de sécurité sociale** et de la **date de naissance** pour chaque patient. La colonne **SSN** utilise le cryptage déterministe, qui prend en charge les recherches de l’égalité, les jointures et les regrouper par. La colonne **BirthDate** utilise le cryptage aléatoire, qui ne prend pas en charge les opérations.

Définir le **Type de chiffrement** pour la colonne **numéro de sécurité sociale** à **déterministes** et de la colonne **date de naissance** à **Randomized**. Cliquez sur **suivant**.

![Crypter les colonnes](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configuration de la clé principale###

La page **Configuration de la clé principale** est où vous définissez votre CMK et que vous sélectionnez le fournisseur de magasin de clés où sera stocké le CMK. Actuellement, vous pouvez stocker un CMK dans le magasin de certificats Windows Azure clé coffre-fort ou un module de sécurité matériel (HSM). Ce didacticiel montre comment stocker les clés dans le magasin de certificats Windows.

Vérifiez que le **magasin de certificats Windows** est sélectionné, puis cliquez sur **suivant**.

![Configuration de la clé principale](./media/sql-database-always-encrypted/master-key-configuration.png)


### <a name="validation"></a>Validation###

Vous pouvez crypter les colonnes maintenant ou enregistrez un script PowerShell pour une exécution ultérieure. Pour ce didacticiel, sélectionnez **Continuer pour terminer maintenant** et cliquez sur **suivant**.

### <a name="summary"></a>Résumé###

Vérifiez que les paramètres sont tous les corriger et cliquez sur **Terminer** pour terminer l’installation pour toujours cryptées.

![Résumé](./media/sql-database-always-encrypted/summary.png)


### <a name="verify-the-wizards-actions"></a>Vérifiez les actions de l’Assistant

Une fois l’Assistant terminé, votre base de données est configuré pour toujours cryptées. Les actions suivantes effectuées par l’Assistant :

- Permet de créer un CMK.
- Permet de créer un CEK.
- Configurer les colonnes sélectionnées pour le cryptage. Votre table de **Patients** a actuellement pas de données, mais les données existantes des colonnes sélectionnées sont désormais cryptées.

Vous pouvez vérifier la création des clés dans SSMS en accédant à **Clinic** > **sécurité** > **Toujours les clés cryptées**. Vous pouvez maintenant voir les nouvelles clés généré par l’Assistant pour vous.


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Créer une application cliente qui fonctionne avec les données chiffrées

Toujours crypté est configuré, vous pouvez créer une application qui effectue des *insertions* et *sélectionne* sur les colonnes cryptés. Pour exécuter l’exemple d’application, vous devez l’exécuter sur le même ordinateur où vous avez exécuté l’Assistant toujours cryptées. Pour exécuter l’application sur un autre ordinateur, vous devez déployer vos certificats toujours cryptées à l’ordinateur exécutant l’application cliente.  

> [AZURE.IMPORTANT] Votre application doit utiliser des objets [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) lors du passage de données en texte brut au serveur avec des colonnes de toujours cryptées. En passant des valeurs littérales sans utiliser d’objets SqlParameter provoquera une exception.


1. Ouvrez Visual Studio et créez une nouvelle application console C#. Assurez-vous que votre projet est à **.NET Framework 4.6** ou version ultérieure.
2. Nommez le projet **AlwaysEncryptedConsoleApp** et cliquez sur **OK**.

![Nouvelle application de console](./media/sql-database-always-encrypted/console-app.png)



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modifier votre chaîne de connexion pour activer toujours cryptées

Cette section explique comment activer toujours cryptées dans votre chaîne de connexion de base de données. Vous allez modifier l’application console que vous venez de créer dans la section suivante, « Toujours chiffré exemple d’application console ».


Pour activer toujours cryptées, vous avez besoin ajouter le mot clé de **Paramètre de cryptage de colonne** à votre chaîne de connexion et le définir sur **activé**.

Vous pouvez définir directement dans la chaîne de connexion, ou vous pouvez la définir à l’aide de [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). L’exemple d’application dans la section suivante montre comment utiliser **SqlConnectionStringBuilder**.

> [AZURE.NOTE] Il s’agit de la seule modification requise dans une application cliente spécifique à toujours cryptées. Si vous disposez d’une application existante qui stocke la chaîne de connexion en externe (c'est-à-dire, dans un fichier de configuration), vous ne pourrez pas activer toujours cryptées sans modification de code.


### <a name="enable-always-encrypted-in-the-connection-string"></a>Activez toujours cryptées dans la chaîne de connexion

Ajoutez le mot clé suivant à votre chaîne de connexion :

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Activez toujours crypté avec SqlConnectionStringBuilder

Le code suivant montre comment activer toujours cryptées en définissant le [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) sur [activé](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Toujours crypté exemple console d’application

Cet exemple montre comment :

- Modifiez votre chaîne de connexion pour activer toujours cryptées.
- Insérer des données dans les colonnes cryptés.
- Sélectionnez un enregistrement en filtrant une valeur spécifique dans une colonne chiffrée.

Remplacez le contenu de **Program.cs** par le code suivant. Remplacez la chaîne de connexion pour la variable globale connectionString dans la ligne directement au-dessus de la méthode Main de votre chaîne de connexion valide à partir du portail Azure. Il s’agit de la seule modification que vous devez apporter à ce code.

Exécutez l’application pour voir toujours cryptées en action.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
         VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }


## <a name="verify-that-the-data-is-encrypted"></a>Vérifiez que les données sont cryptées.

Vous pouvez rapidement vérifier que les données réelles sur le serveur sont cryptées en interrogeant les données **Patients** avec SSMS. (Utiliser la connexion en cours où le paramètre de cryptage de colonne n’est pas encore activé.)

Exécutez la requête suivante sur la base de données du cours pratique.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Vous pouvez voir que les colonnes cryptés ne contiennent pas toutes les données en texte brut.

   ![Nouvelle application de console](./media/sql-database-always-encrypted/ssms-encrypted.png)


Pour utiliser SSMS pour accéder aux données en texte brut, vous pouvez ajouter la **paramètre de cryptage de colonne = activé** paramètre à la connexion.

1. Avec le bouton droit de votre serveur dans **l’Explorateur d’objets**dans SSMS et puis cliquez sur **Déconnecter**.
2. Cliquez sur **se connecter** > **Moteur de base de données** pour ouvrir la fenêtre **connexion au serveur** , puis cliquez sur **Options**.
3. Cliquez sur **Paramètres de connexion supplémentaires** et tapez **paramètre de cryptage de colonne = activé**.

    ![Nouvelle application de console](./media/sql-database-always-encrypted/ssms-connection-parameter.png)

4. Exécutez la requête suivante sur la base de données du **cours pratique** .

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Vous pouvez maintenant voir les données en texte brut dans les colonnes cryptés.


    ![Nouvelle application de console](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [AZURE.NOTE] Si vous vous connectez avec SSMS (ou n’importe quel client) à partir d’un autre ordinateur, il n’aura pas accès aux clés de cryptage et ne sera pas en mesure de décrypter les données.



## <a name="next-steps"></a>Étapes suivantes
Après avoir créé une base de données utilise toujours cryptées, vous souhaiterez peut-être effectuer les opérations suivantes :

- Exécuter cet exemple à partir d’un autre ordinateur. Il n’aurez pas accès aux clés de cryptage, pour qu’il n’aura pas accès aux données en texte clair et ne fonctionnera pas correctement.
- [Faire pivoter et nettoyer vos clés de](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrer des données qui sont déjà cryptées avec toujours cryptées](https://msdn.microsoft.com/library/mt621539.aspx).
- [Déployez toujours cryptées des certificats à d’autres ordinateurs client](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (voir la section « Fabrication certificats disponibles pour Applications et utilisateurs »).

## <a name="related-information"></a>Informations connexes

- [Toujours cryptées (développement de clients)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Chiffrement de données transparent](https://msdn.microsoft.com/library/bb934049.aspx)
- [Chiffrement de SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Assistant toujours crypté](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog toujours crypté](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
