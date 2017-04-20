<properties
    pageTitle="Synchronisation de données hors connexion dans les applications mobiles Azure | Microsoft Azure"
    description="Référence conceptuelle et vue d’ensemble de la fonctionnalité de synchronisation de données en mode hors connexion pour les applications mobiles Azure"
    documentationCenter="windows"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="offline-data-sync-in-azure-mobile-apps"></a>Synchronisation de données hors connexion dans les applications mobiles Azure

## <a name="what-is-offline-data-sync"></a>Quelle est la synchronisation de données hors connexion ?

Synchronisation de données en mode hors connexion est un client et un serveur fonctionnalité Kit de développement d’applications Mobile Azure qui permet aux développeurs de créer des applications qui fonctionne sans connexion réseau.

Lorsque votre application est en mode hors connexion, les utilisateurs peuvent toujours créer et modifier des données, qui seront enregistrées dans un magasin local. Lorsque l’application est en ligne, il peut synchroniser des modifications locales avec la principale application de Mobile Azure. La fonction prend également en charge la détection de conflit lorsque le même enregistrement est modifié sur le client et le serveur principal. Conflits peuvent ensuite être traitées sur le serveur ou le client.

Synchronisation hors connexion présente un certain nombre d’avantages :

* Améliorer la réactivité de l’application par la mise en cache de données du serveur local sur le périphérique
* Créer des applications robustes qui restent utiles lorsqu’il y a des problèmes de réseau
* Permettre aux utilisateurs finaux de créer et de modifier les données même lorsqu’il n’y a pas d’accès réseau, prise en charge des scénarios avec peu ou pas de connectivité
* Synchroniser des données à travers plusieurs périphériques et de détecter les conflits lorsque le même enregistrement est modifié par deux périphériques
* Limiter l’utilisation du réseau sur les réseaux à latence élevée ou contrôlés

Les didacticiels suivants montrent comment ajouter la synchronisation hors connexion pour les clients mobiles à l’aide d’applications de Mobile Azure :

* [Android : Activer la synchronisation hors connexion]
* [iOS : activer la synchronisation hors connexion]
* [Xamarin iOS : activer la synchronisation hors connexion]
* [Android de Xamarin : Activer la synchronisation hors connexion]
* [Xamarin.Forms : Activer la synchronisation hors connexion](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Universel Windows plate-forme : Activer la synchronisation hors connexion]

## <a name="what-is-a-sync-table"></a>Ce qu’est une table de synchronisation ?

Pour accéder au point de terminaison « / tables », le client Mobile d’Azure SDK fournissent des interfaces telles que `IMobileServiceTable` (client .NET SDK) ou `MSTable` (client d’e/s). Ces API vous connecter directement sur le back-end Azure Mobile App et échouera si le périphérique client ne dispose pas d’une connexion réseau.

Pour prendre en charge une utilisation en mode hors connexion, votre application doit plutôt utiliser la *table de synchronisation* API, telles que `IMobileServiceSyncTable` (client .NET SDK) ou `MSSyncTable` (client d’e/s). Les mêmes opérations CRUD (Create, lecture, mise à jour, supprimer) fonctionnent par rapport à la table de synchronisation API, mais maintenant elles seront de lire ou d’écrire dans un *magasin local*. Avant de pouvoir effectuer des opérations de table de synchronisation, la banque locale doit être initialisée.

## <a name="what-is-a-local-store"></a>Quel est un magasin local ?

Un magasin local est la couche de persistance des données sur le périphérique client. Les kits de développement Azure Mobile applications client fournissent une implémentation de magasin local par défaut. Sous Windows, Xamarin et Android, il est basé sur SQLite ; dans iOS, il est basé sur les données de base.

Pour utiliser l’implémentation SQLite sur Windows Phone ou Windows Store 8.1, vous devez installer une extension SQLite. Pour plus d’informations, consultez [Universal Windows plate-forme : activer la synchronisation hors connexion]. Android et iOS sont livrés avec une version de SQLite dans le système d’exploitation du périphérique lui-même, il n’est pas nécessaire de référencer votre propre version de SQLite.

Les développeurs peuvent également implémenter leur propre magasin local. Par exemple, si vous souhaitez stocker les données dans un format chiffré sur le client mobile, vous pouvez définir un magasin local qui utilise SQLCipher pour le cryptage.

## <a name="what-is-a-sync-context"></a>Ce qu’est un contexte de synchronisation ?

Un *contexte de synchronisation* est associé à un objet de client mobile (tel que `IMobileServiceClient` ou `MSClient`) et effectue le suivi des modifications apportées à des tables de synchronisation. Le contexte de synchronisation gère une *file d’attente de l’opération* qui conserve une liste ordonnée de l’opérations CUD (Create, Update, Delete) qui sera ensuite envoyé au serveur.

Un magasin local est associé avec le contexte de synchronisation à l’aide d’une méthode d’initialisation telles que `IMobileServicesSyncContext.InitializeAsync(localstore)` dans le [client de .NET SDK].

## <a name="how-sync-works"></a>Fonctionnement hors connexion de la synchronisation

Lorsque vous utilisez les tables de synchronisation, votre code client contrôle lorsque des modifications locales seront synchronisées avec un back-end de l’application Mobile de Azure. Rien n’est envoyé sur le serveur principal jusqu'à ce qu’un appel à la *transmission* des modifications locales. De même, la banque locale est remplie avec les nouvelles données uniquement s’il existe un appel pour *Extraire* des données.

* **Push**: Push est une opération sur le contexte de synchronisation et les envoie toutes les modifications CUD depuis la dernière diffusion. Notez qu’il n’est pas possible d’envoyer uniquement les modifications d’une table individuelle, car sinon opérations a pu être envoyées de manière désordonnées. Push exécute une série d’appels reste à votre back-end Azure Mobile App, qui à son tour modifiera votre base de données du serveur.

* **Extraction**: extraction est effectuée sur une base par table et peuvent être personnalisée avec une requête pour récupérer uniquement un sous-ensemble des données du serveur. Les kits de développement client Mobile d’Azure insérer ensuite les données résultantes dans le magasin local.

* **Exécute un push d’implicite**: si une extraction est exécutée sur une table qui est en attente de mises à jour locales, l’extraction préalable exécutera un push sur le contexte de synchronisation. Cela permet de réduire les conflits entre les modifications qui sont déjà en file d’attente et de nouvelles données à partir du serveur.

* **Synchronisation incrémentielle**: le premier paramètre de l’opération d’extraction est un *nom de requête* qui est utilisé uniquement sur le client. Si vous utilisez un nom de requête de valeur non-null, le Kit de développement Azure Mobile effectue une *synchronisation incrémentielle*.
  Chaque fois qu’une opération d’extraction renvoie un jeu de résultats, la dernière `updatedAt` l’horodatage à partir de ce jeu de résultats est stocké dans les tables de système local du Kit de développement logiciel. Opérations d’extraction ultérieure n’extrait que les enregistrements après cette estampille.

  Pour utiliser la synchronisation incrémentielle, votre serveur doit retourner significatif `updatedAt` valeurs et devez prennent également en charge le tri par ce champ. Toutefois, étant donné que le Kit de développement logiciel ajoute son propre tri sur le champ updatedAt, vous ne pouvez pas utiliser une requête d’extraction qui a son propre `$orderBy$` clause.

  Le nom de la requête peut être toute chaîne que vous choisissez, mais il doit être unique pour chaque requête logique dans votre application.
  Dans le cas contraire, les opérations d’extraction différente pourraient écraser le même horodatage de synchronisation incrémentielle et vos requêtes peuvent renvoyer des résultats incorrects.

  Si la requête comporte un paramètre, une pour créer un nom de requête unique consiste à intégrer la valeur du paramètre.
  Par exemple, si vous filtrez sur le nom d’utilisateur, le nom de votre requête peut être comme suit (dans C#) :

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Si vous souhaitez refuser de synchronisation incrémentielle, passez `null` comme l’ID de la requête. Dans ce cas, tous les enregistrements seront récupérées à chaque appel de `PullAsync`, qui est potentiellement inefficace.

* **Purge**: vous pouvez effacer le contenu du magasin local en utilisant `IMobileServiceSyncTable.PurgeAsync`.
  Cela peut être nécessaire si vous avez des données périmées dans la base de données client, ou si vous souhaitez ignorer toutes les modifications en attente.

  Une purge efface un tableau à partir du magasin local. S’il existe des opérations en attente de synchronisation avec la base de données du serveur, la purge lèvera une exception, à moins que le paramètre *force de purge* est défini.

  Un exemple de données obsolètes sur le client, supposons que dans l’exemple de la « liste todo », appareil1 extrait uniquement les éléments qui ne sont pas terminées. Puis, un todoitem « Acheter le lait » est marquée comme terminée sur le serveur par un autre périphérique. Toutefois, appareil1 disposera de la todoitem « Lait acheter » dans le magasin local car il est retiré uniquement des éléments qui ne sont pas marquées comme achevées. Une purge efface cet élément obsolète.

## <a name="next-steps"></a>Étapes suivantes

* [iOS : activer la synchronisation hors connexion]
* [Xamarin iOS : activer la synchronisation hors connexion]
* [Android de Xamarin : Activer la synchronisation hors connexion]
* [Universel Windows plate-forme : Activer la synchronisation hors connexion]

<!-- Links -->
[Client .NET SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android : Activer la synchronisation hors connexion]: app-service-mobile-android-get-started-offline-data.md
[iOS : activer la synchronisation hors connexion]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS : activer la synchronisation hors connexion]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Android de Xamarin : Activer la synchronisation hors connexion]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Universel Windows plate-forme : Activer la synchronisation hors connexion]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
