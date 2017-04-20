<properties
   pageTitle="Créez le service Azure principal avec PowerShell | Microsoft Azure"
   description="Décrit l’utilisation de PowerShell d’Azure pour créer une application Active Directory et le principal du service et lui accorder un accès aux ressources par le biais de contrôle d’accès basé sur les rôles. Il montre comment authentifier l’application avec un mot de passe ou un certificat."
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
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Utilisez PowerShell d’Azure pour créer une entité de sécurité du service d’accès aux ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI Azure](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)

Lorsque vous avez une application ou un script qui doit accéder aux ressources, vous ne souhaitez pas exécuter ce processus sous vos propres informations d’identification. Vous pouvez avoir différentes autorisations que vous souhaitez pour l’application, et que vous ne souhaitez pas que l’application de continuer à utiliser vos informations d’identification si la modification de vos responsabilités. Au lieu de cela, vous créez une identité de l’application qui inclut des informations d’authentification et les attributions de rôle. Chaque fois que l’application s’exécute, il authentifie avec ces informations d’identification. Cette rubrique vous indique comment utiliser [PowerShell d’Azure](powershell-install-configure.md) pour définir tout ce dont vous avez besoin d’une application pour s’exécuter sous sa propre identité et les informations d’identification.

Avec PowerShell, vous avez deux options pour l’authentification de l’application d’Active Directory :

 - mot de passe
 - certificat

Cette rubrique indique comment utiliser les deux options dans PowerShell. Si vous avez l’intention d’ouvrir une session Azure à partir d’une infrastructure de programmation (les Python, Ruby ou Node.js), l’authentification de mot de passe peut être votre meilleure option. Avant de décider s’il faut utiliser un mot de passe ou un certificat, reportez-vous à la section des [exemples d’applications](#sample-applications) pour obtenir des exemples d’authentification dans les différentes infrastructures.

## <a name="active-directory-concepts"></a>Concepts relatifs à Active Directory

Dans cet article, vous créez deux objets - l’application d’Active Directory (AD) et le service principal. L’application d’Active Directory est une représentation globale de votre application. Il contient les informations d’identification (un id d’application et un mot de passe ou certificat). Le service principal est la représentation locale de votre application dans Active Directory. Il contient l’attribution de rôle. Cette rubrique se concentre sur une application mono-utilisateur où l’application est conçue pour s’exécuter au sein d’une seule organisation. Vous utilisez généralement les applications mono-utilisateur pour métier à des applications qui s’exécutent au sein de votre organisation. Dans une application mono-utilisateur, vous avez une application AD et principal d’un service.

Vous vous demandez peut-être - pourquoi les deux objets ? Cette approche rend plus de sens lorsque vous considérez les applications mutualisées. Vous utilisez généralement les applications mutualisées pour des applications de logiciel en tant que service (SaaS), dans lequel votre application s’exécute dans un grand nombre de souscriptions différente. Pour les applications de plusieurs utilisateurs, vous avez une application AD et plusieurs entités de service (un dans chaque Active Directory qui autorise l’accès à l’application). Pour configurer une application partagée, consultez le [guide du développeur d’autorisation avec l’API du Gestionnaire de ressources Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Autorisations requises

Pour terminer cette rubrique, vous devez disposer des autorisations suffisantes dans Azure Active Directory et votre abonnement Azure. Plus précisément, vous devez être en mesure de créer une application dans Active Directory et affecter l’entité de service à un rôle. 

Dans Active Directory, votre compte doit être un administrateur (par exemple, **Un administrateur Global** ou **Administrateur d’utilisateur**). Si votre compte est affecté au rôle **d’utilisateur** , vous devez demander à un administrateur d’élever vos autorisations.

Dans votre abonnement, votre compte doit disposer de `Microsoft.Authorization/*/Write` accès, qui est accordé par le rôle de [propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur d’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Si votre compte est affecté au rôle **contributeur** , vous recevez une erreur lorsque vous tentez d’affecter l’entité de service à un rôle. Une fois encore, votre abonnement administrateur doit accorder un accès suffisant.

Maintenant, passez à la section pour l’authentification de [mot de passe](#create-service-principal-with-password) ou de [certificats](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Créez le service principal avec le mot de passe

Dans cette section, vous effectuez les étapes pour :

- créer l’application de publicité avec un mot de passe
- créer l’entité service
- attribuer le rôle de lecteur à l’entité du service

Pour rapidement effectuer ces étapes, consultez les trois applets de commande suivantes. 

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Examinons ces étapes plus en détail pour vous assurer de que comprendre le processus.

1. Connectez-vous à votre compte.

        Add-AzureRmAccount

1. Créez une nouvelle application de Active Directory en fournissant un nom d’affichage, l’URI qui décrit votre application, l’URI identifiant votre application et le mot de passe pour l’identité de votre application.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     Pour les applications mono-utilisateur, l’URI ne sont pas validés.
     
     Si votre compte n’a pas les [autorisations requises](#required-permissions) dans Active Directory, vous voyez un message d’erreur indiquant « Authentication_Unauthorized » ou « aucun abonnement ne trouvé dans le contexte ».

1. Examinez l’objet application. 

        $app
        
     Notez en particulier la propriété **ApplicationId** , ce qui est nécessaire pour la création d’entités de service, les affectations de rôle et d’acquérir le jeton d’accès.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Créez un principal de service pour votre application en passant l’id de l’application de l’application d’Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Accordez les autorisations du service principal de votre abonnement. Dans cet exemple, vous ajoutez l’entité de sécurité du service au rôle de **lecteur** , ce qui accorde l’autorisation de lire toutes les ressources de l’abonnement. Pour les autres rôles, reportez-vous à la section [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md). Pour le paramètre de **nom principal de service** , fournir **ApplicationId** que vous avez utilisé lors de la création de l’application. 

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Si votre compte ne dispose pas d’autorisations suffisantes pour affecter un rôle, vous voyez le message d’erreur. Ce message indique que votre compte **ne dispose pas d’autorisation pour effectuer l’action « Microsoft.Authorization/roleAssignments/write » sur l’étendue « abonnements / {guid} »**. 

Voilà ! Votre application de publicité et les principaux de service sont configurés. La section suivante vous montre comment ouvrir une session avec les informations d’identification par le biais de PowerShell. Si vous souhaitez utiliser les informations d’identification dans votre application de code, vous pouvez accéder aux [exemples d’applications](#sample-applications). 

### <a name="provide-credentials-through-powershell"></a>Fournir des informations d’identification via PowerShell

Maintenant, vous devez ouvrir une session en tant que l’application pour effectuer des opérations.

1. Créer un objet **PSCredential** qui contient vos informations d’identification en exécutant la commande **Get-Credential** . Vous devez **ApplicationId** avant d’exécuter cette commande par conséquent, assurez-vous que vous avez disponible pour coller.

        $creds = Get-Credential

2. Vous devez entrer vos informations d’identification. Le nom d’utilisateur, utilisez **ApplicationId** que vous avez utilisé lors de la création de l’application. Le mot de passe, utilisez celui que vous avez spécifié lors de la création du compte.

     ![Entrez les informations d’identification](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. Lorsque vous ouvrez une session comme entité de service, vous devez fournir l’id client de l’annuaire pour votre application d’Active Directory. Un client est une instance d’Active Directory. Si vous ne disposez que d’un abonnement, vous pouvez utiliser :

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Si vous avez plus d’un abonnement, spécifiez l’abonnement sur lequel réside votre Active Directory. Pour plus d’informations, consultez [comment Azure abonnements sont associés à Active Directory de Azure](./active-directory/active-directory-how-subscriptions-associated-directory.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Connectez-vous en tant que l’entité de service en spécifiant que ce compte est une entité de sécurité du service et en fournissant l’objet d’informations d’identification. 

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Vous êtes maintenant authentifié en tant que le service principal de l’application d’Active Directory que vous avez créé.

### <a name="save-access-token-to-simplify-log-in"></a>Enregistrer le jeton d’accès pour simplifier la connexion

Pour éviter de fournir les informations d’identification principal de service chaque fois qu’il a besoin ouvrir une session, vous pouvez enregistrer le jeton d’accès.

1. Pour utiliser le jeton d’accès actuel dans une session ultérieure, enregistrer le profil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Ouvrez le profil et examinez son contenu. Notez qu’il contient un jeton d’accès. 
        
2. Enregistrement manuel nouveau, simplement charger le profil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Le jeton d’accès arrive à expiration, afin que l’utilisation d’un profil enregistré ne fonctionne que pour tant que le jeton est valide.
        
## <a name="create-service-principal-with-certificate"></a>Créez le service principal avec certificat

Dans cette section, vous effectuez les étapes pour :

- créer un certificat auto-signé
- créer l’application d’Active Directory avec le certificat
- créer l’entité service
- attribuer le rôle de lecteur à l’entité du service

Pour rapidement effectuer ces étapes avec Azure PowerShell 2.0 sur Windows 10 ou présentation technique de Windows Server 2016, voir les applets de commande suivantes. 

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Examinons ces étapes plus en détail pour vous assurer de que comprendre le processus. Cet article explique également comment effectuer les tâches lors de l’utilisation des versions antérieures des systèmes d’exploitation ou de PowerShell d’Azure.

### <a name="create-the-self-signed-certificate"></a>Créer le certificat auto-signé

La version de PowerShell disponible avec Windows 10 et présentation technique de Windows Server 2016 a une applet de commande **New-SelfSignedCertificate** mis à jour pour la génération d’un certificat auto-signé. Systèmes d’exploitation antérieurs ont l’applet de commande New-SelfSignedCertificate, mais il n’offre pas les paramètres requis pour cette rubrique. Au lieu de cela, vous devez importer un module pour générer le certificat. Cette rubrique illustre les deux approches pour générer le certificat basé sur le système d’exploitation que vous avez. 

- Si vous avez **10 de Windows ou de la présentation technique de Windows Server 2016**, exécutez la commande suivante pour créer un certificat auto-signé : 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Si vous **ne disposez pas de Windows 10 ou présentation technique de Windows Server 2016**, vous devez télécharger le [autosigné Générateur de certificat](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) de Microsoft Script Center. Extraire le contenu et importer l’applet de commande dont vous avez besoin.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     Ensuite, générer le certificat.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Vous avez votre certificat et que vous pouvez commencer la création de votre application d’Active Directory.

### <a name="create-the-active-directory-app-and-service-principal"></a>Créer l’application d’Active Directory et le service principal

1. Récupérer la valeur de clé à partir du certificat.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Connectez-vous à votre compte Azure.

        Add-AzureRmAccount

3. Créez une nouvelle application de Active Directory en fournissant un nom d’affichage, l’URI qui décrit votre application, l’URI identifiant votre application et le mot de passe pour l’identité de votre application.

     Si vous avez Azure PowerShell 2.0 l’utilisation (août 2016 ou version ultérieure), l’applet de commande suivante :

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Si vous avez Azure PowerShell 1.0, utilisez l’applet de commande suivante :
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Pour les applications mono-utilisateur, l’URI ne sont pas validés.
    
    Si votre compte n’a pas les [autorisations requises](#required-permissions) dans Active Directory, vous voyez un message d’erreur indiquant « Authentication_Unauthorized » ou « aucun abonnement ne trouvé dans le contexte ».
        
    Examinez l’objet application. 

        $app

    Notez la propriété **ApplicationId** , ce qui est nécessaire pour la création d’entités de service, les affectations de rôle et l’acquisition des jetons d’accès.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Créez un principal de service pour votre application en passant l’id de l’application de l’application d’Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Accordez les autorisations du service principal de votre abonnement. Dans cet exemple, vous ajoutez l’entité de sécurité du service au rôle de **lecteur** , ce qui accorde l’autorisation de lire toutes les ressources de l’abonnement. Pour les autres rôles, reportez-vous à la section [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md). Pour le paramètre de **nom principal de service** , fournir **ApplicationId** que vous avez utilisé lors de la création de l’application.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Si votre compte ne dispose pas d’autorisations suffisantes pour affecter un rôle, vous voyez le message d’erreur. Ce message indique que votre compte **ne dispose pas d’autorisation pour effectuer l’action « Microsoft.Authorization/roleAssignments/write » sur l’étendue « abonnements / {guid} »**.

Voilà ! Votre application de publicité et les principaux de service sont configurés. La section suivante vous montre comment ouvrir une session avec un certificat par le biais de PowerShell.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fournir le certificat par le biais de script PowerShell automatisée

Lorsque vous ouvrez une session comme entité de service, vous devez fournir l’id client de l’annuaire pour votre application d’Active Directory. Un client est une instance d’Active Directory. Si vous ne disposez que d’un abonnement, vous pouvez utiliser :

    $tenant = (Get-AzureRmSubscription).TenantId
    
Si vous avez plus d’un abonnement, spécifiez l’abonnement sur lequel réside votre Active Directory. Pour plus d’informations, reportez-vous à [administrer votre annuaire AD Azure](./active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Pour l’authentification dans votre script, spécifiez le compte est un service principal et fournir l’empreinte numérique du certificat, l’id d’application et les id de client. Pour automatiser votre script, vous pouvez stocker ces valeurs en tant que variables d’environnement et les récupérer lors de l’exécution, ou vous pouvez les inclure dans votre script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

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
- Pour une explication plus détaillée des applications et des entités de service, consultez [les objets Application et Service Principal](./active-directory/active-directory-application-objects.md). 
- Pour plus d’informations sur l’authentification Active Directory, consultez [Scénarios d’authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).



