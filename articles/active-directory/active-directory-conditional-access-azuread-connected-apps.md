<properties
    pageTitle="Azure accès conditionnel pour les applications SaaS | Microsoft Azure"
    description="Accès conditionnel dans Azure AD vous permet de configurer des règles d’accès par application, plusieurs facteurs d’authentification et de la possibilité de bloquer l’accès d’utilisateurs non sur un réseau approuvé. "
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Mise en route avec accès conditionnel Azure Active Directory

Azure accès conditionnel Active Directory pour les applications [SaaS](https://azure.microsoft.com/overview/what-is-saas/) et AD Azure connecté permet d’applications que vous configurez accès conditionnel basé sur le groupe, l’emplacement et sensibilité de l’application. 

Avec accès conditionnel en fonction de la sensibilité de l’application, vous pouvez définir des règles d’accès de plusieurs facteurs d’authentification (AMF) par application. AMF par application offre la possibilité de bloquer l’accès pour les utilisateurs qui ne sont pas sur un réseau approuvé. Vous pouvez appliquer des règles de l’AMF à tous les utilisateurs qui sont affectés à l’application, ou uniquement pour les utilisateurs au sein de groupes de sécurité spécifiés.  Les utilisateurs peuvent être exclus de l’obligation de l’AMF si ils accèdent à l’application à partir d’une adresse IP qui est à l’intérieur du réseau de l’organisation.

Ces fonctionnalités sont disponibles pour les clients qui ont acheté une licence Azure Active Directory prime.

## <a name="scenario-prerequisites"></a>Conditions préalables du scénario
* Licence pour Azure Active Directory prime

* Clients Azure Active Directory fédérées ou managé

* Locataires fédérés nécessitent l’activation de l’authentification multifactorielle.

## <a name="configure-per-application-access-rules"></a>Configurer les règles d’accès de l’application

Cette section décrit comment configurer des règles d’accès de l’application.

1. Connectez-vous au portail Azure classique à l’aide d’un compte qui est un administrateur global pour Azure AD.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Sous l’onglet répertoire, sélectionnez le répertoire.
4. Sélectionnez l’onglet **Applications** .
5. Sélectionnez l’application qui est définie pour la règle.
6. Sélectionnez l’onglet **configurer** .
7. Faites défiler jusqu'à la section de règles d’accès. Sélectionnez la règle d’accès de votre choix.
8. Spécifiez les utilisateurs que la règle s’applique à.
9. Activez la stratégie en sélectionnant **sur activé**.

##<a name="understanding-access-rules"></a>Règles d’accès présentation

Cette section donne une description détaillée des prise en charge par l’accès des applications Azure conditionnelle les règles d’accès.

### <a name="specifying-the-users-the-access-rules-apply-to"></a>Spécifier les utilisateurs les règles d’accès s’applique à

Par défaut, la stratégie s’appliquera à tous les utilisateurs qui ont accès à l’application. Toutefois, vous pouvez également limiter la stratégie aux utilisateurs qui sont membres de groupes de sécurité spécifiés. Le bouton **Ajouter un groupe** permet de sélectionner un ou plusieurs groupes à partir de la boîte de dialogue de sélection de groupe auquel la règle d’accès s’applique. Cette boîte de dialogue peut également être utilisé pour supprimer les groupes sélectionnés. Lorsque les règles sont sélectionnées pour l’appliquer à des groupes, les règles d’accès s’appliquent uniquement aux utilisateurs qui appartiennent à l’un des groupes de sécurité spécifiés.

Les groupes de sécurité peuvent également être explicitement exclus de la stratégie en sélectionnant l’option **sauf** et en spécifiant un ou plusieurs groupes. Les utilisateurs qui ne sont membres d’un groupe dans la liste **, à l’exception** ne sera pas soumis à l’obligation d’authentification à plusieurs facteurs, même s’ils sont un membre d’un groupe auquel s’applique la règle d’accès.
La règle d’accès ci-dessous exiger que tous les utilisateurs dans le groupe Administrateurs pour utiliser une authentification multifacteur lors de l’accès de l’application.

![Définition des règles d’accès conditionnel avec AMF](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>Règles d’accès conditionnel avec AMF
Si un utilisateur a été configuré à l’aide de la fonctionnalité d’authentification à plusieurs facteurs par utilisateur, combine ce paramètre sur l’utilisateur avec les règles d’authentification à plusieurs facteurs de l’application. En d’autres termes, un utilisateur qui a été configuré pour une authentification à facteurs multiples par utilisateur devra effectuer une authentification multifacteur même si elles ont été exemptés des règles d’authentification à plusieurs facteurs application. Pour en savoir plus sur les paramètres d’authentification et par utilisateur de plusieurs facteurs.

### <a name="access-rule-options"></a>Options de règle d’accès
Les options suivantes sont prises en charge :

* **Besoin d’authentification à plusieurs facteurs**: les utilisateurs auxquels les règles d’accès s’appliquent, seront requis pour l’authentification à plusieurs facteurs complète avant d’accéder à l’application que la stratégie s’applique à.

* **Exiger une authentification multifactorielle pas au bureau**: un utilisateur qui provenance d’une adresse IP fiable pas devront effectuer l’authentification à plusieurs facteurs. Les plages d’adresses IP de confiance peuvent être configurés sur la page de paramètres d’authentification à plusieurs facteurs.

* **Bloquer l’accès lorsqu’au travail**: un utilisateur qui ne provenance pas d’une adresse IP approuvée sera bloqué. Les plages d’adresses IP de confiance peuvent être configurés sur la page de paramètres d’authentification à plusieurs facteurs.

### <a name="setting-rule-status"></a>Définir le statut de la règle
État de règle d’accès permet l’activation ou désactivation des règles. Lorsque les règles d’accès sont désactivés, l’exigence d’authentification à plusieurs facteurs n’est pas appliquée.

### <a name="access-rule-evaluation"></a>Évaluation de règle d’accès

Les règles d’accès sont évalués lorsqu’un utilisateur accède à une application fédérée qui utilise OAuth 2.0, le OpenID se connecter, SAML ou WS-Federation. En outre, les règles d’accès sont évaluées lorsque l’OAuth 2.0 et les OpenID connecter utilisent un jeton d’actualisation d’acquérir un jeton d’accès. Si l’évaluation de la stratégie échoue lorsqu’un jeton d’actualisation est utilisé, l' erreur **invalid_grant** sera retourné, cela indique que l’utilisateur doit s’authentifier à nouveau au client.

###<a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Configurer les services de fédération pour fournir une authentification multifactorielle

Pour les locataires fédérés, AMF peut être effectuée par Azure Active Directory ou sur site serveur AD FS.

Par défaut, AMF se produira une page hébergée par Azure Active Directory. Pour configurer AMF sur site, vous devez définir la propriété **– SupportsMFA** à **true** dans Azure Active Directory, à l’aide du module Active Directory Azure pour Windows PowerShell.

L’exemple suivant montre comment activer les locaux AMF sur le locataire contoso.com à l’aide de l' [applet de commande Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) :

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

En plus de la définition de cet indicateur, l’instance de FS locataire fédérées AD doit être configuré pour effectuer l’authentification à plusieurs facteurs. Suivez les instructions de [déploiement d’Azure, plusieurs facteurs d’authentification local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="related-articles"></a>Articles connexes

- [Sécurisation de l’accès à Office 365 et autres applications connecté à Azure Active Directory](active-directory-conditional-access.md)
- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
