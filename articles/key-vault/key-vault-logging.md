<properties
    pageTitle="Azure Vault clé enregistrement | Microsoft Azure"
    description="Utilisez ce didacticiel pour vous aider à démarrer avec Azure clé coffre-fort journalisation."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/31/2016"
    ms.author="cabailey"/>

# <a name="azure-key-vault-logging"></a>Enregistrement de coffre-fort de clé Azure #
Coffre-fort de clé Azure est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification en chambre forte de la clé](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction  
Une fois que vous avez créé un ou plusieurs des coffres-forts clés, vous souhaiterez probablement contrôler comment et quand vos coffres-forts clés sont accessibles et par qui. Pour cela, en activant l’enregistrement de clé coffre-fort, qui enregistre les informations dans un compte de stockage Azure que vous fournissez. Un nouveau conteneur nommé **perspectives-journaux-auditevent** est automatiquement créé pour votre compte de stockage spécifié, et vous pouvez utiliser ce même compte de stockage pour collecter des journaux pour les coffres-forts principaux multiples.

Vous pouvez accéder à vos informations d’enregistrement au maximum, 10 minutes après la clé de la chambre forte de cette opération. Dans la plupart des cas, il sera plus rapide que cela.  C’est à vous permet de gérer vos journaux dans votre compte de stockage :

- Utilisez les méthodes de contrôle des accès Azure standard pour sécuriser vos journaux en limitant leur accès.
- Supprimer les journaux que vous ne souhaitez plus conserver dans votre compte de stockage.

Utilisez ce didacticiel pour vous aider à démarrer avec Azure coffre-fort de clé de connexion, pour la création de votre compte de stockage, activer l’enregistrement et interpréter les informations de journalisation collectées.  


>[AZURE.NOTE]  Ce didacticiel n’inclut pas d’instructions pour la création des secrets, des clés ou des coffres-forts clés. Pour obtenir ces informations, consultez [mise en route de la chambre forte de clé Azure](key-vault-get-started.md). Ou, pour les instructions de l’Interface de ligne de commande de plusieurs plates-formes, consultez [ce didacticiel équivalent](key-vault-manage-with-cli.md).
>
>Actuellement, vous ne pouvez pas configurer Azure clé coffre-fort dans le portail Azure. Utilisez ces instructions PowerShell d’Azure.

Les journaux que vous collectez peuvent être visualisés à l’aide d’analytique de journal à partir de la Suite de gestion des opérations. Pour plus d’informations, reportez-vous à la section [solution de coffre-fort de clé Azure (aperçu) dans le journal Analytique](../log-analytics/log-analytics-azure-key-vault.md).

Pour plus d’informations sur le coffre-fort de clé d’Azure, consultez [Quel est le coffre-fort de clé Azure ?](key-vault-whatis.md)

## <a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez disposer des éléments suivants :

- Coffre-fort de clé existant que vous utilisez.  
- PowerShell Azure, **une version minimale de la version 1.0.1**. Pour installer PowerShell d’Azure et l’associer à votre abonnement Azure, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md). Si vous avez déjà installé les Azure PowerShell et que vous ne connaissez pas la version, à partir de la console PowerShell d’Azure, tapez `(Get-Module azure -ListAvailable).Version`.  
- Espace de stockage suffisant sur Azure pour les journaux de la chambre forte de clé.


## <a id="connect"></a>Se connecter à vos abonnements ##

Démarrer une session PowerShell d’Azure et vous connecter à votre compte Azure avec la commande suivante :  

    Login-AzureRmAccount

Dans la fenêtre contextuelle, entrez votre nom d’utilisateur de compte Azure et d’un mot de passe. PowerShell Azure obtiennent tous les abonnements qui sont associés à ce compte et par défaut, utilise la première.

Si vous disposez de plusieurs abonnements, vous devrez peut-être spécifier un spécifique qui a été utilisé pour créer votre coffre-fort de clé Azure. Tapez ce qui suit pour afficher les abonnements de votre compte :

    Get-AzureRmSubscription

Ensuite, pour spécifier l’abonnement associé à votre coffre-fort clé que se connectent, tapez :

    Set-AzureRmContext -SubscriptionId <subscription ID>

Pour plus d’informations sur la configuration d’Azure PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Créer un nouveau compte de stockage pour les journaux ##

Vous pouvez utiliser un compte de stockage existant pour les journaux, nous allons créer un nouveau compte de stockage qui sera consacré aux journaux de chambre forte de clé. Pour plus de commodité pour nous avons pour cela spécifier plus tard, nous allons stocker les détails dans une variable nommée **sa**.

Pour faciliter le supplémentaire de gestion, nous utiliserons également le même groupe de ressources que celui qui contient notre clé coffre-fort. [Didacticiel de mise en route](key-vault-get-started.md), ce groupe de ressources est nommé **ContosoResourceGroup** , et nous allons continuer à utiliser l’emplacement de l’Asie de l’est. Remplacez ces valeurs pour votre propre, selon le cas :

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Si vous décidez d’utiliser un compte de stockage existant, il doit utiliser le même abonnement comme votre coffre-fort clé et qu’il doit utiliser le modèle de déploiement du Gestionnaire de ressources, plutôt que le modèle de déploiement standard.

## <a id="identify"></a>Identifier le coffre-fort de clés pour les journaux ##

Dans notre didacticiel mise en route démarré, notre nom de coffre-fort de clé a été **ContosoKeyVault**, afin de continuer à utiliser ce nom et de stocker les informations dans une variable nommée **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Activer la journalisation ##

Pour activer la journalisation pour clé de la chambre forte, nous allons utiliser l’applet de commande Set-AzureRmDiagnosticSetting, ainsi que les variables que nous avons créé pour notre nouveau compte de stockage et notre clé coffre-fort. Nous allons également définir le **-activé** indicateur sur **$true** et la valeur AuditEvent (la seule catégorie pour l’enregistrement de la chambre forte de clé), la catégorie :


    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

La sortie de ce inclut :

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Cette opération confirme que l’enregistrement est maintenant activé pour Password vault clé, l’enregistrement des informations sur votre compte de stockage.

Éventuellement vous pouvez également définir la stratégie de rétention pour les journaux telles que les anciens journaux sont automatiquement supprimés. Par exemple, définir une stratégie de rétention à l’aide d’indicateur **- RetentionEnabled** sur **$true** et que **RetentionInDays -** paramètre **90** journaux datant de plus de 90 jours sont automatiquement supprimés.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Ce qui est consigné :

- Toutes les demandes d’API REST authentifiées sont consignés, ce qui inclut les demandes qui ont échoué en raison d’autorisations d’accès, des erreurs système ou des requêtes incorrectes.
- Opérations sur la clé de chambre forte lui-même, ce qui inclut la création, la suppression, les stratégies d’accès paramètre clé de stockage en chambre forte, et mise à jour des attributs de clé coffre-fort telles que des balises.
- Opérations sur les clés et les secrets dans le coffre-fort de clé, qui inclut la création, la modification ou la suppression de ces clés ou les secrets ; opérations de signe, vérifier, crypter, décrypter, retour à la ligne et désencapsuler clés, obtenir des secrets, les clés de la liste et les secrets et leurs versions.
- Demandes non authentifiées qui génère une réponse 401. Par exemple, les requêtes qui n’ont pas un jeton de support, ou sont mal formé ou qui ont expiré ou ont un jeton non valide.  


## <a id="access"></a>Accéder aux journaux de votre ##

Journaux de coffre-fort clés sont stockés dans le conteneur de **perspectives-journaux-auditevent** dans le compte de stockage que vous avez fourni. Pour répertorier tous les objets BLOB dans ce conteneur, tapez :

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

Le résultat peut ressembler à quelque chose de semblable à ceci :

**Uri du conteneur : https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Nom**

**----**

**resourceId = / abonnements/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/fournisseurs/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId = / abonnements/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/fournisseurs/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

** resourceId = / abonnements/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/fournisseurs/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json***


Comme vous pouvez le voir dans cette sortie, les blobs suivent une convention d’affectation de noms : **resourceId =<ARM resource ID>/y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

Les valeurs de date et d’heure utilisent l’heure UTC.

Dans la mesure où le même compte de stockage peut être utilisé pour collecter des journaux pour plusieurs ressources, l’ID de ressource complet dans le nom de l’objet blob est très utile pour accéder à ou télécharger uniquement les objets BLOB dont vous avez besoin. Mais avant cela, nous étudierons tout d’abord la télécharger tous les objets BLOB.

Tout d’abord, créez un dossier pour télécharger les objets BLOB. Par exemple :

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Puis, obtenez une liste de tous les BLOB :  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Tuyau de cette liste par l’intermédiaire de « Get-AzureStorageBlobContent » pour télécharger les objets BLOB dans le dossier de destination :

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Lorsque vous exécutez cette commande deuxième, le **/** délimiteur dans les noms d’objet blob créer une structure de dossier complet dans le dossier de destination, et que cette structure sera utilisée pour télécharger et stocker les objets BLOB en tant que fichiers.

Pour télécharger sélectivement les objets BLOB, utilisez des caractères génériques. Par exemple :

- Si vous avez plusieurs coffres-forts clés et que vous souhaitez télécharger qu’un seul coffre-fort de clé dans les journaux, nommé CONTOSOKEYVAULT3 :

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Si vous avez plusieurs groupes de ressources et que vous souhaitez télécharger les journaux de tout un groupe de ressources, utilisez `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Si vous souhaitez télécharger tous les journaux pour le mois de janvier 2016, utilisez `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Vous êtes maintenant prêt à observer ce qui se trouve dans les journaux. Mais avant de passer à qui, en plus de deux paramètres de Get-AzureRmDiagnosticSetting et que vous souhaitiez connaître :

- Pour interroger l’état des paramètres de diagnostic pour votre ressource de clé de coffre-fort :`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- Pour désactiver la journalisation pour votre ressource de clé de coffre-fort :`Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interpréter vos journaux en chambre forte de la clé ##

Les BLOB est stockées sous forme de texte mis en forme comme un objet blob JSON. Il s’agit d’un exemple d’entrée de journal à partir de l’exécution `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


Le tableau suivant répertorie les noms de champ et descriptions.


| Nom de champ        | Description |
| ------------- |-------------|
| heure      | Date et heure (UTC).|
| resourceId      | ID de ressource de gestionnaire de ressources Azure. Pour les journaux de la chambre forte de clé, il s’agit toujours l’ID de ressource de stockage en chambre forte de clé.|
| operationName      | Nom de l’opération, comme indiqué dans le tableau suivant.|
| operationVersion      | Il s’agit de la version de l’API REST demandée par le client.|
| catégorie      | Pour les journaux de la chambre forte de clé, AuditEvent est la seule valeur disponible.|
| resultType      | Résultat de la demande de l’API REST.|
| resultSignature      | État HTTP.|
| resultDescription     | Description supplémentaire sur le résultat, lorsqu’elles sont disponibles.|
| durationMs      | Temps que nécessaire pour traiter la demande de l’API REST, en millisecondes. Cela n’inclut pas la latence du réseau, le temps de que vous mesurez côté client peut ne pas correspondre cette fois-ci.|
| callerIpAddress      | Adresse IP du client qui a effectué la demande.|
| ID de corrélation      | Un GUID facultatif qui peut s’écouler le client pour mettre en corrélation des journaux de côté client avec les journaux de service côté (coffre-fort de clé).|
| identité      | Identité du jeton qui ont été présentées lors de la création de la demande de l’API REST. Il s’agit généralement d’un « utilisateur », « service principal » ou une combinaison « utilisateur + appId », comme dans le cas d’une demande résultant d’une applet de commande PowerShell d’Azure.|
| propriétés      | Ce champ contient des informations différentes en fonction de l’opération (NomOpération). Contient des informations sur le client (chaîne useragent transmise par le client), dans la plupart des cas, l’URI de requête API REST exacte et code d’état HTTP. En outre, lorsqu’un objet est retourné à la suite d’une demande (par exemple, KeyCreate ou VaultGet) qu’il contient également l’URI de clé (sous la forme « id »), coffre-fort URI ou URI de Secret.|




Les valeurs de champ **NomOpération** sont au format de ObjectVerb. Par exemple :

- Toutes les opérations de clé coffre-fort ont le ' coffre-fort`<action>`' de format, tel que `VaultGet` et `VaultCreate`.

- Toutes les opérations de clés ont la « clé`<action>`' de format, tel que `KeySign` et `KeyList`.

- Toutes les opérations secrètes ont le « Secret`<action>`' de format, tel que `SecretGet` et `SecretListVersions`.

Le tableau suivant répertorie les NomOpération et commande d’API REST correspondante.

| operationName        | Commande d’API REST |
| ------------- |-------------|
| Authentification      | Via le point de terminaison Azure Active Directory|
| VaultGet      | [Obtenir des informations sur un coffre-fort de clé](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [Créer ou mettre à jour d’un coffre-fort de clé](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [Supprimer un clé coffre-fort](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [Mise à jour d’un coffre-fort de clé](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [Liste de tous les coffres-forts principaux dans un groupe de ressources](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [Créer une clé](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [Obtenir des informations sur une clé](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [Importe une clé dans un coffre-fort](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [Sauvegarde d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx).|
| KeyDelete      | [Supprimer une clé](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [Restaurer une clé](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [Signer avec une clé](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [Vérifier à l’aide d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [Encapsulez une clé](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [Désencapsuler une clé](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [Crypter avec une clé](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [Décrypter à l’aide d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [Mise à jour d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| KeyList      | [Répertorier les clés dans une chambre forte](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [Répertorier les versions d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [Créer un secret](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [Obtenir le secret](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [Mise à jour d’un secret](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [Supprimer un secret](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [Liste des secrets dans une chambre forte](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [Liste des versions d’un secret](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>Étapes suivantes ##

Pour voir un didacticiel qui utilise Azure clé coffre-fort dans une application web, [Utilisez Azure clé Vault à partir d’une Application Web](key-vault-use-from-web-application.md).

Pour les références de programmation, consultez [le coffre-fort de clé Azure guide du développeur](key-vault-developers-guide.md).

Pour obtenir une liste des applets de commande PowerShell 1.0 de Azure pour Azure clé coffre-fort, consultez [Azure clé coffre-fort applets de commande](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Pour un didacticiel sur la rotation de la clé et le journal d’audit avec Azure clé coffre-fort, voir [comment le programme d’installation en chambre forte de la clé avec la rotation des clés de bout en bout et d’audit](key-vault-key-rotation-log-monitoring.md).
