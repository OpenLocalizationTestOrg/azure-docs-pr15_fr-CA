<properties
    pageTitle="Résolution des problèmes de pile de Microsoft Azure | Microsoft Azure"
    description="Dépannage de pile Azure."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="microsoft-azure-stack-troubleshooting"></a>Résolution des problèmes de pile de Microsoft Azure

Ce document fournit des informations de dépannage courantes pour pile d’Azure.

Pour une expérience optimale, assurez-vous que votre environnement de déploiement répond à toutes les [exigences](azure-stack-deploy.md) et les [préparations](azure-stack-run-powershell-script.md) avant l’installation. 

Les recommandations pour la résolution des problèmes qui sont décrits dans cette section proviennent de plusieurs sources et peuvent ou ne peuvent pas résoudre votre problème spécifique. Si vous rencontrez un problème non documenté, veillez à vérifier le [Forum MSDN de pile Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) pour obtenir un support technique et des informations supplémentaires.

Exemples de code sont fournis en l’état et les résultats attendus ne peut pas être garanties. Cette section est soumis à des modifications fréquentes et des mises à jour que les améliorations du produit sont mises en œuvre.

  

## <a name="known-issues"></a>Problèmes connus

 - Vous pouvez voir les erreurs suivantes, sans terminaison pendant le déploiement, qui n’affectent pas les succès de déploiement :
     - « Le terme 'C:\WinRM\Start-Logging.ps1' n’est pas reconnu »
     - « EceAction appeler : ne peut pas effectuer une indexation dans un tableau null » 
     - « InvokeEceAction : Impossible de lier un argument au paramètre 'Message', car il est une chaîne vide. »
 - Vous pouvez voir des déploiement échouent à l’étape 60.61.93 avec une erreur « L’Application avec l’identificateur 'URI' introuvable ». Ce comportement est dû à la façon dont les applications sont inscrits dans Azure Active Directory.  Si vous recevez cette erreur, passez à [réexécuter le script d’installation](azure-stack-rerun-deploy.md) à partir de l’étape 60.61.93 jusqu'à ce que le déploiement est terminé.
 - Vous verrez que la ressource de **Jeu de disponibilité** sur le marché s’affiche sous la catégorie **virtualMachine-ARM** , cet aspect est uniquement un problème d’ordre esthétique.
 - Lors de la création d’un nouvel ordinateur virtuel dans le portail, dans l’étape de **Notions de base** , l’option de stockage SSD par défaut.  Ce paramètre doit être modifié sur le disque dur ou sur l’étape de la **taille** du déploiement d’ordinateur virtuel, vous ne verrez pas les tailles de machine virtuelle permettent de sélectionner et de continuer à déployer. 
 - Vous verrez les modules AzureRM PowerShell ne sont plus installés par défaut sur l’ordinateur virtuel MAS-CON01 (dans TP1 cela était nommé ClientVM). Ce comportement est voulu par la conception, car il existe une autre méthode pour [installer ces modules et de vous connecter](azure-stack-connect-powershell.md).  
 - Vous verrez que le fournisseur de ressources de **Microsoft.Insights** n’est pas inscrit automatiquement pour les abonnements des clients. Si vous souhaitez voir les données d’analyse d’un ordinateur virtuel déployé comme un client, exécutez la commande suivante à partir de PowerShell (après vous [installer et vous connecter](azure-stack-connect-powershell.md) comme un client) : 
       
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights 

 - Vous verrez la fonctionnalité dans le portail d’exportation pour les groupes de ressources, mais aucun texte n’est affichée et disponible pour exportation.      
 - Vous pouvez démarrer un déploiement des ressources de stockage plus grandes que le quota disponible.  Ce déploiement échoue et que le compte de ressources sera suspendue.  Il existe deux options de conversion :
     - Administrateur de service peut augmenter le quota, bien que les modifications prennent effet immédiatement pas et généralement prendre jusqu'à une heure pour propager.
     - Service administrateur pouvez créer un plan de module complémentaire avec quota supplémentaire que le locataire peut ensuite ajouter à l’abonnement.
 - Lorsque vous utilisez le portail pour créer des ordinateurs virtuels dans des environnements de pile d’Azure avec identité dans « Azure - Chine », vous ne verront pas les tailles de machine virtuelle disponibles à sélectionner lors de l’étape de la **taille** du déploiement d’ordinateur virtuel et ne pourrez pas continuer à déployer.
 - Une erreur de déploiement dans le portail, peut se produire lorsque la machine virtuelle a réellement été déployée avec succès.
 - Lorsque vous supprimez un plan, une offre ou un abonnement, les machines virtuelles ne peuvent pas être supprimés.
 - Vous verrez les extensions de la machine virtuelle sur le marché.
 - Vous ne pouvez pas déployer un ordinateur virtuel à partir d’une image de machine virtuelle enregistrée.
 - Locataires peuvent voir les services qui ne sont pas inclus dans leur abonnement.  Lorsque vous essaient de déployer ces ressources locataires, ils reçoivent une erreur.  Exemple : Abonnement de clients inclut uniquement les ressources de stockage.  Clients verront option permettant de créer d’autres ressources telles que les ordinateurs virtuels.  Dans ce scénario, lorsqu’un client tente de déployer un ordinateur virtuel, ils reçoivent un message indiquant que la machine virtuelle ne peut pas être créée. 
 - Lorsque vous installez le protocole TP2, vous ne devez pas activer l’hôte de système d’exploitation du disque dur virtuel fourni lorsque vous exécutez le script de configuration de pile d’Azure, ou vous pouvez recevoir une erreur indiquant que Windows la messagerie va bientôt expirer.


## <a name="deployment"></a>Déploiement

### <a name="deployment-failure"></a>Échec du déploiement
Si vous rencontrez un problème lors de l’installation, le programme d’installation de la pile d’Azure vous permet de continuer d’un échec de l’installation en suivant la [réexécuter les étapes de déploiement](azure-stack-rerun-deploy.md).

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>À la fin du déploiement, la session PowerShell est toujours ouverte et n’affiche aucune sortie

Ce comportement est sans doute simplement le résultat du comportement par défaut d’une fenêtre de commande PowerShell, lorsqu’elle a été sélectionnée. Le déploiement de la VT a réussi, mais le script a été interrompu lors de la sélection de la fenêtre. Vous pouvez vérifier que cela est le cas en consultant le mot « sélectionner » dans la barre de titre de la fenêtre de commande.  Appuyez sur la touche ÉCHAP pour la désactiver, et le message de fin doit s’afficher après elle.

## <a name="templates"></a>Modèles

### <a name="azure-template-wont-deploy-to-azure-stack"></a>Modèle Azure ne déployer sur la pile d’Azure

Assurez-vous que :

- Le modèle doit utiliser un service Microsoft Azure qui est déjà disponible ou dans un aperçu de la pile d’Azure.
- Les API utilisées pour une ressource spécifique sont pris en charge par l’instance locale de la pile d’Azure, et que vous ciblez un emplacement valide (« local » dans Azure pile technique aperçu (TP) 2, vs le « US Extrême-Orient » ou « Sud-Inde » dans Azure).
- Vous consultez [cet article](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) sur les applets de commande Test-AzureRmResourceGroupDeployment, intercepter les petites différences dans la syntaxe du Gestionnaire de ressources Azure.

Vous pouvez également utiliser les modèles de pile d’Azure déjà fournies dans le [référentiel de GitHub](http://aka.ms/AzureStackGitHub/) pour vous aider à démarrer.

## <a name="virtual-machines"></a>Ordinateurs virtuels

### <a name="after-starting-my-microsoft-azure-stack-poc-host-all-my-tenants-vms-are-gone-from-hyper-v-manager-and-come-back-automatically-after-waiting-a-bit"></a>Après avoir démarré mon hôte Microsoft Azure pile VT, toutes mes machines virtuelles de locataires sont passés à partir du Gestionnaire Hyper-V et revenir automatiquement après avoir attendu quelques ?

Comme le système est réamorcé le sous-système de stockage et de la nécessité de RPs pour déterminer la cohérence. Le temps nécessaire dépend du matériel et aux spécifications en cours d’utilisation, mais il peut être temps après un redémarrage de l’hôte pour les clients ordinateurs virtuels de revenir et d’être reconnu.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>J’a supprimé des ordinateurs virtuels, mais toujours voir les fichiers VHD sur le disque. Ce comportement est normal ?

Oui, c’est le comportement attendu. Il a été conçu de cette manière parce que :

- Lorsque vous supprimez un ordinateur virtuel, les disques durs virtuels ne sont pas supprimés. Les disques sont des ressources distinctes dans le groupe de ressources.
- Lorsqu’un compte de stockage est supprimé, la suppression est visible immédiatement par le biais de Azure Resource Manager (portal, PowerShell), mais les disques qu’il contient sont toujours conservées dans le stockage jusqu'à l’exécution de l’opération garbage collection.

Si vous voyez des disques durs virtuels de « orphelin », il est important de savoir si elles sont partie du dossier pour un compte de stockage qui a été supprimé. Si le compte de stockage n’a pas été supprimé, il est normal qu’ils sont toujours là.

Vous pouvez en savoir plus sur la configuration du seuil de rétention dans la [Gestion des comptes de stockage](azure-stack-manage-storage-accounts.md).

## <a name="installation-steps"></a>Étapes d’installation
Les informations suivantes sur les étapes de l’installation de pile d’Azure peuvent être utiles pour le dépannage :  

| Index | Nom | Description|
| ----- | ----- | -----|
|0,11 | (DEP) Valider des Machines physiques | Validation de la configuration matérielle et du système d’exploitation sur les nœuds physiques. |
| 0,12 | (DEP) Configurer la mise en réseau des Machines physiques de la VT | Configuration des commutateurs de réseau virtuel et interfaces. |
| 0,14 | (DEP) Déployer le domaine | Déploiement d’Active Directory sur l’ordinateur virtuel. |
| 0,15 | (DEP) Configurer le serveur de domaine | Configurer le serveur de domaine avec les groupes de sécurité etc.. |
| 0,16 | (DEP) Configurer la Machine physique | Configurer le réseau, l’accès au domaine et administrateurs locaux de l’installation. |
| 0,18 | (ARRÊTER) Configurer le Cluster du stockage | Créer le cluster de stockage, de créer un serveur de fichier et le pool de stockage. |
| 0.19 | (CPP) Infrastructure de fabric d’installation | Permet de paramétrer les conditions préalables pour le déploiement de la fabric. |
| 0,21 | (NET) Le programme d’installation de BGP et NAT | Installe BGP et NAT - nécessaire uniquement pour un nœud. |
| 0,22 | (NET) Configurer NAT et le serveur de temps | Le serveur de temps à synchroniser et configure les entrées de NAT. |
| 40.41 | (CPP) Créer des ordinateurs virtuels invités | Créer la gestion des ordinateurs virtuels. |
| 40.42 | FBI) Configurer PowerShell JEA | Permet de paramétrer les JEA PowerShell pour tous les rôles. |
| 40.43 | FBI) Configurer l’autorité de Certification Azure pile | Installe l’autorité de Certification de pile Azure. |
| 40.44 | FBI) Configurer l’autorité de Certification de pile Azure | Configure l’autorité de Certification de pile Azure. |
| 40.45 | (NET) Configurer CN sur des machines virtuelles | Installe CN sur des ordinateurs virtuels invités |
| 40.46 | (NET) Configurer CN sur des machines virtuelles | Configurer les ordinateurs virtuels invités CN |
| 40.47 | (NET) Configurer des ordinateurs virtuels invités | Configurer la gestion des machines virtuelles avec des ACL de CN. |
| 60.61.81 | FBI) Déployer des Services de sonneries pile Azure Fabric - FabricRing requis | Crée des VIP pour FabricRing |
| 60.61.82 | FBI) Déployer des Services de sonneries pile Azure Fabric - Fabric anneau Cluster de déployer | Installe et configure le Cluster d’anneau Azure pile Fabric. |
| 60.61.83 | FBI) Déployer des Extensions Admin pour les fournisseurs de ressources | L’installation des Extensions Admin pour les fournisseurs de ressources |
| 60.61.84 | (ACS) Installer le stockage Azure cohérentes au niveau du nœud. | Installe et configure le stockage Azure cohérentes au niveau du nœud. |
| 60.61.85 | (ACS) Configurer le stockage Azure cohérentes au niveau du cluster. | Installe et configure le stockage Azure cohérentes au niveau du cluster. |
| 60.61.86 | FBI) Déployer des Services de contrôleur de pile Azure Fabric Ring - conditions préalables | Conditions préalables pour InfraServiceController |
| 60.61.87 | FBI) Déployer des Services de contrôleur de pile Azure Fabric Ring - conditions préalables | Conditions préalables pour CPI |
| 60.61.88 | FBI) Déployer des Services de contrôleur de pile Azure Fabric Ring - conditions préalables | Conditions préalables pour ASAppGateway |
| 60.61.89 | FBI) Déployer des Services de contrôleur de pile Azure Fabric Ring - conditions préalables | Conditions préalables pour le contrôleur de stockage |
| 60.61.90 | FBI) Déployer des Services de contrôleur de pile Azure Fabric Ring - conditions préalables | Conditions préalables pour HealthMonitoring |
| 60.61.91 | FBI) Déployer des Services de contrôleur de pile Azure Fabric Ring - conditions préalables | Conditions préalables pour l’ONU |
| 60.61.92 | FBI) Déployer des Services de contrôleur de pile Azure Fabric Ring - conditions préalables | Conditions préalables pour PMM |
| 60.61.93 | (Katal) Créer des entités de Service de AzureStack | Créer les Applications Azure graphique et entités de Service dans un DAS. |
| 60.61.94 | (NET) Le programme d’installation GW VMs | Installe GW sur les ordinateurs virtuels invités. |
| 60.61.95 | (NET) Configurer des ordinateurs virtuels de GW | Configure GW sur les ordinateurs virtuels invités. |
| 60.61.96 | (NET) Déployer les IDN sur les hôtes | Déployer les IDN sur les hôtes de l’infrastructure |
| 60.61.97 | (NET) Configurer les IDN | Configurer les IDN rôle |
| 60.61.98 | FBI) Le programme d’installation de WSUS VMs | Installe le serveur WSUS sur les ordinateurs virtuels invités. |
| 60.61.99 | FBI) Configurer des ordinateurs virtuels WSUS | Configure le serveur WSUS sur les ordinateurs virtuels invités. |
| 60.61.100 | FBI) Le programme d’installation de SQL Azure VM | Serveur d’Azure SQL s’installe sur les ordinateurs virtuels invités |
| 60.61.101 | (Katal) Le programme d’installation des conditions préalables pour les machines virtuelles d’a. | Définit les conditions préalables pour pile d’Azure de Microsoft sur les ordinateurs virtuels invités. |
| 60.61.102 | (Katal) Le programme d’installation a été de machines virtuelles | Installe Microsoft Azure pile sur les ordinateurs virtuels invités. |
| 60.120.121 | FBI) Déployer des contrôleurs et des fournisseurs de ressources | Installe des fournisseurs de ressources et de contrôleurs |
| 60.120.121 | FBI) Déployer des contrôleurs et des fournisseurs de ressources | Installe des fournisseurs de ressources et de contrôleurs |
| 60.120.121 | FBI) Déployer des contrôleurs et des fournisseurs de ressources | Installe des fournisseurs de ressources et de contrôleurs |
| 60.120.121 | FBI) Déployer des contrôleurs et des fournisseurs de ressources | Installe des fournisseurs de ressources et de contrôleurs |
| 60.120.121 | FBI) Déployer des contrôleurs et des fournisseurs de ressources | Installe des fournisseurs de ressources et de contrôleurs |
| 60.120.121 | FBI) Déployer des contrôleurs et des fournisseurs de ressources | Installe des fournisseurs de ressources et de contrôleurs |
| 60.120.121 | FBI) Déployer des contrôleurs et des fournisseurs de ressources | Installe des fournisseurs de ressources et de contrôleurs |
| 60.120.121 | FBI) Déployer des contrôleurs et des fournisseurs de ressources | Installe des fournisseurs de ressources et de contrôleurs |
| 60.120.122 | FBI) Configuration du contrôleur | Configure les contrôleurs |
| 60.120.123 | (Katal) Configurer WAS VMs | Configure Microsoft Azure pile sur les ordinateurs virtuels invités. |
| 60.120.124 | (Katal) Configuration de la pile Azure DAS. | Configure une pile Azure avec AD Azure. |
| 60.120.125 | (Katal) Installer ADFS | Installe Active Directory Federation Services (ADFS) |
| 60.120.126 | (Katal) Installer ADFS/graphique | Installe la pile Azure graphique |
| 60.120.127 | (Katal) Configurer ADFS | Configure Active Directory Federation Services (ADFS) |
| 60.140.141 | FBI) Configurer le SRP | Configure le fournisseur de ressources de stockage |
| 60.140.142 | (ACS) Configurer le stockage Azure cohérents. | Configure le stockage Azure cohérents. |
| 60.140.143 | FBI) Créer des comptes de stockage | Créer tous les comptes de stockage devant être utilisé par des fournisseurs différents. |
| 60.140.144 | FBI) Utilisation des registres pour le SRP | Enregistrer l’utilisation d’un fournisseur de stockage. |
| 60.140.145 | (CPP) Migrer les ordinateurs virtuels créés, des hôtes et des clusters vers CPI | Migre les objets des machines virtuelles, hôtes et Cluster créé à CPI |
| 60.140.146 | FBI) Configurez Windows Defender | Configure Windows Defender |
| 60.160.161 | (MON) Configurer l’Agent de surveillance | Configure l’Agent de surveillance |
| 60.160.162 | FBI) NRP préalable | Installe les composants requis NRP |
| 60.160.163 | FBI) Déploiement de NRP | Installe NRP  |
| 60.160.164 | FBI) Configuration de NRP | Configure NRP |
| 60.160.165 | FBI) Prérequis de CRP | Installe les composants requis de CRP |
| 60.160.166 | FBI) Déploiement de CRP | Installe CRP |
| 60.160.167 | FBI) Configuration de CRP | Configure CRP |
| 60.160.168 | FBI) FRP préalable | Installe les composants requis FRP |
| 60.160.169 | FBI) Déploiement de FRP | Installe FRP  |
| 60.160.170 | FBI) Configuration de FRP | Configure FRP |
| 60.160.174 | FBI) URP requis | Installe les composants requis URP |
| 60.160.175 | FBI) Déploiement de URP | Installe URP  |
| 60.160.176 | FBI) Configuration de URP | Configure URP |
| 60.160.171 | FBI) Ensemble de correctifs requis | Installe les composants requis de PRH |
| 60.160.172 | FBI) Déploiement de l’ensemble de correctifs | Installe l’ensemble de correctifs  |
| 60.160.173 | FBI) Configuration de l’ensemble de correctifs | Configure l’ensemble de correctifs |
| 60.160.177 | (KV) KeyVault requis | Installe les composants requis de KeyVault |
| 60.160.178 | (KV) Déploiement de KeyVault | Installe KeyVault  |
| 60.160.179 | (KV) Configuration de KeyVault | Configure le KeyVault | 
| 60.190.191 | FBI) Configurer la galerie | Configurer la galerie |
| 60.190.192 | FBI) Configurer les Services de sonneries de tissu | Configurer les Services de sonneries de tissu |
| 60.221 | FBI) Le programme d’installation, Console ordinateurs virtuels | Installe le serveur de Console sur les ordinateurs virtuels invités. |
| 60.222 | FBI) Le programme d’installation, Console ordinateurs virtuels | Déplacer DVM contenu dans la Console de machine virtuelle. |
| 251 | Préparer pour le redémarrage de l’hôte future | Définir la stratégie de redémarrage |


## <a name="next-steps"></a>Étapes suivantes

[Forum aux questions](azure-stack-FAQ.md)
