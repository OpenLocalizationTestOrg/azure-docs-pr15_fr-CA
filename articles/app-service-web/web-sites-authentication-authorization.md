<properties 
    pageTitle="Authentifier avec sur site Active Directory dans votre application Azure | Microsoft Azure" 
    description="En savoir plus sur les différentes options pour les applications de métier dans le Service d’application Azure pour authentifier avec sur site Active Directory" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="08/31/2016" 
    ms.author="cephalin"/>

# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Authentifier avec sur site Active Directory dans votre application Azure #

Cet article vous montre comment authentifier avec Active Directory (AD) dans le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md)sur site. Une application Azure est hébergée dans le nuage, mais il existe des façons de s’authentifier sur site utilisateurs AD en toute sécurité. 

## <a name="authenticate-through-azure-active-directory"></a>S’authentifier via Azure Active Directory
Un locataire Azure Active Directory peut être synchronisées de répertoire avec des locaux AD. Cette approche permet aux utilisateurs d’Active Directory pour accéder à votre application à partir d’internet et s’authentifier en utilisant leurs informations d’identification sur site. En outre, Service d’application Azure fournit une [solution clé en main pour cette méthode](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Avec un clic d’un bouton, vous pouvez activer l’authentification avec un client de synchronisation d’annuaire pour votre application Azure. Cette approche présente les avantages suivants :

-   Ne nécessite pas de code d’authentification dans votre application. Application Service permettent de faire l’authentification pour vous et votre temps les fonctionnalités de votre application.
-   [API d’Azure AD graphique](http://msdn.microsoft.com/library/azure/hh974476.aspx) permet d’accéder aux données de l’annuaire à partir de votre application Azure.
-   Offre la technologie SSO à [toutes les applications prises en charge par Active Directory de Azure](/marketplace/active-directory/), y compris Office 365, Dynamics CRM Online, Microsoft Intune et des milliers d’applications non Microsoft cloud. 
-   Azure Active Directory prend en charge le contrôle d’accès basé sur les rôles. Vous pouvez utiliser le modèle [Authorize(Roles="X")] avec peu de modifications à votre code.

Pour savoir comment écrire une application Azure de métier qui s’authentifie avec Azure Active Directory, voir [Création d’une application Azure de ligne d’activité avec l’authentification Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md).

## <a name="authenticate-through-an-on-premises-sts"></a>Authentification via un STS sur site
Si vous avez un local sécurisé service de jeton (STS) comme Active Directory Federation Services (ADFS), vous pouvez l’utiliser que pour fédérer l’authentification pour votre application Azure. Cette approche est recommandée lorsque la politique de la société interdit les données Active Directory d’être stocké dans Azure. Toutefois, notez les points suivants :

-   Topologie de SharePoint Team Services doit être déployé sur site, avec des frais de gestion et des coûts.
-   Seuls administrateurs de AD FS peuvent configurer [des approbations partie utilisatrice et règles de revendication](http://technet.microsoft.com/library/dd807108.aspx), qui peuvent limiter les options du développeur. D’autre part, il est possible de gérer et de personnaliser des [demandes](http://technet.microsoft.com/library/ee913571.aspx) sur une base par application.
-   Accès aux données AD local nécessitent une solution distincte à travers le pare-feu.

Pour savoir comment écrire une application Azure de métier qui s’authentifie auprès d’un STS sur site, consultez [Création d’une application Azure de ligne d’activité avec l’authentification AD FS](web-sites-dotnet-lob-application-adfs.md).
 
