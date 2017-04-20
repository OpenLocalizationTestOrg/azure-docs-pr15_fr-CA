<properties 
    pageTitle="Déplacer les ressources vers le nouveau groupe de ressources | Microsoft Azure" 
    description="Le Gestionnaire de ressources Azure permet de déplacer les ressources vers un nouveau groupe de ressources ou un abonnement." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/21/2016" 
    ms.author="tomfitz"/>

# <a name="move-resources-to-new-resource-group-or-subscription"></a>Déplacer les ressources vers le nouveau groupe de ressources ou d’abonnement

Cette rubrique vous indique comment faire pour déplacer des ressources à un nouvel abonnement ou à un groupe de ressources dans le même abonnement. Vous pouvez utiliser le portail, PowerShell, Azure CLI ou l’API REST pour déplacer les ressources. Les opérations de déplacement de cette rubrique sont disponibles sans l’assistance du support technique Azure.

En règle générale, vous déplacez des ressources lorsque vous décidez de qui :

- À des fins de facturation, une ressource doit résider dans un autre abonnement.
- Une ressource de partage n’est plus le cycle de vie de même que les ressources qu’il a été précédemment groupé avec. Vous voulez déplacer vers un nouveau groupe de ressources afin de gérer ces ressources séparément des autres ressources.

Lorsque vous déplacez des ressources, le groupe de la source et le groupe cible sont verrouillés lors de l’opération. Écrire et les opérations de suppression sont bloqués sur les groupes jusqu'à ce que le déplacement se termine.

Vous ne pouvez pas modifier l’emplacement de la ressource. Déplacement d’une ressource déplace uniquement à un groupe de ressources. Le nouveau groupe de ressources peut avoir un emplacement différent, mais qui ne modifie pas l’emplacement de la ressource.

> [AZURE.NOTE] Cet article décrit comment déplacer des ressources au sein d’un Azure existant compte offres. Si vous souhaitez réellement modifier votre compte Azure offre (par exemple, la mise à niveau à partir de paiement à payer avant) tout en continuant à travailler avec vos ressources existantes, consultez [votre abonnement Azure pour une autre offre de commutateur](billing-how-to-switch-azure-offer.md). 

## <a name="checklist-before-moving-resources"></a>Liste de vérification avant de déplacer les ressources

Il existe certaines étapes importantes à effectuer avant de déplacer une ressource. En vérifiant ces conditions, vous pouvez éviter les erreurs.

1. Le service doit activer la possibilité de déplacer les ressources. Consultez la liste ci-dessous pour plus d’informations sur les [services permettent le déplacement des ressources](#services-that-enable-move).
1. Les abonnements de source et de destination doivent exister dans le même [client d’Active Directory](./active-directory/active-directory-howto-tenant.md). Pour passer à un nouveau client, appelez le support technique.
2. L’abonnement de destination doit être inscrit pour le fournisseur de ressources de la ressource en cours de déplacement. Si ce n’est pas le cas, vous recevez un message d’erreur indiquant que l' **abonnement n’est pas enregistré pour un type de ressource**. Vous pouvez rencontrer ce problème lorsque vous déplacez une ressource vers un nouvel abonnement, mais que l’abonnement n’a jamais été utilisé avec ce type de ressource. Pour savoir comment vérifier l’état d’enregistrement et d’inscrire des fournisseurs de ressources, consultez [les fournisseurs de ressources et de types](../resource-manager-supported-services.md#resource-providers-and-types).
4. Si vous déplacez une application du Service de l’application, vous avez examiné les [limitations du Service d’application](#app-service-limitations).
4. Si vous déplacez des ressources associées aux Services de récupération, vous avez examiné les [limitations des Services de récupération](#recovery-services-limitations)
5. Si vous déplacez les ressources déployées via le modèle classique, vous avez examiné les [limitations de déploiement standard](#classic-deployment-limitations).

## <a name="when-to-call-support"></a>Quand contacter le support

Vous pouvez déplacer la plupart des ressources par les opérations libre-service présentées dans cette rubrique. Utilisez les opérations de libre-service pour :

- Déplacer des ressources du Gestionnaire de ressources.
- Déplacer des ressources classiques selon les [limitations de déploiement classique](#classic-deployment-limitations). 

Appelez le support lorsque vous avez besoin de :

- Déplacer vos ressources vers un nouveau compte Azure (et les clients Active Directory).
- Déplacer des ressources classiques mais rencontrez des problèmes avec les limitations.

## <a name="services-that-enable-move"></a>Services qui permettent les déplacements

Pour l’instant, les services qui permettent le déplacement vers un nouveau groupe de ressources et l’abonnement sont les suivantes :

- Gestion de l’API
- Les applications de Service d’application (applications web) - voir [les restrictions de Service d’application](#app-service-limitations)
- Automation
- Traitement par lots
- PAIE CANADA-FICHIER
- Services en nuage, consultez [limitations de déploiement standard](#classic-deployment-limitations)
- Fabrique de données
- DNS
- DocumentDB
- Clusters HDInsight
- Concentrateurs IoT
- Chambre forte de clé 
- Services de support
- Engagement mobile
- Concentrateurs de notification
- Conseils opérationnels
- Redis de Cache
- Planificateur
- Recherche
- Bus des services
- Stockage
- Stockage (standard) - voir [limitations de déploiement standard](#classic-deployment-limitations)
- Serveur de base de données de SQL - la base de données et le serveur doit résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacés.
- Machines virtuelles - Toutefois, est pas prise en charge de déplacer vers un nouvel abonnement lorsque ses certificats sont stockés dans un coffre-fort de clé
- Virtual Machines (standard) - voir [limitations de déploiement standard](#classic-deployment-limitations)
- Réseaux virtuels

## <a name="services-that-do-not-enable-move"></a>Services qui ne permettent pas de déplacement

Les services qui actuellement ne permettent pas déplacer une ressource sont les suivantes :

- Passerelle d’application
- Aperçu de l’application
- Voie Express
- Services de récupération de chambre forte - également ne pas déplacer les ressources informatiques, de réseau et de stockage associés à la chambre forte de Services de récupération, consultez les [Services de récupération des limitations](#recovery-services-limitations).
- Machines virtuelles avec le certificat stocké dans le coffre-fort de la clé
- Jeux d’échelle de Machines virtuelles
- Les réseaux virtuels (standard) - voir [limitations de déploiement standard](#classic-deployment-limitations)
- Passerelle VPN

## <a name="app-service-limitations"></a>Limitations du Service d’application

Lorsque vous travaillez avec les applications de Service d’application, vous ne pouvez déplacer seulement un plan de Service de l’application. Pour déplacer les applications de Service de l’application, vos options sont les suivantes :

- Déplacer le plan de Service de l’application et toutes les autres ressources du Service de l’application dans ce groupe de ressources à un groupe de ressources qui n’a pas déjà de ressources de Service de l’application. Cette exigence signifie que vous devez déplacer même les ressources de Service de l’application qui ne sont pas associés au plan de Service de l’application. 
- Déplacer les applications pour un groupe de ressources différent, mais conserver tous les plans de Service de l’application dans le groupe de ressource d’origine.

Si votre groupe de ressources d’origine inclut également une ressource d’Application Insights, Impossible de déplacer cette ressource parce que les perspectives de l’Application ne permet pas actuellement l’opération de déplacement. Si vous incluez la ressource d’idées d’Application lorsque vous déplacez des applications de Service d’application, l’opération de déplacement entière échoue. Toutefois, les perspectives d’Application et Service d’application n’avez pas besoin de résider dans le même groupe de ressources que l’application pour l’application fonctionne correctement.

Par exemple, si votre groupe de ressources contient :

- **Web-a** qui est associé à **un plan** et **app-perspectives-a**
- **web-b** qui est associée avec **plan-b** et **app-perspectives-b**

Vos options sont les suivantes :

- Déplacer **un web**, **un plan**, **web-b**et **b-plan**
- Déplacer **un web** et **web-b**
- Déplacer **un web**
- Déplacer le **web-b**

Toutes les autres combinaisons impliquent laisse derrière lui un type de ressource qui ne peuvent pas être conservé lorsque vous déplacez un plan de Service de l’application (n’importe quel type de ressource de Service de l’application) ou déplacement d’un type de ressource qui ne peut pas déplacer (informations de l’Application).

Si votre application web réside dans un groupe de ressources différent que son plan de Service d’application mais que vous souhaitez déplacer vers un nouveau groupe de ressources à la fois, vous devez effectuer le déplacement en deux étapes. Par exemple :

- **Web-a** réside dans un **groupe de web**
- **un plan** se trouve dans le **groupe de plan**
- Vous souhaitez que **web-a** et **un plan** se trouve dans le **groupe combiné**

Pour effectuer ce déplacement, effectuez deux opérations distinctes de déplacement dans l’ordre suivant :

1. Déplacer le **web-a** **-** groupe de plan
2. Déplacer **un web** et **un plan** au **groupe combiné**.

Vous pouvez déplacer un certificat de Service d’application d’un nouveau groupe de ressources ou un abonnement sans aucun problème. Toutefois, si votre application web inclut un certificat SSL que vous avez acheté à l’extérieur et téléchargé vers l’application, vous devez supprimer le certificat avant de déplacer l’application web. Par exemple, vous pouvez effectuer les opérations suivantes :

1. Supprimer le certificat chargé de l’application web
2. Déplacer l’application web
3. Télécharger le certificat à l’application web

## <a name="recovery-services-limitations"></a>Limitations des Services de récupération

Déplacer n’est pas activé pour le stockage, le réseau, ou le calcul des ressources servant à définir une reprise après sinistre avec récupération de Site Azure. 

Par exemple, supposons que vous avez défini une réplication de vos ordinateurs locaux à un compte de stockage (stockage1) et que vous souhaitez que l’ordinateur protégé viennent après basculement Azure comme une machine virtuelle (VM1) connectée à un réseau virtuel (réseau domestique.1). Une de ces ressources Azure - stockage1 VM1 et réseau domestique.1 - Impossible de déplacer entre les groupes de ressources de l’abonnement même ou sur les abonnements.

## <a name="classic-deployment-limitations"></a>Limitations du déploiement classique

Les options de déplacement des ressources déployées via le modèle classique varient en fonction de si vous déplacez les ressources dans un abonnement ou vers un nouvel abonnement. 

### <a name="same-subscription"></a>Même abonnement

Lorsque vous déplacez des ressources à partir d’un groupe de ressources à un autre groupe de ressources dans le même abonnement, les restrictions suivantes s’appliquent :

- Réseaux virtuels (classiques) ne peut pas être déplacés.
- Virtual machines (classic) doit être déplacés avec le service en nuage. 
- Service cloud peut uniquement être déplacé lorsque le déplacement inclut tous ses ordinateurs virtuels.
- Service seul cloud peut être déplacé à la fois.
- Un seul compte de stockage (classique) peut être déplacé à la fois.
- Compte de stockage (classique) ne peut pas être déplacé dans la même opération avec un ordinateur virtuel ou d’un service en nuage.

Pour déplacer des ressources classiques à un nouveau groupe de ressources dans le même abonnement, utilisez les actions de déplacement standard via le [portail](#use-portal), [Azure PowerShell](#use-powershell), [Azure CLI](#use-azure-cli)ou [API REST](#use-rest-api). Vous utilisez les mêmes opérations que vous utilisez pour le déplacement des ressources du Gestionnaire de ressources.

### <a name="new-subscription"></a>Nouvel abonnement

Lorsque vous déplacez les ressources vers un nouvel abonnement, les restrictions suivantes s’appliquent :

- Toutes les ressources classiques de l’abonnement doivent être déplacés dans la même opération.
- L’abonnement de la cible ne doit pas contenir d’autres ressources classiques.
- Le déplacement ne peut être demandé via une API REST distinct pour les déplacements classiques. Les commandes de déplacement de gestionnaire de ressources standard ne fonctionnent pas lorsque vous déplacez des ressources classiques vers un nouvel abonnement.

Pour déplacer des ressources classiques vers un nouvel abonnement, vous devez utiliser les opérations de reste qui sont spécifiques aux ressources classiques. Procédez comme suit pour déplacer les ressources classiques vers un nouvel abonnement.

1. Vérifiez si l’abonnement source peut participer à un déplacement de cross-abonnement. Utilisez l’opération suivante :

         POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
     Dans le corps de la demande, sont les suivantes :

         {
           "role": "source"
         }

     La réponse pour l’opération de validation est au format suivant :

         {
           "status": "{status}",
           "reasons": [
             "reason1",
             "reason2"
           ]
         }

2. Vérifiez si l’abonnement de destination peut participer à un déplacement de cross-abonnement. Utilisez l’opération suivante :

         POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01

     Dans le corps de la demande, sont les suivantes :

         {
           "role": "target"
         }

     La réponse est dans le même format que la validation d’abonnement source.

3. Si les deux abonnements passent le contrôle, vous pouvez déplacer toutes les ressources classiques d’un abonnement d’abonnement avec l’opération suivante :

         POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

    Dans le corps de la demande, sont les suivantes :

         {
           "target": "/subscriptions/{target-subscription-id}"
         }

L’opération peut prendre plusieurs minutes. 

## <a name="use-portal"></a>Utilisez le portail

Pour déplacer les ressources vers un nouveau groupe de ressources dans **même abonnement**, sélectionnez le groupe de ressources contenant ces ressources et cliquez ensuite sur le bouton **déplacer** .

![déplacer des ressources](./media/resource-group-move-resources/edit-rg-icon.png)

Ou, pour déplacer les ressources vers un **nouvel abonnement**, sélectionnez le groupe de ressources contenant ces ressources, puis sélectionnez l’icône d’abonnement de modifier.

![déplacer des ressources](./media/resource-group-move-resources/change-subscription.png)

Sélectionnez les ressources à déplacer et le groupe de ressources de destination. Reconnaissez que vous devez mettre à jour les scripts de ces ressources, puis sélectionnez **OK**. Si vous avez sélectionné l’icône d’abonnement de modification à l’étape précédente, vous devez également sélectionner l’abonnement de destination.

![Sélectionnez la destination](./media/resource-group-move-resources/select-destination.png)

Dans les **Notifications**, vous voyez que l’opération de déplacement est en cours d’exécution.

![afficher l’état du déplacement](./media/resource-group-move-resources/show-status.png)

Lorsqu’il a terminé, vous êtes averti du résultat.

![afficher les résultats de déplacement](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Utilisation de PowerShell

Pour déplacer des ressources existantes à un autre groupe de ressources ou d’abonnement, utilisez la commande **Move-AzureRmResource** .

Le premier exemple montre comment déplacer une ressource à un nouveau groupe de ressources.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

Le deuxième exemple montre comment déplacer plusieurs ressources d’un groupe de ressources.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Pour déplacer vers un nouvel abonnement, inclure une valeur pour le paramètre **DestinationSubscriptionId** .

Vous êtes invité à confirmer que vous souhaitez déplacer les ressources spécifiées.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## <a name="use-azure-cli"></a>Utilisez la CLI Azure

Pour déplacer des ressources existantes à un autre groupe de ressources ou d’abonnement, utilisez la commande **déplacer des Ressources azure** . Vous devez fournir l’ID de ressource des ressources à déplacer. Vous pouvez obtenir l’ID de ressource avec la commande suivante :

    azure resource list -g sourceGroup --json

Qui retourne le format suivant :

    [
      {
        "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
        "name": "storagedemo",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "southcentralus",
        "tags": {},
        "kind": "Storage",
        "sku": {
          "name": "Standard_RAGRS",
          "tier": "Standard"
        }
      }
    ]

L’exemple suivant montre comment déplacer un compte de stockage pour un groupe de ressources. Dans le paramètre **-i** , fournir une liste séparée par des virgules, de l’id de ressource à déplacer.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
    
Vous êtes invité à confirmer que vous souhaitez déplacer la ressource spécifiée.

## <a name="use-rest-api"></a>Utiliser l’API REST

Pour déplacer des ressources existantes à un autre groupe de ressources ou d’abonnement, exécutez :

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Dans le corps de la demande, vous spécifiez le groupe de ressources cible et les ressources à déplacer. Pour plus d’informations sur l’opération de déplacement de reste, voir [déplacer les ressources](https://msdn.microsoft.com/library/azure/mt218710.aspx).


## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir des informations sur les applets de commande PowerShell pour la gestion de votre abonnement, consultez [L’aide du PowerShell Azure avec le Gestionnaire de ressources](powershell-azure-resource-manager.md).
- Pour en savoir plus sur les commandes CLI d’Azure pour la gestion de votre abonnement, reportez-vous [à l’aide de la CLI d’Azure avec le Gestionnaire de ressources](xplat-cli-azure-resource-manager.md).
- Pour obtenir des informations sur les fonctionnalités du portail de gestion de votre abonnement, consultez [l’aide du portail Azure pour gérer les ressources](./azure-portal/resource-group-portal.md).
- Pour en savoir plus sur l’application d’une organisation logique à vos ressources, reportez-vous à la section [pour organiser vos ressources en utilisant des balises](resource-group-using-tags.md).
