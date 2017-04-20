<properties
    pageTitle="Solution d’évaluation de la mise à jour système dans le journal Analytique | Microsoft Azure"
    description="Vous pouvez utiliser la solution de mises à jour système dans le journal Analytique pour vous aider à appliquer les mises à jour manquantes pour les serveurs de votre infrastructure."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="system-update-assessment-solution-in-log-analytics"></a>Solution d’évaluation de la mise à jour système dans le journal Analytique

Vous pouvez utiliser la solution de mises à jour système dans le journal Analytique pour vous aider à appliquer les mises à jour manquantes pour les serveurs de votre infrastructure. Après l’installation de la solution, vous pouvez afficher les mises à jour manquantes à partir de vos serveurs surveillés à l’aide de la mosaïque de **l’Évaluation du système de mise à jour** sur la page de **vue d’ensemble** de l’OMS.

Si les mises à jour manquantes sont trouvées, les détails sont affichés sur le tableau de bord de **mises à jour** . Vous pouvez utiliser le tableau de bord de **mises à jour** pour fonctionner avec les mises à jour manquantes et de développer un plan pour les appliquer aux serveurs qui en ont besoin.

## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Ajouter la solution d’évaluation de mise à jour du système pour votre espace de travail de l’OMS à l’aide de la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’y a aucune configuration supplémentaire requise.

## <a name="system-update-data-collection-details"></a>Détails de collecte de données de mise à jour système

Évaluation de mise à jour du système collecte les données d’état et de métadonnées à l’aide d’agents que vous avez activés.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur le mode de collecte de données pour l’évaluation du système de mise à jour.

| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-system-update/oms-bullet-green.png)|![Oui](./media/log-analytics-system-update/oms-bullet-green.png)|![N°](./media/log-analytics-system-update/oms-bullet-red.png)|            ![N°](./media/log-analytics-system-update/oms-bullet-red.png)|![Oui](./media/log-analytics-system-update/oms-bullet-green.png)| Au moins 2 fois par jour et 15 minutes après l’installation d’une mise à jour|

Le tableau suivant montre des exemples de types de données collectées par l’évaluation du système de mise à jour :

|**Type de données**|**Champs**|
|---|---|
|Métadonnées|BaseManagedEntityId, ObjectStatus, unité d’organisation, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, Nom_ordinateur_netbios, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, adresse IP, NetbiosDomainName, LogicalProcessors, NomDNS, DisplayName, DomainDnsName, ActiveDirectorySite, au PrincipalName, OffsetInMinuteFromGreenwichTime|
|État|StateChangeEventId, StateId, NewHealthState, OldHealthState, contexte, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|


### <a name="to-work-with-updates"></a>Pour fonctionner avec les mises à jour

1. Dans la page **vue d’ensemble** , cliquez sur la mosaïque de **l’Évaluation du système de mise à jour** .  
    ![Mosaïque d’évaluation de la mise à jour système](./media/log-analytics-system-update/sys-update-tile.png)
2. Sur le tableau de bord de **mises à jour** , afficher les catégories de mise à jour.  
    ![Tableau de bord de mises à jour](./media/log-analytics-system-update/sys-updates02.png)
3. Défiler vers la droite de la page pour afficher la lame de **Mises à jour critiques et de sécurité de Windows** et de **Classification**, cliquez sur **Mises à jour de sécurité**.  
    ![Mises à jour de sécurité](./media/log-analytics-system-update/sys-updates03.png)
4. Sur la page de recherche de journal, une variété d’informations est affichée sur les mises à jour de sécurité qui ont été trouvés dans votre infrastructure manquants à partir de serveurs. Cliquez sur **liste** pour afficher des informations détaillées sur les mises à jour.  
    ![Résultats de la recherche - liste](./media/log-analytics-system-update/sys-updates04.png)
5. Sur la page de recherche des journaux, des informations détaillées sur chaque mise à jour sont affichées. À côté du numéro KBID, cliquez sur **Afficher** pour afficher l’article correspondant sur le site Web de support technique de Microsoft.  
    ![Enregistrer les résultats de la recherche - vue Ko](./media/log-analytics-system-update/sys-updates05.png)
6. Votre navigateur web ouvre la page Web de support technique de Microsoft pour la mise à jour dans un nouvel onglet. Permet d’afficher les informations relatives à la mise à jour est manquant.  
    ![Page web de Support de Microsoft](./media/log-analytics-system-update/sys-updates06.png)
7. À l’aide de l’en utilisant les informations que vous avez trouvé, vous pouvez créer un plan pour appliquer manuellement la mise à jour manquante, ou vous pouvez continuer procédez comme suit pour appliquer automatiquement la mise à jour.
8. Si vous souhaitez appliquer automatiquement la mise à jour manquante, revenez au tableau de bord **mises à jour** et puis, sous **Mettre à jour des séries**, cliquez sur **Cliquez ici pour planifier une mise à jour à exécuter**.  
    ![S’exécute à la mise à jour - onglet planifiée](./media/log-analytics-system-update/sys-updates07.png)
9. Dans la page de **Mise à jour de l’exécution** dans l’onglet **planification** , cliquez sur **Ajouter** pour créer une nouvelle exécution de la mise à jour.  
    ![Planifiée onglet - Ajouter](./media/log-analytics-system-update/sys-updates08.png)
10. Dans la page **Nouveau exécuter de mise à jour** , tapez un nom pour la mise à jour run, ajouter des ordinateurs individuels ou des groupes d’ordinateurs, définir une planification, puis cliquez sur **Enregistrer**.  
    ![Nouvelle exécution de la mise à jour](./media/log-analytics-system-update/sys-updates09.png)
11. L’onglet **planification** , sur la page de **Mise à jour des séries** indique la nouvelle mise à jour exécutée qui vous avez planifié.  
    ![Onglet planifiée](./media/log-analytics-system-update/sys-updates10.png)
12. La mise à jour à exécuter au démarrage, vous verrez des données sous l’onglet **en cours d’exécution** .  
    ![Onglet en cours d’exécution](./media/log-analytics-system-update/sys-updates11.png)
13. Après la mise à jour à exécuter, l’onglet **terminé** affiche état.
14. Si les mises à jour ont été appliquées à partir de la mise à jour à exécuter, dans la lame de **Mises à jour critiques et de sécurité** , vous verrez que le nombre de mises à jour est réduit.  
    ![Lame de mises à jour critiques et de sécurité de Windows - nombre de mise à jour réduit](./media/log-analytics-system-update/sys-updates12.png)



## <a name="next-steps"></a>Étapes suivantes

- [Journaux de recherche](log-analytics-log-searches.md) pour afficher les données de mise à jour système détaillées.
