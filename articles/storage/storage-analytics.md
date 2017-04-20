<properties
    pageTitle="Analytique de stockage permet de collecter des données de journaux et de mesures | Microsoft Azure"
    description="Analytique de stockage vous permet pour effectuer le suivi des données de métrique pour tous les services de stockage et pour collecter des journaux pour le stockage Blob, file d’attente et Table."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="storage-analytics"></a>Analytique de stockage

## <a name="overview"></a>Vue d’ensemble

Analytique de stockage Azure dans un journal et fournit des données de métrique pour un compte de stockage. Vous pouvez utiliser ces données pour suivre les demandes, analyser les tendances d’utilisation et diagnostiquer les problèmes avec votre compte de stockage.

Pour utiliser le stockage Analytique, vous devez l’activer individuellement pour chaque service que vous souhaitez surveiller. Vous pouvez l’activer à partir du [Portail Azure](https://portal.azure.com). Pour plus d’informations, voir [Moniteur d’un compte de stockage dans le portail Azure](storage-monitor-storage-account.md). Vous pouvez également activer Analytique de stockage par programme via l’API REST ou la bibliothèque cliente. Les opérations [d’Obtenir les propriétés de Service Blob](https://msdn.microsoft.com/library/hh452239.aspx), [Obtenir les propriétés de Service de file d’attente](https://msdn.microsoft.com/library/hh452243.aspx), [Obtenir les propriétés de Service Table](https://msdn.microsoft.com/library/hh452238.aspx)et [Obtenir les propriétés de fichier Service](https://msdn.microsoft.com/library/mt427369.aspx) permet d’activer le stockage Analytique pour chaque service.

Les données agrégées sont stockées dans un blob connu (pour la journalisation) et dans des tables bien connus (pour les métriques), qui est accessible à l’aide du service d’objet Blob et les API de service de Table.

Analytique de stockage a une limite de 20 To de la quantité de données stockées qui sont indépendantes de la limite totale de votre compte de stockage. Pour plus d’informations sur la facturation et la rétention des données, reportez-vous à la section [Analytique de stockage et de la facturation](https://msdn.microsoft.com/library/hh360997.aspx). Pour plus d’informations sur les limites de compte de stockage, reportez-vous à la section [des cibles de performances et de l’évolutivité du stockage Azure](storage-scalability-targets.md).

Pour obtenir un guide détaillé sur l’utilisation de stockage Analytique et autres outils pour identifier, de diagnostiquer et de résoudre les problèmes liés au stockage d’Azure, voir [moniteur, diagnostiquer et de résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="about-storage-analytics-logging"></a>À propos de l’enregistrement de stockage Analytique

Analytique de stockage enregistre des informations détaillées sur les réussites et les échecs des demandes à un service de stockage. Ces informations peuvent être utilisées pour surveiller les requêtes individuelles et pour diagnostiquer des problèmes avec un service de stockage. Les demandes sont enregistrés sur un meilleur effort.

Les entrées de journal sont créées uniquement si l’activité de service de stockage. Par exemple, si un compte de stockage a activité dans son service d’objet Blob, mais pas dans ses services de Table ou de la file d’attente, que les journaux relatifs au service d’objet Blob seront créés.

Enregistrement de stockage Analytique n’est pas disponible pour le Service de fichiers Azure.

### <a name="logging-authenticated-requests"></a>Demandes de connexion authentifiée

Les types de demandes authentifiées suivants sont enregistrés :

- Demandes réussies.

- Échec des demandes, y compris le délai, la limitation, réseau, autorisation et autres erreurs.

- Demandes à l’aide d’un partage accès Signature (SAS), y compris les demandes ayant échouées et réussies.

- Requêtes pour les données d’analytique.

Les demandes effectuées par Analytique de stockage lui-même, telles que la création d’un journal ou de la suppression, ne sont pas enregistrées. Une liste complète des données journalisées est décrite dans les rubriques [stockage Analytique connecté les opérations et les Messages d’état](https://msdn.microsoft.com/library/hh343260.aspx) et le [Format de stockage du journal Analytique](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="logging-anonymous-requests"></a>Enregistrement des demandes anonymes
Les types de demandes anonymes suivants sont enregistrés :

- Demandes réussies.

- Erreurs du serveur.

- Erreurs de délai d’attente pour le client et le serveur.

- Les requêtes GET a échoué avec l’erreur de code 304 (non modifié).

Toutes les autres demandes anonymes ayant échoué ne sont pas enregistrées. Une liste complète des données journalisées est décrite dans les rubriques [stockage Analytique connecté les opérations et les Messages d’état](https://msdn.microsoft.com/library/hh343260.aspx) et le [Format de stockage du journal Analytique](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="how-logs-are-stored"></a>Mode de stockage des journaux
Tous les journaux sont stockés dans le BLOB de bloc dans un conteneur nommé $logs, qui est automatiquement créé lorsque le stockage Analytique est activée pour un compte de stockage. Le conteneur $logs se trouve dans l’espace de noms du blob du compte de stockage, par exemple : `http://<accountname>.blob.core.windows.net/$logs`. Ce conteneur ne peut pas supprimé une fois le stockage Analytique a été activé, bien que son contenu peut être supprimé.

>[Azure.NOTE] Le conteneur de $logs n’est pas affiché lors de l’exécution d’un conteneur de liste d’opération, telles que la méthode [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . Vous devez y accéder directement. Par exemple, vous pouvez utiliser la méthode [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) pour accéder les objets BLOB dans la `$logs` conteneur.
Que les demandes sont consignés, stockage Analytique pour télécharger les résultats intermédiaires en tant que blocs. Périodiquement, stockage Analytique sera valider ces blocs et les rendre disponibles sous la forme d’un objet blob.

Des enregistrements en double existent pour les journaux créés à la même heure. Vous pouvez déterminer si un enregistrement est un doublon en vérifiant le numéro **RequestId** et **opération** .

### <a name="log-naming-conventions"></a>Conventions d’affectation de noms de journal
Chaque journal sera écrite dans le format suivant.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

Le tableau suivant décrit chaque attribut dans le nom du journal.

| Attribut         | Description                                                                                                                                                                                   |
|----------------   |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   |
| < nom du service >    | Le nom du service de stockage. Par exemple : blob, table ou file d’attente.                                                                                                                          |
| AAAA              | Année à quatre chiffres pour le journal. Par exemple : 2011.                                                                                                                                           |
| MM                | Mois à deux chiffres pour le journal. Par exemple : 07.                                                                                                                                             |
| DD                | Mois à deux chiffres pour le journal. Par exemple : 07.                                                                                                                                             |
| hh                | L’heure à deux chiffres qui indique l’heure de début pour les journaux, au format de 24 heures UTC. Par exemple : 18.                                                                                     |
| mm                | Le nombre à deux chiffres qui indique la minute de début pour les journaux. Cette valeur est pris en charge dans la version actuelle de stockage Analytique, et sa valeur sera toujours 00.     |
| <counter>         | Compteur de base zéro à six chiffres qui indique le nombre d’objets BLOB de journal générées pour le service de stockage dans une période de l’heure. Ce compteur commence à 000000. Par exemple : 000001.     |

Voici un exemple complet de nom de journal qui combine les exemples précédents.

    blob/2011/07/31/1800/000001.log

Voici un exemple d’URI qui peut être utilisé pour accéder au journal précédent.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Lorsqu’une demande de stockage est consignée, le nom du journal qui en résulte correspond à l’heure de fin de l’opération demandée. Par exemple, si une demande de GetBlob a été terminée à 6 h 30. 31/7/2011, le journal serait écrite avec le préfixe suivant :`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Métadonnées du journal
Tous les BLOB de journal sont enregistrés avec les métadonnées qui peuvent être utilisée pour identifier les données de journalisation que contient le blob. Le tableau suivant décrit chaque attribut de métadonnées.

| Attribut     | Description                                                                                                                                                                                                                                                   |
|------------   |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| LogType       | Décrit si le journal contient des informations relatives pour lire, écrire ou supprimer des opérations. Cette valeur peut inclure un type ou une combinaison des trois, séparés par des virgules. Exemple 1 : écrire ; Exemple 2 : lire, écrire ; Exemple 3 : lire, écrire, supprimer.    |
| Heure de début     | La première heure d’une entrée dans le journal, sous la forme aaaa-MM-JJThh. Par exemple : 2011-07-31T18:21:46Z.                                                                                                                                             |
| Heure de fin       | La dernière heure d’une entrée dans le journal, sous la forme aaaa-MM-JJThh. Par exemple : 2011-07-31T18:22:09Z.                                                                                                                                               |
| LogVersion    | La version du format de journal. Actuellement, la seule valeur prise en charge est 1.0.                                                                                                                                                                                     |

La liste suivante affiche les métadonnées d’exemple complet à l’aide des exemples précédents.

- LogType = écriture

- StartTime = 2011-07-31T18:21:46Z

- EndTime = 2011-07-31T18:22:09Z

- LogVersion = 1.0

### <a name="accessing-logging-data"></a>L’accès aux données de l’enregistrement

Toutes les données de la `$logs` conteneur est accessible à l’aide de l’API de service de Blob, y compris les API .NET fourni par l’Azure la bibliothèque managée. L’administrateur de compte de stockage peut lire et supprimer des journaux, mais Impossible de créer ou mettre à jour. Les métadonnées du journal et nom de journal peuvent servir lors de l’interrogation d’un journal. Il est possible pour les journaux d’une heure apparaissent de manière désordonnée, mais les métadonnées spécifie toujours le timespan d’entrées de journal dans un journal. Par conséquent, vous pouvez utiliser une combinaison de noms de journal et les métadonnées lors de la recherche d’un journal spécifique.

## <a name="about-storage-analytics-metrics"></a>À propos des mesures de stockage Analytique

Analytique de stockage peut stocker des métriques qui incluent des données statistiques et de la capacité de transaction globales sur les demandes à un service de stockage. Les transactions sont déclarées à la fois du niveau API opération ainsi qu’au niveau du service de stockage et capacité est déclarée au niveau du service de stockage. Les données de mesures peuvent être utilisées pour analyser l’utilisation du service stockage, diagnostiquer les problèmes avec les requêtes effectuées sur le service de stockage et d’améliorer les performances des applications qui utilisent un service.

Pour utiliser le stockage Analytique, vous devez l’activer individuellement pour chaque service que vous souhaitez surveiller. Vous pouvez l’activer à partir du [Portail Azure](https://portal.azure.com). Pour plus d’informations, voir [Moniteur d’un compte de stockage dans le portail Azure](storage-monitor-storage-account.md). Vous pouvez également activer Analytique de stockage par programme via l’API REST ou la bibliothèque cliente. Les opérations **d’Obtenir les propriétés de Service** permet d’activer le stockage Analytique pour chaque service.

### <a name="transaction-metrics"></a>Métriques de transactions

Un ensemble robuste de données est enregistré à intervalles horaires ou minutes pour chaque service de stockage et demandé l’opération sur l’API, y compris d’ENTREE/sortie, de disponibilité, d’erreurs et classé les pourcentages de la demande. Vous pouvez afficher la liste complète des détails de transaction dans la rubrique [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/hh343264.aspx) .

Données de transaction sont enregistrées à deux niveaux : le niveau de service et le niveau de fonctionnement de l’API. Au niveau du service, statistiques qui récapitule tous les demandé opérations API sont écrites dans une table d’entité toutes les heures même si aucune demande n’ont été effectuées sur le service. Au niveau des opérations API, les statistiques sont écrites uniquement à une entité si l’opération a été demandée à l’intérieur de cette plage.

Par exemple, si vous effectuez une opération **GetBlob** sur votre service d’objet Blob, des indicateurs de stockage Analytique enregistrer la requête et inclure dans les données agrégées pour le service d’objet Blob ainsi que l’opération **GetBlob** . Toutefois, si aucune opération **GetBlob** n’est demandée au cours de l’heure, une entité ne pas être écrite dans le `$MetricsTransactionsBlob` pour cette opération.

Métriques de transactions sont enregistrés pour les demandes de l’utilisateur et les demandes effectuées par Analytique de stockage lui-même. Par exemple, les demandes en stockage Analytique pour écrire les journaux et les entités de table sont enregistrés. Pour plus d’informations sur la façon dont ces demandes sont facturés, reportez-vous à la section [Analytique de stockage et de la facturation](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Mesures de capacité

>[AZURE.NOTE] Actuellement, les mesures de capacité ne sont disponibles que pour le service d’objet Blob. Les mesures de capacité pour le service de la Table et le service de file d’attente sera disponibles dans les futures versions de stockage Analytique.

Les données de capacité sont enregistrées tous les jours pour service d’objet Blob d’un compte de stockage et deux entités de table sont écrits. Une entité fournit des statistiques pour les données utilisateur, et l’autre fournit des statistiques sur les `$logs` conteneur blob utilisé par stockage Analytique. Le `$MetricsCapacityBlob` table inclut les statistiques suivantes :

- **Capacité**: l’espace de stockage utilisé par le service d’objet Blob du compte de stockage, en octets.

- **ContainerCount**: le nombre de conteneurs d’objet blob dans le service d’objet Blob du compte de stockage.

- **ObjectCount**: le nombre de BLOB validées et non validées de page ou de bloc dans le service d’objet Blob du compte de stockage.

Pour plus d’informations sur les mesures de capacité, consultez le [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Comment sont stockées les métriques

Toutes les données de métrique pour chacun des services de stockage sont stockées dans trois tables réservés pour ce service : une table pour les informations de transaction, une table pour les informations sur les transactions minute et une autre table pour les informations de capacité. Informations de transaction des transactions et à la minute est composée des données de demande et de réponse, et les informations de capacité se composent de stockage des données d’utilisation. Mesures de l’heure, minutes mesures et capacité de Blob service d’un compte stockage sont accessibles dans les tables qui sont nommés comme décrit dans le tableau suivant.

| Niveau de mesures                         | Noms de table                                                                                                                   | Versions prises en charge                                                                                                                        |
|------------------------------------   |-----------------------------------------------------------------------------------------------------------------------------  |---------------------------------------------------------------------------------------------------------------------------------------------- |
| Mesures de toutes les heures, emplacement principal      |  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                                  | Versions antérieures à 2013-08-15 uniquement. Ces noms sont toujours pris en charge, il est recommandé de basculer vers les tableaux ci-dessous.  |
| Mesures de toutes les heures, emplacement principal      | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue               | Toutes les versions, y compris 2013-08-15.                                                                                                               |
| Mesures de minute, l’emplacement principal      | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue         | Toutes les versions, y compris 2013-08-15.                                                                                                               |
| Mesures de toutes les heures, emplacement secondaire    | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue         | Toutes les versions, y compris 2013-08-15. La réplication redondante géo accès en lecture doit être activée.                                                    |
| Mesures de minute, emplacement secondaire    | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue   | Toutes les versions, y compris 2013-08-15. La réplication redondante géo accès en lecture doit être activée.                                                    |
| Capacité (service d’objet Blob uniquement)          | $MetricsCapacityBlob                                                                                                          | Toutes les versions, y compris 2013-08-15.                                                                                                               |


Ces tables sont créées automatiquement lorsque le stockage Analytique est activé pour un compte de stockage. Ils sont accessibles via l’espace de noms de compte de stockage, par exemple :`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>L’accès aux données de mesures

Toutes les données dans les tables de mesures sont accessibles à l’aide de l’API de service de Table, y compris les API .NET fourni par l’Azure la bibliothèque managée. L’administrateur de compte de stockage peut lire et supprimer des entités de la table, mais ne peut pas créer ou mettre à jour les.

## <a name="billing-for-storage-analytics"></a>Facturation de stockage Analytique

Analytique de stockage est activée par le propriétaire d’un compte de stockage ; Il n’est pas activé par défaut. Toutes les données de métrique sont écrites par les services d’un compte de stockage. Par conséquent, chaque opération d’écriture effectuée par stockage Analytique est facturable. En outre, l’espace de stockage utilisé par les données de la métrique est également facturable.

Les actions suivantes effectuées par stockage Analytique sont facturables :

- Demandes de création d’objets BLOB pour la journalisation. 

- Demandes de création d’entités de table pour les mesures.

Si vous avez configuré une stratégie de rétention de données, vous ne sont pas facturés pour supprimer des transactions de lorsque Analytique de stockage supprime les anciennes données de journalisation et de mesures. Toutefois, les transactions de suppression à partir d’un client sont facturables. Pour plus d’informations sur les stratégies de rétention, reportez-vous à la section [définition d’une stratégie de rétention de données Analytique stockage](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Présentation des demandes facturables

Toutes les requêtes adressées au service de stockage d’un compte est facturable ou non facturable. Analytique de stockage connecte à chaque demande individuelle apportée à un service, un message d’état qui indique la façon dont la demande a été gérée. De même, stockage Analytique stocke les mesures pour les opérations API de ce service, y compris les pourcentages et le nombre de certains messages d’état et d’un service. Ensemble, ces fonctionnalités peuvent vous aider à analyser vos demandes facturables, apporter des améliorations sur votre application et diagnostiquer les problèmes avec les demandes à vos services. Pour plus d’informations sur la facturation, consultez [Compréhension Azure stockage facturation - la bande passante, Transactions et capacité](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Lorsque vous examinez les données d’Analytique de stockage, vous pouvez utiliser les tables dans la rubrique [stockage Analytique connecté les opérations et les Messages d’état](https://msdn.microsoft.com/library/azure/hh343260.aspx) pour déterminer quelles demandes sont facturables. Vous pouvez ensuite comparer vos journaux et des données de mesures pour les messages d’état pour voir si vous ont été facturés pour une demande particulière. Vous pouvez également utiliser les tableaux dans la rubrique précédente pour étudier la disponibilité d’un service de stockage ou d’opération individuelle sur l’API.

## <a name="next-steps"></a>Étapes suivantes

### <a name="setting-up-storage-analytics"></a>Configuration Analytique de stockage
- [Surveiller un compte de stockage dans le portail Azure](storage-monitor-storage-account.md)
- [Activation et configuration du stockage Analytique](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Enregistrement d’Analytique de stockage  
- [À propos de l’enregistrement d’Analytique de stockage](https://msdn.microsoft.com/library/hh343262.aspx)
- [Format de stockage du journal Analytique](https://msdn.microsoft.com/library/hh343259.aspx)
- [Analytique de stockage connecté les opérations et les Messages d’état](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Indicateurs de stockage Analytique
- [À propos des indicateurs de stockage Analytique](https://msdn.microsoft.com/library/hh343258.aspx)
- [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/hh343264.aspx)
- [Analytique de stockage connecté les opérations et les Messages d’état](https://msdn.microsoft.com/library/hh343260.aspx)  
