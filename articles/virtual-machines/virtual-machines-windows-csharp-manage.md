<properties
    pageTitle="Gérer des ordinateurs virtuels à l’aide du Gestionnaire de ressources Azure et C# | Microsoft Azure"
    description="Gérer des ordinateurs virtuels à l’aide du Gestionnaire de ressources Azure et C#."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Gérer des ordinateurs virtuels de Azure à l’aide du Gestionnaire de ressources Azure et C#  

Les tâches présentées dans cet article vous indiquent comment gérer des machines virtuelles, telles que le démarrage, l’arrêt et la mise à jour. Une machine virtuelle doit exister dans un groupe de ressources pour effectuer les tâches dans cet article.

Pour effectuer les tâches de cet article, vous devez :

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Un jeton d’authentification](../resource-group-authenticate-service-principal.md)

## <a name="create-a-visual-studio-project-and-install-packages"></a>Créez un projet Visual Studio et installez les packages

NuGet packages sont les méthodes les plus simples pour installer les bibliothèques dont vous avez besoin pour terminer les tâches de cet article. Les bibliothèques que vous installez pour cet article sont la bibliothèque d’authentification Active Directory Azure et la bibliothèque de fournisseur de ressources de calcul. Suivez ces étapes pour obtenir les bibliothèques dans Visual Studio :

1. Cliquez sur **le fichier** > **Nouveau** > **projet**.

2. Dans les **modèles** > **Visual C#**, sélectionnez **Application Console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

3. Cliquez sur le nom du projet dans l’Explorateur de solutions, puis cliquez sur **Gérer les Packages NuGet**.

4. Cliquez sur **installer** pour le package de la bibliothèque d’authentification Active Directory et de type *Active Directory* dans la zone de recherche, puis suivez les instructions pour installer le package.

5. En haut de la page, sélectionnez **Inclure la version préliminaire**. Type *Microsoft.Azure.Management.Compute* dans la zone Rechercher, cliquez sur **installer** pour les bibliothèques .NET Compute et puis suivez les instructions pour installer le package.

Vous êtes maintenant prêt à commencer à utiliser les bibliothèques pour gérer vos ordinateurs virtuels.

## <a name="set-up-the-project"></a>Paramétrer le projet

Maintenant que l’application est créée et les bibliothèques sont installées, vous créez un jeton à l’aide des informations de l’application. Ce jeton est utilisé pour authentifier les demandes pour le Gestionnaire de ressources Azure.

1. Ouvrez le fichier Program.cs pour le projet que vous avez créé et puis ajoutez ces instructions using au début du fichier :

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. Ajoutez les variables à la méthode Main de la classe de programme pour spécifier le nom du groupe de ressources et le nom de la machine virtuelle et votre identificateur d’abonnement :

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var subscriptionId = "subsciption id";

    Vous pouvez trouver l’identificateur d’abonnement en exécutant Get-AzureRmSubscription.
    
3. Pour obtenir le jeton qui est nécessaire pour créer les informations d’identification, ajoutez cette méthode à la classe Program :

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
    
4. Pour créer les informations d’identification, ajoutez ce code à la méthode Main dans Program.cs :

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

5. Enregistrez le fichier Program.cs.

## <a name="display-information-about-a-virtual-machine"></a>Afficher des informations sur une machine virtuelle

1. Ajoutez cette méthode à la classe Program dans le projet que vous avez créé précédemment :

        public static async void GetVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");

          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
            groupName, 
            vmName, 
            InstanceViewTypes.InstanceView);

          Console.WriteLine("hardwareProfile");
          Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

          Console.WriteLine("\nstorageProfile");
          Console.WriteLine("  imageReference");
          Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
          Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
          Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
          Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
          Console.WriteLine("  osDisk");
          Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
          Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
          Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
          Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);

          Console.WriteLine("\nosProfile");
          Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
          Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
          Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
          Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

          Console.WriteLine("\nnetworkProfile");
          foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
          {
            Console.WriteLine("  networkInterface id: " + nic.Id);
          }

          Console.WriteLine("\nvmAgent");
          Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
          Console.WriteLine("    statuses");
          foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
          {
            Console.WriteLine("    code: " + stat.Code);
            Console.WriteLine("    level: " + stat.Level);
            Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
            Console.WriteLine("    message: " + stat.Message);
            Console.WriteLine("    time: " + stat.Time);
          }

          Console.WriteLine("\ndisks");
          foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
          {
            Console.WriteLine("  name: " + idisk.Name);
            Console.WriteLine("  statuses");
            foreach (InstanceViewStatus istat in idisk.Statuses)
            {
              Console.WriteLine("    code: " + istat.Code);
              Console.WriteLine("    level: " + istat.Level);
              Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
              Console.WriteLine("    time: " + istat.Time);
            }
          }

          Console.WriteLine("\nVM general status");
          Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
          Console.WriteLine("  id: " + vmResult.Id);
          Console.WriteLine("  name: " + vmResult.Name);
          Console.WriteLine("  type: " + vmResult.Type);
          Console.WriteLine("  location: " + vmResult.Location);
          Console.WriteLine("\nVM instance status");
          foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
          {
            Console.WriteLine("\n  code: " + istat.Code);
            Console.WriteLine("  level: " + istat.Level);
            Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
          }
          
        }

2. Pour appeler la méthode que vous venez d’ajouter, ajoutez ce code à la méthode Main :

        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Enregistrez le fichier Program.cs.

4. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

    Lorsque vous exécutez cette méthode, vous devez voir quelque chose comme dans l’exemple :
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite

          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True

          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1

          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM

          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM

          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus

          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>Arrêter une machine virtuelle

Vous pouvez arrêter un ordinateur virtuel de deux manières. Vous pouvez arrêter une machine virtuelle et conserver tous ses paramètres, mais continuer à donner lieu, ou vous pouvez arrêter une machine virtuelle et désallouer. Lorsqu’une machine virtuelle est désallouée, toutes les ressources qui lui sont associés sont également extrémités désallouées et de facturation.

1. Commentez tout code que vous avez ajouté précédemment à la méthode Main, à l’exception du code pour obtenir les informations d’identification.

2. Ajoutez cette méthode à la classe Program :

        public static async void StopVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
        }

    Si vous souhaitez libérer l’ordinateur virtuel, modifiez l’appel de mettre hors tension à ce code :

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. Pour appeler la méthode que vous venez d’ajouter, ajoutez ce code à la méthode Main :

        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Enregistrez le fichier Program.cs.

5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

    Vous devez voir l’état de la modification de la machine virtuelle arrêté. Si vous avez exécuté la méthode qu'appelante Deallocate, l’état est arrêté (libérée).

## <a name="start-a-virtual-machine"></a>Démarrage d’une machine virtuelle

1. Commentez tout code que vous avez ajouté précédemment à la méthode Main, à l’exception du code pour obtenir les informations d’identification.

2. Ajoutez cette méthode à la classe Program :

        public static async void StartVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
        }

3. Pour appeler la méthode que vous venez d’ajouter, ajoutez ce code à la méthode Main :

        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Enregistrez le fichier Program.cs.

5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

    Vous devez voir l’état de la machine virtuelle à changer en cours d’exécution.

## <a name="restart-a-running-virtual-machine"></a>Redémarrer une machine virtuelle en cours d’exécution

1. Commentez tout code que vous avez ajouté précédemment à la méthode Main, à l’exception du code pour obtenir les informations d’identification.

2. Ajoutez cette méthode à la classe Program :

        public static async void RestartVirtualMachineAsync(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
        }

3. Pour appeler la méthode que vous venez d’ajouter, ajoutez ce code à la méthode Main :

        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Enregistrez le fichier Program.cs.

5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

## <a name="resize-a-virtual-machine"></a>Redimensionner une machine virtuelle

Cet exemple vous montre comment modifier la taille d’une machine virtuelle en cours d’exécution.

1. Commentez tout code que vous avez ajouté précédemment à la méthode Main, à l’exception du code pour obtenir les informations d’identification.

2. Ajoutez cette méthode à la classe Program :

        public static async void UpdateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Pour appeler la méthode que vous venez d’ajouter, ajoutez ce code à la méthode Main :

        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Enregistrez le fichier Program.cs.

5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

    Vous devriez voir la taille de la modification de la machine virtuelle de Standard_A1.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Ajouter un disque de données à une machine virtuelle

Cet exemple vous montre comment ajouter un disque de données à une machine virtuelle en cours d’exécution.

1. Commentez tout code que vous avez ajouté précédemment à la méthode Main, à l’exception du code pour obtenir les informations d’identification.

2. Ajoutez cette méthode à la classe Program :

        public static async void AddDataDiskAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Adding the disk to the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.StorageProfile.DataDisks.Add(
            new DataDisk
              {
                Lun = 0,
                Name = "mydatadisk1",
                Vhd = new VirtualHardDisk
                  {
                    Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
                  },
                CreateOption = DiskCreateOptionTypes.Empty,
                DiskSizeGB = 2,
                Caching = CachingTypes.ReadWrite
              });
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Pour appeler la méthode que vous venez d’ajouter, ajoutez ce code à la méthode Main :

        AddDataDiskAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Enregistrez le fichier Program.cs.

5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

## <a name="delete-a-virtual-machine"></a>Supprimer un ordinateur virtuel

1. Commentez tout code que vous avez ajouté précédemment à la méthode Main, à l’exception du code pour obtenir les informations d’identification.

2. Ajoutez cette méthode à la classe Program :

        public static async void DeleteVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
        }

3. Pour appeler la méthode que vous venez d’ajouter, ajoutez ce code à la méthode Main :

        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Enregistrez le fichier Program.cs.

5. Cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD en utilisant les mêmes nom d’utilisateur et mot de passe que vous utilisez avec votre abonnement.

## <a name="next-steps"></a>Étapes suivantes

S’il y a des problèmes avec un déploiement, vous ressemble à des [déploiements de groupe de ressources de résolution des problèmes avec Azure portal](../resource-manager-troubleshoot-deployments-portal.md)
