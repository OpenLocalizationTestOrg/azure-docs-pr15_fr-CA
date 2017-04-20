<properties 
    pageTitle="DocumentDB programmation : procédures stockées, les déclencheurs de base de données et fichiers UDF | Microsoft Azure" 
    description="Apprenez à utiliser DocumentDB pour écrire des procédures stockées, les déclencheurs de base de données et les fonctions de défini par l’utilisateur (UDF) dans JavaScript. Obtenez des conseils de programmation de base de données et bien plus encore." 
    keywords="Déclencheurs de base de données, une procédure stockée, une procédure stockée, programme de base de données, la procédure stockée, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="andrl"/>

# <a name="documentdb-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programmation côté serveur de DocumentDB : procédures stockées, les déclencheurs de base de données et fichiers UDF

Apprenez comment le langage de d’Azure DocumentDB intégré, exécution transactionnelle de JavaScript permet aux développeurs d’écrire des **procédures stockées**, les **déclencheurs** et **les fonctions définies par l’utilisateur** en mode natif dans JavaScript. Cela vous permet d’écrire une logique d’application de programme de base de données qui peut être livrée et exécutée directement sur les partitions de stockage de base de données 

Nous vous recommandons de mise en route en regardant la vidéo suivante, où Andrew Liu fournit une brève introduction au modèle de programmation côté serveur de base de données de DocumentDB. 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Ensuite, revenez à cet article, où vous découvrirez les réponses aux questions suivantes :  

- Comment écrire une une procédure stockée, le déclencheur ou la FDU à l’aide de JavaScript ?
- Comment DocumentDB ne garantit pas l’acide ?
- Comment fonctionnent les transactions dans DocumentDB ?
- Quels sont les déclencheurs et déclenche après et comment écrire une ?
- Comment enregistrer et exécuter une procédure stockée, déclencheur ou UDF de manière RESTful à l’aide de HTTP ?
- Les kits de développement logiciel DocumentDB sont disponibles pour créer et exécuter procédures stockées, déclencheurs et UDF ?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introduction à la programmation de FDU et de la procédure stockée

Cette approche de *« JavaScript comme un jour moderne T-SQL »* libère les développeurs d’applications à partir de la complexité des incompatibilités de système de type et de technologies de mappage objet-relationnel. Il a également un certain nombre d’avantages intrinsèques qui peuvent être utilisés pour créer des applications complexes :  

-   **Logique procédurale :** JavaScript comme un langage de programmation de niveau élevé fournit une interface riche et familière pour exprimer la logique métier. Vous pouvez effectuer des séquences complexes d’opérations de plus près aux données.

-   **Transactions atomiques :** DocumentDB garantit que les opérations de base de données à l’intérieur d’une seule procédure stockée ou déclencheur sont atomiques. Cela permet à une application de combiner les opérations connexes dans un seul lot afin que tous les réussissent ou aucune d'entre elles réussisse. 

-   **Performances :** Le fait que JSON est intrinsèquement mappé vers le système de type de langage Javascript et est également l’unité de stockage de DocumentDB permet d’obtenir des optimisations de matérialisation tardive de documents JSON dans le pool de tampons et de les rendre disponibles à la demande pour le code en cours d’exécution. Il existe plusieurs avantages de performances associés à livraison de logique métier pour la base de données :
    -   Le traitement par lots – les développeurs peuvent regrouper des opérations, telles que les insertions et les envoyer en vrac. La latence du trafic réseau de coût et la charge de la banque d’informations pour créer des transactions distinctes sont réduits de manière significative. 
    -   Précompilation – DocumentDB précompilation des procédures stockées, les déclencheurs et les fonctions définies par utilisateur (UDF) afin d’éviter le coût de compilation de JavaScript pour chaque appel. La charge de la génération du code octet pour la logique procédurale est amortie sur une valeur minimale.
    -   Séquençage – nécessité de nombreuses opérations un effet secondaire (la « déclenche ») qui potentiellement fait référence à une ou plusieurs opérations de magasin secondaire. Outre l’atomicité, c’est plus performant quand déplacée vers le serveur. 
-   **Encapsulation :** Les procédures stockées peuvent servir à regrouper la logique métier dans un seul endroit. Cette méthode présente deux avantages :
    -   Il ajoute une couche d’abstraction sur les données brutes, ce qui permet aux architectes de données faire évoluer leurs applications indépendamment à partir des données. Ceci est particulièrement avantageux lorsque les données sont sans schéma, en raison des hypothèses fragiles qui devront peut-être être intégrées de l’application, s’ils ont à traiter directement avec les données.  
    -   Cette abstraction permet aux entreprises de protéger leurs données en rationalisant l’accès aux scripts.  

La création et l’exécution des déclencheurs de base de données, d’une procédure stockée et d’opérateurs de requête personnalisée est pris en charge par le biais de [l’API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)et [Kits de développement logiciel de client](documentdb-sdk-dotnet.md) sur de nombreuses plates-formes, y compris .NET, Node.js et JavaScript.

**Que ce didacticiel utilise le [Kit de développement logiciel Node.js avec des promesses de Q](http://azure.github.io/azure-documentdb-node-q/) ** pour illustrer la syntaxe et l’utilisation de procédures stockées, des déclencheurs et des fichiers UDF.   

## <a name="stored-procedures"></a>Procédures stockées

### <a name="example-write-a-simple-stored-procedure"></a>Exemple : Écrire une procédure stockée simple 
Commençons par une simple procédure stockée qui retourne une réponse « Hello World ».

    var helloWorldStoredProc = {
        id: "helloWorld",
        body: function () {
            var context = getContext();
            var response = context.getResponse();
    
            response.setBody("Hello, World");
        }
    }


Les procédures stockées sont enregistrées par la collection et peuvent fonctionner sur n’importe quel document et la pièce jointe présente dans cette collection. L’extrait de code suivant montre comment inscrire la procédure stockée helloWorld avec une collection. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Une fois la procédure stockée est enregistrée, nous pouvons exécuter par rapport à la collection et lire les résultats au client. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


L’objet de contexte fournit l’accès à toutes les opérations qui peuvent être effectuées sur le stockage de DocumentDB, ainsi que l’accès aux objets de demande et de réponse. Dans ce cas, nous avons utilisé l’objet response pour définir le corps de la réponse a été envoyée au client. Pour plus de détails, consultez le [DocumentDB JavaScript server documentation du Kit de développement logiciel](http://azure.github.io/azure-documentdb-js-server/).  

Nous développer cet exemple et ajouter que plusieurs fonctionnalités associées à la procédure stockée de la base de données. Les procédures stockées peuvent créer, mettre à jour, lire, interroger et supprimer des documents et pièces jointes contenus dans la collection.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Exemple : Écrire une procédure stockée pour créer un document 
L’extrait de code suivant montre comment utiliser l’objet de contexte pour interagir avec les ressources de DocumentDB.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        body: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Cette procédure stockée prend comme documentToCreate d’entrée, le corps d’un document doit être créé dans la collection en cours. Toutes les opérations sont asynchrones et dépendent des rappels de fonction JavaScript. La fonction de rappel a deux paramètres, un pour l’objet error en cas de que Échec de l’opération et l’autre pour l’objet créé. Dans le rappel, les utilisateurs peuvent gérer l’exception ou une erreur. Dans le cas où un rappel n’est pas fourni, et une erreur se produit, le runtime DocumentDB génère une erreur.   

Dans l’exemple ci-dessus, le rappel génère une erreur si l’opération a échoué. Dans le cas contraire, il définit l’id du document créé dans le corps de la réponse au client. Voici comment cette procédure stockée est exécutée avec des paramètres d’entrée.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
    
            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };
    
            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });

    
Notez que cette procédure stockée peut être modifiée pour prendre un tableau d’instances de document en tant qu’entrée et les créer dans l’exécution d’une procédure stockée même au lieu de plusieurs demandes de réseau pour créer chacun d’eux individuellement. Cela peut servir à implémenter un importateur en vrac efficace pour DocumentDB (décrite plus loin dans ce didacticiel).   

L’exemple décrit a montré comment utiliser des procédures stockées. Plus loin dans le didacticiel, nous allons aborder les déclencheurs et les fonctions de défini par l’utilisateur (UDF).

## <a name="database-program-transactions"></a>Programme de transactions de base de données
Transactions dans une base de données par défaut peuvent être définie comme une séquence d’opérations effectuées comme une seule unité logique de travail. Chaque transaction fournit des **garanties de l’acide**. ACIDE est un acronyme bien connu quatre propriétés - atomicité, cohérence, Isolation et durabilité.  

Brièvement, atomicité garantit que tout le travail effectué à l’intérieur d’une transaction est considéré comme une seule unité qui soit sont validées ou none. La cohérence permet de s’assurer que les données sont toujours dans un bon état interne au sein de transactions. L’isolation garantit que deux transactions n’interfèrent avec d’autres – généralement, la plupart des systèmes offrent plusieurs niveaux d’isolement qui peuvent être utilisés selon les besoins de l’application. Durabilité garantit que toute modification qui est validée dans la base de données seront toujours présente.   

Dans DocumentDB, JavaScript est hébergé dans le même espace de mémoire que la base de données. Par conséquent, les requêtes effectuées dans des procédures stockées et les triggers s’exécutent dans la même étendue d’une session de base de données. Ainsi garantir l’acide pour toutes les opérations qui font partie d’un seul procédure stocké/déclencheur DocumentDB. Considérez la définition suivante de la procédure stockée :

    // JavaScript source code
    var exchangeItemsSproc = {
        name: "exchangeItems",
        body: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            var player1Document, player2Document;
    
            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);
    
                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];
    
                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });
    
            if (!accept) throw "Unable to read player details, abort ";
    
            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;
    
                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";
    
                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });
    
                        if (!accept2) throw "Unable to update player 2, abort";
                    });
    
                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }
    
    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Cette procédure stockée utilise des transactions au sein d’une application de jeu à des éléments d’échanges entre deux joueurs en une seule opération. La procédure stockée essaie de lire les deux documents, correspondant chacune à l’ID de lecteur passé comme un argument. Si les deux documents de lecteur sont trouvés, puis la procédure stockée met à jour les documents par le remplacement de leurs éléments. Si des erreurs sont rencontrées tout au long du processus, il lève une exception de JavaScript qui implicitement abandonne la transaction.

Si la collection de la procédure stockée est enregistrée contre est une collection à partition unique, puis la transaction est étendue à tous les docuemnts dans la collection. Si la collection est partitionnée, les procédures stockées sont exécutées dans la portée de transaction d’une clé de partition unique. Stockée de chaque exécution de la procédure doit comporter une valeur de clé de partition correspondant à la portée de la transaction doit s’exécuter sous. Pour plus d’informations, consultez [DocumentDB de partitionnement](documentdb-partition-data.md).

### <a name="commit-and-rollback"></a>COMMIT et rollback
Les transactions sont intégrées profondément et en mode natif dans le modèle de programmation JavaScript du DocumentDB. À l’intérieur d’une fonction JavaScript, toutes les opérations sont encapsulées automatiquement dans une transaction unique. Si le code JavaScript se termine sans toute exception, les opérations de la base de données sont validées. En effet, les instructions de « BEGIN TRANSACTION » et « COMMIT TRANSACTION » dans les bases de données relationnelles sont implicites dans DocumentDB.  
 
S’il existe une exception est propagée à partir du script, le runtime JavaScript du DocumentDB restaure la transaction entière. Comme indiqué dans l’exemple précédent, la levée d’une exception revient effectivement à un « ROLLBACK TRANSACTION », dans DocumentDB.
 
### <a name="data-consistency"></a>Cohérence des données
Les procédures stockées et les déclencheurs sont toujours exécutées sur le réplica principal de la collection DocumentDB. Cela garantit le stockage cohérence renforcée de procédures offre lectures à partir de l’intérieur. Requêtes à l’aide de fonctions définies par l’utilisateur peuvent être exécutées sur le serveur principal ou de n’importe quel réplica secondaire, mais nous nous assurons que pour respecter le niveau de cohérence demandée en choisissant le réplica approprié.

## <a name="bounded-execution"></a>Exécution limitée
Toutes les opérations de DocumentDB doivent se terminer dans le serveur spécifié durée du délai d’attente de la demande. Cette contrainte s’applique également aux fonctions JavaScript (procédures stockées, les déclencheurs et les fonctions définies par l’utilisateur). Si une opération ne se termine pas dans ce délai, la transaction est restaurée. Fonctions JavaScript doivent finir dans le délai imparti ou implémenter un modèle de base de continuation pour lot/reprendre l’exécution.  

Pour simplifier le développement de procédures stockées et des triggers pour gérer les limites de temps, toutes les fonctions sous l’objet de collection (pour créer, lire, remplacer et supprimer des documents et des pièces jointes) renvoient une valeur de type Boolean qui indique si cette opération sera terminée. Si cette valeur est false, cela indique que la limite de temps est sur le point d’expirer et que la procédure doit encapsuler l’exécution des requêtes.  Opérations en file d’attente avant la première opération de banque non acceptée est garanti pour terminer si la procédure stockée se termine dans les temps et file d’attente d’autres requêtes.  

Fonctions JavaScript sont également limitées sur la consommation des ressources. DocumentDB réserve de débit par la collection en fonction de la taille de la mise en service d’un compte de base de données. Le débit est exprimé en une unité normalisée de processeur, de mémoire et de la consommation d’e/s appelé unités de demande ou RUs. Les fonctions JavaScript peuvent potentiellement utiliser un grand nombre de RUs dans un délai court et peuvent obtenir limitée si la limite de la collection est atteinte. Procédures stockées intensives de ressource peuvent également être mis en quarantaine pour garantir la disponibilité des opérations de base de données primitif.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Exemple : Bloc d’importation de données dans un programme de base de données
Voici un exemple d’une procédure stockée qui est écrit à l’importation en bloc-documents dans une collection. Notez comment la procédure stockée gère l’exécution limitée en vérifiant la valeur booléenne à partir de createDocument, valeur de retour, puis utilise le nombre de documents insérés dans chaque appel de la procédure stockée pour effectuer le suivi et de reprendre la progression sur des lots.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();
    
        // The count of imported docs, also used as current doc index.
        var count = 0;
    
        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");
    
        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }
    
        // Call the create API to create a document.
        tryCreate(docs[count], callback);
    
        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);
    
            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }
    
        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;
    
            // One more document has been inserted, increment the count.
            count++;
    
            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a>Déclencheurs de base de données
### <a name="database-pre-triggers"></a>Déclencheurs de base de données préliminaire
DocumentDB fournit des déclencheurs qui sont exécutés ou déclenchés par une opération sur un document. Par exemple, vous pouvez spécifier un déclencheur avant lorsque vous créez un document, ce déclencheur avant s’exécute avant que le document est créé. Voici un exemple de l’utilisation des déclencheurs avant de valider les propriétés d’un document qui est en cours de création :

    var validateDocumentContentsTrigger = {
        name: "validateDocumentContents",
        body: function validate() {
            var context = getContext();
            var request = context.getRequest();
    
            // document to be created in the current operation
            var documentToCreate = request.getBody();
    
            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }
    
            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


Et le code d’inscription côté client Node.js correspondant pour le déclencheur :

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };
    
            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };
    
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Les déclencheurs ne peuvent pas avoir tous les paramètres d’entrée. L’objet de la demande peut être utilisé pour manipuler le message de demande associé à l’opération. Ici, le déclencheur préliminaire est en cours d’exécution lors de la création d’un document, et le corps du message demande contient le document doit être créé dans le format JSON.   

Lorsque des déclencheurs sont enregistrés, les utilisateurs peuvent spécifier les opérations qu’il peut exécuter avec. Ce déclencheur a été créé avec TriggerOperation.Create, ce qui signifie que les éléments suivants ne sont pas autorisé.

    var options = { preTriggerInclude: "validateDocumentContents" };
    
    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });
    
    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Déclencheurs de base de données après
Postérieur à déclencheurs, telles que des déclencheurs, sont associés à une opération sur un document et ne prennent pas tous les paramètres d’entrée. Ils s’exécutent **une fois** que l’opération terminée et ont accès au message de réponse qui est envoyé au client.   

L’exemple suivant montre des déclencheurs après en action :

    var updateMetadataTrigger = {
        name: "updateMetadata",
        body: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            // document that was created
            var createdDocument = response.getBody();
    
            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";
     
            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';
                         
                         var metadataDocument = documents[0];
                         
                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                           
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


Le déclencheur peut être enregistré comme indiqué dans l’exemple suivant.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };
    
            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };
        
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Ce déclencheur interroge pour le document de métadonnées et le met à jour avec les détails concernant le document nouvellement créé.  

Une chose importante à noter est l’exécution **transactionnelle** des déclencheurs dans DocumentDB. Ce déclencheur après s’exécute dans le cadre de la même transaction que la création du document d’origine. Par conséquent, si nous avons une exception à partir du déclencheur après (par exemple si nous ne pouvons pas mettre à jour le document de métadonnées), l’ensemble de la transaction échoue et être annulée. Aucun document n’est créée, et une exception sera renvoyée.  

##<a id="udf"></a>Fonctions définies par l’utilisateur
Fonctions définies par l’utilisateur (UDF) sont utilisées pour étendre la grammaire du langage de requête DocumentDB SQL et implémenter la logique métier personnalisée. Ils ne peuvent être appelées à partir de l’intérieur des requêtes. Ils n’ont pas accès à l’objet de contexte et sont destinés à être utilisés en tant que JavaScript compute uniquement. Par conséquent, UDF peuvent être exécutés sur les doubles secondaires du service DocumentDB.  
 
L’exemple suivant crée une fonction pour calculer l’impôt sur le revenu des taux pour les différents supports de revenu et utilise ensuite à l’intérieur d’une requête pour trouver toutes les personnes ayant payé plus de 20 000 $ de vos impôts.

    var taxUdf = {
        name: "tax",
        body: function tax(income) {
    
            if(income == undefined) 
                throw 'no input';
    
            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


Le UDF peut ensuite être utilisé dans les requêtes, comme dans l’exemple suivant :

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);
    
            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>Requête language-integrated query JavaScript API
En plus de l’émission de requêtes à l’aide de la grammaire SQL de DocumentDB, le Kit de développement côté serveur vous permet d’effectuer des requêtes optimisées à l’aide d’une interface JavaScript fluent sans avoir aucune connaissance de SQL. Appelle la requête JavaScript QU'API vous permet de générer des requêtes par programme en passant des fonctions de prédicat dans une fonction peut être reliée, avec une syntaxe familière différencié de tableau et les bibliothèques JavaScript comme lodash de ECMAScript5. Les requêtes sont analysées par le runtime JavaScript pour être exécutée efficacement en utilisant les indices de DocumentDB.

> [AZURE.NOTE]`__` (trait de soulignement double) est un alias pour `getContext().getCollection()`.
> <br/>
> En d’autres termes, vous pouvez utiliser `__` ou `getContext().getCollection()` pour accéder à l’API de requête JavaScript.

Fonctions prises en charge sont les suivantes :
<ul>
<li>
<b>... Chain(). valeur ([rappel] [, options])</b>
<ul>
<li>
Commence un appel chaîné qui doit se terminer par value().
</li>
</ul>
</li>
<li>
<b>filtre (predicateFunction [, options] [, rappel])</b>
<ul>
<li>
Filtre de l’entrée à l’aide d’une fonction de prédicat retourne true/false pour filtrer les documents d’entrée/sortie dans le jeu résultant. Ce comportement similaire à une clause WHERE dans SQL.
</li>
</ul>
</li>
<li>
<b>mappage (transformationFunction [, options] [, rappel])</b>
<ul>
<li>
Applique une projection donnée d’une fonction de transformation qui mappe chaque élément d’entrée à une valeur ou un objet JavaScript. Ce comportement similaire à une clause SELECT dans SQL.
</li>
</ul>
</li>
<li>
<b>Pluck ([propertyName] [, options] [, rappel])</b>
<ul>
<li>
Il s’agit d’un raccourci pour une table qui extrait la valeur d’une propriété unique de chaque élément d’entrée.
</li>
</ul>
</li>
<li>
<b>aplatir ([isShallow] [, options] [, rappel])</b>
<ul>
<li>
Combine et aplatit les baies à partir de chaque élément d’entrée dans un tableau unique. Ce comportement semblable à SelectMany dans LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([prédicat] [, options] [, rappel])</b>
<ul>
<li>
Générer un nouvel ensemble de documents en triant les documents dans le flux du document d’entrée dans l’ordre à l’aide du prédicat donné croissant. Cette instruction se comporte comme une clause ORDER BY dans SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([prédicat] [, options] [, rappel])</b>
<ul>
<li>
Générer un nouvel ensemble de documents en triant les documents dans le flux du document d’entrée dans l’ordre décroissant sous le prédicat donné. Ce comportement similaire à une clause SQL ORDER BY x DESC.
</li>
</ul>
</li>
</ul>


Lorsque inclus à l’intérieur de fonctions prédicat ou sélecteur, les constructions de JavaScript suivantes obtenir automatiquement optimisées pour s’exécuter directement sur les indices de DocumentDB :

* Opérateurs simples : = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Littéraux, y compris le littéral d’objet : {}
* var, retour

Les constructions de JavaScript suivantes n’a pas obtenir optimisées pour les indices de DocumentDB :

* Flux de contrôle (par exemple, if, tandis que)
* Appels de fonction

Pour plus d’informations, veuillez consulter nos [JSDocs du côté serveur](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Exemple : Écrire une procédure stockée à l’aide de l’API de requête JavaScript

L’exemple de code suivant est un exemple de comment l’API de requête JavaScript peut être utilisée dans le cadre d’une procédure stockée. La procédure stockée insère un document donné par un paramètre d’entrée et met à jour une métadonnées du document, à l’aide de la `__.filter()` méthode avec minSize, maxSize et totalSize en fonction de la propriété size du document d’entrée.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL à un mémento pour Javascript
Le tableau suivant présente les différentes requêtes SQL et les requêtes de JavaScript correspondants.

Comme les requêtes SQL, clés de propriétés de document (par exemple, `doc.id`) respectent la casse.

<br/>
<table border="1" width="100%">
<colgroup>
<col span="1" style="width: 40%;">
<col span="1" style="width: 40%;">
<col span="1" style="width: 20%;">
</colgroup>
<tbody>
<tr>
<th>SQL</th>
<th>API de requête JavaScript</th>
<th>Détails</th>
</tr>
<tr>
<td>
<pre>
Sélectionnez * à partir de documents
</pre>
</td>
<td>
<pre>
__.Map(Function(doc) {doc retour ;}) ;
</pre>
</td>
<td>Résultats dans tous les documents (paginés avec jeton de continuation) en tant qu’est.</td>
</tr>
<tr>
<td>
<pre>
SELECT docs.id, docs.message en tant que message, docs.actions de documents
</pre>
</td>
<td>
<pre>
__.Map(Function(doc) {retour {id : doc.id, msg : doc.message, actions : doc.actions} ;}) ;
</pre>
</td>
<td>Les id, message (alias msg) et l’action à partir de tous les documents des projets.</td>
</tr>
<tr>
<td>
<pre>
Sélectionnez * à partir de documents où docs.id="X998_Y998 »
</pre>
</td>
<td>
<pre>
__.Filter(Function(doc) {retourner doc.id === « X998_Y998 ; »}) ;
</pre>
</td>
<td>Requêtes de documents avec le prédicat : id = « X998_Y998 ».</td>
</tr>
<tr>
<td>
<pre>
Sélectionnez * à partir de documents où ARRAY_CONTAINS(docs. Balises, 123)
</pre>
</td>
<td>
<pre>
__.Filter(Function(x) {retourner x.Tags & & x.Tags.indexOf(123) > -1 ;}) ;
</pre>
</td>
<td>Requêtes pour les documents qui ont une propriété balises et balises est un tableau qui contient la valeur 123.</td>
</tr>
<tr>
<td>
<pre>
SELECT docs.id, docs.message en tant que documents FROM msg où docs.id="X998_Y998 »
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {retourner doc.id === « X998_Y998 ; »}) .map(function(doc) {retour {id : doc.id, msg : doc.message} ;}) .value() ;
</pre>
</td>
<td>Requêtes pour les documents comportant un prédicat, id = « X998_Y998 » et projette ensuite l’id et le message (avec un alias de message).</td>
</tr>
<tr>
<td>
<pre>
Balise de sélectionner la valeur à partir de documents de documents jointure balise dans. Balises ORDER BY docs._ts
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {doc de retour. Balises & & Array.isArray (doc. Balises) ; .sortBy(function(doc) de}) {retour doc._ts ;}) .pluck("tags") .flatten() .value()
</pre>
</td>
<td>Filtres pour les documents qui ont une propriété de tableau, les balises et trie les documents résultant de la propriété de système DTS timestamp et projette ensuite + aplatit le tableau de balises.</td>
</tr>
</tbody>
</table>

## <a name="runtime-support"></a>Prise en charge runtime
[Côté serveur de DocumentDB JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) prend en charge la plupart des fonctionnalités du langage JavaScript standard sous la forme normalisée par [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Sécurité
JavaScript de procédures stockées et les déclencheurs sont mis en sandbox afin que les effets d’un script ne gaspillez pas à l’autre sans passer par l’isolement de transaction de cliché au niveau de la base de données. Les environnements runtime sont regroupées et nettoyées du contexte après chaque exécution. Donc qu’ils sont garantis d’aucun effet secondaire imprévu de l’autre.

### <a name="pre-compilation"></a>Précompilation
Les procédures stockées, les déclencheurs et les fonctions utilisateur est implicitement précompilé pour le format du code octet afin d’éviter le coût de la compilation au moment de chaque appel de script. Ainsi, les appels de procédures stockées sont rapides et ont un faible encombrement.

## <a name="client-sdk-support"></a>Prise en charge du Kit de développement logiciel client
En plus du client [Node.js](documentdb-sdk-node.md) , DocumentDB prend en charge le [.NET](documentdb-sdk-dotnet.md), [Java](documentdb-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/)et [Kits de développement logiciel Python](documentdb-sdk-python.md). Les procédures stockées, les déclencheurs et les fonctions utilisateur peut être créées et exécutées à l’aide d’un de ces kits de développement de logiciel ainsi. L’exemple suivant montre comment créer et exécuter une procédure stockée en utilisant le client .NET. Notez comment les types .NET sont passées à la procédure stockée en tant que JSON et lire.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    
    
                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }
    
                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
            }"
    };
    
    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;
    
    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "sproc"), document, 1920);


Cet exemple montre comment utiliser le [Kit de développement .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx) pour créer un déclencheur avant et créer un document avec le déclencheur activé. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };
    
    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


Et l’exemple suivant montre comment créer une fonction définie par l’utilisateur (UDF) et l’utiliser dans une [requête de DocumentDB SQL](documentdb-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };
    
    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>API REST
Toutes les opérations de DocumentDB peuvent être effectuées de manière RESTful. Procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur peuvent être enregistrées dans une collection à l’aide de HTTP POST. Voici un exemple de l’inscription d’une procédure stockée :

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    
    
    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


La procédure stockée est enregistrée par l’exécution d’une demande POST à l’URI dbs/testdb/solénoïdes/testColl/procédures stockées avec le corps contenant la procédure stockée à créer. Les déclencheurs et les fonctions utilisateur peut être enregistrées de la même façon par l’émission d’un billet contre les /triggers et /udfs respectivement.
Cela stockées procédure peut puis être exécutée par l’émission d’une demande POST sur son lien de ressources :

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
    
    
    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Dans ce cas, l’entrée de la procédure stockée est passée dans le corps de la demande. Notez que l’entrée est transmise sous la forme d’un tableau JSON de paramètres d’entrée. La procédure stockée accepte la première entrée sous la forme d’un document qui est un corps de réponse. La réponse que nous recevons est la suivante :

    HTTP/1.1 200 OK
     
    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Contrairement aux procédures stockées, les déclencheurs ne peuvent pas être exécutées directement. Au lieu de cela, elles sont exécutées dans le cadre d’une opération sur un document. Nous pouvons spécifier les déclencheurs s’exécute avec une demande à l’aide d’en-têtes HTTP. Voici la demande de création d’un document.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
    
    
    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Ici, le déclencheur avant d’être exécuté avec la demande est spécifié dans l’en-tête de x-ms-documentdb-pre-trigger-include. En conséquence, tous les déclencheurs après figurent dans l’en-tête de x-ms-documentdb-post-trigger-include. Notez que les deux avant et après les déclencheurs peuvent être spécifiés pour une demande donnée.

## <a name="sample-code"></a>Exemples de code

Vous trouverez plusieurs exemples de code côté serveur (y compris la [suppression en bloc](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js)et [mettre à jour](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) sur notre [référentiel de Github](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Vous souhaitez partager votre procédure stockée Isard ? Veuillez nous envoyer une demande d’extraction ! 

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez un ou plusieurs des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur créées, vous pouvez les charger et les afficher dans le portail Azure à l’aide du Script de l’Explorateur. Pour plus d’informations, consultez [affichage de procédures stockées, déclencheurs et les fonctions définies par l’utilisateur à l’aide de l’Explorateur de scripts DocumentDB](documentdb-view-scripts.md).

Vous pouvez également trouver utile dans votre chemin d’accès pour en savoir plus sur la programmation côté serveur de DocumentDB les références et les ressources suivantes :

- [Kits de développement logiciel DocumentDB Azure](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
- [JSON](http://www.json.org/) 
- [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
- [JavaScript : système de type JSON](http://www.json.org/js.html) 
- [Extensibilité de base de données sécurisée et Portable](http://dl.acm.org/citation.cfm?id=276339) 
- [Architecture de base de données orientée service](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
- [Hébergement du Runtime .NET dans le serveur de Microsoft SQL](http://dl.acm.org/citation.cfm?id=1007669)
