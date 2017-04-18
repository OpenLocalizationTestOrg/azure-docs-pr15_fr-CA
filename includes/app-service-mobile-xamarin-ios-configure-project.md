####<a name="configuring-the-ios-project-in-xamarin-studio"></a>Configuration du projet d’e/s dans un Studio de Xamarin

1. Dans Xamarin.Studio, ouvrez **Info.plist**et mettre à jour l' **Identificateur de l’offre groupée** avec l’ID de lot que vous avez créé précédemment avec votre nouveau code d’application.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Faites défiler **Les Modes en arrière-plan** et cochez la case **Activer les Modes de fond** et la zone de **notifications à distance** . 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Double-cliquez sur votre projet dans le panneau de la Solution pour ouvrir les **Options de projet**.

4.  Cliquez sur **iOS offre groupée signature** sous **génération**et sélectionnez correspondant **identité** et **Provisioning profil** que vous venez de définir pour ce projet. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    Cela garantit que le projet utilise le nouveau profil de signature de code. Pour le périphérique Xamarin officiel, mise en service de la documentation, reportez-vous à la section [Xamarin la configuration de périphériques].

####<a name="configuring-the-ios-project-in-visual-studio"></a>Configuration du projet d’iOS dans Visual Studio

1. Dans Visual Studio, cliquez droit sur le projet, puis cliquez sur **Propriétés**.

2. Dans les pages de propriétés, cliquez sur l’onglet **d’e/s d’Application** et mettre à jour l' **identificateur** avec l’ID que vous avez créé précédemment.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. Dans l’onglet **e/s de la signature de l’offre groupée** , sélectionnez correspondant **identité** et **Provisioning profil** que vous venez de définir pour ce projet. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Cela garantit que le projet utilise le nouveau profil de signature de code. Pour le périphérique Xamarin officiel, mise en service de la documentation, reportez-vous à la section [Xamarin la configuration de périphériques].

4. Double-cliquez sur Info.plist pour l’ouvrir et puis activer **RemoteNotifications** sous Modes de l’arrière-plan. 



[Périphériques Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/