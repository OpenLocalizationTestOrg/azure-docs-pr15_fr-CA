<properties
    pageTitle="Se connecter à la base de données de SQL en utilisant les Python | Microsoft Azure"
    description="Présente un exemple de code Python que vous pouvez utiliser pour vous connecter à la base de données de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-python"></a>Se connecter à la base de données de SQL en utilisant les Python


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Cette rubrique indique comment se connecter et d’interroger une base de données de SQL Azure en utilisant les Python. Vous pouvez exécuter cet exemple à partir de la plate-forme Windows, Ubuntu Linux ou Mac.


## <a name="step-1-create-a-sql-database"></a>Étape 1 : Création d’une base de données SQL

Consultez la [page de mise en route](sql-database-get-started.md) pour savoir comment créer une base de données exemple.  Il est important que vous suivez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**. Une fois que vous créez votre marque de la base de données que vous activez l’accès à votre adresse IP en activant les règles de pare-feu, comme décrit dans la [page mise en route](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>Étape 2 : Configurer l’environnement de développement

### <a name="mac-os"></a>**Mac OS**   
### <a name="install-the-required-modules"></a>Installez les modules requis
Ouvrez votre terminal et installer

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Ouvrez votre terminal et accédez à un répertoire dans lequel vous prévoyez sur la création de script de votre python. Entrez les commandes suivantes pour installer **FreeTDS** et **pymssql**. pymssql FreeTDS pour se connecter aux bases de données SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1
    
### <a name="windows"></a>**Windows**

Installez pymssql à partir [**d’ici**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Assurez-vous de que choisir le fichier approprié whl. Par exemple : Si vous utilisez les Python 2.7 sur un ordinateur 64 bits : pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Une fois que vous téléchargez le fichier .whl le placer dans le dossier C:/Python27.

Installer le pilote de pymssql à l’aide du pip à partir de la ligne de commande. CD C:/Python27 et exécuter les opérations suivantes
    
    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Vous pouvez trouver des instructions pour activer le pip utiliser [ici](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="step-3-run-sample-code"></a>Étape 3 : Exécution des exemples de code

Créez un fichier nommé **sql_sample.py** et collez le code suivant à l’intérieur. Vous pouvez effectuer cette opération à partir de la ligne de commande à l’aide de :
    
    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Se connecter à votre base de données SQL

La fonction [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) est utilisée pour se connecter à la base de données de SQL.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Exécuter une instruction SQL SELECT

La fonction [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) peut être utilisée pour récupérer un jeu de résultats d’une requête sur base de données de SQL. Cette fonction accepte essentiellement n’importe quelle requête et renvoie qu'un jeu de résultats qui peut être parcourue sur l’utilisation de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Insérer une ligne, passer des paramètres et récupérer la clé primaire générée

Dans la base de données de SQL la propriété [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) et l’objet de [séquence](https://msdn.microsoft.com/library/ff878058.aspx) peuvent être utilisés pour générer automatiquement des valeurs de [clé primaire](https://msdn.microsoft.com/library/ms179610.aspx) . 


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transactions


Cet exemple de code illustre l’utilisation de transactions dans lequel vous :

* Commencer une transaction
* Insérer une ligne de données
* ROLLBACK transaction pour annuler l’insertion 

Collez le code suivant dans sql_sample.py.
    
    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Étapes suivantes

* Consultez [vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* Plus d’informations sur le [Pilote de Python de Microsoft pour SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Visitez le [Centre de développement de Python](/develop/python/).

## <a name="additional-resources"></a>Ressources supplémentaires 

* [Modèles de design pour les Applications de base de données SQL Azure mutualisée SaaS pour](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Découvrez toutes les [fonctionnalités de base de données de SQL](https://azure.microsoft.com/services/sql-database/)
