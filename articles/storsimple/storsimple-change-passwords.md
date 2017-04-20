<properties 
   pageTitle="Modifier votre mot de passe StorSimple | Microsoft Azure" 
   description="Décrit comment utiliser le service Gestionnaire de StorSimple pour modifier votre mot de passe administrateur Gestionnaire de snapshots de StorSimple et dispositif." 
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
   ms.workload="TBD" 
   ms.date="08/17/2016"
   ms.author="alkohli"/>

# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Le service Gestionnaire de StorSimple permet de modifier votre mot de passe StorSimple

## <a name="overview"></a>Vue d’ensemble 

La page de **configuration** du portail classique Azure contient tous les paramètres de périphérique vous pouvez reconfigurer sur un dispositif de StorSimple qui est géré par un service de gestionnaire de StorSimple. Ce didacticiel explique comment vous pouvez utiliser la page de **configuration** pour modifier l’administrateur de votre périphérique ou Gestionnaire de snapshots de StorSimple de mot de passe.

## <a name="change-the-device-administrator-password"></a>Modifier le mot de passe administrateur

Lorsque vous utilisez l’interface de Windows PowerShell pour accéder à l’équipement StorSimple, vous devez entrer un mot de passe administrateur. Lorsque le premier périphérique StorSimple est inscrit auprès d’un service, le mot de passe par défaut de cette interface est *mot de passe1*. Pour la sécurité de vos données, vous devez modifier ce mot de passe à la fin de la procédure d’inscription. Vous ne pouvez pas quitter le processus d’inscription sans modifier ce mot de passe. Pour plus d’informations, consultez [étape 3 : configurer et inscrire le périphérique par le biais de Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Le mot de passe a été tout d’abord défini par le biais de l’interface Windows PowerShell lors de votre inscription seront alors modifié via le portail classique Azure. Procédez comme suit pour modifier le mot de passe administrateur.

#### <a name="to-change-the-device-administrator-password"></a>Pour modifier le mot de passe administrateur

1. Dans le portail classique, cliquez sur **périphériques** > **configurer** pour votre périphérique.

2. Faites défiler jusqu'à la section **Mot de passe administrateur de périphérique** . Fournir un mot de passe contenant entre 8 et 15 caractères. Le mot de passe doit être une combinaison d’au moins 3 caractères en majuscules, minuscules, numériques et spéciaux.

3. Confirmer le mot de passe.

4. Cliquez sur **Enregistrer** en bas de la page.

Le mot de passe administrateur doit maintenant être mis à jour. Vous pouvez utiliser ce mot de passe modifié pour accéder à l’interface de Windows PowerShell.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Modifier le mot de passe du Gestionnaire de snapshots de StorSimple

Gestionnaire de snapshots StorSimple logiciel réside sur votre hôte Windows et permet aux administrateurs de gérer des sauvegardes de votre périphérique StorSimple sous la forme d’un local et de snapshots en nuage.

Lors de la configuration d’un périphérique dans le Gestionnaire de snapshots StorSimple, le système vous demandera de fournir l’adresse IP du périphérique et du mot de passe pour authentifier votre périphérique de stockage. Ce mot de passe est d’abord configuré par le biais de l’interface de Windows PowerShell. Pour plus d’informations, consultez [étape 3 : configurer et inscrire le périphérique par le biais de Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Le mot de passe a été tout d’abord défini par le biais de l’interface Windows PowerShell lors de votre inscription seront alors modifié via le portail classique. Procédez comme suit pour modifier le mot de passe du Gestionnaire d’instantanés StorSimple.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Pour modifier le mot de passe du Gestionnaire de snapshots de StorSimple

1. Dans le portail classique, cliquez sur **périphériques** > **configurer** pour votre périphérique.

2. Faites défiler jusqu'à la section **Gestionnaire de snapshots de StorSimple** . Entrez un mot de passe de 14 ou 15 caractères. Assurez-vous que le mot de passe contienne une combinaison d’au moins 3 caractères en majuscules, minuscules, numériques et spéciaux.

3. Confirmer le mot de passe.

4. Cliquez sur **Enregistrer** en bas de la page.

Le mot de passe du Gestionnaire de snapshots StorSimple doit maintenant être mis à jour.
 

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la [sécurité de StorSimple](storsimple-security.md).

- Pour en savoir plus sur la [modification de la configuration de votre périphérique](storsimple-modify-device-config.md).

- En savoir plus sur [le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
