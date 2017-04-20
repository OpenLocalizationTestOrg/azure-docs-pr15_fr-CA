<properties
   pageTitle="Transactions SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’implémentation des transactions dans le magasin de données SQL Azure pour le développement de solutions."
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="transactions-in-sql-data-warehouse"></a>Transactions dans le magasin de données SQL

Comme vous l’imaginez, SQL Data Warehouse prend en charge les transactions dans le cadre de la charge de travail de magasin de données. Toutefois, pour garantir que les performances de SQL Data Warehouse sont maintenue à échelle certaines fonctionnalités sont limitées par rapport à SQL Server. Cet article met en évidence les différences et les autres listes. 

## <a name="transaction-isolation-levels"></a>Niveaux d’isolement des transactions
SQL Data Warehouse implémente des transactions ACID. Toutefois, l’Isolation de la prise en charge transactionnelle est limitée à `READ UNCOMMITTED` et il ne peut pas être modifié. Vous pouvez implémenter un certain nombre de méthodes pour éviter les lectures de données modifiées de données s’il s’agit d’un problème pour vous de codage. Les méthodes les plus populaires exploitent à la fois SACT et le changement de partition table (souvent appelé le modèle de fenêtre coulissante) pour empêcher les utilisateurs de l’interrogation de données qui sont toujours en cours de préparation. Une approche populaire est également vues avant de filtrer les données.  

## <a name="transaction-size"></a>Taille de la transaction
Une transaction de modification de données unique est une taille limitée. La limite est aujourd'hui appliquée « par distribution ». Par conséquent, la répartition totale peut être calculée en multipliant la limite par le comptage de distribution. À approximative le nombre maximal de lignes de la transaction de diviser l’embout de distribution par la taille totale de chaque ligne. Pensez à prendre une longueur de colonne moyenne plutôt que d’utiliser la taille maximale pour les colonnes de longueur variable.

Dans le tableau ci-dessous les hypothèses suivantes ont été apportées :

* Une répartition uniforme des données s’est produite 
* La longueur de la ligne moyenne est de 250 octets

| [DWU][]    | Cap par distribution (talon) | Nombre de Distributions | Taille maximale de la transaction (le talon) | # Lignes par distribution | Nombre maximal de lignes par transaction |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100  |  1                         | 60                      |   60                       |   4,000,000             |    240,000,000           |
| DW200  |  1.5                       | 60                      |   90                       |   6,000,000             |    360,000,000           |
| DW300  |  2,25                      | 60                      |  135                       |   9,000,000             |    540,000,000           |
| DW400  |  3                         | 60                      |  180                       |  12,000,000             |    720,000,000           |
| DW500  |  3,75                      | 60                      |  225                       |  15,000,000             |    900,000,000           |
| DW600  |  4.5                       | 60                      |  270                       |  18,000,000             |  1,080,000,000           |
| DW1000 |  7.5                       | 60                      |  450                       |  30 000 000             |  1,800,000,000           |
| DW1200 |  9                         | 60                      |  540                       |  36,000,000             |  2,160,000,000           |
| DW1500 | 11,25                      | 60                      |  675                       |  45,000,000             |  2,700,000,000           |
| DW2000 | 15                         | 60                      |  900                       |  60 000 000             |  3,600,000,000           |
| DW3000 | 22.5                       | 60                      |  1,350                     |  90,000,000             |  5,400,000,000           |
| DW6000 | 45                         | 60                      |  2 700                     | 180,000,000             | 10,800,000,000           |

La taille limite de transaction est appliquée par l’opération ou la transaction. Elle n’est pas appliquée à toutes les transactions simultanées. Par conséquent, chaque transaction est autorisée à écrire cette quantité de données dans le journal. 

Afin d’optimiser et de réduire la quantité de données écrites dans le journal, reportez-vous à l’article [méthodes conseillées pour les Transactions][] .

> [AZURE.WARNING] La taille maximale de transaction ne peut être obtenue pour le hachage, ou même de tables ROUND_ROBIN distribués étant la propagation des données. Si la transaction écrit des données de manière asymétrique dans les distributions, la limite est susceptible d’être atteint avant la taille maximale de transaction.
<!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>État de la transaction
SQL Data Warehouse utilise la fonction XACT_STATE() pour signaler l’échec d’une transaction à l’aide de la valeur -2. Cela signifie que la transaction a échoué et qu’il est marquée pour la restauration uniquement

> [AZURE.NOTE] L’utilisation de -2 par la fonction XACT_STATE pour indiquer l’échec d’une transaction représente un comportement différent pour SQL Server. SQL Server utilise la valeur -1 pour représenter une transaction validable non. SQL Server peut tolérer des erreurs à l’intérieur d’une transaction sans qu’il ait été marqué comme non validable. Par exemple `SELECT 1/0` serait de provoquer une erreur mais ne force pas une transaction dans un état non validable. SQL Server permet également de lectures dans la transaction validable non. SQL Data Warehouse ne vous permet toutefois pas de cela. Si une erreur se produit à l’intérieur d’une transaction SQL Data Warehouse, il insère automatiquement l’état de-2 et vous ne serez pas en mesure d’apporter les autres instructions select jusqu'à ce que l’instruction a été annulée. Il est donc important de vérifier que le code de votre application pour voir si elle utilise XACT_STATE() comme vous devrez peut-être apporter des modifications de code.

Par exemple, dans SQL Server, vous pouvez voir une transaction qui se présente comme suit :

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Si vous laissez votre code comme c’est le cas ci-dessus, vous obtiendrez le message d’erreur suivant :

Msg 111233, niveau 16, état 1, ligne 1, 111233 ; La transaction en cours a été annulée et les modifications en attente ont été annulées. Cause : Une transaction dans un état de restauration seule a été pas explicitement annulée avant une DDL, DML ou une instruction SELECT.

Vous obtiendrez également pas la sortie des fonctions ERROR_ *.

Dans le magasin de données SQL, le code doit être légèrement modifiés :

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Le comportement attendu est observé maintenant. L’erreur dans la transaction est géré et les fonctions ERROR_ * fournissent des valeurs comme prévu.

Tout ce qui a changé est la `ROLLBACK` de la transaction devait se produire avant la lecture des informations d’erreur dans le `CATCH` bloc.

## <a name="errorline-function"></a>Fonction de Error_Line()
Il est également important de noter que SQL Data Warehouse ne pas mettre en œuvre ou prennent en charge la fonction ERROR_LINE(). Si vous avez cela dans votre code, que vous devez le supprimer pour être compatible avec le Data Warehouse de SQL. Utilisez à la place des étiquettes de requête dans votre code pour implémenter une fonctionnalité équivalente. Veuillez vous reporter à l’article [intitulé][] pour plus de détails sur cette fonctionnalité.

## <a name="using-throw-and-raiserror"></a>L’utilisation de THROW et RAISERROR
THROW est l’implémentation la plus moderne pour lever des exceptions dans l’entrepôt de données de SQL mais RAISERROR est également supporté. Il existe quelques différences qui méritent notre attention toutefois.

- Ne doit pas être dans la plage de 100 000-150 000 de jeter les messages d’erreur définis par l’utilisateur
- Messages d’erreur RAISERROR sont fixés à 50 000
- Utilisation de sys.messages n’est pas pris en charge.

## <a name="limitiations"></a>Limitiations
SQL Data Warehouse possède quelques autres restrictions qui sont liés à des transactions.

Ils sont comme suit :

- Aucune transaction distribuée
- Aucune des transactions imbriquées autorisées
- Ne pas enregistrer les points autorisé
- Aucune transaction nommée
- Aucune transaction marquée
- Aucune prise en charge pour DDL telles que `CREATE TABLE` à l’intérieur d’un utilisateur défini par transaction

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’optimisation des transactions, consultez [méthodes conseillées pour les Transactions][].  Pour en savoir plus sur les autres pratiques recommandées de SQL Data Warehouse, consultez méthodes [conseillées de SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[Meilleures pratiques de transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Meilleures pratiques de SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[ÉTIQUETTE]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
