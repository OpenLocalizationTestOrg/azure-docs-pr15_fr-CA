<properties
   pageTitle="Comprendre le manifeste d’Application Azure Active Directory | Microsoft Azure"
   description="Couverture détaillés du manifeste d’application Azure Active Directory, qui représente la configuration d’identité d’une application dans un locataire AD Azure et est utilisé pour faciliter l’autorisation OAuth, l’expérience de l’accord et plus."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="dkershaw;bryanla"/>

# <a name="understanding-the-azure-active-directory-application-manifest"></a>Comprendre le manifeste d’application Azure Active Directory

Les applications qui s’intègrent avec Azure Active Directory (AD) doivent être enregistrées avec un locataire AD Azure, fournissant une configuration de l’identité permanente de l’application. Cette configuration est consultée lors de l’exécution, l’activation de scénarios qui permettent à une application de sous-traiter et broker d’authentification/autorisation via AD Azure. Pour plus d’informations sur le modèle d’application Azure AD, reportez-vous à la section [Ajout, mise à jour et suppression d’une Application] [ ADD-UPD-RMV-APP] l’article.

## <a name="updating-an-applications-identity-configuration"></a>Mise à jour de la configuration de l’identité d’une application

Il existe en fait plusieurs options de mise à jour des propriétés de la configuration d’identité d’une application, qui varient de capacités et de degrés de difficulté, y compris les éléments suivants :

- Le ** [Azure portal classique] [ AZURE-CLASSIC-PORTAL] interface utilisateur Web** vous permet de mettre à jour les propriétés les plus courantes d’une application. Ceci est la façon de source d’erreur le plus rapide et moins de mettre à jour les propriétés de votre application, mais ne vous donne pas un accès complet à toutes les propriétés, telles que les deux méthodes.
- Pour des scénarios plus avancés où vous devez mettre à jour les propriétés qui ne sont pas exposées dans le portail classique Azure, vous pouvez modifier le **manifeste d’application**. C’est l’objectif de cet article et est décrite plus en détail à la section suivante.
- Il est également possible de **écrire une application qui utilise l' [API de graphique] [ GRAPH-API] ** mise à jour de votre application, ce qui nécessite le plus d’effort. Cela peut être une option intéressante mais, si vous écrivez des logiciels de gestion, ou que vous devez mettre à jour les propriétés de l’application sur une base régulière de manière automatique.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>À l’aide du manifeste d’application pour mettre à jour la configuration de l’identité d’une application
Via le [portail de classique Azure][AZURE-CLASSIC-PORTAL], vous pouvez gérer la configuration d’identité de votre application, en téléchargeant et télécharger un JSON fichier représentation, qui est appelée un manifeste d’application. Aucun fichier réel n’est stocké dans le répertoire. Le manifeste d’application est simplement une opération HTTP GET sur l’entité Azure AD graphique API Application, et que le téléchargement est une opération HTTP des correctifs sur l’entité de l’Application.

Par conséquent, afin de comprendre le format et les propriétés du manifeste d’application, vous devez faire référence à l' API de graphique [entité d’Application] [ APPLICATION-ENTITY] documentation. Voici quelques exemples de mises à jour qui peuvent être effectuées si le manifeste d’application téléchargement :

- **Étendues d’autorisation Declare (oauth2Permissions)** exposées par votre site web API. Reportez-vous à la rubrique « Exposition API d’autres Applications Web » dans [l’Intégration des Applications avec Azure Active Directory] [ INTEGRATING-APPLICATIONS-AAD] pour plus d’informations sur l’implémentation de l’emprunt d’identité utilisateur à l’aide de l’oauth2Permissions déléguée à portée de l’autorisation. Comme mentionné précédemment, les propriétés de l’entité sont documentées dans l’API du graphique de [l’entité et Type complexe] [ APPLICATION-ENTITY] article de référence, y compris la propriété oauth2Permissions, qui est une collection de type [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- **Les rôles d’application Declare (appRoles) exposées par votre application**. Propriété d’appRoles de l’entité d’Application est une collection de type [AppRole][APPLICATION-ENTITY-APP-ROLE]. Consultez le [contrôle d’accès dans les applications en nuage à l’aide d’Active Directory Azure basée sur les rôles] [ RBAC-CLOUD-APPS-AZUREAD] article pour obtenir un exemple d’implémentation.
- **Declare appelée client applications (knownClientApplications)**, qui vous permettent de lier logiquement le consentement des applications clientes à l’API de ressource/web.
- **Demande de publicité Azure pour émettre les appartenances de revendication de groupe** pour l’utilisateur connecté (groupMembershipClaims).  Cela peut également être configuré pour émettre des demandes sur les appartenances aux rôles de répertoire de l’utilisateur. Consultez l' [autorisation dans les Applications en nuage à l’aide de groupes d’annonces] [ AAD-GROUPS-FOR-AUTHORIZATION] article pour obtenir un exemple d’implémentation.
- **Autoriser votre application en charge OAuth 2.0 implicite accorder** les flux (oauth2AllowImplicitFlow). Ce type de flux de la subvention est utilisé avec des pages web incorporées JavaScript ou Applications de Page unique (SPA). Pour plus d’informations sur l’octroi de l’autorisation implicite, consultez [Présentation de l’OAuth2 implicite accorder des flux dans Azure Active Directory][IMPLICIT-GRANT].
- **Activer l’utilisation de X509 de certificats comme clé secrète** (keyCredentials). Reportez-vous à la section [créer des applications de service et le démon dans Office 365] [ O365-SERVICE-DAEMON-APPS] et le [guide du développeur pour l’authentification avec l’API du Gestionnaire de ressources Azure] [ DEV-GUIDE-TO-AUTH-WITH-ARM] articles pour des exemples d’implémentation.
- **Ajouter un nouvel URI d’ID App** de votre application (identifierURIs[]). Application ID URI sont utilisés pour identifier de manière unique une application au sein de ses clients AD Azure (ou différents utilisateurs AD Azure, pour les scénarios mutualisées lorsque qualifié via vérifié domaine personnalisé). Ils sont utilisés lors de la demande d’autorisations pour les ressources d’une application ou acquérir un jeton d’accès d’une application de ressource. Lorsque vous mettez à jour cet élément, la même mise à jour est effectuée à la collection de [] servicePrincipalNames de l’entité du service correspondant, qui se trouve dans les clients de base de l’application.

Le manifeste d’application fournit également un bon moyen pour effectuer le suivi de l’état d’immatriculation de votre application. Dans la mesure où elle est disponible au format JSON, la représentation sous forme de fichier peut être vérifiée dans le contrôle de code source, ainsi que du code source de votre application.

## <a name="step-by-step-example"></a>L’étape par exemple de l’étape
Vous permet de suivre les étapes nécessaires à la mise à jour de la configuration d’identité de votre application dans le manifeste d’application. Il met en surbrillance un des exemples précédents, indiquant comment déclarer une nouvelle portée de l’autorisation d’une application de ressources :

1. Accédez au [portail de classique Azure] [ AZURE-CLASSIC-PORTAL] et connectez-vous avec un compte disposant des privilèges d’administrateur ou co-d’administrateur service.

2. Une fois que vous avez authentifié, faites défiler et sélectionnez l’extension Azure « Active Directory » dans le panneau de navigation de gauche (1), puis cliquez sur le locataire AD Azure où votre application est enregistré (2).

    ![Sélectionnez le locataire Azure AD][SELECT-AZURE-AD-TENANT]

3. Lorsque la page répertoire s’affiche, cliquez sur « Applications » (1) en haut de la page pour voir une liste des applications enregistrées dans le locataire. Recherchez l’application que vous souhaitez mettre à jour dans la liste, puis cliquez sur (2).

    ![Sélectionnez le locataire Azure AD][SELECT-AZURE-AD-APP]

4. Maintenant que vous avez sélectionné la page principale de l’application, notez la fonctionnalité « Gérer manifeste » au bas de la page (1). Si vous cliquez sur ce lien, vous êtes invité à télécharger ou à transférer le fichier de manifeste de JSON. Cliquez sur « Télécharger le manifeste » (2). Il sera immédiatement suivi avec la boîte de dialogue de confirmation de téléchargement vous inviter à confirmer en cliquant sur « Télécharger manifeste » (3), puis ouvrez ou enregistrez le fichier localement (4).

    ![Gérer le manifeste, l’option de téléchargement][MANAGE-MANIFEST-DOWNLOAD]

    ![Télécharger le manifeste][DOWNLOAD-MANIFEST]

5. Dans cet exemple, nous enregistré le fichier en local, ce qui nous permet d’ouvrir dans un éditeur, apporter des modifications à la JSON et enregistrez à nouveau. Voici à quoi ressemble la structure JSON à l’intérieur de l’éditeur Visual Studio JSON. Remarquez qu’il suit le schéma pour l' [entité d’Application] [ APPLICATION-ENTITY] comme nous l’avons mentionné plus haut :

    ![Mise à jour du manifest JSON][UPDATE-MANIFEST]

    Par exemple, en supposant que nous voulons implémenter/exposent une nouvelle autorisation appelée « Employees.Read.All » sur notre application (API) de la ressource, vous aurait simplement ajouter un élément nouveau par seconde à la collection oauth2Permissions, Internet Explorer :

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    L’entrée doit être unique et vous devez par conséquent générer un nouveau Global Unique ID (GUID) pour le `"id"` propriété. Dans ce cas, comme nous avons spécifié `"type": "User"`, cette autorisation peut être consentie à par n’importe quel compte authentifié par Active Directory Azure client dans lequel l’application de l’API des ressources est enregistrée. Cela accorde au client application l’autorisation d’y accéder pour les compte du compte. Les chaînes de noms d’affichage et de description sont utilisées au cours de l’accord et pour l’affichage dans le portail classique Azure.

6. Lorsque vous avez terminé la mise à jour du manifeste, revenir à la page d’application Azure AD dans Azure portal classique, cliquez sur la « Gestion du manifeste » fonctionnalité Nouveau (1). Mais, cette fois-ci, sélectionnez l’option « Télécharger le manifeste » (2). Comme pour le téléchargement, vous êtes accueilli à nouveau avec une deuxième boîte de dialogue vous invitant à spécifier l’emplacement du fichier JSON. Cliquez sur « Rechercher le fichier... » (3), puis utilisez la boîte de dialogue « Choisissez de téléchargement de fichier » pour sélectionner le fichier JSON (4), et appuyez sur « Ouvrir ». Une fois que la boîte de dialogue disparaît, activez la case à cocher de « OK » (5) et de votre manifeste est téléchargée.  

    ![Gérer le manifeste, options de téléchargement][MANAGE-MANIFEST-UPLOAD]

    ![Télécharger le manifest JSON][UPLOAD-MANIFEST]

    ![Télécharger le manifest JSON - confirmation][UPLOAD-MANIFEST-CONFIRM]

Maintenant que le manifeste est enregistré, vous pouvez donner un client application à accéder à la nouvelle autorisation, nous avons ajouté ci-dessus. Cette fois, vous pouvez utiliser l’interface utilisateur du Web du portail classique Azure au lieu de modifier le manifeste de l’application client :  

1. Accédez d’abord à la page « Configurer » de l’application cliente à laquelle vous souhaitez ajouter l’accès à la nouvelle API et cliquez sur le bouton « Ajouter l’application ».
2. Ensuite s’affiche avec la liste des ressources des applications (API) dans le client. Cliquez sur le signe plus / symbole + en regard du nom de l’application de la ressource pour la sélectionner.  
3. Cliquez sur la case à cocher dans le coin inférieur droit.
4. Lorsque vous revenez à la section « Ajouter une Application » de la page de configuration de votre client, vous verrez la nouvelle application de ressource dans la liste. Si vous placez le curseur sur la section « Autorisations déléguées » à droite de cette ligne, vous verrez une liste déroulante s’affichent. Cliquez sur la liste, puis sélectionnez la nouvelle autorisation pour l’ajouter à la liste du client demandé d’autorisations. Remarque : cette nouvelle autorisation est stockée dans la configuration d’identité de l’application cliente, dans la propriété de la collection « requiredResourceAccess ».

![Autorisations à d’autres applications][PERMS-TO-OTHER-APPS]

![Autorisations à d’autres applications][PERMS-SELECT-APP]

![Autorisations à d’autres applications][PERMS-SELECT-PERMS]

Voilà. Maintenant vos applications s’exécutent à l’aide de leur nouvelle configuration de l’identité.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la relation entre les objets d’Application et de Service Principal d’une application, voir [Application et objets principal du service dans Azure AD][AAD-APP-OBJECTS].
- Consultez le [Glossaire de développeur Azure AD] [ AAD-DEVELOPER-GLOSSARY] pour les définitions de certains des concepts développeur core Azure Active Directory (AD).

Utilisez la section de commentaires DISQUS ci-dessous pour envoyer vos commentaires et nous aider à affiner et à notre contenu de forme.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

