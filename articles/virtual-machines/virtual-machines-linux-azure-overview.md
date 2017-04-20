 <properties
   pageTitle="Azure et Linux | Microsoft Azure"
   description="Décrit les services Azure Compute, de stockage et de mise en réseau avec des ordinateurs virtuels Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# <a name="azure-and-linux"></a>Azure et Linux

Microsoft Azure est une collection grandissante de public intégré y compris analytique, Machines virtuelles, bases de données mobiles, réseau, stockage, des services en nuage et web, idéale pour l’hébergement de vos solutions.  Microsoft Azure fournit une plate-forme évolutive qui vous permet de payer uniquement ce que vous utilisez, lorsque vous le souhaitez - sans avoir à investir dans le matériel sur site.  Azure est prêt à évoluer vos solutions et out à quelle que soit l’échelle que vous avez besoin pour répondre aux besoins de vos clients.

Si vous êtes familiarisé avec les différentes caractéristiques d’Amazon AWS, vous pouvez examiner vs Azure AWS [document de mappage de définition](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).


## <a name="regions"></a>Régions
Ressources de Microsoft Azure sont réparties sur plusieurs régions géographiques dans le monde entier.  Une « region » représente plusieurs centres de données dans une seule zone géographique.  À partir du 1 janvier 2016, cela inclut : 8 en Amérique, 2 en Europe, 6 dans la région Asie Pacifique, 2 en Chine continentale et 3 en Inde.  Si vous souhaitez obtenir la liste complète de toutes les régions d’Azure, nous tenir à jour qu'une liste existante et récemment annoncé des régions.

- [Régions Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilité
Afin que votre déploiement bénéficier de notre contrat de niveau de Service VM 99.95, vous devez déployer deux ou davantage d’ordinateurs virtuels exécutant votre charge de travail à l’intérieur d’une disponibilité définie. Ainsi, vos ordinateurs virtuels sont répartis sur plusieurs domaines d’erreur dans nos centres de données ainsi que sur les hôtes avec les fenêtres de maintenance. Le total [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explique la garantie de la disponibilité d’Azure dans son ensemble.

## <a name="azure-virtual-machines--instances"></a>Les Machines virtuelles Azure et Instances
Microsoft Azure, prend en charge un certain nombre de distributions Linux populaires fournie et gérée par un certain nombre de partenaires.  Vous trouverez des distributions de Red Hat Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD et bien plus encore sur le marché d’Azure. Nous travaillons activement à diverses communautés de Linux pour ajouter d’autres types à la liste de [Qu'azure visé Linux Distros](virtual-machines-linux-endorsed-distros.md) .

Si votre distro de Linux préféré de choix n’est pas actuellement présent dans la galerie, vous pouvez « apportez votre propre Linux » VM en [créant et en chargeant un disque dur virtuel Linux dans Azure](virtual-machines-linux-create-upload-generic.md).

Les machines virtuelles Azure vous permettent de déployer une large gamme de solutions informatiques d’une manière agile. Vous pouvez déployer pratiquement n’importe quelle charge de travail et de n’importe quel langage sur pratiquement n’importe quel système d’exploitation : Windows, Linux, ou personnalisé créé à partir de l’un de nos liste toujours plus longue des partenaires. Ne voyez toujours pas ce que vous cherchez ?  Ne vous inquiétez pas : vous pouvez également importer vos propres images du site.

## <a name="vm-sizes"></a>Tailles de machine virtuelle
Lorsque vous déployez un ordinateur virtuel dans Azure, vous allez sélectionner une taille de mémoire virtuelle au sein de notre série de tailles qui convient à votre charge de travail. La taille affecte également la capacité d’alimentation, mémoire, stockage et traitement de la machine virtuelle. Vous êtes facturé en fonction de la durée de la machine virtuelle est en cours d’exécution et utilisation de ses ressources allouées. Une liste complète de [formats de Machines virtuelles](virtual-machines-linux-sizes.md).

Voici quelques conseils de base pour la sélection d’une taille de mémoire virtuelle à partir de notre série de (A, D, DS, G et GS).

* Machines virtuelles d’une série sont notre valeur un prix d’entrée de gamme VM pour les charges de travail de lumière et de scénarios de développement/Test. Elles sont largement disponibles dans toutes les régions et peuvent se connecter et utiliser toutes les ressources standard disponibles pour les ordinateurs virtuels.
* Tailles d’une série (A8 - A11) sont configurations de beaucoup de calcul spécial adaptées aux applications de cluster informatique hautes performances.
* Machines virtuelles de D-series sont conçues pour exécuter des applications qui nécessitent une puissance de calcul supérieure et des performances de disque temporaire. Machines virtuelles de la série D offrent des processeurs plus rapides, un taux plus élevé de la mémoire vers le centre et un lecteur à l’état solide (SSD) pour le disque temporaire.
* Dv2-series, est la dernière version de notre série D, doté d’un processeur plus puissant. Le processeur de la série Dv2 est d’environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération 2,4 GHz Intel Xeon® E5-2673 v3 (Haskell) processeur et avec la technologie 2.0 Intel Turbo Boost, peut aller jusqu'à 3,2 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.
* Machines virtuelles de G-series offrent le plus de mémoire et s’exécutent sur des hôtes qui sont dotés de processeurs Intel Xeon E5 V3 de la gamme.

Remarque : La série DS et machines virtuelles de la série GS ont accès au stockage de prime - nos SSD sauvegardé stockage hautes performances et à faible latence pour les charges de travail intensives d’e/s. Stockage de prime est disponible dans certaines régions. Pour plus d’informations, voir :

- [: Premium hautes performances de stockage pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md)

## <a name="automation"></a>Automation
Pour obtenir une culture DevOps appropriée, toute infrastructure doit être de code.  Lorsque toutes l’infrastructure réside dans le code, qu'il peut être facilement recréées (serveurs de Phoenix).  Azure fonctionne avec tous les principale automation des outils tels que Ansible, Chef, SaltStack et Marionnette.  Azure dispose également de ses propres outils pour l’automatisation :

- [Modèles Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [VMAccess Azure](virtual-machines-linux-using-vmaccess-extension.md)

Prise en charge de [nuage-init](http://cloud-init.io/) développe Azure sur la plupart des Linux Distros qui prennent en charge.  Actuellement Ubuntu VMs de Canonical sont déployés avec le nuage-init est activée par défaut.  Fedora RedHats RHEL et CentOS prennent en charge le nuage-init, toutefois les images Azure maintenu par RedHat n’ont pas installé de nuage-init.  Pour utiliser le nuage-init sur une système d’exploitation de la famille RedHat, vous devez créer une image personnalisée avec le nuage-init est installé.

- [À l’aide de nuage-init sur Azure Linux VM](virtual-machines-linux-using-cloud-init.md)

## <a name="quotas"></a>Quotas
Chaque abonnement Azure a des limites de quota par défaut en place pourrait avoir un impact sur le déploiement d’un grand nombre d’ordinateurs virtuels pour votre projet. La limite sur une base par abonnement est de 20 VM par région.  Limites de quota peuvent être déclenchés par un ticket de support demandant une augmentation de la limite de classement.  Pour plus d’informations sur les limites de quota :

- [Service d’abonnement Azure limite](../azure-subscription-service-limits.md)


## <a name="partners"></a>Partenaires

Microsoft travaille en étroite collaboration avec nos partenaires afin des images disponibles sont mis à jour et optimisés pour une exécution Azure.  Pour plus d’informations sur nos partenaires vérifier leurs pages marketplace ci-dessous.

- [Linux sur les Distributions d’Azure par l’éditeur](virtual-machines-linux-endorsed-distros.md)

Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Canonique - [Azure Marketplace - Ubuntu serveur 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - 8 Debian « Jessica »](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Bibliothèque de Bitnami pour Azure](https://azure.bitnami.com/)

Mésosphère - [Azure Marketplace - mésosphère DC/OS sur Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - Service de conteneur Azure avec essaim de Docker](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - CloudBees Jenkins plate-forme](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## <a name="getting-setup-on-azure"></a>Mise en route installation sur Azure
Pour commencer à l’aide d’Azure, vous avez besoin d’un compte Azure, le CLI Azure installé et une paire de clés publiques et privées de SSH.

## <a name="sign-up-for-an-account"></a>Inscrivez-vous à un compte
La première étape de l’utilisation du Cloud Azure doit s’inscrire pour un compte Azure.  Accédez à la page [D’inscription du compte Azure](https://azure.microsoft.com/pricing/free-trial/) pour commencer.

## <a name="install-the-cli"></a>Installez l’interface CLI
Avec votre nouveau compte Azure, vous pouvez commencer immédiatement à utiliser le portail Azure, qui est un panneau de l’administration basée sur le web.  Pour gérer le Cloud Azure via la ligne de commande, que vous installez le `azure-cli`.  Installer l' [Infrastructure du langage commun Azure ](../xplat-cli-install.md)sur votre station de travail Mac ou Linux.

## <a name="create-an-ssh-key-pair"></a>Créer une paire de clés SSH
Maintenant, vous avez un compte Azure, le portail web Azure et la CLI d’Azure.  L’étape suivante consiste à créer une paire de clés SSH utilisé pour SSH dans Linux sans utiliser un mot de passe.  [Créer SSH clés sous Linux et Mac](virtual-machines-linux-mac-create-ssh-keys.md) pour activer les connexions sans mot de passe et une meilleure sécurité.

## <a name="getting-started-with-linux-on-microsoft-azure"></a>Mise en route avec Linux sur Microsoft Azure
Avec la configuration de votre compte Azure, le CLI Azure installés et des clés SSH créés, vous êtes maintenant prêt à commencer à construire une infrastructure dans le nuage d’Azure.  La première tâche consiste à créer plusieurs ordinateurs virtuels.

## <a name="create-a-vm-using-the-cli"></a>Créer un ordinateur virtuel à l’aide de l’interface CLI
Créer une VM Linux à l’aide de l’interface CLI est un moyen rapide pour déployer un ordinateur virtuel sans quitter le terminal que vous utilisez.  Tout ce que vous pouvez spécifier sur le portail web est disponible via un switch ou un indicateur de ligne de commande.  

- [Créer une VM Linux à l’aide de l’interface CLI](virtual-machines-linux-quick-create-cli.md)

## <a name="create-a-vm-in-the-portal"></a>Créer un ordinateur virtuel dans le portail
Créer une VM Linux dans le portail web Azure consiste à pointer et cliquer sur les différentes options pour accéder à un déploiement facilement.  Au lieu d’utiliser les commutateurs ou les indicateurs de ligne de commande, vous êtes en mesure d’afficher une mise en page web intéressante des différentes options et différents paramètres.  Tous les éléments disponibles via l’interface de ligne de commande sont également disponible dans le portail.

- [Créer une VM Linux via le portail](virtual-machines-linux-quick-create-portal.md)

## <a name="login-using-ssh-without-a-password"></a>Connexion à l’aide de SSH sans mot de passe
La machine virtuelle s’exécute désormais sur Azure et vous êtes prêt à ouvrir une session.  À l’aide de mots de passe pour vous connecter via le protocole SSH est non sécurisé et prend beaucoup de temps.  À l’aide de clés SSH est la méthode la plus sûre et la plus rapide pour la connexion.  Lorsque vous créez les VM Linux via le portail ou l’interface CLI, vous avez deux options d’authentification.  Si vous choisissez un mot de passe pour SSH, Azure configure l’ordinateur virtuel pour permettre les connexions d’accès par mot de passe.  Si vous avez choisi d’utiliser une clé publique SSH, Azure configure la machine virtuelle pour autoriser uniquement les connexions via SSH clés et désactive les connexions de mot de passe. Pour sécuriser votre VM Linux en autorisant uniquement les connexions à des clés SSH, utilisez l’option de clé publique SSH lors de la création de la machine virtuelle dans le portail ou l’interface CLI.

- [Désactiver SSH de mots de passe sur votre VM Linux en configurant SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## <a name="related-azure-components"></a>Composants associés Azure

## <a name="storage"></a>Stockage

- [Introduction au stockage de Microsoft Azure](../storage/storage-introduction.md)

- [Ajouter un disque à une VM de Linux à l’aide de la cli d’azure](virtual-machines-linux-add-disk.md)

- [Comment attacher un disque de données à une VM de Linux dans le portail Azure](virtual-machines-linux-attach-disk-portal.md)

## <a name="networking"></a>Mise en réseau

- [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)

- [Adresses IP dans Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Ouverture de ports à une VM Linux dans Azure](virtual-machines-linux-nsg-quickstart.md)

- [Créer un nom de domaine complet dans le portail Azure](virtual-machines-linux-portal-create-fqdn.md)


## <a name="containers"></a>Conteneurs

- [Ordinateurs virtuels et les conteneurs dans Azure](virtual-machines-linux-containers.md)

- [Introduction de conteneur Service Azure](../container-service/container-service-intro.md)

- [Déployer un cluster Service de conteneur Azure](../container-service/container-service-deployment.md)

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant une vue d’ensemble de Linux sur Azure.  L’étape suivante consiste à se plonger dedans et à créer des ordinateurs virtuels quelques !

- [Créer une VM Linux sur Azure à l’aide du portail](virtual-machines-linux-quick-create-portal.md)

- [Créer une VM Linux sur Azure à l’aide de l’interface CLI](virtual-machines-linux-quick-create-cli.md)
