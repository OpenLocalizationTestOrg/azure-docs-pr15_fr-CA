<properties
   pageTitle="Créer un équilibreur de charge interne à l’aide de la CLI d’Azure dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge interne à l’aide de la CLI d’Azure dans le Gestionnaire de ressources"
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

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Créer un équilibreur de charge interne à l’aide de la CLI d’Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Déploiement de la solution à l’aide de la CLI d’Azure

Les étapes suivantes indiquent comment créer un équilibreur de charge faisant face à Internet en utilisant le Gestionnaire de ressources Azure avec l’interface CLI. Avec le Gestionnaire de ressources d’Azure, chaque ressource est créé et configuré individuellement et mettez ensuite ensemble pour créer une ressource.

Vous devez créer et configurer des objets suivants pour déployer un équilibreur de charge :

- **Configuration IP du front-end**: contient des adresses IP publiques pour le trafic réseau entrant
- **Pool d’adresses back-end**: contient les interfaces réseau (cartes réseau) qui permettent des ordinateurs virtuels recevoir le trafic réseau à partir de l’équilibreur de charge
- **Règles d’équilibrage de la charge**: contient des règles permettant de mapper un port public de l’équilibreur de charge pour le port dans le pool d’adresses de back-end
- **Règles de trafic entrant de NAT**: contient des règles permettant de mapper un port public de l’équilibreur de charge à un port pour une machine virtuelle spécifique dans le pool d’adresses de back-end
- **Sondes**: contient des sondes de santé permettant de vérifier la disponibilité des machines virtuelles des instances dans le pool d’adresses de back-end

Pour plus d’informations, consultez [Le Gestionnaire de ressources Azure prise en charge de l’équilibrage de la charge](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Paramétrer CLI pour utiliser le Gestionnaire de ressources

1. Si vous n’avez jamais utilisé Azure CLI, reportez-vous à la section [installer et configurer l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md). Suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécutez la commande **mode config azure** pour passer en mode de gestionnaire de ressources, comme suit :

        azure config mode arm

    Sortie attendue :

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Créer un équilibreur de charge interne étape par étape

1. Se connecter à Azure.

        azure login

    Lorsque vous y êtes invité, entrez vos informations d’identification Azure.

2. Modifier les outils de commande en mode de Gestionnaire des ressources Azure.

        azure config mode arm

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Toutes les ressources dans le Gestionnaire de ressources Azure sont associés à un groupe de ressources. Si vous ne l’avez pas déjà fait, créez un groupe de ressources.

    azure group create <resource group name> <location>

## <a name="create-an-internal-load-balancer-set"></a>Créer un jeu d’équilibreur de charge interne

1. Créer un équilibreur de charge interne

    Dans le scénario suivant, un groupe de ressources nommé nrprg est créé dans la région des États-Unis.

        azure network lb create --name nrprg --location eastus

    >[AZURE.NOTE] Toutes les ressources pour un équilibrage de charge interne des sous-réseaux de réseaux virtuels, réseaux virtuels doivent être dans le même groupe de ressources et dans la même région.

2. Créer une adresse IP front-end pour l’équilibreur de charge interne.

    L’adresse IP que vous utilisez doit être dans la plage de sous-réseau de votre réseau virtuel.

        azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet

3. Créer le pool d’adresses de back-end.

        azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb

    Après avoir défini une adresse IP front-end et un pool d’adresses de back-end, vous pouvez créer des règles d’équilibreur de charge, des règles NAT entrantes, et des sondes de santé personnalisées.

4. Créer une règle d’équilibrage de la charge de l’équilibreur de charge interne.

    Lorsque vous suivez les étapes précédentes, la commande crée une règle d’équilibrage de la charge pour écouter le port 1433 dans le pool frontal et envoyer le trafic réseau à charge équilibrée au pool d’adresses de back-end, utilisant également le port 1433.

        azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb

5. Créer des règles NAT entrantes.

    Règles de trafic entrant NAT sont utilisés pour créer des points de terminaison dans un équilibreur de charge à une instance de machine virtuelle spécifique. Les étapes précédentes de créer deux règles NAT pour Bureau à distance.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389

6. Créer des sondes de santé pour l’équilibreur de charge.

    Une sonde de santé vérifie toutes les instances de machine virtuelle pour vous assurer qu’ils peuvent envoyer du trafic réseau. L’instance de l’ordinateur virtuel avec les vérifications de sondages a échoué est supprimé de l’équilibreur de charge jusqu'à ce qu’il est en ligne et un contrôle de sonde détermine qu’elle est saine.

        azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4

    >[AZURE.NOTE]La plate-forme Microsoft Azure utilise une adresse IPv4 statique routable publiquement pour de nombreux scénarios d’administration. L’adresse IP est 168.63.129.16. Cette adresse IP ne doit pas être bloquée par un pare-feu, car cela peut entraîner un comportement inattendu.
    >En ce qui concerne les Azure interne d’équilibrage de charge, cette adresse IP est utilisée par la surveillance des sondes de l’équilibreur de charge pour déterminer l’état de santé pour les machines virtuelles dans un ensemble équilibré en charge. Si un groupe de sécurité réseau est utilisé pour restreindre le trafic vers des machines virtuelles Azure dans un jeu en interne avec équilibrage de charge ou est appliqué à un sous-réseau de réseau virtuel, assurez-vous qu’une règle de sécurité de réseau est ajoutée pour autoriser le trafic à partir de 168.63.129.16.

## <a name="create-nics"></a>Créer des cartes d’interface réseau

Vous devez créer des cartes d’interface réseau (ou modifier les existantes) et les associez à des règles NAT, les règles d’équilibrage de la charge et les sondes.

1. Créer une carte d’interface réseau nommé *livres-nic1-être*et puis l’associer à la règle TAR *rdp1* et le pool d’adresses principal *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus

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

2. Créer une carte d’interface réseau nommé *livres-carte réseau 2-être*et puis l’associer à la règle TAR *rdp2* et le pool d’adresses principal *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus

3. Créer un ordinateur virtuel nommé *DB1*et puis l’associer à la carte réseau nommée *livres-nic1-être*. Un compte de stockage appelé *web1nrp* est créé avant l’exécution de la commande suivante :

        azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Ordinateurs virtuels dans un équilibreur de charge doivent être dans le même jeu de disponibilité. Utilisez `azure availset create` pour créer une disponibilité définie.

4. Créer une machine virtuelle (VM) nommée *DB2*et puis l’associer à la carte réseau nommée *être lb-carte réseau 2*. Un compte de stockage nommé *web1nrp* a été créé avant d’exécuter la commande suivante.

        azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="delete-a-load-balancer"></a>Supprimer un équilibreur de charge

Pour supprimer un équilibreur de charge, utilisez la commande suivante :

    azure network lb delete --resource-group nrprg --name ilbset

## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de la charge par l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)
