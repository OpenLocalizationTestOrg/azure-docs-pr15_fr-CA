<properties 
    pageTitle="DocumentDB modèle hiérarchique de ressources et concepts | Microsoft Azure" 
    description="Obtenir des informations sur le modèle hiérarchique du DocumentDB de bases de données, collections, fonction de défini par l’utilisateur (UDF), documents, autorisations pour gérer les ressources et bien plus encore."
    keywords="Modèle hiérarchique, documentdb, azure, Microsoft azure"   
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="anhoh"/>

# <a name="documentdb-hierarchical-resource-model-and-concepts"></a>Concepts et le modèle de ressource hiérarchique de DocumentDB

Les entités de base de données qui gère les DocumentDB sont appelées des **ressources**. Chaque ressource est identifiée par un URI logique. Vous pouvez interagir avec les ressources à l’aide de verbes HTTP standard, les en-têtes de demande/réponse et les codes d’état. 

En lisant cet article, vous serez en mesure de répondre aux questions suivantes :

- Quel est le modèle de ressource de DocumentDB ?
- Quels sont le système défini des ressources et non les ressources définies par l’utilisateur ?
- Comment pour résoudre une ressource ?
- Comment travailler avec les collections ?
- Comment travailler avec des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur (UDF) ?

## <a name="hierarchical-resource-model"></a>Modèle hiérarchique de ressources
Comme l’illustre le diagramme suivant, hiérarchique DocumentDB **modèle de ressource** se compose d’ensembles de ressources sous un compte de base de données, chaque adressable par un URI logique et stable. Un ensemble de ressources va être appelé un **flux** dans cet article. 

>[AZURE.NOTE] DocumentDB offre un protocole TCP hautement efficace, c'est-à-dire également RESTful dans son modèle de communication disponible via le [client de .NET SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

![Modèle de ressource hiérarchique de DocumentDB][1]  
**Modèle hiérarchique de ressources**   

Pour commencer à travailler avec les ressources, vous devez [créer un compte de base de données DocumentDB](documentdb-create-account.md) à l’aide de votre abonnement Azure. Un compte de base de données peut se composer d’un ensemble de **bases de données**, contenant chacun plusieurs **collections**, chacun à son tour contenir **de procédures stockées, déclencheurs, UDF, documents** et les **pièces jointes** (fonctionnalité d’aperçu). Une base de données est également associé aux **utilisateurs**, chacun avec un jeu d' **autorisations** pour accéder aux collections, procédures stockées, déclencheurs, UDF, documents ou pièces jointes. Alors que les bases de données, les utilisateurs, les autorisations et les collections sont définies par le système de ressources avec des schémas connus, les documents et les pièces jointes contiennent arbitraire, contenu JSON défini par l’utilisateur.  

|Ressources   |Description
|-----------|-----------
|Compte de base de données   |Un compte de base de données est associé à un ensemble de bases de données et d’un montant fixe du stockage blob pour les pièces jointes (fonctionnalité d’aperçu). Vous pouvez créer un ou plusieurs comptes de base de données à l’aide de votre abonnement Azure. Pour plus d’informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).
|Base de données   |Une base de données est un conteneur logique de stockage de document partitionnée entre les collections. Il est également un conteneur d’utilisateurs.
|Utilisateur   |L’espace de noms logique pour la portée des autorisations. 
|Autorisation |Un jeton d’autorisation est associé à un utilisateur pour l’accès à une ressource spécifique.
|Collection |Une collection est un conteneur de documents JSON et la logique d’application JavaScript associée. Une collection est une entité facturable, où le [coût](documentdb-performance-levels.md) est déterminé par le niveau de performances associé à la collection. Les collections peuvent couvrir une ou plusieurs partitions et des serveurs et peuvent évoluer pour gérer pratiquement illimité de volumes de stockage ou de débit.
|Procédure stockée   |Logique d’application écrite dans JavaScript qui est inscrit auprès d’une collection et transactionnelle exécutée dans le moteur de base de données.
|Déclencheur    |Logique d’application écrite dans JavaScript exécuté avant ou après une insertion soit, remplacer ou supprimer l’opération.
|UDF    |Logique d’application écrite dans JavaScript. UDF permettent de modéliser un opérateur de requête personnalisé et donc étendre le langage de requête de DocumentDB de base.
|Document   |Contenu JSON (arbitraire) défini par l’utilisateur. Par défaut, aucun schéma ne doit être défini ni faire les index secondaires doivent être fournies pour tous les documents ajoutés à une collection.
|(Aperçu) Pièce jointe   |Une pièce jointe est un document spécial contenant les références et les métadonnées associées de blob/support externe. Le développeur peut choisir d’avoir le blob géré par DocumentDB ou stocker avec un fournisseur de services externe blob OneDrive, échange, etc.. 


## <a name="system-vs-user-defined-resources"></a>Système et les ressources définies par l’utilisateur
Ressources telles que les comptes de la base de données, bases de données, collections, utilisateurs, autorisations, procédures stockées, déclencheurs et UDF - ont tous un schéma fixe et sont appelés des ressources système. En revanche, les ressources telles que les documents et les pièces jointes ne présentent aucune limitation sur le schéma et sont des exemples de ressources de défini par l’utilisateur. Dans DocumentDB, système et définis par l’utilisateur à la fois les ressources sont représentés et managés comme compatible avec la norme JSON. Toutes les ressources système ou définies par l’utilisateur ont les propriétés communes suivantes.

> [AZURE.NOTE] Notez que tous les systèmes généré des propriétés dans une ressource sont précédés d’un trait de soulignement (_) dans leur représentation JSON.

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Propriété</strong></p></td>
            <td valign="top"><p><strong>Par l’utilisateur ou généré par le système ?</strong></p></td>
            <td valign="top"><p><strong>Objectif</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_RID</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>Système est généré, un identificateur unique et hiérarchique de la ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>eTag de la ressource requise pour le contrôle d’accès concurrentiel optimiste</p></td>
        </tr>
        <tr>
            <td valign="top"><p>DTS</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>Dernière modification de la ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>URI adressable unique de la ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>ID</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>Nom unique de la ressource (avec la même valeur de clé partition) défini par l’utilisateur. Si l’utilisateur ne spécifie pas un id, un id est généré par le système</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Représentation sous forme de fils de ressources
DocumentDB ne force pas les extensions propriétaires pour les codages JSON standard ou spéciales ; il fonctionne avec les documents JSON conformes standard.  
 
### <a name="addressing-a-resource"></a>Adressage d’une ressource
Toutes les ressources sont les URI adressable. La valeur de la propriété **_self** d’une ressource représente l’URI relatif de la ressource. Le format de l’URI est constitué par le /\<d’alimentation\>/ segments de chemin d’accès {_rid} :  

|Valeur de la _self |Description
|-------------------|-----------
|/DBS   |Alimentation de bases de données sous un compte de base de données
|/DBS/ {nom_base_de_données}  |Base de données avec un id correspondant à la valeur {nom_base_de_données}
|/colls/ de /DBS/ {nom_base_de_données}   |Flux des collections dans une base de données
|/DBS/ {nom_base_de_données} /colls/ {collName} |Collection dont l’id correspondant à la valeur {collName}
|/DBS/ {nom_base_de_données} /colls/ {collName} / docs    |Flux de documents dans une collection
|/DBS/ {nom_base_de_données} /colls/ {collName} /docs/ {docId}    |Document avec un id correspondant à la valeur {doc}
|/DBS/ {nom_base_de_données} /users/   |Flux d’utilisateurs dans une base de données
|/DBS/ {nom_base_de_données} /users/ {utilisateur}   |Utilisateur avec un id correspondant à la valeur {utilisateur}
|/DBS/ {nom_base_de_données} /users/ {utilisateur} / autorisations   |Aliments pour animaux d’autorisations d’un utilisateur
|/DBS/ {nom_base_de_données} /users/ {utilisateur} /permissions/ {permissionId}    |Autorisation portant l’id correspondant à la valeur {autorisation}
  
Chaque ressource a un nom unique défini par l’utilisateur exposé via la propriété id. Remarque : pour les documents, si l’utilisateur ne spécifie pas un id, nos kits de développement logiciel pris en charge génère automatiquement un id unique pour le document. L’id est une chaîne définie par l’utilisateur, jusqu'à 256 caractères qui est unique dans le contexte d’une ressource spécifique de parent. 

Chaque ressource possède également un identificateur de ressource hiérarchique généré par le système (également appelé un RID), qui est disponible via la propriété _rid. Le RID encode l’ensemble de la hiérarchie d’une ressource donnée, et il s’agit d’une représentation interne pratique permet d’appliquer l’intégrité référentielle dans un mode distribué. Le RID est unique au sein d’un compte de base de données et il est utilisé en interne par DocumentDB pour un routage efficace sans nécessiter des recherches de partition croisée. Les valeurs des propriétés _rid et de la _self sont des représentations alternative et canoniques d’une ressource. 

Les API de reste de DocumentDB prend en charge l’adressage des ressources et le routage des demandes par les propriétés _rid et de l’id.

## <a name="database-accounts"></a>Comptes de base de données
Vous pouvez configurer un ou plusieurs comptes de base de données DocumentDB à l’aide de votre abonnement Azure.

Vous pouvez [créer et gérer des comptes de base de données DocumentDB](documentdb-create-account.md) via le portail Azure à [http://portal.azure.com/](https://portal.azure.com/). Création et gestion d’un compte de base de données nécessite un accès administratif et ne peuvent être effectuée sous votre abonnement Azure. 

### <a name="database-account-properties"></a>Propriétés du compte de base de données
Dans le cadre de la mise en service et la gestion d’un compte de base de données, vous pouvez configurer et lire les propriétés suivantes :  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nom de la propriété</strong></p></td>
            <td valign="top"><p><strong>Description</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Règle de cohérence</p></td>
            <td valign="top"><p>Définir cette propriété pour configurer le niveau de cohérence par défaut pour toutes les collections sous votre compte de base de données. Vous pouvez remplacer le niveau de cohérence sur une base par demande à l’aide de l’en-tête de demande de [x-ms--niveau de cohérence]. <p><p>Notez que cette propriété s’applique uniquement pour les <i>ressources définies par l’utilisateur</i>. Système toutes les ressources définies sont configurés pour prendre en charge les lectures/requêtes avec cohérence renforcée.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clés d’autorisation</p></td>
            <td valign="top"><p>Il s’agit primaires et secondaires clés et principale en lecture seule qui fournissent un accès administratif à toutes les ressources sous le compte de base de données.</p></td>
        </tr>
    </tbody>
</table>

Notez qu’en plus de la mise en service, la configuration et la gestion de votre compte de base de données à partir du portail d’Azure, vous pouvez également programmer de créer et de gérer des comptes de base de données DocumentDB à l’aide de la [Azure DocumentDB reste API](https://msdn.microsoft.com/library/azure/dn781481.aspx) ainsi que les [Kits de développement logiciel de client](https://msdn.microsoft.com/library/azure/dn781482.aspx).  

## <a name="databases"></a>Bases de données
Une base de données DocumentDB est un conteneur logique d’une ou plusieurs collections et les utilisateurs, comme illustré dans le diagramme suivant. Vous pouvez créer autant de bases de données sous un compte de base de données DocumentDB limites de l’offre.  

![Modèle de base de données hiérarchique compte et collections][2]  
**Une base de données est un conteneur logique des utilisateurs et des collections**

Une base de données peut contenir le stockage quasiment illimité de document partitionnée par collections, qui constituent les domaines de la transaction pour les documents qu’ils contiennent. 

### <a name="elastic-scale-of-a-documentdb-database"></a>Évolutivité élastique d’une base de données DocumentDB
Une base de données DocumentDB est élastique par défaut – allant de quelques Go, voire en pétaoctets de stockage SSD sauvegardée des documents et du débit mis en service. 

Contrairement à une base de données dans un SGBDR traditionnel, une base de données dans DocumentDB pas limitée à un seul ordinateur. Avec DocumentDB, comme l’échelle de votre application a besoin de croître, vous pouvez créer plusieurs collections, bases de données ou les deux. En effet, divers premières applications de tiers au sein de Microsoft ont été à l’aide de DocumentDB à une échelle de consommateur en créant des très grandes bases de données DocumentDB chaque qui contient des milliers de collections avec plusieurs téraoctets de stockage de document. Vous pouvez développer ou réduire une base de données en ajoutant ou supprimant des collections pour répondre aux exigences d’échelle de votre application. 

Vous pouvez créer un nombre quelconque de collections dans une base de données sous réserve de l’offre. Chaque collection a SSD sauvegardé stockage et débit provisionnée pour vous en fonction du niveau de performance sélectionné.

Une base de données DocumentDB est également un conteneur d’utilisateurs. Un utilisateur, tour, est un espace de noms logique pour un jeu d’autorisations qui fournit une autorisation précise et l’accès aux collections, les documents et les pièces jointes.  
 
Comme avec les autres ressources dans le modèle de ressource DocumentDB, bases de données peuvent être créés, remplacement, supprimé, lire ou énumérée à l’aide soit [Azure DocumentDB autres API](https://msdn.microsoft.com/library/azure/dn781481.aspx) , ou des [Kits de développement logiciel client](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB garantit une cohérence renforcée pour la lecture ou de l’interrogation des métadonnées d’une ressource de base de données. Suppression d’une base de données automatiquement garantit que vous ne peut pas accéder à des collections ou des utilisateurs qu’il contient.   

## <a name="collections"></a>Collections
Une collection de DocumentDB est un conteneur pour vos documents JSON. Une collection est également une unité d’échelle pour les transactions et les requêtes. 

### <a name="elastic-ssd-backed-document-storage"></a>Stockage de documents SSD sauvegardée élastique
Une collection est intrinsèquement élastique - elle augmente et diminue à mesure que vous ajoutez ou supprimez des documents automatiquement. Les collections sont des ressources logiques et peuvent s’étendre sur un ou plusieurs serveurs ou plusieurs partitions. Le nombre de partitions au sein d’une collection est déterminé par la DocumentDB en fonction de la taille de stockage et le débit de mise en service de votre collection. Chaque partition dans DocumentDB possède une quantité fixe de stockage SSD de secours associé et est répliquée pour une haute disponibilité. Gestion des partitions est entièrement gérée par DocumentDB d’Azure, et vous n’avez pas à écrire du code complexe ou gérer vos partitions. Les collections de DocumentDB sont **pratiquement illimitées** en termes de stockage et de débit. 

### <a name="automatic-indexing-of-collections"></a>L’indexation automatique de collections
DocumentDB est un système de true exempt de schéma de base de données. Il ne pas supposer ou nécessitent un schéma pour les documents JSON. Lorsque vous ajoutez des documents à une collection, DocumentDB les indexe automatiquement et qu’ils sont disponibles pour vous permettent d’interroger. L’indexation automatique de documents sans nécessiter de schéma ou index secondaires est une fonctionnalité clée de DocumentDB et est activée par des techniques de gestion des index optimisés pour écriture, sans verrouillage et structuré de journal. DocumentDB prend en charge le volume constant des écritures extrêmement rapides tout en continuant d’adresser des requêtes cohérentes. Les documents et les index de stockage permettent de calculer le stockage utilisé par chaque collection. Vous pouvez contrôler les stockage et les performances compromis associés à l’indexation en configurant la stratégie d’indexation de la collection. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configuration de la stratégie d’indexation d’une collection
La stratégie d’indexation de chaque collection vous permet des performances et stockage compromis liés à l’indexation. Les options suivantes sont disponibles dans le cadre de la configuration de l’indexation :  

-   Choisissez si la collection indexe automatiquement tous les documents ou non. Par défaut, tous les documents sont automatiquement indexés. Vous pouvez choisir de désactiver l’indexation automatique et sélective d’ajouter uniquement des documents spécifiques dans l’index. À l’inverse, vous pouvez choisir de façon sélective exclure uniquement les documents spécifiques. Vous pouvez accomplir ceci en définissant la propriété automatique soit vrai ou faux sur l’indexingPolicy d’une collection et à l’aide de l’en-tête de demande de [x-ms-indexingdirective] lors de l’insertion, de remplacement ou de suppression d’un document.  
-   Choisissez d’inclure ou d’exclure des chemins spécifiques ou des modèles dans vos documents à partir de l’index. Vous pouvez y parvenir par le paramètre includedPaths et excludedPaths sur l’indexingPolicy d’une collection respectivement. Vous pouvez également configurer les stockage et les performances des compromis pour des requêtes de plage et de hachage pour les modèles de chemin d’accès spécifique. 
-   Choisissez entre synchrone (cohérent) et mises à jour d’asynchrone de l’index (paresseux). Par défaut, l’index est mis à jour simultanément sur chaque insertion, de remplacer ou de supprimer d’un document à la collection. Ainsi, les requêtes d’honorer le même niveau de cohérence que celui de la lecture du document. Lorsque DocumentDB est optimisé en écriture et prend en charge les volumes soutenues d’écritures de document ainsi que la maintenance des index synchrone et de traiter les requêtes cohérentes, vous pouvez configurer certaines collections pour mettre à jour leur index de tardivement. Indexation différée améliore encore les performances d’écriture et est idéal pour les scénarios de réception en bloc pour les collections de lourdes en lecture principalement.

La stratégie d’indexation peut être modifiée par l’exécution d’une commande PUT sur la collection. Ceci peut être réalisé soit par le biais du [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx), le [Portail Azure](https://portal.azure.com) du ou [Azure DocumentDB reste API](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### <a name="querying-a-collection"></a>Interrogation d’une collection
Les documents au sein d’une collection peuvent avoir des schémas arbitraires et que vous pouvez interroger des documents au sein d’une collection sans fournir n’importe quel schéma ou un index secondaire dès le départ. Vous pouvez interroger la collection à l’aide de la [syntaxe de DocumentDB SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)qui fournit un riche relationnels, hiérarchiques et l’opérateurs spatiales et l’extensibilité via les UDF basé sur JavaScript. Grammaire JSON permet pour les documents JSON sous forme d’arborescences avec les étiquettes comme les nœuds d’arbre de modélisation. Cette possibilité est exploitée à la fois par des techniques d’indexation automatiques de DocumentDB comme dialecte SQL de DocumentDB. Le langage de requête DocumentDB se compose de trois aspects principaux :   

1.  Un petit ensemble d’opérations de requête correspondant naturellement à la structure de l’arborescence, y compris les projections et les requêtes hiérarchiques. 
2.  Un sous-ensemble d’opérations relationnelles, y compris la composition, filtre, projections, agrégats et auto-jointures. 
3.  À base de JavaScript pur UDF qui fonctionnent avec (1) et (2).  

Le modèle de requête DocumentDB essaie de trouver un équilibre entre les fonctionnalités, l’efficacité et de simplicité. En mode natif, le moteur de base de données DocumentDB compile et exécute les instructions de requête SQL. Vous pouvez interroger une collection en utilisant les [API de reste Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou des [Kits de développement logiciel de client](https://msdn.microsoft.com/library/azure/dn781482.aspx). Le Kit de développement .NET est livré avec un fournisseur LINQ.

> [AZURE.TIP] Vous pouvez essayer de DocumentDB et exécuter des requêtes SQL par rapport à notre dataset dans le [Laboratoire de la requête](https://www.documentdb.com/sql/demo).

### <a name="multi-document-transactions"></a>Transactions de plusieurs documents
Transactions de base de données fournissent un modèle de programmation sûr et prévisible pour traiter les modifications simultanées apportées aux données. Dans le RDBMS, la traditionnelle à écrire la logique métier consiste à écrire **des procédures stockées** ou des **déclencheurs** et envoyez-le au serveur de base de données pour l’exécution de transactions. Dans le RDBMS, le programmeur de l’application est requis pour traiter les deux langages de programmation différents : 

- La langue (par exemple, JavaScript, Python, C#, Java, etc.) de programmation d’application (non transactionnel)
- T-SQL, le langage de programmation transactionnel qui est exécuté en mode natif par la base de données

Du fait de son engagement profond dans JavaScript et JSON directement dans le moteur de base de données, DocumentDB fournit un modèle de programmation intuitif pour l’exécution de la logique d’application JavaScript basé directement sur les collections en termes de procédures stockées et les déclencheurs. Ainsi, pour le des opérations suivantes :

- Contrôler la mise en oeuvre efficace de la simultanéité, récupération automatique de l’indexation des graphiques d’objets JSON directement dans le moteur de base de données
- Naturellement exprimant le flux de contrôle, variable de portée, affectation et l’intégration des primitives avec des transactions de base de données directement en langage de programmation JavaScript de la gestion des exceptions

La logique JavaScript inscrite à un niveau de collection peut alors émettre des opérations de base de données sur les documents de la collection donnée. DocumentDB encapsule implicitement le code JavaScript sur les procédures stockées et déclencheurs dans une ambiance transactions ACID avec isolement de capture instantanée dans les documents au sein d’une collection. Au cours de son exécution, si le code JavaScript lève une exception, la transaction entière est annulée. Le modèle de programmation qui en résulte est très simples mais puissants. Les développeurs JavaScript d’obtenir un modèle de programmation « durable » tout en utilisant les constructions de langage familier et les primitives de la bibliothèque.   

La possibilité d’exécuter JavaScript directement dans le moteur de base de données dans le même espace d’adressage que le pool de mémoire tampon permet performant et transactionnelle exécution des opérations de base de données contre les documents d’une collection. En outre, moteur de base de données DocumentDB rend profondément envers le JSON et JavaScript élimine les différences d’impédance entre les systèmes de type d’application et la base de données.   

Après avoir créé une collection, vous pouvez enregistrer des procédures stockées, des déclencheurs et des fichiers UDF avec une collection en utilisant les [API de reste Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou des [Kits de développement logiciel de client](https://msdn.microsoft.com/library/azure/dn781482.aspx). Après l’inscription, vous pouvez référencer et les exécuter. Examinez la procédure stockée suivante entièrement écrite en JavaScript, le code ci-dessous prend deux arguments (nom de l’annuaire et le nom de l’auteur) et crée un nouveau document, interroge pour un document puis le met à jour – tout au sein d’une transaction ACID implicite. À tout moment pendant l’exécution, si une exception de JavaScript est levée, la transaction est abandonnée.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()
            
        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);
                
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);
                        
                        context.getResponse().setBody(matchingDocuments.length);
                       
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

Le client peut « navire » la logique JavaScript ci-dessus à la base de données pour l’exécution de transactions via HTTP POST. Pour plus d’informations sur l’utilisation des méthodes HTTP, consultez [interactions RESTful avec DocumentDB de ressources](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Notez que parce que la base de données en mode natif est conscient que JSON et JavaScript, aucune incohérence de système de type, aucune « Mappage ou » ou est magique de génération de code nécessaires.   

Les procédures stockées et les déclencheurs interagissent avec une collection et les documents de la collection via un modèle objet bien défini, ce qui expose le contexte actuel de la collection.  

Collections dans DocumentDB peuvent être créées, supprimés, lire ou énuméré facilement à l’aide soit [Azure DocumentDB autres API](https://msdn.microsoft.com/library/azure/dn781481.aspx) , ou des [Kits de développement logiciel de client](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB toujours cohérence renforcée pour la lecture ou de l’interrogation des métadonnées d’une collection. Suppression d’une collection automatiquement garantit que vous ne peut pas accéder à des documents, des pièces jointes, des procédures stockées, des déclencheurs et UDF qu’il contient.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur (UDF)
Comme décrit dans la section précédente, vous pouvez écrire la logique de l’application pour exécuter directement au sein d’une transaction dans le moteur de base de données. La logique d’application peut être entièrement écrit en JavaScript et peut être modélisée comme une procédure stockée, déclencheur ou un fichier UDF. Le code JavaScript au sein d’une procédure stockée ou un déclencheur peut insérer, remplacer, supprimer, documents de lecture ou d’une requête dans une collection. En revanche, le code JavaScript dans un fichier UDF ne peut pas insérer, remplacer ou supprimer les documents. FDU énumérer les documents du jeu de résultats d’une requête et produisent un autre jeu de résultats. Pour l’architecture mutualisée, DocumentDB applique une stricte réservation basée la gouvernance de ressources. Chaque procédure stockée, déclencheur ou un UDF Obtient un quantum fixe de ressources de système d’exploitation pour effectuer son travail. En outre, les procédures stockées, des déclencheurs ou des UDF ne peut pas lier les bibliothèques JavaScript externes et sur liste noire s’ils dépassent les budgets de ressources qui leur est attribués. Vous pouvez enregistrer, annuler l’enregistrement de procédures stockées, des déclencheurs ou des fichiers UDF avec une collection à l’aide de l’API du reste.  Lors de l’enregistrement une procédure stockée, déclencheur ou un UDF est précompilé et stocké en tant que code octet qui est exécutée ultérieurement. La section suivante illustrent comment vous pouvez utiliser le Kit de développement de JavaScript DocumentDB d’enregistrer, d’exécuter et d’annuler l’inscription d’une procédure stockée, déclencheur et un fichier UDF. Le SDK de JavaScript est un wrapper simple sur les [API de reste de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

### <a name="registering-a-stored-procedure"></a>Inscription d’une procédure stockée
L’inscription d’une procédure stockée crée une nouvelle ressource de procédure stockée sur une collection via HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();
            
            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };
    
    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>L’exécution d’une procédure stockée
L’exécution d’une procédure stockée est effectuée par l’émission d’un HTTP POST par rapport à une ressource d’une procédure stockée existante en passant des paramètres à la procédure dans le corps de la demande.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Annuler l’inscription d’une procédure stockée
Annuler l’inscription d’une procédure stockée est simplement effectuée en émettant une méthode HTTP DELETE par rapport à une ressource d’une procédure stockée existante.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Enregistrement d’un déclencheur avant
Enregistrement d’un déclencheur est effectué en créant une nouvelle ressource de déclencheur sur une collection via HTTP POST. Vous pouvez spécifier si le déclencheur est un pré ou un déclencheur post et le type d’opération, il peut être associée à (par exemple, créer, remplacer, supprimer ou tous).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }
    
    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>L’exécution d’un déclencheur avant
L’exécution d’un déclencheur s’effectue en spécifiant le nom d’un déclencheur existant au moment de l’exécution de la demande POST/PUT/suppression d’une ressource de document via l’en-tête de demande.  
 
    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Annuler l’inscription d’un déclencheur avant
Annuler l’inscription d’un déclencheur est simplement effectuée via émettant un verbe HTTP DELETE par rapport à une ressource existante de déclencheur.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Enregistrement d’un fichier UDF
Enregistrement d’un fichier UDF est effectué en créant une nouvelle ressource de FDU sur une collection via HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>L’exécution d’un fichier UDF dans le cadre de la requête
Un fichier UDF peut être spécifié en tant que partie de la requête SQL et est utilisé comme un moyen d’étendre le [langage de requête SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)de base.

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Annuler l’inscription d’un fichier UDF 
Annuler l’inscription d’un fichier UDF est simplement effectuée en émettant une méthode HTTP DELETE contre une ressource FDU existante.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Bien que les extraits de code ci-dessus ont montré l’enregistrement (de démarrage POST) annulation de l’inscription (PUT), lecture/liste (GET) et l’exécution (de démarrage POST) par le biais du [Kit de développement logiciel DocumentDB JavaScript](https://github.com/Azure/azure-documentdb-js), vous pouvez également utiliser les [API de repos](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou autres [Kits de développement logiciel de client](https://msdn.microsoft.com/library/azure/dn781482.aspx). 

## <a name="documents"></a>Documents
Vous pouvez insérer, remplacer, supprimer, lire, énumérer et interroger des documents JSON arbitraires d’une collection. DocumentDB n’impose pas de n’importe quel schéma et ne nécessite pas d’index secondaires pour prendre en charge l’interrogation sur les documents dans une collection.   

Est un service de base de données réellement ouverte, DocumentDB ne pas inventer des types de données spécifiques (par exemple date heure), ou des encodages spécifiques pour les documents JSON. Notez que DocumentDB ne requiert pas les conventions JSON spéciales à codifier les relations entre les divers documents ; la syntaxe SQL de DocumentDB fournit des opérateurs à des documents sans annotations spéciales ou nécessaire de codifier les relations entre les documents à l’aide de requêtes et de projet distinguent les propriétés de requête relationnelle et hiérarchique très performante.  
 
Comme avec toutes les autres ressources, les documents peuvent être créés, remplacé, supprimé, lecture, énumérée et interrogé facilement à l’aide des API de repos ou des [Kits de développement logiciel client](https://msdn.microsoft.com/library/azure/dn781482.aspx). Suppression d’un document instantanément libère le quota correspondant à toutes les pièces jointes imbriquées. Le niveau de cohérence de lecture de documents suit la règle de cohérence sur le compte de base de données. Cette stratégie peut être remplacée sur une base par demande selon les exigences de cohérence des données de votre application. Lorsque vous interrogez des documents, la cohérence de lecture suit le mode d’indexation dans la collection. Pour « cohérent », il suit la règle de cohérence du compte. 

## <a name="attachments-and-media"></a>Les pièces jointes et les médias
>[AZURE.NOTE] Pièce jointe et les supports de ressources sont des fonctionnalités de l’aperçu.
 
DocumentDB permet de stocker des objets BLOB/media binaire avec DocumentDB ou dans votre propre magasin de support à distance. Il vous permet également de représenter les métadonnées d’un média en un document spécial appelé la pièce jointe. Une pièce jointe dans le DocumentDB est un document spécial (JSON) qui référence le média/blob stocké ailleurs. Une pièce jointe est simplement un document spécial qui capture les métadonnées (par exemple, emplacement, auteur etc.) d’un média stocké dans un stockage à distance. 

Considérez une application de lecture sociale qui utilise DocumentDB pour stocker les annotations manuscrites, et les métadonnées, y compris les commentaires, met en évidence, signets, évaluations, aime/goûts etc. associé à un livre électronique d’un utilisateur donné.   

-   Le contenu de l’annuaire proprement dit est stocké dans les supports de stockage soit disponible en tant que partie du compte de base de données de DocumentDB ou de support à distance. 
-   Une application peut stocker les métadonnées de chaque utilisateur sous la forme d’un document distinct--par exemple, les métadonnées de Joe pour Classeur1 sont stockées dans un document référencé par /colls/joe/docs/book1. 
-   Les pièces jointes pointant vers les pages de contenu d’un livre donné d’un utilisateur sont stockés sous le document correspondant, par exemple /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2, etc.. 

Notez que les exemples ci-dessus utilisent des ID convivial pour transmettre la hiérarchie des ressources. Les ressources sont accessibles via les API reste via l’ID de ressource unique. 

La propriété _media de la pièce jointe référence du média géré par le DocumentDB, le support par son URI. DocumentDB permet de garantir à garbage collecter le média lorsque toutes les références en suspens sont supprimées. DocumentDB génère la pièce jointe lorsque vous téléchargez le nouveau média et remplit le _media pour pointer vers le média nouvellement ajouté automatiquement. Si vous choisissez de stocker le support dans un magasin étendu des objets blob géré par vous (par exemple, OneDrive, stockage Azure, échange etc.), vous pouvez toujours utiliser des pièces jointes pour le média de référence. Dans ce cas, vous créez la pièce jointe vous-même et remplissez sa propriété _media.   

Comme avec toutes les autres ressources, les pièces jointes peuvent être créés, remplacement, supprimé, lire ou énumérés facilement à l’aide des API de repos ou de kits de développement logiciel client. À l’instar des documents, le niveau de cohérence de lecture des pièces jointes suit la stratégie de cohérence sur le compte de base de données. Cette stratégie peut être remplacée sur une base par demande selon les exigences de cohérence des données de votre application. Lors de l’interrogation des pièces jointes, la cohérence de lecture suit le mode d’indexation dans la collection. Pour « cohérent », il suit la règle de cohérence du compte. 
 
## <a name="users"></a>Utilisateurs
Un utilisateur DocumentDB représente un espace de noms logique pour le regroupement des autorisations. Un utilisateur DocumentDB peut correspondre à un utilisateur dans un système de gestion d’identité ou un rôle d’application prédéfinis. Pour DocumentDB, un utilisateur représente simplement une abstraction pour regrouper un ensemble d’autorisations dans une base de données.   

Pour l’implémentation de l’architecture mutualisée dans votre application, vous pouvez créer des utilisateurs dans DocumentDB qui correspond à vos utilisateurs réels ou les locataires de votre application. Vous pouvez ensuite créer des autorisations pour un utilisateur donné qui correspondent à l’accès de contrôler diverses collections, documents, pièces jointes, etc..   

Comme vos applications ont besoin d’évoluer avec la croissance de vos utilisateurs, vous pouvez adopter différentes méthodes pour éclater vos données. Vous pouvez modéliser chaque utilisateur comme suit :   

-   Chaque utilisateur est mappé à une base de données.
-   Chaque utilisateur est mappé à une collection. 
-   Les documents correspondant à plusieurs utilisateurs d’atteindre une collection dédiée. 
-   Les documents correspondant à plusieurs utilisateurs sont transmis à un ensemble de collections.   

Quelle que soit la stratégie spécifique ont choisie, vous pouvez modéliser les utilisateurs réels en tant qu’utilisateurs dans la base de données de DocumentDB et associer des autorisations de granularité fine à chaque utilisateur.  

![Collections d’utilisateurs][3]  
**Les stratégies ont et modélisation des utilisateurs**

Comme toutes les autres ressources, les utilisateurs de DocumentDB peuvent être créés, remplacement, supprimé, lire ou énumérés facilement à l’aide des API de repos ou de kits de développement logiciel client. DocumentDB toujours cohérence renforcée pour la lecture ou de l’interrogation des métadonnées d’une ressource de l’utilisateur. Il est important de noter que la suppression d’un utilisateur automatiquement garantit que vous ne peut pas accéder à toutes les autorisations qu’il contient. Bien que le DocumentDB récupère le quota des autorisations dans le cadre de l’utilisateur supprimé en arrière-plan, les autorisations supprimées redevient disponible immédiatement pour pouvoir utiliser.  

## <a name="permissions"></a>Autorisations
Dans une perspective de contrôle d’accès, ressources telles que les comptes de la base de données, bases de données, les utilisateurs et les autorisations sont considérés comme des ressources *d’administration* dans la mesure où elles requièrent des autorisations administratives. D’autre part, les ressources, y compris les collections, documents, pièces jointes, des procédures stockées, les déclencheurs et les FDU sont portées sous une base de données et considérés comme des *ressources de l’application*. Correspondant aux deux types de ressources et les rôles qui y accèdent (à savoir l’administrateur et l’utilisateur), le modèle d’autorisation définit deux types de *touches d’accès rapide*: *la clé principale* et la *clé de ressource*. La clé principale est une partie du compte de base de données et est fournie pour le développeur (ou un administrateur) qui est mise en service du compte de base de données. Cette clé principale a la sémantique de l’administrateur, dans la mesure où il peut être utilisé pour autoriser l’accès à des ressources à la fois d’administration et de l’applications. En revanche, une clé de ressource est une clé d’accès précis qui permet d’accéder à une ressource d’application *spécifique* . Par conséquent, il capture les relations entre l’utilisateur d’une base de données et les autorisations dont dispose l’utilisateur pour une ressource spécifique (par exemple, collection, document, pièce jointe, procédure stockée, déclencheur ou UDF).   

Le seul moyen d’obtenir une clé de ressource est en créant une ressource d’autorisation sous un utilisateur donné. Notez que pour créer ou extraire une autorisation, une clé principale doit être présentée dans l’en-tête d’autorisation. Une ressource d’autorisation lie la ressource, l’accès et l’utilisateur. Après avoir créé une ressource de l’autorisation, l’utilisateur doit présenter la clé de ressource associés pour accéder à la ressource appropriée. Par conséquent, une clé de ressource peut être affichée sous la forme d’une représentation compacte et logique de la ressource de l’autorisation.  

Comme avec toutes les autres ressources, autorisations de DocumentDB peuvent être créées, remplacement, supprimé, lire ou énumérés facilement à l’aide des API de repos ou de kits de développement logiciel client. DocumentDB toujours cohérence renforcée pour la lecture ou de l’interrogation des métadonnées d’une autorisation. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’utilisation des ressources à l’aide des commandes HTTP dans [des interactions RESTful avec les ressources de DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx).


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

