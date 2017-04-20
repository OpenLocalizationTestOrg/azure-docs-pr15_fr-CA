<properties
   pageTitle="Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (aperçu) | Microsoft Azure"
   description="Échelle de performances des fonctionnalités dans l’entrepôt de données SQL Azure. Évoluer en ajustant la DWUs ou de suspendre et de reprendre des ressources informatiques pour réduire les coûts."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (présentation)

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-data-warehouse-manage-compute-overview.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

L’architecture de l’entrepôt de données de SQL sépare le stockage et le calcul, permettant à chacun à l’échelle indépendamment. Par conséquent, vous pouvez augmenter les performances lors de l’enregistrement des coûts en payant uniquement pour les performances lorsque vous en avez besoin. 

Cette présentation décrit les capacités horizontale suivant de l’entrepôt de données de SQL et donne des recommandations sur la manière et quand les utiliser. 

- Échelle de grande puissance de traitement en réglant les [unités (DWUs) du data warehouse][]
- Suspendre ou reprendre les ressources de calcul

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>Adapter les performances

SQL Data Warehouse, vous pouvez rapidement mettre à l’échelle performance out ou en augmentant ou en diminuant les ressources de calcul du processeur, la mémoire et de la bande passante. Pour adapter les performances, il vous suffit de faire est d’ajuster le nombre d' [unités (DWUs) du data warehouse][] qui alloue de l’entrepôt de données SQL pour votre base de données. SQL Data Warehouse apporte la modification rapide et gère toutes les modifications de matériel ou de logiciel sous-jacent.

Révolue l’époque où vous devez rechercher le type de processeurs, la quantité de mémoire ou le type de stockage vous devez avoir des performances exceptionnelles dans votre entrepôt de données. En plaçant votre entrepôt de données dans le nuage, vous n’avez plus à faire face à des problèmes de matériel de bas niveau. Au lieu de cela, SQL Data Warehouse vous pose cette question : combien de temps souhaitez-vous analyser vos données ? 

### <a name="how-do-i-scale-performance"></a>Comment ajuster les performances ?

Pour ELASTIQUEMENT augmenter ou diminuer votre puissance de calcul, changez simplement le paramètre [unités (DWUs) l’entrepôt de données][] pour votre base de données. Les performances sont améliorées linéairement lorsque vous ajoutez plus de DWU.  À des niveaux supérieurs de DWU, vous devez ajouter plus de 100 DWUs Notez une amélioration significative des performances. Pour vous aider à sélectionner des déviations significatives dans DWUs, nous offrent les niveaux DWU qui donnent les meilleurs résultats.
 
Pour ajuster les DWUs, vous pouvez utiliser une de ces méthodes individuelles.

- [Échelle de calcul puissance avec Azure portal][]
- [Échelle de calcul d’alimentation avec PowerShell][]
- [Puissance de calcul d’échelle avec les autres API][]
- [Échelle de calcul puissance avec TSQL][]

### <a name="how-many-dwus-should-i-use"></a>DWUs combien dois-je utiliser ?
 
Performances SQL Data Warehouse évoluent linéairement et que la modification à partir de l’échelle d’un ordinateur à un autre (par exemple de 100 DWUs pour 2000 DWUs) en secondes. Cela vous permet de faire des essais avec différents paramètres de DWU jusqu'à ce que vous déterminiez ajuster au mieux à de votre scénario.

Pour comprendre la valeur DWU idéale, essayez de mise à l’échelle et vers le bas jusqu'à, ainsi que quelques requêtes en cours d’exécution après le chargement de vos données. Dans la mesure où il est rapide de mise à l’échelle, vous pouvez essayer un nombre de différents niveaux de performance en une heure ou moins. Conservez à l’esprit que SQL Data Warehouse est conçu pour traiter d’importants volumes de données et de voir ses capacités trues pour la mise à l’échelle, en particulier pour les échelles de plus grandes que nous proposons, vous voudrez utiliser des jeux de données volumineux qui approche ou dépasse de 1 To.

Recommandations pour la recherche de la meilleure DWU pour votre charge de travail :

1. Pour un entrepôt de données en cours de développement, commencez par sélectionner un petit nombre de DWUs.  Un bon point de départ est DW400 ou DW200.
2. Surveiller les performances de votre application, en respectant que le nombre de DWUs sélectionné par rapport aux performances que vous observez.
3. Déterminez combien accélérer ou ralentir les performances vous permettant d’atteindre le niveau de performances optimales pour vos besoins en considérant l’échelle linéaire.
4. Augmentez ou diminuez le nombre de DWUs en fonction de comment beaucoup plus rapidement ou plus lentement vous souhaitez que votre charge de travail à effectuer. Le service répond rapidement et ajuster les ressources de calcul afin de répondre aux nouvelles exigences de DWU.
5. Continuer à faire des ajustements jusqu'à atteindre un niveau de performance optimal pour les besoins de votre entreprise.

### <a name="when-should-i-scale-dwus"></a>Quand dois-je faire évoluer DWUs ?

Lorsque vous avez besoin des résultats plus rapidement, augmenter votre DWUs et payer pour de meilleures performances.  Lorsque vous avez besoin de moins grande puissance de traitement, diminuer votre DWUs et payez uniquement ce dont vous avez besoin. 

Recommandations pour laquelle mettre à l’échelle de DWUs :

1. Si votre application a une charge de travail fluctuante, DWU niveaux d’échelle d’ou vers le bas pour prendre en charge les pics et les points faibles. Par exemple, si votre charge de travail est généralement optimale à la fin du mois, prévoyez d’ajouter plus de DWUs au cours de ces jours de pointe, puis les mettre à l’échelle vers le bas une fois la période de pointe.
2. Avant d’effectuer une opération de chargement ou de transformation de données lourdes, mettre à l’échelle des DWUs afin que vos données sont disponibles plus rapidement.

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Calcul de pause

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez une de ces méthodes individuelles.

- [Calcul de pause avec Azure portal][]
- [Calcul de pause avec PowerShell][]
- [Calcul de pause avec les autres API][]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Calcul de la reprise

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Pour reprendre une base de données, utilisez une de ces méthodes individuelles.

- [Calcul de reprise avec Azure portal][]
- [Calcul de reprise avec PowerShell][]
- [Calcul de reprise avec les autres API][]

## <a name="permissions"></a>Autorisations

Mise à l’échelle de la base de données requiert les autorisations décrites dans [ALTER DATABASE][].  Suspendre et reprendre nécessite l’autorisation de [Collaboration de base de données SQL][] , spécifiquement Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes
Veuillez consulter les articles suivants pour vous aider à comprendre certains concepts clés de performance supplémentaires :

- [Gestion de la charge de travail et d’accès concurrentiel][]
- [Vue d’ensemble du design de table][]
- [Distribution de tables][]
- [L’indexation de tableau][]
- [Le partitionnement de table][]
- [Statistiques de la table][]
- [Meilleures pratiques][]

<!--Image reference-->

<!--Article references-->
[unités d’entrepôt de données (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Échelle de calcul puissance avec Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Échelle de calcul d’alimentation avec PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Puissance de calcul d’échelle avec les autres API]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Échelle de calcul puissance avec TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Calcul de pause avec Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Calcul de pause avec PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Calcul de pause avec les autres API]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Calcul de reprise avec Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Calcul de reprise avec PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Calcul de reprise avec les autres API]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Gestion de la charge de travail et d’accès concurrentiel]: ./sql-data-warehouse-develop-concurrency.md
[Vue d’ensemble du design de table]: ./sql-data-warehouse-tables-overview.md
[Distribution de tables]: ./sql-data-warehouse-tables-distribute.md
[L’indexation de tableau]: ./sql-data-warehouse-tables-index.md
[Le partitionnement de table]: ./sql-data-warehouse-tables-partition.md
[Statistiques de la table]: ./sql-data-warehouse-tables-statistics.md
[Meilleures pratiques]: ./sql-data-warehouse-best-practices.md 
[development overview]: ./sql-data-warehouse-overview-develop.md

[Contributor de base de données SQL]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[MODIFIER LA BASE DE DONNÉES]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
