<properties
   pageTitle="Migrer votre entrepôt de données de SQL Azure existant pour le stockage de prime | Microsoft Azure"
   description="Instructions pour la migration d’un entrepôt de données SQL existant pour le stockage de la prime"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="nicw;barbkess;sonyama"/>

# <a name="migration-to-premium-storage-details"></a>Migration vers les détails de stockage Premium
SQL Data Warehouse a récemment introduit [Prime de stockage pour une plus grande prévisibilité de performances][].  Nous sommes maintenant prêts à migrer des entrepôts de données existant actuellement sur le stockage Standard de stockage de la prime.  Continuez la lecture pour plus d’informations sur quand et comment des migrations automatiques ont lieu et comment effectuer une migration automatique si vous souhaitez contrôler lorsque les temps d’arrêt se produit.

Si vous avez plus d’un entrepôt de données, utilisez la [planification de la migration automatique][] ci-dessous pour déterminer le moment où il sera également migré.

## <a name="determine-storage-type"></a>Déterminer le type de stockage
Si vous avez créé un Data Warehouse avant les dates ci-dessous, vous utilisez actuellement le stockage Standard.  Chaque entrepôt de données sur le stockage Standard qui est soumis à la migration automatique a une notification en haut de la lame d’entrepôt de données dans le [Portail Azure][] indiquant « mise à niveau de*un à venir pour le stockage de prime nécessitera une panne.  En savoir plus ->*. »

| **Région**          | **DW créée avant cette Date**   |
| :------------------ | :-------------------------------- |
| Est de l’Australie      | Stockage de la prime n’est pas encore disponible |
| Sud-est de l’Australie | 5 août 2016                    |
| Sud du Brésil        | 5 août 2016                    |
| Canada Central      | 25 mai 2016                      |
| Est du Canada         | 26 mai 2016                      |
| États-Unis centre          | 26 mai 2016                      |
| Chine orientale          | Stockage de la prime n’est pas encore disponible |
| Nord de la Chine         | Stockage de la prime n’est pas encore disponible |
| Asie de l’est           | 25 mai 2016                      |
| Les États-Unis             | 26 mai 2016                      |
| Les US2            | 27 mai 2016                      |
| Central de l’Inde       | 27 mai 2016                      |
| Sud de l’Inde         | 26 mai 2016                      |
| Ouest de l’Inde          | Stockage de la prime n’est pas encore disponible |
| Nord-est du Japon          | 5 août 2016                    |
| Ouest du Japon          | Stockage de la prime n’est pas encore disponible |
| États-Unis centre nord    | Stockage de la prime n’est pas encore disponible |
| Europe du Nord        | 5 août 2016                    |
| États-Unis centre sud    | 27 mai 2016                      |
| Asie du Sud-est      | 24 mai 2016                      |
| Europe de l’ouest         | 25 mai 2016                      |
| Ouest des États-Unis centre     | 26 août 2016                   |
| États-Unis Ouest             | 26 mai 2016                      |
| US2 ouest            | 26 août 2016                   |

## <a name="automatic-migration-details"></a>Détails de la migration automatique
Par défaut, nous allons migrer votre base de données pour vous au cours des 18 h 00 et 06 h 00 heure locale de votre région au cours de la [planification de la migration automatique][] ci-dessous.  Votre entrepôt de données existant sera inutilisable pendant la migration.  Nous estimons que la migration prendra environ une heure par To de stockage par Data Warehouse.  Nous nous assurons également que vous ne seront pas imputés au cours d’une partie de la migration automatique.

> [AZURE.NOTE] Vous ne serez pas en mesure d’utiliser votre entrepôt de données existant lors de la migration.  Une fois la migration terminée, votre entrepôt de données sera en ligne.

Les informations ci-dessous sont les étapes que Microsoft prend pour effectuer la migration à votre place et ne nécessite pas d’intervention de votre part.  Dans cet exemple, imaginez que votre DW existant sur le stockage Standard actuellement nommé « MyDW ».

1.  Microsoft renomme « MyDW » à « MyDW_DO_NOT_USE_ [Timestamp] »
2.  Microsoft s’arrête à « MyDW_DO_NOT_USE_ [Timestamp]. »  Pendant ce temps, une sauvegarde est effectuée.  Vous pouvez voir plusieurs pause/reprend si nous rencontrer des problèmes au cours de ce processus.
3.  Microsoft crée un nouveau DW nommé « MyDW » sur le stockage de Premium à partir de la sauvegarde effectuée à l’étape 2.  « MyDW » n’apparaîtra pas jusqu'à ce qu’une fois la restauration terminée.
4.  Une fois la restauration terminée, « MyDW » renvoie à la mêmes DWUs et l’état actif ou en pause, qu'il était avant la migration.
5.  Une fois la migration terminée, Microsoft supprime « MyDW_DO_NOT_USE_ [Timestamp] »
    
> [AZURE.NOTE] Ces paramètres ne s’appliquent pas dans le cadre de la migration :
> 
>   -  L’audit au niveau de la base de données doit être réactivé
>   -  Les règles de pare-feu au niveau de la **base de données** doivent être a.  Règles de pare-feu au niveau du **serveur** ne sont pas concernés.

### <a name="automatic-migration-schedule"></a>Planification de la migration automatique
Migrations automatiques se produisent à partir de 18 h 00 – 18 : 00 (heure locale par région) au cours de la planification suivante de la panne.

| **Région**          | **Date de début estimée**     | **Date de fin estimée**       |
| :------------------ | :--------------------------- | :--------------------------- |
| Est de l’Australie      | N’a pas déterminé encore           | N’a pas déterminé encore           |
| Sud-est de l’Australie | 10 août 2016              | 24 août 2016              |
| Sud du Brésil        | 10 août 2016              | 24 août 2016              |
| Canada Central      | 23 juin 2016                | 1 juillet 2016                 |
| Est du Canada         | 23 juin 2016                | 1 juillet 2016                 |
| États-Unis centre          | 23 juin 2016                | 4 juillet 2016                 |
| Chine orientale          | N’a pas déterminé encore           | N’a pas déterminé encore           |
| Nord de la Chine         | N’a pas déterminé encore           | N’a pas déterminé encore           |
| Asie de l’est           | 23 juin 2016                | 1 juillet 2016                 |
| Les États-Unis             | 23 juin 2016                | 11 juillet 2016                |
| Les US2            | 23 juin 2016                | 8 juillet 2016                 |
| Central de l’Inde       | 23 juin 2016                | 1 juillet 2016                 |
| Sud de l’Inde         | 23 juin 2016                | 1 juillet 2016                 |
| Ouest de l’Inde          | N’a pas déterminé encore           | N’a pas déterminé encore           |
| Nord-est du Japon          | 10 août 2016              | 24 août 2016              |
| Ouest du Japon          | N’a pas déterminé encore           | N’a pas déterminé encore           |
| États-Unis centre nord    | N’a pas déterminé encore           | N’a pas déterminé encore           |
| Europe du Nord        | 10 août 2016              | 31 août 2016              |
| États-Unis centre sud    | 23 juin 2016                | 2 juillet 2016                 |
| Asie du Sud-est      | 23 juin 2016                | 1 juillet 2016                 |
| Europe de l’ouest         | 23 juin 2016                | 8 juillet 2016                 |
| Ouest des États-Unis centre     | 14 août 2016              | 31 août 2016              |
| États-Unis Ouest             | 23 juin 2016                | 7 juillet 2016                 |
| US2 ouest            | 14 août 2016              | 31 août 2016              |

## <a name="self-migration-to-premium-storage"></a>Migration automatique vers un stockage de prime
Si vous souhaitez contrôler le moment où a lieu la période d’indisponibilité, vous pouvez utiliser les étapes suivantes pour migrer d’un entrepôt de données existant sur le stockage Standard pour le stockage de la prime.  Si vous choisissez d’effectuer une migration automatique, vous devez effectuer la migration automatique avant le début de la migration automatique dans cette région afin d’éviter tout risque de migration automatique provoque un conflit de (reportez-vous à la [planification de la migration automatique][]).

### <a name="self-migration-instructions"></a>Instructions de migration automatique
Si vous souhaitez contrôler la période d’indisponibilité, vous pouvez automatiquement migrer votre Data Warehouse à l’aide de sauvegarde et de restauration.  La partie restauration de la migration devrait prendre environ une heure par To de stockage par Data Warehouse.  Si vous souhaitez conserver le même nom une fois la migration terminée, suivez les étapes de la [procédure pour renommer pendant la migration][]. 

1.  [Suspendre][] votre Data Warehouse qui prend une sauvegarde automatique
2.  [Restaurer][] à partir de votre instantané le plus récent
3.  Supprimer votre DW existant sur le stockage Standard. **Si vous ne parvenez pas à effectuer cette étape, les deux DWs vous sera facturée.**

> [AZURE.NOTE] Ces paramètres ne s’appliquent pas dans le cadre de la migration :
> 
>   -  L’audit au niveau de la base de données doit être réactivé
>   -  Les règles de pare-feu au niveau de la **base de données** doivent être a.  Règles de pare-feu au niveau du **serveur** ne sont pas concernés.

#### <a name="optional-steps-to-rename-during-migration"></a>Facultatif : comment renommer lors de la migration 
Deux bases de données sur le même serveur logique ne peut pas avoir le même nom. SQL Data Warehouse prend désormais en charge la possibilité de renommer un Data Warehouse.

Dans cet exemple, imaginez que votre DW existant sur le stockage Standard actuellement nommé « MyDW ».

1.  Renommer « MyDW » à l’aide de la commande ALTER DATABASE qui suit à quelque chose comme « MyDW_BeforeMigration ».  Cette commande supprime toutes les transactions existantes et doit être effectuée dans la base de données master pour réussir.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.  [Pause][] « MyDW_BeforeMigration », qui effectue une sauvegarde automatique
3.  [Restauration][] de votre plus récente de capture instantanée une nouvelle base de données avec le nom que vous avez utilisé pour l’avoir (ex : « MyDW »)
4.  Supprimer « MyDW_BeforeMigration ».  **Si vous ne parvenez pas à effectuer cette étape, les deux DWs vous sera facturée.**

> [AZURE.NOTE] Ces paramètres ne s’appliquent pas dans le cadre de la migration :
> 
>   -  L’audit au niveau de la base de données doit être réactivé
>   -  Les règles de pare-feu au niveau de la **base de données** doivent être a.  Règles de pare-feu au niveau du **serveur** ne sont pas concernés.

## <a name="next-steps"></a>Étapes suivantes
Avec la modification pour le stockage de la prime, nous avons également augmenté le nombre de fichiers blob de base de données dans l’architecture sous-jacente de votre entrepôt de données.  Pour optimiser les avantages de ce changement, nous vous recommandons de reconstruire votre index de Columnstore en cluster à l’aide du script suivant.  Le script ci-dessous fonctionne en forçant certaines de vos données existantes pour les objets BLOB supplémentaires.  Si vous entreprenez aucune action, les données seront naturellement redistribuer au fil du temps comme vous chargez davantage de données dans les tables du Data Warehouse.

**Conditions préalables :**

1.  Entrepôt de données doit s’exécuter avec 1 000 DWUs ou ultérieur (voir la [puissance de calcul d’échelle][])
2.  Utilisateur qui exécute le script doit être dans le [rôle de mediumrc][] ou supérieur
    1.  Pour ajouter un utilisateur à ce rôle, exécutez la commande suivante : 
        1.  ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Si vous rencontrez des problèmes avec votre Data Warehouse, [créer un ticket de support][] et le « Stockage de Migration à Premium » de référence comme étant la cause possible.

<!--Image references-->

<!--Article references-->
[planification de la migration automatique]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[Création d’un ticket de support]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Restauration]: sql-data-warehouse-restore-database-portal.md
[étapes à suivre pour renommer lors de la migration]: #optional-steps-to-rename-during-migration
[puissance de calcul d’échelle]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[rôle de mediumrc]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Stockage de prime pour une plus grande prévisibilité de performances]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
