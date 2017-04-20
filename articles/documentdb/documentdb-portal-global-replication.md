<properties
    pageTitle="Réplication de base de données global DocumentDB | Microsoft Azure"
    description="Découvrez comment gérer la réplication globale de votre compte de DocumentDB via le portail Azure."
    services="documentdb"
    keywords="base de données global, réplication"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Comment faire pour effectuer une réplication globale de la base de données DocumentDB en utilisant le portail Azure

Apprenez à utiliser le portail Azure pour répliquer les données dans plusieurs régions pour une disponibilité globale de données Azure DocumentDB.

Pour plus d’informations sur comment global de la base de données, la réplication fonctionne dans DocumentDB, reportez-vous à [répartir les données globalement avec DocumentDB](documentdb-distribute-data-globally.md). Pour plus d’informations sur l’exécution d’une réplication globale de la base de données par programme, consultez [développement avec comptes de DocumentDB de plusieurs régions](documentdb-developing-with-multiple-regions.md).

> [AZURE.NOTE] La distribution mondiale des bases de données DocumentDB est disponible et activée automatiquement pour tous les comptes DocumentDB nouvellement créés. Nous nous efforçons d’activer la distribution globale sur tous les comptes existants, mais en attendant, si vous souhaitez que la distribution globale activée sur votre compte, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et nous vous activer pour vous maintenant.

## <a id="addregion"></a>Ajouter des zones de la base de données globale

DocumentDB est disponible dans la plupart des [régions Azure] [azureregions]. Après avoir sélectionné le niveau de cohérence par défaut de votre compte de base de données, vous pouvez associer une ou plusieurs régions (selon votre choix de besoins de distribution global et au niveau de cohérence par défaut).

1. Dans le [portail Azure](https://portal.azure.com/), dans le Jumpbar, cliquez sur **Comptes de DocumentDB**.
2. Dans la lame **DocumentDB compte** , sélectionnez le compte de base de données à modifier.
3. De la lame de compte, cliquez sur **répliquer les données globalement** dans le menu.
4. Dans la lame de **répliquer les données de manière globale** , sélectionnez les zones à ajouter ou à supprimer et puis cliquez sur **Enregistrer**. Il existe un coût à l’ajout de régions, voir la [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/) ou l’article de [répartir les données globalement avec DocumentDB](documentdb-distribute-data-globally.md) pour plus d’informations.

    ![Cliquez sur les régions de la carte pour les ajouter ou les supprimer][1]

### <a name="selecting-global-database-regions"></a>Sélection des régions de la base de données globale

Lors de la configuration de plusieurs régions, il est recommandé que les régions sont sélectionnées selon les paires de la région décrites dans le [Business continuité d’activité et reprise après sinistre (BCDR) : régions associés d’Azure]  [ bcdr] l’article.

En particulier, lors de la configuration de plusieurs régions, veillez à sélectionner le même nombre de régions (+/-1 par paire/impaire) à partir de chacune des colonnes région appariés. Par exemple, si vous souhaitez déployer sur quatre régions aux États-Unis, vous devez sélectionner deux régions américaines à partir de la colonne de gauche et de deux à partir de la droite. Ainsi, le suivant est un jeu approprié : ouest des États-Unis, les États-Unis, Nord centrale nous et Sud.

Ce guide est important à ne suivre que deux zones sont configurés pour les scénarios de récupération d’urgence. Pour plus de deux régions, à la suite de ce guide est recommandé, mais pas critique dans la mesure où certaines régions sélectionnés adhèrent à cette association.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Étapes suivantes

Découvrez comment gérer la cohérence de votre compte global répliqué en lisant les [niveaux la cohérence dans DocumentDB](documentdb-consistency-levels.md).

Pour plus d’informations sur comment global de la base de données, la réplication fonctionne dans DocumentDB, reportez-vous à [répartir les données globalement avec DocumentDB](documentdb-distribute-data-globally.md). Pour plus d’informations sur la réplication par programme les données dans plusieurs régions, consultez [développement avec comptes de DocumentDB de plusieurs régions](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/
