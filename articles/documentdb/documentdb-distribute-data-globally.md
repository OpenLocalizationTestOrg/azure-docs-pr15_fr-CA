<properties
   pageTitle="Distribuer des données globalement avec DocumentDB | Microsoft Azure"
   description="Obtenir des informations sur l’échelle de la planète géo-réplication, basculement et récupération de données à l’aide de DocumentDB d’Azure, un service de base de données NoSQL entièrement géré de bases de données globales."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="kipandya"/>
   
   
# <a name="distribute-data-globally-with-documentdb"></a>Distribuer des données globalement avec DocumentDB

> [AZURE.NOTE] La distribution mondiale des bases de données DocumentDB est disponible et activée automatiquement pour tous les comptes DocumentDB nouvellement créés. Nous nous efforçons d’activer la distribution globale sur tous les comptes existants, mais en attendant, si vous souhaitez que la distribution globale activée sur votre compte, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et nous vous activer pour vous maintenant.

DocumentDB Azure est conçu pour répondre aux besoins des applications IoT consistant en des millions de périphériques distribués globalement et applications internet échelle expériences hautement sensible aux utilisateurs dans le monde entier. Ces systèmes de base de données sont confrontés au défi de réaliser des accès de faible latence aux données d’application à partir de plusieurs régions géographiques avec des garanties de la cohérence et la disponibilité des données bien définie. Comme un système de base de données distribuée globalement, DocumentDB simplifie la distribution globale des données en proposant des comptes entièrement géré, plusieurs régions de la base de données qui fournissent des compromis clair entre la cohérence, de disponibilité et de performances, toutes les garanties correspondantes. DocumentDB base de données comptes sont offerts avec une disponibilité élevée, les latences ms à un seul chiffre, plusieurs [niveaux de cohérence bien définies] [consistency], un basculement transparent régional avec les API d’hébergement multiple et la possibilité de mise à l’échelle ELASTIQUEMENT du stockage et débit dans le monde entier. 

  
## <a name="configuring-multi-region-accounts"></a>Configuration des comptes de plusieurs régions

Configuration de votre compte de DocumentDB à l’échelle du monde peut être effectuée en moins d’une minute via le [portail Azure](documentdb-portal-global-replication.md). Tout ce que vous devez faire est sélectionner le niveau de droit de cohérence entre les différents niveaux de prise en charge de la cohérence bien définies et associer n’importe quel nombre de régions Azure avec votre compte de base de données. Niveaux de cohérence DocumentDB fournissent clair de compromis entre les performances et la garantie de cohérence spécifique. 

![DocumentDB propose plusieurs bien définie de modèles à partir de cohérence (souple)][1]

DocumentDB propose plusieurs, bien définies (souple) la cohérence des modèles.

Sélection du niveau de cohérence droite dépend des données cohérence garantit des besoins de votre application. DocumentDB réplique de vos données dans toutes les régions déterminées automatiquement et garantit la cohérence que vous avez sélectionné pour votre compte de base de données. 


## <a name="using-multi-region-failover"></a>À l’aide de basculement de plusieurs région 

DocumentDB Azure est en mesure de façon transparente des comptes de base de données de basculement dans plusieurs régions Azure – les nouvelles [API d’hébergement multiple] [ developingwithmultipleregions] garantie que votre application peut continuer à utiliser un point de terminaison logique et est ininterrompue par le basculement. Basculement sur incident est contrôlé par vous, offrant la possibilité de récupérer votre base de données de compte dans l’événement de la plage de conditions d’erreur possibles se produisent, y compris les défaillances des applications, infrastructure, service ou régionaux (réels ou simulés). En cas de panne régionale de DocumentDB, le service échoue en toute transparence sur votre compte de base de données et votre application continue à accéder aux données sans perdre de disponibilité. Tandis que le DocumentDB offre la [disponibilité de 99,99 % SLA][sla], vous pouvez tester la fin de votre application à des propriétés de disponibilité fin en simulant une panne régionale à la fois, [par programme] [ arm] , ainsi que via le portail d’Azure.


## <a name="scaling-across-the-planet"></a>Mise à l’échelle sur la planète.
DocumentDB vous permet d’indépendamment provisionner le débit et l’utilisation de stockage pour chaque collection de DocumentDB à n’importe quelle échelle, globalement sur toutes les zones associées à votre compte de base de données. Une collection de DocumentDB est automatiquement distribuée globalement et gérée sur l’ensemble des régions associées à votre compte de base de données. Collections dans votre compte de base de données peuvent être distribuées sur une des régions Azure dans lequel [service de DocumentDB est disponible][serviceregions]. 

Le débit achetés et consommés pour chaque collection de DocumentDB de stockage est configuré automatiquement dans toutes les régions également. Cela permet à votre application à l’échelle en toute transparence sur le globe [payez uniquement pour le débit et le stockage au sein de chaque heure][pricing]. Par exemple, si vous avez déployé le RUs 2 millions pour une collection de DocumentDB, puis les zones associées à votre compte de base de données obtient RUs de 2 millions de cette collection. Cela est illustré ci-dessous.

![Mise à l’échelle de débit pour une collection de DocumentDB entre les quatre régions][2]

DocumentDB garantit < 10 ms Lecture et < 15 ms écriture latence à P99. Les demandes de lecture span jamais limite de centre de données afin de garantir [que vous avez sélectionné des exigences de cohérence][consistency]. Les écritures sont toujours quorum validée localement avant qu’ils sont reconnus aux clients. Chaque compte de base de données est configuré avec la priorité de la région d’écriture. La zone désignée avec la priorité la plus élevée agira en tant que la zone d’écriture en cours pour le compte. Tous les kits de développement logiciel achemine en toute transparence les écritures de compte de base de données de la zone d’écriture en cours. 

Enfin, étant donné que DocumentDB est entièrement [indépendant du schéma] [ vldb] -vous avez jamais à vous soucier de la gestion/mise à jour des schémas ou des index secondaires sur plusieurs centres de données. Vos [requêtes SQL] [ sqlqueries] continuer à travailler pendant vos modèles d’application et les données ne cessent d’évoluer. 


## <a name="enabling-global-distribution"></a>Activer la distribution globale 

Vous pouvez décider de rendre vos données localement ou globalement distribuées par l’association d’un ou de plusieurs régions Azure avec un DocumentDB de base de données de compte. Vous pouvez ajouter ou supprimer des régions à votre compte de base de données à tout moment. Pour activer la distribution globale via le portail, voir [comment effectuer la réplication de base de données global DocumentDB via le portail Azure](documentdb-portal-global-replication.md). Pour activer la distribution globale par programmation, consultez [développement avec comptes de DocumentDB de plusieurs régions](documentdb-developing-with-multiple-regions.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les données de distribution global avec DocumentDB dans les articles suivants :

* [Mise en service du débit et du stockage pour une collection] [throughputandstorage]
* [Hébergement multiple d’API via le reste. .NET, Java, Python et kits de développement de nœud] [developingwithmultipleregions]
* [La cohérence des niveaux de DocumentDB] [consistency]
* [Disponibilité des SLA] [sla]
* [Gestion de compte de base de données] [manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md

