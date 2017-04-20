<properties
   pageTitle="Gérer les bases de données dans l’entrepôt de données SQL Azure | Microsoft Azure"
   description="Vue d’ensemble de la gestion des bases de données SQL Data Warehouse. Inclut des outils de gestion, de DWUs et des performances évolutives, résolution des problèmes de performances des requêtes, établissement de stratégies de sécurité et de la restauration d’une base de données à partir de la corruption des données ou d’une panne régionale."
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
   ms.date="08/16/2016"
   ms.author="barbkess;sonyama;"/>

# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Gérer les bases de données dans l’entrepôt de données SQL Azure

SQL Data Warehouse permet d’automatiser de nombreux aspects de la gestion de vos bases de données. Par exemple, pour faire évoluer les performances vous suffit de régler régler le niveau des ressources de calcul et laisser SQL Data Warehouse faire tout le travail de mise à l’échelle et la mise à l’échelle en. 

Vous souhaiterez sans doute surveiller votre charge de travail pour identifier vos besoins de performances ainsi que résoudre les requêtes d’exécution longue. Vous devrez également effectuer quelques tâches de sécurité pour gérer les autorisations pour les utilisateurs et les connexions.

Cette vue d’ensemble couvre les aspects de la gestion d’entrepôt de données SQL.

- Outils de gestion
- Calcul de l’échelle
- Pause et reprise
- Performances les meilleures pratiques
- Analyse de la requête
- Sécurité
- Sauvegarde et restauration

## <a name="management-tools"></a>Outils de gestion

Vous pouvez utiliser divers outils pour gérer des bases de données SQL Data Warehouse. Lorsque vous gérez des bases de données, vous allez développer des préférences de l’outil pour chaque type de tâche à effectuer.

### <a name="azure-portal"></a>Azure portal
Le [portail Azure][] est un portail web où vous pouvez créer, mettre à jour et de supprimer des bases de données et contrôler les ressources de la base de données. Cet outil est très utile est si vous êtes simplement mise en route avec Azure, gérez un petit nombre de bases de données magasin de données, ou si vous devez rapidement faire quelque chose.

Pour vous familiariser avec le portail Azure, consultez [créer un entrepôt de données SQL (Azure portal)][].

### <a name="sql-server-data-tools-in-visual-studio"></a>Outils de données de SQL Server dans Visual Studio
[Outils de données de SQL Server][] (SSDT) dans Visual Studio vous permet de vous connecter, gérer et développer vos bases de données. Si vous êtes un développeur d’applications familiarisé avec Visual Studio ou d’autres environnements de développement intégré (IDE), essayez d’utiliser SSDT dans Visual Studio.

SSDT comprend l’Explorateur d’objets SQL Server qui vous permet de visualiser, de se connecter et d’exécuter des scripts sur les bases de données SQL Data Warehouse. Pour vous connecter rapidement à un magasin de données SQL, vous pouvez cliquer simplement sur le bouton **Ouvrir dans Visual Studio** , dans la barre de commandes lorsque vous affichez les détails de la base de données dans le portail classique d’Azure.  

Pour commencer à utiliser SSDT dans Visual Studio, consultez [Requêtes Azure SQL Data Warehouse avec Visual Studio][].

### <a name="command-line-tools"></a>Outils de ligne de commande
Les outils de ligne de commande sont idéales pour l’automatisation de vos charges de travail.  PowerShell et sqlcmd sont deux façons intéressantes d’automatiser vos processus.  Nous vous recommandons de ces outils pour gérer un grand nombre de serveurs logiques et le déploiement des modifications de ressources dans un environnement de production comme les tâches nécessaires peuvent être scriptées et puis automatisées.

### <a name="dynamic-management-views"></a>Vues de gestion dynamique 

DMV est indispensables de gestion d’entrepôt de données SQL. Presque toutes les informations des surfaces dans le portail s’appuie sur les DMV. Pour voir une liste des DMV de magasin de données SQL, consultez [les vues système SQL Data Warehouse][].

Pour commencer, consultez [connexion et requête avec sqlcmd][]et [créer une base de données (PowerShell)][].

## <a name="scale-compute"></a>Calcul de l’échelle

SQL Data Warehouse, vous pouvez rapidement mettre à l’échelle performance out ou en augmentant ou en diminuant les ressources de calcul du processeur, la mémoire et de la bande passante. Pour adapter les performances, il vous suffit de faire est d’ajuster le nombre d’unités de magasin de données (DWUs) qui alloue de l’entrepôt de données SQL pour votre base de données. SQL Data Warehouse apporte la modification rapide et gère toutes les modifications de matériel ou de logiciel sous-jacent.

Pour plus d’informations sur la mise à l’échelle de DWUs, reportez-vous à [adapter les performances][].

##  <a name="pause-and-resume"></a>Pause et reprise

Pour réduire les coûts, vous pouvez suspendre et reprendre le calcul ressources à la demande. Par exemple, si vous n’utiliserez pas la base de données pendant la nuit et le week-end, vous pouvez suspendre il au cours de celles-ci et reprendre la lecture pendant la journée. Vous ne sera débitée pour DWUs, alors que la base de données est interrompue.

Pour plus d’informations, reportez-vous à la section [Pause compute][]et [compute de reprise][].

## <a name="performance-best-practices"></a>Performances les meilleures pratiques

Lors de la mise en route avec une nouvelle technologie, découvrir les conseils et astuces qui fonctionnent les droite à partir du début peut vous faire gagner beaucoup de temps.  Vous trouverez tout au long de la plupart de nos rubriques méthodes conseillées.

Pour voir de nombreux un résumé des considérations plus importantes lors du développement de votre charge de travail, consultez [Conseillées entrepôt de données SQL][].

## <a name="query-monitoring"></a>Analyse de la requête

Parfois une requête est trop longue, mais vous n’êtes pas sûr de celui qui est le coupable. SQL Data Warehouse dispose de vues de gestion dynamique (DMV) que vous pouvez utiliser pour déterminer quelle requête est trop longue. 

Pour rechercher les requêtes d’exécution longue, reportez-vous à [surveiller votre charge de travail à l’aide de la DMV][].

## <a name="security"></a>Sécurité

Pour maintenir un système sécurisé, vous devez être sur l’alerte et protection contre tous les types d’accès non autorisé. Un système de sécurité doit s’assurer que les règles de pare-feu sont en place uniquement autorisé les adresses IP peuvent se connecter. Il nécessite une authentification correcte des informations d’identification de l’utilisateur. Une fois un utilisateur s’est connecté à la base de données, l’utilisateur doit avoir uniquement des autorisations pour effectuer un nombre minimal d’actions. Pour sécuriser les données, vous pouvez utiliser le chiffrement. Il est également important de disposer d’audit et de suivi pour vous pouvez retracer des événements s’il existe toute activité suspecte.

Pour en savoir plus sur la gestion de sécurité, tête sur la [vue d’ensemble de la sécurité][].

## <a name="backup-and-restore"></a>Sauvegarde et restauration

Backps fiable de vos données est un élément essentiel de toute base de données de production. SQL Data Warehouse permet de sécuriser vos données par la sauvegarde automatique de vos bases de données actives à intervalles réguliers. Ces sauvegardes permettent de récupérer à partir de scénarios où vous avez vos données corrompues ou accidentellement supprimées les données ou la base de données.  Pour la planification de sauvegarde de données, de rétention et de la restauration d’une base de données, reportez-vous à la section [restaurer à partir de la capture instantanée][].

## <a name="next-steps"></a>Étapes suivantes
À l’aide de concevoir principes rendra plus facile à gérer vos bases de données SQL Data Warehouse. Pour en savoir plus, tête sur la [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[Créez un entrepôt de données SQL (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Créer une base de données (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Requête SQL Azure Data Warehouse avec Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Se connecter et d’interroger avec sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Vue d’ensemble du développement]: sql-data-warehouse-overview-develop.md
[Surveillez votre charge de travail à l’aide de la DMV]: sql-data-warehouse-manage-monitor.md
[Calcul de pause]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restaurer à partir de la capture instantanée]: sql-data-warehouse-restore-database-overview.md
[Calcul de la reprise]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[Adapter les performances]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Vue d’ensemble de la sécurité]: sql-data-warehouse-overview-manage-security.md
[Entrepôt de données SQL meilleures pratiques]: sql-data-warehouse-best-practices.md
[Vues système SQL Data Warehouse]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[Outils de données de SQL Server]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
