<properties
    pageTitle="Évolution verticale de jeux d’échelle Azure virtual machine | Microsoft Azure"
    description="Comment faire pour l’évolution verticale d’une Machine virtuelle en réponse aux alertes avec Azure Automation de surveillance"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="madhana"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="guybo"/>

# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Échelle verticale avec des jeux de l’échelle de l’ordinateur virtuel

Cet article décrit comment faire pour l’évolution verticale Azure [Jeux d’échelle de Machine virtuelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/) avec ou sans le réapprovisionnement. Pour vertical mise à l’échelle des ordinateurs virtuels qui ne sont pas dans les jeux de l’échelle, voir [évolution verticale Azure machine virtuelle avec l’Automation d’Azure](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md).

Échelle verticale, également connu sous le nom _d’évoluer_ et _mettre à l’échelle vers le bas_, signifie l’augmentation ou la diminution des tailles de machine virtuelle (VM) en réponse à une charge de travail. Faites la comparaison avec [la mise à l’échelle horizontale](./virtual-machine-scale-sets-autoscale-overview.md), également appelée _mise à l’échelle_ et l' _échelle dans_, où le nombre d’ordinateurs virtuels est modifié en fonction de la charge de travail.

Le réapprovisionnement signifie la suppression d’un ordinateur virtuel existant et le remplacer par un nouveau. Lorsque vous augmentez ou réduisez la taille des ordinateurs virtuels dans un jeu d’échelle de machine virtuelle, dans certains cas vous voulez redimensionner des machines virtuelles existantes et conserver vos données, tandis que dans d’autres cas, vous avez besoin déployer de nouvelles machines virtuelles de la nouvelle taille. Ce document couvre les deux cas.

Mise à l’échelle verticale peut être utile lorsque :

- Un service basé sur des machines virtuelles est sous-utilisée (par exemple pendant le week-end). Réduction de la taille de la mémoire virtuelle permet de réduire les coûts mensuels.
- Augmentation de la taille de mémoire virtuelle pour faire face à la demande plue sans créer des ordinateurs virtuels supplémentaires.

Vous pouvez configurer la mise à l’échelle verticale pour être déclenchées en fonction des alertes de base métriques de votre jeu d’échelle de machine virtuelle. Lorsque l’alerte est activée, elle déclenche un webhook déclenchant une procédure opérationnelle qui peut s’ajuster votre échelle la valeur vers le haut ou vers le bas. Mise à l’échelle verticale peut être configuré en suivant ces étapes :

1. Créer un compte Azure Automation avec exécuter en tant que fonctionnalité.
2. Importer les procédures opérationnelles de l’échelle verticale de Azure Automation pour les jeux d’échelle de machine virtuelle dans votre abonnement.
3. Ajouter un webhook à votre procédure opérationnelle.
4. Ajouter une alerte à votre échelle de machine virtuelle définie à l’aide d’une notification de webhook.

> [AZURE.NOTE] Autoscaling verticale ne peut avoir lieu au sein de certaines plages de tailles de machine virtuelle. Comparer les spécifications de chaque taille avant de décider de passer d’une à l’autre (nombre supérieur n’indique pas toujours plus grande taille de mémoire virtuelle). Vous pouvez choisir de mettre à l’échelle entre les paires de tailles suivantes :

>| Paire de mise à l’échelle des tailles de machine virtuelle |   |
|---|---|
|  Standard_A0 | Standard_A11 |
|  Standard_D1 |  Standard_D14 |
|  Standard_DS1 |  Standard_DS14 |
|  Standard_D1v2 |  Standard_D15v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Créer un compte d’Automation Azure avec exécuter en tant que fonctionnalité

La première chose à faire est de créer un compte Azure Automation qui hébergera les procédures opérationnelles utilisées à l’échelle les instances VM échelle définie. Récemment [Azure Automation](https://azure.microsoft.com/services/automation/) a introduit la fonctionnalité « Exécuter en tant que compte » qui rend le paramètre de l’entité de Service permettant d’exécuter les procédures opérationnelles de la part d’un utilisateur très facile. Vous trouverez d’autres informations dans l’article ci-dessous :

* [Authentifier les procédures opérationnelles avec Azure exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importer des procédures opérationnelles de l’échelle verticale de Azure Automation dans votre abonnement

Les procédures opérationnelles nécessaires à l’évolution verticale de vos jeux d’échelle de VM sont déjà publiés dans la galerie de procédure opérationnelle Azure Automation. Pour les importer dans votre abonnement de suivre les étapes décrites dans cet article :

* [Galeries de module et de procédure opérationnelle pour Azure Automation](../automation/automation-runbook-gallery.md)

Choisissez l’option Parcourir la galerie dans le menu de procédures opérationnelles :

![Procédures opérationnelles à importer][runbooks]

Les procédures opérationnelles qui doivent être importées sont affichées. Sélectionnez la procédure opérationnelle selon que vertical à l’échelle avec ou sans le réapprovisionnement :

![Galerie de procédures opérationnelles][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Ajouter un webhook à votre procédure opérationnelle

Une fois que vous avez importé les procédures opérationnelles que vous devrez ajouter un webhook pour la procédure opérationnelle afin qu’il peut être déclenché par une alerte à partir d’un ensemble d’échelle de machine virtuelle. Les détails de la création d’un webhook pour votre procédure opérationnelle sont décrits dans cet article :

* [Azure Automation webhooks](../automation/automation-webhooks.md)

> [AZURE.NOTE] Assurez-vous que vous copiez l’URI de webhook avant de fermer la boîte de dialogue webhook comme vous en avez besoin dans la section suivante.

## <a name="add-an-alert-to-your-vm-scale-set"></a>Ajouter une alerte à votre ensemble d’échelle de machine virtuelle

Voici un script PowerShell qui montre comment ajouter une alerte à un ensemble d’échelle de machine virtuelle. Reportez-vous à l’article suivant pour obtenir le nom de la mesure de déclencher l’alerte sur : [métriques de moniteur d’Azure autoscaling commun](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] Il est recommandé de configurer une période raisonnable pour l’alerte afin d’éviter le déclenchement de la mise à l’échelle verticale et toute interruption de service associé, trop souvent. Pensez à une fenêtre de moins de 20 à 30 minutes ou plus. Pensez à l’horizontal mise à l’échelle de si vous avez besoin afin d’éviter toute interruption.

Pour plus d’informations sur la création d’alertes, reportez-vous aux articles suivants :

* [Exemples de démarrage rapide moniteur PowerShell Azure](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Exemples de démarrage rapide Azure moniteur multiplates-CLI](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Résumé

Cet article a montré des exemples simples de mise à l’échelle verticales. Ces blocs de construction - compte d’automatisation, procédures opérationnelles, webhooks, alertes, vous pouvez connecter un large éventail d’événements avec un jeu personnalisé d’actions.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
