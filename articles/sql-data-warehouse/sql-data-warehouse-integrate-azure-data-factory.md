<properties
   pageTitle="Utiliser le Factory de données Azure avec SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’utilisation de fabrique de données Azure (CAD) à l’entrepôt de données SQL Azure pour le développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Utiliser le Factory de données Azure avec SQL Data Warehouse

Usine de données Azure fournit une méthode entièrement gérée pour organiser le transfert des données et l’exécution des procédures stockées sur SQL Data Warehouse.  Cela vous permettra plus facilement configuration et planification extraire de transformation et de chargement (ETL) des procédures complexes avec SQL Data Warehouse. Pour une vue d’ensemble plus complète de la fabrique de données Azure, consultez la [documentation d’Azure Data Factory][].

## <a name="data-movement"></a>Déplacement des données

Azure Data Factory permet le déplacement de données entre des sources sur site et de différents services Azure.  Intégration globale actuelle avec Azure Data Factory prend en charge le déplacement des données vers et à partir des emplacements suivants :

+ Stockage des objets blob Azure
+ Base de données SQL Azure
+ Local SQL Server
+ SQL Server sur IaaS

Pour plus d’informations sur le paramétrage des données de l’activité de copie voir [copier des données avec Azure Data Factory][]

## <a name="stored-procedures"></a>Procédures stockées
 De la même manière, qu'il peut être utilisé pour planifier le transfert des données, Azure Data Factory peut également servir à organiser l’exécution de procédures stockées.  Ainsi, des pipelines plus complexes à créer et permet d’étendre les capacités de fabrique de données Azure pour tirer parti de la puissance de calcul de l’entrepôt de données SQL.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble de l’intégration, consultez [Présentation de l’intégration SQL Data Warehouse][].
Pour plus de conseils de développement, consultez [vue d’ensemble du développement de SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Copier des données avec Azure Data Factory]: ../data-factory/data-factory-data-movement-activities.md
[Vue d’ensemble du développement SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Présentation de l’intégration SQL Data Warehouse]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentation de fabrique de données Azure]:https://azure.microsoft.com/documentation/services/data-factory/

