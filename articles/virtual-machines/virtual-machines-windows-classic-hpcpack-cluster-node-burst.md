<properties
 pageTitle="Ajouter des nœuds de rafales à un cluster HPC Pack | Microsoft Azure"
 description="Découvrez comment développer un cluster HPC Pack dans Azure à la demande en ajoutant des instances de rôle de travail s’exécutant dans un service cloud"
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
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Ajouter des nœuds de « burst » à la demande à un cluster HPC Pack dans Azure



Si vous configurez un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) dans Azure, vous souhaiterez peut-être permet d’évoluer rapidement la capacité du cluster vers le haut ou vers le bas, sans conserver un ensemble de nœud de calcul préconfigurés VMs. Cet article vous montre comment ajouter des nœuds à la demande « burst » (instances de rôle de travail s’exécutant dans un service cloud) en tant que ressources de calcul à un nœud de tête dans Azure. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![Nœuds en rafale][burst]

Les étapes décrites dans cet article vous permettent d’ajouter des nœuds Azure rapidement à un nuage HPC Pack nœud de tête VM pour un test ou le déploiement de la preuve de concept. Les principales étapes sont les mêmes que les étapes pour « burst pour Azure » pour ajouter la capacité à un cluster HPC Pack de locaux de calcul cloud. Pour un didacticiel, reportez-vous à [configurer un hybride compute cluster avec Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Pour des instructions détaillées et des considérations pour les déploiements de production, consultez [rafale vers Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).


## <a name="prerequisites"></a>Conditions préalables

* Le **nœud de tête HPC Pack est déployé dans un ordinateur virtuel d’Azure** - vous pouvez utiliser un machine virtuelle de nœud principal autonome ou une partie d’un cluster de plus grand. Pour créer un nœud principal autonome, voir [déploiement d’un nœud de tête du Pack HPC dans un ordinateur virtuel d’Azure](virtual-machines-windows-hpcpack-cluster-headnode.md). Pour les options de déploiement de clusters HPC Pack automatisées, voir [Options pour créer et gérer un cluster de Windows HPC dans Azure avec Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

    >[AZURE.TIP] Si vous utilisez le [script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour créer le cluster dans Azure, vous pouvez inclure les nœuds de rafale Azure dans votre déploiement automatisé. Consultez les exemples dans cet article.

* **Abonnement azure** - pour ajouter des nœuds Azure, vous pouvez choisir l’abonnement même utilisé pour déployer l’ordinateur virtuel, le nœud de tête ou un autre abonnement (ou abonnements).

* **Quota de cœurs** - vous devrez peut-être augmenter le quota de cœurs, surtout si vous choisissez de déployer plusieurs nœuds Azure avec des tailles de plusieurs cœurs. Pour augmenter le quota, [Ouvrez une demande de support client en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sans frais.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Étape 1 : Créer un service cloud et un compte de stockage pour les nœuds Azure

Utiliser le portail classique Azure ou des outils équivalents pour configurer les ressources suivantes qui sont nécessaires pour déploiement les nœuds Azure :

* Un nouveau service en nuage Azure
* Un nouveau compte de stockage Azure

>[AZURE.NOTE] Ne pas réutiliser un service cloud existant dans votre abonnement. 

**Considérations relatives à la**

* Configurer un service en nuage distinct pour chaque modèle de nœud Azure que vous prévoyez de créer. Toutefois, vous pouvez utiliser le même compte de stockage pour plusieurs modèles de nœud.

* Nous vous recommandons de localiser le service cloud et le compte de stockage pour le déploiement dans la même région d’Azure.




## <a name="step-2-configure-an-azure-management-certificate"></a>Étape 2 : Configurer un certificat de gestion Azure

Pour ajouter des nœuds Azure en tant que ressources de calcul, vous avez besoin d’un certificat de gestion sur le nœud principal et téléchargement correspondant à l’abonnement Azure utilisé pour le déploiement de certificats.

Dans ce scénario, vous pouvez choisir **Par défaut le certificat de gestion de Azure HPC** HPC Pack installe et configure automatiquement sur le nœud de tête. Ce certificat est utile pour tester les fins et les déploiements de la preuve de concept. Pour utiliser ce certificat, télécharger le C:\Program Files\Microsoft HPC Pack de fichier 2012\Bin\hpccert.cer à partir de la machine virtuelle de nœud principal de l’abonnement. Pour télécharger le certificat dans [Azure portal classique](https://manage.windowsazure.com), cliquez sur **paramètres** > **Gestion des certificats**.

Pour obtenir des options supplémentaires configurer le certificat de gestion, voir [scénarios pour configurer le certificat de gestion Azure pour les déploiements de rafales d’Azure](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Étape 3 : Déployer Azure nœuds au cluster



Les étapes pour ajouter et démarrer de nœuds Azure dans ce scénario sont généralement les mêmes que les étapes avec un nœud de tête sur site. Pour plus d’informations, consultez les sections suivantes dans les [étapes à suivre pour déployer les nœuds Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Créer un modèle de nœud Azure

* Ajouter des nœuds Azure au cluster Windows HPC

* Démarrer les nœuds (disposition) l’Azure

Une fois que vous ajoutez et que vous démarrez les nœuds, ils sont prêts à utiliser pour exécuter des tâches de cluster.

Si vous rencontrez des problèmes lors du déploiement de nœuds Azure, consultez [Résoudre les problèmes de déploiements de nœuds Azure avec Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser une taille d’instance de calcul intensif pour les nœuds de transmission en rafale, consultez les considérations de [sur H-series et calcul intensif VM d’une série](virtual-machines-windows-a8-a9-a10-a11-specs.md).

* Si vous souhaitez augmenter ou réduire les ressources en fonction de la charge de travail du cluster Azure automatiquement, reportez-vous à [croître automatiquement et de réduire les ressources de calcul Azure dans un cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png
