<properties
 pageTitle="Mise en route avec le planificateur d’Azure dans Azure portal | Microsoft Azure"
 description="Mise en route avec le planificateur d’Azure dans Azure portal"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Mise en route avec le planificateur d’Azure dans Azure portal

Il est facile de créer des tâches planifiées dans le planificateur d’Azure. Dans ce didacticiel, vous allez apprendre à créer un travail. Vous allez également découvrir les fonctions de surveillance et de gestion du planificateur.

## <a name="create-a-job"></a>Créer une tâche

1.  Connectez-vous au [portail Azure](https://portal.azure.com/).  

2.  Cliquez sur **+ Nouveau** > tapez _Planificateur_ dans la zone de recherche > sélectionnez **Planificateur** dans résultats > cliquez sur **créer**.

     ![][marketplace-create]

3.  Nous allons créer une tâche qui accède simplement à http://www.microsoft.com/ avec une requête GET. Dans l’écran **Planificateur de travail** , entrez les informations suivantes :

    1.  **Nom :**`getmicrosoft`  

    2.  **Abonnement :** Votre abonnement Azure   

    3.  **La tâche de Collection :** Sélectionnez une collection existante de la tâche, ou cliquez sur **Créer nouveau** > entrer un nom.

4.  Ensuite, dans **Paramètres des actions**, définissez les valeurs suivantes :

    1.  **Type d’action :**` HTTP`  

    2.  **Méthode :**`GET`  

    3.  **URL :**` http://www.microsoft.com`  

      ![][action-settings]

5.  Enfin, nous allons définir d’une planification. Le travail peut être défini comme une tâche unique, mais nous allons choisir une fréquence de répétition :

    1. **Périodicité**:`Recurring`

    2. **Début**: date du jour

    3. **Se répète chaque**:`12 Hours`

    4. **Fin**: date du de deux jours à dater d’aujourd'hui  

      ![][recurrence-schedule]

6.  Cliquez sur **créer**

## <a name="manage-and-monitor-jobs"></a>Gérer et surveiller les travaux

Une fois qu’une tâche est créée, elle s’affiche dans le tableau de bord Azure principal. Cliquez sur la tâche, et une nouvelle fenêtre s’ouvre avec les onglets suivants :

1.  Propriétés  

2.  Paramètres des actions  

3.  Planification  

4.  Historique

5.  Utilisateurs

    ![][job-overview]

### <a name="properties"></a>Propriétés

Ces propriétés en lecture seule décrivent les métadonnées de gestion pour la tâche du planificateur.

   ![][job-properties]


### <a name="action-settings"></a>Paramètres des actions

En cliquant sur une tâche dans l’écran **tâches** vous permet de configurer cette tâche. Cela vous permet de configurer des paramètres avancés, si vous n’avez pas les configurer dans l’Assistant de création rapide.

Tous les types d’actions, vous pouvez modifier la stratégie de nouvelle tentative et l’action sur erreur.

Pour les types d’action de la tâche HTTP et HTTPS, vous pouvez modifier la méthode pour tous les verbes HTTP autorisés. Vous pouvez également ajouter, supprimer ou modifier les en-têtes et les informations d’authentification de base.

Stockage file d’attente des types d’actions, vous pouvez modifier le compte de stockage, nom de la file d’attente, jeton SAS et corps.

Service bus des types d’actions, vous pouvez modifier l’espace de noms, rubrique/chemin d’accès, les paramètres d’authentification, type de transport, les propriétés de message et le corps du message.

   ![][job-action-settings]

### <a name="schedule"></a>Planification

Cela vous permet de reconfigurer la planification, si vous souhaitez modifier la planification que vous avez créé dans l’Assistant de création rapide.

Il s’agit d’une opportunité de créer des [planifications complexes et périodicité avancée dans votre travail](scheduler-advanced-complexity.md)

Vous pouvez modifier la date de début et la fin, heure et fréquence de répétition de date et l’heure (si la tâche est périodique.)

   ![][job-schedule]


### <a name="history"></a>Historique

L’onglet **historique** affiche les mesures sélectionnées pour chaque exécution de la tâche dans le système pour la tâche sélectionnée. Ces mesures fournissent les valeurs relatives à la santé de votre planificateur en temps réel :

1.  État  

2.  Détails  

3.  Nouvelles tentatives

4.  Occurrence : 1er, 2e, 3e, etc..

5.  Heure de début de l’exécution  

6.  Heure de fin de l’exécution

   ![][job-history]

Vous pouvez cliquer sur une série de tests pour afficher les **Détails de l’historique**, y compris la réponse entière à chaque exécution. Cette boîte de dialogue vous permet également de copier la réponse dans le Presse-papiers.

   ![][job-history-details]

### <a name="users"></a>Utilisateurs

Azure contrôle d’accès basée sur les rôles (RBAC) permet la gestion d’accès à granularité fine pour Azure planificateur. Pour apprendre à utiliser l’onglet utilisateurs, consultez [Contrôle d’accès Azure Role-Based](../active-directory/role-based-access-control-configure.md)


## <a name="see-also"></a>Voir aussi

 [Quel est le planificateur ?](scheduler-intro.md)

 [Hiérarchie d’entités, terminologie et les concepts du planificateur](scheduler-concepts-terms.md)

 [Plans et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et périodicité avancée avec le planificateur d’Azure](scheduler-advanced-complexity.md)

 [Référence des API REST de planificateur](https://msdn.microsoft.com/library/mt629143)

 [Les applets de commande PowerShell Planificateur de référence](scheduler-powershell-reference.md)

 [Planificateur de haute disponibilité et de fiabilité](scheduler-high-availability-reliability.md)

 [Limites du planificateur, les valeurs par défaut et les codes d’erreur](scheduler-limits-defaults-errors.md)

 [Authentification sortante du planificateur](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
