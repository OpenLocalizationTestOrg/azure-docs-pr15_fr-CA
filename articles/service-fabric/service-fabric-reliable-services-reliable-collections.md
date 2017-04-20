<properties
   pageTitle="Collections fiables | Microsoft Azure"
   description="Avec état services de Fabric de service fournissent des collections fiables qui vous permettent d’écrire des applications en nuage très disponible, évolutif et à faible latence."
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
   ms.workload="required"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduction aux Collections fiable dans les services de Fabric de Service Azure avec état

Collections fiables vous permettent de vous permettent d’écrire des applications en nuage très disponible, évolutif et à faible latence comme si vous écriviez des applications de l’ordinateur. Les classes dans l’espace de noms **Microsoft.ServiceFabric.Data.Collections** fournissent un ensemble de collections de-l’emploi qui automatiquement votre état de disponibilité. Les développeurs doivent programmer uniquement à l’API de Collection fiable et Collections fiables vous permettent de gérer l’état local et répliquée.

La principale différence entre les Collections fiable et d’autres technologies de haute disponibilité (par exemple, Redis, service Table Azure et service de file d’attente d’Azure) est que l’état est conservé localement dans l’instance de service tout aussi effectué hautement disponible. Cela signifie que :

- Toutes les lectures sont locales, ce qui résulte en une faible latence et haut débit lit.
- Toutes les écritures entraînent le nombre minimal d’e/s du réseau, ce qui se traduit par une faible latence et haut débit écrit.

![Image de l’évolution des collections.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Collections fiables peuvent être considérées comme l’évolution naturelle des classes **System.Collections** : un nouveau jeu de collections qui sont conçus pour les applications cloud et plusieurs ordinateurs sans accroître la complexité pour les développeurs. En tant que tel, Collections fiables sont les suivantes :

- Répliqué : Modifications de l’état sont répliquées pour une haute disponibilité.
- Persistant : Les données sont conservées sur le disque pour une durabilité contre les interruptions de service à grande échelle (par exemple, une panne de courant de centre de données).
- Asynchrone : API sont pour vous assurer que les threads ne sont pas bloqués lors de subir les e/s asynchrones.
- Transactionnelle : API utilisant l’abstraction des transactions vous permet de gérer facilement plusieurs Collections fiable au sein d’un service.

Collections fiables offrent la cohérence forte garantit l’emploi afin de faciliter la raisonnement sur l’état de l’application.
Forte cohérence est obtenue en s’assurant de transaction de fin valide uniquement après que la transaction a été enregistrée sur un quorum de la majorité des duplications, y compris le serveur principal.
Pour parvenir à une cohérence plus faible, applications peuvent accuser réception au demandeur/client avant le retour de la validation asynchrone.

Les API de Collections fiables sont une évolution des collections simultanées API (trouvée dans l’espace de noms **System.Collections.Concurrent** ) :

- Asynchrone : Retourne une tâche dans la mesure où, à la différence des collections simultanées, les opérations sont répliquées et rendues persistantes.
- Paramètres de sortie ne : utilise `ConditionalValue<T>` pour renvoyer une valeur booléenne et une valeur au lieu des paramètres de sortie. `ConditionalValue<T>`est semblable à `Nullable<T>` mais ne nécessite ne pas de T à présent d’un struct.
- Transactions : Utilise un objet de transaction pour permettre à l’utilisateur à des actions de groupe sur plusieurs Collections fiable dans une transaction.

Aujourd'hui, **Microsoft.ServiceFabric.Data.Collections** contient deux collections :

- [Dictionnaire fiable](https://msdn.microsoft.com/library/azure/dn971511.aspx): représente une collection répliquée, transactionnelle et asynchrone de paires clé/valeur. Similaire à **ConcurrentDictionary**, la clé et la valeur peuvent être de n’importe quel type.
- [File d’attente fiable](https://msdn.microsoft.com/library/azure/dn971527.aspx): représente un répliquées, transactionnelle et asynchrone strict, first-in, premier sorti (FIFO) file d’attente. Similaire à **ConcurrentQueue**, la valeur peut être de n’importe quel type.

## <a name="isolation-levels"></a>Niveaux d’isolement
Niveau d’isolement définit le degré auquel la transaction doit être isolée des modifications apportées par d’autres transactions.
Il existe deux niveaux d’isolation pris en charge dans les Collections fiable :

- **Repeatable Read**: Spécifie que les instructions ne peuvent pas lire les données qui ont été modifiées, mais pas encore validées par d’autres transactions et qu’aucune autre transaction ne peut modifier les données qui ont été lues par la transaction actuelle jusqu'à la fin de la transaction en cours. Pour plus d’informations, consultez [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Capture instantanée**: Spécifie que les données lues par toute instruction dans une transaction seront la version cohérente de façon transactionnelle des données qui existaient au début de la transaction.
La transaction peut reconnaître seulement les modifications de données qui ont été validées avant le début de la transaction.
Modifications de données effectuées par d’autres transactions après le début de la transaction actuelle ne sont pas visibles pour les instructions en cours d’exécution dans la transaction en cours.
L’effet est que si les instructions d’une transaction obtenir un cliché des données validées telles qu’elles existaient au début de la transaction.
Les snapshots sont cohérents dans Collections fiable.
Pour plus d’informations, consultez [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Collections fiables choisissent automatiquement le niveau d’isolation à utiliser pour une opération de lecture donnée en fonction de l’opération et le rôle du réplica au moment de la création de la transaction.
Voici la table qui représente par défaut de niveau d’isolement pour les opérations de dictionnaire fiable et file d’attente.

| Opération \ rôle      | Principal          | Secondaire        |
| --------------------- | :--------------- | :--------------- |
| Lecture d’entité unique    | Lecture répétable  | Capture instantanée         |
| Énumération \ Count   | Capture instantanée         | Capture instantanée         |

>[AZURE.NOTE] Sont des exemples courants d’opérations d’entité unique `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.

Le dictionnaire fiable et la file d’attente fiable prend en charge la lecture votre écrit.
En d’autres termes, toute écriture dans une transaction sera visible pour une lecture ci-dessous qui appartient à la même transaction.

## <a name="locking"></a>Verrouillage
Dans les Collections fiable, toutes les transactions sont en deux phases : une transaction ne libère pas les verrous qu’elle a acquise jusqu'à ce que la transaction se termine avec une validation ou d’abandon.

Dictionnaire fiable utilise un verrouillage de ligne pour toutes les opérations d’entité unique.
File d’attente fiable de transactions hors d’accès concurrentiel pour stricte propriété FIFO transactionnelle.
File d’attente fiable utilise des verrous de niveau d’opération permettant une transaction avec `TryPeekAsync` et/ou `TryDequeueAsync` et une transaction avec `EnqueueAsync` à la fois.
Notez que pour conserver FIFO, si une `TryPeekAsync` ou `TryDequeueAsync` jamais observe que la file d’attente fiable est vide, ils seront également verrouiller `EnqueueAsync`.

Écrire des opérations prennent toujours des verrous exclusifs.
Pour les opérations de lecture, le verrouillage dépend de plusieurs facteurs.
Toute opération de lecture effectuée à l’aide de l’isolement de cliché est libre de verrou.
Toute opération de lecture répétable par défaut utilise des verrous partagés.
Toutefois, l’utilisateur peut demander à toute opération de lecture qui prend en charge la lecture renouvelable, un verrou de mise à jour au lieu du verrou partagé.
Un verrou de mise à jour est un verrou asymétrique utilisé pour éviter les formes courantes de blocages qui se produisent lorsque plusieurs transactions verrouiller les ressources des mises à jour potentielles à une date ultérieure.

La matrice de compatibilité de verrouillage se trouvent ci-dessous :

| Demande de \ accordé | Aucun         | Partagé       | Mise à jour      | Exclusif    |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Partagé            | Aucun conflit  | Aucun conflit  | Conflit    | Conflit     |
| Mise à jour            | Aucun conflit  | Aucun conflit  | Conflit    | Conflit     |
| Exclusif         | Aucun conflit  | Conflit     | Conflit    | Conflit     |

Notez que l’argument de délai d’attente dans l’API de Collections fiable est utilisé pour la détection des verrous mortels.
Par exemple, deux transactions (T1 et T2) essayez de lire et de mettre à jour les K1.
Il est possible à un blocage, car ils sont tous deux avec le verrou partagé.
Dans ce cas, une ou plusieurs des opérations doit expirer.

Notez que le scénario de blocage ci-dessus est un bon exemple de comment un verrou de mise à jour peut empêcher les interblocages.

## <a name="persistence-model"></a>Modèle de persistance
Le Gestionnaire d’état fiable et fiable de Collections suivent un modèle de persistance qui est appelé point de contrôle et de journal.
Il s’agit d’un modèle où chaque changement d’état est enregistré sur le disque et appliquée uniquement en mémoire.
L’état complet lui-même est rendue persistante qu’occasionnellement (également appelé Point de contrôle).
L’avantage est que les deltas sont transformées en écritures ajout uniquement séquentielles sur disque pour améliorer les performances.

Pour mieux comprendre le modèle de journal et de point de contrôle, penchons-nous d’abord sur le scénario de disque infinie.
Le Gestionnaire d’état fiable enregistre chaque opération avant elle est répliquée.
Ainsi, la Collection fiable à appliquer uniquement l’opération dans la mémoire.
Dans la mesure où les journaux sont conservés, même lorsque le réplica tombe en panne et doit être redémarré, le Gestionnaire d’état fiable possède suffisamment d’informations dans ses journaux à relire toutes les opérations que le réplica a perdu.
Le disque est infini, enregistrements du journal doivent jamais être supprimés et la Collection fiable doit gérer l’état en mémoire uniquement.

Maintenant examinons le scénario disque finie.
Comme les enregistrements de journal s’accumulent, le Gestionnaire d’état fiable s’exécutera plus d’espace disque.
Avant cela, le Gestionnaire d’état fiable doit tronquer son journal pour faire de la place pour les nouveaux enregistrements.
Elle demandera à Collections fiable dans le point de contrôle leur état en mémoire sur le disque.
Il incombe les Collections fiable pour conserver son état à ce point.
Une fois que les Collections fiable terminer leurs points de contrôle, le Gestionnaire d’état fiable peut tronquer le journal afin de libérer de l’espace disque.
Ainsi, lorsque le réplica doit être redémarré, Collections fiable permet de récupérer leur état avec point de contrôle, et le Gestionnaire d’état fiable va récupérer et lire les modifications d’état ayant eu lieu depuis le point de contrôle.

>[AZURE.NOTE] Ajout d’une autre valeur de point de contrôle est qu’il améliore les performances de récupération dans les cas courants.
C’est parce que seules les dernières versions contiennent des points de contrôle.

## <a name="recommendations"></a>Recommandations

- Ne modifiez pas un objet de type personnalisé retourné par les opérations de lecture (par exemple, `TryPeekAsync` ou `TryGetValueAsync`). Les Collections fiables, à l’instar des Collections simultanées, renvoient une référence à ces objets et non une copie.
- Faire une copie complète de l’objet d’un type personnalisé avant de le modifier. Étant donné que les structures et les types intégrés sont passés par valeur, vous n’avez pas besoin effectuer une copie complète sur les.
- N’utilisez pas `TimeSpan.MaxValue` pour les délais d’attente. Les délais d’attente doivent être utilisés pour détecter des blocages.
- N’utilisez pas une transaction une fois qu’il a été validée, abandonnée ou supprimé.
- N’utilisez pas une énumération à l’extérieur de la portée de la transaction qu’il a été créé dans.
- Ne créez pas une transaction au sein d’une autre transaction `using` instruction car il peut provoquer des interblocages.
- Assurez-vous que votre `IComparable<TKey>` implémentation est correcte. Le système prend la dépendance sur ce pour la fusion des points de contrôle.
- Utilisez des verrous de mise à jour lors de la lecture d’un élément avec une intention de mise à jour pour empêcher une certaine classe de blocages.
- Envisagez d’utiliser la sauvegarde et de restauration des fonctionnalités pour que la reprise après sinistre.
- Éviter de mélanger des opérations d’entité unique et entités multiples (par ex. `GetCountAsync`, `CreateEnumerableAsync`) dans la même transaction en raison de niveaux d’isolement différents.

Voici quelques éléments à garder à l’esprit :

- Le délai d’attente par défaut est de 4 secondes pour toutes les API Collection fiable. La plupart des utilisateurs ne doivent pas substituer ceci.
- Le jeton d’annulation par défaut est `CancellationToken.None` dans toutes les API de Collections fiable.
- Le paramètre de type de clé (*TKey*) un dictionnaire fiable doit implémenter correctement `GetHashCode()` et `Equals()`. Les clés doivent être immuables.
- Pour atteindre une haute disponibilité pour les Collections fiable, chaque service doit avoir au moins une cible et un réplica minimal de taille 3.
- Les opérations de lecture sur le secondaire peuvent lire les versions qui ne sont pas validée de quorum.
Cela signifie qu’une version des données lues à partir d’un secondaire unique peut être false progressée.
Bien entendu, les lectures du principal sont toujours stables : false peut être jamais progressé.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide des Services fiable](service-fabric-reliable-services-quick-start.md)
- [Travailler avec des Collections fiables](service-fabric-work-with-reliable-collections.md)
- [Notifications de Services fiables](service-fabric-reliable-services-notifications.md)
- [Services fiables de sauvegarde et de restauration (reprise après sinistre)](service-fabric-reliable-services-backup-restore.md)
- [Configuration du Gestionnaire d’état de fiable](service-fabric-reliable-services-configuration.md)
- [Mise en route avec les services de l’API de Web Service Fabric](service-fabric-reliable-services-communication-webapi.md)
- [L’utilisation avancée de la modèle de programmation des Services fiables](service-fabric-reliable-services-advanced-usage.md)
- [Référence du développeur pour les Collections fiable](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
