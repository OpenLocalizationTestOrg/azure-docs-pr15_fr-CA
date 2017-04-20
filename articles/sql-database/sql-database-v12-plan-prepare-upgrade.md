<properties
    pageTitle="Planifiez votre mise à niveau à SQL de base de données V12 | Microsoft Azure"
    description="Décrit les préparations et les limitations de mise à niveau vers la version V12 d’Azure SQL de base de données."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genemi"/>


# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Planifier et préparer la mise à niveau vers V12 de base de données SQL


Cette rubrique décrit la planification et la préparation que vous devez effectuer pour mettre à niveau vos bases de données SQL d’Azure à partir de la version V11 V12.


Un nouveau [Portail Azure](https://portal.azure.com/) est disponible pour prendre en charge de la mise à niveau à V12.


Le tableau suivant répertorie les autres rubriques d’aide pour V12.


| Titre et liens | Description du contenu |
| :--- | :--- |
| [Quelles sont les nouveautés dans V12 de base de données SQL](sql-database-v12-whats-new.md) | Décrit les détails de comment V12 apporte Azure SQL de base de données plus près à la parité complète avec Microsoft SQL Server. |
| [Créer une base de données dans V12 de base de données SQL](sql-database-get-started.md) | Décrit comment vous pouvez créer une nouvelle base de données Azure SQL version V12. Il décrit les différentes options au-delà de simplement une base de données vide. |


## <a name="plan-ahead"></a>Planifier à l’avance


Les sous-sections suivantes décrivent l’apprentissage et les décisions que vous devez résoudre avant de prendre une actions vers la mise à niveau de votre base de données Azure SQL vers V12.

### <a name="version-clarification"></a>Clarification de la version


Ce document porte sur la mise à niveau de base de données SQL de Microsoft Azure à partir de la version V11 V12. Plus formellement les numéros de version sont les deux valeurs suivantes, comme indiqué par l’instruction Transact-SQL **Sélectionnez @@version; ** :


- 12.0.2000.8 *(ou un peu plus élevé, V12)*
- 11.0.9228.18 *(11)*
 - V11 a été également parfois comme SAWA v2.

### <a name="service-tier-planning"></a>Planification de la couche de service


En commençant par V12, base de données de SQL Azure prend en charge uniquement les niveaux de service nommés Basic, Standard et Premium. La couche de service Web et de l’entreprise n’est pas pris en charge sur V12. Par conséquent, si vous envisagez de mettre à niveau votre base de données Azure SQL au niveau de la couche de service Web ou une entreprise, vous devez décider quelle doit être sa nouvelle couche de service.


Pour obtenir des informations détaillées sur les niveaux de service Basic, Standard et Premium, voir :

- [Niveaux de service de la base de données SQL](sql-database-service-tiers.md)
- [Mise à niveau des bases de données SQL de base de données Web/entreprise sur de nouveaux niveaux de Service](sql-database-upgrade-server-portal.md)



### <a name="review-the-geo-replication-configuration"></a>Vérifiez la configuration de réplication géographique


Si votre base de données Azure SQL est configuré pour la réplication de la zone géographique, vous devez documenter la configuration en cours et arrêter la réplication géographique, avant de commencer les opérations de préparation de mise à niveau. Une fois la mise à niveau terminée, vous devez reconfigurer votre base de données pour la réplication de la zone géographique.


La stratégie consiste à laisser la source intacte et la tester sur une copie de la base de données.


## <a name="preparation-actions"></a>Actions de préparation


Après avoir terminé votre planification, vous pouvez effectuer les actions décrites dans les sous-sections suivantes pour vous préparer à la phase finale de la mise à niveau.


Des descriptions détaillées de la phase finale de la mise à niveau sont disponibles dans les rubriques d’aide liées à en haut de cette rubrique d’aide.


### <a name="service-tier-actions"></a>Actions de niveau de service


Le service Web et de l’entreprise, niveau de prix n’est pas pris en charge sur V12.


Si votre base de données SQL Azure de 11 est une base de données Web ou une entreprise, le processus de mise à niveau propose passer de votre base de données à un niveau de prise en charge. La mise à niveau vous recommande un niveau qui correspond à l’historique de la charge de travail de votre base de données. Toutefois, vous pouvez choisir n’importe quel niveau pris en charge que.


Vous pouvez réduire les étapes nécessaires au cours de la mise à niveau en passant votre base de données de 11 à partir de la couche Web d’entreprises avant de commencer la mise à niveau. Pour cela, à l’aide du nouveau [Portail Azure](https://portal.azure.com/).


Si vous ne savez pas quel niveau de service pour basculer vers, S2 niveau de la couche Standard peut être un choix initial cohérent. N’importe quel niveau moindre aura moins de ressources que la couche Web et de l’entreprise a.


### <a name="suspend-geo-replication-during-upgrade"></a>Geo-suspendue au cours de la mise à niveau


Impossible d’exécuter la mise à niveau V12 si géo-réplication est active sur votre base de données. Vous devez d’abord reconfigurer votre base de données pour cesser d’utiliser la réplication géographique.


Une fois la mise à niveau terminée, vous pouvez configurer votre base de données pour utiliser à nouveau la géo-réplication.


### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Ports ouverts sur une machine virtuelle d’Azure pour la connectivité des clients


Si votre programme client se connecte à SQL de base de données V12 alors que votre client s’exécute sur une machine virtuelle Azure (VM), vous devez ouvrir les plages de ports suivants sur l’ordinateur virtuel :

- 11000-11999
- 14000-14999


Cliquez [ici](sql-database-develop-direct-route-ports-adonet-v12.md) pour plus d’informations sur les ports pour V12 de base de données SQL. Les ports sont requis par les améliorations des performances dans V12 de base de données SQL.


##<a id="limitations"></a>Limitations pendant et après la mise à niveau vers V12


### <a name="portals-for-v12"></a>Portails pour V12


Il existe trois portails pour Azure, et chacun a des capacités différentes en ce qui concerne les V12 de base de données SQL.


- [http://Portal.Azure.com/](https://portal.azure.com/)<br/>Ce portail Azure est nouveau et est toujours à l’état de l’aperçu. Ce portail n’est pas tout à fait à la totale disponibilité générale. Ce portail :
 - Peut gérer votre V12 serveur et la base de données.
 - Peut mettre à niveau votre base de données V11 à V12.


- [http://Manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Ce portail classique Azure peut être progressivement. Ce portail :
 - Peut gérer votre V12 serveur et la base de données.
 - Peut *pas* mise à niveau de base de données de votre V11 à V12.


- (http://*nom_serveur*. database.windows.net)<br/>Portail classique de base de données SQL Azure :
 - Pouvez*pas* gérer les serveurs V12.


Nous vous invitons à vous connecter à vos bases de données SQL d’Azure avec Visual Studio 2015 (VS2015). VS2015 peut être utilisé pour les tâches suivantes :


- Pour exécuter une instruction Transact-SQL.
- Pour créer un schéma.
- Développer une base de données, soit en ligne ou hors connexion.


Ou, au lieu de cela, vous pouvez télécharger gratuitement [Visual Studio 2015 de communauté](https://www.visualstudio.com/vs/community/), qui est une version complète de VS2015.


Le plus ancien portail classique Azure, sur la page de la base de données, vous pouvez cliquer sur **Ouvrir dans Visual Studio** pour lancer VS2015 sur votre ordinateur pour la connexion à votre base de données de SQL Azure.


Pour une autre, vous pouvez utiliser SQL Server Management Studio (SSMS) 2014 avec [CU6](http://support.microsoft.com/kb/3031047/) pour se connecter à la base de données de SQL Azure. Plus de détails sont sur ce billet de blog :<br/>Le [client mises à jour pour la base de données de SQL Azure des outils](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


> [AZURE.IMPORTANT] Il est recommandé de toujours utiliser la dernière version du Management Studio reste synchronisé avec les mises à jour Microsoft Azure et de base de données SQL. [Mise à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="limitation-during-upgrade-to-v12"></a>Limitation *lors de la* mise à niveau vers V12


La base de données V11 reste disponible pour accéder aux données au cours de la mise à niveau V12. Il y a encore quelques limitations à prendre en compte.


| Limitation | Description |
| :--- | :--- |
| Durée de mise à niveau | La durée de mise à niveau dépend de la taille, l’édition et la nombre de bases de données sur le serveur. Le processus de mise à niveau peut exécuter des heures à jours pour les serveurs, en particulier pour les serveurs contenant des bases de données :<br/><br/>*Plus de 50 Go, ou<br/> * À un niveau de service non-premium<br/><br/>Création de nouvelles bases de données sur le serveur au cours de la mise à niveau peut également augmenter la durée de mise à niveau. |
| Aucune réplication-Geo | Geo-réplication n’est pas pris en charge sur un serveur V12 actuellement impliqué dans une mise à niveau à partir de 11. |
| Base de données est brièvement indisponible dans la phase finale de la mise à niveau vers V12 | Les bases de données appartenant à votre serveur V11 restent disponibles pendant le processus de mise à niveau. Toutefois, la connexion au serveur et les bases de données est brièvement indisponible dans la phase finale, lorsque le commutateur sur commence à partir de 11 à la V12 prêt.<br/><br/>Le commutateur période peut être comprise entre 40 secondes à 5 minutes. Pour la plupart des serveurs, passage est censée se terminer dans les 90 secondes. Commutateur de temps augmente pour les serveurs qui possèdent un grand nombre de bases de données, ou lorsque les bases de données ont des charges de travail en écriture. |


### <a name="limitation-after-upgrade-to-v12"></a>Limitation *après* la mise à niveau à V12


| Limitation | Description |
| :--- | :--- |
| Impossible de rétablir V11 | Après une mise à niveau sur place, le résultat ne peut pas être restauré ou annulé. |
| Couche Web ou d’entreprise | Une fois la mise à niveau commence, le serveur pour la nouvelle base de données V12 peut ne plus reconnaître ou accepter la couche de service Web ou une entreprise. |



### <a name="export-and-import-after-upgrade-to-v12"></a>Exportation et importation de mise à niveau *après* à V12


Vous pouvez exporter ou importer une base de données V12 via le [Portail Azure](https://portal.azure.com/). Vous pouvez exporter ou importer à l’aide d’un des outils suivants :


- SQL Server Management Studio (SSMS)
- Visual Studio 2015
- Infrastructure d’Application de couche données (DacFx)


Toutefois, pour utiliser les outils, vous devez tout d’abord avoir ou installer les dernières mises à jour pour vous assurer qu’ils prennent en charge les nouvelles fonctionnalités de V12 :


- [Mise à jour cumulative 6 pour SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Mise à jour de février 2015 pour la base de données de SQL Server outils dans Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Infrastructure de l’Application de couche données février 2015 (DacFx) pour V12 de base de données SQL Azure](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Les liens d’outil précédentes ont été mis à jour ou après le 2 mars 2015. Nous vous conseillons d’utiliser ces mises à jour plus récentes de ces outils.


#### <a name="automated-export"></a>Exportation automatique


Exportation automatique continue d’être disponible sous forme d’aperçu.  Aucun outil mises à jour ne sont nécessaires lors de l’utilisation automatique d’exportation.  Si vous choisissez de prendre le fichier obtenu et d’importation pour un serveur sur site, les mises à jour des outils répertoriés ci-dessus sont nécessaires pour réussir l’importation.


### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Restaurer V12 une base de données supprimé V11

Le scénario suivant explique qu’une base de données SQL Azure de V11 supprimée peut être restaurée sur un serveur de base de données SQL de V12 Azure.

1. Supposons que vous ayez un serveur de base de données SQL de V11 Azure. <br/> Sur le serveur, vous avez une base de données à la couche de service Web d’entreprises obsolète.
2. Vous supprimez la base de données.
3. Vous mettez à niveau le serveur à V12.
4. Ensuite, vous restaurez la base de données sur le serveur. <br/> La base de données est ainsi mis à niveau vers V12, au niveau de la couche de service Standard S0.
5. Vous pouvez basculer la base de données vers n’importe quel niveau de service pris en charge, si S0 n’est pas votre préférence.


### <a name="powershell-cmdlets"></a>Applets de commande PowerShell


Applets de commande PowerShell sont disponibles pour démarrer, arrêter ou contrôler une mise à niveau à V12 de base de données SQL Azure V11 ou toute autre version pré-V12.

- [Mise à niveau V12 de base de données SQL à l’aide de PowerShell](sql-database-upgrade-server-powershell.md)

Pour une documentation de référence sur ces applets de commande PowerShell, voir :


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Démarrer-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


L’applet de commande Stop signifie Annuler, de ne pas attendre. Il n’y a aucune manière de reprendre une mise à niveau, que vous devez redémarrer à partir du début. L’applet de commande Stop nettoie et libère toutes les ressources appropriées.


## <a name="failure-resolution"></a>Résolution de la défaillance


Si la mise à niveau échoue pour une raison étrange, votre base de données V11 reste active et disponible comme à l’accoutumée.


## <a name="related-links"></a>Liens connexes


- Microsoft Azure, [fonctionnalités](https://azure.microsoft.com/services/preview/)


<!--Anchors-->
[Subheading 1]: #subheading-1
