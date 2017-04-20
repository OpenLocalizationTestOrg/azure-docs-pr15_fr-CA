## <a name="how-to-create-a-vnet-using-a-network-config-file-from-powershell"></a>Comment créer un VNet à l’aide d’un fichier de configuration réseau à partir de PowerShell

Azure utilise un fichier xml pour définir tous les VNets disponibles à un abonnement. Vous pouvez télécharger ce fichier, le modifier pour modifier ou supprimer les VNets existants et créer de nouveaux. Dans ce document, vous apprenez à télécharger ce fichier, appelé fichier de configuration (ou netcgf) réseau et modifiez-le pour créer un nouveau VNet. Vérifiez le [schéma de configuration Azure réseau virtuel](https://msdn.microsoft.com/library/azure/jj157100.aspx) pour en savoir plus sur le fichier de configuration réseau.

Pour créer un VNet à l’aide d’un fichier de netcfg à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../articles/powershell-install-configure.md) et suivez les instructions jusqu'à la fin pour signer dans Azure et sélectionnez votre abonnement.
2. À partir de la console PowerShell d’Azure, utilisez l’applet de commande **Get-AzureVnetConfig** pour télécharger le fichier de configuration de réseau en exécutant la commande ci-dessous. 

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    Sortie attendue :

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. Ouvrez le fichier que vous avez enregistré à l’étape 2 ci-dessus à l’aide de n’importe quelle application d’éditeur de texte ou XML et recherchez le **<VirtualNetworkSites>** élément. Si vous avez des réseaux déjà créés, chaque réseau s’affiche comme sa propre **<VirtualNetworkSite>** élément.
4. Pour créer le réseau virtuel décrit dans ce scénario, ajoutez le code XML suivant juste sous la **<VirtualNetworkSites>** élément :

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  Enregistrez le fichier de configuration réseau.
10. À partir de la console PowerShell d’Azure, utilisez l’applet de commande **Set-AzureVnetConfig** pour télécharger le fichier de configuration de réseau en exécutant la commande ci-dessous. Notez la sortie de la commande, vous devez voir **Succeeded** sous **OperationStatus**. Si c’est pas le cas, vérifiez le fichier xml des erreurs.

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    Voici la sortie attendue de la commande ci-dessus :

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
    
11. À partir de la console PowerShell d’Azure, utilisez l’applet de commande **Get-AzureVnetSite** pour vérifier que le nouveau réseau a été ajouté en exécutant la commande ci-dessous. 

        Get-AzureVNetSite -VNetName TestVNet

    Voici la sortie attendue de la commande ci-dessus :

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded