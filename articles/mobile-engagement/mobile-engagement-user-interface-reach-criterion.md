<properties 
   pageTitle="Interface utilisateur de l’Engagement de Mobile Azure - critère de portée" 
   description="Apprenez à utiliser des critères de ciblage pour envoyer des campagnes de push pour sélectionner un sous-ensemble de vos utilisateurs à l’aide d’Azure Mobile Engagement" 
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


# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Comment faire pour utiliser des critères de ciblage pour envoyer des campagnes de push pour sélectionner un sous-ensemble de vos utilisateurs

Cibler votre audience par critères spécifiques avec le bouton « Nouveau critère » est un des concepts plus puissants dans Azure Mobile vous permet de vous envoyez pertinentes des notifications de transmission que les clients répondra au lieu de tout le monde l’arrosage du réseau. Vous pouvez limiter votre audience en fonction de critères standard et simuler push pour déterminer le nombre de personnes qui recevra la notification.

**Voir aussi :**

- [Documentation - Reach - de l’interface utilisateur de nouvelle campagne de Push][Link 27]

## <a name="audience-criteria-can-include"></a>Critères d’audience peuvent inclure :
- **Technicals :** Vous pouvez cibler basé sur les mêmes informations techniques que vous pouvez voir dans les sections Analytique et moniteur. **Voir aussi :** [Documentation de l’interface utilisateur - Analytique] [ Link 15], [Documentation de l’interface utilisateur - Moniteur][Link 16]
- **Emplacement :** Les applications qui utilisent le « rapport d’emplacement en temps réel » avec gardiennage peuvent utiliser géolocalisation comme un critère pour cibler une audience à partir de l’emplacement du GPS. « Paresseux zone emplacement Reporting » appel également être utilisé pour cibler une audience à partir de l’emplacement du téléphone portable (« rapport d’emplacement en temps réel » et « Paresseux zone emplacement Reporting » doivent être activée dans le Kit de développement). **Voir aussi :** [Documentation du Kit de développement logiciel - e/s - intégration] [ Link 5], [Intégration - Android - Documentation de kit de développement logiciel][Link 5]
- **Atteindre des commentaires :** Vous pouvez cibler votre audience en fonction de leurs commentaires à partir des notifications de portée précédentes par le biais de commentaires de portée à partir des annonces, des sondages et exécute un push de données. Cela vous permet de mieux cibler votre audience après deux ou trois atteignent des campagnes que vous pourriez le faire la première fois. Il peut également être utilisé pour filtrer les utilisateurs qui ont déjà reçu une notification ayant un contenu similaire, en définissant une campagne marketing de ne pas être envoyés aux utilisateurs qui ont déjà reçu une campagne précédente spécifique. Vous pouvez même exclure les utilisateurs qui sont inclus à une campagne spécifique qui est encore active à partir de la réception de nouvelles campagnes. **Voir aussi :** [Documentation de l’interface utilisateur - atteindre - Push de contenu][Link 29]
- **Installer le suivi :** Vous pouvez effectuer le suivi des informations basées sur lequel vos utilisateurs installé votre application. **Voir aussi :** [Documentation de l’interface utilisateur - paramètres][Link 20]
- **Profil utilisateur :** Vous pouvez cibler standard en fonction des informations utilisateur et vous pouvez cible basée sur les informations d’application personnalisée que vous avez créé. Cela inclut les utilisateurs qui sont connectés et les utilisateurs qui ont répondu à des questions spécifiques que vous avez demandé à définir dans l’application elle-même au lieu de simplement comment ils ont répondu à des campagnes précédentes. Toutes des informations application défini pour votre application s’affichent dans cette liste.
- Segments : Vous pouvez également cible basée sur les segments que vous avez créé en fonction de comportement utilisateur spécifique contenant plusieurs critères. Tous vos segments définis pour votre application s’affichent dans cette liste. **Voir aussi :** [Documentation de l’interface utilisateur - Segments][Link 18]
- **App Info :** Balises d’Info App personnalisées peuvent être créées à partir de « Paramètres » pour suivre le comportement de l’utilisateur. **Voir aussi :** [Documentation de l’interface utilisateur - paramètres][Link 20]

## <a name="example"></a>Exemple : 
Si vous souhaitez envoyer une annonce uniquement pour l’ensemble secondaire de vos utilisateurs que vous avez effectué une action dans l’application fournisseur.

1. Accédez à la page de paramètres d’application et sélectionnez le menu « App info » « Nouvelle app info »
2. Enregistrer une nouvelles informations app booléenne appelée « inAppPurchase »
3. Rendre votre application définir les informations de cette application à « true » lorsque l’utilisateur exécute avec succès un achat dans l’application (à l’aide de la sendAppInfo ("inAppPurchase",...) fonction)
4. Si vous ne voulez pas le faire à partir de votre application, vous pouvez le faire à partir de votre serveur principal à l’aide de l’API de l’appareil)
5. Ensuite, vous souhaitez de créer votre annonce, avec un critère limitant votre public pour les utilisateurs possédant des « inAppPurchase », la valeur « true »)
 
> Remarque : Ciblage en fonction de critères autres que les balises app info nécessite l’Engagement de Mobile Azure rassembler des informations à partir des périphériques de vos utilisateurs avant l’envoi de la campagne et peut provoquer un retard. Exécute un push de configuration complexe push options (telles que la mise à jour des badges) peuvent également différer. À l’aide d’une campagne « une seule fois » à partir de l’API de transmission est la méthode la plus rapide absolu push dans Azure Mobile Engagement. À l’aide uniquement les balises app info en tant que critères de transmission pour une campagne portée (soit à partir de l’API d’atteindre ou de l’interface utilisateur) d’est suivant la méthode la plus rapide car les balises app info sont stockés sur le serveur. À l’aide d’autres critères de ciblage pour une campagne de push d’est la méthode par diffusion, plus lente mais plus flexible car Azure Mobile Engagement doit interroger les périphériques pour envoyer la campagne.
 
![Portée-Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Les Options de critère s’appliquent à :
- **Technicals**     
- Nom du microprogramme : nom du microprogramme
- Version du microprogramme : version du microprogramme
- Modèle de périphérique : modèle de périphérique
- Fabricant : fabricant du périphérique
- Version de l’application : version de l’Application
- Nom du transporteur : nom du transporteur, non définie
- Pays de la porteuse : pays de transporteur, non définie
- Type de réseau : type de réseau
- Paramètres régionaux : paramètres régionaux
- Taille de l’écran : taille de l’écran
- **Emplacement**      
- Dernière connue zone : pays, région, localité
- Gardiennage en temps réel : liste des POIs (nom, Actions), de la circulaire de POI (nom, Latitude, Longitude, Radius en mètres)
- **Atteindre les commentaires**     
- Commentaires de l’annonce : annonces, commentaires
- Interroger des commentaires : interrogation, commentaires
- Interroger les commentaires de réponse : interroger les commentaires de réponses, question, choix
- Commentaires de transmission de données : données Push, commentaires
- **Installation du suivi**     
- Magasin : Magasin, non défini
- Source : Source, non définie
- **Profil d’utilisateur**     
- Sexe : mâle ou femelle, non définie
- Date de naissance : opérateur, la date, non définie
- Opt-in : vrai ou faux, non défini
- **App Info**      
- Chaîne : Chaîne, non définie
- Date : opérateur, la date, non défini
- : Opérateur de nombre entier, non définie
- Valeur booléenne : true ou false, non définie
- **Segment**    
- Nom de Segments (à partir de la liste déroulante), Exclusion (cibler les utilisateurs qui ne font pas partie de ce segment).

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
 
