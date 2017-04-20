<properties
   pageTitle="Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (Azure portal) | Microsoft Azure"
   description="Tâches du portail Azure pour gérer grande puissance de traitement. Échelle des ressources de calcul en ajustant les DWUs. Ou bien, suspendre et reprendre les ressources pour réduire les coûts de calcul."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/22/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Gérer la puissance de calcul dans l’entrepôt de données SQL Azure (Azure portal)

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-data-warehouse-manage-compute-overview.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Adapter les performances en faisant évoluer calculer des ressources et mémoire pour répondre aux besoins changeants de votre charge de travail. Économies en ressources d’arrière-plan mise à l’échelle pendant les heures creuses ou compute suspension complètement. 

Cet ensemble de tâches utilise le portail Azure pour :

- Calcul de l’échelle
- Calcul de pause
- Calcul de la reprise

Pour plus d’informations, consultez [Gérer compute vue d’ensemble][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Puissance de calcul d’échelle

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier des ressources de calcul :

1. Ouvrir le [portail Azure][], ouvrez votre base de données, cliquez sur **échelle**.

    ![Cliquez sur échelle][1]

1. Dans la blade d’échelle, déplacez le curseur de gauche ou de droite pour modifier le paramètre DWU.

    ![Déplacer le curseur][2]

1. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou **Aucun** pour annuler.

    ![Cliquez sur Enregistrer][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Calcul de pause

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour mettre en pause une base de données :

1. Ouvrir le [portail Azure][] et ouvrez votre base de données. Notez que le statut est **en ligne**. 

    ![État en ligne][6]

1. Pour suspendre les ressources de calcul et de mémoire, cliquez sur **Pause**, puis un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou **Aucun** pour annuler.

    ![Confirmer la pause][7]

1. Pendant le démarrage de la base de données SQL Data Warehouse, l’état est **interruption**.
2. Lorsque le statut est **en pause**, l’opération de pause et vous ne sont plus facturées pour DWUs.

    ![État de pause][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Calcul de la reprise

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]Pour reprendre une base de données :

1. Ouvrir le [portail Azure][] et ouvrez votre base de données. Notez que l’état est **suspendu**. 

    ![Base de données de pause][4]

1. Pour reprendre la base de données cliquez sur **Démarrer**et un message de confirmation s’affiche alors. Cliquez sur **Oui** pour confirmer ou **Aucun** pour annuler.

    ![Confirmer la reprise][5]

1. Pendant le démarrage de la base de données SQL Data Warehouse, le statut est « Relance ».
2. Lorsque le statut est **en ligne**, la base de données est prêt.

    ![État en ligne][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [vue d’ensemble de la gestion][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Vue d’ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[Gérer la vue d’ensemble du calcul]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
