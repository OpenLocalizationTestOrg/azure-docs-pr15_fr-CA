## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Comment créer un VNet classique à l’aide de la CLI d’Azure

Vous pouvez utiliser la CLI Azure pour gérer vos ressources Azure à partir de l’invite de commande à partir de n’importe quel ordinateur exécutant Windows, Linux ou OSX. Pour créer un VNet à l’aide de la CLI d’Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
2. Exécuter la commande **créer des vnet de réseau azure** pour créer un VNet et un sous-réseau, comme illustré ci-dessous. La liste est indiquée après que la sortie explique les paramètres utilisés.

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    Sortie attendue :

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **vnet--**. Nom de la VNet à créer. Pour notre scénario, *TestVNet*
    - **-e (ou--espace d’adressage)**. Espace d’adressage VNet. Pour notre scénario, *192.168.0.0*
    - **-i (ou cidr-)**. Masque de réseau au format CIDR. Pour notre scénario, *16*.
    - **- n (ou--nom du sous-réseau**). Nom du premier sous-réseau. Pour notre scénario, *site Web frontal*.
    - **-p (ou--sous-réseau-début-ip)**. Adresse IP de début pour un sous-réseau ou d’espace d’adressage de sous-réseau. Pour notre scénario, *192.168.1.0*.
    - **-r (ou--sous-réseau-routage inter-domaines sans classe)**. Masque de réseau dans le format CIDR de sous-réseau. Pour notre scénario, *24*.
    - **-l (ou--emplacement)**. Région Azure où sera créé le VNet. Pour notre scénario, *États-Unis centre*.

3. Exécutez la commande **créer des vnet de sous-réseau azure** pour créer un sous-réseau comme indiqué ci-dessous. La liste est indiquée après que la sortie explique les paramètres utilisés.

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    Voici la sortie attendue de la commande ci-dessus :

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (ou nom-vnet--**. Nom de le VNet où sera créé le sous-réseau. Pour notre scénario, il s’agit de *TestVNet*.
    - **n-(ou--nom)**. Nom du nouveau sous-réseau. Pour notre scénario, le *serveur principal*.
    - **-a (ou--préfixe d’adresse)**. Bloc CIDR de sous-réseau. Quatre notre scénario, un *192.168.2.0/24*.

4. Exécutez la commande **réseau azure vnet afficher** pour afficher les propriétés de la nouvelle vnet, comme illustré ci-dessous.

            azure network vnet show

    Voici la sortie attendue de la commande ci-dessus :

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK
