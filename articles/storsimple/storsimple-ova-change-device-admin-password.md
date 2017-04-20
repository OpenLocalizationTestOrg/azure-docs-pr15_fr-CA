<properties 
   pageTitle="Modifier le mot de passe admin de périphérique virtuel StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le portail classique Azure ou l’interface utilisateur du web StorSimple Virtual Array pour modifier le mot de passe administrateur."
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Modifier le mot de passe administrateur de périphérique tableau virtuel de StorSimple

## <a name="overview"></a>Vue d’ensemble

Lorsque vous utilisez l’interface de Windows PowerShell pour accéder au périphérique virtuel StorSimple, vous devez entrer un mot de passe administrateur. Lorsque le périphérique StorSimple est configuré et démarré pour la première fois, le mot de passe par défaut est *mot de passe1*. Pour la sécurité de vos données, le mot de passe expire la première fois que vous vous connectez et que vous devez modifier ce mot de passe.

Vous pouvez également utiliser l’interface utilisateur de web local ou le portail classique Azure pour modifier le mot de passe administrateur à tout moment une fois que le périphérique est déployé dans votre environnement de production. Chacune de ces procédures est décrite dans cet article.

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Le portail classique Azure permet de modifier le mot de passe

Procédez comme suit pour modifier le mot de passe administrateur périphérique via le portail classique Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Pour modifier le mot de passe administrateur via le portail classique Azure

1. Dans le portail, cliquez sur **périphériques** > la**Configuration** de votre périphérique.

2. Faites défiler jusqu'à la section **Mot de passe administrateur de périphérique** . Fournir un mot de passe contenant entre 8 et 15 caractères. Le mot de passe doit être une combinaison de caractères en majuscules, minuscules, numériques et spéciaux.

3. Confirmer le mot de passe.

4. Cliquez sur **Enregistrer** en bas de la page.

Le mot de passe administrateur doit maintenant être mis à jour. Vous pouvez utiliser ce mot de passe modifié d’accéder au périphérique local.

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>Utilisez l’interface utilisateur du web StorSimple Virtual Array pour modifier le mot de passe

Procédez comme suit pour modifier le mot de passe administrateur périphérique par l’intermédiaire de l’interface utilisateur de web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Pour modifier le mot de passe administrateur périphérique via l’interface utilisateur du web local

1. Dans l’interface utilisateur web local, cliquez sur **gestion** > **changer de mot de passe** pour votre périphérique.

    ![Modifier mot de passe1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. Entrez le **mot de passe actuel**.

3. Fournir un **nouveau mot de passe**. Le mot de passe doit comporter au moins 8 caractères. Il doit contenir 3 sur 4 des éléments suivants : les caractères majuscules, minuscules, numériques et spéciaux.

    Notez que votre mot de passe ne peut pas être la même que les dernière 24 mots de passe.

3. Entrez de nouveau le mot de passe pour le confirmer.

    ![Modifier mot de passe2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. En bas de la page, cliquez sur **Appliquer**. Le nouveau mot de passe est appliquée. Si la modification du mot de passe n’est pas réussie, vous verrez l’erreur suivante.

    ![Erreur de mot de passe](./media/storsimple-ova-change-device-admin-password/image42.png)

    Une fois le mot de passe est mis à jour, vous serez averti. Vous pouvez ensuite utiliser ce mot de passe modifié d’accéder au périphérique local.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur [l’administration de votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).
