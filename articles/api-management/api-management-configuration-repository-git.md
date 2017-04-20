<properties 
    pageTitle="Comment faire pour enregistrer et configurer votre configuration de service de gestion de l’API à l’aide de Git" 
    description="Découvrez comment enregistrer et configurer votre configuration de service de gestion de l’API à l’aide de Git." 
    services="api-management" 
    documentationCenter="" 
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


# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Comment faire pour enregistrer et configurer votre configuration de service de gestion de l’API à l’aide de Git

>[AZURE.IMPORTANT] GIT configuration pour la gestion de l’API est actuellement en mode Aperçu. Il est fonctionnellement complet, mais il est en mode aperçu car nous cherchons activement des commentaires sur cette fonctionnalité. Il est possible que nous pouvons modifier une rupture en réponse aux commentaires des clients, nous vous recommandons de pas en fonction de la fonctionnalité pour une utilisation dans des environnements de production. Si vous avez des commentaires ou des questions, n’hésitez pas à `apimgmt@microsoft.com`.

Chaque instance de service de gestion de l’API gère une base de données de configuration qui contient des informations sur la configuration et les métadonnées de l’instance de service. Peut être modifié à l’instance de service en modifiant un paramètre dans le portail de l’éditeur, à l’aide d’une applet de commande PowerShell ou un appel API REST. En plus de ces méthodes, vous pouvez également gérer votre configuration d’instance de service à l’aide de Git, l’activation de scénarios de gestion de service tels que :

-   Version de configuration - télécharger et stocker des versions différentes de la configuration de votre service
-   Bloc de modifications de configuration - apporter des modifications à plusieurs parties de la configuration de votre service dans votre référentiel local et intégrer les modifications sur le serveur en une seule opération
-   Toolchain de Git familière et flux de travail - utiliser le Git outillage et le flux de travail qui vous sont déjà familiers

Le diagramme suivant montre une vue d’ensemble des différentes façons de configurer votre instance de service de gestion de l’API.

![Configurer les GIT][api-management-git-configure]

Lorsque vous apportez des modifications à votre service à l’aide du portail de publisher, les applets de commande PowerShell ou l’API REST, vous gérez votre base de données de configuration de service à l’aide du `https://{name}.management.azure-api.net` point de terminaison, comme indiqué sur le côté droit du diagramme. Le côté gauche du diagramme illustre comment vous pouvez gérer votre configuration de service à l’aide de Git et référentiel Git pour votre service situé dans `https://{name}.scm.azure-api.net`.

Les étapes suivantes fournissent une vue d’ensemble de la gestion de votre instance de service de gestion de l’API à l’aide de Git.

1.  Activer l’accès Git dans votre service
2.  Enregistrer votre base de données de configuration de service à votre référentiel Git
3.  Cloner le mis en pension Git sur votre ordinateur local
4.  Tirez le mis en pension plus récente pour votre ordinateur local et de validation et la diffusion des modifications à votre mis en pension
5.  Déployer les modifications de votre mis en pension dans votre base de données de configuration de service

Cet article décrit comment activer et Git permet de gérer la configuration du service et fournit une référence pour les fichiers et dossiers dans le référentiel Git.

## <a name="to-enable-git-access"></a>Pour permettre l’accès de Git

Vous pouvez rapidement afficher l’état de votre configuration Git en affichant l’icône Git dans le coin supérieur droit du portail publisher. Dans cet exemple, l’accès Git n'a pas encore été activée.

![État de GIT][api-management-git-icon-enable]

Pour afficher et configurer les paramètres de configuration Git, vous pouvez cliquez sur l’icône Git, ou cliquez sur le menu **sécurité** et accédez à l’onglet **référentiel de Configuration** .

![Activer le GIT][api-management-enable-git]

Pour activer l’accès Git, case à cocher **Activer le Git accès** .

Après quelques instants, la modification est enregistrée et un message de confirmation s’affiche. Notez que l’icône de Git a changé de couleur pour indiquer que l’accès Git est activé et le message d’état indique maintenant à qui il sont des modifications non enregistrées dans le référentiel. C’est parce que la base de données de configuration de service Gestion d’API n’a pas encore été enregistré dans le référentiel.

![GIT activé][api-management-git-enabled]

>[AZURE.IMPORTANT] Les secrets ne sont pas définies comme propriétés seront stockées dans le référentiel et qu’il resteront dans son historique jusqu'à ce que vous désactiver et réactiver le Git accès. Les propriétés fournissent un emplacement sécurisé pour gérer les valeurs de chaîne constante, y compris des secrets, sur toutes les stratégies et de configuration de l’API sans que vous ayez à les stocker directement dans vos instructions de stratégie. Pour plus d’informations, voir [comment utiliser les propriétés dans les stratégies de gestion des API Azure](api-management-howto-properties.md).

Pour plus d’informations sur l’activation ou désactivation des accès Git à l’aide de l’API REST, voir [Activer ou désactiver l’accès Git à l’aide de l’API REST](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Pour enregistrer la configuration du service dans le référentiel Git

La première étape avant le référentiel de clonage consiste à enregistrer l’état actuel de la configuration du service dans le référentiel. Cliquez sur **Enregistrer la configuration dans le référentiel**.

![Enregistrer la configuration][api-management-save-configuration]

Apportez les modifications souhaitées sur l’écran de confirmation, puis cliquez sur **Ok** pour enregistrer.

![Enregistrer la configuration][api-management-save-configuration-confirm]

Après quelques instants, la configuration est enregistrée, et l’état de configuration du référentiel s’affiche, y compris la date et l’heure de la dernière modification de la configuration et de la dernière synchronisation entre la configuration du service et le référentiel.

![État de la configuration][api-management-configuration-status]

Une fois que la configuration est enregistrée dans le référentiel, elle peuvent être clonées.

Pour plus d’informations sur l’exécution de cette opération à l’aide de l’API REST, consultez [validation instantané de la configuration à l’aide de l’API REST](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Pour cloner le référentiel vers votre ordinateur local

Pour cloner un référentiel, vous devez l’URL pour votre référentiel, un nom d’utilisateur et un mot de passe. Le nom d’utilisateur et l’URL sont affichés en haut de l’onglet **référentiel de Configuration** .

![GIT clone][api-management-configuration-git-clone]

Le mot de passe est généré en bas de l’onglet **référentiel de Configuration** .

![Générer un mot de passe][api-management-generate-password]

Pour générer un mot de passe, assurez-vous d’abord que l' **expiration** est défini à la date d’expiration souhaitée et l’heure, puis cliquez sur **Générer un jeton**.

![Mot de passe][api-management-password]

>[AZURE.IMPORTANT] Prenez note de ce mot de passe. Une fois que vous quittez cette page le mot de passe ne s’affiche plus.

Les exemples suivants utilisent l’outil Git Bash [Git pour](http://www.git-scm.com/downloads) Windows, mais vous pouvez utiliser n’importe quel outil Git qui vous sont familiers.

Ouvrez votre outil Git dans le dossier de votre choix et exécutez la commande suivante pour cloner le référentiel git sur votre ordinateur local, à l’aide de la commande fournie par le portail de publisher.

    git clone https://bugbashdev4.scm.azure-api.net/ 

Fournir le nom d’utilisateur et le mot de passe lorsque vous y êtes invité.

Si vous rencontrez des erreurs, essayez de modifier votre `git clone` commande pour inclure le nom d’utilisateur et le mot de passe, comme illustré dans l’exemple suivant.

    git clone https://username:password@bugbashdev4.scm.azure-api.net/

Si cela permet une erreur, essayez de la partie mot de passe de la commande de codage d’URL. Un moyen rapide de le faire est d’ouvrir Visual Studio et exécutez la commande suivante dans la **Fenêtre exécution**. Pour ouvrir la **Fenêtre exécution**, ouvrez une solution ou un projet dans Visual Studio (ou créer une nouvelle application de console vide) et choisissez **Windows**, **exécution** à partir du menu **Déboguer** .

    ?System.NetWebUtility.UrlEncode("password from publisher portal")

Pour construire la commande git, utilisez le mot de passe codé en même temps que votre emplacement de référentiel et de nom d’utilisateur.

    git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

Une fois le référentiel est cloné, vous pouvez afficher et travailler avec lui dans votre système de fichiers local. Pour plus d’informations, consultez [référence de structure de fichier et de dossier de référentiel local de Git](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Pour mettre à jour votre référentiel local avec la configuration de l’instance service plus récente

Si vous apportez des modifications à votre instance du service de gestion de l’API dans le portail de l’éditeur ou à l’aide de l’API REST, vous devez enregistrer ces modifications dans le référentiel avant que vous pouvez mettre à jour votre référentiel local avec les dernières modifications. Pour ce faire, cliquez sur **Enregistrer la configuration dans le référentiel** dans l’onglet **référentiel de Configuration** dans le portail de l’éditeur et puis exécutez la commande suivante dans votre référentiel local.

    git pull

Avant d’exécuter `git pull` Vérifiez que vous êtes dans le dossier pour le référentiel local. Si vous venez de terminer la `git clone` de commande, vous devez modifier le répertoire à votre mis en pension en exécutant une commande semblable à celle-ci.

    cd bugbashdev4.scm.azure-api.net/

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Pour envoyer les modifications à partir de votre mis en pension local à la mis en pension de serveur

Pour envoyer les modifications à partir de votre référentiel local au référentiel du serveur, vous devez valider vos modifications et puis de les envoyer au référentiel du serveur. Pour valider vos modifications, ouvrez votre outil de commande Git, basculez vers le répertoire de votre référentiel local et exécutez les commandes suivantes.

    git add --all
    git commit -m "Description of your changes"

Pour envoyer toutes les validations sur le serveur, exécutez la commande suivante.

    git push

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Pour déployer des modifications de configuration de service pour l’instance de service de gestion de l’API

Une fois vos modifications locales sont validées et envoyées au référentiel du serveur, vous pouvez les déployer dans votre instance de service de gestion de l’API.

![Déployer][api-management-configuration-deploy]

Pour plus d’informations sur l’exécution de cette opération à l’aide de l’API REST, consultez [Git de déployer des modifications à la base de données de configuration à l’aide de l’API REST](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Référence de structure de fichier et de dossier de référentiel local de Git

Les fichiers et dossiers dans le référentiel local git contiennent les informations de configuration relatives à l’instance de service.

| Élément                       | Description                                                                                |
|-------------------------   |--------------------------------------------------------------------------------------------|
| dossier de gestion de l’api de racine | Contient la configuration de niveau supérieur pour l’instance de service                                  |
| dossier de l’API                | Contient la configuration pour les API dans l’instance de service                            |
| dossier de groupes              | Contient la configuration pour les groupes dans l’instance de service                          |
| dossier stratégies            | Contient les stratégies dans l’instance de service                                              |
| dossier de portalStyles        | Contient la configuration pour les personnalisations de portail développeur dans l’instance de service |
| dossier de produits            | Contient la configuration pour les produits dans l’instance de service                        |
| dossier de modèles           | Contient la configuration pour les modèles de courrier électronique dans l’instance de service                 |

Chaque dossier peut contenir un ou plusieurs fichiers et dans certains cas, un ou plusieurs dossiers, par exemple un dossier pour chaque API, un produit ou un groupe. Les fichiers dans chaque dossier sont spécifiques du type d’entité décrit par le nom du dossier.

| Type de fichier | Objectif                                                                |
|-----------|------------------------------------------------------------------------|
| JSON      | Informations de configuration sur l’entité respectif                  |
| HTML      | Descriptions sur l’entité, souvent affichée dans le portail des développeurs |
| XML       | Instructions de stratégie                                                      |
| CSS       | Feuilles de style pour la personnalisation du portail développeur                        |

Ces fichiers pouvant être créés, supprimés, modifiés et gérés sur votre système de fichiers local, et les modifications déployées sur le votre instance de service de gestion de l’API.

>[AZURE.NOTE] Les entités suivantes ne sont pas contenues dans le référentiel Git et ne peut pas être configurées à l’aide de Git.
>
>-    Utilisateurs
>-    Abonnements
>-    Propriétés
>-    Entités de portail développeur autres que les styles

### <a name="root-api-management-folder"></a>Dossier de gestion de l’api de racine

La racine de `api-management` dossier contient un `configuration.json` le fichier qui contient des informations de niveau supérieur sur l’instance de service dans le format suivant.

    {
      "settings": {
        "RegistrationEnabled": "True",
        "UserRegistrationTerms": null,
        "UserRegistrationTermsEnabled": "False",
        "UserRegistrationTermsConsentRequired": "False",
        "DelegationEnabled": "False",
        "DelegationUrl": "",
        "DelegatedSubscriptionEnabled": "False",
        "DelegationValidationKey": ""
      },
      "$ref-policy": "api-management/policies/global.xml"
    }

Les quatre premiers paramètres (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, et `UserRegistrationTermsConsentRequired`) carte avec les paramètres suivants sous l’onglet **identités** de la section **sécurité** .

| Définition de l’identité                     | Correspond à                                               |
|--------------------------------------|-------------------------------------------------------|
| RegistrationEnabled                  | Case à cocher de **Rediriger les utilisateurs anonymes à la page de connexion** |
| UserRegistrationTerms                | **Conditions d’utilisation sur l’inscription de l’utilisateur de** la zone de texte               |
| UserRegistrationTermsEnabled         | Case à cocher **Afficher les conditions d’utilisation sur la page d’abonnement**         |
| UserRegistrationTermsConsentRequired | **Nécessite le consentement** de case à cocher                          |

![Paramètres d’identité][api-management-identity-settings]

Les quatre paramètres (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, et `DelegationValidationKey`) carte avec les paramètres suivants sous l’onglet **délégation** dans la section **sécurité** .

| Paramètres de délégation           | Correspond à                                    |
|------------------------------|--------------------------------------------|
| DelegationEnabled            | Case à cocher du **délégué d’ouverture de session et d’inscription**    |
| DelegationUrl                | Zone de texte **URL de point de terminaison de délégation**        |
| DelegatedSubscriptionEnabled | Case à cocher de **l’abonnement délégué** |
| DelegationValidationKey      | Zone de texte **Clé de Validation de délégué**        |

![Paramètres de délégation][api-management-delegation-settings]

Le paramètre final, `$ref-policy`, mappe sur le fichier d’instructions de stratégie globale pour l’instance de service.

### <a name="apis-folder"></a>dossier de l’API

Le `apis` dossier contient un dossier pour chaque API dans l’instance de service qui contient les éléments suivants.

-   `apis\<api name>\configuration.json`-Il s’agit de la configuration de l’API et contient des informations sur l’URL du service back-end et les opérations. Il s’agit des mêmes informations qui seraient retournées si vous deviez appeler [obtenir une API spécifique](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) avec `export=true` dans `application/json` format.
-   `apis\<api name>\api.description.html`-Il s’agit de la description de l’API et correspond à la `description` propriété de l' [entité de l’API](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
-   `apis\<api name>\operations\`-ce dossier contient `<operation name>.description.html` fichiers qui mappent vers les opérations dans l’API. Chaque fichier contient la description d’une seule opération dans l’API qui correspond à la `description` propriété de l' [entité de l’opération](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) dans l’API REST.

### <a name="groups-folder"></a>dossier de groupes

Le `groups` dossier contient un dossier pour chaque groupe défini dans l’instance de service.

-   `groups\<group name>\configuration.json`-Il s’agit de la configuration pour le groupe. Il s’agit des mêmes informations qui seraient retournées si vous deviez appeler l’opération [Get un groupe spécifique](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) .
-   `groups\<group name>\description.html`-Il s’agit de la description du groupe et correspond à la `description` propriété de l' [entité du groupe](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>dossier stratégies

Le `policies` dossier contient les instructions de stratégie pour votre instance de service.

-   `policies\global.xml`-contient les stratégies définies au niveau de la portée globale de votre instance de service.
-   `policies\apis\<api name>\`-Si vous avez des stratégies définies au niveau de la portée de l’API, ils sont contenus dans ce dossier.
-   `policies\apis\<api name>\<operation name>\`dossier - si vous avez des stratégies définies au niveau de la portée de l’opération, ils sont contenus dans ce dossier dans `<operation name>.xml` fichiers qui mappent vers les instructions de stratégie pour chaque opération.
-   `policies\products\`-Si vous avez des stratégies définies au niveau de la portée du produit, ils sont contenus dans ce dossier, qui contient `<product name>.xml` fichiers qui mappent vers les instructions de stratégie pour chaque produit.

### <a name="portalstyles-folder"></a>dossier de portalStyles

Le `portalStyles` dossier contient des feuilles de style et de configuration pour les personnalisations de portail développeur pour l’instance de service.

-   `portalStyles\configuration.json`-contient les noms des feuilles de style utilisés par le portail des développeurs
-   `portalStyles\<style name>.css`-chaque `<style name>.css` fichier contient les styles pour le portail des développeurs (`Preview.css` et `Production.css` par défaut).

### <a name="products-folder"></a>dossier de produits

Le `products` dossier contient un dossier pour chaque produit définie dans l’instance de service.

-   `products\<product name>\configuration.json`-Il s’agit de la configuration du produit. Il s’agit des mêmes informations qui seraient retournées si vous deviez appeler l’opération [Get un produit spécifique](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) .
-   `products\<product name>\product.description.html`-Il s’agit de la description du produit et correspond à la `description` propriété de l' [entité de produit](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) dans l’API REST.

### <a name="templates"></a>modèles

Le `templates` dossier contient la configuration pour les [modèles de courriel](api-management-howto-configure-notifications.md) de l’instance de service.

-   `<template name>\configuration.json`-Il s’agit de la configuration du modèle d’e-mail.
-   `<template name>\body.html`-Ceci est le corps du modèle d’e-mail.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres méthodes pour gérer votre instance de service, voir :

-   Gérer votre instance de service à l’aide des applets de commande PowerShell suivante
    -   [Déploiement du service de référence d’applet de commande PowerShell](https://msdn.microsoft.com/library/azure/mt619282.aspx)
    -   [Gestion des services référence d’applet de commande PowerShell](https://msdn.microsoft.com/library/azure/mt613507.aspx)
-   Gérer votre instance de service dans le portail de publisher
    -   [Gérer votre première API](api-management-get-started.md)
-   Gérer votre instance de service à l’aide de l’API REST
    -   [Référence de l’API REST de gestion API](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>Regarder une vidéo de présentation

> [AZURE.VIDEO configuration-over-git]

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




