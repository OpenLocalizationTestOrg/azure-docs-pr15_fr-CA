<properties
 pageTitle="Gérer les nœuds de calcul du cluster HPC Pack | Microsoft Azure"
 description="En savoir plus sur les outils de script PowerShell pour ajouter, supprimer, démarrer et arrêter des nœuds de calcul cluster HPC Pack dans Azure"
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

# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Gérer le nombre et la disponibilité des nœuds de calcul dans un cluster HPC Pack dans Azure

Si vous avez créé un cluster HPC Pack dans Azure VM, vous souhaiterez peut-être comment ajouter, supprimer, démarrer (disposition) ou stop (annulation) un numéro de calcul nœud ordinateurs virtuels du cluster. Pour effectuer ces tâches, exécuter des scripts PowerShell d’Azure sont installés sur la machine virtuelle de nœud principal. Ces scripts vous aident à contrôler le nombre et la disponibilité de vos ressources de cluster HPC Pack afin que vous puissiez contrôler les coûts.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Conditions préalables

* **Cluster HPC Pack dans Azure VM** - créer un cluster HPC Pack dans le modèle de déploiement classique à l’aide d’au moins 1 mise à jour de HPC Pack 2012 R2. Par exemple, vous pouvez automatiser le déploiement à l’aide de l’image de HPC Pack VM en cours dans le marché Azure et un script PowerShell d’Azure. Pour des informations et les conditions préalables, voir [Création d’un Cluster à haute performance avec le script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

    Après le déploiement, recherchez le nœud scripts de gestion dans le CCP\_dossier bin accueil sur le nœud principal. Vous devez exécuter chacun des scripts en tant qu’administrateur.

* **Azure publier le certificat de gestion ou le fichier de paramètres** - vous devez effectuer une des actions suivantes sur le nœud de tête :

    * **Importer l’Azure publier le fichier de paramètres**. Pour ce faire, exécutez les applets de commande PowerShell de Azure suivantes sur le nœud principal :

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **Configurer le certificat de gestion Azure sur le nœud principal**. Si vous disposez du fichier .cer, importez-le dans le magasin de certificats CurrentUser\My, puis exécutez l’applet de commande PowerShell de Azure suivant pour votre environnement Azure (AzureCloud ou AzureChinaCloud) :

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Ajouter le nœud de calcul VMs

Ajouter des nœuds de calcul avec le script **Add-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Paramètres

* **ServiceName** - nom du nuage de ce nouveau nœud de calcul ordinateurs virtuels seront ajoutés au service.

* **ImageName** - Azure VM nom de l’image, qui peut être obtenu via la Azure portal classique ou Azure PowerShell applet de commande **Get-AzureVMImage**. L’image doit remplir les conditions suivantes :

    1. Un système d’exploitation de Windows doit être installé.

    2. HPC Pack doit être installé dans le rôle de nœud de calcul.

    3. L’image doit être une image privée dans la catégorie d’utilisateur, pas une image Azure VM publique.

* **Quantité** - nombre de nœud de calcul VMs à ajouter.

* **InstanceSize** - taille du nœud de calcul VMs.

* **DomainUserName** - nom d’utilisateur de domaine qui sera utilisé pour connecter les nouveaux ordinateurs virtuels au domaine.

* **DomainUserPassword** - mot de passe de l’utilisateur de domaine.

* **NodeNameSeries** (facultatif) - modèle d’affectation de noms pour les nœuds de calcul. Le format doit être &lt; *racine\_nom de*&gt;&lt;*Démarrer\_numéro*&gt;%. Par exemple, MyCN % 10 % signifie une série de noms de nœuds de calcul à partir de MyCN11. Si non spécifié, le script utilise le nœud configuré d’attribution de noms série du cluster HPC.

### <a name="example"></a>Exemple

L’exemple suivant ajoute un nœud de calcul de grande taille 20 ordinateurs virtuels dans le nuage service *hpcservice1*, en fonction de l' image de machine virtuelle *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Supprimer le nœud de calcul VMs

Supprimer des nœuds de calcul avec le script de **Suppression-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Paramètres

* **Nom** - nom des nœuds de cluster à supprimer. Les caractères génériques sont pris en charge. Le nom du jeu de paramètre est un nom. Vous ne pouvez pas spécifier à la fois le **nom** et le **nœud** de paramètres.

* **Nœud** - HpcNode de l’objet pour les nœuds à supprimer, qui peut être obtenu par le biais de l’applet de commande PowerShell de HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètre est un nœud. Vous ne pouvez pas spécifier à la fois le **nom** et le **nœud** de paramètres.

* **DeleteVHD** (facultatif) - à supprimer les disques associés pour les ordinateurs virtuels qui sont supprimés.

* **Force** (facultatif) - paramètre pour forcer les nœuds HPC en mode hors connexion avant de les supprimer.

* **Confirmer** (facultatif) - demander confirmation avant d’exécuter la commande.

* **WhatIf** - en définissant pour décrire ce qui se passerait si vous avez exécuté la commande sans exécuter la commande.

### <a name="example"></a>Exemple

L’exemple suivant force les nœuds hors connexion dont les noms commencent *HPCNode-CN -* et les supprime les nœuds et leurs disques associés.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Nœud de calcul début VMs

Démarrez les nœuds de calcul avec le script de **Début-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Paramètres

* **Nom** : les noms des nœuds du cluster doit être démarré. Les caractères génériques sont pris en charge. Le nom du jeu de paramètre est un nom. Vous ne pouvez pas spécifier à la fois le **nom** et le **nœud** de paramètres.

* **Nœud**- HpcNode de l’objet pour les nœuds à démarrer, qui peut être obtenu par le biais de l’applet de commande PowerShell de HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètre est un nœud. Vous ne pouvez pas spécifier à la fois le **nom** et le **nœud** de paramètres.

### <a name="example"></a>Exemple

L’exemple suivant démarre les nœuds dont les noms commencent *HPCNode-CN -*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Arrêtez le nœud de calcul VMs

Arrêter les nœuds de calcul avec le script **d’Arrêt-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Syntaxe

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Paramètres


* **Nom**: les noms des nœuds du cluster d’être arrêté. Les caractères génériques sont pris en charge. Le nom du jeu de paramètre est un nom. Vous ne pouvez pas spécifier à la fois le **nom** et le **nœud** de paramètres.

* **Nœud** - HpcNode de l’objet pour les nœuds à arrêter, qui peut être obtenu par le biais de l’applet de commande PowerShell de HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Le nom du jeu de paramètre est un nœud. Vous ne pouvez pas spécifier à la fois le **nom** et le **nœud** de paramètres.

* **Force** (facultatif) - paramètre pour forcer les nœuds HPC en mode hors connexion avant d’arrêter leur.

### <a name="example"></a>Exemple

L’exemple suivant force les nœuds hors connexion dont les noms commencent *HPCNode-CN -* , puis s’arrête les nœuds.

Stop-HPCIaaSNode.ps1 – nom de HPCNodeCN-*-Force

## <a name="next-steps"></a>Étapes suivantes

* Si vous souhaitez augmenter ou réduire les nœuds de cluster en fonction de la charge de travail actuelle des travaux et des tâches sur le cluster automatiquement, reportez-vous à [croître automatiquement et de réduire les ressources de cluster HPC Pack dans Azure en fonction de la charge de travail du cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).
