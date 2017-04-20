<properties
    pageTitle="Surveiller et de gérer un pool élastique de la base de données avec le portail Azure | Microsoft Azure"
    description="Apprenez à utiliser le portail Azure et une intelligence intégrée de SQL base de données pour gérer, surveiller et bonne taille un pool élastique évolutive de la base de données pour optimiser les performances de la base de données et de gérer les coûts."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/22/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Surveiller et de gérer un pool élastique de la base de données avec le portail Azure

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Le portail Azure vous permet de surveiller et de gérer un pool élastique de la base de données et les bases de données dans le pool. À partir du portail, vous pouvez surveiller l’utilisation d’un pool élastique et les bases de données dans ce pool. Vous pouvez également créer un ensemble de modifications à votre pool élastique et soumettre toutes les modifications en même temps. Ces modifications comprennent l’ajout ou suppression bases de données, modifier les paramètres de pool élastique ou modification de vos paramètres de base de données.

L’illustration ci-dessous montre un pool élastique d’exemple. L’affichage inclut :

*  Graphiques pour l’analyse de l’utilisation des ressources de la grappe élastique et les bases de données contenues dans le pool.
*  Le bouton **configurer** pool pour apporter des modifications au pool élastique.
*  Le bouton **créer base de données** qui crée une nouvelle base de données et l’ajoute au pool élastique en cours.
*  Travaux élastiques qui vous aident à gérer un grand nombre de bases de données en exécutant les scripts SQL de Transact sur toutes les bases de données dans une liste.

![Affichage du pool][2]

Pour utiliser les étapes de cet article, vous aurez besoin d’un serveur SQL Azure avec au moins une base de données et un pool élastique. Si vous ne disposez pas d’un pool élastique, reportez-vous à la section [Création d’un pool](sql-database-elastic-pool-create-portal.md); Si vous n’avez pas une base de données, consultez le [didacticiel de base de données SQL](sql-database-get-started.md).

## <a name="elastic-pool-monitoring"></a>Surveillance du pool élastique

Vous pouvez accéder à un pool spécifique pour voir son utilisation des ressources. Par défaut, le pool est configuré pour afficher l’utilisation d’eDTU et de stockage pendant la dernière heure. Le graphique peut être configuré pour afficher les différentes mesures sur différentes périodes.

1. Sélectionnez un regroupement à utiliser.
2. Sous **Surveillance de Pool élastique** est un graphique intitulé **utilisation des ressources**. Cliquez sur le graphique.

    ![Surveillance du pool élastique][3]

    La lame de **mesure** s’ouvre, affichant une vue détaillée des mesures spécifiées sur la fenêtre de temps spécifiée.   

    ![Lame de métrique][9]

### <a name="to-customize-the-chart-display"></a>Pour personnaliser l’affichage du graphique

Vous pouvez modifier le graphique et la lame de métriques pour afficher d’autres mesures de pourcentage processeur, pourcentage d’e/s de données et de pourcentage d’e/s de journal utilisé.

2. Sur la lame de mesure, cliquez sur **Modifier**.

    ![Cliquez sur Modifier][6]

- De la lame de **Modifier un graphique** , sélectionnez une nouvelle plage de temps (heure, aujourd'hui, ou semaine dernière), ou cliquez sur **personnalisé** pour sélectionner une plage de dates au cours des deux dernières semaines. Sélectionnez le type de graphique (barres ou linéaire), puis sélectionnez les ressources à surveiller.

> Remarque : Uniquement les métriques avec la même unité de mesure peuvent être affichés dans le graphique en même temps. Par exemple, si vous sélectionnez « eDTU pourcentage » puis vous uniquement sera en mesure de sélectionner d’autres mesures avec pourcentage comme unité de mesure.

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Puis cliquez sur **OK**.


## <a name="elastic-database-monitoring"></a>Surveillance de l’élasticité de la base de données

Bases de données individuelles peuvent être également surveillées pour des problèmes potentiels.

1. Sous **Surveillance élastique de bases de données**, il existe un graphique qui affiche les mesures de cinq bases de données. Par défaut, le graphique affiche les 5 principales bases de données dans le pool par l’utilisation d’une eDTU moyenne la dernière heure. Cliquez sur le graphique.

    ![Surveillance du pool élastique][4]

2. La lame de **L’utilisation des ressources de base de données** s’affiche. Cela fournit une vue détaillée de l’utilisation de la base de données dans le pool. À l’aide de la grille dans la partie inférieure de la lame, vous pouvez sélectionner les bases de données dans le pool pour afficher son utilisation dans le graphique (jusqu'à 5 bases de données). Vous pouvez également personnaliser la fenêtre de mesures et l’heure affichée dans le graphique en cliquant sur **Modifier le graphique**.

    ![Lame de l’utilisation des ressources de base de données][8]

### <a name="to-customize-the-view"></a>Pour personnaliser l’affichage

1. De la lame de **l’utilisation des ressources de base de données** , cliquez sur **Modifier le graphique**.

    ![Cliquez sur le graphique de modification](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. De la lame de graphique **Modifier** , sélectionnez une nouvelle plage de temps (après heure ou dernières 24 heures), ou cliquez sur **personnalisé** pour sélectionner un jour différent au cours des 2 dernières semaines à afficher.

    ![Cliquez sur personnalisé](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Cliquez sur la liste déroulante de **comparer des bases de données par** pour sélectionner une autre mesure à utiliser lors de la comparaison de bases de données.

    ![Modifier le graphique](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Pour sélectionner des bases de données à surveiller

Dans la liste de la base de données de la lame de **L’utilisation des ressources de base de données** , vous pouvez trouver des bases de données particuliers en consultant les pages de la liste ou en tapant le nom d’une base de données. Utilisez la case à cocher pour sélectionner la base de données.

![Recherche des bases de données à surveiller][7]


## <a name="add-an-alert-to-a-pool-resource"></a>Ajouter une alerte à une ressource de pool

Vous pouvez ajouter des règles aux ressources qui envoient un e-mail à des personnes ou des chaînes d’alerte aux points de terminaison URL lorsque la ressource atteint un seuil d’utilisation que vous définissez.

> [AZURE.IMPORTANT]L’utilisation des ressources pour les Pools élastique de surveillance a un retard de 20 minutes au moins. Définition d’alertes de moins de 30 minutes pour les Pools élastique n’est actuellement pas pris en charge. Toutes les alertes définies pour les Pools élastique avec une période (paramètre appelé «-WindowSize » dans les API PowerShell) de moins de 30 minutes ne peut pas être déclenché. Veuillez vous assurer que les alertes que vous définissez pour les Pools élastique utilisent une période (WindowSize) de 30 minutes ou plus.

**Pour ajouter une alerte à n’importe quelle ressource :**

1. Cliquez sur le graphique de **l’utilisation des ressources** pour ouvrir la lame de **mesure** , cliquez sur **Ajouter une alerte**, puis remplissez les informations de la lame **d’Ajouter une règle d’alerte** (**ressource** est automatiquement configuré pour être le pool avec lequel vous travaillez).
2. Tapez un **nom** et une **Description** qui identifie l’alerte pour vous et les destinataires.
3. Choisissez une **mesure** que vous souhaitez dans la liste des alertes.

    Le graphique affiche dynamiquement l’utilisation des ressources pour cette mesure afin de vous aider à choisir un seuil.

4. Choisissez une **Condition** (supérieur à, inférieur à, etc.) et un **seuil**.
5. Cliquez sur **OK**.



## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique

Vous pouvez ajouter ou supprimer des bases de données à partir d’un pool existant. Les bases de données peuvent être dans d’autres pools. Toutefois, vous ne pouvez ajouter que des bases de données qui se trouvent sur le même serveur logique.

1. Dans la lame pour le pool, cliquez sur **configurer pool**sous **bases de données élastiques** .

    ![Cliquez sur Configurer un pool][1]

2. Dans la lame de **pool de configuration** , cliquez sur **Ajouter à la liste**.

    ![Cliquez sur Ajouter à un pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. Dans la lame de **l’Ajout de bases de données** , sélectionnez la base de données ou les bases de données à ajouter à la liste. Puis cliquez sur **Sélectionner**.

    ![Sélectionnez les bases de données à ajouter](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    La lame de **pool de configuration** répertorie désormais la base de données que vous avez choisi d’ajouter, avec son statut **en attente**.

    ![Ajouts du pool en attente](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. De la **lame de pool de configuration**, cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Déplacer une base de données sur un pool élastique

1. Dans la lame de **pool de configurer** , sélectionnez la base de données ou les bases de données à supprimer.

    ![liste des bases de données](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Cliquez sur **Supprimer de la liste**.

    ![liste des bases de données](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    La lame de **pool de configuration** répertorie désormais la base de données que vous avez choisi de supprimer son statut que le statut **en attente**.

    ![Ajout de base de données d’aperçu et la suppression](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. De la **lame de pool de configuration**, cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>Modifier les paramètres de performance d’un pool

Lorsque vous analysez l’utilisation des ressources d’un pool, vous pouvez constater que certains ajustements sont nécessaires. Peut-être que le pool doit être modifiée dans les limites de stockage ou des performances. Vous souhaitez probablement modifier les paramètres de base de données dans le pool. Vous pouvez modifier le paramétrage du pool à tout moment pour obtenir le meilleur compromis entre performances et coût. Consultez [quand un pool élastique de la base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md) pour plus d’informations.

**Pour modifier les limites de stockage ou eDTUs par pool et eDTUs par base de données :**

1. Ouvrez la lame de **pool de configurer** .

    Sous **paramètres du pool d’élasticité de la base de données**, utilisez un curseur pour modifier les paramètres du pool.

    ![Utilisation des ressources pool élastique](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Lorsque la valeur change, l’affichage indique le coût mensuel estimé de la modification.

    ![Mise à jour d’un pool et le nouveau coût mensuel](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>Créer et gérer des tâches élastiques

Travaux élastiques vous permettent d’exécuter les scripts Transact-SQL par rapport à n’importe quel nombre de bases de données dans le pool. Vous pouvez créer de nouvelles tâches ou gérer des tâches existantes à l’aide du portail.

![Créer et gérer des tâches élastiques][5]


Avant d’utiliser les travaux, installer les composants de tâches élastique et fournir vos informations d’identification. Pour plus d’informations, consultez [vue d’ensemble des tâches élastique de la base de données](sql-database-elastic-jobs-overview.md).

Reportez-vous à la section [mise à l’échelle des base de données de SQL Azure](sql-database-elastic-scale-introduction.md): outils de base de données élastique permet d’horizontale, déplacer des données, de requête ou de créer des transactions.



## <a name="additional-resources"></a>Ressources supplémentaires

- [Pool d’élastique de la base de données SQL](sql-database-elastic-pool.md)
- [Créer un pool d’élasticité de la base de données avec le portail](sql-database-elastic-pool-create-csharp.md)
- [Créer un pool de la base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Créer un pool de la base de données élastique avec C#](sql-database-elastic-pool-create-csharp.md)
- [Considérations de prix et de performances pour les pools d’élasticité de la base de données](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png
