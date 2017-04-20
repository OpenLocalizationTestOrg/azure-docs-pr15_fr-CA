<properties
    pageTitle="Archive de concentrateurs d’événement Azure | Microsoft Azure"
    description="Vue d’ensemble de la fonction d’archivage de concentrateurs d’événement Azure."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="azure-event-hubs-archive"></a>Archive de concentrateurs d’événement Azure

Archive de concentrateurs événement Azure vous permet fournir automatiquement les données de transmission en continu dans vos concentrateurs d’événement à un compte de stockage Blob de votre choix, avec une flexibilité supplémentaire pour indiquer une heure, ou la taille de l’intervalle de votre choix. Configuration d’archivage est rapide, il n’y a aucun frais administratif pour l’exécuter et il met à l’échelle automatiquement avec votre événement concentrateurs [unités de débit](event-hubs-overview.md#capacity-and-security). Archive de concentrateurs d’événement est le moyen le plus simple pour charger des données de transmission en continu dans Azure et vous permet de vous concentrer sur le traitement des données au lieu de capture de données.

Azure Archive de concentrateurs d’événements vous permet de traiter des pipelines de traitement par lots et en temps réel sur le même flux. Cela vous permet de créer des solutions qui peuvent évoluer avec vos besoins dans le temps. Si vous créez des systèmes lot aujourd'hui avec un œil aux futur traitement en temps réel, ou que vous souhaitez ajouter un chemin à froid efficace d’une solution en temps réel, Archive de concentrateurs d’événement permet de travailler avec la diffusion en continu de données plus facilement.

## <a name="how-event-hubs-archive-works"></a>Fonctionnement de l’Archive de concentrateurs d’événement

Concentrateurs d’événement est une mémoire tampon de temps conservation durable pour pénétration de télémétrie, similaire à un journal distribué. La clé de mise à l’échelle dans les concentrateurs d’événement est le [modèle de consommateur partitionnées](event-hubs-overview.md#partition-key). Chaque partition est un segment indépendant des données et est utilisée de façon indépendante. Au fil du temps ces données vieillissent, en fonction de la période de conservation configurables. Par conséquent, un concentrateur d’événement donné ne reçoivent jamais « trop plein ».

Archive de concentrateurs d’événements vous permet de spécifier votre propre compte de stockage des objets Blob Azure et d’un conteneur qui sera utilisé pour stocker les données archivées. Ce compte peut être dans la même région que votre concentrateur d’événements ou dans une autre région, ajout à la flexibilité de la fonction d’archivage de concentrateurs d’événement.

Les données archivées sont écrites au format de [Apache Avro][] ; un format compact, rapide et binaire qui fournit des structures de données complexes avec un schéma inline. Ce format est largement utilisé dans l’écosystème Hadoop, ainsi que par les flux Analytique et Azure Data Factory. Vous trouverez plus d’informations sur l’utilisation de Avro plus loin dans cet article.

### <a name="archive-windowing"></a>Archive Windowing

Archive de concentrateurs d’événements vous permet de vous permet de définir une fenêtre de contrôle d’archivage. Cette fenêtre est une taille minimale et la configuration de l’heure avec une « premier wins stratégie », c'est-à-dire que le premier déclencheur a rencontré provoquera une opération d’archivage. Si vous avez une fenêtre d’archive à quinze-minute/100 Mo et envoyez 1 Mo/s, la fenêtre de taille se déclenche avant que la fenêtre de temps. Chaque partition indépendamment des archives et écrit un blob de bloc terminé au moment de l’archivage, nommé pour la durée lorsque l’intervalle d’archivage a été rencontré. La convention d’affectation de noms est la suivante :

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### <a name="scaling-to-throughput-units"></a>Mise à l’échelle d’unités de débit

Le trafic d’événements concentrateurs est contrôlé par [des unités de débit](event-hubs-overview.md#capacity-and-security). Une unité de débit unique permet de 1 MB/s ou 1000 événements de pénétration et deux fois cette quantité de sortie par seconde. Concentrateurs d’événement standard peut être configurés avec les unités de débit de 1 à 20 et plus peuvent être acheté via une augmentation de quota [prise en charge de la demande][]. L’utilisation au-delà des unités de débit acheté est limitée. Événement concentrateurs Archive copie les données directement à partir de la mémoire interne de l’événement concentrateurs, sans passer par les quotas de sortie unité de débit et de l’enregistrement de votre sortie pour les autres lecteurs de traitement comme flux Analytique ou étincelle.

Une fois configuré, concentrateurs n’Archive d’événements s’exécute automatiquement dès que vous envoyez votre premier événement. Il continue à s’exécuter en permanence. Pour faciliter votre traitement en aval pour savoir que le processus fonctionne, concentrateurs d’événement écrit les fichiers vides lorsqu’il n’y a pas de données. Cela fournit une cadence prévisible et le marqueur qui peut alimenter vos processeurs de traitement par lots.

## <a name="setting-up-event-hubs-archive"></a>Configuration des concentrateurs n’Archive d’événements

Archive de concentrateurs d’événements peut être configuré au moment de la création de concentrateur d’événements via le portail du ou Azure le Gestionnaire de ressources. Vous activez simplement Archive en cliquant **sur** le bouton. Vous configurez un compte de stockage et d’un conteneur en cliquant sur la section du **conteneur** de la lame. Archive de concentrateurs d’événement utilisant l’authentification service-service avec le stockage, il est inutile spécifier une chaîne de connexion de stockage. Le sélecteur de ressource sélectionne l’URI de la ressource de votre compte de stockage automatiquement. Si vous utilisez le Gestionnaire de ressources Azure, vous devez fournir explicitement cet URI sous forme de chaîne.

La fenêtre de temps par défaut est de cinq minutes. La valeur minimale est 1, le maximum 15. La **taille** de fenêtre a une plage de 10 à 500 Mo.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Ajout d’archivage à un concentrateur d’événements existante

Archives peuvent être configurés sur les Hubs d’événement existants qui se trouvent dans un espace de noms de concentrateurs de l’événement. La fonctionnalité n’est pas disponible sur les espaces de noms type mixte ou de messagerie plus anciens. Pour activer l’archivage sur un concentrateur d’événements existant, ou pour modifier vos paramètres d’archivage, cliquez sur l’espace de noms pour charger la lame **Essentials** , puis cliquez sur le concentrateur de l’événement pour lequel vous souhaitez activer ou modifier le paramètre d’archivage. Enfin, cliquez sur dans la section **Propriétés** de la lame ouvrir comme illustré dans la figure suivante.

![][2]

Vous pouvez également configurer l’Archive de concentrateurs d’événement via des modèles Azure le Gestionnaire de ressources. Pour plus d’informations, consultez [cet article](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Exploration de l’archive et l’utilisation de Avro

Une fois configuré, Archive de concentrateurs d’événement crée des fichiers dans le compte de stockage Azure et le conteneur fourni sur la fenêtre de temps configurée. Vous pouvez afficher ces fichiers dans n’importe quel outil comme [Explorateur de stockage Azure][]. Vous pouvez télécharger les fichiers localement à les utiliser.

Les fichiers générés par l’événement concentrateurs Archive ont le schéma Avro suivant :

![][3]

À l’aide des [Outils de Avro][] fichier jar à partir d’Apache constitue un moyen facile pour Explorer les fichiers de Avro. Après avoir téléchargé ce fichier jar, vous pouvez voir le schéma d’un fichier spécifique de Avro en exécutant la commande suivante :

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

Cette commande renvoie

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Vous pouvez également utiliser les outils de Avro pour convertir le fichier au format JSON et effectuer d’autres opérations de traitement.

Pour effectuer un traitement plus avancé, téléchargez et installez des Avro pour votre choix de la plate-forme. Au moment de la rédaction de ce document, il existe des implémentations pour C, C++, C\#, NodeJS, Java, Perl, PHP, Python et Ruby.

Apache Avro a des repères de mise en route terminées pour [Java][] et les [Python][]. Vous pouvez également lire l’article [Mise en route avec Archive de concentrateurs d’événement](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>L’événement concentrateurs Archive est facturé

Archive de concentrateurs d’événement est mesurée de la même façon aux unités de débit, sous la forme d’un tarif horaire. La charge est directement proportionnelle au nombre d’unités de débit achetés pour l’espace de noms. Comme unités de débit augmentent et diminuent, événement concentrateurs Archive augmente et diminue pour fournir des performances correspondantes. Les compteurs se produisent en tandem. Les frais pour l’archivage de concentrateurs d’événement sont de 0,10 $ par heure et par unité de débit, offerte avec une remise de 50 % au cours de la période d’aperçu.

Archive de concentrateurs d’événement est véritablement la façon la plus simple d’obtenir des données dans Azure. À l’aide du lac de données Azure, Azure Data Factory et HDInsight d’Azure, vous pouvez effectuer autres analytique de votre choix et de traitement par lots à l’aide d’outils familiers et des plates-formes à n’importe quelle échelle dont vous avez besoin.

## <a name="next-steps"></a>Étapes suivantes

Vous en apprendrez davantage sur événement concentrateurs en consultant les liens suivants :

- Un [exemple d’application qui utilise des concentrateurs d’événement][]complète.
- L’exemple de [mise à l’échelle traitement avec les concentrateurs d’événement de l’événement][] .
- [Vue d’ensemble des concentrateurs événements][]

[Apache Avro]: http://avro.apache.org/
[demande de support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Explorateur de stockage Azure]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Outils d’Avro]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Vue d’ensemble des concentrateurs événements]: event-hubs-overview.md
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec les concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3