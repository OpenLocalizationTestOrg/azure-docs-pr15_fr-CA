<properties
    pageTitle="Travailler avec des Collections fiables | Microsoft Azure"
    description="Découvrez les meilleures pratiques pour travailler avec des Collections fiable."
    services="service-fabric"
    documentationCenter=".net"
    authors="JeffreyRichter"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="03/28/2016"
    ms.author="jeffreyr" />

# <a name="working-with-reliable-collections"></a>Travailler avec des Collections fiables

TISSU de service offre un modèle de programmation avec état disponible aux développeurs .NET par l’intermédiaire des Collections fiable. En particulier, le Service Fabric fournit dictionnaire fiable et classes de file d’attente fiable. Lorsque vous utilisez ces classes, votre état est partitionné en deux (évolutivité) répliqué (pour une disponibilité) et traité dans une partition (pour la sémantique ACID). Nous allons examiner une utilisation courante d’un objet de dictionnaire fiable et déterminer quelles sont ses réellement.

~~~
retry:
try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to  
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) { 
   await Task.Delay(100, cancellationToken); goto retry; 
}
~~~

Toutes les opérations sur les objets de dictionnaire fiable (à l’exception de ClearAsync qui n’est pas annulable), nécessitent un objet ITransaction. Cet objet est associée à un et que vous tentez de le faire à n’importe quel dictionnaire fiable et/ou fiable de toutes les modifications en file d’attente les objets dans une même partition. Vous acquérez une ITransaction méthode de CreateTransaction de StateManager de l’objet en appelant la partition.
 
Dans le code ci-dessus, l’objet ITransaction est passé à la méthode de AddAsync d’un dictionnaire fiable. En interne, les méthodes de dictionnaire qui accepte une clé prennent un verrou de lecteur/writer associé à la clé. Si la méthode modifie la valeur de la clé, la méthode prend un verrou en écriture sur la clé, et si la méthode lit uniquement à partir de la valeur de la clé, un verrou en lecture est effectuée sur la clé. Dans la mesure où AddAsync modifie la valeur de clé à la nouvelle valeur transmise, verrouillage en écriture de la clé est prise. Ainsi, si vous essaient d’ajouter des valeurs à la fois avec la même clé threads 2 (ou plus), un thread sera acquérir le verrou d’écriture et les autres threads bloquent. Par défaut, les méthodes bloquent pendant 4 secondes acquérir le verrou. après 4 secondes, les méthodes lèvent une exception TimeoutException. Existe des surcharges de méthode qui vous permet de passer une valeur de délai d’attente explicite si vous préférez.
 
En général, vous écrivez le code pour réagir à une TimeoutException en interceptant et réessayer l’opération entière (comme illustré dans le code ci-dessus). Dans mon code simple, j’appelle simplement Task.Delay en passant de 100 millisecondes chaque fois. Mais, en réalité, il est préférable de désactiver à l’aide d’une sorte de retard de recul exponentielle à la place.
 
Une fois que le verrou est acquis, AddAsync ajoute les références d’objet clé et la valeur d’un dictionnaire temporaire interne associé à l’objet ITransaction. Cela est fait pour vous fournir la sémantique en lecture-your-propriétaire-écrit. Autrement dit, après l’appel à AddAsync, un appel ultérieur à TryGetValueAsync (à l’aide du même objet ITransaction) retournera la valeur même si vous n’avez pas encore validé la transaction. Ensuite, AddAsync sérialise la clé et valeur des objets dans les tableaux d’octets et ajoute ces tableaux d’octets dans un fichier journal sur le nœud local. Enfin, AddAsync envoie les tableaux d’octets pour tous les réplicas secondaires afin qu’ils puissent les mêmes informations de clé/valeur. Même si les informations de clé/valeur a été écrit dans un fichier journal, les informations ne considérées pas partie du dictionnaire jusqu'à ce qu’ils sont associés avec la transaction a été validée. 

Dans le code ci-dessus, l’appel à CommitAsync valide toutes les opérations de la transaction. Plus précisément, il ajoute des informations de validation dans le fichier journal sur le nœud local et envoie également l’enregistrement de validation pour tous les réplicas secondaires. Une fois un quorum (majorité) des réplicas a répondu, toutes les modifications de données sont considérées comme permanentes et les verrous associés aux clés qui ont été manipulées via l’objet ITransaction sont libérées afin que les autres threads/transactions peut manipuler les mêmes clés et leurs valeurs.

Si CommitAsync n’est pas appelée (généralement en raison de la levée d’une exception), puis l’objet ITransaction sont détruit. Lors de la cession d’un objet ITransaction non validée, Fabric du Service ajoute des informations d’abandon pour le fichier de journal du nœud local et rien ne doit être envoyée à n’importe quel réplica secondaire. Et ensuite, tous les verrous associés aux clés qui ont été manipulées par la transaction sont libérées.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Les pièges courants et comment les éviter
Maintenant que vous comprenez comment les collections fiables fonctionnent en interne, examinons certaines communes injuste de leur. Consultez le code ci-dessous :

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes, 
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
~~~

Lorsque vous travaillez avec un dictionnaire .NET standard, vous pouvez ajouter une clé/valeur du dictionnaire et puis modifiez la valeur d’une propriété (par exemple, LastLogin). Toutefois, ce code ne fonctionnera pas correctement avec un dictionnaire fiable. N’oubliez pas de la discussion précédente, l’appel à AddAsync sérialise les objets de clé/valeur pour les tableaux d’octets et les baies puis l’enregistre dans un fichier local et les envoie également aux réplicas secondaires. Si vous modifiez ultérieurement une propriété, cela modifie la valeur de propriété dans la mémoire uniquement ; Il n’influe pas sur le fichier local ou les données envoyées dans les réplicas. Si le processus se bloque, ce qui est en mémoire est rejeté. Démarrage d’un nouveau processus ou si un autre réplica devient principal, l’ancienne valeur de la propriété est ce qui est disponible. 

Je ne le répéterai jamais assez combien il est facile pour le type d’erreur indiqué ci-dessus. Et bien, vous seulement découvrirez l’erreur if/quand que le processus tombe en panne. La manière correcte d’écrire le code consiste simplement à inverser les deux lignes :

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync(); 
}
~~~

Voici un autre exemple illustrant une erreur courante :

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync(); 
   }
}
~~~

À nouveau, avec les dictionnaires régulières .NET, le code ci-dessus fonctionne correctement et est un modèle commun : le développeur utilise une clé pour rechercher une valeur. Si la valeur existe, le développeur modifie la valeur d’une propriété. Toutefois, avec les collections fiables, ce code présente le même problème comme indiqué précédemment : __vous ne devez pas modifier un objet une fois que vous avez donné une collection fiable.__
 
La façon correcte des mettre à jour une valeur dans une collection fiable, est d’obtenir une référence à la valeur existante et prenez l’objet référencé par cette référence immuable. Ensuite, créez un nouvel objet qui est une copie exacte de l’objet d’origine. Maintenant, vous pouvez modifier l’état de ce nouvel objet et écrire le nouvel objet dans la collection afin qu’elle sérialisé en tableaux d’octets, ajouté au fichier local et envoyés dans les réplicas. Après avoir effectué les modifications, les objets en mémoire, le fichier local et tous les réplicas ont le même état exact. Tout est bon !

Le code suivant montre la façon correcte des mettre à jour une valeur dans une collection fiable :

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire 
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync(); 
   }
}
~~~

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Définir les types de données immuables afin d’éviter l’erreur du programmeur

Dans l’idéal, nous aimerions le compilateur à signaler des erreurs lorsque vous produisez accidentellement code qui transforme l’état d’un objet que vous êtes censé prendre en compte immuable. Toutefois, le compilateur C# n’a pas la possibilité de le faire. Par conséquent, pour éviter les bogues potentiels de programmeur, il est vivement recommandé que vous définissez les types vous utilisez avec des collections fiables pour être des types immuables. Plus précisément, cela signifie que vous respectez les types valeur de base (par exemple, des nombres [Int32, UInt64, etc.], dateheure, Guid, TimeSpan, etc.). Et, bien sûr, vous pouvez également utiliser des chaînes. Il est préférable d’éviter les propriétés de collection en tant que la sérialisation et leur désérialisation peut fréquemment peut gêner les performances. Toutefois, si vous souhaitez utiliser les propriétés de la collection, nous recommandons vivement l’utilisation de. Bibliothèque de collections inaltérables du NET (System.Collections.Immutable). Cette bibliothèque est disponible au téléchargement à partir de http://nuget.org. Nous vous recommandons également de vos classes de scellement et de rendre les champs en lecture seule chaque fois que possible.

Le type de UserInfo ci-dessous montre comment définir un type immuable en tirant parti des recommandations susmentionnées.

~~~
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;
 
   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }
 
   [DataMember]
   public readonly String Email;
 
   // Ideally, this would be a readonly field but it can't be because OnDeserialized 
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
~~~

Le type ItemId est également un type immuable comme indiqué ici :

~~~
[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
~~~

## <a name="schema-versioning-upgrades"></a>La gestion des versions de schéma (mises à niveau)

En interne, les Collections fiable de sérialiser vos objets à l’aide de. DataContractSerializer du NET. Les objets sérialisés sont conservées sur le disque local du réplica principal et sont également transmises aux réplicas secondaires. Votre service arrivera à échéance, il est probable que vous voudrez modifier le type de données (schéma), que votre service requiert. Vous devez aborder la gestion des versions de vos données avec une extrême prudence. Tout d’abord, vous devez être toujours en mesure de désérialiser des données anciennes. En particulier, cela signifie que votre code de désérialisation doit être infini descendante : 333 de Version de votre code de service doit être en mesure d’effectuer cette opération sur les données placées dans une collection fiable par la version 1 de votre code de service, il y a 5 ans.

En outre, le code de service est mis à niveau un domaine de mise à niveau à la fois. Par conséquent, au cours d’une mise à niveau, vous avez deux versions différentes de votre code de service en cours d’exécution simultanément. Vous devez éviter d’avoir à la nouvelle version de votre code de service d’utiliser le nouveau schéma comme les anciennes versions de votre code de service n’est peut-être pas en mesure de gérer le nouveau schéma. Lorsque cela est possible, vous devez concevoir chaque version de votre service pour être compatibles en 1 version. Plus précisément, cela signifie que V1 de votre code de service doit pouvoir simplement ignorer les éléments de schéma que ne gère pas explicitement. Toutefois, il doit être en mesure d’enregistrer toutes les données qu’il ne connaît explicitement et simplement enregistrer à nouveau lors de la mise à jour d’une valeur ou une clé de dictionnaire. 

>[AZURE.WARNING] Alors que vous pouvez modifier le schéma d’une clé, vous devez vous assurer que le code de hachage de la clé et les algorithmes est égal à sont stables. Si vous modifiez le fonctionnement d’un de ces algorithmes, vous ne serez pas en mesure de rechercher la clé dans le dictionnaire fiable jamais.
  
Sinon, vous pouvez effectuer ce qui est généralement appelé une mise à niveau phase 2. Une mise à niveau phase 2, vous passez au service de la vue V1 V2 : V2 contient le code qui sait comment traiter les nouvelles modifications du schéma, mais ce code ne s’exécute pas. Lorsque le code V2 lit les données de V1, il fonctionne sur elle et écrit les données de V1. Puis, une fois la mise à niveau terminée sur mise à niveau de tous les domaines, vous pouvez signaler aux instances V2 en cours d’exécution que la mise à niveau est terminée. (C’est un moyen de signal de déployer une mise à niveau de la configuration ; c’est ce qui en fait une mise à niveau phase 2). Maintenant, les instances de V2 peuvent lire les données de V1, convertir en données V2, exploiter et écrire en tant que données de V2. Lorsque d’autres instances de lire les données V2, ils n’ont pas besoin pour le convertir, ils fonctionnent sur celui-ci, simplement et écrivent les données de V2.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la création de contrats de transfert de données compatibles, consultez [Les contrats de données à compatibilité ascendante](https://msdn.microsoft.com/library/ms731083.aspx).

Pour découvrir les meilleures pratiques sur la gestion des versions des contrats de données, reportez-vous à la section [Versions des contrats de données](https://msdn.microsoft.com/library/ms731138.aspx). 

Pour savoir comment implémenter des contrats de données à tolérance de version, voir [Rappels de sérialisation avec tolérance de Version](https://msdn.microsoft.com/library/ms733734.aspx). 

Pour savoir comment fournir une structure de données qui peut interagir entre plusieurs versions, consultez [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
