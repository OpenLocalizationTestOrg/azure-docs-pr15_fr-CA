<properties
    pageTitle="Déployer des ressources Azure à l’aide de C# | Microsoft Azure"
    description="Apprenez à utiliser C# et le Gestionnaire de ressources Azure pour créer des ressources de Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="deploy-azure-resources-using-c"></a>Déployer des ressources Azure à l’aide de C# 

Cet article vous montre comment créer des ressources Azure à l’aide de C#.

Vous devez d’abord vous assurer que vous avez terminé ces tâches :

- Installation de [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Vérifier l’installation de [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855) ou de [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595)
- Obtenir un [jeton d’authentification](../resource-group-authenticate-service-principal.md)

Il prend environ 30 minutes pour effectuer ces étapes.

## <a name="step-1-create-a-visual-studio-project-and-install-the-libraries"></a>Étape 1 : Créer un projet Visual Studio et installez les bibliothèques

Packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ce didacticiel. Pour obtenir les bibliothèques dont vous avez besoin dans Visual Studio, procédez comme suit :

1. Cliquez sur **le fichier** > **Nouveau** > **projet**.

2. Dans les **modèles** > **Visual C#**, sélectionnez **Application Console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

3. Cliquez sur le nom du projet dans l’Explorateur de solutions, puis cliquez sur **Gérer les Packages NuGet**.

4. Cliquez sur **installer** pour le package de la bibliothèque d’authentification Active Directory et de type *Active Directory* dans la zone de recherche, puis suivez les instructions pour installer le package.

5. En haut de la page, sélectionnez **Inclure la version préliminaire**. Type *Microsoft.Azure.Management.Compute* dans la zone Rechercher, cliquez sur **installer** pour les bibliothèques .NET Compute et puis suivez les instructions pour installer le package.

6. Cliquez sur **installer** pour les bibliothèques réseau du .NET et de type *Microsoft.Azure.Management.Network* dans la zone de recherche, puis suivez les instructions pour installer le package.

7. Cliquez sur **installer** pour les bibliothèques .NET de stockage et de type *Microsoft.Azure.Management.Storage* dans la zone Rechercher, puis suivez les instructions pour installer le package.

8. Tapez *Microsoft.Azure.Management.ResourceManager* dans la zone Rechercher, cliquez sur **installer** pour les bibliothèques de gestion des ressources.

Vous êtes maintenant prêt à commencer à utiliser les bibliothèques pour créer votre application.

## <a name="step-2-create-the-credentials-that-are-used-to-authenticate-requests"></a>Étape 2 : Créer les informations d’identification utilisées pour authentifier les demandes

Maintenant vous mettre en forme les informations sur l’application que vous avez créé précédemment dans les informations d’identification utilisées pour authentifier les demandes pour le Gestionnaire de ressources Azure.

1. Ouvrez le fichier Program.cs pour le projet que vous avez créé et puis ajoutez ces instructions using au début du fichier :

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. Pour créer le jeton qui est nécessaire, ajoutez cette méthode à la classe Program :

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

    Remplacez {id client} avec l’identificateur d’Azure Active Directory application, {-secret client} avec la touche d’accès rapide de l’application de AD et {id client} avec l’identificateur de client pour votre abonnement. Vous pouvez trouver l’id du client en exécutant Get-AzureRmSubscription. Vous pouvez trouver la touche d’accès rapide à l’aide du portail Azure.

3. Pour appeler la méthode que vous avez ajouté précédemment, ajoutez ce code à la méthode Main dans le fichier Program.cs :

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Enregistrez le fichier Program.cs.

## <a name="step-3-register-the-resource-providers-and-create-the-resources"></a>Étape 3 : Enregistrer les fournisseurs de ressources et de créer les ressources

### <a name="register-the-providers-and-create-a-resource-group"></a>Inscrire les fournisseurs et de créer un groupe de ressources

Toutes les ressources doivent être contenus dans un groupe de ressources. Avant de pouvoir ajouter des ressources à un groupe, votre abonnement doit être enregistré avec les fournisseurs de ressources.

1. Ajoutez les variables à la méthode Main de la classe de programme pour spécifier les noms que vous souhaitez utiliser pour les ressources :

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Remplacez toutes les valeurs de la variable avec le nom et l’identificateur que vous souhaitez utiliser. Vous pouvez trouver l’identificateur d’abonnement en exécutant Get-AzureRmSubscription.

2. Pour créer le groupe de ressources et inscrire les fournisseurs, ajoutez cette méthode à la classe Program :

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
            
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Pour appeler la méthode que vous avez ajouté précédemment, ajoutez ce code à la méthode Main :

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>Créer un compte de stockage

Un [compte de stockage](../storage/storage-create-storage-account.md) est nécessaire pour stocker le fichier de disque dur virtuel est créé pour l’ordinateur virtuel.

1. Pour créer le compte de stockage, ajoutez cette méthode à la classe Program :

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. Pour appeler la méthode que vous avez ajouté précédemment, ajoutez ce code à la méthode Main de la classe Program :

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>Créer l’adresse IP publique

Une adresse IP publique est nécessaire pour communiquer avec l’ordinateur virtuel.

1. Pour créer l’adresse IP publique de l’ordinateur virtuel, ajoutez cette méthode à la classe Program :

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. Pour appeler la méthode que vous avez ajouté précédemment, ajoutez ce code à la méthode Main de la classe Program :

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>Créez le réseau virtuel

Un ordinateur virtuel qui est créé avec le modèle de déploiement du Gestionnaire de ressources doit être un réseau virtuel.

1. Pour créer un sous-réseau et un réseau virtuel, ajoutez cette méthode à la classe Program :

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. Pour appeler la méthode que vous avez ajouté précédemment, ajoutez ce code à la méthode Main de la classe Program :

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### <a name="create-the-network-interface"></a>Création de l’interface réseau

Une machine virtuelle a besoin d’une interface réseau pour communiquer sur le réseau virtuel.

1. Pour créer une interface réseau, ajoutez cette méthode à la classe Program :

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. Pour appeler la méthode que vous avez ajouté précédemment, ajoutez ce code à la méthode Main de la classe Program :

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>Créer un jeu de disponibilité

Jeux de disponibilité facilement vous permettant de gérer la maintenance des ordinateurs virtuels utilisés par votre application.

1. Pour créer le jeu de disponibilité, ajoutez cette méthode à la classe Program :

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. Pour appeler la méthode que vous avez ajouté précédemment, ajoutez ce code à la méthode Main de la classe Program :

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>Créer un ordinateur virtuel

Maintenant que vous avez créé toutes les ressources de prise en charge, vous pouvez créer un ordinateur virtuel.

1. Pour créer la machine virtuelle, ajoutez cette méthode à la classe Program :

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
        }

    >[AZURE.NOTE] Ce didacticiel crée un ordinateur virtuel exécutant une version du système d’exploitation Windows Server. Pour en savoir plus sur la sélection d’autres images, voir [Naviguer et sélectionnez machine virtuelle Azure des images avec Windows PowerShell et de l’infrastructure du langage commun Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Pour appeler la méthode que vous avez ajouté précédemment, ajoutez ce code à la méthode Main :

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##<a name="step-4-delete-the-resources"></a>Étape 4 : Supprimer les ressources

Car vous sont facturés pour les ressources utilisées dans Azure, il est toujours une bonne pratique pour supprimer les ressources qui ne sont plus nécessaires. Si vous souhaitez supprimer les machines virtuelles et toutes les ressources de prise en charge, tout vous avez à faire est de supprimer le groupe de ressources.

1.  Pour supprimer le groupe de ressources, ajoutez cette méthode à la classe Program :

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Pour appeler la méthode que vous avez ajouté précédemment, ajoutez ce code à la méthode Main :

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## <a name="step-5-run-the-console-application"></a>Étape 5 : Exécution de l’application console

1. Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

2. Appuyez sur **entrée** après que chaque code d’état est retourné pour créer chaque ressource. Une fois que la machine virtuelle est créée, effectuez l’étape suivante avant d’appuyer sur ENTRÉE pour supprimer toutes les ressources.

    Il devrait prendre environ cinq minutes pour cette application de console pour s’exécuter à partir du début. Avant d’appuyer sur ENTRÉE pour commencer la suppression des ressources, vous pouvez prendre quelques minutes pour vérifier la création des ressources dans le portail Azure avant de les supprimer.

3. Pour afficher l’état des ressources, parcourir les journaux d’Audit dans le portail Azure :

    ![Parcourir les journaux d’audit dans Azure portal](./media/virtual-machines-windows-csharp/crpportal.png)
    
## <a name="next-steps"></a>Étapes suivantes

- Tirer parti de l’utilisation d’un modèle pour créer un ordinateur virtuel en utilisant les informations de [déployer un ordinateur virtuel de Azure à l’aide de C# et un modèle de gestionnaire de ressources](virtual-machines-windows-csharp-template.md).
- Apprenez à gérer la machine virtuelle que vous avez créé en passant en revue les [machines virtuelles de gérer à l’aide du Gestionnaire de ressources Azure et PowerShell](virtual-machines-windows-csharp-manage.md).
