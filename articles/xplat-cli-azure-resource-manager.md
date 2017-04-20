
<properties
    pageTitle="Gestion des ressources avec la CLI d’Azure | Microsoft Azure"
    description="Permet de gérer les groupes et les ressources Azure Azure Interface de ligne de commande (CLI)"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="danlep"/>

# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>L’infrastructure du langage commun Azure permet de gérer les ressources et groupes ressources Azure


> [AZURE.SELECTOR]
- [Portail](azure-portal/resource-group-portal.md) 
- [CLI Azure](xplat-cli-azure-resource-manager.md)
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [API REST](resource-manager-rest-api.md)


L’Interface de ligne de commande (CLI Azure) Azure est un des nombreux outils que vous pouvez utiliser pour déployer et gérer les ressources avec le Gestionnaire de ressources. Cet article présente les méthodes courantes pour gérer les ressources et groupes ressources Azure à l’aide de la CLI Azure en mode de Gestionnaire des ressources. Pour plus d’informations sur l’utilisation de l’interface CLI pour déployer des ressources, consultez [ressources de déploiement avec les modèles du Gestionnaire de ressources et de la CLI d’Azure](resource-group-template-deploy-cli.md). Pour des informations sur les ressources Azure et Gestionnaire de ressources, consultez la [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md).

>[AZURE.NOTE] Pour gérer les ressources Azure avec la CLI d’Azure, vous devez [installer l’interface CLI d’Azure](xplat-cli-install.md)et [Connectez-vous à Azure](xplat-cli-connect.md) à l’aide de la `azure login` commande. Assurez-vous que l’infrastructure CLI est en mode de Gestionnaire des ressources (exécuter `azure config mode arm`). Si vous avez effectué ces opérations, vous êtes prêt à l’emploi.



## <a name="get-resource-groups-and-resources"></a>Obtenir des ressources et des groupes de ressources

### <a name="resource-groups"></a>Groupes de ressources

Pour obtenir une liste de tous les groupes de ressources dans votre abonnement et leurs emplacements, exécutez cette commande.

    azure group list
    

### <a name="resources"></a>Ressources
 Pour répertorier toutes les ressources dans un groupe, par exemple avec le nom *testRG*, utilisez la commande suivante.

    azure resource list testRG

Pour afficher une ressource au sein du groupe, tel qu’un ordinateur virtuel nommé *MyUbuntuVM*, utilisez une commande semblable à la suivante.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
Notez le paramètre **Microsoft.Compute/virtualMachines** . Ce paramètre indique le type de la ressource que vous demandez des informations sur.
    
>[AZURE.NOTE]Lorsque vous utilisez les commandes **azure ressource** autre que la commande de **liste** , vous devez spécifier la version de l’API de la ressource avec le paramètre **-o** . Si vous ne savez pas sur la version de l’API, consultez le fichier de modèle et de trouver le champ apiVersion pour la ressource. Pour plus d’informations sur les versions des API dans le Gestionnaire de ressources, consultez les [fournisseurs du Gestionnaire de ressources, les régions, les versions de l’API et les schémas](resource-manager-supported-services.md).

Lorsque vous affichez les détails sur une ressource, il est souvent utile d’utiliser le `--json` paramètre. Ce paramètre rend la sortie plus lisible, car certaines valeurs sont des structures imbriquées ou des collections. L’exemple suivant illustre la retournant les résultats de la commande **show** sous la forme d’un document JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] Si vous le souhaitez, enregistrer les données JSON dans fichier à l’aide de la &gt; caractère pour diriger la sortie vers un fichier. Par exemple :
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### <a name="tags"></a>Balises

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Gestion des ressources


Pour ajouter une ressource telle qu’un compte de stockage à un groupe de ressources, exécutez une commande semblable à :

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"
    
En plus de spécifier la version de l’API de la ressource avec le paramètre **-o** , utilisez le paramètre **-p** pour passer une chaîne au format JSON avec toutes les propriétés requises ou supplémentaires.
    
    
Pour supprimer une ressource existante comme une ressource d’ordinateur virtuel, utilisez une commande semblable à la suivante.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Pour déplacer des ressources existantes à un autre groupe de ressources ou d’abonnement, utilisez la commande **déplacer des Ressources azure** . L’exemple suivant montre comment déplacer un Cache Redis à un nouveau groupe de ressources. Dans le paramètre **-i** , fournir une liste séparée par des virgules, de l’id de ressource à déplacer.


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Contrôle l’accès aux ressources

Vous pouvez utiliser la CLI Azure pour créer et gérer des stratégies pour contrôler l’accès aux ressources Azure. Pour des informations sur les définitions de stratégie et affectation de stratégies aux ressources, consultez [utiliser la stratégie pour la gestion des ressources et de contrôler l’accès](resource-manager-policy.md).

Par exemple, définissez la stratégie suivante pour refuser toutes les demandes, où emplacement n’est pas ouest des États-Unis ou du Nord centrale nous et enregistrez-le à le policy.json de fichier de définition de stratégie :

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Ensuite, exécutez la commande de **Création d’une définition de la stratégie** :

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
Cette commande affiche la sortie semblable à la suivante.

    + Création de la définition de stratégie MyPolicy données : PolicyName : données de MyPolicy : PolicyDefinitionId : /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    données : élément PolicyType : données personnalisées : DisplayName : non défini de données : Description : non défini de données : PolicyRule : champ = emplacement, dans = [westus, northcentralus], effet = refuser

 Pour affecter une stratégie au niveau de l’étendue que vous souhaitez, utilisez le **PolicyDefinitionId** retourné à partir de la commande précédente. Dans l’exemple suivant, cette étendue est l’abonnement, mais vous pouvez limiter l’étendue des ressources individuelles ou de groupes de ressources :

    affectation de stratégie Azure créer MyPolicyAssignment -p /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy s - /subscriptions/###-###-###-###-### /

Vous pouvez obtenir, modifier ou supprimer des définitions de stratégie en utilisant les commandes de **définition de la stratégie Afficher**, **définition de la stratégie**et **suppression de la définition de la stratégie** .

De la même façon, vous pouvez obtenir, modifier ou supprimer les affectations de stratégie en utilisant les commandes **Afficher les affectations de stratégie**, **les affectations de stratégie**et **Supprimer de l’affectation de stratégie** .


## <a name="export-a-resource-group-as-a-template"></a>Exporter un groupe de ressources sous la forme d’un modèle

Pour un groupe de ressources existant, vous pouvez afficher le modèle du Gestionnaire de ressources du groupe de ressources. Exporter le modèle offre deux avantages :

1. Vous pouvez facilement automatiser les déploiements à venir de la solution, car toute l’infrastructure est défini dans le modèle.

2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant le JSON qui représente votre solution.

À l’aide de la CLI d’Azure, vous pouvez exporter un modèle qui représente l’état actuel de votre groupe de ressources ou de télécharger le modèle qui a été utilisé pour un déploiement particulier.

* **Exporter le modèle pour un groupe de ressources** - cela est utile lorsque vous apporté des modifications à un groupe de ressources et que vous devez récupérer la représentation JSON de son état actuel. Toutefois, le modèle généré contient uniquement un nombre minimal de paramètres et aucune variable. La plupart des valeurs dans le modèle est codées en dur. Avant de déployer le modèle généré, vous souhaiterez peut-être plusieurs valeurs transformer en paramètres afin que vous pouvez personnaliser le déploiement pour des environnements différents.

    Pour exporter le modèle pour un groupe de ressources dans un répertoire local, exécutez le `azure group export` de commande comme indiqué dans l’exemple suivant. (Remplacement d’un répertoire local approprié pour votre environnement de système d’exploitation).

        azure group export testRG ~/azure/templates/

* **Télécharger le modèle pour un déploiement particulier** --Ceci est utile lorsque vous souhaitez afficher le modèle réel qui a été utilisé pour déployer des ressources. Le modèle comprend tous les paramètres et les variables définies pour le déploiement d’origine. Toutefois, si une personne de votre organisation apporté des modifications au groupe de ressources à l’extérieur de la définition du modèle, ce modèle ne représente pas l’état actuel du groupe de ressources.

    Pour télécharger le modèle utilisé pour un déploiement particulier dans un répertoire local, exécutez le `azure group deployment template download` commande. Par exemple :

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] Exportation de modèle est en mode Aperçu, et pas tous les types de ressources actuellement prend en charge que l’exportation d’un modèle. Lorsque vous tentez d’exporter un modèle, vous pouvez observer une erreur indiquant que certaines ressources n’ont pas été exportés. Si nécessaire, définir manuellement ces ressources dans votre modèle après le téléchargement.



## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des détails sur les opérations de déploiement et résoudre les erreurs de déploiement avec la CLI d’Azure, reportez-vous à la section [Afficher les opérations de déploiement avec l’interface CLI d’Azure](resource-manager-troubleshoot-deployments-cli.md).
* Si vous souhaitez utiliser la CLI pour configurer une application ou un script pour accéder aux ressources, reportez-vous à la section [Utilisation de CLI Azure pour créer un service principal pour accéder aux ressources](resource-group-authenticate-service-principal-cli.md).


