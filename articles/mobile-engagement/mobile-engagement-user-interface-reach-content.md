<properties 
   pageTitle="Interface utilisateur de l’Engagement de Mobile Azure - contenu de portée" 
   description="Apprenez à gérer le contenu unique de différents types de campagnes de notification push lors de l’Engagement Mobile Azure" 
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

# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Comment gérer le contenu unique de différents types de campagnes de notification de transmission
 
Vous pouvez utiliser la section contenu d’une nouvelle campagne de portée pour modifier le contenu de vos annonces, sondages, exécute un push de données et de mosaïques (Windows Phone uniquement). Le paramètre de contenu de campagnes de Push est spécifique au type de campagne. 
 
### <a name="content-types"></a>Types de contenu :
- Annonces
- Sondages
- Données push
- Mosaïques (Windows Phone uniquement)
 
## <a name="content-of-announcements"></a>Contenu des annonces
 ![Portée-Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Choisissez le type de votre annonce :
-    Notification uniquement : il s’agit d’une simple notification standard. Ce qui signifie que si un utilisateur clique dessus, aucun affichage supplémentaires ne s’affichent, mais uniquement l’action associée à se produira.
-    Annonce du texte : il s’agit d’une notification qui s’engage à l’utilisateur d’un coup de œil à un affichage de texte.
-    Annonce du Web : il s’agit d’une notification qui s’engage à l’utilisateur de consulter un affichage web.

### <a name="see-also"></a>Voir aussi
- [Atteindre - comment OT - annonces][Link 3] 

### <a name="about-web-view-announcements"></a>À propos de Web afficher les annonces :
Occurrences du modèle « {deviceid} » dans le code HTML ou le code JavaScript que vous fournissez ici seront automatiquement remplacés par l’identificateur de l’unité d’affichage de l’annonce. Il s’agit d’un moyen simple de récupérer des identificateurs de périphérique Azure Mobile Engagement dans un service web externe hébergé sur votre back-office.
Si vous souhaitez créer une vue de web plein écran (sans Action et quitter les boutons par défaut nous fournir) vous pouvez utiliser les fonctions suivantes à partir de code JavaScript de de votre annonce affichage web : 

-    effectuer l’action de l’annonce : ReachContent.actionContent()
-    sortie de l’annonce : ReachContent.exitContent()
 
### <a name="choose-your-action"></a>L’Action choisie :

### <a name="about-action-urls"></a>À propos des URL d’Action :
Les URL qui peut être interprétée par le système d’exploitation d’un périphérique cible peut être utilisée comme URL d’action.
Dédiés URL que votre application peut prendre en charge (par exemple, pour que les utilisateurs d’accéder à un écran particulier) peut également être utilisé sous la forme d’une URL de l’action.
Chaque occurrence du modèle {deviceid} est automatiquement remplacé par l’identificateur du périphérique de l’action. Cela peut servir à récupérer facilement les identificateurs de périphérique Azure Mobile Engagement via un service web externe hébergé sur votre back-office.

- **Android + iOS actions**
    - Ouvrez une page web
    - http://\[-site-domaine du web\] 
    - Exemple : http://www.azure.com
    - Envoyer un message électronique
    - mailto :\[destinataire de courrier électronique\]?subject =\[objet\]& corps =\[message\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Envoyer un SMS
    - SMS :\[numéro de téléphone\] 
    - Exemple : sms:2125551212
    - Composer un numéro de téléphone
    - Tél :\[numéro de téléphone\] 
    - Exemple : tel:2125551212
- **Actions seulement Android**
    - Télécharger une application sur la banque de lecture
    - Market://details?id=\[package app\] 
    - Exemple : market://details?id=com.microsoft.office.word
    - Lancer une recherche géo-localisés
    - geo:0, 0?q =\[requête de recherche\] 
    - Exemple : geo:0, 0 ? q = starbucks, paris
- **iOS seules actions**
    - Télécharger une application sur l’App Store
    - http://iTunes.Apple.com/ [pays] /app/ [nom de l’application] /id [id app] ? mt = 8 
    - Exemple : http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Actions de Windows
    - Ouvrez une page web
    - http://\[-site-domaine du web\] 
    - Exemple : http://www.azure.com
    - Envoyer un message électronique
    - mailto :\[destinataire de courrier électronique\]?subject =\[objet\]& corps =\[message\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Envoyer un SMS (App Store Skype requis)
    - SMS :\[numéro de téléphone\] 
    - Exemple : sms:2125551212
    - Composer un numéro de téléphone (App Store Skype requis)
    - Tél :\[numéro de téléphone\] 
    - Exemple : tel:2125551212
    - Télécharger une application sur la banque de lecture
    - Microsoft-windows-banque d’informations : PDP?PFN =\[ID de package d’application\] 
    - Exemple : ms-windows-banque : PDP ? PFN = 4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Lancer une recherche bingmaps
    - bingmaps:?q =\[requête de recherche\] 
    - Exemple : bingmaps : ? q = starbucks, paris
    - Utiliser un modèle personnalisé
    - \[modèle personnalisé\]://\[params de modèle personnalisé\] 
    - Exemple : myCustomProtocol://myCustomParams
    - Utiliser une package des données (banque d’App pour l’extension de lecture requis)
    - \[dossier\]\[données\]. \[extension\] 
    - Example:myfolderdata.txt
 
### <a name="build-a-tracking-url"></a>Créer une URL de suivi :
-    Consultez la section « Paramètres » de la <UI Documentation> pour obtenir des instructions sur la création d’une URL de suivi qui permettra aux utilisateurs de télécharger un de vos autres applications.
 
### <a name="define-the-texts-of-your-announcement"></a>Définir le texte de votre annonce
Renseignez le titre, le contenu et le texte de bouton de votre annonce. Vous pouvez cibler une audience d’une campagne de future en fonction de l’évaluation de la portée de la façon dont les utilisateurs répondu à cette campagne. Ciblage de l’audience peut être basé sur le profil d’évaluation si cette campagne a été simplement poussée, réponse, traitement ou terminé.

### <a name="see-also"></a>Voir aussi
- [Documentation - portée - de l’interface utilisateur de nouveau critère de Push][Link 28]

## <a name="content-of-polls"></a>Contenu des sondages
![Portée-Content2][31] remplir dans le titre, la description et le texte du bouton de votre annonce. Ajoutez ensuite des questions et des choix pour les réponses à vos questions.
Vous pouvez cibler une audience d’une campagne de future en fonction de l’évaluation de la portée de la façon dont les utilisateurs répondu à cette campagne. Ciblage de l’audience peut être basé sur si cette campagne a été simplement poussée, réponse, traitement ou terminé. Ciblage de l’audience peut également reposer sur les commentaires de réponse de sondage, où la question et la réponse choix sont utilisés comme critères.

### <a name="see-also"></a>Voir aussi
- [Documentation - portée - de l’interface utilisateur de nouveau critère de Push][Link 28]
 
## <a name="content-of-data-pushes"></a>Exécute un push du contenu de données
![Portée-Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Choisissez le type de vos données :
- Texte
- Données binaires
- Données Base64

### <a name="define-the-content-of-your-data"></a>Définissez le contenu de vos données
- Si vous avez sélectionné des données de texte, copiez et collez le texte dans la zone « contenu ».
- Si vous avez sélectionné les données base64 ou binaire, utilisez le bouton « Télécharger le fichier » pour télécharger votre fichier.
- Vous pouvez cibler une audience d’une campagne de future en fonction de l’évaluation de la portée de la façon dont les utilisateurs répondu à cette campagne. Ciblage de l’audience peut être basé sur si cette campagne a été simplement poussée, réponse, traitement ou terminé.

### <a name="see-also"></a>Voir aussi
- [Documentation - portée - de l’interface utilisateur de nouveau critère de Push][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Contenu des mosaïques (Windows Phone uniquement)
![Portée-Content4][33]

### <a name="define-the-content-of-your-tile"></a>Définissez le contenu de votre mosaïque
La charge de la mosaïque est le texte à afficher dans la mosaïque de votre application sur les périphériques Windows Phone.
Un push de la mosaïque est la version de Microsoft Push Notification Service (MPNS) un push de native pour Windows Phone. Le type de commande mosaïque est le seul type de transmission qui ne dispose pas d’une réponse et donc l’audience des campagnes futures ne peuvent être générée sur les résultats d’une campagne de push de mosaïque. 

### <a name="see-also"></a>Voir aussi
- [Push de Native de Documentation - API de portée - API][Link 4]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
