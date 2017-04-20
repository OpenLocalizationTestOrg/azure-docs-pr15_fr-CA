<properties
   pageTitle="Plusieurs adresses IP pour les machines virtuelles - PowerShell | Microsoft Azure"
   description="Découvrez comment attribuer plusieurs adresses IP à un ordinateur virtuel à l’aide de PowerShell d’Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />


# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Affecter plusieurs adresses IP à des machines virtuelles

Un Azure Machine virtuelle (VM) peut avoir une ou plusieurs interfaces réseau (NIC) attachés. Une carte d’interface réseau peut avoir un ou plusieurs publics ou privés adresses IP qui lui est assignées. Si vous n’êtes pas familier avec les adresses IP dans Azure, lisez l’article de [adresses IP dans Azure](virtual-network-ip-addresses-overview-arm.md) pour en savoir plus à leur sujet. Cet article explique comment utiliser PowerShell d’Azure pour affecter plusieurs adresses IP pour un ordinateur virtuel dans le modèle de déploiement du Gestionnaire de ressources Azure.

Affectation de plusieurs adresses IP à un ordinateur virtuel Active les fonctionnalités suivantes :

- Hébergement de plusieurs sites Web ou des services avec des adresses IP différentes et des certificats SSL sur un serveur unique.
- Servir un matériel de réseau virtuel, tel qu’un pare-feu ou l’équilibreur de charge.
- La possibilité d’ajouter des adresses IP privées pour une des cartes réseau à un pool d’Azure équilibreur de charge back-end. Dans le passé, seule l’adresse IP principale de la carte réseau principale pu être ajouté à un pool de back-end.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Pour obtenir l’aperçu, envoyez un e-mail à [Plusieurs IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) avec votre ID d’abonnement et de la destination.

## <a name="scenario"></a>Scénario

Dans cet article, vous allez associer trois configurations IP d’une interface réseau.
Les configurations d’exemple suivant seront créées et affectées à une carte réseau ayant trois adresses IP privées et une adresse IP publique affectée à :

- IPConfig-1 : Une adresse IP privée dynamique (par défaut) et un public l’adresse IP de la ressource d’adresse IP publique nommée PIP1.
- IPConfig-2 : Aucune adresse IP publique et une adresse IP privée.
- IPConfig-3 : Adresse IP privée dynamique et aucune adresse IP publique.

    ![Texte de l’image ALT](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Ce scénario suppose que vous avez un groupe de ressources appelé *RG1* dans laquelle se trouve un VNet appelé *VNet1* et un sous-réseau appelé *Subnet1*. En outre, il suppose que vous disposez d’une machine virtuelle appelée *VM1*et une interface de réseau appelée *VM1-NIC1* associé à une adresse IP publique appelée *PIP1*.

[Cet article](./virtual-machines/virtual-machines-windows-ps-create.md ) explique comment créer les ressources mentionnées ci-dessus dans le cas où vous n’avez pas créé auparavant.

## <a name = "create"></a>Créer un ordinateur virtuel avec plusieurs adresses IP

1. Ouvrez une invite de commande PowerShell et terminer les étapes restantes de cette section au sein d’une seule session de PowerShell. Si vous n’avez pas installé et configuré de PowerShell, suivez les étapes de l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

2. Modifier les « valeurs » de le $Variables suivant à [l’emplacement](https://azure.microsoft.com/regions) d' Azure que votre réseau virtuel est, le nom de votre [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups), le VNet dans le groupe de ressources et le sous-réseau que vous souhaitez vous connecter à la carte réseau pour le nom de la carte réseau. Suivez les étapes pour ajouter plusieurs adresses IP à une carte réseau attaché à une machine virtuelle, que vous le souhaitez.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    Si vous ne connaissez pas le nom d’un emplacement Azure existant ou d’un groupe de ressources, tapez les commandes suivantes :

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>La carte réseau doit être connectée à un sous-réseau au sein d’un réseau virtuel existant Azure (VNet). Les trois composants : carte réseau, du sous-réseau et VNet, doivent tous exister dans la même région et [l’abonnement](../azure-glossary-cloud-terminology.md#subscription).  Si vous n’êtes pas familier avec VNets, lisez l’article de [Présentation de réseau virtuel](virtual-networks-overview.md) pour en savoir plus sur les ou lisez l’article [créer un VNet](virtual-networks-create-vnet-arm-ps.md) pour savoir comment en créer un.

    Si vous ne connaissez pas le nom d’un VNet existant, entrez la commande suivante et remplacez le nom d’un VNet de *VNet1* dans la variable précédente :

        Get-AzureRmVirtualNetwork | Format-Table Name

    Si la liste retournée est vide, vous devez créer un VNet. Pour en savoir plus, voir l’article [créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md) .

    Tapez les commandes suivantes pour obtenir le nom du sous-réseau existant au sein de la VNet et remplacez *Subnet1* ci-dessus par le nom d’un sous-réseau :

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. Entrez la commande suivante pour récupérer le sous-réseau et l’assigner à une variable.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Définir les configurations IP que vous souhaitez affecter à la carte réseau. Chaque configuration peut comporter un statique ou dynamique adresse IP privée et un associé de ressource d’adresse IP publique avec une adresse statique ou dynamique.

    Ajouter ou supprimer un certain nombre des configurations qui suivent, selon le nombre d’adresses IP à associer à la carte réseau et les paramètres que vous souhaitez configurer.

    **IPConfig-1**

    Modifier la valeur *PIP1* pour le nom de ressource adresse IP existant public qui existe à l’emplacement que vous créez la carte d’interface réseau dans et qui n’est pas associé à une autre carte de réseau. Modification *RG1* au nom de la ressource d’adresse IP publique du groupe de ressources existe dans. Remplacez le nom que vous souhaitez donner à la première configuration IP *IPConfig-1* . Entrez les commandes suivantes :

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    Remarque le *-principal* basculer. Lorsque vous affectez plusieurs configurations IP à une carte réseau, une configuration doit être affectée en tant que *principal*. Si vous ne connaissez pas le nom d’une ressource d’adresse IP publique existante, entrez la commande suivante : Get-AzureRMPublicIPAddress | Nom de format-Table, IPFichier de l’emplacement, l’adresse IP,

    Si la colonne **IPFichier** a aucune valeur dans le résultat renvoyé, la ressource d’adresse IP publique n’est pas associée à une carte d’interface réseau et peut être utilisée. Si la liste est vide ou si aucune ressource d’adresse IP publique disponible, vous pouvez en créer un à l’aide de la commande **New-AzureRmPublicIPAddress** .

    >[AZURE.NOTE] Les adresses IP publiques ont une somme insignifiante. Pour en savoir plus sur la tarification des adresses IP, lisez la page [tarification d’adresse IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

    **IPConfig-2**

    Remplacez le nom que vous souhaitez donner à la seconde configuration IP et de modifier *10.0.0.5* pour une adresse IP valide inutilisée pour le sous-réseau que vous affectez à la carte réseau à *IPConfig-2* . Entrez les commandes suivantes :

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    Entrez la commande suivante, si vous ne connaissez pas l’adresse IP affectée au sous-réseau de plage d’adresses :

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    Modifier *IPConfig-3* pour le nom que vous souhaitez donner à la troisième configuration IP et entrez les commandes suivantes :

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] Vous pouvez affecter jusqu'à 250 adresses IP privées à une carte réseau. Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour en savoir plus, lisez l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) .

6. Créez la carte d’interface réseau en utilisant les configurations IP définies dans l’étape précédente.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Joindre la carte réseau lors de la création d’une machine virtuelle en suivant les étapes décrites dans l’article [créer un ordinateur virtuel](../virtual-machines/virtual-machines-windows-ps-create.md) . Bien que l’article crée un ordinateur virtuel exécutant Windows Server, les étapes sont les mêmes pour une VM Linux, autres que la sélection d’un autre système d’exploitation. Effectuez les étapes 1 à 3 de l’article. Ignorez les étapes 4 et 5 et ensuite l’étape 6 dans la créer un article de la machine virtuelle.

    >[AZURE.WARNING] Étape 6 de la créer un article de la machine virtuelle échoue si vous avez modifié la variable nommée $nic à une chose à l’étape 6 de cet article, ou que vous n’avez pas effectué les étapes précédentes de cet article.

8. Afficher l’adresse IP privé répond que DHCP Azure affecté à la carte réseau et la ressource d’adresse IP publique affectée à la NIC en entrant la commande suivante :

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Ajouter manuellement toutes les adresses IP privées (y compris le principal) pour la configuration TCP/IP du système d’exploitation. 

**Windows**

1. À partir d’une invite de commande, tapez *ipconfig/all*.  Vous ne voyez que l’adresse IP privée *principale* (via DHCP).
2. Ensuite, tapez *ncpa.cpl* dans la fenêtre d’invite de commandes. Une nouvelle fenêtre s’ouvre.
3. Ouvrez les propriétés de **Connexion au réseau Local**.
4. Double cliquez sur protocole Internet version 4 (IPv4)
5. Sélectionnez **utiliser l’adresse IP suivante** , entrez les valeurs suivantes :
    - **Adresse IP**: Saisissez l’adresse IP privée *principale*
    - **Masque de sous-réseau**: jeu basé sur votre sous-réseau. Par exemple, si le sous-réseau est un /24 puis le masque de sous-réseau est 255.255.255.0.
    - **Passerelle par défaut**: la première adresse IP du sous-réseau. Si votre sous-réseau est 10.0.0.0/24, l’adresse IP de la passerelle est 10.0.0.1.
    - Cliquez sur **utiliser l’adresse de serveur DNS suivante** et entrez les valeurs suivantes :
        - **Le serveur DNS préféré :** Entrez 168.63.129.16 si vous n’utilisez pas votre propre serveur DNS.  Si vous êtes, entrez l’adresse IP de votre serveur DNS.
    - Cliquez sur le bouton **Avancé** et ajoutez des adresses IP supplémentaires. Ajouter des adresses IP privées secondaires répertoriées à l’étape 8 pour la carte réseau avec le même sous-réseau que celui spécifié pour l’adresse IP principale.
    - Cliquez sur **OK** pour fermer les paramètres TCP/IP, puis sur **OK** pour fermer les paramètres de la carte. Ceci permet ensuite de rétablir votre connexion RDP.

6. À partir d’une invite de commande, tapez *ipconfig/all*. Toutes les adresses IP que vous avez ajoutés sont affichés et que DHCP est désactivé.


**Linux (Ubuntu)**

1. Ouvrez une fenêtre de terminal.
2. Assurez-vous que vous êtes l’utilisateur racine. Si vous ne l’êtes pas, vous pouvez pour cela à l’aide de la commande suivante :

            sudo -i
3. Mettre à jour le fichier de configuration de l’interface de réseau (en supposant que « eth0 »).
    - Conserver la ligne de facturation existante pour dhcp. Cela configurera l’adresse IP principale tel qu’il est utilisé pour être antérieure.
    - Ajouter une configuration pour une adresse IP statique supplémentaire avec les commandes suivantes :

                cd /etc/network/interfaces.d/
                ls

        Vous devriez voir un fichier .cfg.
4. Ouvrir le fichier : vi, *nom de fichier*.

    Vous devez voir les lignes suivantes à la fin du fichier :

            auto eth0
            iface eth0 inet dhcp
5. Après les lignes qui existent dans ce fichier, ajoutez les lignes suivantes :

            iface eth0 inet static
            address <your private IP address here>
6. Enregistrez le fichier à l’aide de la commande suivante :

            :wq
7.  Réinitialisation de l’interface réseau avec la commande suivante :

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] Exécuter à la fois ifdown et ifup dans la même ligne si vous utilisez une connexion à distance.
8. Vérifiez que l’adresse IP est ajoutée à l’interface réseau avec la commande suivante :

            ip addr list eth0

    Vous devez voir l’adresse IP que vous avez ajouté en tant que partie de la liste.

**Linux (Redhat, CentOS, etc.)**

1. Ouvrez une fenêtre de terminal.
2. Assurez-vous que vous êtes l’utilisateur racine. Si vous ne l’êtes pas, vous pouvez pour cela à l’aide de la commande suivante :

            sudo -i
3. Entrez votre mot de passe et suivez les instructions à l’invite. Une fois l’utilisateur root, accédez au dossier scripts réseau avec la commande suivante :

            cd /etc/sysconfig/network-scripts
4. Liste les fichiers ifcfg connexes à l’aide de la commande suivante :

            ls ifcfg-*

    Vous devez voir *ifcfg-eth0* comme l’un des fichiers.
5. Copiez le fichier *ifcfg-eth0* et nommez-le *ifcfg-eth0:0* avec la commande suivante :

            cp ifcfg-eth0 ifcfg-eth0:0
6. Modifier la *ifcfg-eth0:0* le fichier avec la commande suivante :

            vi ifcfg-eth1
7. Modifier le périphérique pour le nom approprié dans le fichier ; *eth0:0* dans ce cas, la commande suivante :

            DEVICE=eth0:0
8. Modifier la *IPADDR = YourPrivateIPAddress* ligne pour refléter l’adresse IP.
9. Enregistrez le fichier avec la commande suivante :

            :wq
10. Redémarrez les services de réseau et assurez-vous que les modifications ont réussi en exécutant les commandes suivantes :

            /etc/init.d/network restart
            Ipconfig

    Vous devez voir l’adresse IP que vous avez ajouté, *eth0:0*, dans la liste retournée.

## <a name="add"></a>Ajouter des adresses IP à un ordinateur virtuel existant

Effectuez les étapes suivantes pour ajouter des adresses IP supplémentaires à une carte d’interface réseau :

1. Ouvrez une invite de commande PowerShell et terminer les étapes restantes de cette section au sein d’une seule session de PowerShell. Si vous n’avez pas installé et configuré de PowerShell, suivez les étapes de l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

2. Modifier les « valeurs » de le $Variables suivant le nom de la carte réseau que vous souhaitez ajouter des adresses IP à et le groupe de ressources et l’emplacement de dans que la carte réseau existe :

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    Si vous ne connaissez pas le nom de la carte réseau que vous souhaitez modifier, entrez les commandes suivantes, puis modifiez les valeurs des variables précédentes :

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Créer une variable et définissez-la sur la carte d’interface réseau en tapant la commande suivante :

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. Récupérer l’ID de sous-réseau de la carte réseau est connectée à [l’étape 3](#subnet) de créer un ordinateur virtuel avec plusieurs section d’adresses IP de cet article.

5. Créer les configurations IP que vous souhaitez ajouter au réseau en suivant les instructions de [l’étape 5](#ipconfigs) de créer une machine virtuelle avec plusieurs section d’adresses IP de cet article.

6. Modifier *IPConfigName4 $* au nom de la configuration d’IP que vous avez créé à l’étape précédente. Pour ajouter la configuration, entrez la commande suivante :

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. Pour configurer la carte réseau avec la configuration IP, entrez la commande suivante :

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Ajoutez les adresses IP que vous avez ajouté à la carte réseau pour le système d’exploitation de l’ordinateur virtuel en suivant les instructions de [l’étape 9](#os) de créer une machine virtuelle avec plusieurs section d’adresses IP de cet article.
