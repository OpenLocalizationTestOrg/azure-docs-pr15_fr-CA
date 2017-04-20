<properties
   pageTitle="Les notifications de Services fiables | Microsoft Azure"
   description="Documentation conceptuelle pour les notifications de Service Fabric des Services fiables"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="reliable-services-notifications"></a>Notifications de Services fiables

Les notifications permettent aux clients d’effectuer le suivi des modifications en cours apportées à un objet qui les intéresse. Deux types d’objets prennent en charge les notifications : *Gestionnaire d’état fiable* et *Dictionnaire fiable*.

Raisons courantes de l’utilisation de notifications sont les suivantes :

- Bâtiment vue matérialisée, tels que des index secondaires ou agrégées des vues filtrées de l’état de son. Un exemple est un index trié de toutes les clés dans le dictionnaire fiable.
- Envoi de données surveillance, telles que le nombre d’utilisateurs ajoutés dans la dernière heure.

Notifications sont déclenchées dans le cadre de l’application des opérations. Pour cette raison, les notifications doivent être traitées aussi vite que possibles et synchrones des événements ne doit pas inclure les opérations coûteuses.

## <a name="reliable-state-manager-notifications"></a>Notifications du gestionnaire Reliable état

Gestionnaire d’état de Reliable fournit des notifications pour les événements suivants :

- Transaction
    - Valider
- Gestionnaire d’état
    - Reconstruction
    - Ajout d’un état fiable
    - Suppression d’un état fiable

Gestionnaire d’état de fiable assure le suivi des transactions METEOROLOGIQUE actuelles. La seule modification dans l’état qui provoque une notification de déclenchement de la transaction est une transaction en cours de validation.

Gestionnaire d’état de Reliable gère une collection d’états fiables comme dictionnaire fiable et de la file d’attente fiable. Gestionnaire d’état de Reliable déclenche des notifications lors de la modification de cette collection : un état fiable est ajouté ou supprimé, ou que la collection entière est recréée.
La collection fiable Gestionnaire d’état est reconstruite dans trois cas :

- Récupération : Lors du démarrage d’un réplica, il restaure son état d’origine à partir du disque. À la fin de la restauration, il utilise **NotifyStateManagerChangedEventArgs** pour déclencher un événement qui contient l’ensemble des États de fiables récupérés.
- Copie complète : avant un réplica peut joindre le jeu de configuration, elle doit être créée. Dans certains cas, cela requiert une copie complète de l’état du Gestionnaire d’état fiable à partir du réplica principal pour être appliqué au réplica secondaire inactif. Gestionnaire d’état de fiable sur le réplica secondaire utilise **NotifyStateManagerChangedEventArgs** pour déclencher un événement qui contient l’ensemble des États fiables qui proviennent de réplica principal.
- Restauration : Dans les scénarios de récupération d’urgence, état de son peut être restauré à partir d’une sauvegarde via **RestoreAsync**. Dans ce cas, le Gestionnaire d’état fiable sur le réplica principal utilise **NotifyStateManagerChangedEventArgs** pour déclencher un événement qui contient l’ensemble des États fiables qui il est restauré à partir de la sauvegarde.

Pour vous inscrire pour les notifications de transactions ou de notifications de gestionnaire d’état, vous devez enregistrer avec les événements **TransactionChanged** ou **StateManagerChanged** dans le Gestionnaire d’état fiable. Un emplacement commun pour enregistrer les gestionnaires d’événements est le constructeur de votre service avec état. Lorsque vous enregistrez dans le constructeur, vous ne perdrez aucune notification est provoquée par une modification pendant la durée de vie de **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Le Gestionnaire d’événements **TransactionChanged** utilise **NotifyTransactionChangedEventArgs** pour plus d’informations sur l’événement. Il contient la propriété action (par exemple, **NotifyTransactionChangedAction.Commit**) qui spécifie le type de modification. Il contient également la propriété de transaction qui fournit une référence à la transaction qui a été modifié.

>[AZURE.NOTE] Aujourd'hui, **TransactionChanged** sont déclenchés uniquement si la transaction est validée. L’action est alors égale à **NotifyTransactionChangedAction.Commit**. Mais à l’avenir, les événements peuvent être déclenchés pour les autres types de modifications de l’état transaction. Nous vous recommandons de vérifier l’action et le traitement de l’événement uniquement s’il en est une qui vous attendez.

Voici un exemple de gestionnaire d’événements **TransactionChanged** .

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Le Gestionnaire d’événements **StateManagerChanged** utilise **NotifyStateManagerChangedEventArgs** pour plus d’informations sur l’événement.
**NotifyStateManagerChangedEventArgs** a deux sous-classes : **NotifyStateManagerRebuildEventArgs** et **NotifyStateManagerSingleEntityChangedEventArgs**.
La propriété action dans **NotifyStateManagerChangedEventArgs** vous permet d’effectuer un cast de **NotifyStateManagerChangedEventArgs** à la sous-classe appropriée :

- **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
- **NotifyStateManagerChangedAction.Add** et **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Voici un exemple de gestionnaire de notification **StateManagerChanged** .

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notifications de dictionnaire fiables

Dictionnaire fiable fournit des notifications pour les événements suivants :

- Reconstruction : Appelé lorsque **ReliableDictionary** a restauré son état à partir d’une sauvegarde ou un état local de récupérés ou copié.
- Effacer : Appelée lorsque l’état de **ReliableDictionary** a été effacé par le biais de la méthode **ClearAsync** .
- Ajouter : Appelé lorsqu’un élément a été ajouté à **ReliableDictionary**.
- Mise à jour : Appelé lorsqu’un élément de **IReliableDictionary** a été mis à jour.
- Supprimer : Appelé lorsqu’un élément de **IReliableDictionary** a été supprimé.

Pour obtenir des notifications de dictionnaire fiable, vous devez enregistrer avec le Gestionnaire d’événements **DictionaryChanged** sur **IReliableDictionary**. Une pour enregistrer ces gestionnaires d’événements est courant dans les **ReliableStateManager.StateManagerChanged** ajouter une notification.
Enregistrement lors de l’ajout de **IReliableDictionary** à **IReliableStateManager** garantit que vous ne manquez pas les notifications.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] **ProcessStateManagerSingleEntityNotification** exemple représente la méthode qui appelle de l’exemple précédent de la **OnStateManagerChangedHandler** .

Le code précédent définit l’interface **IReliableNotificationAsyncCallback** , et **DictionaryChanged**. Car **NotifyDictionaryRebuildEventArgs** contient une interface **IAsyncEnumerable** --qui doit être énumérés de manière asynchrone--reconstruire les notifications sont déclenchées par le biais de **RebuildNotificationAsyncCallback** au lieu de **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] Tout d’abord dans le code précédent, dans le cadre du traitement de la notification de la régénération, l’état de synthèse mise à jour est désactivée. Étant donné que la collection fiable avec un nouvel état est en cours de reconstruction, toutes les notifications précédentes sont sans importance.

Le Gestionnaire d’événements **DictionaryChanged** utilise **NotifyDictionaryChangedEventArgs** pour plus d’informations sur l’événement.
**NotifyDictionaryChangedEventArgs** a cinq sous-classes. Utilisez la propriété action dans **NotifyDictionaryChangedEventArgs** d’effectuer un cast de **NotifyDictionaryChangedEventArgs** à la sous-classe appropriée :

- **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
- **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
- **NotifyDictionaryChangedAction.Add** et **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
- **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
- **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recommandations

- ** N’effectuez pas aussi rapidement que possible les événements de notification.
- *Pas* d’exécuter les opérations coûteuses (par exemple, les opérations d’e/s) dans le cadre d’événements synchrones.
- *Contrôlez le type d’action avant de traiter l’événement.* Nouveaux types d’action peuvent être ajoutés à l’avenir.

Voici quelques éléments à garder à l’esprit :

- Notifications sont déclenchées dans le cadre de l’exécution d’une opération. Par exemple, une restauration notification est déclenchée comme la dernière étape d’une opération de restauration. Une restauration ne se terminera pas tant que l’événement de notification est traité.
- Parce que les notifications sont déclenchées dans le cadre d’opérations de l’application, les clients voient uniquement les notifications pour les opérations validées localement. Et parce que les opérations sont garanties uniquement localement validée (en d’autres termes, connecté), ils peuvent ou ne peuvent pas être annulées à l’avenir.
- Sur le chemin d’accès du rétablissement, une seule notification est déclenchée pour chaque opération appliquée. Cela signifie que si la transaction T1 inclut Create(X), Delete(X) et Create(X), vous obtiendrez une notification pour la création de X, une pour la suppression et une pour la création de nouveau, dans cet ordre.
- Pour les transactions qui contiennent plusieurs opérations, les opérations sont appliquées dans l’ordre dans lequel ils ont été reçus sur le réplica principal de l’utilisateur.
- Dans le cadre du traitement de progression false, certaines opérations peuvent être annulées. Notifications sont déclenchées pour des opérations d’annulation, restauration de l’état du réplica un point stable. Une différence importante des notifications d’annulation est que les événements qui ont des clés en double sont agrégés. Par exemple, si la transaction T1 est en cours d’annulation, vous verrez une seule notification à Delete(X).

## <a name="next-steps"></a>Étapes suivantes

- [Collections fiables](service-fabric-work-with-reliable-collections.md)
- [Démarrage rapide des Services fiable](service-fabric-reliable-services-quick-start.md)
- [Services fiables de sauvegarde et de restauration (reprise après sinistre)](service-fabric-reliable-services-backup-restore.md)
- [Référence du développeur pour les Collections fiable](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
