<properties
   pageTitle="Installer la mise à jour 2 sur le périphérique StorSimple | Microsoft Azure"
   description="Explique comment installer le 2 série 8000 StorSimple sur votre périphérique de la gamme 8000 de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="install-update-2-on-your-storsimple-device"></a>Installer la mise à jour 2 sur votre périphérique de StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment faire pour installer la mise à jour 2 sur un dispositif de StorSimple une version antérieure du logiciel en cours d’exécution via le portail classique Azure. Ce didacticiel couvre également les étapes nécessaires à la mise à jour lorsqu’une passerelle est configurée sur une interface réseau autres que les données 0 du périphérique StorSimple et que vous essayez de mettre à jour à partir d’une version préliminaire de mise à jour du logiciel 1.

Mise à jour 2 comprend les mises à jour du logiciel de périphérique, les mises à jour de pilote LSI et les mises à jour du microprogramme de disque. Le logiciel de périphérique et les mises à jour de LSI sont mises à jour sans interruption de service et peuvent être appliquées via le portail classique Azure. Les mises à jour du microprogramme de disque sont mises à jour sans interruption de service et peuvent être appliquées uniquement via l’interface Windows PowerShell du périphérique.

> [AZURE.IMPORTANT]

> -  Mise à jour 2 n’est pas visible immédiatement, étant donné que nous avons un déploiement échelonné de mises à jour. Analyse des mises à jour dans quelques jours à nouveau en tant que cette mise à jour ne sera bientôt plus disponible.
> - Un ensemble de contrôles avant de manuels et automatiques sont effectuées avant l’installation pour déterminer l’état de santé en termes de matériel état et la connectivité réseau. Ces contrôles préalables sont effectués uniquement si vous appliquez les mises à jour à partir du portail classique Azure.
> - Nous vous conseillons d’installer les mises à jour du pilote et le logiciel via le portail classique Azure. Vous ne devez pas dépasser à l’interface Windows PowerShell de l’équipement (à installer les mises à jour) en cas d’échec de la vérification de la mise à jour préalable passerelle dans le portail. Les mises à jour peuvent prendre des heures de 4 à 7 pour installer (y compris les mises à jour de Windows). Les mises à jour du mode de maintenance doivent être installés via l’interface Windows PowerShell du périphérique. Comme les mises à jour du mode de maintenance sont mises à jour sans interruption de service, il entraîne un temps d’arrêt pour votre périphérique.
> - Si vous exécutez le Gestionnaire de snapshots StorSimple facultatif, assurez-vous que vous avez mis à niveau votre version du Gestionnaire de snapshots mise à jour 2 avant la mise à jour de l’appareil.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>Installez la mise à jour 2 via le portail classique Azure

Effectuez les opérations suivantes pour mettre à jour votre périphérique pour la [mise à jour 2](storsimple-update2-release-notes.md).


> [AZURE.NOTE]
Mise à jour 2 permet à Microsoft d’extraire des informations de diagnostic supplémentaires à partir du périphérique. Par conséquent, lorsque notre équipe des opérations identifie les périphériques qui posent problème, nous sont mieux équipés pour collecter des informations à partir du périphérique et de diagnostiquer les problèmes. En acceptant les 2 mise à jour, vous nous autorisez à cette prise en charge proactive.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Vérifiez que votre périphérique est en cours d’exécution **StorSimple 8000 Series mise à jour 2 (6.3.9600.17673)**. La **dernière mise à jour de la date** doit être modifié. Vous verrez également que les mises à jour du mode de Maintenance sont disponibles (ce message peut continuer à afficher jusqu'à 24 heures après avoir installé les mises à jour).

    Mises à jour du mode de maintenance sont des mises à jour sans interruption de service entraînent un arrêt du périphérique et peuvent être appliquées uniquement via l’interface Windows PowerShell de votre périphérique. Dans certains cas lorsque vous exécutez la mise à jour 1.2, votre microprogramme de disque peut-être déjà à jour, auquel cas vous n’avez pas besoin d’installer les mises à jour du mode de maintenance.

13. Télécharger les mises à jour du mode de maintenance à l’aide de la procédure indiquée dans la zone [pour télécharger des correctifs](#to-download-hotfixes) pour rechercher et télécharger des KB3121899, les installations de disque mises à jour (les autres mises à jour doivent déjà être installés maintenant).

13. Suivez les étapes répertoriées dans [installer et vérifier les correctifs en mode maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer les mises à jour du mode de maintenance.


## <a name="install-update-2-as-a-hotfix"></a>Installer la mise à jour 2 sous la forme d’un correctif

Utilisez cette procédure si vous ne la vérification de la passerelle lorsque vous essayez d’installer les mises à jour via le portail classique Azure. Le contrôle échoue car vous avez une passerelle affectée à une interface réseau 0 de données non- et votre périphérique exécute une version de logiciel avant la mise à jour 1.

Les versions du logiciel peuvent être mis à niveau à l’aide de la méthode de correctif sont mise à jour 0.1, 0.2, mise à jour d’et mise à jour de 0,3, mise à jour 1, 1.1 de mise à jour et 1.2 de mise à jour. La méthode correctif implique les trois étapes suivantes :

- Télécharger les correctifs à partir du catalogue Microsoft Update.
- Installer et vérifier les correctifs du mode normal.
- Installer et vérifier le correctif en mode de maintenance.

Pour installer la mise à jour 2 sous la forme d’un correctif, vous devez télécharger et installer les correctifs suivants :

| Commande  | KO        | Description                    | Type de mise à jour  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3121901 | Mise à jour logicielle         |  Régulière     |
| 2      | KB3121900 | Pilote LSI              |  Régulière     |
| 3      | KB3080728 | Correctif de Storport </br> Windows Server 2012 R2 |  Régulière     |
| 4      | KB3090322 | Correctif de spaceport </br> Windows Server 2012 R2 |  Régulière     |
| 5      | KB3121899 | Microprogramme de disque           | Gestion des  |


> [AZURE.IMPORTANT]
>
> - Si votre périphérique exécute la version de publication (GA), veuillez contacter le [Support technique de Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à la mise à jour.
> - Cette procédure doit être effectuée qu’une seule fois pour appliquer la mise à jour 2. Vous pouvez utiliser le portail classique Azure pour appliquer les mises à jour ultérieures.
> - Chaque installation de correctif peut prendre 20 minutes environ. Temps total d’installation est près de 2 heures.
> - Avant d’utiliser cette procédure pour appliquer la mise à jour, assurez-vous que les deux contrôleurs de périphérique sont en ligne et tous les composants matériels sont en bon état.

Procédez comme suit pour appliquer cette mise à jour sous la forme d’un correctif.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la [version 2 de la mise à jour](storsimple-update2-release-notes.md).
