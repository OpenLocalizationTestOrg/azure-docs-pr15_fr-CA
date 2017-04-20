<properties
    pageTitle="Nœuds Linux dans des pools d’Azure lot | Microsoft Azure"
    description="Découvrez comment traiter vos charges de travail de calcul parallèle sur des pools de machines virtuelles de Linux dans le lot d’Azure."
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="marsma" />

# <a name="provision-linux-compute-nodes-in-azure-batch-pools"></a>Configurer les nœuds de calcul Linux dans des pools d’Azure lot

Vous pouvez utiliser Azure lot pour exécuter des charges de travail de calcul parallèle sur les ordinateurs virtuels Linux et Windows. Cet article explique en détail comment créer des pools de nœuds de calcul Linux dans le service de traitement par lots à l’aide de deux [Commandes Python] [ py_batch_package] et [Lot .NET] [ api_net] les bibliothèques clientes.

> [AZURE.NOTE] [Packages d’applications](batch-application-packages.md) sont actuellement pas pris en charge sur les nœuds de calcul Linux.

## <a name="virtual-machine-configuration"></a>Configuration de machine virtuelle

Lorsque vous créez un groupe de nœuds de calcul dans le traitement par lots, vous avez deux options permettant de sélectionner la taille du nœud et le système d’exploitation : Configuration des Services de Cloud et de Configuration d’ordinateur virtuel.

**Configuration de Services de cloud** fournit que Windows compute nœuds *uniquement*. Tailles de nœud de calcul disponibles sont répertoriés dans les [tailles pour les Services en nuage](../cloud-services/cloud-services-sizes-specs.md)et les systèmes d’exploitation disponibles sont répertoriés dans les [versions de système d’exploitation invité de Azure et matrice de compatibilité SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Lorsque vous créez un pool contenant des nœuds de Services Cloud Azure, vous devez spécifier uniquement la taille du nœud et ses « OS famille », qui figurent dans les articles mentionnés précédemment. Pour les nœuds de calcul pools de Windows, les Services en nuage est plus communément utilisé.

**Configuration de Machine virtuelle** fournit des images de Linux et Windows pour les nœuds de calcul. Tailles de nœud de calcul disponibles sont répertoriés dans les [tailles pour les machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows) et de [tailles pour les machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux). Lorsque vous créez un pool qui contient les nœuds de Configuration de Machine virtuelle, vous devez spécifier la taille de l’agent de nœud de lots SKU doit être installé sur les nœuds, les nœuds et la référence d’image de machine virtuelle.

### <a name="virtual-machine-image-reference"></a>Référence d’image de machine virtuelle

Le service de traitement par lots utilise [l’échelle de machine virtuelle définit](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pour fournir des nœuds de calcul Linux. Les images de système d’exploitation pour ces machines virtuelles sont fournis par le [Azure Marketplace][vm_marketplace]. Lorsque vous configurez une référence d’image de machine virtuelle, vous spécifiez les propriétés d’une image de machine virtuelle Marketplace. Les propriétés suivantes sont requises lorsque vous créez une référence d’image de machine virtuelle :

| **Propriétés de la référence image** | **Exemple** |
| ----------------- | ------------------------ |
| Publisher         | Canonique                |
| Offre             | UbuntuServer             |
| POINT DE STOCK               | 14.04.4-LTS              |
| Version           | dernière version                   |

> [AZURE.TIP] Pour plus d’informations sur ces propriétés et explique comment la liste images Marketplace de [Naviguer et sélectionner les images machine virtuelle Linux dans Azure avec l’interface CLI ou PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Notez que pas toutes les images du marché sont actuellement compatibles avec le traitement par lots. Pour plus d’informations, voir [agent de nœud SKU](#node-agent-sku).

### <a name="node-agent-sku"></a>Agent de nœud SKU

L’agent de nœud de traitement par lots est un programme qui s’exécute sur chaque nœud dans le pool et fournit l’interface de commande et de contrôle entre le nœud et le service de traitement par lots. Il existe différentes implémentations de l’agent de nœud connu comme points de stock, pour différents systèmes d’exploitation. Essentiellement, lorsque vous créez une Configuration de Machine virtuelle, vous spécifiez tout d’abord la référence d’image de machine virtuelle, puis vous spécifiez l’agent à installer sur l’image de nœud. En général, chaque agent de nœud SKU est compatible avec plusieurs images de machine virtuelle. Voici quelques exemples de l’agent de nœud Références SKU :

* batch.Node.Ubuntu 14.04
* batch.Node.CentOS 7
* batch.Node.Windows amd64

> [AZURE.IMPORTANT] Pas de toutes les images des machines virtuelles qui sont disponibles sur le marché sont compatibles avec les agents de nœud lots actuellement disponibles. Vous devez utiliser les kits de développement de lot pour répertorier l’agent disponible nœud Références SKU et les images de machine virtuelle avec lesquels ils sont compatibles. Voir les [images de la liste de Machine virtuelle](#list-of-virtual-machine-images) plus loin dans cet article pour plus d’informations.

## <a name="create-a-linux-pool-batch-python"></a>Créer un pool de Linux : les Python de lot

L’extrait de code suivant montre comment utiliser la [Bibliothèque de Client Microsoft Azure par lots pour les Python] [ py_batch_package] pour créer un pool de serveurs d’Ubuntu nœuds de calcul. Vous trouverez la documentation de référence pour le module les Python de lot au [package de la azure.batch] [py_batch_docs] lors de la lecture de la documentation.

Cet extrait de code crée un [ImageReference] [ py_imagereference] explicitement et spécifie chacune de ses propriétés (éditeur, offre, SKU, version). Dans le code de production, toutefois, nous vous conseillons la [list_node_agent_skus] [ py_list_skus] méthode pour déterminer et sélectionner les combinaisons disponibles de SKU d’image et le nœud de l’agent lors de l’exécution.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Comme mentionné précédemment, il est recommandé au lieu de créer [ImageReference] [ py_imagereference] explicitement, vous utilisez la [list_node_agent_skus] de[ py_list_skus] méthode pour sélectionner de manière dynamique des combinaisons image agent/Marketplace nœuds actuellement pris en charge. L’extrait de code Python suivant montre l’utilisation de cette méthode.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Créer un pool de Linux : .NET de lot

L’extrait de code suivant montre comment utiliser le [Traitement par lots .NET] [ nuget_batch_net] bibliothèque client pour créer un pool de serveurs d’Ubuntu nœuds de calcul. Vous pouvez trouver la [documentation de référence de lot .NET] [ api_net] sur le site MSDN.

L’extrait de code suivant utilise le [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] méthode pour sélectionner dans la liste des actuellement prise en charge Marketplace image et nœud SKU combinaisons d’agents. Cette technique est souhaitable, car la liste des combinaisons prises en charge peut-être changer à tout moment. En règle générale, sont ajoutées les combinaisons prises en charge.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Bien que l’extrait de code précédent utilise le [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] méthode dynamiquement la liste et sélectionnez à partir de prise en charge d’image et nœud de combinaisons de SKU de l’agent (recommandés), vous pouvez également configurer un [ImageReference] [ net_imagereference] explicitement :

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Liste des images de machine virtuelle

Le tableau suivant répertorie les images de machine virtuelle de marché qui sont compatibles avec les agents de nœud lots disponibles lorsque cet article a été mis à jour. Il est important de noter que cette liste n’est pas définitive, car les images et les agents de nœud peuvent ajouter ou supprimer à tout moment. Nous conseillons de vos applications de traitement par lots et services toujours [list_node_agent_skus] [ py_list_skus] (Python) et [ListNodeAgentSkus] [ net_list_skus] (lot .NET) pour déterminer et sélectionnez les points de stock actuellement disponibles.

> [AZURE.WARNING] La liste suivante peut-être changer à tout moment. Toujours utiliser les méthodes de **l’agent de nœud de liste SKU** disponibles dans l’API par lots pour répertorier et puis sélectionnez à partir des machines virtuelles compatible et agent de nœud Références SKU lorsque vous exécutez le traitement par lots.

| **Publisher** | **Offre** | **Image de point de stock** | **Version** | **Agent de nœud ID du point de stock** |
| ------- | ------- | ------- | ------- | ------- |
| Canonique | UbuntuServer | 14.04.0-LTS | dernière version | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.1-LTS | dernière version | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.2-LTS | dernière version | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.3-LTS | dernière version | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.4-LTS | dernière version | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.5-LTS | dernière version | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 16.04.0-LTS | dernière version | batch.Node.Ubuntu 16.04 |
| Credativ | Debian | 8 | dernière version | batch.Node.Debian 8 |
| OpenLogic | CentOS | 7.0 | dernière version | batch.Node.CentOS 7 |
| OpenLogic | CentOS | 7.1 | dernière version | batch.Node.CentOS 7 |
| OpenLogic | HPC centOS | 7.1 | dernière version | batch.Node.CentOS 7 |
| OpenLogic | CentOS | 7.2 | dernière version | batch.Node.CentOS 7 |
| Oracle | Linux-Oracle | 7.0 | dernière version | batch.Node.CentOS 7 |
| SUSE | openSUSE | 13.2 | dernière version | batch.Node.OpenSuSE 13.2 |
| SUSE | bissextile openSUSE | 42.1 | dernière version | batch.Node.OpenSuSE 42.1 |
| SUSE | SLES-HPC | 12 | dernière version | batch.Node.OpenSuSE 42.1 |
| SUSE | SLES | 12-SP1 | dernière version | batch.Node.OpenSuSE 42.1 |
| annonces Microsoft | standard-données-science-vm | standard-données-science-vm | dernière version | batch.Node.Windows amd64 |
| annonces Microsoft | Linux-données-science-vm | linuxdsvm | dernière version | batch.Node.CentOS 7 |
| MicrosoftWindowsServer | Windows2003 Server Edition | 2008-R2-SP1 | dernière version | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | Windows2003 Server Edition | 2012-centre de données | dernière version | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | Windows2003 Server Edition | 2012 R2-centre de données | dernière version | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | Windows2003 Server Edition | Serveur Windows-Aperçu technique | dernière version | batch.Node.Windows amd64 |

## <a name="connect-to-linux-nodes"></a>Se connecter à des nœuds Linux

Lors du développement ou lors de la résolution des problèmes, vous pouvez s’avérer nécessaire pour vous connecter aux nœuds dans le pool. Contrairement aux nœuds de calcul Windows, vous ne pouvez pas utiliser le protocole RDP (Remote Desktop) pour se connecter à des nœuds de Linux. Au lieu de cela, le service de traitement par lots permet l’accès SSH sur chaque nœud de connexion à distance.

L’extrait de code Python suivant crée un utilisateur sur chaque nœud dans un pool, ce qui est requis pour la connexion à distance. Il imprime ensuite les informations de connexion SSH (secure shell) pour chaque nœud.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Voici un exemple de sortie pour le code précédent pour un pool qui contient quatre nœuds Linux :

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Notez qu’au lieu d’un mot de passe, vous pouvez spécifier une clé publique SSH lorsque vous créez un utilisateur sur un nœud. Dans le Kit de développement de Python, cela s’effectue en utilisant le paramètre **ssh_public_key** sur [ComputeNodeUser][py_computenodeuser]. Dans .NET, cela s’effectue à l’aide de la [ComputeNodeUser][net_computenodeuser]. [SshPublicKey] [net_ssh_key] propriété.

## <a name="pricing"></a>Tarification

Lot Azure est basé sur la technologie de Services en nuage Azure et d’Azure Virtual Machines. Le service de traitement par lots lui-même est proposé gratuitement, qui signifie que vous sont facturés uniquement pour les ressources de calcul que vos solutions de lot consomment. Lorsque vous choisissez la **Configuration des Services Cloud**, vous sera facturée en fonction de la [tarification des Services en nuage] [ cloud_services_pricing] structure. Lorsque vous choisissez la **Configuration de la Machine virtuelle**, vous sera facturée en fonction de la [tarification des Machines virtuelles] [ vm_pricing] structure.

## <a name="next-steps"></a>Étapes suivantes

### <a name="batch-python-tutorial"></a>Didacticiel de Python de lot

Pour un didacticiel plus détaillé sur la façon de travailler avec le traitement par lots en utilisant les Python, consultez [mise en route avec le client Azure lot Python](batch-python-tutorial.md). Son associé [des exemples de code] [ github_samples_pyclient] inclut une fonction d’assistance, `get_vm_config_for_distro`, qui montre une autre technique pour obtenir une configuration d’ordinateur virtuel.

### <a name="batch-python-code-samples"></a>Exemples de code Python de lot

Consultez les autres [exemples de code Python] [ github_samples_py] dans les [exemples de traitement par lots azure] [ github_samples] référentiel sur GitHub de plusieurs scripts qui vous montrent comment effectuer les opérations courantes du lot comme pool, de travaux et de création de tâche. Le [fichier README] [ github_py_readme] qui accompagne les Python exemples a plus d’informations sur la façon d’installer les packages requis.

### <a name="batch-forum"></a>Forum du lot

Le [Forum du lot Azure] [ forum] sur le site MSDN est l’endroit idéal pour discuter des lots et de poser des questions sur le service. Lire les publications utiles « stickied » et publiez vos questions, qu’ils surviennent alors que vous générez vos solutions de traitement par lots.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
