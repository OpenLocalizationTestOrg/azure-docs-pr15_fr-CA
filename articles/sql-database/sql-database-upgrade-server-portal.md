<properties
    pageTitle="Mise à niveau V12 de base de données SQL Azure via le portail Azure | Microsoft Azure"
    description="Explique comment mettre à niveau vers V12 de base de données SQL Azure, y compris la mise à niveau des bases de données Web et de l’entreprise et comment mettre à niveau un serveur V11 migrer ses bases de données directement dans un pool élastique de la base de données à l’aide du portail Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/08/2016"
    ms.author="sstein"/>


# <a name="upgrade-to-azure-sql-database-v12-using-the-azure-portal"></a>Mise à niveau V12 de base de données SQL Azure via le portail Azure


> [AZURE.SELECTOR]
- [Azure portal](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de base de données SQL est la version la plus récente mise à niveau de serveurs existants vers V12 de base de données SQL est recommandé.
V12 de base de données SQL a notamment de nombreux [avantages par rapport à la version précédente](sql-database-v12-whats-new.md) :

- Compatibilité accrue avec SQL Server.
- Les performances améliorées de prime et nouveaux niveaux de performances.
- [Les pools élastique de la base de données](sql-database-elastic-pool.md).

Cet article fournit des instructions relatives à la mise à niveau de bases de données et les serveurs SQL de base de données V11 existants vers V12 de base de données SQL.

Au cours du processus de mise à niveau vers V12 vous mettra à niveau les bases de données Web et de l’entreprise à un nouveau niveau de service afin que les instructions de mise à niveau des bases de données Web et de l’entreprise sont incluses.

En outre, la migration vers un [pool d’élastique de la base de données](sql-database-elastic-pool.md) peut être plus efficace que la mise à niveau vers des niveaux de performances individuelles (tarification niveaux) pour les bases de données unique. Pools simplifient également la gestion de la base de données car vous n’avez besoin gérer les paramètres de performances pour le pool, plutôt que de gestion distincte des niveaux de performances des bases de données. Si vous avez des bases de données sur plusieurs serveurs envisagez de les déplacer dans le même serveur et en tirant parti de les placer dans un pool. Vous pouvez facilement [migrer les bases de données à partir de serveurs V11 directement dans les pools élastique de la base de données à l’aide de PowerShell automatique](sql-database-upgrade-server-powershell.md). Vous pouvez également utiliser le portail pour migrer des bases de données V11 dans un pool, mais dans le portail vous devez déjà disposer d’un serveur V12 pour créer un pool. Indications sont fournies plus loin dans cet article pour créer le pool après la mise à niveau du serveur, si vous avez des [bases de données qui peuvent tirer parti d’un pool](sql-database-elastic-pool-guidance.md).

Notez que vos bases de données va rester en ligne et continuer à travailler pendant toute l’opération de mise à niveau. Au moment de la transition réelle pour le nouveau niveau de performances temporaire de suppression des connexions à la base de données peut se produire pour une durée de très petite taille qui est généralement environ 90 secondes, mais peut être autant que 5 minutes. Si votre application a les [pannes transitoires pour les interruptions de connexion](sql-database-connectivity-issues.md) qu’il est suffisant pour se protéger contre les pertes de connexion à la fin de la mise à niveau.

Mise à niveau vers V12 de base de données SQL ne peut pas être annulée. Après une mise à niveau, le serveur ne peut pas être annulée en V11.

Après la mise à niveau à V12, [recommandations de niveau de service](sql-database-service-tier-advisor.md) et les [Considérations relatives aux performances de pool élastique](sql-database-elastic-pool-guidance.md) pas seront immédiatement disponibles jusqu'à ce que le service de temps pour évaluer vos charges de travail sur le nouveau serveur. Historique de recommandation de serveur V11 ne s’applique pas aux serveurs de V12 afin qu’il n’est pas conservé.

## <a name="prepare-to-upgrade"></a>Préparer la mise à niveau

- **Mise à niveau de toutes les bases de données Web et de l’entreprise**: voir section ci-dessous ou la [mise à niveau de toutes les bases de données Web et de l’entreprise](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) [moniteur et de gérer un pool de la base de données élastique (PowerShell)](sql-database-elastic-pool-manage-powershell.md).
- **Examen et suspendre la réplication géo**: Si votre base de données Azure SQL est configuré pour la réplication de la zone géographique, vous devez documenter sa configuration actuelle et [Arrêter la réplication de la zone géographique](sql-database-geo-replication-portal.md#remove-secondary-database). Une fois la mise à niveau terminée reconfigurer votre base de données pour la réplication de la zone géographique.
- **Ouverture de ces ports si vous avez des clients sur un ordinateur virtuel d’Azure**: Si votre programme client se connecte à SQL de base de données V12 alors que votre client s’exécute sur une machine virtuelle Azure (VM), vous devez ouvrir des plages de ports 11999-11000 et 14000-14999 sur l’ordinateur virtuel. Pour plus d’informations, reportez-vous à la section [Ports pour V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).



## <a name="start-the-upgrade"></a>Démarrer la mise à niveau

1. Dans la rechercher [Azure portal](https://portal.azure.com/) sur le serveur que vous souhaitez mettre à niveau en sélectionnant **Parcourir** > **les serveurs SQL**et en sélectionnant le serveur de version 2.0 que vous souhaitez mettre à niveau.
2. Sélectionnez la **Dernière mise à jour de base de données SQL**, puis sélectionnez la **mise à niveau de ce serveur**.

      ![mise à niveau du serveur][1]

3. Mise à niveau d’un serveur à la dernière mise à jour de base de données SQL est permanente et irréversible. Pour confirmer la mise à niveau, tapez le nom de votre serveur, puis cliquez sur **OK**.

## <a name="upgrade-all-web-and-business-databases"></a>Mise à niveau de toutes les bases de données Web et de l’entreprise

Si votre serveur dispose d’un site Web ou de bases de données commerciales vous devez les mettre à niveau. Au cours du processus de mise à niveau vers SQL de base de données V12 vous mettra à jour toutes les bases de données Web et de l’entreprise à un nouveau niveau de service.    

Pour vous aider avec la mise à niveau, le service de base de données SQL recommande une couche et performances niveau de service approprié (niveau de tarification) pour chaque base de données. Le service vous recommande la meilleure couche pour l’exécution de la charge de travail de votre base de données existante en analysant l’historique de l’utilisation de votre base de données.

3. Dans la **mise à niveau de ce serveur** lame sélectionnez chaque base de données à vérifier, puis sélectionnez la tarification recommandée de niveau pour mettre à niveau. Vous pouvez toujours parcourir les niveaux de prix disponibles et sélectionnez celui le mieux à votre environnement.


     ![bases de données][2]


7. Après avoir cliqué sur la couche de suggestion s’affiche avec la lame de **Choisir votre niveau de tarification** , où vous pouvez sélectionner un niveau et puis cliquez sur **le bouton pour passer à ce niveau** . Sélectionnez un nouveau niveau pour chaque base de données Web ou d’entreprise.

    Ce qui est important à noter est que votre base de données SQL n’est pas verrouillé dans n’importe quel niveau de performances ou de niveau service spécifique, afin que les exigences de votre base de données vous pouvez facilement changer entre les niveaux de service disponible et performances. En fait, Basic, Standard et Premium SQL bases de données sont facturés à l’heure, et vous avez la possibilité de mettre à l’échelle de chaque base de données vers le haut ou vers le bas 4 fois dans un délai de 24 heures.

    ![recommandations][6]


Une fois que toutes les bases de données sur le serveur sont éligibles, vous êtes prêt à commencer la mise à niveau

## <a name="confirm-the-upgrade"></a>Confirmer la mise à niveau

3. Lorsque toutes les bases de données sur le serveur sont éligibles pour la mise à niveau vous devez **TYPE le nom du serveur** pour vérifier que vous souhaitez effectuer la mise à niveau, puis cliquez sur **OK**.

    ![vérifier la mise à niveau][3]


4. La mise à niveau démarre et affiche la progression de notification. Le processus de mise à niveau est lancé. Les détails de vos bases de données spécifiques en fonction de la mise à niveau vers V12 peut prendre du temps. Pendant ce temps, toutes les bases de données sur le serveur restera en ligne mais le serveur et les actions de gestion de base de données sera limitées.

    ![mise à niveau en cours][4]

    Au moment de la transition réelle sur les performances de nouveau niveau temporaire de suppression des connexions à la base de données peut se produire pour une durée de très petite taille (généralement mesurée en secondes). Si une application dispose d’une gestion des pannes transitoires (logique des nouvelles tentatives) pour des interruptions de connexion, il est suffisant pour se protéger contre les pertes de connexion à la fin de la mise à niveau.

5. La lame de la **Dernière mise à jour** affiche **activé**après la fin de l’opération de mise à niveau.

    ![V12 activé][5]  

## <a name="move-your-databases-into-an-elastic-database-pool"></a>Déplacez vos bases de données dans un pool d’élasticité de la base de données

Dans le [portail Azure](https://portal.azure.com/) naviguer vers le serveur V12 et cliquez sur **Ajouter un pool**.

- ou -

Si vous voyez un message indiquant **Cliquez ici pour afficher les pools élastique recommandés de la base de données pour ce serveur**, cliquez sur pour créer facilement un pool qui est optimisé pour les bases de données de votre serveur. Pour plus d’informations, consultez [Considérations de prix et de performances pour un pool d’élasticité de la base de données](sql-database-elastic-pool-guidance.md).

![Ajouter un pool à un serveur][7]

Suivez les instructions dans l’article [créer un pool de la base de données élastique](sql-database-elastic-pool.md) pour terminer la création de votre pool.

## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Surveiller les bases de données après la mise à niveau vers V12 de base de données SQL

>[AZURE.IMPORTANT] Mise à niveau vers la dernière version de SQL Server Management Studio (SSMS) pour tirer parti des nouvelles fonctionnalités v12. [Télécharger SQL Server Management Studio] (https://msdn.microsoft.com/library/mt238290.aspx).

Après la mise à niveau, surveiller activement la base de données pour vous assurer que les applications exécutent lors de l’exécution de votre choix et puis optimiser les paramètres selon vos besoins.

Outre la surveillance des bases de données, vous pouvez surveiller les pools [moniteur, gérer et la taille d’un pool élastique de la base de données avec le portail Azure](sql-database-elastic-pool-manage-portal.md) élastique de la base de données ou avec [PowerShell](sql-database-elastic-pool-manage-powershell.md).


**Données de consommation de ressources :** Basique, Standard et Premium, des bases de données de consommation de ressources sont disponibles par le biais de la [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV dans la base de données de l’utilisateur. Cette vue offre près en temps réel la consommation informations sur la ressource à 15 deuxième niveau de granularité pour l’heure précédente de l’opération. La consommation de pourcentage DTU pour un intervalle est calculée comme la consommation de pourcentage maximal des dimensions UC, d’e/s et le journal. Voici une requête pour calculer la consommation de pourcentage DTU moyenne sur la dernière heure :

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informations supplémentaires :

- [Guide de performances de base de données de SQL azure des bases de données unique](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considérations de prix et de performances pour un pool d’élasticité de la base de données](sql-database-elastic-pool-guidance.md).
- [Surveillance de base de données SQL Azure à l’aide des vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)




**Alertes :** Configurer « Alertes » dans le portail Azure afin de vous avertir lorsque certain niveau élevé est proche de la consommation de DTU pour une base de données mis à niveau. Alertes de la base de données peuvent être le programme d’installation dans le portail Azure pour diverses métriques de performances telles que DTU, UC, d’e/s et journal. Recherchez votre base de données, puis sélectionnez les **règles d’alerte** de la lame de **paramètres** .

Par exemple, vous pouvez définir jusqu'à un message d’alerte sur le « Pourcentage de DTU » si la valeur de pourcentage DTU moyenne est supérieure à 75 % sur au cours des 5 dernières minutes. Pour en savoir plus sur la configuration des notifications d’alerte, reportez-vous à [recevoir des notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) .





## <a name="next-steps"></a>Étapes suivantes

- [Vérification des recommandations de pool et de créer un pool](sql-database-elastic-pool-create-portal.md).
- [Modifier le niveau de performances et de la couche de service de votre base de données](sql-database-scale-up.md).



## <a name="related-links"></a>Liens connexes

- [Quelles sont les nouveautés dans V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Planifier et préparer la mise à niveau vers V12 de base de données SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png
