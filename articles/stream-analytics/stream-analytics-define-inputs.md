<properties
    pageTitle="Connexion de données : flux de données entrées à partir d’un flux d’événements | Microsoft Azure"
    description="Obtenir des informations sur la configuration d’une connexion de données d’Analytique de flux de données appelées « entrées ». Les entrées incluent un flux de données à partir d’événements et aussi référencent des données."
    keywords="flux de données, connexion de données, flux d’événements"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Connexion de données : en savoir plus sur les données entrées flux Analytique du flux d’événements

La connexion de données d’Analytique de flux est un flux de données d’événements à partir d’une source de données. Il s’agit d’une « entrée ». Intégration en première classe avec Azure flux sources concentrateur d’événements IoT Hub et Blob stockage de données qui peut être de l’abonnement Azure identiques ou différent, comme votre travail analytique a été Analytique de flux.

## <a name="data-input-types-data-stream-and-reference-data"></a>Types d’entrées de données : les données de référence et des flux de données
Comme les données sont insérées dans une source de données, il est utilisé par la tâche de flux de données Analytique et traité en temps réel. Les entrées sont divisées en deux types : les entrées de flux de données et des entrées de données de référence.

### <a name="data-stream-inputs"></a>Entrées de flux de données
Un flux de données est la séquence illimitée d’événements arrivant dans le temps. Tâches de flux de données Analytique doivent inclure l’entrée de flux de données au moins une pour être consommé et transformé par le travail. Stockage des objets BLOB, événement concentrateurs et IoT concentrateurs sont pris en charge comme sources d’entrée de flux de données. Concentrateurs d’événement sont utilisés pour collecter les flux d’événements à partir de plusieurs périphériques et les services, tels que les activités de support de réseaux sociaux, des informations boursière ou des données provenant des capteurs. Les concentrateurs IoT sont optimisés pour collecter des données à partir de périphériques connectés dans les scénarios Internet des objets (IoT).  Stockage des objets BLOB peut servir comme une source d’entrée pour l’ingestion des données en bloc sous forme de flux.  

### <a name="reference-data"></a>Données de référence
Analytique de flux prend en charge un deuxième type d’entrée connu en tant que données de référence. Il s’agit de données auxiliaires qui est soit statique, soit variation lente au fil du temps et sont généralement utilisées pour effectuer la corrélation et les recherches. Stockage de Blob Azure est actuellement la seule source d’entrée pris en charge pour les données de référence. BLOB de source de données de référence taille sont limitées à 100 Mo.
Pour savoir comment créer des entrées de données de référence, reportez-vous à la section [Utiliser les données de référence](stream-analytics-use-reference-data.md)  

## <a name="create-a-data-stream-input-with-an-event-hub"></a>Créer une entrée de flux de données avec un concentrateur d’événements

[Concentrateurs d’événement Azure](https://azure.microsoft.com/services/event-hubs/) sont très évolutifs ingestor d’événement de publication-abonnement. Il peut collecter des millions d’événements par seconde, afin que vous puissiez traiter et analyser les quantités énormes de données produites par vos applications et de périphériques connectés. Il s’agit d’une des deux entrées plus couramment utilisées pour les flux de données Analytique. Événement concentrateurs et Analytique de flux de données ensemble prévoient clients une solution de bout en bout en temps réel analytique. Concentrateurs d’événements clients introduire des événements dans Azure en temps réel et de tâches de flux de données Analytique de les traiter en temps réel. Par exemple, clients peuvent envoyer des clics du web, les lectures des capteurs, les événements du journal en ligne à des concentrateurs d’événements et créer des travaux de flux Analytique pour utiliser des concentrateurs d’événements comme les flux de données d’entrée pour le filtrage en temps réel, le groupement et la corrélation.

Il est important de noter que l’estampille par défaut des événements en provenance de concentrateurs d’événement dans le flux de données Analytique est l’horodatage de l’événement est arrivé dans le concentrateur d’événements qui est EventEnqueuedUtcTime. Pour traiter les données sous forme de flux à l’aide d’une estampille dans la charge utile d’événement, le mot clé [Par estampille](https://msdn.microsoft.com/library/azure/dn834998.aspx) doit être utilisé.

### <a name="consumer-groups"></a>Groupes de consommateurs

Chaque entrée de flux Analytique événement concentrateur doit être configurée pour avoir son propre groupe de consommateurs. Lorsqu’un travail contient une jointure réflexive ou plusieurs entrées, une entrée peut être lue par plus d’un lecteur en aval, qui a une incidence sur le nombre de lecteurs dans un groupe unique de consommateur. Pour éviter de dépasser la limite de concentrateur d’événements de 5 lecteurs par groupe de consommateur par partition, il est recommandé de désigner un groupe de consommateurs pour chaque tâche de flux de données Analytique. Notez qu’il existe également une limite de 20 groupes de consommateurs par le concentrateur d’événements. Pour plus d’informations, consultez le [Guide de programmation des concentrateurs d’événement](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-event-hub-as-an-input-data-stream"></a>Configurer le concentrateur d’événements sous la forme d’un flux de données d’entrée

Le tableau ci-dessous décrit chaque propriété dans le concentrateur d’entrée onglet avec sa description des événements :

| NOM DE LA PROPRIÉTÉ | DESCRIPTION |
|------|------|
| Alias d’entrée | Un nom convivial qui sera utilisé dans la requête de travail pour faire référence à cette entrée |
| Namespace de Bus de service | Bus de Service d’un espace de noms est un conteneur pour un ensemble d’entités de messagerie. Lorsque vous avez créé un nouveau concentrateur d’événements, vous avez créé également un espace de noms du Bus de Service. |
| Concentrateur d’événements | Le nom de votre événement de supervision d’entrée. |
| Nom de concentrateur des événements à l’aide de la stratégie | La stratégie d’accès partagé, qui peut être créée sous l’onglet configurer de concentrateur d’événement. Chaque stratégie d’accès partagé va avoir un nom, les autorisations que vous définissez, et accéder aux clés. |
| Clé de concentrateur des événements à l’aide de la stratégie | La clé de l’accès partagé utilisée pour authentifier l’accès à l’espace de noms du Bus de Service. |
| Groupe de consommateurs événement Hub (facultatif) | Le groupe de consommateurs pour ajouter des données à partir du concentrateur de l’événement. Si non spécifié, les tâches de flux de données Analytique utilise le groupe de consommateurs de par défaut pour ajouter des données à partir du concentrateur d’événements. Il est recommandé d’utiliser un groupe de consommateurs distinct pour chaque tâche de flux de données Analytique. |
| Format de sérialisation d’événement | Pour vous assurer que vos requêtes fonctionnent comme prévu, Analytique de flux de données a besoin de connaître le format de sérialisation (JSON, CSV ou Avro), vous utilisez des flux de données entrantes. |
| Codage | UTF-8 est le seul format de codage pris en charge pour l’instant. |

Lorsque vos données proviennent d’une source d’événement concentrateur, vous pouvez accéder à certains champs de métadonnées dans votre requête Analytique de flux de données. Le tableau ci-dessous répertorie les champs et leur description.

| PROPRIÉTÉ | DESCRIPTION |
|------|------|
| EventProcessedUtcTime | La date et l’heure à laquelle l’événement a été traité par flux Analytique. |
| EventEnqueuedUtcTime | La date et l’heure à laquelle l’événement a été reçu par les concentrateurs d’événement. |
| IDPartition | L’ID de partition de base zéro de la carte d’entrée. |

Par exemple, vous pouvez écrire une requête comme suit :

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-an-iot-hub-data-stream-input"></a>Créer une entrée de flux de données IoT concentrateur

Azure Iot concentrateur est hautement évolutive de publication-abonnement ingestor événement optimisé pour les scénarios de IoT.
Il est important de noter que l’estampille par défaut des événements en provenance de concentrateurs IoT dans le flux de données Analytique est l’horodatage de l’événement est arrivé dans le concentrateur IoT qui est EventEnqueuedUtcTime. Pour traiter les données sous forme de flux à l’aide d’une estampille dans la charge utile d’événement, le mot clé [Par estampille](https://msdn.microsoft.com/library/azure/dn834998.aspx) doit être utilisé.

> [AZURE.NOTE] Seuls les messages envoyés avec une propriété DeviceClient peuvent être traités.

### <a name="consumer-groups"></a>Groupes de consommateurs

Chaque entrée de flux de données Analytique IoT concentrateur doit être configurée pour avoir son propre groupe de consommateurs. Lorsqu’un travail contient une jointure réflexive ou plusieurs entrées, une entrée peut être lue par plus d’un lecteur en aval, qui a une incidence sur le nombre de lecteurs dans un groupe unique de consommateur. Pour éviter de dépasser la limite de IoT concentrateur de 5 lecteurs par groupe de consommateur par partition, il est recommandé de désigner un groupe de consommateurs pour chaque tâche de flux de données Analytique.

### <a name="configure-iot-hub-as-an-data-stream-input"></a>Configurer IoT concentrateur sous la forme d’un flux de données d’entrée

Le tableau ci-dessous décrit chaque propriété de l’onglet entrée de IoT Hub avec sa description :

| NOM DE LA PROPRIÉTÉ | DESCRIPTION |
|------|------|
| Alias d’entrée | Un nom convivial qui sera utilisé dans la requête de travail pour faire référence à cette entrée. |
| Concentrateur de IoT | Un concentrateur IoT est un conteneur pour un ensemble d’entités de messagerie. |
| Point de terminaison | Le nom de votre point de terminaison IoT concentrateur. |
| Nom de la stratégie accès partagé | La stratégie d’accès partagé pour donner accès au concentrateur IoT. Chaque stratégie d’accès partagé va avoir un nom, les autorisations que vous définissez, et accéder aux clés. |
| Clé de stratégie d’accès partagé | La clé de l’accès partagé utilisée pour authentifier l’accès au concentrateur IoT. |
| Groupe de consommateurs (facultatif) | Le groupe de consommateurs pour ajouter des données à partir du concentrateur IoT. Si non spécifié, les tâches de flux de données Analytique utilise le groupe de consommateurs de par défaut pour ajouter des données à partir du concentrateur IoT. Il est recommandé d’utiliser un groupe de consommateurs distinct pour chaque tâche de flux de données Analytique. |
| Format de sérialisation d’événement | Pour vous assurer que vos requêtes fonctionnent comme prévu, Analytique de flux de données a besoin de connaître le format de sérialisation (JSON, CSV ou Avro), vous utilisez des flux de données entrantes. |
| Codage | UTF-8 est le seul format de codage pris en charge pour l’instant. |

Lorsque vos données proviennent d’une source IoT concentrateur, vous pouvez accéder à certains champs de métadonnées dans votre requête Analytique de flux de données. Le tableau ci-dessous répertorie les champs et leur description.

| PROPRIÉTÉ | DESCRIPTION |
|------|------|
| EventProcessedUtcTime | La date et l’heure à laquelle l’événement a été traité. |
| EventEnqueuedUtcTime | La date et l’heure à laquelle l’événement a été reçu par le concentrateur IoT. |
| IDPartition | L’ID de partition de base zéro de la carte d’entrée. |
| IoTHub.MessageId | Utilisée pour corréler une communication bidirectionnelle dans IoT concentrateur. |
| IoTHub.CorrelationId | Utilisé dans les réponses de message et les commentaires dans IoT concentrateur. |
| IoTHub.ConnectionDeviceId | Id authentifié utilisé pour envoyer ce message, apposé sur les messages de servicebound par IoT concentrateur. |
| IoTHub.ConnectionDeviceGenerationId | La generationId du périphérique utilisé pour envoyer ce message, authentifié estampillée sur les messages de servicebound par IoT concentrateur. |
| IoTHub.EnqueuedTime | Lorsque le message a été reçu par IoT concentrateur de temps. |
| IoTHub.StreamId | Propriété de type événement personnalisé ajoutée par le périphérique de l’expéditeur. |

## <a name="create-a-blob-storage-data-stream-input"></a>Créer une entrée de flux de données de stockage Blob

Pour les scénarios avec de grandes quantités de données non structurées à stocker dans le nuage, stockage Blob offre une solution économique et évolutive. Données dans le [stockage Blob](https://azure.microsoft.com/services/storage/blobs/) sont considérées comme données « au repos », mais elle peut être traitée sous la forme d’un flux de données par flux Analytique. Un scénario courant pour les entrées de stockage Blob avec flux Analytique est le traitement du journal, où la télémétrie est capturé à partir d’un système et doit être analysée et traités pour extraire des données significatives.

Il est important de noter que l’estampille par défaut des événements de stockage Blob dans le flux de données Analytique est l’estampille que le blob de la dernière modification de *isBlobLastModifiedUtcTime*. Pour traiter les données sous forme de flux à l’aide d’une estampille dans la charge utile d’événement, le mot clé [Par estampille](https://msdn.microsoft.com/library/azure/dn834998.aspx) doit être utilisé.

Notez également que CSV au format entrées **nécessitent** une ligne d’en-tête pour définir des champs pour le jeu de données. Autres champs de ligne d’en-tête doivent être **unique**.

> [AZURE.NOTE] Analytique de flux de données ne supporte pas l’ajout de contenu à un blob existant. Analytique de flux de données est uniquement afficher un blob qu’une seule fois et toutes les modifications réalisées après que cette lecture ne sera pas traité. La meilleure solution consiste à télécharger toutes les données qu’une seule fois et pas d’ajouter des événements supplémentaires au magasin de blob.

Le tableau ci-dessous décrit chaque propriété de l’onglet entrée de stockage Blob avec sa description :

<table>
<tbody>
<tr>
<td>NOM DE LA PROPRIÉTÉ</td>
<td>DESCRIPTION</td>
</tr>
<tr>
<td>Alias d’entrée</td>
<td>Un nom convivial qui sera utilisé dans la requête de travail pour faire référence à cette entrée.</td>
</tr>
<tr>
<td>Compte de stockage</td>
<td>Le nom du compte de stockage où se trouvent vos fichiers blob.</td>
</tr>
<tr>
<td>Clé de compte de stockage</td>
<td>La clé secrète avec le compte de stockage.</td>
</tr>
<tr>
<td>Conteneur de stockage
</td>
<td>Les conteneurs fournissent un regroupement logique des objets BLOB stockées dans le service Microsoft Azure Blob. Lorsque vous téléchargez un objet blob dans le service d’objet Blob, vous devez spécifier un conteneur pour ce blob.</td>
</tr>
<tr>
<td>Chemin d’accès du préfixe modèle [facultatif]</td>
<td>Le chemin d’accès du fichier utilisé pour localiser vos objets BLOB dans le conteneur spécifié.
Dans le chemin d’accès, vous pouvez choisir de spécifier une ou plusieurs instances des 3 variables suivantes :<BR>{date}, {heure},<BR>partition de {}<BR>Exemple 1 : cluster1/logs / {date} / {time} / {partitionner}<BR>Exemple 2 : cluster1/logs / {date}<P>Notez que « * » n’est pas une valeur autorisée pour pathprefix. Uniquement valide de <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caractères de l’objet blob Azure</a> sont autorisés.</td>
</tr>
<tr>
<td>Format de date [facultatif]</td>
<td>Si le jeton de date est utilisé dans le chemin d’accès de préfixe, vous pouvez sélectionner le format de date dans laquelle les fichiers sont organisés. Exemple : AAAA/MM/JJ</td>
</tr>
<tr>
<td>Format d’heure [facultatif]</td>
<td>Si le jeton de l’heure est utilisé dans le chemin d’accès de préfixe, spécifiez le format dans lequel les fichiers sont organisés. Actuellement, la seule valeur prise en charge est HH.</td>
</tr>
<tr>
<td>Format de sérialisation d’événement</td>
<td>Pour vous assurer que vos requêtes fonctionnent comme prévu, Analytique de flux de données a besoin de connaître le format de sérialisation (JSON, CSV ou Avro), vous utilisez des flux de données entrantes.</td>
</tr>
<tr>
<td>Codage</td>
<td>CSV et JSON, UTF-8 est le seul format de codage pris en charge pour l’instant.</td>
</tr>
<tr>
<td>Séparateur</td>
<td>Analytique de flux prend en charge un certain nombre de délimiteurs communs pour la sérialisation des données au format CSV. Les valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale.</td>
</tr>
</tbody>
</table>

Lorsque vos données proviennent d’une source de stockage Blob, vous pouvez accéder à certains champs de métadonnées dans votre requête Analytique de flux de données. Le tableau ci-dessous répertorie les champs et leur description.

| PROPRIÉTÉ | DESCRIPTION |
|------|------|
| BlobName | Le nom de l’objet blob d’entrée provenant de l’événement. |
| EventProcessedUtcTime | La date et l’heure à laquelle l’événement a été traité par flux Analytique. |
| BlobLastModifiedUtcTime | La date et l’heure de la dernière modification de l’objet blob. |
| IDPartition | L’ID de partition de base zéro de la carte d’entrée. |

Par exemple, vous pouvez écrire une requête comme suit :

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
Vous avez appris sur les options de connexion de données dans Azure pour vos travaux d’Analytique de flux de données. Pour en savoir plus sur les flux de données Analytique, voir :

- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
