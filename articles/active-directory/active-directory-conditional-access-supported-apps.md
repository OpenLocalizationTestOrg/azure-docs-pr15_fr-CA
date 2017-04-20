<properties
    pageTitle="Les applications qui utilisent des règles d’accès conditionnel dans Azure Active Directory | Microsoft Azure"
    description="Avec contrôle d’accès conditionnel, Azure Active Directory vérifie pour des conditions spécifiques lors de l’authentification de l’utilisateur et pour permettre l’accès de l’application."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/26/2016"
    ms.author="markvi"/>

# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Applications qui utilisent des règles d’accès conditionnel dans Azure Active Directory

Les règles d’accès conditionnel sont pris en charge dans Azure Active Directory (AD Azure)-applications connectées, pré-intégrées fédéré logiciel comme une application de service (SaaS), les applications qui utilisent le mot de passe de session unique (SSO), les applications line-of-business et les applications qui utilisent le Proxy d’Application Azure AD. Pour obtenir une liste détaillée des applications pour lesquelles vous pouvez utiliser accès conditionnel, consultez [Services activés avec accès conditionnel](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). Accès conditionnel fonctionne avec les applications mobiles et de bureau qui utilisent l’authentification moderne. Dans cet article, nous couvrir de fonctionnement d’un accès conditionnel dans les applications mobiles et de bureau.

Vous pouvez utiliser Azure signe dans des pages d’annonce dans les applications qui utilisent l’authentification moderne. Avec une page de connexion, un utilisateur doit fournir une authentification multifactorielle. Un message s’affiche si l’accès est bloqué. Authentification moderne est requise pour le périphérique authentifier avec AD Azure, afin que les stratégies des périphériques d’accès conditionnel sont évaluées.

Il est important de savoir quelles applications peuvent utiliser des règles d’accès conditionnel et les étapes que vous devrez prendre pour sécuriser d’autres points d’entrée application.

## <a name="applications-that-use-modern-authentication"></a>Applications qui utilisent l’authentification moderne

> [AZURE.NOTE] Si vous avez une stratégie d’accès conditionnel dans Azure AD qui dispose d’un équivalent dans Office 365, configurer deux stratégies d’accès conditionnel. Cela s’appliquerait, par exemple, aux stratégies d’accès conditionnel pour Exchange Online ou SharePoint Online.

Les applications suivantes prennent en charge l’accès conditionnel pour Office 365 et d’autres applications de service de connexion AD Azure :

| Service cible  | Plate-forme  | Application                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|En ligne Exchange de Office 365 | Windows 10|Application de messagerie/calendrier/personnes, Outlook 2016, Outlook 2013 (avec authentification moderne), Skype pour l’entreprise (avec authentification moderne)|
|En ligne Exchange de Office 365| Windows 8.1, Windows 7 |Outlook 2016, Outlook 2013 (avec authentification moderne), Skype pour l’entreprise (avec authentification moderne)|
|En ligne Exchange de Office 365|iOS, Android|  Application de mobile Outlook|
|En ligne Exchange de Office 365|Mac OS X| 2016 Outlook pour une authentification multifacteur et l’emplacement uniquement ; prise en charge de la stratégie basée sur le dispositif prévu pour l’avenir, Skype pour la prise en charge de l’entreprise prévue pour l’avenir|
|Office 365 SharePoint Online|Windows 10| Les applications Office 2016, applications Office universel, Office 2013 (avec authentification moderne), OneDrive pour l’application d’entreprise (le Client de synchronisation de génération suivante ou NGSC) prend en charge prévue pour l’avenir, la prise en charge de groupes Office prévue pour l’avenir, la prise en charge de l’application SharePoint prévue pour l’avenir|
|Office 365 SharePoint Online|Windows 8.1, Windows 7|Applications Office 2016, Office 2013 (avec authentification moderne), OneDrive de l’application métier (client de synchronisation de Groove)|
|Office 365 SharePoint Online|iOS, Android|  Office des applications mobiles |
|Office 365 SharePoint Online|Mac OS X| Applications Office 2016 pour une authentification multifacteur et l’emplacement uniquement ; prise en charge de la stratégie basée sur le dispositif prévu pour l’avenir|
|Office 365 Yammer|Windows 10, iOS et Android | Application de Yammer Office|
|Dynamics CRM|Windows 10 8.1 de Windows, Windows 7, iOS et Android | Application CRM Dynamics|
|Service de PowerBI|Windows 10 8.1 de Windows, Windows 7, iOS et Android | Application de PowerBI|
|Service d’application distante Azure|Windows 10, 8.1 de Windows, Windows 7, iOS, Android et Mac OS X |Application à distance Azure|
|N’importe quel service d’application de mes applications|Android et iOS|N’importe quel service d’application de mes applications |


## <a name="applications-that-do-not-use-modern-authentication"></a>Applications qui n’utilisent pas l’authentification moderne

Actuellement, vous devez utiliser d’autres méthodes pour bloquer l’accès aux applications qui n’utilisent pas l’authentification moderne. Règles d’accès pour les applications qui n’utilisent pas l’authentification moderne ne sont pas appliquées par accès conditionnel. Il s’agit principalement d’un compte d’accès Exchange et SharePoint. La plupart des versions antérieures des applications utilisent d’anciens protocoles de contrôle de l’accès.

### <a name="control-access-in-office-365-sharepoint-online"></a>Contrôle de l’accès dans Office 365 SharePoint Online
Vous pouvez désactiver les protocoles hérités pour l’accès à SharePoint à l’aide de l’applet de commande Set-SPOTenant. Cette applet de commande permet d’empêcher les clients Office qui utilisent des protocoles non-moderne authentification de l’accès aux ressources SharePoint Online.

**Exemple de commande**:    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Contrôle de l’accès dans Office 365 Exchange Online

Exchange offre deux catégories principales de protocoles. Passez en revue les options suivantes et puis sélectionnez la stratégie adaptée à votre organisation.

-   **Exchange ActiveSync**. Par défaut, les stratégies d’accès conditionnel pour plusieurs facteurs d’authentification et l’emplacement ne sont pas appliquées pour Exchange ActiveSync. Vous devez protéger l’accès à ces services en configurant les stratégies Exchange ActiveSync directement, soit par le blocage d’Exchange ActiveSync à l’aide de règles d’Active Directory Federation Services (ADFS).
-   **Les protocoles hérités**. Vous pouvez bloquer les protocoles hérités avec AD FS. Ceci bloque l’accès aux clients Office plus anciens, tels que Office 2013 sans authentification moderne est activée et les versions antérieures d’Office.


### <a name="use-ad-fs-to-block-legacy-protocol"></a>AD FS permet de bloquer les protocole hérité

Vous pouvez utiliser les règles suivantes pour bloquer l’accès de protocole hérités au niveau AD FS. Choisissez deux configurations courantes.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Option 1 : Permettre à Exchange ActiveSync et permettre à des applications héritées, mais uniquement sur l’intranet

En appliquant les trois règles suivantes à l’approbation de partie pour la plate-forme d’identité Microsoft Office 365, le trafic d’Exchange ActiveSync et navigateur et le trafic d’authentification modernes de confiance AD FS, ont accès. Applications héritées sont bloquées à partir de l’extranet.

##### <a name="rule-1"></a>Règle 1

    @RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Règle 2

    @RuleName = “Allow Exchange ActiveSync ”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Règle 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Option 2 : Autoriser Exchange ActiveSync et de bloquer les applications héritées

En appliquant les trois règles suivantes à l’approbation de partie pour la plate-forme d’identité Microsoft Office 365, le trafic d’Exchange ActiveSync et navigateur et le trafic d’authentification modernes de confiance AD FS, ont accès. Applications héritées sont bloquées à partir de n’importe quel emplacement.

##### <a name="rule-1"></a>Règle 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Règle 2

    @RuleName = “Allow Exchange ActiveSync”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Règle 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");
