
1. Dans l’Explorateur de solutions de Visual Studio, le projet d’application Windows Store avec le bouton droit, cliquez sur **banque de** > **Associer une application à la banque...**.

    ![Associer des application Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)

2. Dans l’Assistant, cliquez sur **suivant**, connectez-vous à votre compte Microsoft, tapez un nom pour votre application en **réserve un nouveau nom de l’application**, puis cliquez sur **réserver**.

3. Après que l’inscription de l’application est créée avec succès, sélectionnez le nouveau nom de l’application et cliquez sur **suivant**, puis cliquez sur **associer**. Cette opération ajoute les informations d’inscription de Windows Store requises au manifeste d’application.

7. Répétez les étapes 1 et 3 pour le projet d’application Windows Phone banque en utilisant le même type d’enregistrement créé précédemment pour l’application du Windows Store.  

7. Accédez au [Centre de développement Windows](https://dev.windows.com/en-us/overview), connectez-vous à l’aide de votre compte Microsoft, cliquez sur la nouvelle immatriculation app dans **Mes applications**, puis développez **Services** > **notifications de transmission**.

8. Dans la page **notifications de transmission** , cliquez sur **services Microsoft Live de site** sous **de Notification Services Push Windows (WNS) et les applications Mobile Microsoft Azure**et prenez note des valeurs de l' **Identificateur de Package de sécurité** et de la valeur *actuelle* dans le **Secret de l’Application**. 

    ![Paramètre d’application dans le centre de développement](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT] Le secret de l’application et le package SID sont des informations d’identification de sécurité important. Ne pas partager ces valeurs avec n’importe qui ou les distribuer avec votre application.
