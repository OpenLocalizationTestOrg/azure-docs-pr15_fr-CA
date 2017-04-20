<properties
   pageTitle="Quel est le magasin de données SQL Azure ? | Microsoft Azure"
   description="Entreprise distribuée capable de traiter des volumes de pétaoctets de données relationnelles et non relationnelles de la base de données. Il est premier nuage data warehouse de l’industrie avec agrandir, réduire et pause en secondes."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# <a name="what-is-azure-sql-data-warehouse"></a>Quel est le magasin de données SQL Azure ?

Entrepôt de données SQL Azure est une nuage cloisonnée de base de données capable de traiter de très gros volumes de données, relationnelles et non relationnelles. Grâce à notre architecture de traitement massivement parallèle (MPP), SQL Data Warehouse peut gérer votre charge de travail d’entreprise.

Magasin de données SQL :

- Combine la base de données relationnelle SQL Server avec des capacités de déploiement Azure cloud. Vous pouvez augmenter, diminuer, suspendre ou reprendre le calcul en secondes. Vous enregistrez les coûts en mise à l’échelle du processeur lorsque vous en avez besoin et en éliminant l’utilisation durant les heures creuses.
- S’appuie sur la plateforme Azure. Il est facile à déployer, gérées en toute transparence et entièrement de pannes tolérance en raison des sauvegardes automatiques.
- Complète l’écosystème de SQL Server. Vous pouvez développer avec connaître SQL Server de Transact-SQL (T-SQL) et les outils.

Cet article décrit les fonctionnalités clés du magasin de données SQL.

## <a name="massively-parallel-processing-architecture"></a>Architecture de traitement massivement parallèle

SQL Data Warehouse est qu'un traitement massivement parallèle (MPP) distribuée de système de base de données. En divisant les données et capacités de traitement sur plusieurs nœuds, SQL Data Warehouse peut offrir évolutivité considérable - au-delà d’un seul système.  Dans les coulisses, SQL Data Warehouse permet de répartir vos données sur des unités de traitement et stockage de type shared-nothing. Les données sont enregistrées dans un stockage redondant localement Premium et liées à des nœuds pour l’exécution de la requête. Avec cette architecture, SQL Data Warehouse adopte une approche de « diviser pour mieux régner » à l’exécution des charges et des requêtes complexes. Les demandes sont reçues sur le nœud du contrôle, optimisés et ensuite passés aux nœuds de calcul pour effectuer leur travail en parallèle.

En combinant l’architecture MPP et des capacités de stockage Azure, SQL Data Warehouse peut :

- Augmenter ou réduire le stockage indépendant du calcul.
- Agrandir ou réduire des compute sans déplacement de données.
- Suspendre la capacité de calcul tout en conservant les données intactes.
- Reprendre la capacité en un clin de calcul.

Le diagramme suivant illustre l’architecture plus en détail.

![Architecture de magasin de données SQL][1]


**Nœud contrôle :** Le nœud de contrôle gère et optimise les requêtes. Il s’agit de la partie frontale qui interagit avec toutes les applications et les connexions. SQL Data Warehouse, le nœud contrôle d’est alimenté par la base de données SQL, et y connecter recherche et estime le même. Sous la surface, le nœud de contrôle coordonne tous les calculs nécessaires à l’exécution de requêtes parallèles sur les données distribuées et déplacement de données. Lorsque vous soumettez une requête T-SQL à SQL Data Warehouse, le nœud contrôle de transforme en requêtes distinctes qui s’exécutent sur chaque nœud de calcul en parallèle.

**Les nœuds de calcul :** Les nœuds de calcul servent à la puissance de SQL Data Warehouse. Ils sont les bases de données SQL que stocker vos données et de traiter votre requête. Lorsque vous ajoutez des données, SQL Data Warehouse distribue les lignes à vos nœuds de calcul. Les nœuds de calcul sont les travailleurs qui exécutent les requêtes parallèles sur vos données. Après le traitement, ils transmettent les résultats sur le nœud de contrôle. Pour terminer la requête, le nœud de contrôle regroupe les résultats et retourne le résultat final.

**Stockage :** Vos données sont stockées dans le stockage des objets Blob Azure. Lorsque les nœuds de calcul interagissent avec vos données, écrire et de lire directement vers et depuis le stockage blob. Étant donné que le stockage Azure s’étend largement et de façon transparente, SQL Data Warehouse peut faire de même. Étant donné que le calcul et stockage sont indépendants, SQL Data Warehouse peut automatiquement mettre à l’échelle stockage séparément à partir de la mise à l’échelle de calcul et vice versa. Stockage de Blob Azure est également totalement à tolérance de pannes et rationalise le processus de sauvegarde et de restauration.

**Service de mouvement de données :** Service de transfert de données (DMS) déplace les données entre les nœuds. Système de gestion documentaire donne l’accès aux nœuds de calcul aux données que dont ils ont besoin pour les jointures et les agrégations. Système de gestion documentaire n’est pas un service Azure. Il s’agit d’un service Windows qui s’exécute à côté de la base de données SQL sur tous les nœuds. Étant donné que le système de gestion documentaire s’exécute en arrière-plan, vous n’interagissez directement avec elle. Toutefois, lorsque vous consultez les plans de requête, vous remarquerez qu’ils comprennent certaines opérations DMS dans la mesure où le déplacement de données est nécessaire à l’exécution de chaque requête en parallèle.


## <a name="optimized-for-data-warehouse-workloads"></a>Optimisé pour les charges d’entrepôt de données

L’approche MPP est facilité par un certain nombre d’optimisations de performances spécifiques, y compris de l’entrepôt de données :

- Un optimiseur de requêtes distribuées et des statistiques complexes au sein des données. À l’aide des informations sur la distribution et de la taille des données, le service est en mesure d’optimiser les requêtes en évaluant le coût des opérations de requête distribuée spécifique.

- Techniques et des algorithmes avancés intégrés dans le processus de déplacement de données à déplacer efficacement des données parmi les ressources informatiques nécessaires pour exécuter la requête. Ces opérations de déplacement de données sont intégrées, et toutes les optimisations au Service de mouvement de données se produisent automatiquement.

- Ordonnés en **columnstore** par défaut. À l’aide de stockage basé sur la colonne, SQL Data Warehouse obtient en moyenne 5 x compression gains, par rapport au stockage traditionnel en ligne et jusqu'à 10 fois ou plusieurs gains de performances de requête. Requêtes Analytique qui ont besoin d’analyser un grand nombre de travaux de lignes sur un index de columnstore.


## <a name="predictable-and-scalable-performance"></a>Performances prévisibles et évolutive

SQL Data Warehouse sépare de stockage et de calcul, ce qui permet à chaque mise à l’échelle de façon indépendante. SQL Data Warehouse peut évoluer rapidement et simplement pour ajouter des ressources de calcul supplémentaire en un clin de. Ce complément est l’utilisation de stockage des objets Blob Azure. BLOB fournit un stockage stable et répliqué, mais également l’infrastructure d’expansion sans effort à moindre coût. Cette combinaison de nuage-évolutivité du stockage et de calcul Azure, SQL Data Warehouse vous permet de payer pour le stockage et les performances des requêtes lorsque vous en avez besoin. Modification de la quantité de calcul est aussi simple que le déplacement d’un curseur dans Azure portal à gauche ou à droite, ou il peut également être planifiée à l’aide de T-SQL et PowerShell.

Avec la possibilité de prendre le contrôle total de la quantité de calcul indépendamment du stockage, SQL Data Warehouse vous permet de suspendre totalement de votre entrepôt de données, ce qui signifie que vous ne payez pas de calcul lorsque vous n’avez pas besoin. Tout en conservant votre stockage en place, tous les compute est libéré dans le pool principal d’Azure, vous économisant de l’argent. Lorsque cela est nécessaire, simplement reprendre le calcul et que vos données et disponibles pour votre charge de travail de calcul.

## <a name="data-warehouse-units"></a>Unités d’entrepôt de données

Allocation de ressources à votre magasin de données SQL est mesurée en unités de magasin de données (DWUs). DWUs est une mesure des ressources sous-jacentes, telles que processeur, mémoire, e/s, qui sont affectés à votre magasin de données SQL. Augmentation du nombre de DWUs augmente les performances et les ressources. En particulier, DWUs vous assurer que :

- Vous ne pouvez mettre à l’échelle de votre entrepôt de données facilement, sans se soucier du matériel ou du logiciel sous-jacent.

- Vous pouvez prédire amélioration des performances d’un niveau DWU avant de modifier la taille de votre entrepôt de données.

- Le matériel et les logiciels de votre instance sous-jacente pour modifier ou déplacer sans affecter les performances de votre charge de travail.

- Microsoft peut modifier l’architecture sous-jacente du service sans affecter les performances de votre charge de travail.

- Microsoft peut rapidement améliorer les performances dans SQL Data Warehouse, d’une manière qui est évolutif et uniformément des effets du système.

Unités d’entrepôt de données fournissent une mesure des trois mesures précises hautement mises en corrélation avec les données de performance de la charge de travail de l’entrepôt. L’objectif est que les mesures suivantes de la clé de la charge de travail évolue de façon linéaire avec les DWUs que vous avez choisi pour votre entrepôt de données.

**Analyse/agrégation :** Cette mesure de la charge de travail prend une requête qui analyse un grand nombre de lignes puis effectue une agrégation complexe de l’entrepôt de données standard. Il s’agit d’une opération gourmande en e/s et du processeur.

**Charge :** Cette mesure mesure la capacité de transfert de données dans le service. Charges sont complétées par PolyBase du chargement d’un jeu de données représentatif de stockage des objets Blob Azure. Cette mesure vise à réseau de stress et aspects de processeur du service.

**Créer la Table comme Select (SACT) :** SACT mesure la capacité de copier une table. Cela implique la lecture des données de stockage, sa distribution sur les nœuds de la solution matérielle-logicielle et son écriture dans le stockage à nouveau. Il s’agit d’une opération intensive du processeur, d’e/s et le réseau.

## <a name="pause-and-scale-on-demand"></a>Pause et l’échelle à la demande

Lorsque vous avez besoin des résultats plus rapidement, augmenter votre DWUs et payer pour de meilleures performances. Lorsque vous avez besoin de moins grande puissance de traitement, diminuer votre DWUs et payez uniquement ce dont vous avez besoin. Vous pourriez penser sur la modification de votre DWUs dans ces scénarios :

- Lorsque vous n’avez pas besoin exécuter des requêtes, par exemple dans le soir ou le week-end, suspendre vos requêtes. Puis mettez en pause les ressources de calcul pour éviter de payer pour DWUs lorsque vous n’en avez pas besoin.

- Lorsque votre système a faible demande, envisagez de réduire les DWU à une taille réduite. Vous pouvez toujours accéder les données, mais à des économies de coût importantes.

- Lors de l’exécution d’une opération de chargement ou de transformation de données lourdes, vous souhaiterez peut-être évoluer afin que vos données sont disponibles plus rapidement.

Pour comprendre la valeur DWU idéale, essayez de mise à l’échelle et vers le bas jusqu'à, ainsi que quelques requêtes en cours d’exécution après le chargement de vos données. Dans la mesure où il est rapide de mise à l’échelle, vous pouvez essayer un nombre de différents niveaux de performance en une heure ou moins.  Conservez à l’esprit que SQL Data Warehouse est conçu pour traiter d’importants volumes de données et de voir ses capacités trues pour la mise à l’échelle, en particulier pour les échelles de plus grandes que nous proposons, vous voudrez utiliser des jeux de données volumineux qui approche ou dépasse de 1 To.


## <a name="built-on-sql-server"></a>Basé sur SQL Server

SQL Data Warehouse est basé sur le moteur de base de données relationnelle de SQL Server et inclut de nombreuses fonctionnalités que vous attendez d’un data warehouse d’entreprise. Si vous connaissez déjà le T-SQL, il est facile de transférer vos connaissances à l’entrepôt de données SQL. Si vous sont avancées ou novice, les exemples dans la documentation vous aide à commencer. En général, vous pouvez considérer à propos de la façon que nous avons créé les éléments de langage de l’entrepôt de données de SQL comme suit :

- SQL Data Warehouse utilise une syntaxe de T-SQL pour de nombreuses opérations. Il prend également en charge un large éventail de constructions SQL traditionnelles, telles que les procédures stockées, fonctions définies par l’utilisateur, le partitionnement de table, les index et les classements.

- SQL Data Warehouse contient également un certain nombre de nouvelles fonctionnalités de SQL Server, y compris : clustered index de **columnstore** , l’intégration de la PolyBase et les données d’audit (complète avec évaluation des menaces).

- Certains éléments de langage T-SQL qui sont moins courantes pour les charges de travail de l’entrepôt de données, ou qui sont plus récent pour SQL Server, n’est peut-être pas disponibles actuellement. Pour plus d’informations, consultez la [documentation de la Migration][].

Avec la compatibilité Transact-SQL et la fonction entre SQL Server, SQL Data Warehouse, de la base de données SQL et système de plate-forme Analytique, vous pouvez développer une solution adaptée à vos besoins. Vous pouvez décider de l’emplacement conserver vos données, en fonction des performances, de sécurité et exigences en matière d’échelle et ensuite de transférer les données si nécessaire entre les différents systèmes.

## <a name="data-protection"></a>Protection des données

SQL Data Warehouse stocke toutes les données dans le stockage Azure Premium localement redondant. Plusieurs copies synchrones des données sont conservées dans le centre de données local pour garantir la protection des données transparente en cas de défaillance localisée. En outre, SQL Data Warehouse sauvegarde automatiquement vos bases de données actives (reprises) à intervalles réguliers à l’aide d’instantanés de stockage Azure. Pour en savoir plus sur la sauvegarde et la restauration fonctionne, consultez la [vue d’ensemble de sauvegarde et de restauration][].

## <a name="integrated-with-microsoft-tools"></a>Intégration avec les outils Microsoft

SQL Data Warehouse s’intègre également plusieurs outils que SQL Server les utilisateurs connaissent peut-être. Celles-ci comprennent :

**Les outils traditionnels de SQL Server :** SQL Data Warehouse est entièrement intégré à SQL Server Analysis Services, Integration Services et Reporting Services.

**Outils basés sur le cloud :** Entrepôt de données SQL peut être utilisé avec un certain nombre de nouveaux outils dans Azure, y compris le Factory de données, flux Analytique, apprentissage automatique et alimentation BI. Pour une liste plus complète, consultez la [vue d’ensemble d’outils intégrés][].

**Des outils tiers :** Un grand nombre de fournisseurs de l’outil tiers ont certifié intégration de leurs outils avec SQL Data Warehouse. Pour une liste complète, consultez [les partenaires solution SQL Data Warehouse][].

## <a name="hybrid-data-sources-scenarios"></a>Scénarios de sources de données hybride

À l’aide de SQL Data Warehouse avec PolyBase offre aux utilisateurs une possibilité sans précédent pour déplacer des données dans tout leur écosystème, déverrouillage de la possibilité de configurer des scénarios avancés hybride avec non relationnelles et sources de données sur site.

Polybase vous permet d’exploiter vos données de différentes sources à l’aide des commandes T-SQL. Polybase vous permet d’interroger les données non relationnelles contenues dans le stockage des objets Blob Azure comme s’il s’agit d’un tableau ordinaire. Utilisez Polybase pour interroger des données non relationnelles ou d’y importer des données non relationnelles SQL Data Warehouse.

- PolyBase utilise des tables externes d’accéder aux données non relationnelles. Les définitions de table sont stockées dans l’entrepôt de données SQL et vous pouvez y accéder à l’aide de SQL et les outils comme vous accédez à des données relationnelles normales.

- Polybase est agnostique dans son intégration. Il expose les mêmes fonctionnalités pour toutes les sources qui prend en charge la fonctionnalité. Les données lues par Polybase peuvent être dans différents formats, y compris les fichiers délimités ou les fichiers ORC.

- PolyBase peut être utilisé pour accéder au stockage de blob est également utilisé comme espace de stockage pour un cluster de HDInsight. Cela vous permet d’accéder aux mêmes données avec des outils relationnelles et non relationnelles.

## <a name="sla"></a>CONTRAT SLA

SQL Data Warehouse propose un contrat de niveau de niveau de service (SLA) produit dans le cadre du contrat SLA de Microsoft Online Services. Pour plus d’informations, reportez-vous au [contrat SLA pour SQL Data Warehouse][]. Pour les informations de contrat SLA sur tous les autres produits, vous pouvez consulter le [Contrats de niveau de Service] Azure de page ou les télécharger sur la page de [Licence en Volume][] . 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez un peu sur SQL Data Warehouse, découvrez comment rapidement [créer un entrepôt de données SQL][] et de [charger des exemples de données][]. Si vous êtes nouveau sur Azure, est préférable le [Glossaire Azure][] que vous rencontrez de nouveaux éléments de terminologie. Ou bien, examinons certaines de ces autres ressources de magasin de données SQL.  

- [Témoignages clients]
- [Blogs]
- [Demandes de fonctionnalités]
- [Vidéos]
- [Blogs de l’équipe de conseil clientèle]
- [Créer le ticket de support]
- [Forum MSDN]
- [Forum de débordement de pile]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Créer le ticket de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[charger des exemples de données]: ./sql-data-warehouse-load-sample-databases.md
[Créez un entrepôt de données SQL]: ./sql-data-warehouse-get-started-provision.md
[Documentation de la migration]: ./sql-data-warehouse-overview-migrate.md
[Partenaires solution SQL Data Warehouse]: ./sql-data-warehouse-partner-business-intelligence.md
[Vue d’ensemble d’outils intégrés]: ./sql-data-warehouse-overview-integrate.md
[Vue d’ensemble de sauvegarde et de restauration]: ./sql-data-warehouse-restore-database-overview.md
[Glossaire Azure]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Témoignages clients]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe de conseil clientèle]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum de débordement de pile]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Contrat SLA pour l’entrepôt de données SQL]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Licences en volume]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Accords de niveau de service]: https://azure.microsoft.com/en-us/support/legal/sla/
