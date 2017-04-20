<properties 
    pageTitle="Configurer géo-réplication de base de données de SQL Azure avec le portail Azure | Microsoft Azure" 
    description="Configurer géo-réplication de base de données de SQL Azure en utilisant le portail Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/18/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Configurer géo-réplication de base de données de SQL Azure avec le portail Azure


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-geo-replication-overview.md)
- [Azure portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Cet article vous indique comment configurer géo-réplication Active pour la base de données SQL [Azure portal](http://portal.azure.com).

Pour initier un basculement sur incident avec le portail Azure, consultez [initier un basculement planifié ou de base de données de SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] Geo-réplication Active (lisibles secondaires) est maintenant disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017, est déclassé le type secondaire non lisible et bases de données non lisible passeront automatiquement à secondaires lisibles.

Pour configurer la réplication géographique via le portail Azure, vous devez la ressource suivante :

- Une base de données Azure SQL - la base de données principale que vous souhaitez répliquer à une région géographique différente.

## <a name="add-secondary-database"></a>Ajouter une base de données secondaire

Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat de réplication géographique.  

Pour ajouter un secondaire, vous devez être le propriétaire de l’abonnement ou le copropriétaire. 

La base de données secondaire aura le même nom que la base de données primaire et aura, par défaut, le même niveau de service. La base de données secondaire peut être une seule base de données ou une base de données élastique. Pour plus d’informations, consultez [Niveaux de Service](sql-database-service-tiers.md).
Une fois l’image secondaire est créée et amorcé, données commencera la réplication à partir de la base de données principale vers la nouvelle base de données secondaire. 

> [AZURE.NOTE] Si la base de données de partenaire existe déjà (par exemple - à la suite de terminaison d’une relation géo-réplication précédente) la commande échoue.

### <a name="add-secondary"></a>Ajouter secondaire

1. Dans le [portail Azure](http://portal.azure.com), accédez à la base de données que vous souhaitez configurer pour la réplication de la zone géographique.
2. Sur la page de base de données SQL, sélectionnez **Réplication géographique**et puis sélectionnez la zone pour créer la base de données secondaire. Vous pouvez sélectionner n’importe quelle région que la région hébergeant la base de données principale, mais la [région associée](../best-practices-availability-paired-regions.md) est recommandé.

    ![configurer la réplication de la zone géographique](./media/sql-database-geo-replication-portal/configure-geo-replication.png)


4. Sélectionner ou configurer le serveur et le niveau de tarification pour la base de données secondaire.

    ![configurer secondaire](./media/sql-database-geo-replication-portal/create-secondary.png)

5. Si vous le souhaitez, vous pouvez ajouter une base de données secondaire à un pool d’élasticité de la base de données :

 Pour créer la base de données secondaire dans un pool, cliquez sur **pool d’élasticité de la base de données** et sélectionnez un pool dans le serveur cible. Un pool doit déjà exister sur le serveur cible, que ce flux de travail ne crée pas un pool.

6. Cliquez sur **créer** pour ajouter l’image secondaire.
 
6. La base de données secondaire est créée et commence par le processus d’amorçage. 
 
    ![configurer secondaire](./media/sql-database-geo-replication-portal/seeding0.png)

7. Lorsque le processus d’amorçage est terminé, la base de données secondaire affiche son état.

    ![amorçage terminé](./media/sql-database-geo-replication-portal/seeding-complete.png)


## <a name="remove-secondary-database"></a>Supprimer la base de données secondaire

L’opération termine la réplication à la base de données secondaire de manière permanente et modifie le rôle de secondaire à une base de données en lecture-écriture. Si la connectivité à la base de données secondaire est rompue, la commande aboutit, mais le secondaire est n’est ne pas en lecture-écriture jusqu'à ce que la connectivité est restauré.  

1. Dans le [portail Azure](http://portal.azure.com), accédez à la base de données primaire du partenariat de réplication géographique.
2. Sur la page de la base de données SQL, sélectionnez **Réplication géographique**.
3. Dans la liste de **serveurs secondaires** , sélectionnez la base de données que vous souhaitez supprimer le partenariat de réplication géographique.
4. Cliquez sur **Arrêter la réplication**.

    ![supprimer le secondaire](./media/sql-database-geo-replication-portal/remove-secondary.png)

5. Cliquez sur **Arrêter la réplication** ouvre une fenêtre de confirmation cliquez sur **Oui** pour supprimer la base de données à partir du partenariat de réplication géographique (définissez-la à une base de données en lecture-écriture ne faisant pas partie de toute réplication).


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur géo-réplication Active, consultez - [Géo-réplication](sql-database-geo-replication-overview.md) Active
- Pour une vue d’ensemble de la continuité de l’activité commerciale et scénarios, consultez [vue d’ensemble de la continuité de l’activité commerciale](sql-database-business-continuity.md)

