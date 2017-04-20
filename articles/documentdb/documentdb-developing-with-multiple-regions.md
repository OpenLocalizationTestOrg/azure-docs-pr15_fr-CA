<properties
   pageTitle="Développement avec plusieurs régions avec DocumentDB | Microsoft Azure"
   description="Découvrez comment accéder à vos données dans plusieurs régions d’Azure, DocumentDB, un service de base de données NoSQL entièrement géré."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="kipandya"/>
   
# <a name="developing-with-multi-region-documentdb-accounts"></a>Développement avec les comptes de DocumentDB multizone

> [AZURE.NOTE] La distribution mondiale des bases de données DocumentDB est disponible et activée automatiquement pour tous les comptes DocumentDB nouvellement créés. Nous nous efforçons d’activer la distribution globale sur tous les comptes existants, mais en attendant, si vous souhaitez que la distribution globale activée sur votre compte, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et nous vous activer pour vous maintenant.

Pour tirer parti de [distribution globale](documentdb-distribute-data-globally.md), les applications clientes peuvent spécifier la liste de préférence ordonnée des régions à utiliser pour effectuer des opérations de document. Pour ce faire, vous pouvez définir la stratégie de connexion. En fonction de la configuration du compte Azure DocumentDB, disponibilité régionale actuelle et la liste de préférence spécifiée, le point de terminaison optimal est choisi par le SDK pour effectuer l’écriture et les opérations de lecture. 

Cette liste de préférence est spécifiée lors de l’initialisation d’une connexion à l’aide de kits de développement logiciel client DocumentDB. Les kits de développement acceptent un paramètre facultatif « PreferredLocations » qui est une liste ordonnée des régions Azure.

Le Kit de développement logiciel enverra automatiquement toutes les écritures en cours écrire la région. 

Vous recevrez toutes les lectures à la première zone disponible dans la liste de PreferredLocations. Si la demande échoue, le client échoue vers le bas de la liste à la région suivante et ainsi de suite. 

Le client que tentent uniquement de kits de développement logiciel de lecture des régions spécifié dans PreferredLocations. Ainsi, par exemple, si le compte de base de données est disponible dans trois régions, mais le client spécifie uniquement deux des régions non-écriture de PreferredLocations, puis aucune lecture n’est fournis en dehors de la zone d’écriture, même en cas de basculement.

L’application peut vérifier le point de terminaison actuel de l’écriture et lire le point de terminaison choisi par le Kit de développement en vérifiant les deux propriétés, WriteEndpoint et ReadEndpoint, disponible dans la version du Kit de développement logiciel 1.8 et au-dessus. 

Si la propriété PreferredLocations n’est pas définie, toutes les demandes seront servis à partir de la zone d’écriture en cours. 


## <a name="net-sdk"></a>KIT DE DÉVELOPPEMENT .NET
Le SDK peut être utilisé sans aucune modification de code. Dans ce cas, le Kit de développement logiciel automatiquement dirige les opérations de lecture et les écrit dans la zone d’écriture en cours. 

Dans la version 1.8 et ultérieures du Kit de développement .NET, le paramètre ConnectionPolicy pour le constructeur DocumentClient a une propriété appelée Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Cette propriété est de type Collection `<string>` et doit contenir une liste de noms de région. Les valeurs de chaîne sont mis en forme par la colonne nom de la région sur les [Régions d’Azure]  [ regions] page, sans espace avant ou après le premier et le dernier caractère respectivement.

L’écriture en cours et la lecture des points de terminaison sont disponibles dans DocumentClient.WriteEndpoint et DocumentClient.ReadEndpoint respectivement.

> [AZURE.NOTE] Les URL pour les points de terminaison ne doivent pas être considérées comme des constantes de longue durées. Le service peut mettre à jour ces à tout moment. Le Kit de développement logiciel, gère automatiquement cette modification.

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

    //Setting read region selection preference 
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB 
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS, JavaScript et les Python SDK
Le SDK peut être utilisé sans aucune modification de code. Dans ce cas, le Kit de développement vous redirige automatiquement les lectures et les écritures à actuel écrivent la région. 

Dans la version 1.8 et versions ultérieure de chaque kit de développement logiciel, le paramètre ConnectionPolicy pour le constructeur de DocumentClient une nouvelle propriété appelée DocumentClient.ConnectionPolicy.PreferredLocations. Ce paramètre est un tableau de chaînes qui prend une liste de noms de région. Les noms sont mis en forme par la colonne nom de la région dans les [Régions d’Azure]  [ regions] page. Vous pouvez également utiliser des constantes prédéfinies dans l’objet de pratiques AzureDocuments.Regions

L’écriture en cours et la lecture des points de terminaison sont disponibles dans DocumentClient.getWriteEndpoint et DocumentClient.getReadEndpoint respectivement.

> [AZURE.NOTE] Les URL pour les points de terminaison ne doivent pas être considérées comme des constantes de longue durées. Le service peut mettre à jour ces à tout moment. Le Kit de développement logiciel gère automatiquement cette modification.

Vous trouverez ci-dessous un exemple de code pour NodeJS ou Javascript. Python et Java suivent le même modèle.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();
    
    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];
    
    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>RESTE 
Une fois un compte de base de données a été rendu disponible dans plusieurs régions, les clients peuvent interroger sa disponibilité en effectuant une demande GET sur l’URI suivant.

    https://{databaseaccount}.documents.azure.com/

Le service renverra une liste de régions et de leur DocumentDB point de terminaison correspondant URI pour les réplicas. La zone d’écriture en cours est indiquée dans la réponse. Le client peut ensuite sélectionner le point de terminaison approprié pour toutes les autres API REST demandes comme suit.

Réponse de l’exemple

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


-   Demandes ensemble PUT, POST et DELETE doivent atteindre l’écriture indiqué URI
-   Obtient toutes les et les autres demandes en lecture seule (par exemple les requêtes) peuvent accéder à n’importe quel point de terminaison de choix du client

Écrire des demandes pour les zones en lecture seule échouent avec le code d’erreur HTTP 403 (« refusé »).

Si la zone d’écriture change après la phase de découverte initiale du client, les écritures ultérieures à la zone d’écriture précédente échoue avec le code d’erreur HTTP 403 (« refusé »). Le client doit ensuite obtenir la liste des régions pour récupérer la zone d’écriture mis à jour.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les données de distribution global avec DocumentDB dans les articles suivants :

- [Distribuer des données globalement avec DocumentDB](documentdb-distribute-data-globally.md)
- [Niveaux de cohérence](documentdb-consistency-levels.md)
- [Fonctionne de débit avec plusieurs régions](documentdb-manage.md#how-throughput-works-with-multiple-regions)
- [Ajouter des zones à l’aide du portail Azure](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/ 
