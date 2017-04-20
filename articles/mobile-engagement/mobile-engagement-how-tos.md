<properties 
   pageTitle="Interface utilisateur de l’Engagement de Mobile Azure - portée comment"
   description="Présentation de l’Interface utilisateur pour Azure Engagement Mobile" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Comment démarrer l’utilisation et la gestion d’exécute un push d’accéder à vos utilisateurs finaux

Une fois le Kit de développement logiciel est totalement intégré à votre application, vous pouvez commencer à utiliser la la section de la portée de l’interface utilisateur pour les notifications de type Pousser aux utilisateurs de votre application.  

## <a name="do-your-first-push-notification-campaign"></a>Effectuez votre première campagne de Notification de transmission
-    Confirmez que votre présence est intégré dans votre application avec le Kit de développement logiciel. 
-    Sélectionnez votre application
 
![First1][1]

-    Accédez à la Section de « Atteindre » puis cliquez sur « nouvelle annonce »
 
![First2][2]

-    Créer une campagne et nommez-le
 
 ![First3][3]

-    Sélectionnez la façon dont la notification doit être remise, comme dans l’application uniquement
 
![First4][4]

-    Créer le message que vous souhaitez distribuer
 
![First5][5]

-    Vous pouvez écrire un titre sur la notification (facultatif).
-    Écrire le contenu du message envoyé.
-    Vous pouvez télécharger une image. N’oubliez pas que la taille du fichier ne peut pas dépasser 32 768 octets.
-    Vous avez également la possibilité de sélectionner d’autres options, mais pour l’objectif de ce didacticiel, nous verrons que plus tard.

-    Sélectionnez le type de contenu de Notification uniquement
 
![First6][6]

-    Créer votre campagne push et il s’affiche dans votre liste des campagnes.
 
![First7][7]

## <a name="test-your-push-notification-campaign"></a>Tester votre campagne de Notification de transmission
![Test1][8]

-    Inscrire votre appareil.
-    Cliquez sur la case à cocher du périphérique que vous voulez distribuer.
-    Cliquez sur le bouton « Test » pour envoyer la commande au périphérique.
 
![Test2][9]

-    Activez la campagne
 
![Test3][10]

-    Maintenant que vous avez créé votre campagne, que vous devez l’activer pour la notification vers vos utilisateurs.
 
## <a name="send-personalized-pushes"></a>Envoyer push personnalisés
-    Cet exemple crée un « push » où un code personnalisé de remise est entré dans la notification de transmission.
 
![Personalize1][11]

Works de personnalisation en remplaçant un marqueur par une application d’info donc, vous devrez vous assurer que l’utilisateur a le bon app-info défini en premier. Dans cet exemple, les utilisateurs ciblés auront une balise d’info application nommée rebate_code définie.
Comme vous le voir ci-dessus le contenu de la notification push inclut le $ marqueur {rebate_code}, qui indique qu’il est remplacé par le contenu de la balise d’info app.

> Avertissement : Si la balise info app n’est pas définie pour l’utilisateur, l’utilisateur recevra pas la diffusion.

-    Résultat
 
![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Vous pouvez personnaliser le texte de votre notification
![Personalize3][13]

-    Y compris le titre de la notification,
-    Et le contenu du message.
-    Choisissez le type de l’annonce (affichage de texte ou en mode Web)
 
![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Le corps d’une annonce peut également être personnalisé avec :
-    L’URL de l’action, si vous souhaitez personnaliser la page d’accueil
-    Le titre,
-    Le corps du message.
 
 
## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Différencier votre Notification de transmission (dans ou en dehors de l’application)
-    Choisissez le type de notification push, sélectionnez votre application, reportez-vous à la section « Joint », sélectionnez ou créez une campagne de push et passez à la section « Notification ».
 
-    Cliquez sur « mode de livraison » de votre choix.
-    Cliquez sur la case à cocher « Restreindre les activités » lorsque vous souhaitez que la notification se produit sur des activités spécifiques (écrans).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Mode de livraison « De l’application »
![Differentiate2][16]

Insuffisante"App » mode de livraison fournit une notification de transmission lors de la fermeture de l’application. Il s’agit de la notification de la transmission standard.
Lorsque vous sélectionnez « de l’application », vous avez devez déjà fourni les certificats à partir de la plate-forme de création de votre application (APNS ou GCM).

### <a name="see-also"></a>Voir aussi
-  [Apple Push Notification Service – certificats](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Google Cloud messagerie – certificat] (http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>« dans l’application uniquement » mode de livraison
![Differentiate3][17]

Mode de livraison « Dans l’application uniquement » fournit la notification de transmission lorsque l’application est en cours d’exécution.
Pour cette notification, vous n’avez pas besoin de passer par le système APNS et GCM.
Vous pouvez utiliser le système de livraison dans l’application d’atteindre vos utilisateurs finaux.
Vous pouvez entièrement personnaliser la notification et décider d’apparition des activités (écran) de la notification.

### <a name="anytime-delivery-mode"></a>Mode de livraison « À tout moment »
Vous pouvez choisir un mode de livraison « À tout moment », vous permet de conserver pour atteindre l’utilisateur final si l’application est en cours d’exécution ou non.
Lorsque vous sélectionnez « Tout », vous avez devez déjà fournies les certificats à partir de la plate-forme de la génération de votre application (APNS ou GCM). 
 
## <a name="schedule-a-push-campaign"></a>Planifier une campagne marketing de Push
### <a name="plan-to-start-a-campaign"></a>Envisagez de commencer une campagne
![Shedule1][18]

Il est le 21 mars et vous avez une annonce à faire prévu pour le 22 mars à minuit. Vous n’êtes pas obligé de rester devant l’interface pour mener une campagne ! Vous pouvez planifier à l’avance la minute exacte les notifications seront envoyées.
-    Désélectionnez « None » case à cocher, puis sélectionnez une heure de début 
-    Choisissez la date et l’heure que vous souhaitez démarrer la campagne push.

### <a name="plan-to-end-a-campaign"></a>Plan à la fin d’une campagne
![Shedule2][19]

Vous souhaitez que votre campagne s’achève le 25 mars à 15 h 00, mais vous savez que vous ne serez plus à faire.
Vous n’êtes pas obligé de rester devant l’interface pour pousser ! Vous pouvez planifier à l’avance la minute exacte de que votre campagne s’arrête.
-    Cliquez sur « None » case à cocher ou sélectionner une heure de fin
-    Choisissez la date et l’heure que vous souhaitez terminer la campagne push.

### <a name="end-a-campaign-manually"></a>Terminer une campagne manuellement
![Shedule3][20]

Par défaut, le « None » cases à cocher sont sélectionnées.
Cela signifie que la campagne démarrera dès que vous l’activer dans la section portée et se termine lorsque vous l’arrêtez sur la section de la portée.
 
> Remarque : Les campagnes marketing créées sans date de fin stocke localement de la diffusion sur le périphérique et affiche la prochaine fois que l’application est ouverte, même si la campagne est terminée manuellement.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Améliorer une Notification de transmission avec un affichage de texte
### <a name="what-is-a-text-view"></a>Ce qui est une vue de texte ?
![TextView1][21]

Une vue de texte est une fenêtre contextuelle avec du contenu de texte. Ce menu contextuel s’affiche une fois que l’utilisateur final a cliqué sur la notification de transmission.
Une vue de texte vous permet de présenter davantage de contenu à votre utilisateur final. Il s’agit également de la possibilité de présenter un appel d’action, tels que l’accès à une page de votre application, la redirection vers un magasin, ouverture d’une page web, le courrier électronique, lancer une recherche géo-localisés, etc....

### <a name="example-text-view"></a>Exemple : Affichage de texte
-    Créez votre campagne de notification de transmission dans la section « Accessible » et donnez un nom à votre campagne.
 
![TextView2][22]

-    Écrire le message qui s’affichera sur la notification.
-    Sélectionnez le Type de contenu d’annonce « Text »
 
![TextView3][23]

> Remarque : lorsque vous appuyez sur un affichage de texte, il toujours est fourni avec une notification préalable. 

- Définir le texte (après avoir sélectionné le contenu annonce du texte, la section apparaît, vous permettant de définir le texte que vous souhaitez afficher.)
 
![TextView4][24]

-    Écrire le titre qui apparaîtra en haut du message.
-    Écrire le contenu principal de l’affichage de texte.
-    Écrire le contenu qui s’affichera sur le bouton d’action (bouton d’action permet à l’application effectuer une action spécifique comme l’ouverture d’une page de l’application, la redirection vers un magasin d’application ou de tout type de sources, que vous pouvez fournir).
-    Écrire le contenu qui s’affichera sur le bouton Quitter (en cliquant sur le bouton Quitter, l’affichage de texte disparaît.)
 
-    Créez votre campagne de notification de transmission, et il apparaîtra dans la liste des campagnes.
 
![TextView5][25]

-    Activer votre campagne de notification de transmission pour envoyer l’affichage de texte pour vos utilisateurs.
 
![TextView6][26]

-    Résultat
 
![TextView7][27]

-    L’utilisateur reçoit la notification, puis cliquez dessus.
-    L’affichage de texte s’affiche sous la forme d’un menu contextuel permettant à l’utilisateur d’interagir avec lui.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Améliorer une Notification de transmission comportant un affichage Web
### <a name="what-is-a-web-view"></a>Quel est un affichage Web ?
![WebView1][28]

Un affichage web est une fenêtre contextuelle avec le contenu web. Ce menu contextuel apparaît lorsque l’utilisateur final a cliqué sur la notification de transmission.
Un affichage web vous permet d’avoir plus d’interaction avec l’utilisateur final.
Il s’agit également de la possibilité de présenter un appel à l’action de redirection pour App Store, ouverture d’une page web, courrier électronique, lancer une recherche géo-localisés, etc....

### <a name="example-web-view"></a>Exemple : Affichage de Web
-    Créez votre campagne de transmission dans la section « Atteindre » et donnez un nom à votre campagne.
 
![WebView2][29]

-    Écrire le message qui s’affichera sur la notification.
-    Sélectionnez le Type de contenu de l’annonce en tant que « web »
 
![WebView3][30]

### <a name="about-announcement-types"></a>À propos des types d’annonce :
- Notification uniquement : il s’agit d’une simple notification standard. Ce qui signifie que si un utilisateur clique dessus, aucun affichage supplémentaires ne s’affichent, mais uniquement l’action associée à se produira.
- Annonce du texte : il s’agit d’une notification qui s’engage à l’utilisateur d’un coup de œil à un affichage de texte.
- Annonce du Web : il s’agit d’une notification qui s’engage à l’utilisateur de consulter un affichage web.
Sélectionnez le contenu de « Annonce Web ».

> Remarque : Lorsque vous poussez un affichage web, il toujours est fourni avec une notification préalable.

- Définissez le contenu web (après avoir sélectionné le contenu web annonce la sous-section apparaît, qui vous permet de définir le contenu d’affichage web que vous souhaitez afficher.)

 
![WebView4][31]

-    Écrire le titre qui apparaîtra en haut du message (facultatif).
-    Écrire votre code HTML ici.
-    Cliquez sur la bouton mode pour changer d’édition et de voir comment il ressemble de modification de la source.
-    Écrire le contenu qui s’affichera sur le bouton d’action (bouton d’action permet à l’application effectuer une action spécifique comme l’ouverture d’une page de l’application, à une banque ou tout type de sources, que vous pouvez fournir la redirection).
-    Écrire le contenu qui s’affichera sur le bouton Quitter (en cliquant sur le bouton Quitter, l’affichage web disparaît).
 
-    Résultat
 
![WebView5][32]

-    L’utilisateur reçoit la notification et cliquez dessus.
-    L’affichage de texte s’affiche sous la forme d’un menu contextuel permettant à l’utilisateur d’interagir avec lui.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
