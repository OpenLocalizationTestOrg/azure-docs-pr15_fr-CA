<properties
    pageTitle="Comment faire pour activer la publication d’applications client natif avec des applications de serveur proxy | Microsoft Azure"
    description="Décrit comment activer des applications client natif communiquer avec le connecteur de Proxy de Application Azure Active Directory pour fournir un accès distant sécurisé à vos applications sur site."
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

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Comment faire pour activer les applications client natif d’interagir avec les Applications de proxy

Azure Active Directory Application Proxy est largement utilisé pour publier des applications de navigateur telles que SharePoint, Outlook Web Access et ligne personnalisée des applications d’entreprise. Il peut également être utilisé pour publier des applications client natif, qui diffèrent des applications web, car les installer sur un périphérique. Pour cela, il prend en charge l’AD Azure émis des jetons qui sont envoyées dans les en-têtes HTTP d’autorisation standard.

![Relation entre les utilisateurs et les applications publiées Azure Active Directory](./media/active-directory-application-proxy-native-client/richclientflow.png)

La méthode recommandée pour publier ces applications est d’utiliser la bibliothèque de l’authentification AD Azure, qui prend en charge toutes les contraintes d’authentification et prend en charge de nombreux environnements clients différents. Proxy de l’application s’intègre dans l' [Application Native pour le scénario de l’API Web](active-directory-authentication-scenarios.md#native-application-to-web-api). Le processus de cette tâche est la suivante :

## <a name="step-1-publish-your-application"></a>Étape 1 : Publier votre application

Publier votre application proxy comme vous le feriez pour toute autre application, affecter des utilisateurs et leur donner premium ou licences de base. Pour plus d’informations, consultez [applications de publier avec le Proxy de l’Application](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Étape 2 : Configurer votre application.

Configurez votre application native comme suit :

1. Connectez-vous au portail Azure classique.
2. Sélectionnez l’icône Active Directory dans le menu de gauche, puis sélectionnez votre répertoire.
3. Dans le menu supérieur, cliquez sur **Applications**. Si aucune application n’ont été ajoutées à votre répertoire, cette page affichera uniquement le lien **Ajouter une application** . Cliquez sur le lien, ou vous pouvez également cliquer sur le bouton **Ajouter** dans la barre de commandes.
4. Sur la page **que voulez-vous faire** , cliquez sur le lien pour **Ajouter une application à mon entreprise se développe**.
5. Dans la page **nous dire sur votre application** , spécifiez un nom pour votre application, choisissez **application client natif**. Cliquez sur la flèche pour continuer.
6. Dans la page **informations sur l’Application** , fournissent l' **URI de redirection** pour l’application client natif, puis cliquez sur la case à cocher à la fin.

Votre application a été ajoutée, et vous serez sur la page de démarrage rapide pour votre application.

## <a name="step-3-grant-access-to-other-applications"></a>Étape 3 : Accorder l’accès aux autres applications

Activer l’application native pour être exposés à d’autres applications dans votre répertoire :

1. Dans le menu supérieur, cliquez sur **Applications**, sélectionnez la nouvelle application native, puis cliquez sur **configurer**.
2. Faites défiler jusqu'à la section **autorisations à d’autres applications** . Cliquez sur le bouton **Ajouter application** et sélectionnez l’application de proxy que vous souhaitez accorder l’accès de l’application native à et cliquez sur la coche dans le coin inférieur droit. Dans la liste déroulante **Autorisations de délégué** , sélectionnez la nouvelle autorisation.

![Ajouter des autorisations à d’autre applications capture d’écran - application](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Étape 4 : Modifier la bibliothèque de l’authentification Active Directory

Modifier le code de l’application d’origine dans le cadre de l’authentification de l’Active Directory d’authentification Library (ADAL) pour inclure les éléments suivants :

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Les variables doivent être remplacés comme suit :

- **TenantId** vous pouvez trouver le GUID dans l’URL de la page de **Configuration** de l’application, juste après le « Répertoire / ».
- **URL du site Web frontal** est l’URL de front-end vous entré dans l’Application de Proxy et se trouve sur la page de **Configuration** de l’application de proxy.
- Vous trouverez l' **Identifiant client** de l’application native sur la page de **configuration** de l’application native.
- **Rediriger l’URI de l’application native** peut être trouvé sur la page de **configuration** de l’application native.

![Nouvelle application native configurer la capture d’écran de la page](./media/active-directory-application-proxy-native-client/new_native_app.png)

Pour plus d’informations sur le flux de l’application native, consultez [application Native pour une API web](active-directory-authentication-scenarios.md#native-application-to-web-api).


## <a name="see-also"></a>Voir aussi

- [Publier des applications à l’aide de votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Utilisation d’applications prenant en charge des sinistres](active-directory-application-proxy-claims-aware-apps.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
