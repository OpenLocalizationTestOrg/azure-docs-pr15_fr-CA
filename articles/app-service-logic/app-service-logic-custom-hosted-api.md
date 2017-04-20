<properties
    pageTitle="Appeler une API personnalisée dans les applications de logique"
    description="L’utilisation de votre API personnalisé hébergé sur le Service d’application avec des applications de logique"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>

# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>L’utilisation de votre API personnalisé hébergé sur le Service d’application avec des applications de logique

Bien que les applications de logique a un ensemble de connecteurs 40 + une grande variété de services, vous souhaiterez appellent vos propres API personnalisé que vous pouvez exécuter votre propre code. Une des méthodes plus simples et la plus évolutives pour héberger votre propre API de web personnalisé est d’utiliser le Service de l’application. Cet article explique comment appeler dans n’importe quel site web API hébergé dans une application API de Service d’application, web app ou une application mobile.

Pour plus d’informations sur la création des API comme un déclencheur ou d’une action dans les applications de logique, consultez [cet article](app-service-logic-create-api-app.md).

## <a name="deploy-your-web-app"></a>Déployer votre application Web

Tout d’abord, vous devez déployer votre API sous la forme d’une application Web dans le Service d’application. Les instructions fournies ici couvrent le déploiement de base : [créer une application web ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).  Alors que vous pouvez appeler des API à partir d’une application de logique, pour une expérience optimale, que nous vous recommandons de vous ajouter des métadonnées Swagger pour s’intégrer facilement avec les actions de logique d’applications.  Vous trouverez plus d’informations sur [l’Ajout de swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Paramètres de l’API

Afin que le Concepteur d’applications logique analyser votre Swagger, il est important que vous activez CORS et définissez les propriétés APIDefinition de votre application web.  Il est très facile de définir au sein du portail Azure.  Simplement ouvrir la lame des paramètres de votre application Web, dans la section API définir la « définition de API » à l’URL de votre fichier swagger.json (il s’agit généralement de https://{name}.azurewebsites.net/swagger/docs/v1) et ajouter une stratégie CORS pour ' *' pour autoriser les requêtes des logique d’applications concepteur.

## <a name="calling-into-the-api"></a>Appel de l’API

Lorsque dans le portail d’applications logique, si vous avez défini les CORS et les propriétés de définition de l’API vous pourrez facilement ajouter actions personnalisées API au sein de votre flux.  Dans le concepteur, vous pouvez sélectionner pour parcourir vos sites Web d’abonnement pour répertorier les sites Web avec une URL swagger définie.  Vous pouvez également utiliser le protocole HTTP + action Swagger à pointer sur un swagger et la liste des actions disponibles et des entrées.  Enfin, vous pouvez toujours créer une requête à l’aide de l’action HTTP pour appeler une API, même ceux qui n’ont pas ou exposer un document swagger.

Si vous souhaitez sécuriser votre API, il existe deux façons de le faire :

1. Aucune modification du code requise - Azure Active Directory ne peut être utilisée pour protéger les votre API sans nécessiter des modifications de code ou le redéploiement.
1. Appliquer l’authentification de base, authentification de DAS ou authentification de certificat dans le code de votre API.

## <a name="securing-calls-to-your-api-without-a-code-change"></a>Sécurisation des appels à votre API sans une modification du code

Dans cette section, vous allez créer deux applications Azure Active Directory – une pour votre application logique et l’autre pour votre application Web.  Vous allez authentifier les appels à votre application Web à l’aide de l’identité de service (id de client et code secret) associée à l’application DAS pour votre application logique. Enfin, vous devez inclure l’application dans votre définition d’application logique du code.

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>Partie 1 : Configurer une identité d’Application pour votre application logique

C’est ce que l’application logique utilise pour authentifier par rapport à active directory. Vous seul *doivent* faire qu’une seule fois pour votre répertoire. Par exemple, vous pouvez choisir d’utiliser la même identité pour toutes vos applications de logique, bien que vous pouvez également créer des identités uniques par application de la logique si vous le souhaitez. Vous pouvez effectuer cette opération dans l’interface utilisateur ou utiliser PowerShell.

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>Créer l’identité de l’application via le portail classique Azure

1. Accédez à [Active directory dans Azure portal classique](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) , sélectionnez le répertoire que vous utilisez pour votre application Web
2. Cliquez sur l’onglet **Applications**
3. Cliquez sur **Ajouter** dans la barre de commandes au bas de la page
4. Donnez un nom à utiliser, cliquez sur la flèche suivant à votre identité.
5. Placez dans une chaîne unique en tant que domaine dans deux zones de texte et cliquez sur la case à cocher
6. Cliquez sur l’onglet **configurer** pour cette application
7. Copiez l' **ID de Client**, il sera utilisé dans votre logique d’application
8. Dans la section **touches** , cliquez sur **Sélectionnez durée** et choisissez 1 an ou 2 ans
9. Cliquez sur le bouton **Enregistrer** au bas de l’écran (vous devrez peut-être attendre quelques secondes)
10. Maintenant, vous devez copier la clé dans la zone. Il sera également utilisé dans votre logique d’application

#### <a name="create-the-application-identity-using-powershell"></a>Créer l’identité de l’application à l’aide de PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Veillez à copier l' **ID des clients**, l' **ID de l’Application** et le mot de passe que vous avez utilisé

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>Partie 2 : Protéger votre application Web avec une identité d’application DAS

Si votre application Web est déjà déployée vous pouvez simplement l’activer dans le portail. Dans le cas contraire, vous pouvez effectuer l’activation de l’autorisation de votre déploiement de gestionnaire de ressources d’Azure.

#### <a name="enable-authorization-in-the-azure-portal"></a>Activer l’autorisation dans le portail Azure

1. Accédez à l’application Web et cliquez sur les **paramètres** dans la barre de commandes.
2. Cliquez sur **L’autorisation/authentification**.
3. Mettez-le **sous tension**.

À ce stade, une Application est automatiquement créée pour vous. Vous avez besoin Client ID de cette Application pour la partie 3, il vous faudra donc :

1. Accédez à [Active directory dans le portail classique Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) et sélectionnez votre répertoire.
2. Recherche de l’application dans la zone de recherche
3. Cliquez dessus dans la liste
4. Cliquez sur l’onglet **configurer**
5. Vous devez voir l' **ID de Client**

#### <a name="deploying-your-web-app-using-an-arm-template"></a>Déploiement de votre application Web à l’aide d’un modèle ARM

Tout d’abord, vous devez créer une application pour votre application Web. Il doit être différent de l’application qui est utilisée pour votre application logique. Démarrer en suivant la procédure décrite ci-dessus dans la partie 1, mais maintenant pour l’utilisation de la **page d’accueil** et **IdentifierUris** **URL** https:// réel de votre application Web.

>[AZURE.NOTE]Lorsque vous créez l’Application pour votre application Web, vous devez utiliser l' [approche de portail classique Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), que l’applet de commande PowerShell ne configure pas les autorisations requises pour la connexion des utilisateurs dans un site Web.

Une fois que vous avez le client ID et l’ID de client, incluent les éléments suivants en tant que sub ressource de l’application Web dans votre modèle de déploiement :

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Pour exécuter un déploiement automatiquement qui déploie un vide Web app et une application de logique ensemble qui utilisent DAS, cliquez sur le bouton suivant :

[![Déployer vers Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Pour le modèle complet, voir [appels d’application logique dans une API personnalisé hébergé sur le Service de l’application et protégée par DAS](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>Partie 3 : Remplir la section d’autorisation dans l’application logique

Dans la section **Authorization** de l’action **HTTP** :`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Élément | Description |
|---------|-------------|
| type de | Type d’authentification. Pour l’authentification de ActiveDirectoryOAuth, la valeur est ActiveDirectoryOAuth. |
| clients | L’identificateur de client permettant d’identifier le client Active Directory. |
| public | Obligatoire. La ressource que vous vous connectez à. |
| clientID | L’identificateur client d’application Azure AD. |
| secret | Obligatoire. Secret du client qui demande le jeton. |

Le modèle ci-dessus a déjà cette installation, mais si vous créez l’application logique directement, vous devez inclure la section autorisation complète.

## <a name="securing-your-api-in-code"></a>Sécurisation de votre API dans le code

### <a name="certificate-auth"></a>Authentification de certificat

Vous pouvez utiliser des certificats clients pour valider les requêtes entrantes vers votre application Web. Consultez [Comment à configurer TLS l’authentification mutuelle pour l’application Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md) pour la configuration de votre code.

Dans la section *autorisation* , vous devez fournir : `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Élément | Description |
|---------|-------------|
| type de | Obligatoire. Type d’authentification. Pour les certificats de client SSL, la valeur doit être ClientCertificate. |
| PFX | Obligatoire. Codé en base 64 de contenu d’un fichier PFX. |
| mot de passe | Obligatoire. Mot de passe pour accéder au fichier PFX. |

### <a name="basic-auth"></a>Authentification de base

Vous pouvez utiliser l’authentification de base (par exemple, le nom d’utilisateur et le mot de passe) pour valider les demandes entrantes. Authentification de base est un modèle commun et vous pouvez le faire dans n’importe quel langage que vous créez dans votre application.

Dans la section *autorisation* , vous devez fournir : `{"type": "basic","username": "test","password": "test"}`.

| Élément | Description |
|---------|-------------|
| type de | Obligatoire. Type d’authentification. L’authentification de base, la valeur doit être de Basic. |
| nom d’utilisateur | Obligatoire. Nom d’utilisateur à authentifier. |
| mot de passe | Obligatoire. Mot de passe pour l’authentification. |

### <a name="handle-aad-auth-in-code"></a>Gérer l’authentification DAS dans le code

Par défaut, l’authentification Azure Active Directory que vous activez le portail ne fait pas une autorisation précise. Par exemple, elle ne verrouille pas votre API à une application ou un utilisateur spécifique, mais uniquement pour un client particulier.

Si vous souhaitez restreindre l’API pour simplement la logique d’application, par exemple, dans le code, vous pouvez extraire l’en-tête qui contient le JWT et vérifier les personnes l’appelant, rejette toutes les demandes qui ne correspondent pas.

Poursuivre, si vous souhaitez implémenter complètement dans votre propre code et pas tirer parti de la fonctionnalité de portail, vous pouvez lire cet article : [Utilisation de Active Directory pour l’authentification dans le Service d’application Azure](../app-service-web/web-sites-authentication-authorization.md).

Vous devez toujours suivre les étapes ci-dessus pour créer une identité d’Application pour votre application logique et l’utiliser pour appeler l’API.
