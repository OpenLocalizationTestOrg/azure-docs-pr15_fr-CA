<properties 
    pageTitle="Windows Phone SDK Silverlight présentation" 
    description="Vue d’ensemble du SDK Windows Phone Silverlight pour Azure Engagement Mobile"                     
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

#<a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone SDK Silverlight présentation pour Azure Engagement Mobile

Cliquez ici pour obtenir des détails sur la manière d’intégrer l’Engagement de Mobile Azure dans une application Silverlight de Windows Phone. Si vous souhaitez essayer tout d’abord, assurez-vous que vous avez effectué notre [didacticiel de 15 minutes](mobile-engagement-windows-phone-get-started.md).

Cliquez pour afficher le [Contenu du Kit de développement logiciel](mobile-engagement-windows-phone-sdk-content.md)

##<a name="integration-procedures"></a>Procédures d’intégration

1. Démarrer ici : [comment intégrer l’Engagement Mobile dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)

2. [Comment intégrer la portée (Notifications) dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement-reach.md) pour les Notifications :

3. Mise en oeuvre du plan de la balise : [Comment faire pour utiliser l’Engagement Mobile avancée balisage API dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md)

##<a name="release-notes"></a>Notes de publication

###<a name="330-04192016"></a>3.3.0 (19/04/2016)
Partie de la de package nuget *MicrosoftAzure.MobileEngagement* **v3.4.0**

-   Journaux de console ajouté « TestLogLevel » API pour activer/désactiver/filtre émis par le Kit de développement logiciel.

Pour une version antérieure, consultez les [notes de version complète](mobile-engagement-windows-phone-release-notes.md)

##<a name="upgrade-procedures"></a>Procédures de mise à niveau

Si vous avez déjà intégré une ancienne version de notre kit de développement logiciel dans votre application, vous devez considérer les points suivants lors de la mise à niveau de la SDK.

Il se peut que vous deviez suivre plusieurs procédures si vous avez manqué plusieurs versions du Kit de développement. Consultez [Procédures de mise à niveau](mobile-engagement-windows-phone-upgrade-procedure.md)terminée. Par exemple, si vous migrez à partir de 0.10.1 vers 0.11.0, que vous devez tout d’abord suivre la procédure « à partir de 0.9.0 à 0.10.1 » puis la procédure « à partir de 0.10.1 à 0.11.0 ».

###<a name="from-200-to-330"></a>À partir de 2.0.0 à 3.3.0

####<a name="test-logs"></a>Journaux des tests

Journaux produits par le Kit de développement peuvent désormais être activé/désactivé/filtrées. Pour le personnaliser, mettre à jour de la propriété `EngagementAgent.Instance.TestLogEnabled` à l’une de disponible à partir de la valeur de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Mise à niveau à partir de versions antérieures

Consultez [les procédures de mise à niveau](mobile-engagement-windows-phone-upgrade-procedure.md)
 
