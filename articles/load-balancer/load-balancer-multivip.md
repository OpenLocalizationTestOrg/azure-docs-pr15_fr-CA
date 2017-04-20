<properties
   pageTitle="Plusieurs des VIP pour un service en nuage"
   description="Vue d’ensemble de la définition de plusieurs VIP sur un service en nuage et de multiVIP"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurer plusieurs VIP pour un service en nuage

Vous pouvez accéder de services cloud Azure via Internet à l’aide d’une adresse IP fournie par Azure. Cette adresse IP publique est appelée une adresse VIP (virtual IP) dans la mesure où elle est liée à l’équilibreur de charge Azure et les instances pas la Machine virtuelle (VM) dans le service cloud. Vous pouvez accéder à n’importe quelle instance de machines virtuelles au sein d’un service en nuage à l’aide d’une adresse IP virtuelle unique.

Toutefois, il existe des scénarios dans lesquels vous devrez peut-être plusieurs VIP sous la forme d’une entrée de pointer vers le même service de cloud. Par exemple, votre service cloud peut héberger plusieurs sites Web qui nécessitent une connectivité SSL utilise le port par défaut 443, que chaque site est hébergé pour un autre client ou client. Dans ce scénario, vous devez avoir une adresse IP en vis-à-vis public différente pour chaque site Web. Le schéma ci-dessous illustre un web mutualisée typique avec un besoin de plusieurs certificats SSL sur le même port public.

![Scénario multi VIP SSL](./media/load-balancer-multivip/Figure1.png)

Dans l’exemple ci-dessus, VIP tous utilisent le même port public (443) et le trafic est redirigé vers une charge plus équilibrée des machines virtuelles sur un port unique et privé de l’adresse IP interne du service nuage tous les sites Web d’hébergement.

>[AZURE.NOTE] Une autre situation exigeant le recours à la plusieurs VIP héberge plusieurs écouteurs groupe de disponibilité AlwaysOn de SQL sur le même ensemble de Machines virtuelles.

VIP est dynamiques par défaut, ce qui signifie que l’adresse IP réelle affecté au service de cloud peut-être changer au fil du temps. Pour éviter cela, vous pouvez réserver une adresse IP virtuelle de votre service. Pour en savoir plus sur VIP réservés, consultez [IP publiques réservées](../virtual-network/virtual-networks-reserved-public-ip.md).

>[AZURE.NOTE] Voir [adresse IP de tarification](https://azure.microsoft.com/pricing/details/ip-addresses/) pour plus d’informations sur la tarification de VIP et IPs réservé.

Vous pouvez utiliser PowerShell pour vérifier la VIP utilisés par vos services en nuage, ainsi qu’Ajouter supprimer VIP, associer une adresse IP virtuelle à un point de terminaison et configurer l’équilibrage de charge sur une VIP spécifique.

## <a name="limitations"></a>Limitations

À ce stade, la fonctionnalité de plusieurs VIP est limitée aux scénarios suivants :

- **IaaS uniquement**. Vous ne pouvez activer que les VIP de multiples pour les services en nuage qui contiennent des ordinateurs virtuels. Impossible d’utiliser plusieurs VIP dans les scénarios de PaaS avec les instances de rôles.
- **PowerShell uniquement**. Vous ne pouvez gérer que les VIP de multiples à l’aide de PowerShell.

Ces limitations sont temporaires et peuvent changer à tout moment. Assurez-vous de revisiter cette page pour vérifier les modifications futures.


## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Comment faire pour ajouter une adresse IP virtuelle pour un service en nuage

Pour ajouter une adresse IP virtuelle à votre service, exécutez la commande PowerShell suivante :

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

Cette commande affiche un résultat semblable à l’exemple suivant :

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Comment faire pour supprimer une adresse IP virtuelle à partir d’un service en nuage

Pour supprimer l’adresse IP virtuelle ajouté à votre service dans l’exemple ci-dessus, exécutez la commande PowerShell suivante :

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT] Vous ne pouvez supprimer une adresse IP virtuelle si elle n’a aucun point de terminaison associé à.

## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Comment récupérer des informations de l’adresse IP virtuelle à partir d’un Service en nuage

Pour récupérer la VIP associé à un service en nuage, exécutez le script PowerShell suivant :

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

Le script affiche un résultat semblable à l’exemple suivant :

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

Dans cet exemple, le service en nuage a 3 VIP :

- **Vip1** est l’adresse IP virtuelle par défaut, vous savez que dans la mesure où la valeur de IsDnsProgrammedName est définie sur true.
- **Vip2** et **Vip3** ne sont pas utilisés car ils n’ont pas toutes les adresses IP. Ils seront utilisés uniquement si vous associez un point de terminaison à l’adresse VIP.

>[AZURE.NOTE] Votre abonnement ne seront débité pour VIP supplémentaire une fois qu’ils sont associés à un point de terminaison. Pour plus d’informations sur la tarification, consultez [tarification d’adresse IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Comment faire pour associer une adresse IP virtuelle à un point de terminaison

Pour associer une adresse IP virtuelle sur un service en nuage à un point de terminaison, exécutez la commande PowerShell suivante :

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
  	| Update-AzureVM

La commande crée un point de terminaison lié à l’adresse IP virtuelle appelée *Vip2* sur le port *80*et des liens à la machine virtuelle nommée *myVM1* dans un service cloud nommé *myService* utilisant *TCP* sur le port *8080*.

Pour vérifier la configuration, exécutez la commande PowerShell suivante :

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

La sortie ressemble à l’exemple suivant :

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Comment faire pour activer l’équilibrage de charge sur une VIP spécifique

Vous pouvez associer une adresse VIP unique avec plusieurs machines virtuelles pour des fins d’équilibrage de charge. Par exemple, vous avez un service cloud nommée *myService*et deux ordinateurs virtuels nommés *myVM1* et *myVM2*. Et votre service cloud a plusieurs VIP, un d’eux nommé *Vip2*. Si vous souhaitez vous assurer que tout le trafic sur le port *81* sur *Vip2* est équilibré entre les *myVM1* et *myVM2* sur le port *8181*, exécutez le script PowerShell suivant :

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

Vous pouvez également mettre à jour de votre équilibreur de charge pour utiliser une adresse IP virtuelle différente. Par exemple, si vous exécutez la commande PowerShell ci-dessous, vous allez modifier la jeu pour utiliser une adresse IP virtuelle nommée Vip1 d’équilibrage de la charge :

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## <a name="next-steps"></a>Étapes suivantes

[Analytique de journal pour équilibrer la charge Azure](load-balancer-monitor-log.md)

[Vue d’ensemble du équilibreur de charge qui fait face à Internet](load-balancer-internet-overview.md)

[Mise en route sur Internet en vis-à-vis d’équilibrage de la charge](load-balancer-get-started-internet-arm-ps.md)

[Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)

[API de reste réservé IP](https://msdn.microsoft.com/library/azure/dn722420.aspx)