<properties 
    pageTitle="Comment autoriser les comptes de développeur à l’aide d’Azure Active Directory dans la gestion des API Azure" 
    description="Découvrez comment autoriser des utilisateurs à l’aide d’Azure Active Directory dans la gestion de l’API." 
    services="api-management" 
    documentationCenter="API Management" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Comment autoriser les comptes de développeur à l’aide d’Azure Active Directory dans la gestion des API Azure


## <a name="overview"></a>Vue d’ensemble
Ce guide vous explique comment activer l’accès au portail développeur pour tous les utilisateurs dans un ou plusieurs répertoires Active Azure. Ce guide vous montre également comment gérer des groupes d’utilisateurs Azure Active Directory en ajoutant des groupes externes qui contiennent les utilisateurs d’Azure Active Directory.

>Pour toutes les étapes de ce guide, vous devez tout d’abord disposer Azure Active Directory dans lequel vous souhaitez créer une application.

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Comment autoriser les comptes de développeur à l’aide d’Azure Active Directory

Pour commencer, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API. Cela vous amène sur le portail d’éditeur de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Cliquez sur **sécurité** dans le menu de **Gestion de l’API** sur la gauche et **Identités externes**.

![Identités externes][api-management-security-external-identities]

Cliquez sur **Azure Active Directory**. Prenez note de **l’URL de redirection** et du commutateur à Azure Active Directory dans le portail classique d’Azure.

![Identités externes][api-management-security-aad-new]

Cliquez sur le bouton **Ajouter** pour créer une nouvelle application Azure Active Directory, puis sélectionnez **Ajouter une application à mon entreprise se développe**.

![Ajouter la nouvelle application Azure Active Directory][api-management-new-aad-application-menu]

Entrez un nom pour l’application, sélectionnez **application Web et/ou des API Web**et cliquez sur le bouton suivant.

![Nouvelle application Azure Active Directory][api-management-new-aad-application-1]

Pour l' **ouverture de session d’URL**, entrez l’URL de connexion de votre portail développeur. Dans cet exemple, l' **ouverture de session d’URL** est `https://aad03.portal.current.int-azure-api.net/signin`. 

Pour l' **Application ID URL**, entrez le domaine par défaut ou un domaine personnalisé pour Azure Active Directory et ajouter une chaîne unique. Dans cet exemple, le domaine par défaut de **https://contoso5api.onmicrosoft.com** est utilisé avec le suffixe **/api/API** spécifié.

![Nouvelles propriétés d’application Azure Active Directory][api-management-new-aad-application-2]

Cliquez sur le bouton de vérification pour enregistrer et créer la nouvelle application et passer à l’onglet de **configuration** pour configurer la nouvelle application.

![Créé d’application Azure Active Directory][api-management-new-aad-app-created]

Si plusieurs annuaires Active Azure vont être utilisées pour cette application, cliquez sur **Oui** pour **l’Application est partagée**. La valeur par défaut est **non**.

![Application est partagée][api-management-aad-app-multi-tenant]

Copier l' **URL de redirection** à partir de la section **Azure Active Directory** de l’onglet **Identités externes** sur le portail de l’éditeur et le coller dans la zone de texte **URL de la réponse** . 

![URL de la réponse][api-management-aad-reply-url]

Faites défiler vers le bas de l’onglet Configurer, sélectionnez la liste déroulante **Autorisations de l’Application** et vérifiez les **données de l’annuaire en lecture**.

![Autorisations de l’application][api-management-aad-app-permissions]

Sélectionnez la liste déroulante **Autorisations de délégué** et cochez **Activer l’authentification et de lire des profils des utilisateurs**.

![Autorisations déléguées][api-management-aad-delegated-permissions]

>Pour plus d’informations sur l’application et les autorisations déléguées, consultez [accès à l’API de graphique][].

Copiez l' **Id de Client** dans le Presse-papiers.

![Id de client][api-management-aad-app-client-id]

Basculer vers le portail de publisher et collez l' **Id Client** est copié à partir de la configuration de l’application Azure Active Directory.

![Id de client][api-management-client-id]

Revenez à la configuration Azure Active Directory, puis cliquez sur **Sélectionnez durée** vers le bas dans la section **clés de** spécifier un intervalle. Dans cet exemple, **1 an** est utilisé.

![Clé][api-management-aad-key-before-save]

Cliquez sur **Enregistrer** pour enregistrer la configuration et afficher la clé. Copiez la clé dans le Presse-papiers.

>Notez cette clé. Une fois que vous fermez la fenêtre de configuration Azure Active Directory, la clé ne s’affiche plus.

![Clé][api-management-aad-key-after-save]

Basculer vers le portail de publisher et collez la clé dans la zone de texte **Client Secret** .

![Secret des clients][api-management-client-secret]

**Autorisé les locataires** spécifie les répertoires qui ont accès à l’API de l’instance de service de gestion de l’API. Spécifiez les domaines des instances Azure Active Directory auquel vous souhaitez accorder l’accès. Vous pouvez séparer plusieurs domaines avec des sauts de ligne, des espaces ou des virgules.

![Locataires autorisées][api-management-client-allowed-tenants]

Plusieurs domaines peuvent être spécifiés dans la section **Autorisé les locataires** . Avant que n’importe quel utilisateur peut se connecter à partir d’un autre domaine que le domaine d’origine où l’application a été enregistrée, un administrateur global de l’autre domaine doit accorder l’autorisation de l’application pour accéder aux données de répertoire. Pour accorder l’autorisation, l’administrateur global doit se connecter à l’application et cliquez sur **Accepter**. Dans l’exemple suivant `miaoaad.onmicrosoft.com` a été ajouté à **Autorisé les locataires** et global administrateur de ce domaine se connecte pour la première fois.

![Autorisations][api-management-permissions-form]

>Si un administrateur non global tente de se connecter avant que les autorisations sont accordées par un administrateur global, la tentative de connexion échoue et un écran d’erreur s’affiche.

Une fois la configuration souhaitée est spécifiée, cliquez sur **Enregistrer**.

![Enregistrer][api-management-client-allowed-tenants-save]

Une fois que les modifications sont enregistrées, les utilisateurs dans Azure Active Directory spécifié peuvent ouvrir dans le portail de développement en suivant les étapes de [Ouvrir une session sur le portail des développeurs à l’aide d’un compte Azure Active Directory][].

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Comment ajouter une externe Azure du groupe Active Directory

Après l’activation de l’accès pour les utilisateurs dans Azure Active Directory, vous pouvez ajouter des groupes de Azure Active Directory dans la gestion d’API pour gérer plus facilement l’association des développeurs dans le groupe avec les produits de votre choix.

> Pour configurer un groupe Azure Active Directory externe, Azure Active Directory doit d’abord être configuré dans l’onglet identités en suivant la procédure décrite dans la section précédente. 

Groupes d’Azure Active Directory externes sont ajoutés à partir de l’onglet **visibilité** du produit pour lequel vous souhaitez accorder l’accès au groupe. Cliquez sur **produits**, puis cliquez sur le nom du produit souhaité.

![Configurer le produit][api-management-configure-product]

Basculez vers l’onglet **visibilité** , puis cliquez sur **Ajouter des groupes d’Azure Active Directory**.

![Ajouter des groupes][api-management-add-groups]

Sélectionnez **Azure Active Directory clients** dans la liste déroulante, puis tapez le nom du groupe de votre choix dans les **groupes** à ajouter à la zone de texte.

![Sélectionner un groupe][api-management-select-group]

Ce nom de groupe peut trouver dans la liste **groupes** pour Azure Active Directory, comme indiqué dans l’exemple suivant.

![Liste de groupes d’Azure Active Directory][api-management-aad-groups-list]

Cliquez sur **Ajouter** pour valider le nom du groupe et ajoutez le groupe. Dans cet exemple, les **Développeurs de Contoso 5** groupe externe est ajoutée. 

![Groupe ajouté][api-management-aad-group-added]

Cliquez sur **Enregistrer** pour enregistrer la nouvelle sélection de groupe.

Une fois un groupe Azure Azure Active Directory a été configuré à partir d’un produit, il est disponible sous l’onglet **visibilité** pour les autres produits dans l’instance de service de gestion de l’API.

Pour consulter et configurer les propriétés de groupes externes une fois qu’ils ont été ajoutés, cliquez sur le nom du groupe à partir de l’onglet **groupes** .

![Gérer des groupes][api-management-groups]

À partir de là, vous pouvez modifier le **nom** et la **Description** du groupe.

![Modifier le groupe][api-management-edit-group]

Les utilisateurs de Azure Active Directory configuré peuvent ouvrir une session dans le portail de développement et de la vue et vous abonner à tous les groupes pour lesquels ils disposent d’une visibilité en suivant les instructions dans la section suivante.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Comment faire pour ouvrir une session sur le portail des développeurs à l’aide d’un compte Azure Active Directory

Pour vous connecter au portail développeur à l’aide d’un compte Azure Active Directory configuré dans les sections précédentes, ouvrir une nouvelle fenêtre de navigateur à l’aide de l' **URL de connexion** à partir de la configuration de l’application Active Directory, puis cliquez sur **Azure Active Directory**.

![Portail des développeurs][api-management-dev-portal-signin]

Entrez les informations d’identification de l’un des utilisateurs dans Azure Active Directory, puis cliquez sur **se connecter**.

![Connexion][api-management-aad-signin]

Vous pouvez être invité avec un formulaire d’inscription si des informations supplémentaires sont requises. Remplissez le formulaire d’inscription, puis cliquez sur **inscrire**.

![Inscription][api-management-complete-registration]

L’utilisateur est maintenant connecté le portail des développeurs pour votre instance de service de gestion de l’API.

![Inscription terminée][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Mise en route de la gestion des API Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accès à l’API du graphique]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Ouvrir une session sur le portail des développeurs à l’aide d’un compte Azure Active Directory]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

