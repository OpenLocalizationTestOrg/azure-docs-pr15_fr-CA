<properties
   pageTitle="MATLAB des clusters de machines virtuelles | Microsoft Azure"
   description="Utilisez des ordinateurs virtuels Microsoft Azure pour créer des clusters serveur MATLAB Distributed Computing pour exécuter vos charges de travail intensives parallèles MATLAB"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mscurrell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Windows"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="markscu"/>

# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Créer des clusters de MATLAB Distributed Computing Server sur Azure VM 

Ordinateurs virtuels Microsoft Azure permet de créer un ou plusieurs clusters de serveur MATLAB Distributed Computing pour exécuter vos charges de travail intensives parallèles MATLAB. Installez votre logiciel MATLAB Distributed Computing Server sur un ordinateur virtuel à utiliser comme une image de base et utiliser un script d’Azure PowerShell (disponible sur [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) ou un modèle de quickstart Azure à déployer et à gérer le cluster. Après le déploiement, connectez-vous au cluster pour exécuter vos charges de travail. 

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>À propos de MATLAB et serveur MATLAB distribué Computing 

La plate-forme [MATLAB](http://www.mathworks.com/products/matlab/) est optimisée pour la résolution des problèmes techniques et scientifiques. Les utilisateurs MATLAB avec simulations à grande échelle et les tâches de traitement de données peuvent utiliser parallèle MathWorks produits d’informatique pour accélérer leurs charges de travail intensives en tirant parti des services de la grille et de clusters de calcul. [Boîte à outils informatique parallèle](http://www.mathworks.com/products/parallel-computing/) permet aux utilisateurs MATLAB de paralléliser les applications et tirer parti des processeurs multicœur, GPU, clusters de calcul. [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/) permet aux utilisateurs MATLAB utiliser plusieurs ordinateurs dans un cluster de calcul. 


En utilisant des machines virtuelles Azure, vous pouvez créer des clusters de MATLAB Distributed Computing Server qui ont les mêmes mécanismes disponibles pour soumettre un travail en parallèle en tant que clusters locaux, tels que les tâches interactives, traitements par lots, tâches indépendantes et tâches de communication. À l’aide d’Azure conjointement avec la plate-forme MATLAB comporte de nombreux avantages par rapport à la mise en service et de l’aide classique matériel sur site : tailles d’une plage de la machine virtuelle, création de clusters à la demande et de payer uniquement pour les ressources de calcul vous utilisez et possibilité de tester les modèles à grande échelle.  

## <a name="prerequisites"></a>Conditions préalables

* **Ordinateur client** , vous aurez besoin d’un ordinateur client Windows de communiquer avec Azure et le cluster de MATLAB Distributed Computing serveur après le déploiement. 

* **Azure PowerShell** - voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour l’installer sur votre ordinateur client. 

* **Abonnement azure** - si vous n’êtes pas abonné, vous pouvez créer un [compte de libérer](https://azure.microsoft.com/free/) dans quelques minutes. Pour les clusters de grande taille, envisagez un abonnement de paiement ou d’autres options d’achat. 

* **Quota de cœurs** - vous devrez peut-être augmenter le quota de base pour déployer un cluster de grande taille ou plusieurs clusters de MATLAB Distributed Computing Server. Pour augmenter le quota, [Ouvrez une demande de support client en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sans frais. 

* **MATLAB, Parallel Computing Toolbox et licences MATLAB Distributed Computing Server** - les scripts supposent que le [Gestionnaire de licences MathWorks hébergé](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) est utilisé pour toutes les licences.  

* **Logiciel MATLAB Distributed Computing Server** - sera installé sur un ordinateur virtuel qui sera utilisé comme la base image de machine virtuelle pour l’ordinateurs virtuels du cluster. 


## <a name="high-level-steps"></a>Étapes de niveau élevés

Pour utiliser les ordinateurs virtuels Azure votre MATLAB Distributed Computing clusters de serveurs, les étapes suivantes sont nécessaires. Des instructions détaillées se trouvent dans la documentation qui accompagne les scripts et le modèle de démarrage rapide sur [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Créer une image de machine virtuelle de base**  
    * Téléchargez et installez le logiciel de MATLAB Distributed Computing Server sur cet ordinateur virtuel. 

    >[AZURE.NOTE]Ce processus peut prendre quelques heures, mais vous ne devez le faire une fois pour chaque version de MATLAB que vous utilisez.   
    
2. **Créer un ou plusieurs clusters**  
    * Utilisez le script PowerShell fourni ou le modèle de démarrage rapide pour créer un cluster à partir de l’image de machine virtuelle de base.   
    * Gérer les clusters à l’aide du script PowerShell fourni, qui permet de répertorier, suspendre, reprendre et supprimer les clusters. 
 
## <a name="cluster-configurations"></a>Configurations de cluster 

Actuellement, le modèle et le script de création de cluster permettent de créer une seule topologie de MATLAB Distributed Computing Server. Si vous le souhaitez, créer un ou plusieurs clusters supplémentaires, avec chaque cluster ayant un nombre différent de travail ordinateurs virtuels, à l’aide de différentes tailles de machine virtuelle et ainsi de suite. 

### <a name="matlab-client-and-cluster-in-azure"></a>Client MATLAB et cluster dans Azure 

Le MATLAB client nœuds, Planificateur de MATLAB et les nœuds « Travailleur » de MATLAB Distributed Computing Server sont tous configurés comme Azure VM dans un réseau virtuel, comme illustré dans la figure suivante. 

![Topologie de cluster](./media/virtual-machines-windows-matlab-mdcs-cluster/mdcs_cluster.png)

* Pour utiliser le cluster, connexion Bureau à distance vers le nœud client. Le nœud client exécute le client MATLAB. 

* Le nœud client a un partage de fichiers accessible par tous les travailleurs.

* MathWorks hébergé License Manager est utilisé pour les contrôles de licence pour tous les logiciels MATLAB. 

* Par défaut, un seul travailleur MATLAB Distributed Computing serveur par cœur est créé sur le travailleur ordinateurs virtuels, mais vous pouvez spécifier n’importe quel nombre. 


## <a name="use-an-azure-based-cluster"></a>Utiliser un Cluster basé sur Azure 

Comme avec d’autres types de MATLAB Distributed Computing des clusters de serveurs, vous devez utiliser le Gestionnaire de profil de Cluster dans le client MATLAB (sur le client VM) pour créer un profil de cluster Planificateur de MATLAB.

![Gestionnaire du cluster de profil](./media/virtual-machines-windows-matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des instructions détaillées déployer et gérer des clusters de MATLAB Distributed Computing Server dans Azure, consultez le référentiel [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) contenant les modèles et les scripts. 

* Accédez au [site de MathWorks](http://www.mathworks.com/) pour obtenir une documentation détaillée de MATLAB et MATLAB Distributed Computing Server.
