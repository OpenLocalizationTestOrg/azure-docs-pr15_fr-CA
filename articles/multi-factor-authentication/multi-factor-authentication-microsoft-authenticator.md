<properties
    pageTitle="Application Microsoft Authenticator pour téléphones mobiles | Microsoft Azure"
    description="Découvrez comment mettre à niveau vers la dernière version de l’authentificateur d’Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator"></a>Authentificateur de Microsoft

L’application de Microsoft Authenticator fournit un niveau supplémentaire de sécurité dans votre compte Azure (par exemple, bsimon@contoso.onmicrosoft.com), vos locaux de travail compte (par exemple, bsimon@contoso.com), ou de votre compte Microsoft (par exemple, bsimon@outlook.com).

L’application fonctionne de deux façons :

- **Notification**. L’application peut aider à empêcher l’accès non autorisé aux comptes et à arrêter des transactions frauduleuses en envoyant une notification à votre smartphone ou votre Tablet PC. Simplement afficher la notification et si elle est légitime, sélectionnez **Vérifier**. Dans le cas contraire, vous pouvez sélectionner les **Refuser**. Pour plus d’informations sur les notifications de refus, voir comment utiliser la fonctionnalité de rapport de fraude et Deny pour authentification à plusieurs facteurs.

- **Mot de passe avec le code de vérification**. L’application peut servir à générer un code de vérification OAuth sous la forme d’un jeton logiciel. Vous entrez le code fourni par l’application dans l’écran de connexion, ainsi que le nom d’utilisateur et le mot de passe lorsque vous y êtes invité. Le code de vérification fournit une seconde forme d’authentification.

Avec la version de l’application Microsoft Authenticator, l’application Azure authentificateur ancienne est remplacée.  L’application Azure authentificateur continueront à fonctionner, mais si vous décidez de passer à la nouvelle application Microsoft Authenticator, cet article peut vous aider.  

## <a name="install-the-app"></a>Installation de l’application

L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Ajouter des comptes à l’application

Pour chaque compte que vous souhaitez ajouter à l’application Microsoft Authenticator, utilisez une des procédures suivantes.

### <a name="add-an-account-to-the-app-by-using-the-qr-code-scanner"></a>Ajouter un compte à l’application à l’aide de l’Analyseur de code QR

1. Accédez à l’écran de paramètres de vérification de la sécurité.  Pour plus d’informations sur la façon d’accéder à cet écran, voir [modification de vos paramètres de sécurité](multi-factor-authentication-end-user-manage-settings.md).

2. Sélectionnez **configurer**.

    ![Le bouton Configurer dans l’écran de paramètres de vérification de sécurité](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Ceci fait apparaître un écran avec un code QR.

    ![Écran qui fournit le code QR](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Ouvrez l’application de l’authentificateur de Microsoft. Dans l’écran **comptes** , sélectionnez **+**, puis spécifiez que vous souhaitez ajouter un compte de travail ou l’école.

    ![L’écran comptes avec signe plus](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Écran permettant de spécifier un compte de travail ou de l’école](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. La caméra permet d’analyser le code QR et sélectionnez **terminé** pour fermer l’écran de code QR.

    ![Écran pour l’analyse d’un code QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

    Si votre appareil photo ne fonctionne pas correctement, vous pouvez saisir manuellement le code QR et l’URL. Pour plus d’informations, consultez [Ajouter un compte à l’application manuellement](#add-an-account-to-the-app-manually).

5. Attendez que le compte est activé. Une fois l’activation terminée, sélectionnez **Contact moi**.  Envoie une notification ou sur un code de vérification à votre téléphone.  Sélectionnez **Vérifier**.

    ![Écran dans lequel vous pouvez sélectionner Vérifiez pour vous connecter](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. Si votre société nécessite un code confidentiel pour l’approbation de vérification, entrez-le.

    ![Zone permettant d’entrer un code confidentiel](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. Une fois le code PIN est terminée, sélectionnez **Fermer**. À ce stade, la vérification doit réussir.
8. Nous recommandons d’entrer votre numéro de téléphone portable en cas de perte de l’accès à votre application. Spécifiez votre pays dans la liste déroulante et entrez votre numéro de téléphone portable dans la zone en regard du nom de pays. Cliquez sur **suivant**.
9. À ce stade, vous avez configuré votre méthode de contact. Il est maintenant temps de définir des mots de passe app pour des applications autres que des navigateurs, tels qu’Outlook 2010 ou plus ancien. Si vous n’utilisez pas ces applications, cliquez sur **terminé**. Sinon, passez à l’étape suivante.

    ![Écran de création d’un mot de passe d’application](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. Si vous utilisez les applications autres que des navigateurs, copier le mot de passe fourni app et coller le mot de passe dans vos applications. Pour sur les applications comme Outlook et Lync, voir comment faire pour modifier le mot de passe de votre messagerie pour le mot de passe d’application et comment modifier le mot de passe dans votre application pour le mot de passe d’application.
11. Cliquez sur **terminé**.

Vous devez maintenant voir le nouveau compte dans l’écran **comptes** .

![Écran comptes](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ajouter un compte à l’application manuellement

1. Accédez à l’écran de paramètres de vérification de la sécurité.  Pour plus d’informations sur la façon d’accéder à cet écran, voir [modification de vos paramètres de sécurité](multi-factor-authentication-end-user-manage-settings.md).

2. Sélectionnez **configurer**.

    ![Le bouton Configurer dans l’écran de paramètres de vérification de sécurité](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Ceci fait apparaître un écran avec un code QR.  Notez le code et l’URL.

    ![Écran qui fournit le code QR et l’URL](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Ouvrez l’application de l’authentificateur de Microsoft. Dans l’écran **comptes** , sélectionnez **+**, puis spécifiez que vous souhaitez ajouter un compte de travail ou l’école.

    ![L’écran comptes avec signe plus](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Écran permettant de spécifier un compte de travail ou de l’école](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Dans l’analyseur, sélectionnez **entrer le code manuellement**.

    ![Écran pour l’analyse d’un code QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. Entrez le code et le URL dans les zones appropriées dans l’application.

    ![Écran de saisie de code et des URL](./media/multi-factor-authentication-azure-authenticator/manual.png)

    ![Écran de saisie de code et des URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Attendez que le compte est activé. Une fois l’activation terminée, sélectionnez **Contact moi**. Envoie une notification ou sur un code de vérification à votre téléphone. Sélectionnez **Vérifier**.

Vous devez maintenant voir le nouveau compte dans l’écran **comptes** .

![Écran comptes](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-by-using-touch-id"></a>Ajouter un compte à l’application à l’aide de toucher les ID

L’application Microsoft Authenticator sur iOS prend en charge les ID de contact.  Azure plusieurs facteurs d’authentification permet aux organisations d’exiger un code confidentiel pour les périphériques. Avec l’ID tactile, utilisateurs d’e/s n’est pas nécessaire d’entrer un code PIN. Au lieu de cela, ils peuvent analyser leurs empreintes et sélectionnez **Approuver**.

Configuration des ID de toucher avec Microsoft Authenticator est simple. Vous effectuez un défi de vérification normale avec un code confidentiel. Si votre périphérique prend en charge les ID de toucher, Microsoft Authenticator se configurer automatiquement pour ce compte.

![Vérification de l’installation de toucher les ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

À partir de qui stade, lorsque vous devez vérifier votre connexion à, sélectionnez les notifications reçues et de numériser votre empreinte digitale au lieu d’entrer votre code PIN.

![Notification de transmission](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## <a name="uninstall-the-old-azure-authentication-app"></a>Désinstaller l’application Azure authentification ancienne

Après avoir ajouté tous les comptes à la nouvelle application, vous pouvez désinstaller l’ancienne application à partir de votre téléphone.

## <a name="delete-an-account"></a>Supprimer un compte

Pour supprimer un compte de l’application Microsoft Authenticator, sélectionnez le compte, puis sélectionnez **Supprimer**.

![Bouton Supprimer](./media/multi-factor-authentication-azure-authenticator/remove.png)
