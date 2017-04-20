<properties 
    pageTitle="Se connecter à un compte DocumentDB avec prise en charge de protocole de MongoDB | Microsoft Azure" 
    description="Apprenez à vous connecter à un compte DocumentDB avec prise en charge de protocole de MongoDB, désormais disponible pour l’aperçu. Connectez-vous en utilisant votre chaîne de connexion de MongoDB." 
    keywords="chaîne de connexion de MongoDB"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>

# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>Comment se connecter à un compte DocumentDB avec prise en charge de protocole de MongoDB

Apprenez à vous connecter à un compte Azure DocumentDB avec prise en charge de protocole de MongoDB en utilisant le format d’URI de chaîne de connexion MongoDB standard.  

## <a name="get-the-accounts-connection-string-information"></a>Obtenir des informations de chaîne de connexion du compte

1. Dans une nouvelle fenêtre, connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre de **Navigation latérale gauche** de la lame de compte, cliquez sur **La chaîne de connexion**. Pour accéder à la **Lame de compte**, sur le Jumpbar cliquez sur **Plus de Services**et cliquez sur **DocumentDB (NoSQL)** , puis sélectionnez le compte de DocumentDB avec prise en charge de protocole de MongoDB.

    ![Capture d’écran de la lame de tous les paramètres](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. La blade **d’Informations de chaîne de connexion** s’ouvre et a toutes les informations nécessaires pour vous connecter sur le compte à l’aide d’un pilote de MongoDB, y compris une chaîne de connexion préalable construit.

    ![Capture d’écran de la lame de chaîne de connexion](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Exigences de chaîne de connexion

Il est important de noter que DocumentDB prend en charge le standard MongoDB connexion chaîne format d’URI, avec un certain nombre d’exigences spécifiques : DocumentDB comptes requièrent l’authentification et communication sécurisée via SSL.  Ainsi, le format de chaîne de connexion est :

    mongodb://username:password@host:port/[database]?ssl=true

Où les valeurs de cette chaîne sont disponibles dans la lame de la chaîne de connexion ci-dessus.

- Nom d’utilisateur (obligatoire)
    - Nom de compte DocumentDB
- Mot de passe (obligatoire)
    - Mot de passe de compte DocumentDB
- Hôte (obligatoire)
    - Compte du nom de domaine complet de le DocumentDB
- Port (obligatoire)
    - 10250
- Base de données (facultatif)
    - La base de données par défaut utilisé par la connexion
- SSL = true (obligatoire)

Par exemple, considérez le compte indiqué dans les informations de chaîne de connexion ci-dessus.  Une chaîne de connexion valide est :
    
    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c-driver-for-mongodb"></a>Connectez le pilote C# pour MongoDB
Comme déjà mentionné, tous les comptes de DocumentDB requièrent l’authentification et une communication sécurisée via SSL. Le format URI de chaîne de connexion MongoDB prend en charge une connexion ssl = paramètre de chaîne de requête true, travailler avec le MongoDB C# pilote requiert l’utilisation de l’objet MongoClientSettings lors de la création d’un MongoClient.  Étant donné les informations ci-dessus, l’extrait de code suivant montre comment se connecter au compte et de travailler avec la base de données « Tâches ».

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
    

## <a name="next-steps"></a>Étapes suivantes


- Découvrez comment [utiliser MongoChef](documentdb-mongodb-mongochef.md) avec un compte de DocumentDB avec le protocole de prise en charge de MongoDB.
- Explorez le DocumentDB avec prise en charge de protocole de MongoDB [échantillons](documentdb-mongodb-samples.md).

 
