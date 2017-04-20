<properties 
    pageTitle="Modèle de tarification des applications de logique | Microsoft Azure" 
    description="Pour plus d’informations sur le fonctionne de la tarification dans les applications de logique" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="logic-apps-pricing-model"></a>Applications de logique modèle de tarification

Les applications Azure logique vous permet de mettre à l’échelle et d’exécuter des workflows d’intégration dans le nuage.  Vous trouverez ci-dessous plus d’informations sur la facturation et les prix des plans pour les applications de la logique.

## <a name="consumption-pricing"></a>Prix de la consommation

Nouvellement créé Apps de logique d’utiliser un plan de la consommation. Avec le modèle de tarification de la consommation des applications de logique, vous ne payez que ce que vous utilisez.  Applications de logique ne sont pas limitées lors de l’utilisation d’un plan de la consommation.
Toutes les actions exécutées lors de l’exécution d’une instance d’application logique sont contrôlées.

### <a name="what-are-action-executions"></a>Que sont les exécutions de l’action ?

Chaque étape d’une définition d’application logique est une action.  Cela inclut les déclencheurs, les étapes du flux de contrôle comme conditions, étendues, pour chaque boucle, jusqu'à ce que les boucles, les appels à des connecteurs et des appels à actions natives.
Les déclencheurs sont des actions spéciales seulement sont conçues pour instancier une nouvelle instance d’une application de logique lorsqu’un événement particulier se produit.  Il existe un certain nombre de comportements différents pour les déclencheurs qui peuvent affecter la façon dont l’application logique est mesurée.

-   **L’interrogation de déclencheur** – ce déclencheur en permanence d’interroge un point de terminaison jusqu'à ce qu’il reçoive un message répondant aux critères de création d’une nouvelle instance d’une application de logique.  L’intervalle d’interrogation peut être configuré dans le déclencheur dans le Concepteur d’applications de logique.  Chaque demande d’interrogation, même si elle ne crée pas une nouvelle instance d’une application de logique, comptera comme l’exécution d’une action.

-   **Webhook déclencheur** – ce déclencheur attend un client pour l’envoi d’une demande sur un point de terminaison particulier.  Chaque demande envoyée au point de terminaison webhook est considérée comme l’exécution d’une action. Le déclencheur Webhook de HTTP et la demande sont les deux déclencheurs de webhook.

-   **Déclencheur de périodicité** – ce déclencheur va créer une nouvelle instance de l’application de la logique en fonction de l’intervalle de récurrence configuré dans le déclencheur.  Par exemple, un déclencheur de périodicité peut être configuré pour exécuter tous les 3 jours ou même toutes les minutes.

Exécutions du déclencheur peuvent être vus dans la lame de ressources des applications de logique dans la partie de l’historique du déclencheur.

Toutes les actions qui ont été exécutées, si elles ont réussi ou échoué sont contrôlés, comme l’exécution d’une action.  Les actions qui ont été ignorées en raison d’une condition ne pas respectée ou n’a pas s’exécuter car l’application logique arrêté avant terme ne sont pas considérées comme des exécutions de l’action.

Les actions exécutées dans des boucles sont comptées par itération de la boucle.  Par exemple, une action unique dans un pour chaque itération de boucle dans une liste de 10 articles est comptée comme le nombre d’éléments dans la liste (10), multiplié par le nombre d’actions dans la boucle (1) et l’autre pour l’ouverture de la boucle qui, dans cet exemple, serait (10 * 1) + 1 = 11 exécutions d’action.

Logique d’applications qui sont désactivés ne peut pas être instanciés de nouvelles instances et donc au moment où ils sont désactivés ne sera pas obtenir débitée.  Être conscients qu’après la désactivation d’une application de logique peut prendre un peu de temps pour les instances de mise en veille avant d’être complètement désactivée.

## <a name="app-service-plans"></a>Les programmes de Service d’application

Plans de Service d’application ne sont plus requis pour créer une application de logique.  Vous pouvez également référencer un Plan de Service application avec une application de logique existant.  Applications logique créées au préalable avec un Plan de Service d’application continuera de se comporter comme avant où, selon le plan choisi, va obtenir accéléré après qu’un nombre d’exécutions quotidiennes est dépassé et qu’il ne sera pas facturé à l’aide de l’indicateur d’exécution d’action.

Les programmes de Service App et leurs exécutions quotidiennes d’action autorisée :

| |Libre/Shared/Basic|Standard|Premium|
|---|---|---|---|
|Exécutions d’actions par jour| 200|10 000|50 000|

### <a name="convert-from-consumption-to-app-service-plan-pricing"></a>Convertir le Plan de Service d’application de tarification de la consommation

Pour faire référence à un Plan de Service application pour une application de logique de consommation, vous pouvez simplement [exécuter l’inférieure script PowerShell](https://github.com/logicappsio/ConsumptionToAppServicePlan).  Assurez-vous que vous tout d’abord installer les [Outils de PowerShell d’Azure](https://github.com/Azure/azure-powershell) .

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Convertir à partir du Plan de Service d’application les prix à la consommation

Pour modifier une application de logique qui a un Plan de Service application associé à un modèle de consommation supprimer la référence au Plan de Service App dans la définition de l’application de la logique.  Ceci est possible avec un appel à une applet de commande PowerShell :

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## <a name="pricing"></a>Tarification

Pour connaître les prix, consultez [Tarification des applications logique](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Étapes suivantes

- [Une vue d’ensemble de la logique d’applications][whatis]
- [Créer votre première application logique][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

