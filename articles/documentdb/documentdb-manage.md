<properties
    pageTitle="DocumentDB stockage et performances | Microsoft Azure" 
    description="Obtenir des informations sur le stockage des données et de stockage des documents dans les DocumentDB et comment vous pouvez mettre à l’échelle DocumentDB pour satisfaire les besoins de votre application." 
    keywords="stockage de documents"
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="syamk"/>

# <a name="learn-about-storage-and-predictable-performance-provisioning-in-documentdb"></a>En savoir plus sur le stockage et la mise en service des performances prévisibles dans DocumentDB
DocumentDB Azure est un entièrement managé et évolutive orientés document NoSQL de base de données service pour les documents JSON. Avec DocumentDB, vous n’avez pas à louer des ordinateurs virtuels, déployer des logiciels ou contrôler les bases de données. DocumentDB est géré et surveillé en permanence par des ingénieurs de Microsoft pour offrir une disponibilité de classe mondiale, de performances et de protection des données.  

Vous pouvez commencer avec DocumentDB en [créant un compte de base de données](documentdb-create-account.md) et d’une [base de données de DocumentDB](documentdb-create-database.md) via le [portail Azure](https://portal.azure.com/). DocumentDB de bases de données sont proposés en stockage d’unités de disque à l’état solide (SSD) de secours et le débit. Ces unités de stockage sont mis en service par la [Création de collections de la base de données](documentdb-create-collection.md) de votre compte de base de données, chaque collection avec débit réservé qui peut être mis à l’échelle vers le haut ou vers le bas à tout moment pour répondre aux besoins de votre application. 

Si votre application dépasse le débit réservé pour une ou plusieurs collections, les demandes sont limitées sur une base par collection. Cela signifie que certaines demandes d’application peuvent réussir tandis que d’autres utilisateurs peuvent être limitées.

Cet article fournit une vue d’ensemble des ressources et des mesures disponibles pour gérer la capacité et de planifier le stockage de données. 

## <a name="database-account"></a>Compte de base de données
En tant qu’Azure abonné, vous pouvez configurer un ou plusieurs comptes de base de données DocumentDB pour gérer vos ressources de base de données. Chaque abonnement est associé à un seul abonnement Azure. 

Comptes de DocumentDB peuvent être créés via le [portail Azure](documentdb-create-account.md), ou à l’aide [d’un modèle ARM ou la CLI d’Azure](documentdb-automation-resource-manager-cli.md).

## <a name="databases"></a>Bases de données
Une seule base de données DocumentDB peut contenir pratiquement un nombre illimité de stockage des documents regroupé en collections. Collections de fournissent une isolation de performances - chaque collection peut être configurée avec le débit qui n’est pas partagé avec les autres collections dans la même base de données ou d’un compte. Une base de données DocumentDB est élastique dans la taille, allant de Go de stockage de document to SSD sauvegardés et débit mis en service. Contrairement à une base de données de SGBDR traditionnel, une base de données dans DocumentDB n’est pas une portée limitée à une seule machine et peut s’étendre sur plusieurs ordinateurs ou groupes.  

Avec DocumentDB, que vous devez faire évoluer vos applications, vous pouvez créer plus de collections ou de bases de données ou les deux. Bases de données peuvent être créés via le [portail Azure](documentdb-create-database.md) ou par l’intermédiaire de l’un des [Kits de développement logiciel DocumentDB](documentdb-dotnet-samples.md).   

## <a name="database-collections"></a>Collections de base de données
Chaque base de données DocumentDB peut contenir une ou plusieurs collections. Collections agissent comme des partitions de données hautement disponibles pour le traitement et le stockage des documents. Chaque collection peut stocker des documents à l’aide de schémas hétérogènes. L’indexation automatique de DocumentDB et des fonctions de requête vous permettent facilement filtrer et récupérer les documents. Une collection fournit la portée de l’exécution de requête et de stockage de document. Une collection est également un domaine de transaction de tous les documents qu’il contient. Collections sont allouées en fonction de la valeur définie dans le portail Azure ou via les kits de développement de débit. 

Collections sont automatiquement répartis dans un ou plusieurs serveurs physiques en DocumentDB. Lorsque vous créez une collection, vous pouvez spécifier le débit mis en service en termes d’unités de demande par seconde et une propriété de clé de partition. La valeur de cette propriété est utilisée par DocumentDB pour distribuer des documents entre des partitions et de router les requêtes comme des requêtes. La valeur de clé de partition est également la limite de transaction des procédures stockées et des déclencheurs. Chaque collection a un montant réservé spécifique à cette collection, qui n’est pas partagée avec les autres collections dans le même compte de débit. Par conséquent, vous pouvez évoluer votre application en termes de stockage et de débit. 

Collections peuvent être créées via le [portail Azure](documentdb-create-collection.md) ou par l’intermédiaire de l’un des [Kits de développement logiciel DocumentDB](documentdb-sdk-dotnet.md).   
 
## <a name="request-units-and-database-operations"></a>Demander des unités et des opérations de base de données

Lorsque vous créez une collection, vous réservez le débit en termes d' [unités de demande (RU)](documentdb-request-units.md) par seconde. À la place de penser et la gestion des ressources matérielles, vous pouvez envisager d’une **unité de la demande** comme une seule mesure pour les ressources requises pour effectuer différentes opérations de base de données et la demande d’une application de service. Lecture d’un document de 1 Ko consomme la même 1 RU, quel que soit le nombre d’éléments stockés dans la collection ou le nombre de demandes simultanées en cours d’exécution simultanément. Toutes les requêtes contre les DocumentDB, y compris les opérations complexes telles que les requêtes SQL ont une valeur RU prévisible qui peut être déterminée au moment du développement. Si vous connaissez la taille de vos documents et de la fréquence de chaque opération (lectures, écritures et requêtes) pour prendre en charge pour votre application, vous pouvez provisionner le montant exact de débit pour répondre aux besoins de votre application et mettre à l’échelle votre base de données de haut en bas en fonction des besoins de vos performances. 

Chaque collection peut être réservée avec le débit dans des blocs de 100 unités de demande par seconde, allant de quelques centaines à des millions d’unités de demande par seconde. Le débit de mise en service peut être réglé pendant toute la durée de vie d’une collection pour s’adapter aux besoins changeants de traitement et d’accéder aux modèles de votre application. Pour plus d’informations, voir [DocumentDB des niveaux de performance](documentdb-performance-levels.md). 

>[AZURE.IMPORTANT] Les collections sont des entités facturables. Le coût est déterminé par le débit de mise en service de la collection, mesurée en unités de demande par seconde avec le stockage total consommé en gigaoctets. 

Le nombre d’unités demande consomme une opération particulière, telles qu’insert, delete, requête ou exécution de procédure stockée ? Une unité de la demande est une mesure normalisée du coût de traitement de la demande. Lecture d’un document de 1 Ko est 1 RU, mais une demande d’insertion, de remplacer ou de supprimer le document même consomme plus de traitement à partir du service et, par conséquent plus d’unités demande. Chaque réponse du service inclut un en-tête personnalisé (`x-ms-request-charge`) qui indique les unités de requête utilisées pour la demande. Cet en-tête est également accessible via les [Kits de développement logiciel](documentdb-sdk-dotnet.md). Dans le Kit de développement .NET, [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) est une propriété de l’objet [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx) . Si vous voulez estimer vos besoins en débit avant d’effectuer un appel unique, vous pouvez utiliser le [Planificateur de capacités](documentdb-request-units.md#estimating-throughput-needs) pour aider à cette estimation. 

>[AZURE.NOTE] La ligne de base 1 unité de demande pour un document de 1 Ko correspond à un simple GET du document avec la [Cohérence de la Session](documentdb-consistency-levels.md). 

Il y a plusieurs facteurs qui influent sur les unités de demande utilisées pour une opération avec un compte de base de données DocumentDB. Ces facteurs sont les suivants :

- Taille du document. Comme les tailles de document augmentent les unités consommées pour lire ou écrire que les données augmentera.
- Nombre de propriétés. En supposant que par défaut l’indexation de toutes les propriétés, les unités utilisées pour écrire un document augmente à mesure que de l’augmentation du nombre de propriété.
- Cohérence des données. Lorsque vous utilisez des niveaux de cohérence des données de fortes ou d’obsolescence de limitées, des unités supplémentaires seront consommées pour lire des documents.
- Propriétés indexées. Une stratégie d’index sur chaque collection détermine les propriétés qui sont indexées par défaut. Vous pouvez réduire votre consommation d’unités demande en limitant le nombre de propriétés indexées. 
- L’indexation du document. Par défaut de que chaque document est automatiquement indexé, vous allez consommer moins d’unités demande si vous choisissez de ne pas indexer certains de vos documents.

Pour plus d’informations, reportez-vous à la section [unités de demande DocumentDB](documentdb-request-units.md). 

Par exemple, Voici un tableau qui indique le nombre d’unités demande à la disposition à trois tailles de document différent (1 Ko, 4 Ko et 64 Ko) et à deux niveaux de performance différents (500 lectures/s + 100 écritures/seconde et 500 lectures/s + 500 d’écritures/seconde). La cohérence des données a été configurée au niveau de la Session et la politique d’indexation a été définie sur aucun.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Taille du document</strong></p></td>
            <td valign="top"><p><strong>Lectures par seconde</strong></p></td>
            <td valign="top"><p><strong>Écritures/seconde</strong></p></td>
            <td valign="top"><p><strong>Unités de la demande</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1 000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3 000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1.3) + (100* 7) = RU/s 1,350</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1.3) + (500* 7) = RU/s 4,150</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9 800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = RU/s 29,000</p></td>
        </tr>
    </tbody>
</table>

Requêtes, procédures stockées et les déclencheurs consomment des unités de demande en fonction de la complexité des opérations en cours d’exécution. Lorsque vous développez votre application, vérifiez que l’en-tête de facture de demande pour mieux comprendre comment chaque opération consomme la capacité d’unité de demande.  


## <a name="choice-of-consistency-level-and-throughput"></a>Choix du niveau de cohérence et de débit
Le choix du niveau de cohérence par défaut a un impact sur le débit et la latence. Vous pouvez définir le niveau de cohérence par défaut à la fois par programme et via le portail Azure. Vous pouvez également remplacer le niveau de cohérence sur une base par demande. Par défaut, le niveau de cohérence est défini à la **Session**, qui fournit des lectures/écritures monotone et lire vos garanties d’écriture. Cohérence de la session est idéale pour les applications orientées utilisateur et fournit un équilibre parfait entre la cohérence et les performances des compromis.    

Pour obtenir des instructions sur la modification de votre niveau de cohérence sur le portail Azure, voir [comment gérer un compte DocumentDB](documentdb-manage-account.md#consistency). Ou, pour plus d’informations sur les niveaux de cohérence, voir [utilisation des niveaux de cohérence](documentdb-consistency-levels.md).

## <a name="provisioned-document-storage-and-index-overhead"></a>Frais de stockage et d’index de document mis en service
DocumentDB prend en charge la création de collections de partition unique et partitionnées. Chaque partition dans DocumentDB prend en charge jusqu'à 10 Go de stockage SSD sauvegardée. Le 10 Go de stockage de document inclut les documents plus le stockage de l’index. Par défaut, une collection de DocumentDB est configurée pour indexer automatiquement de tous les documents sans explicitement n’importe quel index secondaire ou le schéma. En fonction des applications à l’aide de DocumentDB, les coûts de type index est entre 2 à 20 %. La technologie d’indexation utilisée par DocumentDB garantit que les valeurs des propriétés, les coûts des index ne dépassent pas plus de 80 % de la taille des documents avec les paramètres par défaut. 

Par défaut tous les documents sont automatiquement indexés par DocumentDB. Toutefois, si vous souhaitez ajuster les coûts des index, vous pouvez choisir de supprimer certains documents indexés au moment de l’insertion ou remplacement d’un document, comme décrit dans [les stratégies d’indexation DocumentDB](documentdb-indexing-policies.md). Vous pouvez configurer une collection DocumentDB pour exclure tous les documents de la collection de l’indexation. Vous pouvez également configurer une collection DocumentDB pour sélective d’index uniquement certaines propriétés ou les chemins d’accès avec des caractères génériques de vos documents JSON, comme indiqué dans la [configuration de la stratégie d’indexation de la collection](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). À l’exclusion des documents ou des propriétés améliore également le débit d’écriture – ce qui signifie que vous allez consommer moins d’unités demande.   

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir sur le fonctionnement de la DocumentDB, consultez [partitionnement et mise à l’échelle dans Azure DocumentDB](documentdb-partition-data.md).

Pour plus d’informations sur la surveillance des niveaux de performance sur le portail Azure, voir [moniteur un compte DocumentDB](documentdb-monitor-accounts.md). Pour plus d’informations sur le choix des niveaux de performances pour les collections, consultez [niveaux de performances dans DocumentDB](documentdb-performance-levels.md).
 
