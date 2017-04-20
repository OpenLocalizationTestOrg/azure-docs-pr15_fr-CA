<properties
    pageTitle="Modélisation de l’architecture multilocataire dans Azure recherche | Microsoft Azure | Service de recherche de nuage hébergé"
    description="Obtenir des informations sur les modèles de design communs pour les applications SaaS partagées lors de l’utilisation de recherche d’Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/26/2016"
    ms.author="ashmaka"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Concevoir des modèles pour les applications SaaS mutualisées et recherche d’Azure

Une application partagée est celui qui fournit les mêmes services et fonctionnalités à n’importe quel nombre de clients qui ne peuvent pas voir ou partager les données de n’importe quel autre client. Ce document traite des stratégies d’isolation de clients pour des applications mutualisées créées avec recherche d’Azure.

## <a name="azure-search-concepts"></a>Concepts de recherche Azure
Sous la forme d’une solution de recherche-as-a-service, recherche d’Azure permet aux développeurs d’ajouter des expériences riches à des applications sans gestion d’infrastructure ou de devenir un expert dans la recherche. Données sont téléchargées vers le service et ensuite stockées dans le nuage. À l’aide de requêtes simples à l’API de recherche d’Azure, les données peuvent ensuite être modifiées et recherchées. Vous trouverez une vue d’ensemble du service de [cet article](http://aka.ms/whatisazsearch). Avant d’examiner les modèles de conception, il est important de comprendre certains concepts de recherche de Azure.

### <a name="search-services-indexes-fields-and-documents"></a>Services de recherche, les index, les champs et les documents
Lorsque vous utilisez recherche d’Azure, un s’abonne à un _service de recherche_. Comme les données sont téléchargées à la recherche d’Azure, il est stocké dans un _index_ , au sein du service de recherche. Il peut être un nombre d’index au sein d’un seul service. Pour utiliser les concepts familiers de bases de données, le service de recherche peut être comparé à une base de données, tandis que les index au sein d’un service peuvent être comparées à des tables dans une base de données.

Chaque index dans un service de recherche possède son propre schéma, ce qui est défini par un certain nombre de _champs_de personnalisables. Données sont ajoutées à un index de recherche d’Azure sous la forme de _documents_. Chaque document doit être téléchargé à un index particulier et doit tenir de schéma de cet index. Lors de la recherche des données à l’aide de la recherche d’Azure, les requêtes de recherche de texte intégral sont émis par rapport à un index particulier.  Pour comparer ces concepts à celles d’une base de données, les champs peuvent être comparées à des colonnes dans une table et documents peuvent être comparées à des lignes.

### <a name="scalability"></a>Évolutivité
N’importe quel service de recherche d’Azure dans le Standard [niveau de tarification](https://azure.microsoft.com/pricing/details/search/) peut mettre à l’échelle en deux dimensions : stockage et disponibilité.
* _Les partitions_ peuvent être ajoutés pour augmenter le stockage d’un service de recherche.
* _Les réplicas_ peuvent être ajoutés à un service pour augmenter le débit des demandes capable de gérer un service de recherche.

Ajout et suppression des partitions et des duplications à permettra la capacité du service search pour augmenter la quantité de données et les demandes d’application de trafic. Dans l’ordre pour un service de recherche obtenir une lecture de [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), il nécessite deux réplicas. Dans l’ordre d’un service obtenir une lecture seule [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), il requiert trois répliques.


### <a name="service-and-index-limits-in-azure-search"></a>Limites de service et des index dans Azure recherche
Il existe quelques différents [niveaux de tarification](https://azure.microsoft.com/pricing/details/search/) dans Azure recherche, chaque niveau a différentes [limites et des quotas](search-limits-quotas-capacity.md). Certaines de ces limites sont au niveau du service, certaines sont au niveau de l’index, et certains sont au niveau de la partition.


|                                  | Base     | Standard1   | Standard2   | Standard3   | HD de Standard3  |
|----------------------------------|-----------|-------------|-------------|-------------|---------------|
| Réplicas maximum par Service     | 3         | 12          | 12          | 12          | 12            |
| Partitions maximales par Service   | 1         | 12          | 12          | 12          | 1             |
| Unités de recherche maximale (réplicas * Partitions) par Service | 3         | 36          | 36          | 36          | 36 (au maximum 3 partitions)            |
| Maximum de Documents par Service    | 1 million | 180 millions | 720 millions | 1,4 milliard de | 600 millions   |
| Stockage maximal par Service      | 2 GO      | 300 GO      | 1,2 TO      | 2.4 TO      | 600 GO        |
| Maximum de Documents par Partition  | 1 million | 15 millions  | 60 millions  | 120 millions | 200 millions   |
| Capacité maximale de stockage par Partition    | 2 GO      | 25 GO       | 100 GO      | 200 GO      | 200 GO        |
| Index maximales par Service      | 5         | 50          | 200         | 200         | 3000 (max 1000 index/partition)          |


#### <a name="s3-high-density"></a>S3 Haute densité '
Dans niveau de tarification de la recherche d’Azure S3, il existe une option pour le mode haute densité (HD) conçu spécifiquement pour les scénarios partagés. Dans de nombreux cas, il est nécessaire de prendre en charge d’un grand nombre de clients plus petits sous un seul service pour bénéficier des avantages de la simplicité et la rentabilité.

Permet de S3 HD pour les index de nombreux petits doit être emballée sous la gestion d’un service de recherche unique par vos commerciaux la possibilité de faire évoluer des index à l’aide de partitions pour avoir la possibilité d’héberger plusieurs index dans un seul service.

Concrètement, un service de S3 peut avoir entre 1 et 200 index ensemble héberger jusqu'à 1,4 milliard de documents. Un HD S3 permettrait d’autre part des index individuels pour uniquement aller jusqu'à 1 millions de documents, mais il peut gérer jusqu'à 1000 index par partition (jusqu'à 3000 par service) avec un nombre de document total de 200 millions par partition (jusqu'à 600 millions par service).



## <a name="considerations-for-multitenant-applications"></a>Considérations relatives aux applications mutualisées
Applications mutualisées doivent répartir efficacement les ressources entre les locataires tout en conservant un niveau de confidentialité entre les locataires différents. Il existe quelques considérations lors de la conception de l’architecture pour ce type d’application :

* _D’isolement de client :_ Développeurs d’application doivent prendre les mesures appropriées pour garantir qu’aucun locataires non autorisé ou indésirables d’accéder aux données des autres locataires. Au-delà du point de vue de la confidentialité des données, stratégies d’isolation de clients nécessitent une gestion efficace des ressources partagées et protection contre les voisins bruyants.
* _Coût des ressources en nuage :_ Comme avec toute autre application, solutions logicielles doivent rester concurrentiel en tant que composant d’une application partagé de coût.
* _Simplifie les opérations de :_ Lors du développement d’une architecture partagée, l’impact sur les opérations et la complexité de l’application est une considération importante. Recherche Azure a un [SLA de 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* _Envergure internationale :_ Applications mutualisées peuvent doivent servir efficacement les locataires qui sont distribuées dans le monde entier.
* _L’évolutivité :_ Les développeurs d’application doivent envisager leur rapprochement entre le maintien d’un niveau suffisamment faible de la complexité des applications et la conception de l’application à l’échelle avec le nombre de clients et de la taille des données et la charge de travail des locataires.

Recherche Azure offre quelques limites qui peuvent être utilisés pour isoler les données et la charge de travail des locataires.

## <a name="modeling-multitenancy-with-azure-search"></a>Modélisation de l’architecture multilocataire avec recherche d’Azure
Dans le cas d’un scénario partagé, le développeur de l’application consomme un ou plusieurs services de recherche et de diviser les locataires entre les services, les index ou les deux. Recherche Azure a quelques modèles courants lors de la modélisation d’un scénario partagé :

1. _Index par le locataire :_ Chaque client possède son propre index dans un service de recherche qui est partagé avec d’autres locataires.
1. _Service par client :_ Chaque client a son propre service de recherche d’Azure dédié, offrant le plus haut niveau de séparation des données et la charge de travail.
1. _La combinaison des deux :_ Les locataires, plus grandes, plus actives sont affectés services dédiés tandis que les locataires plus petits sont affectés des index individuels au sein des services partagés.

## <a name="1-index-per-tenant"></a>1. d’index par le locataire
![Une image du modèle d’index-par-clients](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Dans un modèle d’index-par-client, différents utilisateurs occupent un seul service de recherche d’Azure où chaque client possède son propre index.

Locataires isoler les données, car toutes les requêtes de recherche et des opérations de document sont émises au niveau de l’index de recherche d’Azure. Dans la couche d’application, il existe la connaissance nécessaire pour diriger le trafic des locataires différents index appropriés tout en gérant les ressources au niveau du service sur tous les locataires.

Un attribut de clé du modèle d’index-par-clients est la possibilité pour le développeur de l’application à surabonner la capacité d’un service de recherche parmi les locataires de l’application. Si les locataires ont une répartition inégale de la charge de travail, la combinaison optimale de locataires peut être distribuée sur les index d’un service de recherche pour prendre en charge un certain nombre de locataires très actifs, gourmandes en ressources lors du traitement d’une longue traîne de locataires moins actifs simultanément. Le compromis est l’impossibilité de gérer les situations où chaque client est simultanément très actif du modèle.

Le modèle d’index-par-locataire fournit la base pour un modèle de coût variable, où un service de recherche d’Azure entier est acheté initial et complété par la suite avec les locataires. Ainsi, pour une capacité inutilisée à être désigné pour les essais et les comptes gratuits.

Pour les applications d’envergure internationale, le modèle d’index-par-locataire n’est peut-être pas la plus efficace. Si les locataires d’une application sont distribués dans le monde entier, un service distinct peut être nécessaire pour chaque région, qui peut dupliquer des coûts sur chacun d’eux.

Recherche Azure permet de l’échelle de l’index individuels et le nombre total d’index augmente. Si un prix appropriés couche est choisie, les partitions et duplications peuvent être ajoutées au service de recherche lorsqu’un index individuel au sein du service devient trop volumineux en termes de stockage ou le trafic.

Si le nombre total d’index devient trop volumineux pour un seul service, un autre service doit être configuré pour accueillir les locataires de nouveau. Si l’index ne doit être déplacé entre des services de recherche lors de l’ajout de nouveaux services, les données de l’index doit être copié manuellement à partir d’un index à l’autre comme Azure recherche ne permet pas à un index à déplacer.


## <a name="2-service-per-tenant"></a>2. le Service par le client
![Une image du modèle de service-par-clients](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Dans une architecture de service-par-clients, chaque client possède son propre service de recherche.

Dans ce modèle, l’application permet d’atteindre le niveau maximum d’isolation pour ses locataires. Chaque service a dédié de stockage et débit pour la demande de recherche ainsi que les clés API séparées.

Pour les applications où chaque client est encombrante ou la charge de travail a peu de variabilité de clients aux clients, le modèle de service-par-clients est un choix effectif que les ressources ne sont pas partagées entre les charges de travail des locataires différents.

Un service par modèle de clients offre également l’avantage d’un modèle de coûts prévisible et fixe. Il n’existe aucun investissement initial dans un service de recherche jusqu'à ce qu’un client pour le remplir, mais le coût par client est supérieur à un modèle d’index-par-clients.

Le modèle de service-par-clients est un choix efficace pour les applications d’envergure internationale. Avec les locataires géographiquement, il est facile d’avoir les service de chaque client dans la région appropriée.

Les défis liés à ce modèle de mise à l’échelle surviennent lorsque des locataires dépassent leur service. Recherche Azure ne prend pas actuellement en charge mise à niveau de la couche de tarification d’un service de recherche, afin que toutes les données doivent être copiés manuellement vers un nouveau service.

## <a name="3-mixing-both-models"></a>3. les deux modèles de mélange
Un autre modèle pour la modélisation de l’architecture multilocataire est mélange de stratégies-index par client et de service-par-locataire.

En mélangeant les deux modèles, les locataires de la plus grandes d’une application peuvent occuper services dédiés pendant la longue traîne de locataires moins actifs, plus petites peut occuper un index dans un service partagé. Ce modèle garantit que les locataires de plus grandes performances élevées à partir du service tout en protégeant les locataires plus petits à partir de n’importe quel voisins bruyants.

Toutefois, la mise en œuvre de cette stratégie s’appuie prospective à prévoir les locataires nécessitera un service dédié plutôt que l’index d’un service partagé. La complexité des applications augmente avec la nécessité de gérer les deux de ces modèles multitenancy.

## <a name="achieving-even-finer-granularity"></a>Atteindre la granularité encore plus fine
Les modèles de conception ci-dessus pour modéliser des scénarios mutualisées dans Azure recherche supposent une étendue uniforme dans lequel chaque client est une instance entière d’une application. Toutefois, les applications peuvent gérer parfois plusieurs zones plus petites.

Si les modèles de service-par-clients et clients-par-index ne sont pas suffisamment petites étendues, il est possible de modéliser un index pour parvenir à un niveau encore plu de granularité.

Pour qu’un seul index se comportent différemment pour les points de terminaison clients différents, un champ peut être ajouté à un indice qui désigne une certaine valeur pour chaque client possible. Chaque fois qu’un client appelle recherche Azure pour interroger ou modifier un index, le code de l’application cliente spécifie la valeur appropriée pour ce champ à l’aide de la fonction de [filtre](https://msdn.microsoft.com/library/azure/dn798921.aspx) de la recherche d’Azure au moment de la requête.

Cette méthode peut être utilisée pour obtenir une fonctionnalité de comptes d’utilisateurs distincts, les niveaux d’autorisation distincts et même complètement les applications distinctes.

## <a name="next-steps"></a>Étapes suivantes
Recherche Azure est un outil de premier choix pour de nombreuses applications, [en savoir plus sur ses fonctionnalités robustes](http://aka.ms/whatisazsearch). Lorsque vous évaluez les différents modèles de conception pour les applications mutualisées, envisagez les [divers niveaux de tarification](https://azure.microsoft.com/pricing/details/search/) et des [limites de service](search-limits-quotas-capacity.md) pour mieux adapter recherche Azure pour correspondre aux charges de travail applicatives et les architectures de toutes tailles.

Pour toute question sur la recherche d’Azure et scénarios partagés peut être dirigées vers azuresearch_contact@microsoft.com.
