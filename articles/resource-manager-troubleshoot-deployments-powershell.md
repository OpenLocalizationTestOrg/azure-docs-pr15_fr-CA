<properties
   pageTitle="Afficher les opérations de déploiement avec PowerShell | Microsoft Azure"
   description="Décrit comment utiliser PowerShell Azure pour détecter les problèmes de déploiement du Gestionnaire de ressources."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-powershell"></a>Afficher les opérations de déploiement avec Azure PowerShell

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Vous pouvez afficher les opérations pour un déploiement via PowerShell Azure. Vous serez peut-être plus intéressé par les opérations lorsque vous avez reçu une erreur pendant le déploiement afin que cet article se concentre sur l’affichage des opérations qui ont échoué. PowerShell fournit des applets de commande qui vous permettent de facilement trouver les erreurs et de déterminer les éventuels correctifs.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Vous pouvez éviter certaines erreurs en validant votre modèle et votre infrastructure avant le déploiement. Vous pouvez également vous connecter demande supplémentaire et des informations de réponse au cours du déploiement qui peut être utile ultérieurement pour la résolution des problèmes. Pour en savoir plus sur la validation et les informations de consignation des demandes et de réponses, voir [déploiement d’un groupe de ressources avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md).

## <a name="use-deployment-operations-to-troubleshoot"></a>Les opérations de déploiement permet de résoudre les problèmes

1. Pour obtenir l’état global d’un déploiement, utilisez la commande **Get-AzureRmResourceGroupDeployment** . Vous pouvez filtrer les résultats pour seulement ces déploiements qui ont échoué.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Qui retourne les déploiements défectueux dans le format suivant :
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Chaque déploiement est généralement constitué de plusieurs opérations, et chaque opération qui représente une étape dans le processus de déploiement. Pour découvrir ce qui s’est passé avec un déploiement, vous devez généralement afficher des détails sur les opérations de déploiement. Vous pouvez voir le statut des opérations avec **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Qui retourne plusieurs opérations avec chacun d'entre eux dans le format suivant :
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Pour obtenir plus de détails sur les opérations ayant échoué, récupérez les propriétés d’opérations avec un état **d’Échec** .

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Qui retourne toutes les opérations ayant échouées avec chacun d'entre eux dans le format suivant :
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Notez l’ID de suivi pour l’opération. Vous allez utiliser que dans l’étape suivante pour vous concentrer sur une opération spécifique.

4. Pour obtenir le message d’état d’une opération ayant échoué particulière, utilisez la commande suivante :

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Qui retourne :
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## <a name="use-audit-logs-to-troubleshoot"></a>Utilisation des journaux d’audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs pour le déploiement, procédez comme suit :

1. Pour récupérer les entrées du journal, exécutez la commande **Get-AzureRmLog** . Vous pouvez utiliser les paramètres **ResourceGroup** et **état** pour retourner uniquement les événements qui ont échoué pour un groupe de ressources unique. Si vous ne spécifiez pas d’heures de début et de fin, les écritures pendant la dernière heure sont renvoyées.
Par exemple, pour récupérer les opérations ayant échouées à la dernière heure à exécuter :

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Vous pouvez spécifier un timespan particulier. Dans l’exemple suivant, nous allons pour les actions qui ont échoué pour le dernier jour. 

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    Ou bien, vous pouvez définir une heure de fin pour les actions qui ont échoué et de début exacte :

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Si cette commande renvoie trop d’entrées et propriétés, vous pouvez concentrer vos efforts d’audit en récupérant la propriété **Properties** . Nous allons inclure le paramètre **DetailedOutput** pour afficher les messages d’erreur.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Qui retourne les propriétés des entrées du journal dans le format suivant :
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. En fonction de ces résultats, penchons-nous sur le deuxième élément. Vous pouvez affiner les résultats en consultant le message d’état pour cette entrée.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Qui retourne :
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## <a name="next-steps"></a>Étapes suivantes

- Pour l’aide sur la résolution des erreurs de déploiement particulier, consultez [résoudre les erreurs courantes lors du déploiement de ressources vers Azure avec le Gestionnaire de ressources Azure](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, voir [Auditer les opérations effectuées avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant de l’exécuter, voir [déploiement d’un groupe de ressources avec le modèle de gestionnaire de ressources Azure](resource-group-template-deploy.md).

