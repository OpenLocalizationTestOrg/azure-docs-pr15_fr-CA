<properties 
   pageTitle="Interface utilisateur de l’Engagement de Mobile Azure - atteindre" 
   description="Apprenez à être en contact avec les utilisateurs de votre application avec des notifications de type pousser à l’aide d’Azure Mobile Engagement" 
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


# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Comment être en contact avec les utilisateurs de votre application avec des notifications de type Pousser

Cet article décrit l’onglet **Atteindre** du portail **Mobile Engagement** . Le portail **Mobile Engagement** vous permet de surveiller et de gérer vos applications mobiles. Notez que pour commencer à utiliser le portail vous devez d’abord créer un compte **Azure Mobile Engagement** . Pour plus d’informations, voir [Création d’un compte Azure Mobile Engagement](mobile-engagement-create.md).

La section de la portée de l’interface utilisateur est l’outil de gestion de campagne de Push dans laquelle vous pouvez créer/modifier/activer/fin/moniteur et obtenir des statistiques sur les campagnes de notification de transmission et les fonctionnalités qui sont également accessibles via l’API atteindre (et certains éléments du pousser des API de bas niveau). Gardez à l’esprit que si vous utilisez les API ou l’interface utilisateur, vous devez intégrer Azure Mobile Engagement et portée dans votre application pour chaque plate-forme avec le Kit de développement avant de pouvoir utiliser atteint des campagnes.

>[AZURE.NOTE] De nombreuses sections du portail **Mobile Engagement** interface utilisateur contiennent le bouton **Afficher l’aide** . Appuyez sur ce bouton pour obtenir des informations contextuelles sur une section.

## <a name="four-types-of-push-notifications"></a>Quatre types de notifications de type Pousser
1.    Annonces - vous permet d’envoyer des messages publicitaires à des utilisateurs qui les rediriger vers un autre emplacement à l’intérieur de votre application ou de les envoyer vers une page Web ou à l’extérieur de votre application. 
2.    Sondages - vous permettent de recueillir des informations à partir des utilisateurs finaux en posant des questions.
3.    Push de données - vous permettent d’envoyer un fichier de données binaires ou base64. Les informations contenues dans une transmission de données sont envoyées à votre application pour modifier l’expérience de vos utilisateurs en cours dans votre application. Votre application doit être en mesure de traiter les données d’une transmission de données.

## <a name="campaign-details"></a>Détails de la campagne

Vous pouvez modifier, dupliquer, supprimer ou activer les campagnes qui n’ont pas encore été activés en plaçant au-dessus de leurs noms, ou vous pouvez cliquer sur pour les ouvrir. Vous pouvez cloner les campagnes qui ont déjà été activées en pointant sur leurs noms ou vous pouvez cliquer sur pour les ouvrir. Toutefois, vous ne pouvez pas modifier une campagne une fois qu’il a été activé.
 
![Reach1][18]

## <a name="reach-feedback"></a>Atteindre les commentaires

Cliquez sur **statistiques** pour afficher les détails d’une campagne de portée. La vue **Simple** fournit une représentation visuelle sous la forme d’un graphique à barres colonne sur ce qui s’est passé après qu’une campagne a été activée. L’affichage **Avancé** offre des détails plus précis sur la campagne push. Ces informations ne sera pas disponibles si vous envoyez une campagne de test c'est-à-dire un push envoyée à un équipement de test. Voici comment vous devez interpréter ces détails :

1. **Pushed** - Spécifie le nombre de messages vers les périphériques. Ce nombre dépend du public cible que vous avez spécifié lors de la création de la campagne de push. Si vous ne spécifiez pas de n’importe quel public cible, puis cette commande va être envoyé aux tous les périphériques inscrits. Comme tous les autres services de type Pousser, nous ne pas distribuer les notifications directement aux périphériques mais plutôt de les pousser à la plate-forme correspondante de Notification Services de transmission spécifique (solution - APNS/GCM/WNS) afin qu’ils peuvent livrer les notifications pour les périphériques. 

2.  **Remis** - Spécifie le nombre de messages qui sont fournis par la solution pour le périphérique et reconnu correctement comme reçu par le Kit de développement Mobile Engagement. 
        
    *Comptent les raisons remis est inférieur au nombre de poussées :*
    
    1. Si l’utilisateur a désinstallé l’application à partir du périphérique, mais la solution ne connaît au moment où que nous envoyer la diffusion à la solution le message va être supprimé.
    2. Si le périphérique possède l’application, mais les périphériques eux-mêmes ont été hors connexion pendant de longues périodes de temps, la solution échoue remettre le message au périphérique. 
    3. Si le message remis au périphérique, mais le contenu du message ne reconnaît pas le Kit de développement Mobile Engagement dans l’application, puis il supprime ce message. Cela peut se produire si la personnalisation de la notification dans l’application génère une exception qui nous catch dans le Kit de développement logiciel et déplacer le message. Cela peut également se produire si l’application sur le périphérique utilise une version du SDK Engagement Mobile qui n’est pas en mesure de comprendre la nouvelle version du message poussé envoyé à partir de la plate-forme, mais ce n’est que lorsque l’application a été mis à niveau après que la notification a été envoyée à partir de la plate-forme de services. L’onglet **Avancé** vous indique combien de messages ont été supprimés. 
    4. Sur les périphériques d’e/s, messages parfois ne pas remis au destinataire si soit le périphérique est sur batterie faible ou si l’application consomme une quantité importante d’énergie lors du traitement des notifications à distance. Il s’agit d’une limitation des périphériques d’e/s.   

3.  **Affiché** - cette option spécifie le nombre de messages qui sont affichés correctement à l’utilisateur de l’application sur le périphérique sous la forme d’une notification de transmission/out-de-app système dans le centre de notification ou une notification dans l’application au sein de l’application mobile.  L’onglet **Avancé** vous indique combien les notifications système et combien sont les notifications dans l’application. 
    
    *Comptent les raisons affichées est inférieure à count remis (en attente à afficher)*
    
    1. Si la campagne notification a une date de fin sur ce qu’il est possible que la notification a été remise mais lorsque le temps est fournie pour l’ouvrir et l’afficher à l’utilisateur de l’application, il a été déjà expiré afin qu’il n’a jamais affiche.   
    2. Si la notification est une notification dans l’application la notification est affichée uniquement lorsque l’utilisateur de l’application ouvre l’application. Dans le cas où l’utilisateur de l’application n’a pas ouvert l’application, le Kit de développement logiciel signale que la notification a été livrée mais pas encore affichée jusqu'à ce que l’application est ouverte. 
    2. Si la notification est une notification dans l’application et configuré pour figurer sur un activité spécifique/écran puis également la notification sera signalée comme livré mais ne pas encore livrées jusqu'à ce que l’utilisateur ouvre l’application sur un écran spécifique. 
    
4.  **Interactions de l’utilisateur** - cette option spécifie le nombre de messages qui interagisse avec l’utilisateur de l’application et inclut les messages qui sont soit activées soit terminé. 

    - *L’utilisateur de l’application peut l’action d’une notification d’une des manières suivantes :*
            
        1. Si la notification est une notification système/out-de-app ou une dans l’application envoyée comme notification uniquement puis l’utilisateur application clique sur la notification.
        2. Si la notification est une notification dans l’application avec un texte ou d’une vue web ou interroge ensuite l’application utilisateur clique sur le bouton d’Action dans la notification.
        3. Si la notification est une notification dans l’application avec un affichage de web puis l’utilisateur application clique sur une URL dans l’affichage web [Android uniquement]
    
    - *L’utilisateur de l’application peut quitter une notification d’une des manières suivantes :*
    
        1. Cliquez sur le bouton Fermer dans la notification directement. 
        2. Passant absent ou la suppression de la notification. 
        3. Contenu texte/web et interroge les notifications dans l’application sont généralement affichées à l’utilisateur de l’application dans un processus en deux étapes. Une notification préalable et pas lorsqu’ils cliquent dessus, ils voient le contenu texte/web/interrogation ultérieur. L’utilisateur de l’application peut quitter une notification dans une de ces étapes et cette capture les détails dans l’affichage avancé. 

5.  **Actioned** - Spécifie le nombre de messages qui ont été explicitement initié par l’utilisateur de l’application. C’est le nombre plus intéressant que cela indique le nombre d’utilisateurs app intéressé par le message que vous poussées dans la notification. 
 
> [AZURE.NOTE] Sur iOS et Windows ouvrir des plates-formes, si l’utilisateur dispose de l’application et la campagne est une campagne « À tout moment », puis il est possible que les applications et les notifications dans l’application sont affichés en même temps. Cela peut entraîner un nombre affiché plus élevé que le remis. Si l’utilisateur interagit ou actions la notification, même le nombre d’Interactions d’utilisateur/Actioned pourrait être supérieur à remis. 


![Reach2][19]

## <a name="see-also"></a>Voir aussi

- [Concepts][Link 6]
- [Service du Guide de dépannage][Link 24]

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
 
