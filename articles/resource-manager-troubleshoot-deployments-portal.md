<properties
   pageTitle="Afficher les opérations de déploiement avec le portail | Microsoft Azure"
   description="Décrit comment utiliser le portail Azure pour détecter les erreurs de déploiement du Gestionnaire de ressources."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-portal"></a>Afficher les opérations de déploiement avec Azure Portal

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Vous pouvez afficher les opérations pour un déploiement via le portail Azure. Vous serez peut-être plus intéressé par les opérations lorsque vous avez reçu une erreur pendant le déploiement afin que cet article se concentre sur l’affichage des opérations qui ont échoué. Le portail fournit une interface qui vous permet de facilement trouver les erreurs et de déterminer les éventuels correctifs.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="use-deployment-operations-to-troubleshoot"></a>Les opérations de déploiement permet de résoudre les problèmes

Pour visualiser les opérations de déploiement, procédez comme suit :

1. Pour le groupe de ressources impliqué dans le déploiement, notez l’état du dernier déploiement. Vous pouvez sélectionner ce statut pour obtenir plus de détails.

    ![état du déploiement](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Vous verrez l’historique récent de déploiement. Sélectionnez le déploiement qui a échoué.

    ![état du déploiement](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Sélectionnez **a échoué. Pour plus d’informations, cliquez ici** pour obtenir une description de la raison de l’échec du déploiement. Dans l’image ci-dessous, l’enregistrement DNS n’est pas unique.  

    ![afficher l’échec du déploiement](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    Ce message d’erreur doit être suffisant pour pouvoir commencer le dépannage. Toutefois, si vous avez besoin de plus d’informations sur les tâches qui ont été accomplies, vous pouvez afficher les opérations comme indiqué dans les étapes suivantes.

4. Vous pouvez afficher toutes les opérations de déploiement dans la lame de **déploiement** . Sélectionnez n’importe quelle opération pour plus de détails.

    ![afficher les opérations](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    Dans ce cas, vous voyez que le compte de stockage, réseau virtuel et ensemble de disponibilité ont été créées. Échec de l’adresse IP publique et autres ressources n’ont pas été tentées.

5. Vous pouvez afficher les événements pour le déploiement en sélectionnant les **événements**.

    ![afficher les événements](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. Vous consultez tous les événements pour le déploiement et que vous sélectionnez une pour plus de détails.

    ![Voir événements](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## <a name="use-audit-logs-to-troubleshoot"></a>Utilisation des journaux d’audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs pour le déploiement, procédez comme suit :

1. Permet d’afficher les journaux d’audit pour un groupe de ressources en sélectionnant les **Journaux d’Audit**.

    ![Sélectionnez les journaux d’audit](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. Dans la lame de **Journaux d’Audit** , vous verrez un résumé des opérations récentes de tous les groupes de ressources dans votre abonnement. Il comprend une représentation graphique du temps et l’état des opérations, ainsi que la liste des opérations.

    ![afficher les actions](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Vous pouvez filtrer l’affichage des journaux d’audit pour vous concentrer sur des conditions spécifiques. Sélectionnez le **filtre** en haut de la lame de **journaux d’Audit** .

    ![journaux du filtre](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. À partir de la blade de **filtre** , sélectionnez les conditions pour restreindre l’affichage des journaux d’audit à uniquement les opérations que vous souhaitez afficher. Par exemple, vous pouvez filtrer les opérations pour afficher uniquement les erreurs pour le groupe de ressources.

    ![définir les options de filtre](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. Vous pouvez filtrer davantage les opérations en définissant un intervalle de temps. L’image suivant filtre la vue à un timespan particulier de 20 minutes.

    ![définition de l’heure](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. Vous pouvez sélectionner une des opérations dans la liste. Choisissez l’opération qui contient l’erreur que vous souhaitez rechercher.

    ![Sélectionnez l’opération](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. Vous verrez tous les événements pour cette opération. Notez l' **ID de corrélation** dans le résumé. Cet ID est utilisé pour effectuer le suivi des événements connexes. Il peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème. Vous pouvez sélectionner un des événements pour voir les détails de l’événement.

    ![Sélectionnez l’événement](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. Vous verrez plus d’informations sur l’événement. En particulier, faites attention aux **Propriétés** pour plus d’informations sur l’erreur.

    ![afficher les détails du journal d’audit](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Le filtre que vous avez appliqué le journal d’audit est conservé à la prochaine fois que vous l’affichez, vous serez peut-être amené à modifier ces valeurs pour étendre votre affichage des opérations.

## <a name="next-steps"></a>Étapes suivantes

- Pour l’aide sur la résolution des erreurs de déploiement particulier, consultez [résoudre les erreurs courantes lors du déploiement de ressources vers Azure avec le Gestionnaire de ressources Azure](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, voir [Auditer les opérations effectuées avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant de l’exécuter, voir [déploiement d’un groupe de ressources avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md).
