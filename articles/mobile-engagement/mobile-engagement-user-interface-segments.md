<properties 
   pageTitle="Interface utilisateur de l’Engagement de Mobile Azure - Segments" 
   description="Découvrez comment créer et gérer les segments d’utilisateurs afin d’identifier des modèles d’utilisation à l’aide d’Azure Mobile Engagement" 
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

# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Comment créer et gérer les segments d’utilisateurs afin d’identifier des modèles d’utilisation

Cet article décrit l’onglet **SEGMENTS** du portail **Mobile Engagement** . Le portail **Mobile Engagement** vous permet de surveiller et de gérer vos applications mobiles.

La section des Segments de l’interface utilisateur vous permet de travailler sur la segmentation de vos utilisateurs sur la base un comportement différent et analytique que vous pouvez obtenir à partir de l’application et est également accessible via l’API de Segments. Segments sont calculées tout d’abord de 24 heures après leur création, et ils sont recalculés toutes les 24 heures basé sur les dernières informations d’analytique. Une fois qu’un segment est calculé, il affiche un graphique de « Jour dans l’historique du jour » à chaque jour.


>[AZURE.NOTE] De nombreuses sections du portail **Mobile Engagement** interface utilisateur contiennent le bouton **Afficher l’aide** . Appuyez sur ce bouton pour obtenir des informations contextuelles sur une section.

## <a name="create-segments"></a>Créer des Segments
Vous pouvez créer un segment basé sur jusqu'à 10 critères sur une période donnée jusqu'à 60 jours dans le passé à partir de la section analytique. Par exemple, vous pouvez créer un segment basé sur les personnes qui ont certaines pages consultées ou rechercher un contenu spécifique dans votre application au cours des 10 derniers jours. Ces informations sont disponibles dans la section analytique. Ainsi, vous pouvez l’utiliser pour créer un segment et ensuite configurer une notification de transmission pour cibler ce sous-ensemble d’utilisateurs pour les faire revenir à l’application. 
 
> Remarque : Une fois qu’un segment a été calculé, il ne peut pas être modifié ; Il peut uniquement être cloné (copié) ou détruit (deleted). Un segment peut être cloné dans la même application (avec la même AppID), et il peut également être clonée dans d’autres applications (avec un AppID différent). 
 
 ![segments1][35] 

## <a name="examples-segments"></a>Segments d’exemples
 ![segments2][36]

Segments vous permettent de segmenter les utilisateurs finaux de votre application.
Segmentation de vos utilisateurs est une importante stratégie de marketing. Azure Engagement de Mobile vous permet d’obtenir des données historiques et de créer des segments personnalisés. Ce puissant outil vous permet d’en savoir plus sur l’expérience de vos clients dans votre application. Vous pouvez facilement analyser vos segments et utiliser des segments en tant que cibles de push.
Un cas d’usage commun est que vous souhaitez envoyer une campagne une notification pour encourager les utilisateurs à évaluer votre application dans le magasin. Plutôt que d’envoyer une notification à tous les utilisateurs finaux, vous pouvez créer un segment qui serait de spécifier uniquement les utilisateurs qui ont utilisé le votre application tous les jours du mois dernier et ont un utilisateur formidable expérience. Lorsque vous envoyez des notifications de transmission moins, hautement ciblées, vous obtenez un meilleur retour sur investissement.
 
 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Vous pouvez créer des segments basées sur les principaux éléments de l’Engagement de Mobile Azure :
- Événement : créez un segment cet événement spécifique une cible de l’application qui s’est passé de plus de deux fois par semaine. 
- Session : créez un segment d’utilisateurs qui ont utilisé l’application plus de 5 fois la semaine dernière.
- Activité : créez un segment d’utilisateurs qui ont utilisé une page ou contenu supérieure ou inférieure à 10 fois le mois dernier.
- Tâche : créer un segment d’utilisateurs qui ont effectué un travail plus de deux fois par jour.
- Arrêt : créer un segment de tous les utilisateurs qui ont eu une panne de plus de 10 fois la semaine dernière. (Impossible de pousser ce segment avec un excuses ou même un coupon !)
- Erreur : créez un segment de tous les utilisateurs qui ont eu une erreur plus de 100 fois 3 jours.
- App Info : créer un segment qui cible un App Info personnalisée qui s’est produite pendant 25 jours.
 
 ![segments4][38]

Pour utiliser le Segment de façon optimale, vous devez effectuer une intégration personnalisée du Kit de développement de votre application avec un plan de marquage de balises de « App Info ».
Ensuite, passez à la page d’accueil de l’interface, sélectionnez l’application que vous souhaitez et cliquez sur la section « Segments ».

1. Sélectionnez la section « Segments ».
2. Cliquez sur « nouveau segment » bouton pour créer un nouveau segment.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Exemple de vie réel : Créer un segment simple basé sur les informations de la « Session »
Créez un segment de tous les utilisateurs finaux que vous avez utilisé votre application au moins 50 fois la semaine dernière. À partir de là, recherche uniquement les utilisateurs finaux qui ont passé au moins 30 secondes dans votre application par session. Ceci affiche tous les utilisateurs finals qui ont une expérience positive dans votre application. Ensuite, le segment créé peut servir à une notification d’émission à ces utilisateurs finaux pour leur demander d’évaluer votre application dans le magasin.
 
 ![segments5][39]

1. Nommez votre segment afin de trouver rapidement dans la liste « Segment ».
2. Cliquez sur le bouton « Créer ».
 
 ![segments6][40]

Sélectionnez la Session.
 
 ![segments7][41]

1. Sélectionnez la période de « Semaine dernière ».
2. Cliquez sur Suivant.
 
 ![segments8][42]

1. Sélectionnez l’opérateur approprié dans la liste : = ; ≥, ≤.
2. Entrez le nombre souhaité.
3. Sélectionnez l’Occurrence. 
4. Cliquez sur Suivant.
Cet exemple est défini sur la dernière semaine, correspondant aux utilisateurs qui ont effectué au moins 50 sessions.
 
 ![segments9][43]

Pour la segmentation de la Session, vous pouvez choisir la longueur par session comme critère.

1. Sélectionnez un opérateur dans la liste.
2. Fournir la longueur par session.
3. Cliquez sur Suivant.
Dans cet exemple, il indique que sur toutes les sessions qui ont été placés sur la section de l’occurrence, sélectionnez uniquement les utilisateurs qui ont dépensé plus de 30 secondes par session.
 
 ![segments10][44]

Nommez votre critère pour le récupérer dans l’ampoule à décanter complète et cliquez sur Terminer.
 
 ![segments11][45]

Lorsque vous avez défini votre critère, il apparaîtra dans l’ampoule à décanter de segment.
Un segment est basé sur les données d’analytique, les segments sont calculées une fois par jour.
Dans cet exemple, 47,7 % des utilisateurs finaux total correspond au critère. Ils doivent être des utilisateurs qui ont une bonne expérience et seront susceptibles de donner un classement plus élevé si vous les poussez une notification leur demandant d’évaluer l’application dans le magasin.


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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
