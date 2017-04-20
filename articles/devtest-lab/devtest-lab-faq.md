<properties
    pageTitle="DevTest Azure Labs FAQ | Microsoft Azure"
    description="Trouvez les réponses aux questions les plus fréquentes Azure DevTest Labs"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="tarcher"/>

# <a name="azure-devtest-labs-faq"></a>Forum aux questions sur les laboratoires DevTest Azure

Cet article répond aux questions les plus fréquemment posées sur Azure DevTest Labs.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Général
- [Que se passe-t-il si n’est pas répondu à ma question ici ?](#what-if-my-question-isnt-answered-here)
- [Pourquoi dois-je utiliser des laboratoires DevTest de Azure ?](#why-should-i-use-azure-devtest-labs) 
- [Que signifie « sérénité, libre service » ?](#what-does-quotworry-free-self-servicequot-mean)
- [Comment puis-je utiliser les Azure DevTest Labs ?](#how-can-i-use-azure-devtest-labs) 
- [Mode de facturation pour Azure DevTest Labs ?](#how-am-i-billed-for-azure-devtest-labs) 
 
## <a name="security"></a>Sécurité 
- [Quels sont les différents niveaux de sécurité dans les laboratoires DevTest Azure ?](#what-are-the-different-security-levels-in-azure-devtest-labs) 
- [Comment pour créer un rôle pour permettre aux utilisateurs d’effectuer une tâche spécifique ?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 
 
## <a name="cicd-integration--automation"></a>Automatisation et intégration des CI/CD 
 
- [Azure DevTest Labs s’intègre-t-il à mon toolchain CI/CD ?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 
 
## <a name="virtual-machines"></a>Ordinateurs virtuels 
 
- [Pourquoi ne puis-je pas voir certains ordinateurs virtuels de la lame d’ordinateurs virtuels Azure que dans Azure DevTest Labs ?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
- [Quelle est la différence entre des images personnalisées et les formules ?](#what-is-the-difference-between-custom-images-and-formulas) 
- [Comment pour créer plusieurs ordinateurs virtuels à partir du même modèle à la fois ?](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
- [Comment déplacer mes machines virtuelles Azure existantes dans mon laboratoire Azure DevTest Labs ?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
- [Puis-je attacher plusieurs disques à mes machines virtuelles ?](#can-i-attach-multiple-disks-to-my-vms) 
- [Comment pour automatiser le processus de téléchargement des fichiers de disque dur virtuel pour créer des images personnalisées ?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
- [Comment puis-je pour automatiser le processus de suppression de tous les ordinateurs virtuels dans mon laboratoire ?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)
 
## <a name="artifacts"></a>Artefacts 
 
- [Que sont les artefacts ?](#what-are-artifacts) 
 
## <a name="lab-configuration"></a>Configuration de l’atelier 
 
- [Comment créer un laboratoire à partir d’un modèle de gestionnaire de ressources Azure ?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
- [Pourquoi mes ordinateurs virtuels créés dans différents groupes de ressources portant des noms arbitraires ? Puis-je renommer ou modifier les groupes de ressources ?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
- [Laboratoires combien puis-je créer sous le même abonnement ?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Le nombre de machines virtuelles puis-je créer par le laboratoire ?](#how-many-vms-can-i-create-per-lab)
- [Comment faire pour partager un lien direct à mon atelier ?](#how-do-i-share-a-direct-link-to-my-lab)
- [Ce qu’est un compte Microsoft ?](#what-is-a-microsoft-account)
 
## <a name="troubleshooting"></a>Résolution des problèmes 
 
- [Mon objet a échoué lors de la création de la machine virtuelle. Comment le résoudre ?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
- [Pourquoi n’est pas mon réseau existant, virtuel enregistrer correctement ?](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isnt-answered-here"></a>Que se passe-t-il si n’est pas répondu à ma question ici ?
Si votre question n’est pas répertoriée ici, faites-le nous savoir et nous vous aiderons à trouver une réponse.

- Publier une question dans le [thread de Disqus](#comments) à la fin de cette FAQ et faites appel à l’équipe de Cache d’Azure et d’autres membres de la Communauté à propos de cet article.
- Pour atteindre un public plus large, poser une question sur le [forum MSDN de laboratoires DevTest Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)et de s’engager avec l’équipe des laboratoires DevTest Azure et les autres membres de la Communauté.
- Pour effectuer une demande de fonctionnalité, soumettez vos demandes et des idées pour la [Voix d’utilisateur Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>Pourquoi dois-je utiliser des laboratoires DevTest de Azure ? 
Les laboratoires DevTest Azure peut enregistrer votre temps de l’équipe et de l’argent. Les développeurs peuvent créer leurs propres environnements utilisant plusieurs bases différentes et utilisez des artefacts pour rapidement déployer et configurer des applications. À l’aide des formules et des images personnalisées, machines virtuelles peuvent être enregistrés en tant que modèles et reproduire facilement. En outre, les laboratoires offrent plusieurs règles configurables qui permettent aux administrateurs de laboratoire afin de réduire les déchets et de gérer des environnements d’une équipe. Ces stratégies incluent l’arrêt automatique, seuil de coût, maximum de machines virtuelles par utilisateur et la taille maximale de mémoire virtuelle. Pour une explication plus approfondie de Azure DevTest Labs, consultez la [vue d’ensemble](devtest-lab-overview.md) ou regardez la [vidéo d’introduction](/documentation/videos/videos/what-is-azure-devtest-labs). 

### <a name="what-does-worry-free-self-service-mean"></a>Que signifie « sérénité, libre service » ?
« Sérénité, libre-service » signifie que les développeurs et les testeurs de créent leurs propres environnements selon vos besoins, et les administrateurs ont la sécurité de savoir que les laboratoires DevTest Azure contribue à réduire les déchets et contrôler les coûts. Les administrateurs peuvent spécifier les tailles de la machine virtuelle sont autorisés, le nombre maximum de machines virtuelles, et lorsque les ordinateurs virtuels sont démarrés et arrêter. Les laboratoires DevTest Azure facilite également à surveiller les coûts et de définir des alertes pour une parfaite connaissance de l’utilisation des ressources de l’atelier. 

### <a name="how-can-i-use-azure-devtest-labs"></a>Comment puis-je utiliser les Azure DevTest Labs ? 
Les laboratoires DevTest Azure est utile à tout moment vous nécessitent dev ou environnements de test, et que vous souhaitez reproduire rapidement ou de les gérer avec des stratégies de réduction des coûts. 

Voici quelques scénarios qui nos clients utilisent les laboratoires DevTest de Azure pour : 

- Gestion des environnements de test et de développement dans un seul endroit, utilisant des stratégies pour réduire les coûts et partager des images personnalisées crée au sein de l’équipe.
- Développement d’une application à l’aide d’images personnalisées pour enregistrer l’état du disque dans toutes les étapes du développement.
- Suivi des coûts en fonction de la progression. 
- Création d’environnements de test de masse pour les tests d’assurance qualité.
- À l’aide des artefacts et des formules pour configurer facilement et de reproduire une application sous différents environnements. 
- Distribution des ordinateurs virtuels pour hackathons (travail de test ou de développement collaboratif) et ensuite facilement mise hors service les lorsque l’événement se termine. 

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Mode de facturation pour Azure DevTest Labs ? 
Azure DevTest Labs est un service gratuit, ce qui signifie que la création de laboratoires et configuration des stratégies, des modèles et des artefacts libre. Vous payez uniquement les ressources Azure utilisés dans vos laboratoires, tels que les ordinateurs virtuels, des comptes de stockage et des réseaux virtuels. Pour plus d’informations sur le coût des ressources de laboratoire, en savoir plus sur la [tarification d’Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/). 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Quels sont les différents niveaux de sécurité dans les laboratoires DevTest Azure ?  
Accès de sécurité est déterminée par le [Contrôle d’accès Azure Role-Based (RBAC)](../active-directory/role-based-access-built-in-roles.md). Pour comprendre le fonctionnement de l’accès, il est utile de comprendre les différences entre une étendue définie par RBAC, une autorisation et un rôle.

- **Autorisation** - une autorisation est un accès définie pour une action spécifique. Par exemple, une autorisation peut être machines d’accès en lecture à toutes virtuelles. 
- **Rôle** - un rôle est un ensemble d’autorisations qui peuvent être regroupées et affectées à un utilisateur. Par exemple, un « propriétaire » a accès à toutes les ressources au sein d’un abonnement. 
- **Portée** - une étendue est un niveau dans la hiérarchie des ressources Azure. Par exemple, une portée peut être un groupe de ressources ou un seul laboratoire ou tout l’abonnement. 
 
Dans le cadre d’ateliers de DevTest d’Azure, il existe deux types de rôles pour définir des autorisations utilisateur : propriétaire de laboratoire et de l’utilisateur de l’atelier.

- **Propriétaire de l’atelier** - propriétaire d’un atelier dispose de l’accès à toutes les ressources dans l’atelier. Par conséquent, ils peuvent modifier les stratégies, lire et écrire les ordinateurs virtuels, modifier le réseau virtuel et ainsi de suite. 
- **Utilisateur d’atelier** - atelier utilisateur puisse le consulter toutes les ressources de laboratoire, tels que les ordinateurs virtuels, des stratégies et des réseaux virtuels, mais ils ne peuvent pas modifier les stratégies ou les ordinateurs virtuels créés par d’autres utilisateurs. Il est également possible de créer des rôles personnalisés dans Azure DevTest Labs, et vous pouvez apprendre à faire dans l’article, [accorder des autorisations utilisateur pour les stratégies de laboratoire spécifique](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). 
 
Étant donné que les étendues sont hiérarchiques, si un utilisateur dispose des autorisations à une portée, elles sont automatiquement accordés ces autorisations à chaque portée de niveau inférieur englobé. Par exemple, si un utilisateur est affecté au rôle de propriétaire de l’abonnement, puis ils ont accès à toutes les ressources dans un abonnement. Ces ressources incluent tous les ordinateurs virtuels, tous les réseaux virtuels et tous les ateliers pratiques. Ainsi, un propriétaire de l’abonnement hérite automatiquement le rôle de propriétaire de l’atelier. Toutefois, l’inverse n’est pas vrai. Un propriétaire de laboratoire a accès à un laboratoire, qui est une étendue moindre que le niveau d’abonnement. Par conséquent, propriétaire d’un atelier n’est pas en mesure de voir les ordinateurs virtuels ou les réseaux virtuels ou toutes les ressources qui sont à l’extérieur de l’atelier. 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Comment pour créer un rôle pour permettre aux utilisateurs d’effectuer une tâche spécifique ?
Vous trouverez ici un article complet sur la façon de créer des rôles personnalisés et attribuer des autorisations à ce rôle. Voici un exemple d’un script qui crée le rôle « DevTest Labs avancée utilisateur », qui a l’autorisation de démarrer et d’arrêter tous les ordinateurs virtuels dans le laboratoire :
 
    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  
 
### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest Labs s’intègre-t-il à mon toolchain CI/CD ? 
Si vous utilisez VSTS, il existe une [extension des tâches de laboratoires DevTest Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) qui vous permet d’automatiser votre pipeline version dans Azure DevTest Labs. Voici quelques-unes des utilisations de cette extension :

- Création et déploiement automatique d’un ordinateur virtuel et configurez-le avec la dernière version à l’aide des tâches de copie de fichier Azure ou PowerShell VSTS. 
- Capturer automatiquement l’état d’un ordinateur virtuel après l’avoir testé pour reproduire un bogue sur le même VM pour examen ultérieur. 
- Suppression de la machine virtuelle à la fin du pipeline version lorsqu’il n’est plus nécessaire. 

Les billets de blog suivants fournissent des conseils et des informations sur l’utilisation de l’extension VSTS :
 
- [Azure DevTest Labs – extension VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
- [Déploiement d’une nouvelle machine virtuelle dans un AzureDevTestLab existant dans VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
- [À l’aide de la gestion des versions VSTS pour les déploiements continues à AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 
 
Pour les autres toolchains CI/CD, tous les scénarios mentionnés ci-dessus peuvent être obtenues par l’extension de tâches VSTS peuvent être obtenus de la même façon via le déploiement de [modèles de Azure le Gestionnaire de ressources](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) à l’aide des [applets de commande PowerShell de Azure](../resource-group-template-deploy.md) et [Kits de développement logiciel .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Vous pouvez également utiliser les [API de reste de DevTest Labs](http://aka.ms/dtlrestapis) pour s’intégrer à votre toolchain.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Pourquoi ne puis-je pas voir certains ordinateurs virtuels de la lame d’ordinateurs virtuels Azure que dans Azure DevTest Labs ?
Lorsqu’une machine virtuelle est créée dans Azure DevTest Labs, l’autorisation est donnée pour accéder à cet ordinateur virtuel. Vous ne pouvez l’afficher à la fois dans la lame de laboratoires et de la lame de **Machines virtuelles** . Les utilisateurs du rôle DevTest Labs peuvent voir toutes les machines virtuelles créées dans l’atelier, par le biais de lame de **tous les ordinateurs virtuels** de l’atelier. Toutefois, les utilisateurs du rôle DevTest Labs ne sont pas accordées automatiquement l’accès en lecture aux ressources de la machine virtuelle qu’ils ont créés. Par conséquent, ces ordinateurs virtuels ne sont pas affichés dans la lame de **Machines virtuelles** . 

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>Quelle est la différence entre des images personnalisées et les formules ? 
Une image personnalisée est un disque dur virtuel (disque dur virtuel), considérant qu’une formule est une image que vous pouvez configurer des paramètres supplémentaires que vous pouvez enregistrer et reproduire. Une image personnalisée peut être préférable si vous souhaitez créer rapidement plusieurs environnements avec la même image de base, immuable. Une formule peut être préférable si vous voulez reproduire la configuration de votre machine virtuelle avec les derniers bits, un sous-réseau virtuel ou une taille spécifique. Pour une explication plus approfondie, consultez l’article de la [comparaison des images personnalisées et les formules dans les laboratoires DevTest](devtest-lab-comparing-vm-base-image-types.md). 
 
### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Comment pour créer plusieurs ordinateurs virtuels à partir du même modèle à la fois ? 
Vous pouvez utiliser [l’extension des tâches VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) ou [Générer un modèle de gestionnaire de ressources Azure](devtest-lab-add-vm-with-artifacts.md#save-arm-template) lors de la création d’une machine virtuelle et [déployer le modèle de gestionnaire de ressources Azure à partir de Windows PowerShell](../resource-group-template-deploy.md). 
 
### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>Comment déplacer mes machines virtuelles Azure existantes dans mon laboratoire Azure DevTest Labs ? 
Nous sommes conception d’une solution de déplacer directement des machines virtuelles à Azure DevTest Labs, mais actuellement vous pouvez copier vos ordinateurs virtuels existants vers Azure DevTest Labs comme suit : 

1. Copiez le fichier de disque dur virtuel de votre VM existant à l’aide de ce [script de Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1) 
1. [Créer l’image personnalisée](devtest-lab-create-template.md) à l’intérieur de votre laboratoire Azure DevTest Labs. 
1. Créer un ordinateur virtuel dans l’atelier de votre image personnalisée 
 
### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Puis-je attacher plusieurs disques à mes machines virtuelles ? 
Attachement de plusieurs disques d’ordinateurs virtuels est pris en charge.  
 
### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Comment pour automatiser le processus de téléchargement des fichiers de disque dur virtuel pour créer des images personnalisées ? 
Il existe deux options :

- [AzCopy d’Azure](../storage/storage-use-azcopy.md#blob-upload) permet de copier ou de télécharger des fichiers VHD sur le compte de stockage associé à l’atelier.
- [Explorateur de stockage Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome qui s’exécute sur Windows et Linux OSX.   
 
Pour trouver le compte de stockage de destination associé à votre laboratoire, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
1. Sélectionnez les **Groupes de ressources** à partir du Panneau de gauche. 
1. Recherchez et sélectionnez le groupe de ressources associé à votre laboratoire. 
1. Sur la lame de la **vue d’ensemble** , sélectionnez un des comptes de stockage. 
1. Sélectionnez les **objets BLOB**.
1. Recherchez des téléchargements dans la liste. Si aucune n’existe, retourner à l’étape 4 et essayer un autre compte de stockage.
1. Utilisez l' **URL** en tant que destination dans votre commande de AzCopy.


### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Comment puis-je pour automatiser le processus de suppression de tous les ordinateurs virtuels dans mon laboratoire ?

Outre la suppression des ordinateurs virtuels à partir de votre laboratoire dans Azure portal, vous pouvez supprimer tous les ordinateurs virtuels dans votre laboratoire à l’aide d’un script PowerShell. Dans l’exemple suivant, simplement modifier les valeurs de paramètre sous le commentaire **à modifier les valeurs** . Vous pouvez récupérer le `subscriptionId`, `labResourceGroup`, et `labName` les valeurs de la lame de laboratoire dans le portail Azure. 


    # Delete all the VMs in a lab
    
    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    
    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}
    
    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>Que sont les artefacts ? 
Les artefacts sont des éléments personnalisables qui peuvent être utilisés pour déployer vos derniers bits ou vos outils de développement sur un ordinateur virtuel. Ils sont liés à votre machine virtuelle lors de la création en quelques clics simples, et une fois l’ordinateur virtuel est déployé, les artefacts de déploiement et de configurer votre machine virtuelle. Il existe différents artefacts préexistants dans notre [référentiel de Github public](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mais vous pouvez facilement [créer vos propres objets](devtest-lab-artifact-author.md). 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Comment créer un laboratoire à partir d’un modèle de gestionnaire de ressources Azure ? 
Nous avons fourni un que vous pouvez déployer en tant que [référentiel de Github de modèles du Gestionnaire de ressources Azure lab](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) -est ou la modifier pour créer des modèles personnalisés pour vos laboratoires. Chacun de ces modèles est un lien sur lequel vous pouvez cliquer sur pour déployer le laboratoire comme-est sous votre propre abonnement Azure, ou vous pouvez personnaliser le modèle et les [déployer à l’aide de PowerShell ou Azure CLI](../resource-group-template-deploy.md).
 
### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Pourquoi mes ordinateurs virtuels créés dans différents groupes de ressources portant des noms arbitraires ? Puis-je renommer ou modifier les groupes de ressources ? 
Groupes de ressources sont créés de cette manière afin que les laboratoires DevTest Azure gérer les autorisations de l’utilisateur et l’accès aux machines virtuelles. Vous pouvez déplacer l’ordinateur virtuel à un autre groupe de ressources avec le nom de votre choix, cela est déconseillé. Nous travaillons sur l’amélioration de cette expérience pour permettre une plus grande souplesse.   
 
### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Laboratoires combien puis-je créer sous le même abonnement ? 
Il n’y a aucune limite spécifique sur le nombre de travaux pratiques qui peuvent être créées par abonnement. Toutefois, les ressources utilisées sont limitées par abonnement. Vous pouvez lire sur les [quotas et les limites imposées sur les abonnements Azure](../azure-subscription-service-limits.md) et [comment augmenter ces limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests). 
 
### <a name="how-many-vms-can-i-create-per-lab"></a>Le nombre de machines virtuelles puis-je créer par le laboratoire ? 
Il n’y a aucune limite spécifique sur le nombre de machines virtuelles qui peuvent être créés par le laboratoire. Toutefois, le laboratoire prend en charge seulement 40 VM en cours d’exécution en même temps dans le stockage standard et 25 VM qui s’exécutent simultanément dans le stockage de la prime. Nous travaillons actuellement sur l’augmentation de ces limites. 

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Comment faire pour partager un lien direct à mon atelier ?

Pour partager un lien direct à vos utilisateurs de laboratoire, vous pouvez effectuer la procédure suivante.

1. Accédez à l’atelier dans Azure portal.
2. Copier l’URL lab à partir de votre navigateur et le partager avec les utilisateurs du laboratoire. 

>[AZURE.NOTE] Si vos utilisateurs de laboratoire sont des utilisateurs externes à un [compte MSA](#what-is-a-microsoft-account) et ils n’appartiennent pas à Active directory de votre entreprise, ils peuvent recevoir une erreur lors de la navigation vers le lien fourni. S’ils reçoivent une erreur, leur demander de cliquez sur leur nom dans le coin supérieur droit du portail Azure et sélectionnez le répertoire où se trouve le laboratoire à partir de la section **annuaire** du menu.

### <a name="what-is-a-microsoft-account"></a>Ce qu’est un compte Microsoft ?

Un compte Microsoft est que vous utilisez pour la plupart des opérations effectuées avec des services et des périphériques Microsoft. Il s’agit d’une adresse électronique et un mot de passe que vous utilisez pour vous connecter sur Skype, Outlook.com, OneDrive, Windows Phone et Xbox LIVE, et cela signifie que vos fichiers, les photos, les contacts et les paramètres peuvent vous poursuivre vers n’importe quel périphérique. 

>[AZURE.NOTE] Compte Microsoft utilisé pour être appelé « Windows Live ID ».
 
### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mon objet a échoué lors de la création de la machine virtuelle. Comment le résoudre ? 
Consultez le billet de blog [comment résoudre les problèmes liés à l’échec des artefacts dans AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) - écrit par l’une de nos MVP - pour savoir comment obtenir les journaux concernant votre objet a échoué. 
 
### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Pourquoi n’est pas mon réseau existant, virtuel enregistrer correctement ?  
Il est possible que votre nom de réseau virtuel contient des points. Si tel est le cas, essayez de supprimer les périodes ou en les remplaçant par des traits d’union, puis réessayez d’enregistrer le réseau virtuel.
