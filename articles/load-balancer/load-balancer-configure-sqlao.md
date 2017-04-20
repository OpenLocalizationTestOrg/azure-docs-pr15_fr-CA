<properties
   pageTitle="Configurer l’équilibrage de la charge pour SQL toujours sur | Microsoft Azure"
   description="Configurer l’équilibreur de charge fonctionne avec SQL toujours et comment tirer parti de powershell pour créer l’équilibreur de charge pour la mise en oeuvre de SQL"
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

# <a name="configure-load-balancer-for-sql-always-on"></a>Configurer l’équilibrage de la charge pour SQL toujours sur

Groupes de disponibilité AlwaysOn de SQL Server peuvent désormais être exécutées avec ILB. Groupe de disponibilité est le produit phare de SQL Server pour une haute disponibilité et reprise après sinistre. L’écouteur du groupe de disponibilité permet aux applications clientes de se connecter en toute transparence pour le réplica principal, quel que soit le nombre de répliques dans la configuration.

Le nom de l’écouteur (DNS) est mappé à une adresse IP à charge équilibrée et équilibrage de la charge d’Azure dirige le trafic entrant pour seulement le serveur principal dans le jeu de réplicas.

Vous pouvez utiliser la prise en charge ILB pour les points de terminaison SQL Server AlwaysOn (récepteur). Vous avez le contrôle sur l’accessibilité de l’écouteur et la possibilité de l’adresse IP à charge équilibrée à partir d’un sous-réseau spécifique dans votre réseau virtuel (VNet).

À l’aide de ILB sur le port d’écoute, le point de terminaison de serveur SQL (par exemple, Server = tcp:ListenerName, 1433 ; base de données = DatabaseName) est accessible uniquement par :

- Les services et les ordinateurs virtuels sur le même réseau virtuel
- Services et machines virtuelles à partir du réseau des locaux connectés
- Services et les ordinateurs virtuels à partir de VNets interconnectés

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figure 1 : AlwaysOn de SQL configuré avec l’équilibrage de la charge via Internet

## <a name="add-internal-load-balancer-to-the-service"></a>Ajouter un équilibreur de charge interne au service

1. Dans l’exemple suivant, nous allons configurer un réseau virtuel qui contient un sous-réseau appelé 'Sous-réseau-1' :

        Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

2. Ajouter des points de terminaison équilibrée de la charge pour ILB sur chaque machine virtuelle

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
        DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Dans l’exemple ci-dessus, vous disposez de 2 virtuelle appelée « sqlsvc1 » et « sqlsvc2 » en cours d’exécution dans le cloud service « Sqlsvc ». Après avoir créé la ILB avec le commutateur de « DirectServerReturn », vous ajoutez des points de terminaison d’équilibrage de la charge pour le ILB pour permettre à SQL configurer les écouteurs pour les groupes de disponibilité.

Pour plus d’informations sur AlwaysOn de SQL, consultez [configurer un équilibreur de charge interne pour un groupe de disponibilité AlwaysOn dans Azure](../virtual-machines/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Voir aussi

[Démarrer la configuration d’un équilibreur de charge d’ouvert sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)
