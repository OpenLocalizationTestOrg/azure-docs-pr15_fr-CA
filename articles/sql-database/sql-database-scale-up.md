<properties
    pageTitle="Modifier le niveau de service niveau et les performances d’une base de données SQL d’Azure | Microsoft Azure"
    description="Modifier le niveau de service et le niveau de performances d’une base de données Azure SQL indique comment faire évoluer votre base de données SQL vers le haut ou vers le bas. Modification du niveau de tarification d’une base de données SQL d’Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Modifier le niveau et les performances niveau de service (couche de tarification) d’une base de données SQL via le portail Azure


> [AZURE.SELECTOR]
- [**Azure portal**](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Les niveaux de service et de performances décrivent les fonctionnalités et les ressources disponibles pour votre base de données SQL et peuvent être mis à jour selon les besoins de votre application. Pour plus d’informations, consultez [Niveaux de Service](sql-database-service-tiers.md).

Notez que la modification de la couche de service et/ou le niveau de performances d’une base de données crée une réplique de la base de données d’origine dans le nouveau niveau de performances et puis de passer les connexions au réplica. Pendant la brève intervention lorsque nous basculer vers le réplica, les connexions à la base de données sont désactivées, et certaines opérations en vol peuvent être annulées, mais aucune donnée n’est perdue au cours de ce processus. Cette fenêtre varie, mais est en moyenne moins de 4 secondes et dans plus de 99 % des cas est de moins de 30 secondes. Très rarement, en particulier si grand nombre de transactions à la volée sur les connexions du moment est désactivé, cette fenêtre peut être plus longue.  

La durée de la totalité du processus l’extrapolation dépend de la couche service et taille de la base de données avant et après la modification. Par exemple, une base de données de 250 Go est la modification de vers, d’ou au sein d’un niveau de service Standard, doit se terminer dans les 6 heures. Pour une base de données de la même taille que la modification des niveaux de performances dans le niveau de service Premium est, il doit se terminer dans les 3 heures.


Utilisez les informations dans [des niveaux de performances et de niveaux de Service de base de données SQL Azure](sql-database-service-tiers.md) afin de déterminer le niveau de performances et le niveau de service approprié pour votre base de données de SQL Azure.

- Pour mettre à niveau une base de données, la base de données doit être inférieure à la taille maximale autorisée de la couche de service cible. 
- Lors de la mise à niveau d’une base de données avec [Réplication géographique](sql-database-geo-replication-overview.md) est activée, vous devez d’abord passer ses bases de données secondaires vers le niveau de performance souhaité avant la mise à niveau de la base de données principale.
- Lors d’un déclassement d’un niveau de service, vous devez d’abord terminer toutes les relations de réplication géographique. 
- Les offres de service de restauration sont différentes pour les divers niveaux de service. Si la mise à niveau vous peuvent perdre la possibilité de restaurer à un point dans le temps, ou ont une période de rétention de sauvegarde inférieure. Pour plus d’informations, voir [restauration et sauvegarde de base de données SQL Azure](sql-database-business-continuity.md).
- La modification de votre base de données de niveau de prix ne modifie pas la taille maximale de la base de données. Pour modifier votre base de données taille maximale, utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- Les nouvelles propriétés de la base de données ne sont pas appliquées tant que les modifications sont terminées.



**Pour terminer cet article, vous devez les éléments suivants :**

- Une base de données SQL d’Azure. Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données de SQL Azure](sql-database-get-started.md).


## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Modifier le niveau de performances et de la couche de service de votre base de données


Ouvrez la lame de la base de données SQL pour la base de données que vous souhaitez mettre à l’échelle vers le haut ou vers le bas :

1.  Dans le [portail Azure](https://portal.azure.com), cliquez sur **plus de services** > **les bases de données SQL**.
2.  Cliquez sur la base de données que vous souhaitez modifier.
3.  Sur la lame de la **base de données SQL** , cliquez sur **niveau de tarification (échelle de DTUs)**:

    ![niveau de tarification][1]

1.  Choisissez un nouveau niveau, puis cliquez sur **Sélectionner**:

    Cliquez sur **Sélectionnez** soumet une demande d’échelle pour modifier le niveau de prix. Selon la taille de votre base de données mise à l’échelle peut prendre un certain temps à complète (voir les informations en haut de cet article).

    > [AZURE.NOTE] La modification de votre base de données de niveau de prix ne modifie pas la taille maximale de la base de données. Pour modifier votre base de données taille maximale, utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![Sélectionnez le niveau de tarification][2]

3.  Cliquez sur l’icône de notification (sonnette), dans le coin supérieur droit :

    ![notifications][3]

    Cliquez sur le texte de notification pour ouvrir le volet d’informations où vous pouvez consulter le statut de la demande.




## <a name="next-steps"></a>Étapes suivantes

- Modifier votre taille maximale de base de données à l’aide de [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Mettre à l’échelle et dans](sql-database-elastic-scale-get-started.md)
- [Se connecter et d’interroger une base de données SQL avec SSMS](sql-database-connect-query-ssms.md)
- [Exportation d’une base de données SQL d’Azure](sql-database-export.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)
- [Documentation de la base de données de SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png
