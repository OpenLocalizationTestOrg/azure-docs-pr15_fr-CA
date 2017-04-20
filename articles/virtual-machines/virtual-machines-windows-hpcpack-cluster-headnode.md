<properties
 pageTitle="Créer un nœud principal HPC Pack dans un ordinateur virtuel d’Azure | Microsoft Azure"
 description="Apprenez à utiliser le portail Azure et le modèle de déploiement du Gestionnaire de ressources pour créer un nœud de tête Microsoft HPC Pack dans un ordinateur virtuel d’Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Créer le nœud principal d’un cluster HPC Pack dans un ordinateur virtuel d’Azure avec une image de la Marketplace


Utiliser une [image de machine virtuelle Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) à partir de l’Azure Marketplace et le portail Azure pour créer le nœud principal d’un cluster HPC. Cette image de machine virtuelle de HPC Pack est basée sur Windows Server 2012 R2 Datacenter avec le HPC Pack 2012 R2 mise à jour 3 préinstallé. Utilisez ce nœud principal pour une preuve de déploiement du concept du Pack HPC dans Azure. Vous pouvez ensuite ajouter des nœuds de calcul au cluster pour exécuter des charges de travail HPC.



>[AZURE.TIP]Pour déployer un cluster HPC Pack complet dans Azure qui inclut le nœud de tête et les nœuds de calcul, nous vous recommandons d’utiliser une méthode automatisée. Les options incluent le [script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) et le modèle de gestionnaire de ressources du [cluster HPC Pack pour les charges de travail de Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) . Des modèles supplémentaires, reportez-vous à la section [options de cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-options.md) . 


## <a name="planning-considerations"></a>Considérations sur la planification

Comme illustré dans la figure suivante, vous déployez le HPC Pack de nœud principal dans un domaine Active Directory dans un réseau virtuel Azure.

![Nœud de tête HPC Pack][headnode]

* **Domaine active Directory** - le HPC Pack nœud de tête doit être joints à un domaine Active Directory dans Azure avant de démarrer les services HPC sur la machine virtuelle. Comme indiqué dans cet article, une preuve de déploiement du concept, vous pouvez promouvoir l’ordinateur virtuel que vous créez pour le nœud principal en tant que contrôleur de domaine avant de démarrer les services HPC. Une autre option consiste à déployer un contrôleur de domaine distincts et des forêts dans Azure auquel vous participez à la machine virtuelle de nœud principal.

* **Azure réseau virtuel** - lorsque vous utilisez le modèle de déploiement du Gestionnaire de ressources pour déployer le nœud de tête, de spécifier ou de créer un réseau virtuel Azure. Vous utilisez le réseau virtuel si vous avez besoin joindre le nœud principal à un domaine Active Directory existant. Vous devez également ultérieurement pour ajouter le nœud de calcul VMs au cluster.

    
## <a name="steps-to-create-the-head-node"></a>Étapes pour créer le nœud de tête

Voici les étapes principales pour utiliser le portail Azure pour créer un ordinateur virtuel d’Azure pour le nœud principal HPC Pack en utilisant le modèle de déploiement du Gestionnaire de ressources. 


1. Si vous souhaitez créer une nouvelle forêt Active Directory dans Azure avec le contrôleur de domaine de différents ordinateurs virtuels, une seule option est d’utiliser un [modèle de gestionnaire de ressources](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Pour une simple validation du déploiement du concept, il convient omettre cette étape et configurer le nœud principal VM lui-même comme contrôleur de domaine. Cette option est décrite plus loin dans cet article.
    
2. Le [HPC Pack 2012 R2 sur la page Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) sur le marché d’Azure, cliquez sur **créer un ordinateur virtuel**. 

3. Dans le portail, dans la page **HPC Pack 2012 R2 de Windows Server 2012 R2** , sélectionnez le modèle de déploiement du **Gestionnaire de ressources** et puis cliquez sur **créer**.

    ![Image de HPC Pack][marketplace]

4. Le portail permet de configurer les paramètres et créer la machine virtuelle. Si vous êtes novice dans Azure, suivez le didacticiel [créer un ordinateur virtuel de Windows Azure portail](virtual-machines-windows-hero-tutorial.md). Pour une preuve de déploiement du concept, vous pouvez accepter habituellement la valeur par défaut ou des paramètres recommandés.

    >[AZURE.NOTE]Si vous souhaitez joindre un domaine Active Directory existant dans Azure le nœud de tête, assurez-vous que vous spécifiez lors de la création de la machine virtuelle du réseau virtuel pour ce domaine.
       
4. Après avoir créé l’ordinateur virtuel et l’ordinateur virtuel s’exécute, de [se connecter à la machine virtuelle](virtual-machines-windows-connect-logon.md) par bureau à distance. 

5. Participez à la machine virtuelle vers une forêt de domaine existante, ou créer une forêt de domaine sur l’ordinateur virtuel lui-même.

    * Si vous avez créé l’ordinateur virtuel dans un réseau virtuel Azure avec une forêt de domaine existante, vous pouvez joindre la machine virtuelle à la forêt à l’aide des outils Gestionnaire de serveur ou de Windows PowerShell standard. Puis redémarrez.

    * Si vous avez créé l’ordinateur virtuel dans un nouveau réseau virtuel (sans une forêt de domaine existante), puis promouvoir la machine virtuelle comme un contrôleur de domaine. Utilisez les étapes standard pour installer et configurer le rôle des Services de domaine Active Directory sur le nœud principal. Pour obtenir la procédure détaillée, reportez-vous à la section [installation d’un nouveau Windows Server 2012 forêt Active Directory](https://technet.microsoft.com/library/jj574166.aspx).

5. Une fois que l’ordinateur virtuel est en cours d’exécution et est joint à une forêt Active Directory, démarrez les services HPC Pack comme suit :

    une barre d’outils. Se connecter à la machine virtuelle à l’aide d’un compte de domaine qui est membre du groupe Administrateurs local du nœud principal. Par exemple, utiliser le compte d’administrateur que vous configurez lorsque vous avez créé la machine virtuelle de nœud principal.

    b. Pour une configuration de nœud principal par défaut, démarrez Windows PowerShell en tant qu’administrateur et tapez le texte suivant :

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Elle peut prendre plusieurs minutes pour démarrer les services HPC Pack.

    Pour les options de configuration supplémentaires de nœuds principaux, tapez `get-help HPCHNPrepare.ps1`.


## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez maintenant travailler avec le nœud de tête du cluster HPC Pack. Par exemple, démarrer le Gestionnaire de Cluster HPC et compléter la [Liste de tâches de déploiement](https://technet.microsoft.com/library/jj884141.aspx).
* Si vous souhaitez augmenter le cluster de calcul capacité sur demande, d’ajouter des [nœuds de rafale Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) dans un service cloud. 

* Essayez d’exécuter un test de charge sur le cluster. Pour obtenir un exemple, consultez le HPC Pack [guide Mise en route](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png
