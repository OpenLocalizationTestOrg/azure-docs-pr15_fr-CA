<properties
   pageTitle="Créer un équilibreur de charge interne à l’aide de PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge interne à l’aide de PowerShell dans le modèle de déploiement classique"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Démarrer la création d’un équilibreur de charge interne (standard) à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](load-balancer-get-started-ilb-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Créer un équilibreur de charge interne pour les machines virtuelles

Pour créer un jeu d’équilibreur de charge interne et les serveurs qui enverra le trafic au il, vous devez effectuer les opérations suivantes :

1. Créez une instance d’interne l’équilibrage de charge qui sera le point de terminaison du trafic entrant à charge équilibrée entre les serveurs d’un ensemble équilibré en charge.

1. Ajouter des points de terminaison correspondant aux ordinateurs virtuels qui recevront le trafic entrant.

1. Configurez les serveurs qui enverra le trafic à charge équilibrée pour envoyer leur trafic vers l’adresse IP virtuelle (VIP) de l’instance de l’équilibrage de charge interne.


### <a name="step-1-create-an-internal-load-balancing-instance"></a>Étape 1 : Créer une instance de l’équilibrage de charge interne

Pour un service cloud existant ou d’un service en nuage déployé sous un réseau virtuel, vous pouvez créer une instance de l’équilibrage de charge interne avec les commandes Windows PowerShell suivantes :

    $svc="<Cloud Service Name>"
    $ilb="<Name of your ILB instance>"
    $subnet="<Name of the subnet within your virtual network>"
    $IP="<The IPv4 address to use on the subnet-optional>"

    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


Notez que cette utilisation de l’applet de commande [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell utilise le jeu de paramètres de DefaultProbe. Pour plus d’informations sur les jeux de paramètres supplémentaires, voir [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Étape 2 : Ajouter des points de terminaison à l’instance de l’équilibrage de charge interne

Voici un exemple :

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $ilb="ilbset"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Étape 3 : Configurer vos serveurs pour envoyer le trafic vers le nouveau point de terminaison l’équilibrage de charge interne

Vous devez configurer les serveurs dont le trafic doit être équilibré pour utiliser la nouvelle adresse IP (l’adresse IP virtuelle) de l’instance de l’équilibrage de charge interne. Il s’agit de l’adresse à laquelle l’instance de l’équilibrage de charge interne est à l’écoute. Dans la plupart des cas, vous devez simplement ajouter ou modifier un enregistrement DNS pour l’adresse VIP de l’instance de l’équilibrage de charge interne.

Si vous avez spécifié l’adresse IP lors de la création de l’instance de l’équilibrage de charge interne, vous avez déjà l’adresse VIP. Dans le cas contraire, vous pouvez voir l’adresse VIP dans les commandes suivantes :

    $svc="<Cloud Service Name>"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



Pour utiliser ces commandes, renseignez les valeurs et supprimer le < et >. Voici un exemple :

    $svc="mytestcloud"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


À partir de l’affichage de la commande Get-AzureInternalLoadBalancer, notez l’adresse IP et apportez les modifications nécessaires à vos serveurs ou vos enregistrements DNS pour vous assurer que le trafic soit envoyé à l’adresse VIP.

>[AZURE.NOTE]La plate-forme Microsoft Azure utilise une adresse IPv4 statique routable publiquement pour de nombreux scénarios d’administration. L’adresse IP est 168.63.129.16. Cette adresse IP ne doit pas être bloquée par un pare-feu, car il peut provoquer un comportement inattendu.
>En ce qui concerne l’Azure interne l’équilibrage de charge, cette adresse IP est utilisée par la surveillance des sondes de l’équilibreur de charge pour déterminer l’état de santé pour les machines virtuelles dans un ensemble de l’équilibrage de la charge. Si un groupe de sécurité réseau est utilisé pour restreindre le trafic vers des machines virtuelles Azure dans un jeu en interne avec équilibrage de charge ou est appliqué à un sous-réseau de réseau virtuel, assurez-vous qu’une règle de sécurité de réseau est ajoutée pour autoriser le trafic à partir de 168.63.129.16.


## <a name="example-of-internal-load-balancing"></a>Exemple d’équilibrage de charge interne

Pour vous guider dans la fin pour terminer le processus de création d’un ensemble équilibré en charge de deux exemples de configurations, consultez les sections suivantes.

### <a name="an-internet-facing-multi-tier-application"></a>Internet en vis-à-vis, application multicouche

Vous souhaitez fournir un service de base de données d’équilibrage de la charge pour un ensemble de serveurs web d’Internet. Les deux ensembles de serveurs sont hébergés dans un service cloud d’Azure unique. Trafic de serveur Web sur le port TCP 1433 doit être réparti entre les deux ordinateurs virtuels dans la couche de base de données. La figure 1 illustre la configuration.

![Ensemble d’équilibrage de charge interne pour la couche de base de données](./media/load-balancer-internal-getstarted/IC736321.png)


La configuration comprend les éléments suivants :

- Le service cloud existant, les machines virtuelles est nommé mytestcloud.

- Les deux serveurs de base de données existants sont nommés DB1, DB2.

- Les serveurs Web de la couche web se connectent aux serveurs de base de données dans la couche de base de données à l’aide de l’adresse IP privée. Une autre option consiste à utiliser votre propre serveur DNS pour le réseau virtuel et d’enregistrer manuellement un enregistrement A pour le jeu d’équilibreur de charge interne.

Les commandes suivantes configurer une nouvelle instance de l’équilibrage de charge interne nommée **ILBset** et ajouter des points de terminaison aux machines virtuelles correspondant aux serveurs de base de deux données :

    $svc="mytestcloud"
    $ilb="ilbset"
    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $vmname="DB1"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

    $epname="TCP-1433-1433-2"
    $vmname="DB2"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## <a name="remove-an-internal-load-balancing-configuration"></a>Supprimer une configuration de l’équilibrage de charge interne

Pour supprimer un ordinateur virtuel comme un point de terminaison à partir d’une instance d’équilibreur de charge interne, utilisez les commandes suivantes :

    $svc="<Cloud service name>"
    $vmname="<Name of the VM>"
    $epname="<Name of the endpoint>"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Pour utiliser ces commandes, renseignez les valeurs, en supprimant le < et >.

Voici un exemple :

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Pour supprimer une instance d’équilibreur de charge interne à partir d’un service en nuage, utilisez les commandes suivantes :

    $svc="<Cloud service name>"
    Remove-AzureInternalLoadBalancer -ServiceName $svc

Pour utiliser ces commandes, renseignez la valeur et de supprimer le < et >.

Voici un exemple :

    $svc="mytestcloud"
    Remove-AzureInternalLoadBalancer -ServiceName $svc



## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Plus d’informations sur les applets de commande équilibreur de charge interne


Pour obtenir plus d’informations sur les applets de commande de l’équilibrage de charge interne, exécutez les commandes suivantes à l’invite de Windows PowerShell :

- Nouvelle-AzureInternalLoadBalancerConfig-help Get-complet

- Ajouter-AzureInternalLoadBalancer-help Get-complet

- Get-help Get-AzureInternalLoadbalancer-complet

- Get-help Remove-AzureInternalLoadBalancer-complet

## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de la charge l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)