<properties 
    pageTitle="Comment mettre en œuvre la reprise après sinistre à l’aide de la sauvegarde et de restauration dans la gestion des API Azure | Microsoft Azure" 
    description="Apprenez à utiliser la sauvegarde et de restauration pour effectuer la récupération d’urgence dans Azure API de gestion." 
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

# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Comment mettre en œuvre la reprise après sinistre à l’aide de la sauvegarde et de restauration dans la gestion des API Azure

En choisissant de publier et de gérer votre API via la gestion des API Azure vous tirez parti des nombreux tolérance de pannes et les capacités de l’infrastructure que vous auriez sinon à concevoir, implémenter et gérer. La plateforme Azure permet d’atténuer une grande partie des défaillances potentielles en une fraction du coût.

Pour restaurer à partir de la disponibilité des problèmes affectant la région où la gestion de l’API service est hébergé vous doivent être prêts à reconstituer votre service dans une région différente à tout moment. En fonction de vos objectifs de disponibilité et d’un objectif de temps de récupération vous pouvez souhaiter réserver un service de sauvegarde dans une ou plusieurs régions et essayez de mettre à jour leur configuration et leur contenu synchronisé avec le service actif. La sauvegarde du service et de la fonctionnalité de restauration fournit les blocs de construction nécessaires pour la mise en œuvre de votre stratégie de récupération d’urgence.

Ce guide explique comment authentifier les demandes du Gestionnaire de ressources Azure et comment faire pour sauvegarder et restaurer vos instances de service de gestion de l’API.

>[AZURE.NOTE] Le processus de sauvegarde et de restauration d’une instance de service de gestion de l’API de reprise après sinistre peut également servir pour les instances de service de gestion de l’API pour les scénarios de mise en attente de réplication.
>
>Notez que chaque sauvegarde expire après 7 jours. Si vous essayez de restaurer une sauvegarde après l’expiration de la période d’expiration de 7 jours, la restauration risque d’échouer avec une `Cannot restore: backup expired` message.

## <a name="authenticating-azure-resource-manager-requests"></a>Demande d’authentification Azure le Gestionnaire de ressources

>[AZURE.IMPORTANT] L’API REST de sauvegarde et de restauration utilise le Gestionnaire de ressources Azure et dispose d’un mécanisme d’authentification que les API reste pour la gestion de vos entités de gestion de l’API. Les étapes de cette section décrivent comment authentifier les demandes du Gestionnaire de ressources Azure. Pour plus d’informations, consultez [les demandes d’authentification Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Toutes les tâches que vous effectuez sur les ressources à l’aide du Gestionnaire de ressources Azure doivent être authentifié avec Azure Active Directory à l’aide de la procédure suivante.

-   Ajouter une application au locataire Azure Active Directory.
-   Définir des autorisations pour l’application que vous avez ajouté.
-   Obtenir le jeton d’authentification des demandes pour le Gestionnaire de ressources Azure.

La première étape consiste à créer une application Azure Active Directory. Connectez-vous au [Portail classique d’Azure](http://manage.windowsazure.com/) à l’aide de l’abonnement qui contient l’instance de service de gestion de l’API et accédez à l’onglet **Applications** pour Azure Active Directory par défaut.

>[AZURE.NOTE] Si le répertoire par défaut Azure Active Directory n’est pas visible sur votre compte, contactez l’administrateur de l’abonnement Azure pour accorder les autorisations requises à votre compte. Pour plus d’informations sur l’emplacement de votre répertoire par défaut, reportez-vous à [localiser votre répertoire par défaut](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-portal).

![Créer l’application Azure Active Directory][api-management-add-aad-application]

Cliquez sur **Ajouter**, **Ajouter une application à mon entreprise se développe**et choisissez **l’application cliente Native**. Entrez un nom descriptif, puis cliquez sur la flèche suivant. Entrez une URL d’espace réservé tel que `http://resources` pour l' **URI de redirection**, telle qu’elle est un champ obligatoire, mais la valeur n’est pas utilisée plus tard. Cliquez sur la case à cocher pour enregistrer l’application.

Une fois que l’application est enregistrée, cliquez sur **configurer**, faites défiler jusqu'à la section **autorisations à d’autres applications** et cliquez sur **Ajouter une application**.

![Ajouter des autorisations][api-management-aad-permissions-add]

Sélectionnez **Windows Azure** **API de gestion de Service** , puis cliquez sur la case à cocher pour ajouter l’application.

![Ajouter des autorisations][api-management-aad-permissions]

Cliquez sur **Déléguer les autorisations** en regard de l’application **Windows Azure** **API de gestion de Service** nouvellement ajoutée, la case à cocher pour la **Gestion de Service accès Azure (aperçu)**et cliquez sur **Enregistrer**.

![Ajouter des autorisations][api-management-aad-delegated-permissions]

Avant d’appeler les API qui génèrent de la sauvegarde et de restaurer, il est nécessaire d’obtenir un jeton. L’exemple suivant utilise le package nuget [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pour récupérer le jeton.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace GetTokenResourceManagerRequests
    {
        class Program
        {
            static void Main(string[] args)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
                var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

                if (result == null) {
                    throw new InvalidOperationException("Failed to obtain the JWT token");
                }

                Console.WriteLine(result.AccessToken);

                Console.ReadLine();
            }
        }
    }

Remplacer `{tentand id}`, `{application id}`, et `{redirect uri}` à l’aide des instructions suivantes.

Remplacer `{tenant id}` avec l’id client de l’application Azure Active Directory que vous venez de créer. Vous pouvez accéder à l’id en cliquant sur les **points de terminaison de vue**.

![Points de terminaison][api-management-aad-default-directory]

![Points de terminaison][api-management-endpoint]

Remplacer `{application id}` et `{redirect uri}` à l’aide de l' **Id de Client** et l’URL à partir de la section de **Rediriger un URI** à partir de l’onglet de **configuration** de votre application Azure Active Directory. 

![Ressources][api-management-aad-resources]

Une fois que les valeurs sont spécifiées, l’exemple de code doit renvoyer un jeton similaire à l’exemple suivant.

![Jeton][api-management-arm-token]

Avant d’appeler les opérations de sauvegarde et de restauration décrites dans les sections suivantes, définir l’en-tête de la demande d’autorisation de votre appel reste.

    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>Un service de gestion de l’API de sauvegarde
Pour une gestion de l’API de sauvegarde service émettre la requête HTTP suivante :

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

où :

* `subscriptionId`-id de l’abonnement contenant le service de gestion de l’API que vous essayez de sauvegarde
* `resourceGroupName`-une chaîne sous la forme de « Api - par - défaut {service-région} » où `service-region` identifie la région Azure où la gestion de l’API de service vous tentez de sauvegarde est hébergé, par exemple :`North-Central-US`
* `serviceName`-le nom du service de gestion de l’API vous effectuez une sauvegarde de spécifié au moment de sa création
* `api-version`-remplacer par`2014-02-14`

Dans le corps de la demande, spécifiez le nom du compte cible stockage Azure, touche d’accès rapide, nom du conteneur de blob et nom de la sauvegarde :

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Définir la valeur de la `Content-Type` en-tête de demande pour `application/json`.

La sauvegarde est une longue opération qui peut prendre plusieurs minutes pour terminer.  Si la demande a abouti et que le processus de sauvegarde a été lancé que vous allez recevoir un `202 Accepted` code d’état de réponse avec une `Location` en-tête.  Vérifiez le 'GET' demandes vers l’URL dans le `Location` en-tête pour connaître le statut de l’opération. Lorsque la sauvegarde est en cours, vous continuerez de recevoir un code d’état « 202 accepté ». Code de réponse `200 OK` indique la réussite de l’opération de sauvegarde.

**Remarque**:

- **Conteneur** spécifié dans le corps de la demande **doit exister**.
* Lors de la sauvegarde est en cours, vous **n’essayez pas les opérations de gestion de service** comme référence ou rétrograder, changement de nom de domaine, etc.. 
* Restauration d’une **sauvegarde est garanti uniquement pour les 7 jours** depuis le moment de sa création. 
* **Les données d’utilisation** , utilisé pour créer l’analytique des rapports **n’est pas inclus** dans la sauvegarde. [Azure API gestion reste API][] permet d’extraire périodiquement des rapports analytique pour les conserver en lieu sûr.
* La fréquence avec laquelle vous effectuez des sauvegardes du service affectent votre objectif de point de récupération. Nous vous conseillons pour le réduire de mise en œuvre des sauvegardes régulières, ainsi que pour effectuer les sauvegardes à la demande après avoir apporté des modifications importantes à votre service de gestion de l’API.
* Les **modifications** apportées à la configuration du service (par exemple, API, stratégies, apparence de portail développeur) lors de l’opération de sauvegarde est en cours **ne peut pas être inclus dans la sauvegarde et par conséquent seront perdues**.

## <a name="step2"> </a>Restaurer un service de gestion de l’API
Pour restaurer une gestion de l’API de service à partir d’une sauvegarde créée précédemment effectuer la requête HTTP suivante :

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

où :

* `subscriptionId`-id de l’abonnement contenant le service de gestion de l’API que vous restaurez une sauvegarde en
* `resourceGroupName`-une chaîne sous la forme de « Api - par - défaut {service-région} » où `service-region` identifie la région Azure où le service de gestion de l’API que vous restaurez une sauvegarde en est hébergé, par exemple :`North-Central-US`
* `serviceName`-le nom de la gestion de l’API de service en cours de restauration dans spécifié au moment de sa création
* `api-version`-remplacer par`2014-02-14`

Dans le corps de la demande, spécifiez l’emplacement du fichier de sauvegarde, par exemple, nom de compte de stockage Azure, touche d’accès rapide, nom du conteneur de blob et nom de la sauvegarde :

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Définir la valeur de la `Content-Type` en-tête de demande pour `application/json`.

La restauration est une opération longue qui peut prendre plus de 30 minutes pour terminer.  Si la demande a abouti et que le processus de restauration a été lancé que vous allez recevoir un `202 Accepted` code d’état de réponse avec une `Location` en-tête.  Vérifiez le 'GET' demandes vers l’URL dans le `Location` en-tête pour connaître le statut de l’opération. Lorsque la restauration est en cours vous continuerez à recevoir des « accepté 202 » code d’état. Code de réponse `200 OK` indique la réussite de l’opération de restauration.

>[AZURE.IMPORTANT] **Le SKU** du service restauré dans **doit correspondre à** la référence SKU du service de sauvegarde en cours de restauration.
>
>**Les modifications** apportées à la configuration de service (par exemple, API, stratégies, apparence de portail développeur) lors de l’opération de restauration est en cours **peuvent être remplacés**.

## <a name="next-steps"></a>Étapes suivantes
Consultez les blogs Microsoft suivants pour deux différentes procédures pas à pas le processus de sauvegarde et de restauration.

-   [Répliquer des comptes de gestion API Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
    -   Merci à Gisela pour sa contribution à cet article.
-   [Gestion des API Azure : Sauvegarde et restauration de la Configuration](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    -   L’approche détaillée par Stuart ne correspond pas le Guide officiel, mais il est très intéressant.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[API Azure Management API REST]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 
