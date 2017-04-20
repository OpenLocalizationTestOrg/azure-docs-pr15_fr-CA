<properties 
    pageTitle="Utilisez des émoticônes Emoji dans Azure Mobile Engagement" 
    description="Comment faire pour utiliser des émoticônes Emoji dans vos notifications de type Pousser"     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="use-emoji-emoticon-within-push-notifications"></a>Utiliser des émoticônes Emoji dans l’envoi des Notifications

Vous pouvez inclure les Emoji émoticônes dans votre notification d’émission en quelques étapes simples : 

1. Il que vous suffit de rechercher le Emoji d’abord, vous souhaitez envoyer dans le message. Veuillez vous assurer que le Emoji que vous sélectionnez est pris en charge par le périphérique cible que les fabricants de périphériques prennent un certain temps pour ajouter Emojis nouvellement approuvées pour les plates-formes de périphérique. 

2. Sous **Windows** - vous pouvez accéder à ce [lien](http://apps.timwhitlock.info/emoji/tables/unicode) et copiez l’icône 'Native'.

    ![][7] 

3. Sur **Mac** - vous pouvez rechercher que les Emojis dans l’application de dictionnaire sous Edition -> Emoji et symboles.

    ![][6] 

4. Passez maintenant à l’onglet **atteignent** le portail Azure Mobile Engagement. Sélectionnez le type de votre notification de transmission (interroge annonce, etc.). Pour cet exemple, nous choisissons un push de l’annonce.

5. Spécifier les champs différents de la notification jusqu'à ce que vous atteigniez le texte de la notification. Il s’agit de laquelle vous allez ajouter votre émoticône Emoji. Vous pouvez choisir de le placer dans le titre, le message ou les deux. Vous devez faire glisser et déplacer ou copier le Emoji que vous trouvez à partir des emplacements ci-dessus. 

    ![][1]

6. Remplissez les autres champs de la notification et l’enregistrer. 

7. Lorsque vous exécutez un test ou activez l’annonce vous verrez une notification avec l’émoticône comme spécifié.   

    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

