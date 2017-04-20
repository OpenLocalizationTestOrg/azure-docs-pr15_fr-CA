<properties
    pageTitle="Publier des applications avec un Proxy d’Application AD Azure | Microsoft Azure"
    description="Publier des applications sur site vers le nuage avec le Proxy d’Application Azure AD."
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
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publier des applications à l’aide du Proxy d’Application Azure AD

Le Proxy d’Application Azure AD vous aide à prise en charge des travailleurs à distance par la publication d’applications local accessible via internet. À ce stade, vous disposez déjà [activé le Proxy de l’Application dans Azure portal classique](active-directory-application-proxy-enable.md). Cet article vous guide tout au long de la procédure pour publier des applications qui sont exécutent sur votre réseau local et de fournissent un accès distant sécurisé à partir de l’extérieur de votre réseau. À la fin de cet article, vous serez prêt à configurer l’application avec des informations personnalisées ou des exigences de sécurité.

> [AZURE.NOTE] Proxy de l’application est une fonctionnalité qui est disponible uniquement si la mise à niveau pour la prime ou de la version Basic edition d’Azure Active Directory. Pour plus d’informations, consultez [éditions d’Azure Active Directory](active-directory-editions.md).

## <a name="publish-an-app-using-the-wizard"></a>Publier une application à l’aide de l’Assistant

1. Ouvrez une session en tant qu’administrateur dans [Azure portal classique](https://manage.windowsazure.com/).
2. Accédez à Active Directory et sélectionnez le répertoire où vous avez activé le Proxy de l’Application.

    ![Active Directory - icône](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Cliquez sur l’onglet **Applications** , puis cliquez sur le bouton **Ajouter** au bas de l’écran

    ![Ajouter une application](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Sélectionnez **publier une application qui sera accessible de l’extérieur de votre réseau**.

    ![Publier une application qui sera accessible de l’extérieur de votre réseau](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Fournir les informations suivantes sur votre application :

    - **Nom**: le nom convivial pour votre application. Il doit être unique au sein de votre répertoire.
    - **URL interne**: l’adresse utilisée par le Proxy d’Application Connector pour accéder à l’application à partir de l’intérieur de votre réseau privé. Vous pouvez fournir un chemin d’accès spécifique sur le serveur principal à publier, tandis que le reste du serveur est non publié. De cette façon, vous pouvez publier des sites différents sur le même serveur et chacun d’eux donner à ses propres règles d’accès et le nom.

        > [AZURE.TIP] Si vous publiez un chemin d’accès, assurez-vous qu’il inclut toutes les images nécessaires, les scripts et les feuilles de style pour votre application. Par exemple, si votre application est à https://yourapp/app et utilise des images situés dans https://yourapp/media, vous devez publier https://yourapp/ comme le chemin d’accès.

    - **Méthode de la pré-authentification**: le Proxy de l’Application vérifie les utilisateurs avant qu’ils puissent accéder à votre application. Dans le menu déroulant, choisissez une des options.

        - Azure Active Directory : Proxy de l’Application redirige les utilisateurs à se connecter avec AD Azure, qui authentifie leurs autorisations pour le répertoire et l’application.
        - Passthrough : Les utilisateurs ne doivent s’authentifier pour accéder à l’application.

    ![Propriétés de l’application](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Pour terminer l’Assistant, cliquez sur la case à cocher au bas de l’écran. L’application est maintenant définie dans Azure AD.


## <a name="assign-users-and-groups-to-the-application"></a>Affecter des utilisateurs et des groupes à l’application

Afin que vos utilisateurs d’accéder à votre application publiée, vous devez les affecter individuellement ou en groupes. (N’oubliez pas vous affecter l’accès, trop). Cela nécessite que chaque utilisateur dispose d’une licence pour Azure base ou version ultérieure. Vous pouvez attribuer des licences individuellement ou à des groupes. Pour plus d’informations, consultez [affectation des utilisateurs à une application](active-directory-applications-guiding-developers-assigning-users.md) . 

Pour les applications qui requièrent la pré-authentification, ceci accorde des autorisations à utiliser l’application. Pour les applications qui ne nécessitent pas la pré-authentification, toujours être affecter des utilisateurs à l’application pour qu’elle apparaisse dans leur liste de l’application, comme MyApps.

1. À la fin de l’Assistant Ajouter une application, vous consultez la page de démarrage rapide pour votre application. Pour gérer les utilisateurs ayant accès à l’application, sélectionnez **utilisateurs et groupes**.

    ![Démarrage rapide de l’application Proxy affecter des utilisateurs - capture d’écran](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Rechercher des groupes spécifiques de votre répertoire, ou afficher tous les utilisateurs. Pour afficher les résultats de la recherche, cliquez sur la case à cocher.

    ![Rechercher des groupes ou des utilisateurs - capture d’écran](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Sélectionnez chaque utilisateur ou groupe que vous souhaitez attribuer à cette application, puis cliquez sur **attribuer**. Vous êtes invité à confirmer cette action.

> [AZURE.NOTE] Pour les applications de l’authentification Windows intégrée, vous pouvez affecter uniquement les utilisateurs et les groupes qui ont été synchronisées à partir d’Active Directory sur site. Impossible d’affecter des utilisateurs de se connecter avec un compte Microsoft et des invités pour les applications publiées avec le Proxy d’Application Azure Active Directory. Assurez-vous que vos utilisateurs se connecter avec les informations d’identification qui font partie du même domaine que l’application que vous publiez.

## <a name="test-your-published-application"></a>Testez votre application publiée

Une fois que vous avez publié votre application, vous pouvez tester l’extraire en accédant à l’URL que vous avez publié. Assurez-vous que vous y avez accès, qu’il restitue correctement, et que tout fonctionne comme prévu. Si vous rencontrez un problème ou obtenez un message d’erreur, essayez le [guide de dépannage](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Configuration de votre application

Vous pouvez modifier les applications publiées ou définir des options avancées dans la page Configurer. Sur cette page, vous pouvez personnaliser votre application en modifiant le nom ou le chargement d’un logo. Vous pouvez également gérer les règles d’accès à la méthode de la pré-authentification ou d’une authentification à plusieurs facteurs.

![Configuration avancée](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Après la publication d’applications à l’aide du Proxy d’Application Azure Active Directory, ils s’affichent dans la liste des Applications dans Active Directory Azure, et vous pouvez les gérer il.

Si vous désactivez les services de Proxy de l’Application une fois que vous avez publié des applications, ils ne sont plus accessibles de l’extérieur de votre réseau privé. Cela ne supprime pas les applications.

Pour afficher une application et assurez-vous qu’il est accessible, double-cliquez sur le nom de l’application. Si le service de Proxy de l’Application est désactivé et que l’application n’est pas disponible, un message d’avertissement s’affiche en haut de l’écran.

Pour supprimer une application, sélectionnez une application dans la liste puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- [Publier des applications à l’aide de votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Utilisation d’applications prenant en charge des sinistres](active-directory-application-proxy-claims-aware-apps.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
