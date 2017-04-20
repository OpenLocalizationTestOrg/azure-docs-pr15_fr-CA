<properties
   pageTitle="Installer 1.2 de mise à jour de votre périphérique StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple 8000 série de mise à jour 1.2 sur votre périphérique de la gamme 8000 de StorSimple."
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
   ms.date="08/22/2016"
   ms.author="alkohli" />

# <a name="install-update-12-on-your-storsimple-device"></a>Installer 1.2 de mise à jour de votre périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment installer la mise à jour 1.2 sur un dispositif de StorSimple qui exécute une version du logiciel avant la mise à jour 1. Ce didacticiel couvre également les étapes supplémentaires requises pour la mise à jour lorsqu’une passerelle est configurée sur une interface réseau autres que les données 0 du périphérique StorSimple.

Mise à jour 1.2 comprend des mises à jour du logiciel de périphérique, les mises à jour de pilote LSI et les mises à jour du microprogramme de disque. Les logiciels et les mises à jour de pilote LSI sont mises à jour sans interruption de service et peuvent être appliquées via le portail classique Azure. Les mises à jour du microprogramme de disque sont mises à jour sans interruption de service et peuvent être appliquées uniquement via l’interface Windows PowerShell du périphérique.

Selon la version de votre périphérique est en cours d’exécution, vous pouvez déterminer si 1.2 de mise à jour sera appliquée. Vous pouvez vérifier la version du logiciel de votre périphérique, accédez à la section **Aperçu rapide** de votre périphérique de **tableau de bord**.

</br>

| Si la version du logiciel en cours d’exécution...   | Que se passe-t-il dans le portail ?                              |
|---------------------------------|--------------------------------------------------------------|
| Version - GA                    | Si vous utilisez la version d’origine (GA), n’appliquez pas cette mise à jour. Veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour mettre à jour votre périphérique.|
| Mise à jour de 0,1                      | Portail s’applique la mise à jour 1.2.                                |
| Mise à jour de 0,2                      | Portail s’applique la mise à jour 1.2.                                |
| Mise à jour de 0,3                      | Portail s’applique la mise à jour 1.2.                                |
| Mise à jour 1                        | Cette mise à jour ne sera pas disponible.                           |
| Mise à jour 1.1                      | Cette mise à jour ne sera pas disponible.                           |

</br>

> [AZURE.IMPORTANT]

> -  Mise à jour 1.2 n’est pas visible immédiatement, étant donné que nous avons un déploiement échelonné de mises à jour. Analyse des mises à jour dans quelques jours à nouveau en tant que cette mise à jour ne sera bientôt plus disponible.
> - Cette mise à jour inclut un ensemble de Pre-contrôles de manuelles et automatiques pour déterminer l’état de santé en termes de matériel état et la connectivité réseau. Ces contrôles préalables sont effectués uniquement si vous appliquez les mises à jour à partir du portail classique Azure.
> - Nous vous conseillons d’installer les mises à jour du pilote et le logiciel via le portail classique Azure. Vous ne devez pas dépasser à l’interface Windows PowerShell de l’équipement (à installer les mises à jour) en cas d’échec de la vérification de la mise à jour préalable passerelle dans le portail. Les mises à jour peuvent prendre 5 à 10 heures installer (y compris les mises à jour de Windows). Les mises à jour du mode de maintenance doivent être installés via l’interface Windows PowerShell du périphérique. Comme les mises à jour du mode de maintenance sont mises à jour sans interruption de service, il entraîne un temps d’arrêt pour votre périphérique.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Installer la mise à jour 1.2 via le portail classique Azure

Effectuez les opérations suivantes pour mettre à jour votre périphérique à la [mise à jour de la version 1.2](storsimple-update1-release-notes.md). Utilisez cette procédure uniquement si vous avez une passerelle est configurée sur l’interface réseau 0 de données sur votre périphérique.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Vérifiez que votre périphérique est en cours d’exécution **StorSimple 8000 série de mise à jour 1.2 (6.3.9600.17584)**. La **dernière mise à jour de la date** doit être modifié. Vous verrez également que les mises à jour du mode de Maintenance sont disponibles (ce message peut continuer à afficher jusqu'à 24 heures après avoir installé les mises à jour).

    Mises à jour du mode de maintenance sont des mises à jour sans interruption de service entraînent un arrêt du périphérique et peuvent être appliquées uniquement via l’interface Windows PowerShell de votre périphérique.

    ![Page de maintenance] (./media/storsimple-install-update-1/InstallUpdate12_10M.png "Page de maintenance")

13. Télécharger les mises à jour du mode de maintenance à l’aide de la procédure indiquée dans la zone [pour télécharger des correctifs]( #to-download-hotfixes) pour rechercher et télécharger des KB3063416, les installations de disque mises à jour (les autres mises à jour doivent déjà être installés maintenant).

13. Suivez les étapes répertoriées dans [installer et vérifier les correctifs en mode maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer les mises à jour du mode de maintenance.

14. Dans le portail classique Azure, accédez à la page de **Maintenance** et en bas de la page, cliquez sur **Rechercher les mises à jour** pour vérifier les mises à jour de Windows puis cliquez sur **Installer les mises à jour**. Vous avez terminé une fois que toutes les mises à jour sont installées correctement.



## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>Installer la mise à jour 1.2 sur un périphérique doté d’une passerelle est configurée pour une interface réseau 0 de données non-

Vous devez utiliser cette procédure uniquement si vous ne la vérification de la passerelle lorsque vous essayez d’installer les mises à jour via le portail classique Azure. Le contrôle échoue car vous avez une passerelle affectée à une interface réseau 0 de données non- et votre périphérique exécute une version de logiciel avant la mise à jour 1. Si votre périphérique ne dispose pas d’une passerelle sur une interface de réseau 0 non-DATA, vous pouvez mettre à jour votre périphérique directement à partir du portail classique Azure. Voir la rubrique [Install 1.2 via le portail classique Azure de mettre à jour](#install-update-1.2-via-the-azure-classic-portal).

Les versions du logiciel peuvent être mis à niveau à l’aide de cette méthode sont mise à jour de 0,1, mise à jour de 0,2 et 0,3 mise à jour de.


> [AZURE.IMPORTANT]
>
> - Si votre périphérique exécute la version de publication (GA), veuillez contacter le [Support technique de Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à la mise à jour.
> - Cette procédure doit être effectuée qu’une seule fois pour appliquer la mise à jour 1.2. Vous pouvez utiliser le portail classique Azure pour appliquer les mises à jour ultérieures.

Si votre périphérique exécutant le logiciel de mise à jour préalable 1 et possède une passerelle pour une interface réseau autres que les données de 0, vous pouvez appliquer 1.2 de mise à jour dans le des deux façons suivantes :

- **Option 1**: télécharger la mise à jour et l’appliquer à l’aide de la `Start-HcsHotfix` applet de commande à partir de l’interface Windows PowerShell du périphérique. Il s’agit de la méthode recommandée. **N’utilisez pas cette méthode pour appliquer la mise à jour 1.2 si votre périphérique s’exécute Update 1.0 ou 1.1 de mise à jour.**

- **Option 2**: supprimer la configuration de la passerelle et d’installer la mise à jour directement à partir du portail classique Azure.


Pour obtenir des instructions détaillées pour chacune d'entre elles sont fournies dans les sections suivantes.

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>Option 1 : Utiliser Windows PowerShell pour appliquer la mise à jour 1.2 en tant que correctif StorSimple

Vous devez utiliser cette procédure uniquement si vous exécutez la mise à jour de 0.1, 0.2, 0.3 et si votre vérification de la passerelle a échoué lorsque vous essayez d’installer des mises à jour à partir du portail classique Azure. Si vous exécutez le logiciel de publication (GA), veuillez la [Prise en charge de Microsoft](storsimple-contact-microsoft-support.md) pour mettre à jour votre périphérique.

Pour installer la mise à jour 1.2 sous la forme d’un correctif, vous devez télécharger et installer les correctifs suivants :

| Commande  | KO        | Description             | Type de mise à jour  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3063418 | Mise à jour logicielle         |  Régulière     |
| 2      | KB3043005 | Mise à jour du contrôleur SAS de LSI |  Régulière     |
| 3      | KB3063416 | Microprogramme de disque           | Gestion des  |

Avant d’utiliser cette procédure pour appliquer la mise à jour, assurez-vous que :

- Les deux contrôleurs de périphérique sont en ligne.

Procédez comme suit pour appliquer la mise à jour 1.2. **Les mises à jour peuvent prendre environ 2 heures (environ 30 minutes pour les logiciels, les 30 minutes pour pilote, 45 minutes de microprogramme de disque).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>Option 2 : Utiliser le portail classique Azure pour appliquer la mise à jour 1.2 après la suppression de la configuration de la passerelle

Cette procédure s’applique uniquement aux périphériques de StorSimple qui exécutent une version de logiciel avant la mise à jour 1 et qui ont une passerelle définie sur une interface réseau autres que les données 0. Vous devez désactiver le paramètre de passerelle avant l’application de la mise à jour.

La mise à jour peut prendre quelques heures. Si vos hôtes se trouvent dans des sous-réseaux différents, suppression de la configuration de passerelle sur les interfaces iSCSI peut entraîner d’interruption. Nous vous conseillons de configurer les données 0 pour le trafic iSCSI pour réduire les temps morts.

Procédez comme suit pour désactiver l’interface réseau avec la passerelle et appliquez la mise à jour.

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la [version de mise à jour 1.2](storsimple-update1-release-notes.md).
