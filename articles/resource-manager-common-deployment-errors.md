<properties
   pageTitle="Résoudre les erreurs courantes de déploiement d’Azure | Microsoft Azure"
   description="Décrit comment résoudre les erreurs courantes lors du déploiement de ressources vers Azure à l’aide du Gestionnaire de ressources Azure."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="Erreur de déploiement, déploiement d’azure, déployer vers azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Résoudre les erreurs courantes de déploiement d’Azure avec le Gestionnaire de ressources Azure

Cette rubrique décrit comment vous pouvez résoudre courantes que vous pouvez rencontrer des erreurs de déploiement d’Azure. Si vous avez besoin de plus d’informations sur la cause du problème à votre déploiement, tout d’abord [Afficher les opérations de déploiement](resource-manager-troubleshoot-deployments-portal.md) et puis revenir ultérieurement à cet article pour vous aider à la résolution de l’erreur. Les sections de cette rubrique répertorient le code d’erreur.

## <a name="invalid-template"></a>Modèle non valide

Cette erreur peut provenir de plusieurs types d’erreurs. 

### <a name="syntax-error"></a>Erreur de syntaxe

Si vous recevez un message d’erreur qui indique la validation du modèle a échoué, peut avoir un problème de syntaxe dans votre modèle. 

    Code=InvalidTemplate 
    Message=Deployment template validation failed

Cette erreur est facile, car les expressions de modèle peuvent être complexes. Par exemple, l’affectation de nom suivant d’un compte de stockage contient un jeu de crochets, trois fonctions, trois jeux de parenthèses, un seul jeu de guillemets simples et une propriété :

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Si vous ne fournissez pas la syntaxe de correspondance, le modèle de produit une valeur différente à votre intention.

Lorsque vous recevez ce type d’erreur, examinez attentivement la syntaxe de l’expression. Envisagez d’utiliser un éditeur de JSON comme [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou de [Code de Visual Studio](resource-manager-vs-code.md), ce qui peut vous avertir à propos des erreurs de syntaxe. 

### <a name="incorrect-segment-lengths"></a>Longueurs de segment incorrect

Une autre erreur de modèle non valide se produit lorsque le nom de la ressource n’est pas au format correct.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

Une ressource de niveau racine doit avoir un segment inférieur dans le nom de celui dans le type de ressource. Chaque segment est différenciée par une barre oblique. Dans l’exemple suivant, le type a deux segments et le nom comporte un seul segment, afin qu’il soit un **nom valide**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",
      ...
    }

Mais l’exemple suivant n’est **pas un nom valide** parce qu’il a le même nombre de segments comme type.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",
      ...
    }

Pour les ressources de l’enfant, le type et le nom ont le même nombre de segments. Ce nombre de segments de sens, car le nom complet et un type pour l’enfant inclut le nom du parent et le type. Par conséquent, le nom complet a toujours un segment de moins que le type complet. 

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",
                    ...
                }
            ]
        }
    ]

Les segments de droite peut être difficile avec des types de gestionnaire de ressources qui sont appliqués sur les fournisseurs de ressources. Par exemple, l’application d’un verrou de ressource à un site web nécessite un type à quatre segments. Par conséquent, le nom est trois segments :

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
        ...
    }

### <a name="copy-index-is-not-expected"></a>Index de la copie n’est pas attendu.

Vous rencontrez cette erreur **InvalidTemplate** lorsque vous avez appliqué l’élément **copie** à une partie du modèle qui ne prend pas en charge cet élément. Vous ne pouvez appliquer que l’élément de copie pour un type de ressource. Vous ne pouvez pas appliquer la copie à une propriété dans un type de ressource. Par exemple, vous appliquez la copie à une machine virtuelle, mais vous ne pouvez pas l’appliquer sur les disques du système d’exploitation pour une machine virtuelle. Dans certains cas, vous pouvez convertir une ressource enfant à une ressource parent pour créer une boucle de copie. Pour plus d’informations sur l’utilisation de copie, reportez-vous à la section [créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure](resource-group-create-multiple.md).

### <a name="parameter-is-not-valid"></a>Le paramètre n’est pas valide

Si le modèle spécifie les valeurs autorisées pour un paramètre et si vous fournissez une valeur qui n’est pas une de ces valeurs, vous recevez un message similaire à l’erreur suivante :

    Code=InvalidTemplate; 
    Message=Deployment template validation failed: 'The provided value {parameter vaule} 
    for the template parameter {parameter name} is not valid. The parameter value is not 
    part of the allowed value(s)

Double vérifier les valeurs autorisées dans le modèle et en fournir un au cours du déploiement.

## <a name="not-found-or-resource-not-found"></a>Pas (ou ressource introuvable)

Lorsque votre modèle inclut le nom d’une ressource qui ne peut pas être résolu, vous recevez un message d’erreur semblable à :

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

Si vous tentez de déployer la ressource manquante dans le modèle, vérifiez si vous avez besoin d’ajouter une dépendance. Le Gestionnaire de ressources optimise le déploiement en créant des ressources en parallèle, lorsque cela est possible. Si une ressource doit être déployée après une autre ressource, vous devez utiliser l’élément **dependsOn** dans votre modèle pour créer une dépendance sur les autres ressources. Par exemple, lorsque vous déployez une application web, le plan de Service d’application doit exister. Si vous n’avez pas spécifié dépend de l’application web sur le plan de Service de l’application, le Gestionnaire de ressources crée les ressources en même temps. Vous recevez une erreur indiquant que la ressource de plan de Service d’application est introuvable, car il n’existe pas encore lorsque vous tentez de définir une propriété sur l’application web. Vous éviter cette erreur en affectant à la dépendance dans l’application web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }
 
Vous voyez également cette erreur lorsque la ressource se trouve dans un groupe de ressources différent que celui en cours de déploiement. Dans ce cas, utilisez la [fonction de resourceId](resource-group-template-functions.md#resourceid) pour obtenir le nom qualifié complet de la ressource.

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    } 

Si vous essayez d’utiliser les fonctions de [référence](resource-group-template-functions.md#referenc) ou [listKeys](resource-group-template-functions.md#listkeys) avec une ressource qui ne peut pas être résolue, vous recevez le message d’erreur suivant :

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource 
    group {resource group name} was not found.

Recherchez une expression contenant la fonction de **référence** . Vérifiez que les valeurs des paramètres sont corrects.

## <a name="storage-account-already-exists-or-already-taken"></a>Compte de stockage existe déjà (ou déjà pris)

Pour les comptes de stockage, vous devez fournir un nom pour la ressource qui est unique dans Azure. Si vous ne fournissez pas un nom unique, vous recevez une erreur comme :

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

Vous pouvez créer un nom unique en concaténant votre convention d’affectation de noms avec le résultat de la fonction [uniqueString](resource-group-template-functions.md#uniquestring) .

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

Si vous déployez un compte de stockage portant le même nom qu’un compte de stockage existant dans votre abonnement, mais fournissez un emplacement différent, vous recevez une erreur indiquant que le compte de stockage existe déjà dans un autre emplacement. Supprimer le compte de stockage existant, soit fournir le même emplacement que le compte de stockage existant.

## <a name="account-name-invalid"></a>Nom de compte non valide

Vous voyez l’erreur **AccountNameInvalid** lorsque vous essayez d’attribuer à un compte de stockage un nom qui inclut des caractères interdits. Les noms de compte de stockage doivent être comprise entre 3 et 24 caractères et utiliser des chiffres et des lettres minuscules uniquement.

## <a name="no-registered-provider-found"></a>Aucun fournisseur enregistré trouvé

Lorsque vous déployez des ressources, vous pouvez recevoir le message d’erreur suivant et le message :

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation} 
    and API version {api-version} for type {resource-type}.

Vous recevez cette erreur pour l’une des trois raisons :

1. Emplacement non pris en charge pour le type de ressource
2. Version de l’API non prises en charge pour le type de ressource
3. Le fournisseur de ressources n’a pas été enregistré pour votre abonnement

Le message d’erreur devrait vous donner des suggestions pour les emplacements pris en charge et les versions de l’API. Vous pouvez modifier votre modèle à l’une des valeurs proposées. La plupart des fournisseurs sont enregistrées automatiquement par le portail Azure ou l’interface de ligne de commande que vous utilisez, mais pas toutes. Si vous n’avez pas utilisé un fournisseur de ressources particulier avant, vous devrez inscrire ce fournisseur. Vous pouvez en savoir plus sur les fournisseurs de ressources à l’aide de PowerShell ou Azure CLI.

### <a name="powershell"></a>PowerShell

Pour voir votre statut d’enregistrement, utilisez **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Pour inscrire un fournisseur, utilisez le **Registre-AzureRmResourceProvider** et fournir le nom du fournisseur de ressources que vous souhaitez enregistrer.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Pour obtenir les emplacements pris en charge pour un type particulier de ressource, utilisez :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Pour obtenir les versions API prises en charge pour un type particulier de ressource, utilisez :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### <a name="azure-cli"></a>CLI Azure

Pour savoir si le fournisseur est inscrit, utilisez le `azure provider list` commande.

    azure provider list

Pour enregistrer un fournisseur de ressources, utilisez les `azure provider register` de commande et spécifiez l' *espace de noms* à inscrire.

    azure provider register Microsoft.Cdn

Pour voir les emplacements pris en charge et les versions de l’API pour un fournisseur de ressources, utilisez :

    azure provider show -n Microsoft.Compute --json > compute.json

## <a name="operation-not-allowed"></a>Opération non autorisée

Vous pouvez avoir des problèmes lors de déploiement dépasse un quota, ce qui pourrait être par groupe de ressources, les abonnements, les comptes et les autres étendues. Par exemple, votre abonnement peut être configuré pour limiter le nombre de cœurs pour une zone. Si vous tentez de déployer un ordinateur virtuel avec plus de cœurs à la quantité autorisée, vous recevez une erreur indiquant que le quota a été dépassé.
Pour plus d’informations de quota complète, consultez [abonnement Azure et les limites de service, les quotas et les contraintes](azure-subscription-service-limits.md).

Pour examiner les quotas de votre abonnement pour noyaux, vous pouvez utiliser la `azure vm list-usage` commande CLI Azure. L’exemple suivant illustre que le quota de base pour un compte d’essai gratuit est 4 :

    azure vm list-usage

Qui retourne :

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Si vous déployez un modèle qui crée plus de quatre cœurs dans la région Ouest américaine, vous obtenez une erreur de déploiement qui ressemble à :

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core. 
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

Ou dans PowerShell, vous pouvez utiliser l’applet de commande **Get-AzureRmVMUsage** .

    Get-AzureRmVMUsage

Qui retourne :

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

Dans ces cas, vous devez accéder au portail et un problème de prise en charge pour augmenter votre quota pour la région dans lequel vous souhaitez déployer le fichier.

> [AZURE.NOTE] N’oubliez pas que pour les groupes de ressources, le quota pour chaque région, pas pour tout l’abonnement. Si vous avez besoin déployer 30 cœurs ouest des États-Unis, vous devez demander 30 noyaux de gestionnaire de ressources dans l’ouest des États-Unis. Si vous devez déployer 30 cœurs dans une des régions auxquels vous avez accès, vous demandez à 30 noyaux de gestionnaire de ressources dans toutes les régions.

## <a name="invalid-content-link"></a>Lien de contenu non valide

Lorsque vous recevez le message d’erreur :

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

Vous avez tenté probablement créer un lien vers un modèle imbriqué qui n’est pas disponible. Vérifiez l’URI que vous avez fournie pour le modèle imbriqué. Si le modèle existe dans un compte de stockage, vérifiez que l’URI est accessible. Vous devrez peut-être passer un jeton d’associations de sécurité. Pour plus d’informations, consultez [l’aide de modèles liés avec le Gestionnaire de ressources Azure](resource-group-linked-templates.md).

## <a name="authorization-failed"></a>Échoué de l’autorisation

Vous pouvez recevoir une erreur lors du déploiement car le compte ou le service principal d’essayer de déployer les ressources n’a pas accès à ces actions. Azure Active Directory active vous ou votre administrateur pour contrôler les identités qui peut accéder à quelles ressources avec un degré élevé de précision. Par exemple, si votre compte est affecté au rôle de lecteur, vous n’êtes pas en mesure de créer des ressources. Dans ce cas, vous voyez un message d’erreur indiquant que l’autorisation a échoué.

Pour plus d’informations à propos du contrôle d’accès basé sur les rôles, consultez [Contrôle d’accès Azure Role-Based](./active-directory/role-based-access-control-configure.md).

En plus du contrôle d’accès basé sur les rôles, vos actions de déploiement peuvent être limitées par les stratégies sur l’abonnement. Par le biais de stratégies, l’administrateur peut appliquer les conventions de toutes les ressources déployées dans l’abonnement. Par exemple, un administrateur peut exiger qu’une valeur de balise particulière est fournie pour un type de ressource. Si vous ne répondent pas à des critères de la stratégie, vous recevez une erreur pendant le déploiement. Pour plus d’informations sur les stratégies, reportez-vous à la section [Utiliser une stratégie pour la gestion des ressources et de contrôler l’accès](resource-manager-policy.md).

## <a name="troubleshooting-virtual-machines"></a>Résolution des problèmes de machines virtuelles

| Erreur | Articles |
| -------- | ----------- |
| Erreurs d’extension de script personnalisé | [Échecs d’extension de mémoire virtuelle de Windows](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />ou<br />[Échecs d’extension Linux VM](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Erreurs de provisionnement d’images de système d’exploitation | [Nouvelles erreurs de mémoire virtuelle de Windows](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />ou<br />[Nouvelles erreurs Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md ) |
| Échecs d’allocation | [Échecs d’allocation de mémoire virtuelle de Windows](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />ou<br />[Échecs d’allocation de Linux VM](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Secure Shell (SSH) erreurs lors de la tentative de connexion | [Connexions sécurisées Shell Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Erreurs de connexion à l’application en cours d’exécution sur l’ordinateur virtuel | [Application s’exécutant sur une machine virtuelle de Windows](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />ou<br />[Application s’exécutant sur une VM Linux](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Erreurs de connexion Bureau à distance | [Connexions Bureau à distance à la machine virtuelle de Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Résolu en redéployant des erreurs de connexion | [Redéployer l’ordinateur virtuel pour le nouveau nœud Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Erreurs de service cloud | [Problèmes de déploiement de service cloud](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## <a name="troubleshooting-other-services"></a>Dépannage des autres services

Le tableau suivant n’est pas une liste complète des rubriques de dépannage pour Azure. Au lieu de cela, il se concentre sur les problèmes liés au déploiement ou la configuration des ressources. Si vous avez besoin d’aide pour résoudre les problèmes d’exécution avec une ressource, consultez la documentation de ce service Azure.

| Service | Article |
| -------- | -------- |
| Automation | [Conseils de dépannage pour les erreurs courantes dans Azure Automation](./automation/automation-troubleshooting-automation-errors.md) |
| Pile Azure | [Résolution des problèmes de pile de Microsoft Azure](./azure-stack/azure-stack-troubleshooting.md) |
| Pile Azure | [Les applications Web et pile Azure](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md ) |
| Fabrique de données | [Résolution des problèmes de Data Factory](./data-factory/data-factory-troubleshoot.md) |
| TISSU de service | [Résoudre les problèmes courants lorsque vous déployez des services sur Azure Fabric de Service](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Récupération de site | [Surveiller et résoudre les problèmes de protection pour les ordinateurs virtuels et les serveurs physiques](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Stockage | [Contrôler, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Résoudre les problèmes de déploiement de périphérique StorSimple](./storsimple/storsimple-troubleshoot-deployment.md) |
| Base de données SQL | [Résoudre les problèmes de connexion à la base de données de SQL Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| Entrepôt de données SQL | [Dépannage de SQL Azure Data Warehouse](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="understand-when-a-deployment-is-ready"></a>Déterminer quand un déploiement prêt

Le Gestionnaire de ressources Azure signale une réussite sur un déploiement lorsque tous les fournisseurs de retour avec succès du déploiement. Toutefois, ce message ne signifie pas nécessairement que votre groupe de ressources est « active et prêt pour les utilisateurs ». Par exemple, un déploiement devrez télécharger les mises à niveau, attendre les ressources sans modèle ou installer des scripts complexes. Le Gestionnaire de ressources ne sait pas lorsque ces tâches terminées, car ils ne sont pas des activités qui suit d’un fournisseur. Dans ces cas, il peut être temps avant vos ressources sont prêts à l’emploi du monde réel. Par conséquent, vous devez vous attendre que l’état du déploiement réussit longtemps avant que votre déploiement peut être utilisé.

Vous pouvez empêcher Azure de signaler le succès du déploiement, toutefois, en créant un script personnalisé pour votre modèle personnalisé. Le script sait comment surveiller la totalité du déploiement pour la mise en réseau et retourne avec succès uniquement lorsque les utilisateurs peuvent interagir avec la totalité du déploiement. Si vous souhaitez vous assurer que votre extension est la dernière à exécuter, utilisez la propriété **dependsOn** dans votre modèle.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations sur l’audit des actions, consultez [opérations de vérification avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour en savoir plus sur les actions pour déterminer les erreurs au cours du déploiement, reportez-vous à la section [Afficher les opérations de déploiement](resource-manager-troubleshoot-deployments-portal.md).
