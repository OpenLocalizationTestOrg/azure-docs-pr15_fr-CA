<properties 
   pageTitle="Engagement de Mobile Azure Guide - API de dépannage" 
   description="Résolution des problèmes de repères pour Azure Engagement Mobile - API" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="10/04/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-api-issues"></a>Guide de dépannage pour les problèmes d’API

Les éléments suivants sont des problèmes possibles que vous pouvez rencontrer l’interaction entre les administrateurs avec Engagement de Mobile Azure via les API.

## <a name="syntax-issues"></a>Problèmes de syntaxe

### <a name="issue"></a>Problème
- Erreurs de syntaxe à l’aide de l’API (ou un comportement inattendu).

### <a name="causes"></a>Causes

- Problèmes de syntaxe :
    - Assurez-vous de vérifier la syntaxe de l’API spécifique que vous utilisez pour vérifier que l’option est disponible.
    - Un problème courant avec l’utilisation de l’API est de confondre les atteindre API et le Push (la plupart des tâches doit être effectuée avec l’API de parvenir au lieu de l’API Push). 
    - Un autre problème courant avec intégration du Kit de développement logiciel et utilisation de l’API est de confondre la clé du Kit de développement logiciel et la clé de l’API.
    - Les scripts qui se connectent à l’API est nécessaire envoyer des données au moins 10 minutes, ou la connexion expire (particulièrement courant dans les scripts API du moniteur à l’écoute pour les données). Pour éviter les délais d’attente, disposez votre script envoyer une ping XMPP toutes les 10 minutes afin de maintenir la session avec le serveur.

### <a name="see-also"></a>Voir aussi
 
- [Documentation sur l’API][Link 4]
- [Informations sur le protocole XMPP]( http://xmpp.org/extensions/xep-0199.html)
 
## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Impossible d’utiliser l’API pour effectuer la même action disponible dans l’interface utilisateur Engagement Azure Mobile

### <a name="issue"></a>Problème
- Une action qui fonctionne à partir de l’interface utilisateur Engagement Azure Mobile ne fonctionne pas dans l’API d’Engagement de Mobile Azure connexes.

### <a name="causes"></a>Causes

- Confirmant que vous pouvez effectuer la même action à partir de l’interface utilisateur de Azure Mobile Engagement indique que vous avez correctement intégré cette fonctionnalité d’Azure Mobile Engagement avec le Kit de développement logiciel.

### <a name="see-also"></a>Voir aussi
 
- [Documentation de l’interface utilisateur][Link 1]
 
## <a name="error-messages"></a>Messages d’erreur

### <a name="issue"></a>Problème
- Codes d’erreur à l’aide de l’API affiché lors de l’exécution ou dans les journaux.

### <a name="causes"></a>Causes

- Voici la liste des numéros de codes communs API état de référence et de dépannage préliminaire composite :

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Voir aussi

- [Documentation sur l’API - des erreurs détaillées sur chaque API spécifique][Link 4]
 
## <a name="silent-failures"></a>Échecs silencieux

### <a name="issue"></a>Problème
- Action d’API échoue et aucun message d’erreur s’affichée lors de l’exécution ou dans les journaux.

### <a name="causes"></a>Causes

- Nombre d’éléments sera désactivé dans l’interface utilisateur de Azure Mobile Engagement si elles ne sont pas intégrées correctement, mais échouer en mode silencieux à partir de l’API, il vous suffit de tester la même fonctionnalité à partir de l’interface utilisateur pour voir si elle fonctionne.
- Azure Mobile Engagement et nombreuses fonctionnalités avancées de l’Engagement Mobile Azure vous essayez d’utiliser, doivent être individuellement intégrés dans votre application avec le Kit de développement logiciel en plusieurs étapes avant de pouvoir les utiliser.

### <a name="see-also"></a>Voir aussi

- [Guide de dépannage des - Kit de développement logiciel][Link 25]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
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
 
