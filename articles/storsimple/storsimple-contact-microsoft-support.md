<properties 
   pageTitle="Contactez le Support Microsoft | Microsoft Azure"
   description="Apprenez à créer une demande de support et de démarrer une session de prise en charge sur le périphérique StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="contact-microsoft-support"></a>Contactez le support technique de Microsoft

Si vous rencontrez des problèmes avec votre solution Microsoft Azure StorSimple, vous pouvez créer une demande de service pour le support technique. Dans une session en ligne avec votre ingénieur de support, vous devez également démarrer une session de prise en charge sur le périphérique StorSimple. Cet article vous guide à travers :

- Comment créer une demande de support.
- Comment démarrer une session de prise en charge de l’interface Windows PowerShell de votre périphérique StorSimple.

Examinez les [SLA de prise en charge série StorSimple 8000 et informations](https://msdn.microsoft.com/library/mt433077.aspx) avant de créer une demande de Support.

## <a name="create-a-support-request"></a>Créer une demande de support

Effectuez les étapes suivantes pour créer une demande de support :

#### <a name="to-create-a-support-request"></a>Pour créer une demande de prise en charge

1. Dans [Azure portal classique](https://manage.windowsazure.com/), dans le coin supérieur droit, cliquez sur votre nom de compte et puis cliquez sur **Contacter le support technique Microsoft**.

    ![Prise en charge de MS de contact via ManagementPortal](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Vous allez être redirigé vers le nouveau portail Azure (portal.azure.com). Cliquez sur la mosaïque de la **nouvelle demande d’assistance** .

    ![Prise en charge de MS de contact via le nouveau portail](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    Sur le côté droit de l’écran, le volet **Nouveau prend en charge la demande** s’affiche. 

    ![Nouveau volet de demande de prise en charge](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. Dans la boîte de dialogue de **Notions de base** , effectuez les opérations suivantes :                                
    1. Dans la liste déroulante **type de problème** , sélectionnez **technique**.
    2. Dans la liste déroulante, sélectionnez un **abonnement** .
    3. Dans la liste déroulante **Service** , sélectionnez **StorSimple**. 
    4. Dans la liste déroulante, sélectionnez un **plan de prise en charge** . Vous avez besoin d’un plan de support technique payant pour activer le Support technique.

4. Cliquez sur **suivant**. La boîte de dialogue du **problème** s’affiche.

    ![Nouveau volet de demande de prise en charge](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 

5. Dans la boîte de dialogue **problème** , effectuez les opérations suivantes :

    1.  Dans la liste déroulante, sélectionnez un niveau de **gravité** .
    2.  Dans la liste déroulante, sélectionnez un **type de problème** .
    3.  Sélectionnez une **catégorie** dans la liste déroulante. 
    4.  Dans la zone **Détails** , décrivez votre problème.
    5.  Dans la zone **délai** , indiquer la date, heure et fuseau horaire correspondant à la dernière occurrence du problème.
    6.  **Téléchargement de fichier**, cliquez sur l’icône du dossier pour naviguer jusqu'à votre package de prise en charge.
    7.  Activez la case à cocher de **partager les informations de diagnostic** .

6. Cliquez sur **suivant**. La boîte de dialogue **informations sur le Contact** s’affiche.

    ![Nouveau volet de demande de prise en charge](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 

7. Entrez vos informations de contact et sélectionnez une méthode de contact (téléphone ou courrier électronique). 

8. Activez la case à cocher **Enregistrer les modifications du contact pour les demandes de prise en charge future** .

9. Cliquez sur **créer**.

Une fois que vous avez soumis votre demande, un technicien du support technique vous contactera dès que possible pour poursuivre votre demande.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Démarrer une session de prise en charge de Windows PowerShell pour StorSimple

Pour résoudre les problèmes que vous pouvez rencontrer avec le périphérique StorSimple, vous devrez contacter l’équipe de Support de Microsoft. Support Microsoft devrez peut-être utiliser une prise en charge session pour vous connecter à votre périphérique. 

Effectuez les étapes suivantes pour démarrer une session de prise en charge :

#### <a name="to-start-a-support-session"></a>Pour démarrer une session de prise en charge

1. Accéder au périphérique directement à l’aide de la console série ou via une session telnet à partir d’un ordinateur distant. Pour ce faire, suivez les étapes de [PuTTY utiliser pour se connecter à la console série du périphérique](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Dans la session qui s’affiche, appuyez sur la touche **entrée** pour obtenir une invite de commande.

3. Dans le menu de la console série, sélectionnez l’option 1, **Connectez-vous à l’aide d’un accès complet**.

4. À l’invite, tapez le mot de passe suivant : 

    `Password1`

5. À l’invite, tapez la commande suivante :

    `Enable-HcsSupportAccess`

6. Une chaîne chiffrée s’affichera pour vous. Copier cette chaîne dans un éditeur de texte tel que le bloc-notes.

7. Enregistrer cette chaîne et l’envoyer dans un message électronique au Support Microsoft. 

> [AZURE.IMPORTANT] Vous pouvez désactiver l’accès au support en exécutant `Disable-HcsSupportAccess`. Le périphérique StorSimple tentera également de désactiver l’accès de la prise en charge de 8 heures, une fois que la session a été lancée. Il est recommandé de modifier vos informations d’identification de périphérique StorSimple après le lancement d’une session de prise en charge.
