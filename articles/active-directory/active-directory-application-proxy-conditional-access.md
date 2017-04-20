<properties
    pageTitle="Accès conditionnel pour les Applications publiées avec le Proxy d’Application Azure AD"
    description="Explique comment configurer l’accès conditionnel pour les applications que Web pour accéder à distance à l’aide de Proxy d’Application Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-conditional-access"></a>Utilisation de l’accès conditionnel

Vous pouvez configurer des règles d’accès pour accorder un accès conditionnel aux applications publiées à l’aide du Proxy de l’Application. Cela vous permet de :

- Exiger une authentification multifactorielle par application
- Exiger une authentification multifactorielle uniquement lorsque les utilisateurs ne sont pas au bureau
- Empêcher les utilisateurs d’accéder à l’application lorsqu’ils ne sont pas au travail

Ces règles peuvent être appliquées à tous les utilisateurs et les groupes ou uniquement aux utilisateurs et groupes spécifiques. Par défaut, la règle s’applique à tous les utilisateurs qui ont accès à l’application. Toutefois, la règle peut également être limitée à des utilisateurs qui sont membres de groupes de sécurité spécifiés.  

Les règles d’accès sont évalués lorsqu’un utilisateur accède à une application fédérée qui utilise OAuth 2.0, le OpenID se connecter, SAML ou WS-Federation. En outre, les règles d’accès sont évaluées avec OAuth 2.0 et OpenID se connecter lorsqu’un jeton d’actualisation est utilisé pour obtenir un jeton d’accès.

## <a name="conditional-access-prerequisites"></a>Conditions préalables d’accès conditionnel

- Abonnement Premium d’Azure Active Directory
- Un locataire Azure Active Directory fédérée ou managé
- Les locataires fédérés nécessitent l’activation de l’authentification multifactorielle (AMF)  
    ![Configurer les règles d’accès - nécessitent une authentification multifactorielle](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configurer l’authentification à plusieurs facteurs par application
1. Ouvrez une session en tant qu’administrateur dans Azure portal classique.
2. Accédez à Active Directory et sélectionnez le répertoire dans lequel vous souhaitez activer le Proxy d’Application.
3. Cliquez sur **Applications** et faites défiler jusqu'à la section **Règles d’accès** . La section de règles d’accès s’affiche uniquement pour les applications publiées à l’aide du Proxy d’Application qui utilisent l’authentification fédérée.
4. Activer la règle en sélectionnant **Activer les règles d’accès** pour **sur**.
5. Spécifiez les utilisateurs et les groupes auxquels les règles seront appliquent. Utilisez le bouton **Ajouter un groupe** pour sélectionner un ou plusieurs groupes auxquels s’applique la règle d’accès. Cette boîte de dialogue peut également être utilisé pour supprimer les groupes sélectionnés.  Lorsque les règles sont sélectionnées pour l’appliquer à des groupes, les règles d’accès s’appliquent uniquement aux utilisateurs qui appartiennent à l’un des groupes de sécurité spécifiés.  

  - Pour exclure explicitement des groupes de sécurité de la règle, vérifiez **, à l’exception** , et spécifiez un ou plusieurs groupes. Les utilisateurs qui sont membres d’un groupe dans la liste Except ne devront pas effectuer l’authentification à plusieurs facteurs.  

  - Si un utilisateur a été configuré à l’aide de la fonction de plusieurs facteurs d’authentification par l’utilisateur, ce paramètre prévaut sur les règles d’application authentification à plusieurs facteurs. Cela signifie qu’un utilisateur qui a été configuré pour une authentification à facteurs multiples par utilisateur est requis pour effectuer une authentification multifacteur, même si elles ont été exemptés de règles de plusieurs facteurs d’authentification de l’application. Plus d’informations sur [l’authentification à facteurs multiples et les paramètres par utilisateur](../multi-factor-authentication/multi-factor-authentication.md).

6. Sélectionnez la règle d’accès que vous souhaitez définir :
    - **Exiger une authentification multifactorielle**: auxquels s’appliquent les règles d’accès des utilisateurs sera nécessaires à l’authentification selon plusieurs facteur complète avant d’accéder à l’application à laquelle s’applique la règle.
    - **Exiger une authentification multifactorielle pas au bureau**: les utilisateurs qui essaient d’accéder à l’application à partir d’une adresse IP fiable pas devront effectuer l’authentification à plusieurs facteurs. Les plages d’adresses IP de confiance peuvent être configurés sur la page de paramètres d’authentification à plusieurs facteurs.
    - **Bloquer l’accès lorsqu’au travail**: les utilisateurs qui essaient d’accéder à l’application à partir de l’extérieur de votre réseau d’entreprise ne sera pas en mesure d’accéder à l’application.


## <a name="configuring-mfa-for-federation-services"></a>Configuration des AMF pour les services de fédération
Pour les locataires fédérés, plusieurs facteurs d’authentification (AMF) peut être effectuée par Azure Active Directory ou sur site serveur AD FS. Par défaut, AMF se produira sur n’importe quelle page hébergée par Azure Active Directory. Pour configurer AMF sur site, exécuter de Windows PowerShell et la propriété SupportsMFA – permet de configurer le module AD Azure.

L’exemple suivant montre comment activer les locaux AMF sur le locataire contoso.com à l’aide de l' [applet de commande Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) :`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

En plus de la définition de cet indicateur, l’instance de FS locataire fédérées AD doit être configuré pour effectuer l’authentification à plusieurs facteurs. Suivez les instructions pour le [déploiement d’une authentification multifacteur Microsoft Azure sur les sites](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).


## <a name="see-also"></a>Voir aussi

- [Utilisation d’applications prenant en charge des sinistres](active-directory-application-proxy-claims-aware-apps.md)
- [Publier des applications avec le Proxy de l’Application](active-directory-application-proxy-publish.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Publier des applications à l’aide de votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
