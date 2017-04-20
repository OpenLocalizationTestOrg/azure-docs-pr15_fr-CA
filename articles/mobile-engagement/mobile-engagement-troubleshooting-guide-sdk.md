<properties 
   pageTitle="Engagement de Mobile Azure Guide - Kit de développement logiciel de dépannage" 
   description="Résolution des problèmes d’intégration de SDK dans Azure Mobile Engagement" 
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

# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Guide de dépannage pour les problèmes d’intégration du SDK

Les éléments suivants sont des problèmes possibles que vous pouvez rencontrer avec comment Azure Mobile Engagement s’intègre dans votre application.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Problèmes de kit de développement logiciel découverts par une défaillance dans une autre zone de l’application

### <a name="issue"></a>Problème
- Échec de collection de données IU (en Analytique, analyse, Segmentation ou tableaux de bord).
- Poussez les échecs (push ne fonctionne pas dans l’application, de l’application, ou les deux).
- Avancée des échecs de fonction (suivi de géolocalisation ou plate-forme que pousse spécifique ne fonctionne pas).
- Échec de l’API (API échouent souvent en mode silencieux sans messages d’erreur).
- Échecs du service (aucun Engagement de Mobile Azure fonctionne pour votre application).

### <a name="causes"></a>Causes

- La plupart des problèmes devant être résolus avec l’Engagement de Mobile Azure SDK sera découvert par une panne de votre application (par exemple, un échec de collection de données de l’interface utilisateur, Échec de la transmission, échecs d’une fonction avancée, Échec de l’API, Application tombe en panne ou interruption de service apparente).  
- Si une fonctionnalité particulière d’Azure Mobile Engagement n’a jamais fonctionné dans votre application, vous devez terminer l’intégration. 
- Si une fonctionnalité particulière d’Azure Mobile Engagement fonctionnait et s’est arrêté, vous devrez peut-être mettre à niveau à la dernière version avec l’Engagement de Mobile Azure SDK. N’oubliez pas qu’il existe une version différente de l’Engagement de Mobile Azure SDK pour chaque plate-forme prise en charge par l’Engagement de Mobile Azure (Android, iOS, Windows et Windows Phone).

#### <a name="sdk-integration"></a>Intégration du Kit de développement logiciel

- Engagement de Mobile Azure intégré n’est pas correctement dans le Kit de développement logiciel (Analytique).
- Atteindre n’est pas correctement intégrée dans le Kit de développement logiciel (dans l’application et l’application exécute un push).
- Le certificat a expiré ou incorrecte PROD et DEV (e/s uniquement).
- GCM ou ADM pas correctement intégrée dans le Kit de développement logiciel (Android uniquement - Service spécifique exécute un push).
- Le suivi n’est pas correctement intégré dans le Kit de développement (banque d’installation suivi).
- Emplacement ou les emplacements GPS intégré n’est pas correctement dans le Kit de développement logiciel (ciblage par géolocalisation).


**Voir aussi :**

- [Documentation du Kit de développement logiciel - Guides d’intégration][Link 5] 
- [Guide de dépannage des - Push][Link 23]

#### <a name="sdk-upgrade"></a>Mise à niveau du Kit de développement logiciel

- Vous devez mettre le Kit de développement logiciel pour résoudre des problèmes avec les versions antérieures du Kit de développement (souvent liées à des versions plus récentes du périphérique du système d’exploitation).
- Désinstaller toutes les versions précédentes de votre application à partir de votre périphérique et réinstallez la dernière version de votre application, l’inscrire de nouveau votre ID de périphérique de l’interface utilisateur de Azure Mobile Engagement pour confirmer que votre périphérique à l’aide de la version la plus récente de votre application.

**Voir aussi :**

- [Documentation du kit SDK - Notes de publication](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [Documentation du kit SDK - Guides de mise à niveau](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>Kit de développement logiciel autre

- Erreurs dans le manifeste d’Application « AndroidManifest.xml » peuvent entraîner l’Engagement de Mobile Azure de fonctionner (Android uniquement).
- Un problème courant avec intégration du Kit de développement logiciel et utilisation de l’API est de confondre la clé du Kit de développement logiciel et la clé de l’API.

**Voir aussi :**

- [Concepts - glossaire][Link 6]

## <a name="advanced-coding-issues"></a>Avancée des problèmes de codage

### <a name="issue"></a>Problème
-  Code spécifique de plate-forme pas directement lié à un Engagement de Mobile Azure peut provoquer des problèmes sur iOS, Android et Windows Phone.

### <a name="causes"></a>Causes

- Nombreuses avancées des problèmes de codage avec Azure Mobile Engagement sont provoquées par le code spécifique de plate-forme mal écrit pas directement lié à un Engagement de Mobile Azure. Vous devez consulter la documentation spécifique à la plate-forme que vous développez pour venant compléter la documentation Azure Mobile Engagement (Android, iOS, Web, Windows et Windows Phone).
- N’est pas correctement configuré « catégories », empêche la liaison d’une notification à un autre emplacement à l’intérieur ou à l’extérieur de l’application (Android uniquement). 
- Ne définissez ne pas « UIKit.framework » « facultatifs » dans votre code d’e/s, présente une « Symbole introuvable » et/ou des blocages sur les anciens périphériques d’e/s (e/s uniquement).
- Les certificats expirés ou n’est pas correctement à l’aide de la version de nomenclature de production ou de développement du certificat, les causes des problèmes push (e/s uniquement).
- Il existe certaines limitations inhérentes à une plateforme Azure Mobile Engagement ne peut pas contrôler (par exemple, comment le centre fonctionne pour en dehors de l’application exécute un push de Android et iOS).
- Engagement de Mobile Azure publie une liste complète des packages internes utilisé par Azure Mobile Engagement pour iOS et Android, à titre de référence. Gardez à l’esprit que certaines fonctionnalités d’Azure Mobile Engagement sont spécifiques à la plate-forme (Android, iOS, Web, Windows et Windows Phone).

### <a name="see-also"></a>Voir aussi

 - [Guide de dépannage des - Push][Link 23] 
 - [Documentation du kit SDK - Notes de publication][Link 5]
 - [Documentation du kit SDK - Guides de mise à niveau][Link 5]

## <a name="application-crashes"></a>Application tombe en panne

### <a name="issue"></a>Problème
- Votre application se bloque sur le périphérique de l’utilisateur final.

### <a name="causes"></a>Causes

- Les informations de blocage peuvent être affichées dans l' *Interface utilisateur d’Analytique* ou de l' *API d’Analytique*
- Vous pouvez trouver l’ID de périphérique de votre périphérique de test et prennent la même action à l’origine de votre application blocage d’un utilisateur final aider à identifier la cause de votre incident.
- Des problèmes connus liés à l’Engagement de Mobile Azure SDK qui provoquent le blocage, les applications sont parfois résolus par la mise à niveau vers la dernière version du Kit de développement. Vérifiez les notes de version sur votre plate-forme lors de l’étude des pannes.

### <a name="see-also"></a>Voir aussi

- [Documentation du kit SDK - Notes de publication][Link 5]
- [Documentation du kit SDK - Guides de mise à niveau][Link 5]

## <a name="app-store-upload-failures"></a>Échecs de téléchargement App store

### <a name="issue"></a>Problème
- Erreurs liées au téléchargement de la dernière version de votre application à Apple, Google ou le magasin d’application Windows.

### <a name="causes"></a>Causes

- Application stocke parfois empêche les applications avec certaines fonctions activées (la banque Apple empêche l’utilisation de IDFV dans les applications dans le magasin et le magasin GooglePlay empêche le partage d’informations entre les applications de l’application). 
- Vérifiez que les notes de version sur votre plate-forme et la version actuelle du SDK si vous avez des difficultés à télécharger une application dans le magasin.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
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
 
