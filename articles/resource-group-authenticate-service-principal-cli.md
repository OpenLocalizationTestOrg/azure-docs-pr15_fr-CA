<properties
   pageTitle="Créez le service principal avec l’interface CLI d’Azure | Microsoft Azure"
   description="Décrit comment Azure CLI permet de créer une application Active Directory et le principal du service et de lui accorder l’accès aux ressources par le biais de contrôle d’accès basé sur les rôles. Il montre comment authentifier l’application avec un mot de passe ou un certificat."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/30/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Azure CLI permet de créer une entité de sécurité du service d’accès aux ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI Azure](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)


Lorsque vous avez une application ou un script qui doit accéder aux ressources, vous ne souhaitez pas exécuter ce processus sous vos propres informations d’identification. Vous pouvez avoir différentes autorisations que vous souhaitez pour l’application, et que vous ne souhaitez pas que l’application de continuer à utiliser vos informations d’identification si la modification de vos responsabilités. Au lieu de cela, vous créez une identité de l’application qui inclut des informations d’authentification et les attributions de rôle. Chaque fois que l’application s’exécute, il authentifie avec ces informations d’identification. Cette rubrique vous indique comment utiliser [l’Infrastructure du langage commun pour Mac, Linux et Windows Azure](xplat-cli-install.md) pour configurer une application pour s’exécuter sous sa propre identité et les informations d’identification.

Avec l’interface CLI d’Azure, vous avez deux options pour l’authentification de l’application d’Active Directory :

 - mot de passe
 - certificat

Cette rubrique indique comment utiliser les deux options dans Azure CLI. Si vous avez l’intention d’ouvrir une session Azure à partir d’une infrastructure de programmation (les Python, Ruby ou Node.js), l’authentification de mot de passe peut être votre meilleure option. Avant de décider s’il faut utiliser un mot de passe ou un certificat, reportez-vous à la section des [exemples d’applications](#sample-applications) pour obtenir des exemples d’authentification dans les différentes infrastructures.

## <a name="active-directory-concepts"></a>Concepts relatifs à Active Directory

Dans cet article, vous créez deux objets - l’application d’Active Directory (AD) et le service principal. L’application d’Active Directory est une représentation globale de votre application. Il contient les informations d’identification (un id d’application et un mot de passe ou certificat). Le service principal est la représentation locale de votre application dans Active Directory. Il contient l’attribution de rôle. Cette rubrique se concentre sur une application mono-utilisateur où l’application est conçue pour s’exécuter au sein d’une seule organisation. Vous utilisez généralement les applications mono-utilisateur pour métier à des applications qui s’exécutent au sein de votre organisation. Dans une application mono-utilisateur, vous avez une application AD et principal d’un service.

Vous vous demandez peut-être - pourquoi les deux objets ? Cette approche rend plus de sens lorsque vous considérez les applications mutualisées. Vous utilisez généralement les applications mutualisées pour des applications de logiciel en tant que service (SaaS), dans lequel votre application s’exécute dans un grand nombre de souscriptions différente. Pour les applications de plusieurs utilisateurs, vous avez une application AD et plusieurs entités de service (un dans chaque Active Directory qui autorise l’accès à l’application). Pour configurer une application partagée, consultez le [guide du développeur d’autorisation avec l’API du Gestionnaire de ressources Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Autorisations requises

Pour terminer cette rubrique, vous devez disposer des autorisations suffisantes dans Azure Active Directory et votre abonnement Azure. Plus précisément, vous devez être en mesure de créer une application dans Active Directory et affecter l’entité de service à un rôle. 

Dans Active Directory, votre compte doit être un administrateur (par exemple, **Un administrateur Global** ou **Administrateur d’utilisateur**). Si votre compte est affecté au rôle **d’utilisateur** , vous devez demander à un administrateur d’élever vos autorisations.

Dans votre abonnement, votre compte doit disposer de `Microsoft.Authorization/*/Write` accès, qui est accordé par le rôle de [propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur d’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Si votre compte est affecté au rôle **contributeur** , vous recevez une erreur lorsque vous tentez d’affecter l’entité de service à un rôle. Une fois encore, votre abonnement administrateur doit accorder un accès suffisant.

Maintenant, passez à la section pour l’authentification de [mot de passe](#create-service-principal-with-password) ou de [certificats](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Créez le service principal avec le mot de passe

Dans cette section, vous suivez les étapes pour créer l’application de publicité avec un mot de passe et attribuez le rôle de lecteur au service principal.

Examinons ces étapes.

1. Connectez-vous à votre compte.

        azure login

1. Vous avez deux options pour la création de l’application d’Active Directory. Vous pouvez créer l’application d’Active Directory et le service principal en une seule étape, ou les créer séparément. Créer en une seule étape si vous n’avez pas besoin de spécifier une page d’accueil et d’un identificateur URI pour votre application. Créer séparément si vous avez besoin de définir ces valeurs pour une application web. Les deux options sont présentées à cette étape.

     - Pour créer l’application d’Active Directory et le service principal en une seule étape, indiquez le nom de l’application et un mot de passe, comme indiqué dans la commande suivante :
     
            azure ad sp create -n exampleapp -p {your-password}     
     
     - Pour créer l’application AD séparément, indiquez le nom de l’application, une page d’accueil URI, identificateur URI et un mot de passe, comme indiqué dans la commande suivante :
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>

         La commande précédente renvoie une valeur AppId. Pour créer une entité de service, fournir cette valeur comme paramètre dans la commande suivante :
     
            azure ad sp create -a <AppId>
     
     Si votre compte n’a pas les [autorisations requises](#required-permissions) dans Active Directory, vous voyez un message d’erreur indiquant « Authentication_Unauthorized » ou « aucun abonnement ne trouvé dans le contexte ».
    
     Pour les deux options, la nouvelle entité de service est retournée. L' **Id d’objet** est nécessaire lorsque vous accordez des autorisations. Le guid de liste avec les **Noms principaux de Service** est nécessaire lors de la connexion. Ce guid est la même valeur que l’id de l’application. Dans les exemples d’applications, cette valeur correspond à l' **ID de Client**. 
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Accordez les autorisations du service principal de votre abonnement. Dans cet exemple, vous ajoutez l’entité de sécurité du service au rôle de **lecteur** , ce qui accorde l’autorisation de lire toutes les ressources de l’abonnement. Pour les autres rôles, reportez-vous à la section [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md). Pour le paramètre de **nom principal de service** , fournir **ObjectId** que vous avez utilisé lors de la création de l’application. 

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     Si votre compte ne dispose pas d’autorisations suffisantes pour affecter un rôle, vous voyez le message d’erreur. Ce message indique que votre compte **ne dispose pas d’autorisation pour effectuer l’action « Microsoft.Authorization/roleAssignments/write » sur l’étendue « abonnements / {guid} »**. 

Voilà ! Votre application de publicité et les principaux de service sont configurés. La section suivante vous montre comment ouvrir une session avec les informations d’identification via l’interface CLI d’Azure. Si vous souhaitez utiliser les informations d’identification dans votre application de code, vous n’avez pas besoin continuer avec cette rubrique. Vous pouvez accéder aux [exemples d’applications](#sample-applications) pour obtenir des exemples d’enregistrement avec id d’application et de votre mot de passe. 

### <a name="provide-credentials-through-azure-cli"></a>Fournir des informations d’identification via la CLI d’Azure

Maintenant, vous devez ouvrir une session en tant que l’application pour effectuer des opérations.

1. Lorsque vous ouvrez une session comme entité de service, vous devez fournir l’id client de l’annuaire pour votre application d’Active Directory. Un client est une instance d’Active Directory. Pour récupérer l’id de client pour votre abonnement actuellement authentifié, utilisez :

        azure account show

     Qui retourne :

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Si vous avez besoin obtenir l’id client d’un autre abonnement, utilisez la commande suivante :

        azure account show -s {subscription-id}

2. Si vous devez récupérer l’id de client à utiliser pour la connexion, utilisez :

        azure ad sp show -c exampleapp --json

     La valeur à utiliser pour la connexion est le guid répertorié dans les noms principaux de service.

        [
          {
            "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "7132aca4-1bdb-4238-ad81-996ff91d8db4"
            ]
          }
        ]

3. Connectez-vous en tant que l’entité de sécurité du service.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}

    Vous êtes invité pour le mot de passe. Fournir le mot de passe que vous avez spécifié lors de la création de l’application d’Active Directory.

        info:    Executing command login
        Password: ********

Vous êtes maintenant authentifié comme l’entité du service de l’entité du service que vous avez créé.

## <a name="create-service-principal-with-certificate"></a>Créez le service principal avec certificat

Dans cette section, vous effectuez les étapes pour :

- créer un certificat auto-signé
- créer l’application d’Active Directory avec le certificat et l’entité du service
- attribuer le rôle de lecteur à l’entité du service

Pour effectuer ces étapes, vous devez avoir [OpenSSL](http://www.openssl.org/) est installé.

1. Créer un certificat auto-signé.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combiner les clés publique et privées.

        cat privkey.pem cert.pem > examplecert.pem

3. Ouvrez le fichier **examplecert.pem** et recherchez la longue séquence de caractères entre **---début certificat---** et **---fin certificat---**. Copier les données de certificat. Vous transmettez ces données en tant que paramètre lors de la création du service principal.

1. Connectez-vous à votre compte.

        azure login

1. Vous avez deux options pour la création de l’application d’Active Directory. Vous pouvez créer l’application d’Active Directory et le service principal en une seule étape, ou les créer séparément. Créer en une seule étape si vous n’avez pas besoin de spécifier une page d’accueil et d’un identificateur URI pour votre application. Créer séparément si vous avez besoin de définir ces valeurs pour une application web. Les deux options sont présentées à cette étape.

     - Pour créer l’application d’Active Directory et le service principal en une seule étape, fournir le nom de l’application et les données de certificat, comme indiqué dans la commande suivante :
     
            azure ad sp create -n exampleapp --cert-value <certificate data>
     
     - Pour créer l’application AD séparément, indiquez le nom de l’application, une page d’accueil URI, identificateur URI et les données de certificat, comme indiqué dans la commande suivante :
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value <certificate data>

         La commande précédente renvoie une valeur AppId. Pour créer une entité de service, fournir cette valeur comme paramètre dans la commande suivante :
     
            azure ad sp create -a <AppId>
  
     Si votre compte n’a pas les [autorisations requises](#required-permissions) dans Active Directory, vous voyez un message d’erreur indiquant « Authentication_Unauthorized » ou « aucun abonnement ne trouvé dans le contexte ».
    
     Pour les deux options, la nouvelle entité de service est retournée. L’Id d’objet est nécessaire lorsque vous accordez des autorisations. Le guid de liste avec les **Noms principaux de Service** est nécessaire lors de la connexion. Ce guid est la même valeur que l’id de l’application. Dans les exemples d’applications, cette valeur correspond à l' **ID de Client**. 
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Accordez les autorisations du service principal de votre abonnement. Dans cet exemple, vous ajoutez l’entité de sécurité du service au rôle de **lecteur** , ce qui accorde l’autorisation de lire toutes les ressources de l’abonnement. Pour les autres rôles, reportez-vous à la section [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md). Pour le paramètre de **nom principal de service** , fournir **ObjectId** que vous avez utilisé lors de la création de l’application. 

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     Si votre compte ne dispose pas d’autorisations suffisantes pour affecter un rôle, vous voyez le message d’erreur. Ce message indique que votre compte **ne dispose pas d’autorisation pour effectuer l’action « Microsoft.Authorization/roleAssignments/write » sur l’étendue « abonnements / {guid} »**. 

### <a name="provide-certificate-through-automated-azure-cli-script"></a>Fournir des certificats par le biais de script automatisé de CLI d’Azure

Maintenant, vous devez ouvrir une session en tant que l’application pour effectuer des opérations.

1. Lorsque vous ouvrez une session comme entité de service, vous devez fournir l’id client de l’annuaire pour votre application d’Active Directory. Un client est une instance d’Active Directory. Pour récupérer l’id de client pour votre abonnement actuellement authentifié, utilisez :

        azure account show

     Qui retourne :

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Si vous avez besoin obtenir l’id client d’un autre abonnement, utilisez la commande suivante :

        azure account show -s {subscription-id}

1. Pour récupérer l’empreinte numérique du certificat et supprimez les caractères inutiles, utilisez :

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Qui retourne une valeur de l’empreinte numérique semblable à :

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Si vous devez récupérer l’id de client à utiliser pour la connexion, utilisez :

        azure ad sp show -c exampleapp

     La valeur à utiliser pour la connexion est le guid répertorié dans les noms principaux de service.

        [
          {
            "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "4fd39843-c338-417d-b549-a545f584a745",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "4fd39843-c338-417d-b549-a545f584a745"
            ]
          }
        ]

1. Connectez-vous en tant que l’entité de sécurité du service.

        azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

Vous êtes maintenant authentifié en tant que le service principal de l’application d’Active Directory que vous avez créé.

## <a name="sample-applications"></a>Exemples d’applications

Les exemples d’applications suivantes montrent comment ouvrir une session en tant que l’entité de sécurité du service.

**.NET**

- [Déployer un SSH activé l’ordinateur virtuel avec un modèle avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gérer les ressources Azure et les groupes de ressources avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Mise en route de ressources - déployer à l’aide du modèle de gestionnaire de ressources Azure - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Mise en route de ressources - gérer le groupe de ressources - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Déployer un SSH activé l’ordinateur virtuel à un modèle dans les Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gestion des ressources Azure et des groupes de ressources avec les Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Déployer un SSH activé l’ordinateur virtuel à un modèle dans Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gérer les ressources et groupes avec Node.js ressources Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Déployer un SSH activé l’ordinateur virtuel avec un modèle en Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gestion des ressources Azure et Ruby, les groupes de ressources](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## <a name="next-steps"></a>Étapes suivantes
  
- Pour obtenir la procédure détaillée sur l’intégration d’une application dans Azure pour la gestion des ressources, consultez le [guide du développeur d’autorisation avec l’API du Gestionnaire de ressources Azure](resource-manager-api-authentication.md).
- Pour obtenir plus d’informations sur l’utilisation de certificats et la CLI d’Azure, consultez [authentification par certificats avec des entités de Service Azure à partir de la ligne de commande sous Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx). 
