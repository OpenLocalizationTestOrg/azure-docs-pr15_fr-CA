<properties
   pageTitle="Afficher les opérations de déploiement avec l’interface CLI d’Azure | Microsoft Azure"
   description="Décrit l’utilisation de l’infrastructure du langage commun Azure pour détecter les problèmes de déploiement du Gestionnaire de ressources."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-cli"></a>Afficher les opérations de déploiement avec l’interface CLI d’Azure

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Si vous avez reçu une erreur lors du déploiement des ressources dans Azure, vous souhaiterez afficher plus de détails sur les opérations de déploiement qui ont été exécutées. CLI Azure fournit des commandes qui vous permettent de rechercher les erreurs et de déterminer les éventuels correctifs.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Vous pouvez éviter certaines erreurs en validant votre modèle et votre infrastructure avant le déploiement. Vous pouvez également vous connecter demande supplémentaire et des informations de réponse au cours du déploiement qui peut être utile ultérieurement pour la résolution des problèmes. Pour en savoir plus sur la validation et les informations de consignation des demandes et de réponses, voir [déploiement d’un groupe de ressources avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy-cli.md).

## <a name="use-audit-logs-to-troubleshoot"></a>Utilisation des journaux d’audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs pour le déploiement, procédez comme suit :

1. Pour voir les journaux d’audit, exécutez la commande **Afficher les journaux de groupe azure** . Vous pouvez inclure la **--dernier déploiement** option pour extraire uniquement le journal pour le déploiement de la plus récent.

        azure group log show ExampleGroup --last-deployment

2. La commande **Afficher les journaux de groupe azure** renvoie un grand nombre d’informations. Pour le dépannage, vous souhaitez généralement concentrer sur les opérations qui ont échoué. Le script suivant utilise l’option **--json** et l’utilitaire JSON [jq](https://stedolan.github.io/jq/) pour rechercher le journal des échecs de déploiement.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"Target\":null,\"Details\":[{\"Message\":\"Website with given name
            mysite already exists.\"},{\"Code\":\"Conflict\"},{\"ErrorEntity\":{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"ExtendedCode\":
            \"54001\",\"MessageTemplate\":\"Website with given name {0} already exists.\",\"Parameters\":[\"mysite\"],\"InnerErrors\":null}}],\"Innererror\":null}"
        },
        ...

    Vous pouvez voir **Propriétés** comprend des informations au format json sur l’opération qui a échoué.

    Vous pouvez utiliser la **--commentaires** et options **vv -** pour plus d’informations à partir des journaux.  Utiliser le **--commentaires** option pour afficher les étapes les opérations passent par sur `stdout`. Pour un historique d’une demande complète, utilisez l’option **- vv** . Les messages fournissent souvent précieux indices sur la cause des échecs.

3. Pour vous concentrer sur le message d’état pour les entrées en échec, utilisez la commande suivante :

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == \"Failed\") | .properties.statusMessage"


## <a name="use-deployment-operations-to-troubleshoot"></a>Les opérations de déploiement permet de résoudre les problèmes

1. Obtenir l’état global d’un déploiement avec le **déploiement d’un groupe azure show** . Dans l’exemple ci-dessous, le déploiement a échoué.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Pour afficher le message pour les opérations ayant échouées pour le déploiement, utilisez :

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == \"Failed\") | .properties.statusMessage.Message"


## <a name="next-steps"></a>Étapes suivantes

- Pour l’aide sur la résolution des erreurs de déploiement particulier, consultez [résoudre les erreurs courantes lors du déploiement de ressources vers Azure avec le Gestionnaire de ressources Azure](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, voir [Auditer les opérations effectuées avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant de l’exécuter, voir [déploiement d’un groupe de ressources avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md).
