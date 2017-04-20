<properties
 pageTitle="Les nœuds de cluster AutoScale HPC Pack | Microsoft Azure"
 description="Développer et réduire le nombre de nœuds de compute cluster HPC Pack dans Azure automatiquement"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Développer et réduire les ressources de cluster HPC Pack dans Azure en fonction de la charge de travail du cluster automatiquement




Si vous déployez de nœuds de « Burst » Azure dans votre cluster HPC Pack ou si vous créez un cluster HPC Pack dans Azure VM, vous souhaiterez un moyen d’augmenter ou de réduire le nombre de ressources de calcul Azure comme nœuds ou cœurs en fonction de la charge de travail en cours sur le cluster automatiquement. Cela vous permet d’utiliser plus efficacement vos ressources Azure et contrôler leurs coûts.
Pour ce faire, définissez la propriété de cluster HPC Pack **AutoGrowShrink**. Vous pouvez également exécuter le script PowerShell de HPC **AzureAutoGrowShrink.ps1** qui est installé avec le HPC Pack.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]En outre, actuellement vous pouvez uniquement automatiquement augmenter et réduire les nœuds de calcul HPC Pack qui exécutent un système d’exploitation Windows.

## <a name="set-the-autogrowshrink-cluster-property"></a>Affectez à la propriété de cluster AutoGrowShrink

### <a name="prerequisites"></a>Conditions préalables

* Le **HPC Pack 2012 R2 mise à jour 2 ou version ultérieure cluster** - le nœud de tête du cluster peut être déployé sur site ou dans un ordinateur virtuel d’Azure. Consultez [configurer un cluster hybride avec le HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) pour démarrer avec un nœud de tête sur site et des nœuds de rafale « Azure ». Consultez le [script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) rapidement déployer un cluster HPC Pack dans Azure VM.


* **Pour un cluster avec un nœud de tête dans Azure** - si vous utilisez le script de déploiement des HPC Pack IaaS pour créer le cluster, activez la propriété de cluster de **AutoGrowShrink** en définissant l’option AutoGrowShrink dans le fichier de configuration de cluster. Pour plus d’informations, consultez la documentation qui accompagne le [téléchargement du script](https://www.microsoft.com/download/details.aspx?id=44949). 

    Vous pouvez également activer la propriété de cluster **AutoGrowShrink** après le déploiement du cluster à l’aide de commandes de PowerShell de HPC décrites dans la section suivante. Pour préparer ce, procédez tout d’abord comme suit :
    1. Configurer un certificat de gestion Azure sur le nœud principal et dans l’abonnement Azure. Pour un déploiement de test, vous pouvez utiliser le certificat auto-signé par défaut Microsoft HPC Azure HPC Pack installe sur le nœud de tête et téléchargez ce certificat à votre abonnement Azure. Pour des étapes et des options, consultez le [Guide de la bibliothèque TechNet](https://technet.microsoft.com/library/gg481759.aspx).
    2. Exécutez **regedit** sur le nœud principal, accédez à HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo et ajouter une nouvelle valeur de chaîne. Définissez la valeur à « Empreinte numérique » et les données de l’empreinte numérique du certificat à l’étape 1 de la valeur.


### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Commandes PowerShell de HPC pour définir la propriété AutoGrowShrink

Suivantes sont un exemple de commandes PowerShell HPC pour définir **AutoGrowShrink** et ajuster son comportement avec des paramètres supplémentaires. Plus loin dans cet article pour obtenir la liste complète des paramètres, reportez-vous à la section [paramètres de AutoGrowShrink](#AutoGrowShrink-parameters) . 

Pour exécuter ces commandes, démarrer PowerShell de HPC sur le nœud principal du cluster en tant qu’administrateur.

**Pour activer la propriété AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Pour désactiver la propriété de AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Pour modifier l’intervalle de croissance en quelques minutes**

    Set-HpcClusterProperty –GrowInterval <interval>

**Pour modifier l’intervalle de réduction en quelques minutes**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Pour afficher la configuration en cours de AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### <a name="autogrowshrink-parameters"></a>Paramètres de AutoGrowShrink

Voici les paramètres AutoGrowShrink que vous pouvez modifier à l’aide de la commande **Set-HpcClusterProperty** .

* **EnableGrowShrink** - commutateur pour activer ou désactiver la propriété **AutoGrowShrink** .
* **ParamSweepTasksPerCore** - nombre de tâches de nettoyage paramétrique à évoluer d’un seul cœur. La valeur par défaut est d’augmenter d’un seul cœur par tâche. 
 
    >[AZURE.NOTE] HPC Pack QFE KB3134307 devient **ParamSweepTasksPerCore** **TasksPerResourceUnit**. Il est basé sur le type de ressource de travail et peut être un nœud, socket ou core.
    
* **GrowThreshold** - seuil de tâches en file d’attente pour déclencher la croissance automatique. La valeur par défaut est 1, ce qui signifie que s’il y a 1 ou plusieurs tâches dans l’état en file d’attente, automatiquement développer les noeuds.
* **GrowInterval** - intervalle en minutes pour déclencher la croissance automatique. L’intervalle par défaut est de 5 minutes.
* **ShrinkInterval** - intervalle en minutes pour déclencher la réduction automatique. L’intervalle par défaut est de 5 minutes. |
* **ShrinkIdleTimes** - nombre de chèques continues à indiquer que les nœuds sont inactifs. La valeur par défaut est 3 fois. Par exemple, si la **ShrinkInterval** est de 5 minutes, HPC Pack vérifie si le nœud est inactif toutes les 5 minutes. Si les nœuds sont dans l’état d’inactivité après que 3 continue vérifie (15 minutes), puis le HPC Pack réduit ce nœud.
* **ExtraNodesGrowRatio** - pourcentage supplémentaire de nœuds d’augmenter pour les tâches de l’Interface MPI (Message Passing). La valeur par défaut est 1, ce qui signifie que HPC Pack augmente les nœuds 1 % pour les travaux MPI. 
* **GrowByMin** - commutateur pour indiquer si la stratégie de croissance automatique est basée sur les ressources minimales requises pour le projet. La valeur par défaut est false, ce qui signifie que HPC Pack augmente les nœuds pour les tâches basées sur les ressources maximales requises pour les tâches.
* **SoaJobGrowThreshold** - seuil de demandes entrantes de SOA à déclenchement automatique se développent des processus. La valeur par défaut est 50000.  
    
    >[AZURE.NOTE] Ce paramètre est pris en charge dans le HPC Pack 2012 R2 mise à jour 3.
    
* **SoaRequestsPerCore** -nombre de SOA entrant requêtes à un seul cœur de croître. La valeur par défaut est de 20 000.  

    >[AZURE.NOTE] Ce paramètre est pris en charge dans le HPC Pack 2012 R2 mise à jour 3.

### <a name="mpi-example"></a>Exemple MPI

Par défaut HPC Pack augmente de 1 % de nœuds supplémentaires pour les tâches MPI (**ExtraNodesGrowRatio** est définie à 1). La raison est que les MPI peut nécessiter plusieurs nœuds, et la tâche ne peut être exécutée que lorsque tous les nœuds sont prêts. Azure démarrage de nœuds, un nœud peut besoin plus de temps à démarrer que d’autres, à l’origine d’autres nœuds soit inactif en attendant que ce nœud pour vous préparer. Par croissance de nœuds supplémentaires, HPC Pack réduit le temps d’attente de cette ressource et potentiellement enregistre les coûts. Pour augmenter le pourcentage de nœuds supplémentaires pour les tâches MPI (par exemple, pour 10 %), exécutez une commande semblable à

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Exemple SOA

Par défaut, **SoaJobGrowThreshold** est la valeur 50000 et **SoaRequestsPerCore** a la valeur 200000. Si vous soumettez un travail SOA avec 70000 demandes, il y a une tâche en file d’attente et les requêtes entrantes sont 70000. Dans ce cas s’agrandit le HPC Pack 1 noyau de la tâche en file d’attente et pour les demandes entrantes, augmentera (70000-50000) / base de 20000 = 1, donc augmentera au total 2 noyaux pour ce projet SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Exécutez le script AzureAutoGrowShrink.ps1

### <a name="prerequisites"></a>Conditions préalables

* **1 mise à jour de HPC Pack 2012 R2 ou ultérieur cluster** - le script **AzureAutoGrowShrink.ps1** est installé dans le dossier bin de % CCP_HOME %. Le nœud principal de cluster peut être déployé sur site ou dans un ordinateur virtuel d’Azure. Consultez [configurer un cluster hybride avec le HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) pour démarrer avec un nœud de tête sur site et des nœuds de rafale « Azure ». Consultez le [script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) rapidement déployer un cluster HPC Pack dans Azure VM, ou utiliser un [modèle de quickstart Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12** - actuellement le script dépend de cette version spécifique de PowerShell d’Azure. Si vous exécutez une version ultérieure sur le nœud principal, vous devrez éventuellement déclasser Azure PowerShell [version 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) pour exécuter le script. 

* **Pour un cluster avec des noeuds de rafale Azure** - exécuter le script sur un ordinateur client où HPC Pack est installé, ou sur le nœud principal. Si vous exécutez sur un ordinateur client, vous assurer que la variable $env : CCP_SCHEDULER correctement afin de pointer vers le nœud de tête. Les nœuds « Burst » Azure doivent déjà être ajoutés au cluster, mais ils peuvent être dans l’état n’est pas déployé.


* **Pour un cluster déployé dans Azure VM** - exécuter le script sur le nœud principal de machine virtuelle, car il dépend de la **HpcIaaSNode.ps1-démarrer** et **Arrêter-HpcIaaSNode.ps1** des scripts qui y sont installés. En outre, ces scripts nécessitent un certificat de gestion Azure ou publier le fichier de paramètres (voir [Gestion des nœuds dans un cluster HPC Pack dans Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Assurez-vous que les ordinateurs virtuels dont vous avez besoin du nœud de calcul ont déjà été ajoutés au cluster. Ils peuvent être à l’état arrêté.

### <a name="syntax"></a>Syntaxe

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### <a name="parameters"></a>Paramètres

 * **NodeTemplates** - les noms des modèles de nœud pour définir l’étendue pour les nœuds à croître et à diminuer. Si non spécifié (la valeur par défaut est @()), tous les nœuds dans le groupe de nœud **AzureNodes** sont dans la portée lorsque **NodeType** a la valeur AzureNodes, et tous les nœuds dans le groupe de nœud **ComputeNodes** sont dans la portée lorsque **NodeType** a la valeur ComputeNodes.

 * **JobTemplates** - les noms des modèles de tâche pour définir l’étendue pour les nœuds de croître.

 * **NodeType** - le type de nœud à croître et à diminuer. Les valeurs prises en charge sont :

     * **AzureNodes** – pour les nœuds d’Azure PaaS (burst) dans un local ou un cluster d’Azure IaaS.

     * **ComputeNodes** - pour le nœud de calcul ordinateurs virtuels uniquement dans un cluster Azure IaaS.

* **NumOfQueuedJobsPerNodeToGrow** - nombre de travaux en file d’attente requis pour développer un nœud.

* **NumOfQueuedJobsToGrowThreshold** - le nombre de travaux en file d’attente pour démarrer le processus de croissance.

* **NumOfActiveQueuedTasksPerNodeToGrow** - le nombre de tâches de file d’attente actives requis pour développer un nœud. Si **NumOfQueuedJobsPerNodeToGrow** est spécifié avec une valeur supérieure à 0, ce paramètre est ignoré.

* **NumOfActiveQueuedTasksToGrowThreshold** - le nombre de tâches en file d’attente en cours pour démarrer le processus de croissance.

* **NumOfInitialNodesToGrow** - le nombre initial de nœuds à croître si tous les nœuds dans la portée ne sont **Pas déployées** ou **arrêté (Deallocated)**.

* **GrowCheckIntervalMins** - l’intervalle en minutes entre les vérifications de croître.

* **ShrinkCheckIntervalMins** - l’intervalle en minutes entre les vérifications de réduction.

* **ShrinkCheckIdleTimes** - le nombre de continue la réduction chèques (séparés par **ShrinkCheckIntervalMins**) pour indiquer les nœuds sont inactifs.

* **UseLastConfigurations** - les configurations précédentes enregistrées dans le fichier de l’argument.

* **ArgFile**- le nom du fichier argument permet d’enregistrer et de mettre à jour les configurations pour exécuter le script.

* **Préfixe_du_fichier_journal** - le nom de préfixe du fichier journal. Vous pouvez spécifier un chemin d’accès. Par défaut, le journal est écrit dans le répertoire de travail actuel.

### <a name="example-1"></a>Exemple 1

L’exemple suivant configure les nœuds en rafale Azure déployés avec le modèle par défaut de AzureNode à croître et à diminuer automatiquement. Si tous les nœuds sont initialement dans un état **Non déployée** , au moins 3 nœuds sont démarrés. Si le nombre de travaux en file d’attente dépasse 8, le script démarre les nœuds jusqu'à ce que leur nombre dépasse le taux de travaux en attente à **NumOfQueuedJobsPerNodeToGrow**. Si un nœud est inactif dans les 3 heures d’inactivité consécutives, elle est arrêtée.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Exemple 2

L’exemple suivant configure le nœud de calcul Azure ordinateurs virtuels déployés avec le modèle par défaut de ComputeNode à croître et à diminuer automatiquement.
Les tâches configurées par le modèle de tâche par défaut définissent l’étendue de la charge de travail sur le cluster. Si tous les nœuds sont initialement arrêtés, au moins 5 nœuds sont démarrés. Si le nombre de tâches en cours en file d’attente est supérieur à 15, le script démarre les nœuds jusqu'à ce que leur nombre dépasse le taux de tâches en file d’attente actives **NumOfActiveQueuedTasksPerNodeToGrow**. Si le temps d’inactivité de 10 heures d’inactivité consécutifs, un nœud est trouvé, il est arrêté.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
