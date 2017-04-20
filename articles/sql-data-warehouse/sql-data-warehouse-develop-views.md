<properties
   pageTitle="Vues SQL Data Warehouse | Microsoft Azure"
   description="Conseils d’utilisation des vues de Transact-SQL dans le magasin de données SQL Azure pour le développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="jrj;barbkess;sonyama"/>


# <a name="views-in-sql-data-warehouse"></a>Vues SQL Data Warehouse

Les vues sont particulièrement utiles dans l’entrepôt de données SQL. Ils peuvent être utilisés dans un certain nombre de façons d’améliorer la qualité de votre solution.  Cet article met en évidence quelques exemples montrant comment enrichir votre solution avec des vues, ainsi que les limitations qui doivent être pris en considération.

> [AZURE.NOTE] Syntaxe de `CREATE VIEW` n’est pas abordé dans cet article. Reportez-vous à l’article [Créer un affichage][] sur MSDN pour obtenir ces informations de référence.

## <a name="architectural-abstraction"></a>Abstraction architecturale
Un modèle d’application très courante est de recréer les tables à l’aide de créer tableau comme sélectionner (SACT) suivi d’un objet de renommer le modèle pendant le chargement de données.

L’exemple ci-dessous ajoute les enregistrements de la nouvelle date à une dimension de date. Notez comment un nouveau tableau, DimDate_New, est d’abord créé et ensuite renommé pour remplacer la version d’origine de la table.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Toutefois, cette approche peut générer des tableaux qui apparaissent et disparaissent de la vue d’un utilisateur, ainsi que des messages d’erreur « la table n’existe pas ». Vues peuvent être utilisées pour fournir aux utilisateurs une couche de présentation cohérent tandis que les objets sous-jacents sont renommés. En fournissant aux utilisateurs un accès aux données via une vue, signifie que les utilisateurs ne doivent disposer de visibilité des tables sous-jacentes. Il fournit une expérience utilisateur cohérente tout en garantissant que les données les concepteurs du warehouse peut évoluer le modèle de données et optimiser les performances pendant le processus de chargement de données à l’aide de SACT.    

## <a name="performance-optimization"></a>Optimisation des performances
Vues peuvent également être utilisés pour appliquer des jointures de performances optimisées entre les tables. Par exemple, une vue peut incorporer une clé de répartition redondante dans le cadre des critères de jointure afin de réduire le déplacement des données.  Un autre avantage d’une vue peut être pour forcer une requête spécifique ou un indicateur de jointure. Utilisation des vues de cette manière garantit que les jointures sont toujours effectuées de façon optimale en évitant la nécessité pour les utilisateurs de garder à l’esprit la construction correcte pour les jointures.

## <a name="limitations"></a>Limitations
Vues SQL Data Warehouse sont uniquement des métadonnées.  En conséquence les options suivantes ne sont pas disponibles :

-   Il n’y a aucune option de liaison de schéma
-   Tables de base ne peut pas être mis à jour par le biais de la vue
-   Vues ne peuvent pas être créés sur des tables temporaires
-   Il n’y a aucune prise en charge pour le développement / les indications NOEXPAND
-   Il existe des vues indexées dans SQL Data Warehouse


## <a name="next-steps"></a>Étapes suivantes
Pour plus de conseils de développement, consultez [vue d’ensemble du développement de SQL Data Warehouse][].
Pour `CREATE VIEW` syntaxe, reportez-vous à [CREATE VIEW][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble du développement SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CRÉATION DE VUE]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
