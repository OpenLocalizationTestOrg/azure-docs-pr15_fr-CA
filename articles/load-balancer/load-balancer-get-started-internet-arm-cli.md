<properties
   pageTitle="Créer un Internet face à l’équilibreur de charge dans le Gestionnaire de ressources à l’aide de la CLI Azure | Microsoft Azure"
   description="Apprenez à créer un Internet face à l’équilibreur de charge dans le Gestionnaire de ressources à l’aide de la CLI d’Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>Création d’un équilibreur de charge interne à l’aide de la CLI d’Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [apprendre à créer un équilibreur de charge à l’aide de déploiement classique d’ouvert sur Internet](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Déploiement de la solution à l’aide de la CLI d’Azure

Les étapes suivantes indiquent comment créer un équilibreur de charge à l’aide du Gestionnaire de ressources Azure avec l’interface CLI d’ouvert sur Internet. Avec le Gestionnaire de ressources Azure chaque ressource est créé et configuré individuellement, puis placer ensemble pour créer une ressource.

Vous devez créer et configurer des objets suivants pour déployer un équilibreur de charge :

- La configuration IP front-end - contient les adresses IP publiques pour le trafic réseau entrant.
- Pool d’adresses principal - contient des interfaces réseau (cartes réseau) pour les ordinateurs virtuels recevoir le trafic réseau à partir de l’équilibreur de charge.
- Règles d’équilibrage de la charge - contient les règles de mappage d’un port public de l’équilibreur de charge au port dans le pool d’adresses de back-end.
- NAT règles de trafic entrant - contient les règles de mappage d’un port public de l’équilibreur de charge à un port pour une machine virtuelle spécifique dans le pool d’adresses de back-end.
- Sondes - contient les sondes de santé permettant de vérifier la disponibilité des machines virtuelles des instances dans le pool d’adresses de back-end.

Pour plus d’informations, consultez [Le Gestionnaire de ressources Azure prise en charge de l’équilibrage de la charge](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Paramétrer CLI pour utiliser le Gestionnaire de ressources

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécutez la commande **mode config azure** pour passer en mode de gestionnaire de ressources, comme indiqué ci-dessous.

        azure config mode arm

    Sortie attendue :

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Créer un réseau virtuel et une adresse IP publique pour le pool IP front-end

1. Créer un réseau virtuel (VNet) nommé *NRPVnet* à l’emplacement américain d’Extrême-Orient à l’aide d’un groupe de ressources nommé *NRPRG*.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    Créer un sous-réseau nommé *NRPVnetSubnet* avec un bloc CIDR 10.0.0.0/24 dans *NRPVnet*.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. Créer une adresse IP publique nommée *NRPPublicIP* devant être utilisé par un pool d’IP front-end de nom DNS *loadbalancernrp.eastus.cloudapp.azure.com*. La commande ci-dessous utilise le type de ventilation statique et le délai d’inactivité de 4 minutes.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT]L’équilibreur de charge utilise le nom de domaine de l’adresse IP publique par son nom de domaine complet. Ce service une modification du déploiement classique, qui utilise le nuage en tant que nom de domaine complet (FQDN) de l’équilibreur de charge.
    >Dans cet exemple, le nom de domaine complet est *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Créer un équilibreur de charge

La commande suivante crée un équilibreur de charge nommé *NRPlb* dans le groupe de ressources *NRPRG* aux *États-Unis est* emplacement d’Azure.

    azure network lb create NRPRG NRPlb eastus

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Créer un pool d’IP front-end et back-end d’un pool d’adresses

Cet exemple montre comment créer le pool frontal IP qui reçoit le trafic réseau entrant sur l’équilibrage de charge et le pool d’IP serveur principal où le pool frontal envoie le trafic d’équilibrage de la charge.

1. Créer un pool frontal de IP associer l’adresse IP publique, créé dans l’étape précédente et l’équilibreur de charge.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. Configurer un pool d’adresses de back-end utilisé pour recevoir le trafic entrant à partir du pool IP front-end.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## <a name="create-lb-rules-nat-rules-and-probe"></a>Créer des règles de livres, des règles NAT et sonde

Cet exemple crée les éléments suivants.

- une règle TAR pour traduire tout le trafic entrant sur le port 21, port 22<sup>1</sup>
- une règle TAR pour traduire tout le trafic entrant sur le port 23 à la voie 22
- une règle d’équilibrage de la charge pour équilibrer le trafic entrant sur le port 80 au port 80 sur les adresses dans le pool principal.
- une règle de sonde pour vérifier l’état de santé sur une page nommée *HealthProbe.aspx*.

<sup>1</sup> les règles NAT sont associés à une instance de machine virtuelle spécifique situé derrière l’équilibreur de charge. Le trafic réseau entrant sur le port 21 est envoyé à une machine virtuelle spécifique sur le port 22 associée à cette règle NAT. Vous devez spécifier un protocole (TCP ou UDP) pour une règle NAT. Les deux protocoles ne peut pas être assignés au même port.

1. Créer des règles NAT.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22

2. Créer une règle d’équilibrage de la charge.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. Créer une sonde de santé.

        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4

4. Vérifiez vos paramètres.

        azure network lb show nrprg nrplb

    Sortie attendue :

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Créer des cartes d’interface réseau

Vous devez créer des cartes d’interface réseau (ou modifier les existantes) et les associez à des règles NAT, les règles d’équilibrage de la charge et les sondes.

1. Créer une carte d’interface réseau nommé *livres-nic1-être*et l’associer à la règle TAR *rdp1* et le pool d’adresses principal *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Sortie attendue :

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Créer une carte d’interface réseau nommé *être lb-carte réseau 2*et l’associer à la règle TAR *rdp2* et le pool d’adresses principal *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Créer une machine virtuelle (VM) nommée *web1*et l’associer à la carte réseau nommée *livres-nic1-être*. Un compte de stockage nommé *web1nrp* a été créé avant d’exécuter la commande ci-dessous.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Ordinateurs virtuels dans un équilibreur de charge doivent être dans le même jeu de disponibilité. Utilisez `azure availset create` pour créer une disponibilité définie.

    La sortie doit être similaire à la suivante :

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] Le message d’information, **il s’agit d’une carte NIC sans publicIP configuré** est attendu depuis la carte réseau créée pour se connecter à Internet à l’aide de l’adresse IP publique de charge équilibrage l’équilibreur de charge.

    Dans la mesure où *l’être lb-nic1* NIC est associé à la règle TAR *rdp1* , vous pouvez vous connecter sur *web1* en utilisant le protocole RDP via le port 3441 de l’équilibreur de charge.

4. Créer une machine virtuelle (VM) nommée *web2*et l’associer à la carte réseau nommée *être lb-carte réseau 2*. Un compte de stockage nommé *web1nrp* a été créé avant d’exécuter la commande ci-dessous.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="update-an-existing-load-balancer"></a>Mise à jour d’un équilibreur de charge existant

Vous pouvez ajouter des règles qui référence un équilibreur de charge existant. Dans l’exemple suivant, une nouvelle règle d’équilibrage de la charge est ajoutée à un équilibreur de charge existant **NRPlb**

    azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool

## <a name="delete-a-load-balancer"></a>Supprimer un équilibreur de charge

Pour supprimer un équilibreur de charge, utilisez la commande suivante :

    azure network lb delete --resource-group nrprg --name nrplb

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-cli.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)
