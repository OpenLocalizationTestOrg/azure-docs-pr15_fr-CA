<properties
    pageTitle="Gérer l’accès concurrentiel dans le stockage Azure de Microsoft"
    description="Comment faire pour gérer l’accès concurrentiel pour les services de Blob, la file d’attente, Table et fichier"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jahogg"/>

# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gérer l’accès concurrentiel dans le stockage Azure de Microsoft

## <a name="overview"></a>Vue d’ensemble

Des applications Internet de base modernes ont généralement plusieurs utilisateurs, affichage et mise à jour des données simultanément. Cette opération nécessite les développeurs d’applications à réfléchir sur la façon de fournir une expérience prévisible à leurs utilisateurs finaux, en particulier pour les scénarios où plusieurs utilisateurs peuvent mettre à jour les mêmes données. Il existe trois stratégies de données principale d’accès concurrentiel considère généralement les développeurs :  


1.  L’accès concurrentiel optimiste-une application effectuant qu'une mise à jour dans le cadre de sa mise à jour vérifie si les données ont changé depuis l’application dernière lecture de ces données. Par exemple, si deux utilisateurs, affichage d’une page wiki effectuent une mise à jour à la même page puis la plate-forme wiki doit s’assurer que la deuxième mise à jour ne remplace pas la première mise à jour – et que les deux utilisateurs comprennent si leur mise à jour a réussi ou non. Cette stratégie est souvent utilisée dans les applications web.
2.  L’accès concurrentiel pessimiste – une application de recherche pour effectuer une mise à jour prendra un verrou sur un objet, empêchant les autres utilisateurs de mettre à jour les données jusqu'à ce que le verrou est libéré. Par exemple, dans un scénario de réplication de données maître/esclave où seul le masque effectuera les mises à jour le masque généralement contiendra un verrou exclusif pour une longue période de temps sur les données afin de garantir à que personne d’autre ne peut le mettre à jour.
3.  Dernier à écrire gagne – une approche qui permet des opérations de mise à jour continuer sans vérifier si une autre application a mis à jour les données depuis la demande tout d’abord lire les données. Cette stratégie (ou l’absence d’une stratégie formelle) est généralement utilisée lorsque les données sont partitionnées de manière à ce qu’il n’existe aucun risque que plusieurs utilisateurs n’accèdent pas les mêmes données. Il peut également être utile où les flux de données de courte durée sont en cours de traitement.  

Cet article fournit une vue d’ensemble de la façon dont la plate-forme de stockage Azure simplifie le développement en fournissant la prise en charge de première classe pour les trois de ces stratégies d’accès concurrentiel.  

## <a name="azure-storage--simplifies-cloud-development"></a>Stockage Azure – simplifie le développement en nuage
Le service de stockage Azure prend en charge tous les trois stratégies, bien qu’il soit dans sa capacité à fournir la prise en charge complète pour la concurrence d’accès optimiste et pessimiste car elle a été conçue pour intégrer un modèle de cohérence renforcée qui garantit que lorsque le service de stockage valide une insertion de données ou opération de mise à jour de tous les autres accède à pour données verront la dernière mise à jour. Plates-formes de stockage qui utilisent un modèle de cohérence éventuelle ont un retard entre lorsqu’une écriture est effectuée par un utilisateur et lorsque les données mises à jour peuvent être vu par d’autres utilisateurs, compliquant ainsi de développement d’applications client afin d’éviter les incohérences n’affectent les utilisateurs finaux.  

En plus de la sélection d’une stratégie d’accès concurrentiel approprié aux développeurs Sachez également comment une plate-forme de stockage isole les modifications – en particulier les modifications apportées au même objet au sein de transactions. Le service de stockage Azure utilise l’isolement de cliché pour permettre les opérations de lecture pour se produire en même temps que les opérations d’écriture dans une même partition. Contrairement à d’autres niveaux d’isolement, l’isolement de cliché garantit que toutes les lectures voient un instantané cohérent des données en cours de mises à jour – essentiellement en retournant les dernières valeurs validées lors d’une mise à jour de transaction est en cours de traitement.  

## <a name="managing-concurrency-in-blob-storage"></a>Gérer l’accès concurrentiel dans le stockage Blob
Vous pouvez choisir d’utiliser deux modèles d’accès concurrentiel optimiste ou pessimiste pour gérer l’accès à des conteneurs dans le service d’objet blob et les objets BLOB. Si vous ne spécifiez pas explicitement un wins écrit de la dernière stratégie est la valeur par défaut.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Accès concurrentiel optimiste pour les conteneurs et les objets BLOB  

Le service de stockage assigne un identificateur à chaque objet stocké. Cet identificateur est mis à jour chaque fois qu’une opération de mise à jour est effectuée sur un objet. L’identificateur est renvoyée au client dans le cadre d’une réponse HTTP GET à l’aide de l’en-tête ETag (balise d’entité) qui est défini dans le protocole HTTP. Un utilisateur d’effectuer une mise à jour sur un tel objet peut envoyer dans l’ETag d’origine avec un en-tête conditionnel afin de garantir qu’une mise à jour se produira uniquement si une certaine condition a été remplie : dans ce cas la condition est un en-tête « If-Match » qui requiert le Service de stockage vérifier la valeur de l’en-tête ETag spécifié dans la demande de mise à jour est identique à celui stocké dans le Service de stockage.  

Le contour de ce processus est le suivant :  

1.  Récupérer un objet blob à partir du service de stockage, la réponse inclut une valeur HTTP ETag en-tête qui identifie la version actuelle de l’objet dans le service de stockage.
2.  Lorsque vous mettez à jour le blob, incluez la valeur ETag obtenu à l’étape 1 dans l’en-tête **If-Match** du conditionnelle de la demande que vous envoyez au service.
3.  Le service compare la valeur ETag dans la requête avec la valeur ETag actuelle de l’objet blob.
4.  Si la valeur ETag actuelle de l’objet blob est une version différente de celle de l’ETag dans l’en-tête **If-Match** conditionnelle la demande, le service renvoie une erreur 412 au client. Ceci indique au client qu’un autre processus a mis à jour le blob dans la mesure où le client a récupéré.
5.  Si la valeur ETag actuelle de l’objet blob est la même version que l’ETag dans l’en-tête **If-Match** conditionnelle la demande, le service effectue l’opération demandée et met à jour la valeur ETag actuelle de l’objet blob pour montrer qu’il a créé une nouvelle version.  

L’extrait C# suivant (à l’aide de la bibliothèque de stockage Client 4.2.0) montre un simple exemple de comment construire une **If-Match AccessCondition** basé sur la valeur ETag qui est accessible à partir des propriétés d’un objet blob qui a été précédemment extraites ou insérées. Il utilise ensuite l’objet **AccessCondition** lorsqu’il le blob de mise à jour : l’objet **AccessCondition** ajoute l’en-tête **If-Match** à la demande. Si un autre processus a mis à jour le blob, le service d’objet blob renvoie un message d’état HTTP les 412 (Échec de la condition préalable). Vous pouvez télécharger l’exemple complet ici : [Gestion d’accès concurrentiel à l’aide du stockage Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

    // Retrieve the ETag from the newly created blob
    // Etag is already populated as UploadText should cause a PUT Blob call
    // to storage blob service which returns the etag in response.
    string orignalETag = blockBlob.Properties.ETag;

    // This code simulates an update by a third party.
    string helloText = "Blob updated by a third party.";

    // No etag, provided so orignal blob is overwritten (thus generating a new etag)
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}",
    blockBlob.Properties.ETag);

    // Now try to update the blob using the orignal ETag provided when the blob was created
    try
    {
        Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
        blockBlob.UploadText(helloText,accessCondition:
        AccessCondition.GenerateIfMatchCondition(orignalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
            // TODO: client can decide on how it wants to handle the 3rd party updated content.
        }
        else
            throw;
    }  

Le Service de stockage prend également en charge des en-têtes supplémentaires conditionnels tels que **If-Modified-Since**, **If-Unmodified-Since** et **If-None-Match** ainsi que leurs combinaisons. Pour plus d’informations consultez [Spécifiant les en-têtes conditionnelle pour les opérations de Service d’objet Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx) sur MSDN.  

Le tableau suivant récapitule les opérations de conteneur qui acceptent les en-têtes conditionnels tels que **If-Match** dans la demande et qui retournent une valeur ETag dans la réponse.  

| Opération                | Renvoie la valeur ETag de conteneur | Accepte les en-têtes conditionnelles |
|:-------------------------|:-----------------------------|:----------------------------|
| Créez le conteneur         | Oui                          | N°                          |
| Obtenir les propriétés du conteneur | Oui                          | N°                          |
| Obtenir les métadonnées du conteneur   | Oui                          | N°                          |
| Métadonnées du jeu de conteneur   | Oui                          | Oui                         |
| Obtenir le conteneur d’ACL        | Oui                          | N°                          |
| Définir des ACL de conteneur        | Oui                          | Oui (*)                     |
| Supprimer le conteneur         | N°                           | Oui                         |
| Conteneur de bail          | Oui                          | Oui                         |
| Liste des BLOB               | N°                           | N°                          |

(*) Les autorisations définies par le SetContainerACL sont mis en cache et les mises à jour de ces autorisations prennent 30 secondes pour propager les mises à jour ne sont pas garantis au cours de la période dans laquelle faire preuve de cohérence.  

Le tableau suivant récapitule les opérations d’objet blob qui acceptent les en-têtes conditionnels tels que **If-Match** dans la demande et qui retournent une valeur ETag dans la réponse.

| Opération           | Retourne une valeur ETag | Accepte les en-têtes conditionnelles           |
|:--------------------|:-------------------|:--------------------------------------|
| Placer les Blob            | Oui                | Oui                                   |
| Obtenir des Blob            | Oui                | Oui                                   |
| Obtenir les propriétés de l’objet Blob | Oui                | Oui                                   |
| Définir les propriétés de l’objet Blob | Oui                | Oui                                   |
| Obtenir les métadonnées de l’objet Blob   | Oui                | Oui                                   |
| Métadonnées du jeu de Blob   | Oui                | Oui                                   |
| Blob de bail (*)      | Oui                | Oui                                   |
| Blob de capture instantanée       | Oui                | Oui                                   |
| Copier des Blob           | Oui                | Oui (pour les blob source et de destination) |
| Abandonner le Blob de la copie     | N°                 | N°                                    |
| Supprimer des Blob         | N°                 | Oui                                   |
| Placez le bloc           | N°                 | N°                                    |
| Placez la liste de blocage      | Oui                | Oui                                   |
| Obtenir la liste de blocage      | Oui                | N°                                    |
| Placez la Page            | Oui                | Oui                                   |
| Les plages de pages Get     | Oui                | Oui                                   |


(*) Blob de bail ne modifie pas l’ETag sur un objet blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>L’accès concurrentiel pessimiste pour les objets BLOB
Pour verrouiller un objet blob pour usage exclusif, vous pouvez acquérir un [bail](http://msdn.microsoft.com/library/azure/ee691972.aspx) sur celui-ci. Lorsque vous acquérez un bail, vous spécifiez pendant combien de temps vous devez le bail : il peut s’agir d’entre 15 à 60 secondes ou infinie qui s’élève à un verrou exclusif. Vous pouvez renouveler un bail fini pour l’étendre, et vous pouvez libérer un bail lorsque vous avez terminé de l’utiliser. Le service d’objet blob libère automatiquement les baux finie lorsqu’ils expirent.  

Baux d’activer les stratégies de synchronisation différent être pris en charge, y compris une écriture exclusive / partagé écriture lecture, exclusif et exclusif de lecture partagée d’écriture / de lecture exclusive. Dans le cas où il existe un contrat de location le service de stockage applique exclusif écrit (placer, définir et supprimer des opérations) garantissant l’exclusivité pour les opérations de lecture nécessite toutefois le développeur pour s’assurer que toutes les applications clientes utilisent un ID de bail et qu’un seul client à la fois a un ID de bail valide. Les opérations de lecture qui n’incluent pas un résultat ID de location dans lit partagé.  

L’extrait de code C# suivant montre un exemple de l’acquisition d’un bail exclusif pendant 30 secondes sur un blob, mise à jour le contenu de l’objet blob et puis en relâchant le bail. S’il existe déjà un bail valide sur l’objet blob lorsque vous essayez d’obtenir un nouveau bail, le service d’objet blob retourne un résultat de « Conflit de HTTP (409) ». L’extrait de code ci-dessous utilise un objet **AccessCondition** pour encapsuler les informations de bail lorsqu’il émet une demande de mise à jour de l’objet blob dans le service de stockage.  Vous pouvez télécharger l’exemple complet ici : [Gestion d’accès concurrentiel à l’aide du stockage Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    // Acquire lease for 15 seconds
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation will succeed
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    //Simulate third party update to blob without lease
    try
    {
        // Below operation will fail as no valid lease provided
        Console.WriteLine("Trying to update blob without valid lease");
        blockBlob.UploadText("Update without lease, will fail");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
            Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
        else
            throw;
    }  

Si vous tentez une opération d’écriture sur un objet blob loué sans passer l’ID de location, la demande échoue avec une erreur 412. Notez que si le bail expire avant d’appeler la méthode **UploadText** , mais vous passez toujours l’ID de location, la demande échoue également avec une erreur **412** . Pour plus d’informations sur la gestion des délais d’expiration de bail et ID de location, consultez la documentation de reste de [Blob de bail](http://msdn.microsoft.com/library/azure/ee691972.aspx) .  

Les opérations d’objet blob suivantes permet de gérer l’accès concurrentiel pessimiste baux :  


-   Placer les Blob
-   Obtenir des Blob
-   Obtenir les propriétés de l’objet Blob
-   Définir les propriétés de l’objet Blob
-   Obtenir les métadonnées de l’objet Blob
-   Métadonnées du jeu de Blob
-   Supprimer des Blob
-   Placez le bloc
-   Placez la liste de blocage
-   Obtenir la liste de blocage
-   Placez la Page
-   Les plages de pages Get
-   Blob de capture instantanée - ID de location facultatif dans le cas d’un bail
-   ID de bail de Blob de copie - obligatoire si un bail existe sur l’objet blob de destination
-   ID de location de Blob de copie d’abandon - obligatoire si un bail infinie existe sur l’objet blob de destination
-   Blob de bail  

### <a name="pessimistic-concurrency-for-containers"></a>L’accès concurrentiel pessimiste pour conteneurs
Location de conteneurs activer les stratégies de synchronisation même être pris en charge que sur les objets BLOB (exclusif écriture / partagé écriture lecture, exclusif et exclusif de lecture partagée d’écriture / de lecture exclusive) toutefois à la différence des objets BLOB le service de stockage applique uniquement l’exclusivité sur les opérations de suppression. Pour supprimer un conteneur avec un bail actif, un client doit inclure l’ID de bail actif avec la demande de suppression. Toutes les autres opérations de conteneur aboutissent sur un conteneur loué sans inclure l’ID de location auquel cas ils sont partagés des opérations. Si l’exclusivité de la mise à jour (put ou set) ou des opérations de lecture est requise puis les développeurs doivent s’assurer que tous les clients utilisent un ID de location et que seul un client à la fois a un ID de bail valide.  

Les opérations de conteneur suivant permet de gérer l’accès concurrentiel pessimiste baux :  

-   Supprimer le conteneur
-   Obtenir les propriétés du conteneur
-   Obtenir les métadonnées du conteneur
-   Métadonnées du jeu de conteneur
-   Obtenir le conteneur d’ACL
-   Définir des ACL de conteneur
-   Conteneur de bail  

Pour plus d’informations, consultez :  

- [Spécification d’en-têtes conditionnelles pour les opérations de Service de Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Conteneur de bail](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Blob de bail](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Gérer l’accès concurrentiel dans le Service de la Table
Le service de la table utilise optimiste de contrôles d’accès concurrentiel en tant que le comportement par défaut, lorsque vous travaillez avec des entités, contrairement au service d’objet blob dans laquelle vous devez choisir explicitement pour effectuer des contrôles d’accès concurrentiel optimiste. L’autre différence entre les services de la table et l’objet blob est que vous pouvez gérer le comportement concurrentiel des entités uniquement qu’avec le service d’objet blob, vous pouvez gérer l’accès concurrentiel des conteneurs et des objets BLOB.  

Pour utiliser l’accès concurrentiel optimiste et vérifiez si un autre processus a modifié une entité depuis l’extraction à partir du service de stockage de table, vous pouvez utiliser la valeur ETag que lorsque le service de la table retourne une entité. Le contour de ce processus est le suivant :  

1.  Récupérer une entité à partir du service de stockage de table, la réponse inclut une valeur ETag qui identifie l’identificateur en cours associée à cette entité dans le service de stockage.
2.  Lorsque vous mettez à jour l’entité, incluez la valeur ETag obtenu à l’étape 1 dans l’en-tête **If-Match** obligatoire de la demande que vous envoyez au service.
3.  Le service compare la valeur ETag dans la requête avec la valeur ETag actuelle de l’entité.
4.  Si la valeur ETag actuelle de l’entité est différente de l’ETag dans l’en-tête **If-Match** obligatoire dans la demande, le service renvoie une erreur 412 au client. Ceci indique au client qu’un autre processus a mis à jour l’entité dans la mesure où le client a récupéré.
5.  Si la valeur ETag actuelle de l’entité est le même que l’ETag dans l’en-tête **If-Match** obligatoire dans la demande ou de l’en-tête **If-Match** contient le caractère générique (*), le service effectue l’opération demandée et met à jour la valeur ETag actuelle de l’entité dans laquelle il a été mis à jour.  

Notez que, contrairement au service d’objet blob, le service de la table requiert le client à inclure un en-tête **If-Match** dans les demandes de mise à jour. Toutefois, il est possible de forcer un inconditionnel (dernière stratégie wins de writer) de mettre à jour et de contourner les contrôles d’accès concurrentiel si le client affecte à l’en-tête **If-Match** le caractère générique (*) dans la demande.  

L’extrait de code C# suivant montre une entité client qui a été précédemment créée ou récupérée avec leur adresse e-mail mis à jour. L’initiale insérer ou extraire opération stocke la valeur ETag dans l’objet client et parce que l’exemple utilise la même instance d’objet lors de l’exécution de l’opération de remplacement, il envoie automatiquement la valeur ETag dans le service de la table, l’activation du service vérifier les violations d’accès concurrentiel. Si un autre processus a mis à jour l’entité dans le stockage de la table, le service renvoie un message d’état HTTP les 412 (Échec de la condition préalable).  Vous pouvez télécharger l’exemple complet ici : [Gestion d’accès concurrentiel à l’aide du stockage Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    try
    {
        customer.Email = "updatedEmail@contoso.org";
        TableOperation replaceCustomer = TableOperation.Replace(customer);
        customerTable.Execute(replaceCustomer);
        Console.WriteLine("Replace operation succeeded.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == 412)
            Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
        else
            throw;
    }  

Pour désactiver explicitement le contrôle d’accès concurrentiel, vous devez définir la propriété **ETag** de l’objet de **l’employé** à « * » avant d’exécuter l’opération de remplacement.  

    customer.ETag = "*";  

Le tableau suivant récapitule l’utilisent des valeurs ETag dans les opérations d’entité de table :

| Opération                | Retourne une valeur ETag | Nécessite l’en-tête de demande If-Match |
|:-------------------------|:-------------------|:---------------------------------|
| Entités de requête           | Oui                | N°                               |
| Insérer l’entité            | Oui                | N°                               |
| Entité de mise à jour            | Oui                | Oui                              |
| Fusion d’entité             | Oui                | Oui                              |
| Supprimer une entité            | N°                 | Oui                              |
| Insérer ou remplacer l’entité | Oui                | N°                               |
| Insertion ou entité de fusion   | Oui                | N°                               |

Notez que les opérations **Insert ou remplacer une entité** et **Insert ou fusionner une entité** *pas* effectuer toutes les vérifications d’accès concurrentiel, car ils n’envoient pas une valeur ETag pour le service de la table.  

En règle générale les développeurs qui utilisent des tables doivent dépendre d’accès concurrentiel optimiste lors du développement d’applications évolutives. Si le verrouillage pessimiste est nécessaire, les développeurs d’une approche peuvent prendre lorsque l’accès à des Tables est à affecter un blob désigné pour chaque table et essayez de prendre un bail sur le blob avant d’utiliser la table. Cette approche ne nécessite pas l’application Assurez-vous que tous les chemins d’accès aux données obtiennent le bail avant pour la table. Vous devez également noter que la durée du bail minimale est de 15 secondes qui requiert une attention particulière pour l’évolutivité.  

Pour plus d’informations, consultez :  

- [Opérations sur les entités](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gérer l’accès concurrentiel dans le Service de file d’attente
Un scénario dans lequel d’accès concurrentiel est un problème dans le service de file d’attente est où plusieurs clients récupérez des messages à partir d’une file d’attente. Lorsqu’un message est récupéré à partir de la file d’attente, la réponse inclut le message et une valeur de reçu pop, ce qui est nécessaire pour supprimer le message. Le message n’est pas automatiquement supprimé de la file d’attente, mais après que qu’elle a été récupérée, il n’est pas visible pour les autres clients pour l’intervalle de temps spécifié par le paramètre visibilitytimeout. Le client récupère le message est prévu pour supprimer le message une fois qu’elle a été traitée, et avant le délai spécifié par le TimeNextVisible élément de la réponse, qui est calculée en fonction de la valeur du paramètre visibilitytimeout. La valeur de visibilitytimeout est ajoutée à l’heure à laquelle le message est récupéré pour déterminer la valeur de TimeNextVisible.  

Le service de file d’attente n’a pas de prise en charge pour un accès concurrentiel optimiste ou pessimiste et que pour cette clients raison messages récupérés d’une file d’attente de traitement doivent garantir les messages sont traités d’une manière idempotent. Une dernière stratégie de wins writer est utilisée pour les opérations de mise à jour, tels que SetQueueServiceProperties, SetQueueMetaData, SetQueueACL et UpdateMessage.  

Pour plus d’informations, consultez :  

- [API de file d’attente Service REST](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Obtenir les Messages](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Gérer l’accès concurrentiel dans le Service de fichiers
Le service de fichiers sont accessibles à l’aide de deux protocoles différents points de terminaison – SMB et le reste. Le service reste n’a pas de prise en charge pour le verrouillage optimiste ou verrouillage pessimiste et les mises à jour suivent une dernière stratégie de wins de writer. Clients SMB monter les partages de fichiers peuvent tirer parti des mécanismes de verrouillage du système de fichiers pour gérer l’accès aux fichiers partagés, y compris la possibilité d’effectuer le verrouillage pessimiste. Lorsqu’un client SMB ouvre un fichier, elle spécifie l’accès aux fichiers et le partage de mode. Définition d’une option d’accès à un fichier de « Écriture » ou « Lecture » avec un mode de partage de fichiers de « None » entraîne le fichier verrouillé par un client SMB jusqu'à ce que le fichier est fermé. Si la tentative reste sur un fichier dans lequel un client SMB a verrouillé le fichier le service reste retournera le code statut 409 (conflit) avec le code d’erreur SharingViolation.  

Lorsqu’un client SMB ouvre un fichier pour le supprimer, il marque le fichier comme en attente de suppression jusqu'à ce que tous les autres clients SMB les descripteurs ouverts sur ce fichier sont fermés. Si un fichier est marqué comme en attente de suppression, toute opération reste sur le fichier retournera le code statut 409 (conflit) avec le code d’erreur SMBDeletePending. Code d’état 404 (introuvable) n’est pas retourné dans la mesure où il est possible que le client SMB supprimer l’indicateur de suppression en attente avant la fermeture du fichier. En d’autres termes, code d’état 404 (introuvable) devrait uniquement lorsque le fichier a été supprimé. Notez que lorsque le fichier est dans une PME/PMI en attente de l’état de la suppression, il ne sera pas être inclus dans les résultats de la liste des fichiers. Notez également que les opérations de suppression de fichier reste et reste supprimer le répertoire sont validées de manière atomique et n’entraînent pas d’attente de supprimer l’état.  

Pour plus d’informations, consultez :  

- [Gestion des fichiers de verrouillage](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Synthèse et étapes suivantes
Le service de stockage de Microsoft Azure a été conçu pour répondre aux besoins des applications en ligne plus complexes sans obliger les développeurs à compromettre ou de repenser les hypothèses de conception clés tels que de la cohérence des données et concurrence d’accès qui proviennent de prendre pour accordé.  

Pour l’exemple complet d’application référencée dans ce blog :  

- [Gérer l’accès concurrentiel à l’aide du stockage Azure - exemple d’Application](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Pour plus d’informations sur le stockage Azure, consultez :  

- [Page d’accueil de stockage Microsoft Azure](https://azure.microsoft.com/services/storage/)
- [Introduction au stockage Azure](storage-introduction.md)
- Mise en route pour les [Blob](storage-dotnet-how-to-use-blobs.md), [Table](storage-dotnet-how-to-use-tables.md), [files d’attente](storage-dotnet-how-to-use-queues.md)et les [fichiers](storage-dotnet-how-to-use-files.md) de stockage
- Architecture de stockage – [stockage Azure : un Service de stockage Cloud hautement disponible grâce à la cohérence fort](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
