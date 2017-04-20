<properties
    pageTitle="Seule la gestion de session pour les applications d’entreprise dans l’aperçu Azure Active Directory | Microsoft Azure"
    description="Apprenez à gérer l’ouverture de session unique sur pour les applications d’entreprise à l’aide d’Active Directory Azure"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="asmalser"/>

# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>Aperçu : Gestion de l’authentification unique pour les applications d’entreprise dans le nouveau portail Azure

> [AZURE.SELECTOR]
- [Azure portal](active-directory-enterprise-apps-manage-sso.md)
- [Azure portal classique](active-directory-sso-integrate-saas-apps.md)

Cet article décrit comment utiliser le [portail Azure](https://portal.azure.com) pour gérer les paramètres d’authentification unique pour les applications, en particulier celles qui ont été ajoutés dans la [Galerie des applications Azure Active Directory (AD Azure)](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). L’expérience de gestion Azure AD pour l’authentification unique est en cours d’évaluation, et que cet article décrit les nouvelles fonctionnalités, ainsi que quelques limitations temporaires qui seront en place uniquement pendant la période d’aperçu. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

##<a name="finding-your-apps-in-the-new-portal"></a>Recherche de vos applications dans le nouveau portail

À partir de septembre 2016, toutes les applications qui ont été configurées pour une seule ouverture de session dans un répertoire, par un administrateur de l’annuaire à l’aide de la [Galerie d’applications Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) à l’intérieur du [Azure portal classique](https://manage.windowsazure.com), peuvent maintenant consulter et gérer dans le portail Azure.

Ces applications sont accessibles dans la section **Des Applications d’entreprise** du portail Azure, un lien sur lequel vous pouvez trouver dans la liste de **Plus de Services** dans le [portail](https://portal.azure.com). Les applications d’entreprise sont des applications qui ont été déployées et utilisées par les utilisateurs au sein de votre organisation.

![Lame d’Applications d’entreprise][1]

Sélectionnez **toutes les applications** pour afficher une liste de toutes les applications qui ont été configurés, y compris les applications qui avaient été ajoutées à partir de la galerie. La sélection d’une application charge la lame de la ressource pour cette application, où les rapports peuvent être affichées pour cette application et une variété de paramètres peut être gérée.

Pour gérer les paramètres d’authentification unique, sélectionnez **l’ouverture de session unique**.

![Lame de ressources d’application][2]


##<a name="single-sign-on-modes"></a>Modes d’ouverture de session unique

La lame **de l’authentification unique** commence par un menu de **Mode** , ce qui permet le mode d’ouverture de session unique à configurer. Les options disponibles sont les suivantes :

* **Authentification basée sur les SAML** - cette option est disponible si l’application prend en charge complet fédéré de l’authentification unique avec Azure Active Directory en utilisant le protocole SAML 2.0.

* **Authentification par mot de passe** - cette option est disponible si AD Azure prend en charge le remplissage de cette application de formulaires de mot de passe.

* **Lié de session** - anciennement connu sous le nom « Existant single sign-on », cette option permet aux administrateurs pour placer un lien vers cette application dans le Lanceur d’applications Azure d’accès AD ou Office 365 leur utilisateur.

Pour plus d’informations sur ces modes, consultez [comment single sign-on avec travail d’Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).


##<a name="saml-based-sign-on"></a>Authentification basée sur les SAML

L’option **d’authentification basée sur les SAML** affiche une lame est divisée en quatre sections :

###<a name="domains-and-urls"></a>Domaines et URL
Il s’agit d’où tous les détails sur le domaine et l’URL de l’application sont ajoutés à votre répertoire AD Azure. Toutes les entrées nécessaires à la création d’applications de travail d’ouverture de session unique sont affichées directement sur l’écran, que toutes les entrées facultatives peuvent être affichées en sélectionnant la case à cocher **Afficher les paramètres d’URL avancés** . La liste complète des entrées prises en charge comprend :

* **Signe d’URL** où l’utilisateur va se connecter à cette application. Si l’application est configurée pour effectuer une authentification unique par le fournisseur de service sur, puis lorsqu’un utilisateur accède à cette URL, le fournisseur de services effectue la redirection nécessaire pour AD Azure pour authentifier et connecter l’utilisateur. Si ce champ est renseigné, AD Azure utilisent cette URL pour lancer l’application à partir d’Office 365 et le panneau AD Azure. Si ce champ est omis, puis AD Azure exécute plutôt le fournisseur d’identité-initiée par ouverture de session lorsque l’application est lancée à partir d’Office 365, le panneau AD Azure, ou la publicité Azure unique d’ouverture de session des URL.

* **Identificateur** - cet URI doit identifier de manière unique l’application de la simple ouverture de session est en cours de configuration. Il s’agit de la valeur AD Azure envoie à l’application en tant que paramètre de l’Audience du jeton SAML, et l’application est prévue pour le valider. Cette valeur s’affiche également en tant que l’ID d’entité dans les métadonnées SAML fournie par l’application.

* **URL de la réponse** - l’URL de réponse est où l’application attend de recevoir le jeton SAML. Cela est également appelé l’URL du Service de consommateur d’Assertion (ACS). Une fois que celles-ci ont été saisis, cliquez sur Suivant pour passer à l’écran suivant. Cet écran fournit des informations sur ce qui doit être configuré sur le côté de l’application pour lui permettre d’accepter un jeton SAML depuis Active Directory Azure.

* L' **État du relais** - l’état du relais est un paramètre facultatif permettant d’indiquer à l’application où diriger l’utilisateur une fois l’authentification terminée. En règle générale la valeur est une URL valide au niveau de l’application, cependant, certaines applications utilisent ce champ différemment (voir l’ouverture de session unique de l’application sur la documentation pour plus de détails). La possibilité de définir l’état du relais est une nouvelle fonctionnalité qui est unique pour le nouveau portail Azure.

###<a name="user-attributes"></a>Attributs de l’utilisateur
Il s’agit dans laquelle les administrateurs peuvent afficher et éditer les attributs qui sont envoyés dans le jeton SAML qui émet des annonces Azure à l’application de chaque fois que des utilisateurs se connecter.

Pour la première version d’aperçu, l’attribut modifiable uniquement pris en charge est l’attribut **d’Identification de l’utilisateur** . La valeur de cet attribut est le champ dans Azure AD qui identifie de manière unique chaque utilisateur au sein de l’application. Par exemple, si l’application a été déployée à l’aide de le « adresse E-mail » comme le nom d’utilisateur et d’un identificateur unique, puis la valeur devrait être définie dans le champ « user.mail » dans Azure AD.

Modification d’attributs supplémentaires est pris en charge dans un aperçu ultérieur.

###<a name="saml-signing-certificate"></a>Certificat de signature de SAML
Cette section affiche les détails du certificat AD Azure utilise pour signer les jetons SAML émises à l’application chaque fois que l’utilisateur s’authentifie. Il permet les propriétés du certificat actuel à inspecter, y compris la date d’expiration.

La possibilité de substitution du certificat et gérer des certificats autres options seront pris en charge dans une version ultérieure. Notez que la gestion complète des certificats peut toujours être effectuée dans [Azure portal classique](active-directory-sso-certs.md).

###<a name="application-configuration"></a>Configuration de l’application
La dernière section fournit de la documentation et les contrôles nécessaires à la configuration de l’application lui-même pour Azure Active Directory en tant que fournisseur d’identité.

Le menu volant de **Configurer l’Application** fournit les nouvelles instructions concises et intégrées pour la configuration de l’application. C’est une autre nouvelle fonctionnalité unique pour le nouveau portail Azure.

> [AZURE.NOTE] Pour obtenir un exemple complet de documentation incorporé, reportez-vous à l’application de Salesforce.com. Documentation pour les applications supplémentaires est ajoutée en permanence au cours de l’aperçu.

![Documents incorporés][3]

##<a name="password-based-sign-on"></a>Authentification par mot de passe
Si la prise en charge de l’application, le mode de l’authentification basée sur mot de passe, cliquez sur **Enregistrer** instantanément configurent pour faire la fonctionnalité SSO basée sur le mot de passe. Pour plus d’informations sur le déploiement de l’authentification de mot de passe, reportez-vous à la section [comment single sign-on avec travail d’Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Authentification par mot de passe][4]


##<a name="linked-sign-on"></a>Ouverture de session liée
Si la prise en charge de l’application, sélection du mode d’authentification unique lié vous permet d’entrer l’URL que vous souhaitez le panneau Azure AD ou un Office 365 pour rediriger vers lorsque les utilisateurs cliquent sur cette application. Pour plus d’informations sur SSO lié (anciennement appelé « SSO existant »), consultez [comment single sign-on avec travail d’Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![L’ouverture de session liée][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
