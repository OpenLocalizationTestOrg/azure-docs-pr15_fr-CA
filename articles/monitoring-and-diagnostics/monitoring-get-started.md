<properties
    pageTitle="Mise en route avec le moniteur d’Azure | Microsoft Azure"
    description="Mise en route à l’aide du moniteur de Azure pour obtenir un aperçu du fonctionnement de vos ressources et de prendre des mesures sur la base de données."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-azure-monitor"></a>Mise en route avec le moniteur d’Azure

Moniteur Azure est le service de plate-forme qui fournit une source unique de surveillance des ressources Azure. Avec le moniteur d’Azure, visualiser, interroger, acheminer, archiver et effectuer une action sur les mesures et les journaux provenant des ressources dans Azure. Vous pouvez travailler avec ces données à l’aide de la lame de portail moniteur, [Les applets de commande PowerShell moniteur](./insights-powershell-samples.md), [Multiplates-formes CLI](insights-cli-samples.md)ou [Azure moniteur reste API](https://msdn.microsoft.com/library/dn931943.aspx). Dans cet article, nous guider parmi les principaux composants du moniteur d’Azure, à l’aide du portail de démonstration.

1. Dans le portail, accédez à **d’autres services** et rechercher l’option **Moniteur** . Cliquez sur l’icône en étoile pour ajouter cette option à votre liste de favoris afin qu’il soit toujours facilement accessible à partir de la barre de navigation de gauche.

    ![Contrôler dans la liste de services](./media/monitoring-get-started/monitor-more-services.png)

2. Cliquez sur l’option **Moniteur** pour ouvrir la **Moniteur** de lame. Cette blade rassemble toutes vos données et paramètres de surveillance dans une vue consolidée. Il ouvre pour la première fois à la section du **journal d’activité** .

    ![Navigation de lame de moniteur](./media/monitoring-get-started/monitor-blade-nav.png)

    > [AZURE.WARNING] Les options de **notifications de Service** et des **groupes de Notification** affichées ci-dessus apparaissent uniquement à ceux qui ont rejoint l’aperçu privé de ces fonctionnalités.

    Moniteur Azure a trois catégories de base de données d’analyse : le **journal d’activité**, les **mesures**et les **journaux de diagnostic**.

3. Cliquez sur le **journal d’activité** pour vous assurer que l’activité journal s’affiche.

    ![Lame de journal d’activité](./media/monitoring-get-started/monitor-act-log-blade.png)

    Le [**journal d’activité**](./monitoring-overview-activity-logs.md) décrit toutes les opérations effectuées sur les ressources de votre abonnement. Le journal d’activité, vous pouvez déterminer la ' quoi, qui, quand et ' pour les créer, mettre à jour ou supprimer des opérations sur les ressources de votre abonnement. Par exemple, le journal d’activité vous indique quand une application web a été arrêtée et qui l’a arrêté. Événements de journal d’activité sont stockés dans la plate-forme et disponibles pour interroger pendant 90 jours.
   
    Vous pouvez créer et enregistrer des requêtes pour les filtres communs, puis épingler les requêtes les plus importantes à un tableau de bord de portail pour que vous sachiez toujours si les événements qui correspondent à vos critères ont eu lieu.

4. Filtrer l’affichage à un groupe de ressources sur la dernière semaine, puis cliquez sur le bouton **Enregistrer** .

    ![Enregistrer la requête de journal d’activité](./media/monitoring-get-started/monitor-act-log-save.png)

5. Maintenant, cliquez sur le bouton **code confidentiel** .

    ![Cliquez sur Ajouter pour le journal d’activité](./media/monitoring-get-started/monitor-act-log-pin.png)

    La plupart des affichages dans cette procédure pas à pas peut être épinglée à un tableau de bord. Cela vous permet de créer une source unique d’informations des données opérationnelles sur les services. 

6. Revenir à votre tableau de bord. Vous pouvez maintenant voir que la requête (et le nombre de résultats) s’affiche dans votre tableau de bord. Cela est utile si vous souhaitez voir rapidement toutes les actions de grande ampleur qui sont sont produits récemment dans votre abonnement, par exemple. un nouveau rôle a été attribué ou un ordinateur virtuel a été supprimé.

    ![Journal d’activité est épinglé au tableau de bord](./media/monitoring-get-started/monitor-act-log-db.png)

7. Revenir à la mosaïque de **Moniteur** , puis cliquez sur la section **mesures** . Vous devez d’abord sélectionner une ressource par le filtrage et la sélection à l’aide des options de liste déroulante en haut de la lame.

    ![Filtrer les ressources pour les mesures](./media/monitoring-get-started/monitor-met-filter.png)

    [**Les mesures de**](./monitoring-overview-metrics.md)l’émission de toutes les ressources d’Azure. Cette vue réunit toutes les mesures dans un seul volet de verre afin de comprendre facilement comment effectuez vos ressources.

8. Une fois que vous avez sélectionné une ressource, tous les paramètres disponibles s’affichent sur le côté gauche de la lame. Vous pouvez plusieurs métriques du graphique à la fois en sélectionnant des métriques et modifier la graphique type et la plage horaire. Vous pouvez également afficher toutes les alertes de métriques sur cette ressource.

    ![Lame de métrique](./media/monitoring-get-started/monitor-metric-blade.png)

    > [AZURE.NOTE] Certaines mesures ne sont disponibles qu’en activant les [Perspectives d’Application](../application-insights/app-insights-overview.md) et/ou Windows ou Linux Azure Diagnostics sur vos ressources.

9. Lorsque vous êtes satisfait de votre graphique, vous pouvez utiliser le bouton **code confidentiel** pour épingler à votre tableau de bord.

10. Revenir à la lame de **Moniteur** , puis cliquez sur **journaux de Diagnostic**.

    ![Lame de journaux de diagnostic](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Journaux de diagnostic**](monitoring-overview-of-diagnostic-logs.md) sont journaux émises *par* une ressource qui fournissent des informations sur le fonctionnement de cette ressource particulière. Par exemple, les compteurs de règle de groupe de sécurité réseau et les journaux de flux de travail d’application logique sont les deux types de journaux de diagnostic. Ces journaux peut être stockées dans un compte de stockage, à un concentrateur d’événements et/ou envoyé au [Journal Analytique](../log-analytics/log-analytics-overview.md). Analytique de journal est produit de renseignements opérationnels de Microsoft pour la recherche avancée et les alertes.
   
    Dans le portail, vous pouvez afficher et filtrer une liste de toutes les ressources de votre abonnement à identifier s’ils ont activé les journaux de diagnostic.

11. Cliquez sur une ressource dans la lame de journaux de diagnostic. Si les journaux de diagnostic sont stockés dans un compte de stockage, vous verrez une liste de journaux horaires que vous pouvez télécharger directement.

    ![Journaux de diagnostic pour une ressource](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    Vous pouvez également cliquer sur **Les paramètres de Diagnostic**, qui vous permet de définir ou modifier vos paramètres pour archivage à un compte de stockage, de diffusion en continu à des concentrateurs d’événement, ou l’envoi d’un espace de travail du journal Analytique.

    ![Activer les journaux de diagnostic](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Si vous avez configuré les journaux de diagnostic au journal Analytique, vous pouvez les rechercher dans la section de **recherche** du portail.

12. Accédez à la section **alertes** de la lame du moniteur.

    ![lame d’alertes public](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Ici, vous pouvez gérer toutes les [**alertes**](./monitoring-overview-alerts.md) sur vos ressources Azure. Cela inclut les alertes sur les mesures, événements journal d’activité (en aperçu privée), les tests web Application perspectives (emplacements), diagnostics proactifs de perspectives de l’Application. Alertes peuvent déclencher un e-mail doit être envoyé ou un HTTP POST à une URL webhook.
   
13. Cliquez sur **Ajouter une alerte métrique** pour créer une alerte.

    ![Ajouter une alerte métrique](./media/monitoring-get-started/monitor-alerts-add.png)

    Vous pouvez ensuite épingler une alerte à votre tableau de bord pour voir facilement son état à tout moment.

14. La section Moniteur comprend également des liens vers des [Perspectives d’Application](../application-insights/app-insights-overview.md) applications et solutions de gestion [Analytique du journal](../log-analytics/log-analytics-overview.md) . Ces autres produits Microsoft ont une forte intégration avec moniteur d’Azure.

15. Si vous n’utilisez pas les perspectives d’Application ou journal Analytique, sans doute que Azure moniteur dispose d’un partenariat avec votre analyse, l’enregistrement en cours et les produits d’alerte. Consultez notre [page de partenaires](./monitoring-partners.md) pour obtenir une liste complète et des instructions sur la façon d’intégrer.

En suivant ces étapes et l’épinglage toutes les tuiles pertinentes pour un tableau de bord, vous pouvez créer des vues complètes de l’application et l’infrastructure de ce type :

![Tableau de bord de moniteur Azure](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Étapes suivantes
- Lisez la [vue d’ensemble du moniteur d’Azure](./monitoring-overview.md)
