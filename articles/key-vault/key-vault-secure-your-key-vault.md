<properties
    pageTitle="Sécuriser votre coffre-fort clé | Microsoft Azure"
    description="Gérer les autorisations d’accès pour la clé coffre-fort pour la gestion des coffres-forts et des clés et des secrets. Modèle d’authentification et d’autorisation pour coffre-fort clé et comment sécuriser votre clé coffre-fort"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/07/2016"
    ms.author="ambapat"/>


# <a name="secure-your-key-vault"></a>Sécuriser votre clé coffre-fort

Coffre-fort de clé Azure est un service en nuage qui protège les clés de chiffrement et codes secrets (par exemple, les certificats, les chaînes de connexion, les mots de passe) pour vos applications en nuage. Dans la mesure où ces données sont sensibles et critiques, que vous souhaitez sécuriser l’accès à votre clés coffres-forts afin que seuls les autorisés les applications et les utilisateurs peuvent accéder à clé vault. Cet article fournit une vue d’ensemble du modèle d’accès aux clés de stockage en chambre forte, explique l’authentification et l’autorisation et explique comment sécuriser l’accès à la clé coffre-fort pour vos applications de cloud avec un exemple.

## <a name="overview"></a>Vue d’ensemble

Accès à un coffre-fort clé est contrôlée par le biais de deux interfaces distinctes : plan de gestion et plan de données. Pour les deux plans d’autorisation et d’authentification appropriée est nécessaire pour un appelant (un utilisateur ou une application) peut accéder au coffre-fort de clé. L’authentification établit l’identité de l’appelant, tandis que l’autorisation détermine les opérations que l’appelant est autorisé à exécuter.

Pour l’authentification à la fois le plan de gestion et plan de données utilisent Azure Active Directory. Pour l’autorisation, plan de gestion utilise le contrôle d’accès basé sur les rôles (RBAC) alors que le plan de données utilise la stratégie d’accès de clé de chambre forte.

Voici un bref aperçu des thèmes abordés :

[Authentification à l’aide d’Azure Active Directory](#authentication-using-azure-active-direcrory) - cette section explique comment un appelant authentifie avec Azure Active Directory pour accéder à une clé vault via le plan de gestion et plan de données. 

[Plan de gestion et plan de données](#management-plane-and-data-plane) - plan de gestion et plan de données sont deux plans d’accès utilisés pour accéder à votre clé coffre-fort. Chaque plan d’accès prend en charge des opérations spécifiques. Cette section décrit les points de terminaison d’accès, la prise en charge des opérations et accéder à la méthode de contrôle utilisée par chaque plan. 

[Contrôle du plan de gestion des accès](#management-plane-access-control) - dans cette section, que nous examinerons permettant d’accéder aux opérations de gestion du plan à l’aide du contrôle d’accès basé sur les rôles.

[Données de plan de contrôle d’accès](#data-plane-access-control) - cette section décrit comment utiliser la stratégie d’accès de clé de coffre-fort pour contrôler l’accès de plan de données.

[Exemple](#example) : cet exemple décrit comment configurer le contrôle d’accès pour votre coffre-fort clé autoriser les trois différentes équipes (équipe de sécurité, les développeurs/opérateurs et auditeurs) pour effectuer des tâches spécifiques pour développer, gérer et surveiller une application dans Azure.


## <a name="authentication-using-azure-active-directory"></a>Authentification à l’aide d’Azure Active Directory

Lorsque vous créez un coffre-fort de clé dans un abonnement Azure, il est automatiquement associé à des clients d’Azure Active Directory de l’abonnement. Tous les appelants (les utilisateurs et les applications) doivent être enregistrés dans ce client pour accéder à cette clé vault. Une application ou un utilisateur doit s’authentifier avec Azure Active Directory pour accéder aux clés coffre-fort. Cela s’applique aux deux accès plan du plan et des données de gestion. Dans les deux cas, une application peut accéder à clé coffre-fort de deux manières :

-  **utilisateur + accès de app** - cela est généralement pour les applications qui accèdent aux clés coffre-fort pour le compte d’un utilisateur connecté. PowerShell Azure, Azure Portal sont des exemples de ce type d’accès. Il existe deux façons pour accorder l’accès aux utilisateurs : eux consiste à accorder l’accès aux utilisateurs afin de coffre-fort clé accessibles à partir de n’importe quelle application et l’autre consiste à accorder l’accès à la clé coffre-fort uniquement lorsqu’ils utilisent une application spécifique (dénommée identité composée). 
-  **accès uniquement-app** - pour les applications qui s’exécutent de services daemon, etc. les tâches d’arrière-plan. Identité de l’application a accès à la chambre forte de clé.

Dans les deux types d’applications, l’application authentifie avec Azure Active Directory à l’aide de la [prise en charge des méthodes d’authentification](../active-directory/active-directory-authentication-scenarios.md) et acquiert un jeton. Méthode d’authentification utilisée dépend du type d’application. L’application utilise ce jeton, puis envoie la demande d’API REST clé coffre-fort. En cas d’accès de plan de gestion des demandes sont acheminées via le Gestionnaire de ressources Azure le point de terminaison. Lors de l’accès aux données, le parle d’applications directement à une clé de chambre forte de point de terminaison. Afficher plus de détails sur le [flux d’authentification entier](../active-directory/active-directory-protocols-oauth-code.md). 

Le nom de la ressource pour laquelle l’application demande un jeton est différent selon que l’application accède au plan de gestion ou un plan des données. Le nom de la ressource est donc Gestion plan ou données plan extrémités décrites dans le tableau dans une section ultérieure, en fonction de l’environnement Azure.

Ayant un mécanisme unique pour l’authentification à la gestion et de plan de données possède ses propres avantages :

- Organisations peuvent contrôler de manière centralisée l’accès à tous les coffres-forts principaux dans leur organisation.
- Si un utilisateur quitte, ils instantanément perdent l’accès à tous les coffres-forts principaux de l’organisation
- Les organisations peuvent personnaliser l’authentification via les options dans Azure Active Directory (par exemple, l’activation de l’authentification à plusieurs facteurs pour une sécurité accrue)

## <a name="management-plane-and-data-plane"></a>Plan de gestion et plan de données

Coffre-fort de clé Azure est un service de Azure disponible via le modèle de déploiement d’Azure le Gestionnaire de ressources. Lorsque vous créez un coffre-fort de clés, vous obtenez un conteneur virtuel dans lequel vous pouvez créer d’autres objets comme des secrets, des clés et des certificats. Puis vous accédez à votre clé coffre-fort à l’aide du plan de gestion et plan de données pour effectuer des opérations spécifiques. Interface de gestion de plan est utilisé pour gérer votre clé coffre-fort lui-même, telles que la création, de suppression, de mise à jour des attributs de clé coffre-fort et de définition de stratégies d’accès pour un plan de données. Interface de plan de données est utilisée pour ajouter, supprimer, modifier et utiliser les clés, les secrets et certificats stockés dans votre chambre forte de clé.

Les interfaces plan du plan et des données de gestion sont accessibles par le biais des points de terminaison différents (voir tableau). La deuxième colonne de la table décrit les noms DNS pour ces points de terminaison dans différents environnements d’Azure. La troisième colonne décrit les opérations que vous pouvez effectuer à partir de chaque plan d’accès. Chaque plan d’accès possède également son propre mécanisme de contrôle d’accès : pour le contrôle d’accès de plan de gestion est défini à l’aide du contrôle d’accès Azure Resource Manager Role-Based (RBAC), tandis que pour le contrôle d’accès de plan de données est défini à l’aide de la stratégie d’accès de clé de chambre forte.

| Plan d’accès | Points de terminaison de l’accès | Opérations | Mécanisme de contrôle d’accès|
|--------------|------------------|--------------------|--------|
| Plan de gestion|**Global :**<br> Management.Azure.com:443<br><br> **Chine Azure :**<br> Management.chinacloudapi.CN:443<br><br> **Azure du gouvernement américain :**<br> Management.usgovcloudapi.NET:443<br><br> **Allemagne Azure :**<br> Management.microsoftazure.de:443 | Chambre forte de la clé de création/lecture/mise à jour/suppression <br> Définir des stratégies d’accès pour la chambre forte clé<br>Balises de jeu pour chambre forte de clé | Accès basé sur le rôle de contrôle du Gestionnaire de ressources Azure (RBAC) |
| Plan de données | **Global :**<br> &lt;nom de la chambre forte&gt;. vault.azure.net:443<br><br> **Chine Azure :**<br> &lt;nom de la chambre forte&gt;. vault.azure.cn:443<br><br> **Azure du gouvernement américain :**<br> &lt;nom de la chambre forte&gt;. vault.usgovcloudapi.net:443<br><br> **Allemagne Azure :**<br> &lt;nom de la chambre forte&gt;. vault.microsoftazure.de:443 | Pour les clés : Décrypter, crypter, UnwrapKey, WrapKey, vérifier, signer, obtenir, liste, mettre à jour, créer, importer, supprimer, sauvegarde, restauration<br><br> Pour les secrets : obtenir, liste, ensemble, supprimer | Stratégie d’accès de clé de chambre forte|

Les gestion des données et plan de plan des contrôles d’accès fonctionnent indépendamment. Par exemple, si vous souhaitez accorder l’accès d’une application pour utiliser les touches dans un coffre-fort de clé, vous ne devez accorder des autorisations d’accès de plan données à l’aide de stratégies d’accès de clé de coffre-fort et aucun accès de plan de gestion n’est nécessaire pour cette application. À l’inverse, si vous souhaitez un utilisateur doit pouvoir lire les balises et propriétés de stockage en chambre forte, mais n’a pas accès à des clés, des secrets ou des certificats, vous pouvez accorder à cet utilisateur, '' un accès en lecture à l’aide de RBAC et aucun accès au plan de données n’est nécessaire.

## <a name="management-plane-access-control"></a>Contrôle du plan de gestion des accès

Le plan de gestion se compose des opérations qui affectent le coffre-fort clé lui-même. Par exemple, vous pouvez créer ou supprimer un coffre-fort de clé. Vous pouvez obtenir une liste des coffres-forts dans un abonnement. Vous pouvez récupérer les propriétés de clé coffre-fort (par exemple des SKU, des balises) et définir des stratégies qui contrôlent les utilisateurs et les applications qui peuvent accéder aux clés et les secrets dans le coffre-fort de clé d’accès de clé coffre-fort. Contrôle du plan de gestion des accès utilise RBAC. Consultez la liste complète des opérations de coffre-fort clés qui peuvent être effectuées via le plan de gestion de la table dans la précédente section. 

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès basé sur les rôles (RBAC)
Chaque abonnement Azure a un Azure d’Active Directory. Applications à partir de ce répertoire, les groupes et les utilisateurs peuvent accéder pour gérer les ressources de l’abonnement Azure qui utilisent le modèle de déploiement du Gestionnaire de ressources Azure. Ce type de contrôle d’accès est appelé contrôle d’accès basée sur les rôles (RBAC). Pour gérer cet accès, vous pouvez utiliser le [portail Azure](https://portal.azure.com/), les [Outils de l’interface CLI d’Azure](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou les [API de reste Azure le Gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Avec le modèle de gestionnaire de ressources d’Azure, créer votre coffre-fort clé dans une ressource groupe et contrôler l’accès au plan de gestion de ce coffre-fort clé à l’aide d’Azure Active Directory. Par exemple, vous pouvez accorder aux utilisateurs ou un groupe permet de gérer les coffres-forts principaux dans un groupe de ressources spécifique.

Vous pouvez accorder l’accès à des utilisateurs, des groupes et des applications au niveau d’une portée spécifique en assignant des rôles RBAC appropriés. Par exemple, pour accorder l’accès à un utilisateur de gérer les coffres-forts principaux, vous devez attribuer un rôle prédéfini « clé coffre-fort collaborateur » pour cet utilisateur à une étendue spécifique. La portée est dans ce cas un abonnement, un groupe de ressources ou simplement un coffre-fort de clé spécifique. Un rôle attribué au niveau de l’abonnement s’applique à tous les groupes de ressources et les ressources au sein de cet abonnement. Un rôle attribué au niveau de groupe de ressource s’applique à toutes les ressources dans ce groupe de ressources. Un rôle affecté à une ressource spécifique s’applique uniquement à cette ressource. Il existe plusieurs rôles prédéfinis (voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md)), et si les rôles prédéfinis ne correspondent pas à vos besoins vous pouvez également définir vos propres rôles.

>[AZURE.IMPORTANT] Notez que si un utilisateur a collaborateur (RBAC) des autorisations pour un plan de gestion de clés coffre-fort, lui s’accordent accéder au plan de données, par la définition de clé de chambre forte stratégie d’accès, qui contrôle l’accès au plan de données. Par conséquent, il est recommandé de contrôler étroitement qui a accès « Collaborateur » pour vos coffres-forts clés pour garantir des seules les personnes autorisées peuvent accéder et gérer votre clés coffres-forts, les clés, les secrets et les certificats.

## <a name="data-plane-access-control"></a>Contrôle d’accès de plan de données

Le plan de données vault clé se compose des opérations qui affectent les objets dans un coffre-fort clé, telles que des secrets, des clés et des certificats.  Cela inclut des opérations telles que la création de clé, importation, mise à jour, liste, sauvegarde et restauration, clés, les opérations cryptographiques comme signe, vérifier, crypter, décrypter, encapsuler, désencapsuler et définir des balises et autres attributs pour les clés. De la même façon, pour qu’il inclut des secrets, get, set liste, supprimer.

Accès de plan de données est accordée en définissant des stratégies d’accès pour un coffre-fort de clé. Un utilisateur, de groupe ou d’une application doit avoir des autorisations de collaborateur (RBAC) pour le plan de gestion pour un coffre-fort clé pouvoir définir des stratégies d’accès pour ce coffre-fort de clé. Un utilisateur, un groupe ou une application peut accéder pour effectuer des opérations spécifiques pour les clés ou les secrets dans une chambre forte de clé. clé vault prend en charge jusqu'à 16 entrées de stratégie d’accès pour un coffre-fort de clé. Créer un groupe de sécurité Active Directory de Azure et ajoutez des utilisateurs à ce groupe pour accorder l’accès de plan de données à plusieurs utilisateurs dans un coffre-fort de clé.

### <a name="key-vault-access-policies"></a>clé coffre-fort de stratégies d’accès

les stratégies d’accès clé coffre-fort accordent des autorisations pour les clés et les certificats, les secrets séparément. Par exemple, vous pouvez donner un accès utilisateur aux touches uniquement, mais pas des autorisations pour les secrets. Toutefois, les autorisations d’accès aux clés ou des secrets ou des certificats sont au niveau de la chambre forte. En d’autres termes, stratégie d’accès de clé de chambre forte ne gère pas les autorisations au niveau objet. Vous pouvez utiliser [Azure portal](https://portal.azure.com/), les [Outils de l’interface CLI d’Azure](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou le [coffre-fort clé API de gestion reste](https://msdn.microsoft.com/library/azure/mt620024.aspx) à définir des stratégies d’accès pour un coffre-fort de clé.

>[AZURE.IMPORTANT] Notez que les stratégies d’accès de clé de coffre-fort s’appliquent au niveau de la chambre forte. Par exemple, lorsqu’un utilisateur est autorisé à créer et supprimer des clés, elle peut effectuer ces opérations sur toutes les clés dans ce coffre-fort de clé.

## <a name="example"></a>Exemple

Supposons que vous développiez une application qui utilise un certificat pour SSL, le stockage Azure pour stocker des données et qui utilise une clé RSA 2048 bits pour les opérations de connexion. Supposons que cette application s’exécute dans une machine virtuelle (ou un ensemble d’échelle de machine virtuelle). Vous pouvez utiliser un coffre-fort clé pour stocker tous les secrets de l’application et coffre-fort de clé permet de stocker le certificat d’amorçage qui est utilisé par l’application pour authentifier avec Azure Active Directory.

Voici donc un récapitulatif de toutes les clés et les secrets pour être stockés dans un coffre-fort de clé.

- **Certificat SSL** - utilisé pour SSL
- **Clé de stockage** - utilisée pour obtenir l’accès à un compte de stockage
- **Clé RSA 2048 bits** - utilisé pour les opérations de connexion
- **Certificat d’amorçage** - en utilisé pour authentifier à Azure d’Active Directory, pour obtenir l’accès à la clé coffre-fort pour extraire la clé de stockage et d’utiliser la clé RSA pour la signature.

Maintenant nous allons répondre aux personnes qui sont de gestion, de déploiement et de l’audit de cette application. Nous allons utiliser trois rôles dans cet exemple.

- **Équipe de sécurité** - il s’agit généralement de personnel informatique à partir de « office du CSO (sécurité directeur) » ou équivalent, est responsable de la bonne conservation des secrets tels que des certificats SSL, des clés RSA utilisés pour la signature, les chaînes de connexion pour les bases de données, clés de compte de stockage.
- **Les développeurs/opérateurs** : ce sont des personnes qui développement cette application, puis le déployer dans Azure. En général, ils ne sont pas partie de l’équipe de sécurité et, par conséquent, ils ne devront pas accéder aux données sensibles, telles que les certificats SSL, les clés RSA, mais l’application qu'ils déploient doit avoir accès aux.
- **Comptes** - il s’agit généralement d’un autre ensemble de personnes, isolés des développeurs et informaticiens en général. Leur responsabilité est de vérifier la bonne utilisation et la gestion des certificats, des clés, etc. et de s’assurer du respect des normes de sécurité des données. 

Il existe un rôle plus qui se trouve en dehors de la portée de cette application, mais ici pertinente à mentionner, et qui serait l’abonnement (ou un groupe de ressources) administrateur. Administrateur d’abonnement définit les autorisations d’accès initiales pour l’équipe de sécurité. Ici, nous supposons que l’administrateur d’abonnement a accordé l’accès à l’équipe de sécurité à un groupe de ressources dans lequel toutes les ressources nécessaires pour ce résident de l’application.

Maintenant nous allons voir les actions exécutées par chaque rôle dans le cadre de cette application.

- **Équipe de sécurité**
    - Créer la clé coffres-forts
    - Activer clé vault journalisation
    - Ajouter des clés/codes secrets
    - Créer une sauvegarde des clés de récupération d’urgence
    - Définir la stratégie d’accès de clé de coffre-fort pour accorder des autorisations aux utilisateurs et aux applications d’effectuer des opérations spécifiques
    - ROULEAU périodiquement les clés/codes secrets
- **Les développeurs/opérateurs**
    - Obtenir des références à des données d’amorçage et des certificats SSL (empreintes), clé de stockage (secret URI) et la signature de clé (clé URI) à partir de l’équipe de sécurité
    - Développer et de déployer l’application qui accède à des clés et des secrets par programme
- **Auditeurs**
    - Examen des journaux de l’utilisation pour confirmer la bonne utilisation de la clé/secret et la conformité aux normes de sécurité des données

Maintenant nous allons voir quelles autorisations d’accès au coffre-fort de clés sont nécessaires à chaque rôle (et de l’application) pour effectuer les tâches affectées. 

| Rôle d’utilisateur    | Autorisations de plan de gestion | Autorisations de plan de données |
|--------------|------------------------------|------------------------|
|Équipe de sécurité|clé coffre-fort de collaborateur|Clés : sauvegarde, créer, supprimer, obtenir, importer, répertorier, restauration <br> Secrets : tous les|
|Les développeurs/opérateur| clé coffre-fort déployer l’autorisation afin que les ordinateurs virtuels qu’elles déploient peuvent extraire les secrets de la chambre forte de clé | Aucun |
|Auditeurs| Aucun | Clés : liste<br>Secrets : liste|
|Application| Aucun | Clés : signe<br>Secrets : obtenir |

>[AZURE.NOTE] Auditeurs doivent répertorier les autorisations pour les clés et les secrets afin qu’ils inspectent les attributs pour les clés et les secrets ne sont pas émis dans les journaux, telles que les balises, les dates d’activation et d’expiration.

En plus de l’autorisation de clé coffre-fort, tous les trois rôles a également besoin d’accéder à d’autres ressources. Par exemple, pour être en mesure de déployer des machines virtuelles (ou Web applications etc.). Les développeurs/les opérateurs doivent également accès « Collaborateur » pour ces types de ressources. Auditeurs ont besoin d’un accès en lecture au compte de stockage où se trouvent les journaux de la chambre forte de clé.

Étant donné que l’objectif de cet article est sécurisation de l’accès à votre clé coffre-fort, nous avons uniquement illustrent les différentes sections de cette rubrique et ignorer les détails concernant le déploiement des certificats, l’accès à des clés et des secrets par programme etc.. Ces détails sont déjà abordés. Déploiement de certificats stockés dans un coffre-fort clé aux machines virtuelles est couvertes dans un [billet de blog](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/), et [exemples de code](https://www.microsoft.com/download/details.aspx?id=45343) disponibles qui illustre l’utilisation de certificats d’amorçage pour s’authentifier sur Azure AD pour avoir accès à la chambre forte de clé.

La plupart des autorisations d’accès peut être accordée à l’aide d’Azure portal, mais pour accorder des autorisations granulaires, vous devrez peut-être utiliser PowerShell d’Azure (ou Azure CLI) pour obtenir le résultat souhaité. 

Les extraits de code PowerShell suivants supposent que :

- L’administrateur Active Directory de Azure a créé des groupes de sécurité qui représentent les trois rôles, à savoir des Auditors équipe de sécurité de Contoso, Devops App de Contoso, Contoso App. L’administrateur a également ajouté des utilisateurs pour les groupes auxquels qu'ils appartiennent.

- **ContosoAppRG** est le groupe de ressources où toutes les ressources résident. **contosologstorage** est l’endroit où les journaux sont stockés. 

- Compte coffre-fort clé **ContosoKeyVault** et de stockage utilisé pour les journaux de coffre-fort clé **contosologstorage** doit être dans le même emplacement Azure


Tout d’abord, l’administrateur d’abonnement affecte « clé coffre-fort collaborateur » et les rôles de « administrateur d’accès utilisateurs » à l’équipe de sécurité. Cela permet à l’équipe de sécurité gérer l’accès à d’autres ressources et gérer les coffres-forts clés dans le groupe de ressources ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Le script suivant illustre comment l’équipe de sécurité peut créer un coffre-fort de clé, le programme d’installation, de journalisation et définir des autorisations d’accès pour les autres rôles et de l’application. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionToKeys backup,create,delete,get,import,list,restore -PermissionToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devlopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $role

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionToKeys list -PermissionToSecrets list
```

Le rôle personnalisé défini, n’est peut être assigné à l’abonnement dans lequel le groupe de ressources de ContosoAppRG est créé. Si les mêmes rôles personnalisés doit être utilisés pour d’autres projets dans les autres abonnements, sa portée peut avoir plusieurs abonnements ajoutés.

L’affectation de rôle personnalisé pour les développeurs/opérateurs pour l’autorisation de déployer / « action » est portée au groupe de ressources. De cette façon uniquement les ordinateurs virtuels créés dans le groupe de ressources 'ContosoAppRG' obtenez les secrets (certificat SSL et certificat d’amorçage). Les ordinateurs virtuels créés par un membre de l’équipe dev/ops dans un autre groupe de ressources ne sera pas en mesure d’obtenir ces secrets, même si elles savaient que secrète URI.

Cet exemple illustre un scénario simple. Scénarios réels peuvent être plus complexes et vous devrez peut-être ajuster les autorisations pour votre clé coffre-fort en fonction de vos besoins. Par exemple, dans notre exemple, nous supposons que l’équipe sécurité fournira la clé et les références secrètes (URI et les empreintes) qui équipe les développeurs/opérateurs devez référencer dans leurs applications. Par conséquent, ils n’est pas nécessaire d’accorder aux développeurs/opérateurs tout accès de plan de données. Notez également que cet exemple se concentre sur la sécurisation de votre coffre-fort de clé. Similaire doit être envisagé pour sécuriser [vos ordinateurs virtuels](https://azure.microsoft.com/services/virtual-machines/security/), les [comptes de stockage](../storage/storage-security-guide.md) et les autres ressources Azure de trop.

>[AZURE.NOTE] Remarque : Cet exemple montre comment les coffre-fort accès sera verrouillé dans la production. Les développeurs doivent avoir leur propre abonnement ou les resourcegroup où ils disposent des autorisations maximales pour gérer leurs coffres-forts, de machines virtuelles et de compte de stockage où ils développent l’application.


## <a name="resources"></a>Ressources

-   [Contrôle d’accès basé sur rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    Cet article explique le contrôle d’accès basé sur le rôle de Azure Active Directory et comment il fonctionne.

-   [RBAC : Les rôles intégrée](../active-directory/role-based-access-built-in-roles.md)

    Cet article décrit en détail tous les rôles prédéfinis disponibles dans RBAC.

-   [Présentation du déploiement du Gestionnaire de ressources et déploiement classique](../resource-manager-deployment-model.md)

    Cet article explique le déploiement du Gestionnaire de ressources et les modèles de déploiement classiques et explique les avantages liés à l’aide des groupes de ressources et le Gestionnaire de ressources

-    [Gérer le contrôle d’accès basé sur les rôles avec PowerShell Azure](../active-directory/role-based-access-control-manage-access-powershell.md)

     Cet article explique comment gérer le contrôle d’accès basé sur les rôles avec Azure PowerShell

-   [Gestion du contrôle d’accès basé sur les rôles avec l’API REST](../active-directory/role-based-access-control-manage-access-rest.md)

    Cet article explique comment utiliser l’API REST pour gérer RBAC.

-   [Contrôle d’accès par rôle pour Microsoft Azure à partir de Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Voici un lien vers une vidéo sur Channel 9 de la conférence de la Ignite MS 2015. Dans cette session, ils parlent accéder aux fonctions de gestion et création de rapports dans Azure et d’Explorer les meilleures pratiques concernant la sécurisation de l’accès aux abonnements Azure à l’aide d’Azure Active Directory.

-   [Autoriser l’accès aux applications web à l’aide de OAuth 2.0 et Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)

    Cet article décrit le déroulement complet du OAuth 2.0 pour l’authentification avec Azure Active Directory.

-   [vault clé API reste de gestion](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Ce document est la référence de l’API reste à gérer votre coffre-fort clé par programme, y compris la définition de la stratégie d’accès clé coffre-fort.

-   [chambre forte de clé reste API](https://msdn.microsoft.com/library/azure/dn903609.aspx)

    Créer un lien vers une documentation de référence API REST de coffre-fort clé.

-   [Contrôle de l’accès aux clés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

    Créer un lien vers la documentation de référence de contrôle de l’accès clé.

-   [Contrôle d’accès secret](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

    Créer un lien vers la documentation de référence de contrôle de l’accès clé.

-   [Définition](https://msdn.microsoft.com/library/mt603625.aspx) et [suppression des](https://msdn.microsoft.com/library/mt619427.aspx) stratégie d’accès clé coffre-fort à l’aide de PowerShell

    Liens vers la documentation pour les applets de commande PowerShell gérer la stratégie d’accès de clé de chambre forte.

## <a name="next-steps"></a>Étapes suivantes

Pour un didacticiel démarré lors de l’obtention d’un administrateur, voir [Mise en route de la chambre forte de clé Azure](key-vault-get-started.md).

Pour plus d’informations sur la journalisation d’utilisation de clé coffre-fort, consultez [enregistrement des Azure coffre-fort de clé](key-vault-logging.md).

Pour plus d’informations sur l’utilisation de clés et secrets avec Azure coffre-fort de clés, voir [à propos des clés et des Secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Si vous avez des questions à propos de la clé coffre-fort, visitez le [coffre-fort clé Azure Forums](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
