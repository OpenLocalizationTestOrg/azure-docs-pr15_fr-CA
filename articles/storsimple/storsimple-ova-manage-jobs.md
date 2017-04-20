<properties 
   pageTitle="Permet d’afficher et de gérer les tâches de tableau virtuel de StorSimple | Microsoft Azure"
   description="Décrit la page de travaux du service Gestionnaire de StorSimple et comment l’utiliser pour effectuer le suivi des travaux en cours et récentes pour le tableau virtuel StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Le service Gestionnaire de StorSimple permet d’afficher les tâches du tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

La page **projets** fournit un portail unique et central pour l’affichage et la gestion des tâches qui sont démarrées sur des réseaux virtuels (également connu sous les périphériques virtuels locaux) qui sont connectés à votre service de gestionnaire de StorSimple. Vous pouvez afficher les tâches en cours d’exécution, terminés et échecs pour plusieurs périphériques virtuels. Résultats sont présentés dans un format tabulaire. 

![Page tâches](./media/storsimple-ova-manage-jobs/ovajobs1.png)

Vous pouvez trouver rapidement les tâches qui que vous intéressent en filtrant sur les champs, tels que :

- **Statut** : vous pouvez rechercher toutes les tâches en cours d’exécution, terminés ou échecs.
- **À partir de et vers** – travaux peuvent être filtrées en fonction de la plage de date et d’heure.
- **Type** : le type de tâche peut être tout, sauvegarder, restaurer, basculement, télécharger des mises à jour ou installer des mises à jour.
- **Périphériques** – les tâches sont lancées sur un périphérique connecté à votre service. Les tâches filtrées sont ensuite sous forme de tableau sur les attributs suivants :

    - **Type** : le type de tâche peut être tout, sauvegarder, restaurer, basculement, télécharger des mises à jour ou installer des mises à jour.

    - **Statut** – can de travaux être tous, en cours d’exécution, terminé ou échec.

    - **Entité** – les tâches peuvent être associés à un volume, un partage ou un périphérique. 

    - Le **périphérique** – le nom du périphérique sur lequel le travail a été démarré.

    - **Démarré sur** – l’heure de début de la tâche.

    - **Cours** – le pourcentage d’achèvement d’une tâche en cours d’exécution. Pour un travail terminé, cela doit toujours être de 100 %.

La liste des tâches est actualisée toutes les 30 secondes.

## <a name="view-job-details"></a>Afficher les détails du projet

Effectuez les opérations suivantes pour afficher les détails de n’importe quelle tâche.

#### <a name="to-view-job-details"></a>Pour afficher les détails d’une tâche

1. Dans la page **tâches** , afficher l’ou les travaux que vous intéressent en exécutant une requête avec les filtres appropriés. Vous pouvez rechercher les tâches terminées ou en cours d’exécution.

2. Sélectionnez une tâche dans la liste sous forme de tableau des tâches.

3. En bas de la page, cliquez sur **Détails**.

4. Dans **la boîte de dialogue,** vous pouvez afficher le statut, les détails et les statistiques de temps. L’illustration suivante montre un exemple de la boîte de dialogue **Détails de la tâche de sauvegarde** .
 
    ![Page de détails de projet](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Échecs des tâches lorsque l’ordinateur virtuel est suspendu dans l’hyperviseur

Lorsqu’un travail est en cours sur votre tableau virtuel StorSimple et le périphérique (mis en service dans l’hyperviseur de machine virtuelle) est interrompue de plus de 15 minutes, le travail échoue. Ceci en raison de votre temps de tableau virtuel de StorSimple est synchronisée avec l’heure Microsoft Azure. Un exemple d’un échec de tâche de restauration est illustré dans la capture d’écran suivante.

![Échec du travail de restauration](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Ces échecs seront applique aux tâches de sauvegarde, de restauration, de mise à jour et de basculement. Si votre ordinateur virtuel n’est configuré dans Hyper-V, l’ordinateur synchronisera finit par heure avec votre hyperviseur. Une fois que cela se produit, vous pouvez redémarrer votre travail. 

## <a name="next-steps"></a>Étapes suivantes

[Apprenez à utiliser l’interface utilisateur pour administrer votre tableau virtuel StorSimple site web local](storsimple-ova-web-ui-admin.md).
