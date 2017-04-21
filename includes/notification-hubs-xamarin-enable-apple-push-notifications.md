

Pour enregistrer l’application de notifications de type Pousser par Apple Push Notification Service (APNS), vous devez créer un nouveau certificat de l’émetteur, l’ID d’application et profil de mise en service pour le projet sur le portail des développeurs d’Apple. L’ID d’application contient les paramètres de configuration qui permettent à votre application d’envoyer et de recevoir des notifications de type Pousser. Ces paramètres inclut le certificat de notification de transmission nécessaire à l’authentification avec Apple Push Notification Service (APNS) lors de l’envoi et la réception de notifications de type Pousser. Pour plus d’informations sur ces concepts, consultez la documentation [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) officielle.


####<a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Générer le fichier de demande de certificat de signature pour le certificat de l’émetteur

Ces étapes vous guident dans le processus de création de la demande de signature de certificat. Cela servira à générer un certificat d’émetteur à utiliser avec APNS.

1. Sur votre Mac, exécutez l’outil de trousseau d’accès. Il peut être ouvert depuis le dossier **utilitaires** ou dans **l’autre** dossier dans la zone de lancement.

2. Cliquez sur le **Trousseau d’accès**, développez **L’Assistant de certificat**, puis cliquez sur **demander un certificat auprès d’une autorité de certificat...**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Sélectionnez votre **Adresse de messagerie de l’utilisateur** et le **Nom commun** , assurez-vous que **enregistré sur le disque** est sélectionné, puis cliquez sur **Continuer**. Laissez vide le champ **Adresse de messagerie d’autorité de certification** comme il n’est pas obligatoire.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Tapez un nom pour le fichier de demande de signature de certificat (CSR) dans la zone **Enregistrer sous**, sélectionnez l’emplacement dans **lequel**, puis cliquez sur **Enregistrer**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Le fichier du service clientèle est enregistré à l’emplacement sélectionné ; l’emplacement par défaut est dans le bureau. N’oubliez pas de l’emplacement choisi pour ce fichier.


####<a name="register-your-app-for-push-notifications"></a>Enregistrer votre application pour les notifications de type Pousser

Créer un nouvel ID App explicite de votre application avec Apple et aussi le configurer pour les notifications de transmission.  

1. Atteindre au centre de développement Apple [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456) connectez-vous avec votre ID d’Apple et cliquez sur **identificateurs**, puis cliquez sur **ID de l’application**, cliquez sur le **+** vous connecter pour enregistrer une nouvelle application.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Les trois champs suivants pour votre nouvelle application de mise à jour, puis cliquez sur **Continuer**:

    * **Nom**: entrez un nom descriptif pour votre application dans le champ **nom** de la section **Description de l’ID App** .

    * **Identificateur du lot**: dans la section **ID explicite de l’application** , entrez un **Identificateur de groupe** sous la forme `<Organization Identifier>.<Product Name>` comme indiqué dans le [Guide de Distribution d’application](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Cela doit correspondre à ce qui est également utilisé dans le projet XCode, Xamarin ou Cordova pour votre application.

    * **Envoyer des Notifications**: Cochez l’option **Envoyer des Notifications** dans la section **Services d’application** ,.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.  Dans le champ Confirmer votre écran ID de l’application, vérifiez le paramètre et après avoir vérifié les cliquez sur **Soumettre**

4.  Une fois que vous avez soumis la nouvelle ID App, vous verrez l’écran **d’enregistrement complet** . Cliquez sur **terminé**.

5. Dans le centre de développement, sous ID de l’application, recherchez l’ID de l’application que vous venez de créer et cliquez sur sa ligne. En cliquant sur la ligne de code d’application affiche les détails de l’application. Cliquez sur le bouton **Modifier** en bas.

6. Faites défiler vers le bas de l’écran et cliquez sur le bouton **Créer un certificat** dans la section **Développement pousser des certificats SSL**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    L’assistant « Ajouter iOS certificat » s’affiche.

    > [AZURE.NOTE] Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l’inscription d’un certificat de production. Assurez-vous que vous utilisez le même type de certificat lors de l’envoi de notifications.

7. Cliquez sur **Fichier**, recherchez l’emplacement où vous avez enregistré le conseiller du service clientèle de votre certificat d’émetteur. Puis cliquez sur **Générer**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. Une fois que le certificat est créé par le portail, cliquez sur le bouton **Télécharger** .

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Il télécharge le certificat de signature et l’enregistre sur votre ordinateur dans le dossier Téléchargements.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] Par défaut, le fichier téléchargé un certificat de développement est nommé **aps_development.cer**.

9. Double-cliquez sur certificat de push téléchargé **aps_development.cer**. Cela installe le nouveau certificat dans la chaîne de clé, comme indiqué ci-dessous :

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] Le nom de votre certificat peut être différent, mais il sera précédé de **développement d’Apple iOS Push de Services :**.

10. Dans le trousseau d’accès, cliquez sur le nouveau certificat d’émission que vous venez de créer dans la catégorie des **certificats** . Cliquez sur **Exporter**, nommez le fichier, sélectionnez le format **.p12** , puis cliquez sur **Enregistrer**.

    N’oubliez pas que le nom de fichier et l’emplacement du certificat exporté .p12 push. Elle servira à activer l’authentification avec APNS en le téléchargeant sur le portail classique d’Azure.



####<a name="create-a-provisioning-profile-for-the-app"></a>Créer un profil de mise en service de l’application

1. Dans <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>, sélectionnez les **Profils de mise en service**, sélectionnez **tout**, puis cliquez sur le **+** pour créer un nouveau profil. Cette action lance l’Assistant **Ajouter iOS profil de Provisiong**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Sélectionnez le type de profil de provisiong **iOS, développement d’applications** en cours de **développement** , puis cliquez sur **Continuer**.


3. Ensuite, sélectionnez l’ID d’application que vous venez de créer dans la liste déroulante **ID de l’application** , puis cliquez sur **Continuer**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. Dans l’écran **Sélectionner les certificats** , sélectionner votre certificat de développement utilisé pour la signature de code et cliquez sur **Continuer**. Il s’agit d’un certificat de signature, pas le certificat envoyé que vous venez de créer.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Ensuite, sélectionnez les **périphériques** à utiliser pour le test, puis cliquez sur **Continuer**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Enfin, choisissez un nom pour le profil dans la zone **Nom de profil**, cliquez sur **Générer**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
