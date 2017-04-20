<properties 
   pageTitle="Interface utilisateur de l’Engagement de Mobile Azure - campagne de portée" 
   description="Laern comment faire pour créer et gérer des notification de transmission des campagnes à l’aide d’Azure Mobile Engagement" 
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


# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Comment faire pour créer et gérer des campagnes de notification de transmission
Vous pouvez utiliser la section de la portée de l’interface utilisateur pour créer une nouvelle campagne de Push avec une formule complexe en fournissant toutes les informations dont vous avez besoin d’envoyer une notification de transmission. Les options d’une campagne de Push varient légèrement selon les types de campagnes de quatre : annonces, sondages, exécute un push de données et de mosaïques (Windows Phone uniquement).

### <a name="option-applies-to"></a>Option s’applique à :
- Langues : Toutes les (annonces, sondages, données push, mosaïques)
- Campagne : Tous les (annonces, sondages, données push, mosaïques)
- Notification : Les annonces, les sondages
- Contenu : Unique pour chaque type de campagne
- Public : Tous les (annonces, sondages, données push, mosaïques)
- Délai : annonces, sondages, mosaïques
- Test : Toutes les (annonces, sondages, données push, mosaïques)
 
![Portée-Campaign1][20]

## <a name="languages"></a>Traduction
Vous pouvez utiliser la liste déroulante langues pour envoyer une version différente de votre campagne à des périphériques qui sont configurés pour utiliser des langues différentes. Par défaut, tous les périphériques recevra la même diffusion quelle que soit la langue qu’ils sont définis pour utiliser. Les utilisateurs de leurs périphériques définis pour une langue différente recevra la version de langue par défaut de l’émetteur. La plupart des options push campagne vous permettent de vous permet de spécifier un autre contenu pour chacune des langues supplémentaires que vous sélectionnez. 
 
![Portée-Campaign2][21]

### <a name="language-differences-apply-to"></a>Différences de langage s’appliquent à :
- Langues : Langues Unique peuvent être sélectionnées en plus de la langue par défaut
- Campagne : Identique pour toutes les langues
- Notification : Uniques pour chaque langue en plus de la langue par défaut
- Contenu : Uniques pour chaque langue en plus de la langue par défaut
- Public : Peuvent être filtrés par un critère de langage distinct
- Délai : même pour toutes les langues
- Test : Peut être envoyé pour chaque langue à la fois
 
### <a name="supported-languages"></a>Langues prises en charge :
- Arabe (ar) 
- Bulgare (bg) 
- Catalan (ca) 
- Chinois (zh) 
- Croate (hr) 
- Czech (cs) 
- Danois (da) 
- Néerlandais (nl) 
- Anglais (en) 
- Finnois (fi) 
- Français (fr) 
- Allemand (de) 
- Grec (el) 
- Hébreu (he) 
- Hindi (Bonjour) 
- Hongrois (hu) 
- Indonésien (id) 
- Italien (it) 
- Japonais (ja) 
- Coréen (ko) 
- Letton (lv) 
- Lituanien (lt) 
- Malais (macrolanguage) (ms) 
- Norvégien Bokmål (nb) 
- Polonais (pl) 
- Portugais (pt) 
- Roumanie (ro) 
- Russe (ru) 
- Serbe (sr) 
- Slovaque (sk) 
- Slovène (sl) 
- Espagnol (es) 
- Suédois (sv) 
- TAGALOG (tl) 
- Thaï (th) 
- Turc (tr) 
- Ukrainien (Royaume-Uni) 
- Vietnamien (vi) 
 
## <a name="campaign"></a>Campagne
Vous pouvez utiliser la section campagne pour définir le nom et la catégorie de votre campagne, ainsi que si vous souhaitez ignorer la section public de la campagne Push et envoyer cette campagne via l’API atteindre (et certains éléments avec le Push des API de bas niveau) au lieu de cela. Catégories peuvent être utilisées avec un modèle de notification personnalisée pour les notifications dans l’application de contrôle en fonction de paramètres prédéfinis. Vous pouvez obtenir la liste de vos « catégories » via l’API d’atteindre.

> Avertissement : Si vous utilisez l’option « Ignorer public, push sera envoyé aux utilisateurs via l’API » dans la section « Campagne » d’une campagne de portée, la campagne n’enverra pas automatiquement, vous devrez envoyer manuellement via l’API d’atteindre.
 
![Portée-Campaign3][22]
 
### <a name="option-applies-to"></a>Option s’applique à :
- Nom : tous les
- Catégorie : Annonces, les sondages
- Ignorer l’Audience, push sera envoyé aux utilisateurs via l’API : tous les
 
## <a name="notification"></a>Notification
Vous pouvez utiliser la section Notification pour définir des paramètres de base pour votre commande, y compris : le titre de la campagne, le message, une image dans l’application, ou s’il s’agit de Halo. De nombreux paramètres de notification sont spécifiques à la plate-forme de votre périphérique. Vous pouvez choisir votre commande sera envoyé « dans l’application » ou « application » ou les deux. (N’oubliez pas que les utilisateurs peuvent « opt-in » ou « opt-out » de « application de » pousse au niveau du système d’exploitation au niveau sur leurs périphériques, et Azure Mobile Engagement ne sera pas en mesure de remplacer ce paramètre. N’oubliez pas également que l’API atteindre gère « dans l’application » et exécute un push de le « out d’application ». L’API de transmission peut servir à gérer trop d’insuffisante"app » push.) Push peut être personnalisée avec des images ou du contenu HTML, y compris les liens complètes pour la liaison en dehors de votre application ou vers un autre emplacement dans votre application (SDK Android 2.1.0 ou ultérieure catégories intention requis). Vous pouvez modifier l’icône ou iOS badge et envoyer du contenu web ou de texte (une fenêtre contextuelle avec html, URL lien contenu dans un autre emplacement à l’intérieur ou à l’extérieur de l’application). Vous pouvez également rendre circulaire des appareils Android ou vibration avec la diffusion. (Gardez à l’esprit que vous devez les autorisations du Kit de développement logiciel dans votre Android du fichier pour l’anneau ou vibrer un dispositif manifeste.) Il n’existe actuellement aucun secteur industriel standard pour les formats de Android « présentation générale », dans la mesure où les tailles d’écran sont différentes sur chaque périphérique, mais 400 x 100 images fonctionnent sur presque n’importe quelle taille de l’écran.

### <a name="delivery-types"></a>Types de livraison :
-    En dehors de l’application uniquement : la notification est envoyée lorsque l’utilisateur n’utilise pas l’application.
- L’extérieur de la seule notification d’application nécessite un certificat à partir d’Apple ou Google (APNS ou GCM le certificat).
- Dans l’application uniquement : la notification s’affiche uniquement lorsque l’application est en cours d’exécution.
- La notification utilise le système de livraison Capptain pour accéder à l’utilisateur. Vous pouvez entièrement personnaliser la mise en page/Affichage visuel de votre campagne.
- À tout moment : Cette option permet de s’assurer que vous envoyez une notification à que chaque exécution de l’application ou non.

 
![Portée-Campaign4][23]

### <a name="option-applies-to"></a>Option s’applique à :
- Notification : Les annonces, les sondages
 
## <a name="content"></a>Contenu
Vous pouvez utiliser la section contenu pour modifier le contenu de vos annonces, sondages, exécute un push de données et de mosaïques (Windows Phone uniquement). Le paramètre de contenu de campagnes de Push est spécifique au type de campagne. 

### <a name="see-also"></a>Voir aussi
- [Documentation de l’interface utilisateur - atteindre - Push de contenu][Link 29]
 
![Portée-Campaign5][24]

## <a name="audience"></a>Public
Vous pouvez utiliser la section Audience pour définir une liste standard des éléments dans votre campagne ou les limites de votre campagne en fonction de critères personnalisés. Le jeu standard des options pour limiter votre public vous permet de distribuer aux utilisateurs de nouveau ou anciens ou push natif uniquement. Vous pouvez également définir un quota pour limiter le nombre d’utilisateurs qui reçoivent la diffusion. Vous pouvez modifier manuellement l’expression de la manière dont votre campagne est filtrée pour inclure un ou plusieurs critères pour cibler les utilisateurs. Vous pouvez taper manuellement l’expression de l’audience. Une telle expression doit définir explicitement la relation entre les critères. Un critère est décrit par un identificateur doit commencer par une lettre majuscule et ne peut pas contenir d’espaces. La relation entre les critères peut être décrits à l’aide de 'et', 'ou', 'non' opérateurs mais aussi '(',')'. Exemple : « Criterion1 ou (Criterion1 et non Criterion2) ».

> Remarque : Avec un large public inclus dans campagnes marketing, ciblage d’analyse côté serveur peut être lent, surtout si vous essayez de démarrer plusieurs campagnes en même temps.

- Si possible, ne démarrer une campagne à la fois.
- Ne démarrer au maximum, quatre campagnes à la fois.
- Push uniquement aux utilisateurs actifs (case à cocher « engager uniquement les utilisateurs qui peuvent être atteint à l’aide de Push natif » et « uniquement les utilisateurs actifs ») afin qu’uniquement les utilisateurs qui ont l’application installée et l’utiliser quand même devront à analyser.
Une fois votre public est défini, vous pouvez utiliser le bouton simuler pour savoir combien d’utilisateurs recevront cette transmission. Cela va calculer le nombre d’utilisateurs connus, potentiellement ciblées par ce public (il s’agit d’une estimation basée sur un échantillon aléatoire des utilisateurs). N’oubliez pas que les utilisateurs qui ont désinstallé l’application font également partie de ce public, mais ne peut pas être atteint.

### <a name="see-also"></a>Voir aussi
- [Documentation - portée - de l’interface utilisateur de nouveau critère de Push][Link 28]

![Portée-Campaign6][25]

### <a name="edit-expression"></a>Modifier l’expression
![Portée-Campaign7][26]
 
### <a name="limit-your-audience-option-applies-to"></a>Limite de : que l' option public s’applique :
- S’engager uniquement un sous-ensemble d’utilisateurs : tous les (annonces, sondages, exécute un push de données, mosaïques)
- Engager des anciens utilisateurs uniquement : toutes les (annonces, sondages, exécute un push de données, mosaïques)
- Engager des nouveaux utilisateurs uniquement : toutes les (annonces, sondages, exécute un push de données, mosaïques)
- Engager les utilisateurs inactifs uniquement : annonces, sondages, mosaïques
- S’engager uniquement les utilisateurs actifs : tous les (annonces, sondages, exécute un push de données, mosaïques)
- S’engager uniquement les utilisateurs qui peuvent être atteint à l’aide de Push natif : annonces, les sondages
 
## <a name="time-frame"></a>Intervalle de temps
Vous pouvez utiliser la section délai pour définir lors de l’envoi doit être envoyé ou vous pouvez laisser vide pour lancer la campagne immédiatement le laps de temps. N’oubliez pas qu’à l’aide du fuseau horaire de l’utilisateurs finaux peut démarrer la campagne un jour plus tôt que prévu pour les utilisateurs finaux en Asie et envoyer de petits lots d’instructions push à la fois jusqu'à ce que tous les fuseaux horaires dans le monde correspondent à l’intervalle de temps défini pour votre campagne. À l’aide du fuseau horaire de l’utilisateur final peut également entraîner des retards dans les campagnes car il doit demander l’heure à partir du téléphone avant de commencer la diffusion.

> Remarque : Sans peut mettre en cache une date de fin de campagne pousse localement et toujours affiche les après avoir terminé manuellement les campagnes. Pour éviter ce problème, spécifique à une heure de fin pour les campagnes.

### <a name="see-also"></a>Voir aussi
- [Atteindre - comment OT – planification][Link 3] 
 
![Portée-Campaign8][27]

### <a name="settings-apply-to"></a>Les paramètres s’appliquent à :
- Délai : annonces, sondages, mosaïques
 
## <a name="test"></a>Test
Vous pouvez utiliser la section Test pour envoyer cette commande au périphérique de test avant d’enregistrer la campagne. Si vous avez configuré des langues personnalisées pour cette campagne, vous pouvez tester la diffusion dans chaque langue. Vous pouvez configurer un périphérique de test à partir de « Mon compte ».
> Exécute un push de la Remarque : Aucun côté serveur que les données sont enregistrées lorsque vous utilisez le bouton « tester », seulement les données sont enregistrées pour les campagnes de transmission réel.

### <a name="see-also"></a>Voir aussi
- [Documentation de l’interface utilisateur - mon compte][Link 14]
 
![Portée-Campaign9][28]

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
 
