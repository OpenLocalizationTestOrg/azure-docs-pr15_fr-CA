<properties 
    pageTitle="Guide de l’utilisateur Linux Agent | Microsoft Azure" 
    description="Découvrez comment installer et configurer l’Agent de Linux (waagent) pour gérer l’interaction de votre machine virtuelle avec Azure Fabric Controller." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



# <a name="azure-linux-agent-user-guide"></a>Guide de l’utilisateur Azure Agent de Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introduction

L’Agent de Linux Microsoft Azure (waagent) gère Linux et FreeBSD mise en service et l’interaction de machine virtuelle avec le contrôleur de tissu Azure. Il fournit les fonctionnalités suivantes de Linux et de FreeBSD IaaS déploiements :

> [AZURE.NOTE] Consultez l’agent Azure Linux [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md) pour plus de détails.

* **Provisionnement d’images**
  - Création d’un compte d’utilisateur
  - Configuration des types d’authentification SSH
  - Déploiement des clés publiques de SSH et des paires de clés
  - Définition du nom d’hôte
  - Le nom d’hôte de publication vers la plate-forme DNS
  - Rapport d’empreinte de clé hôte SSH à la plate-forme
  - Gestion des ressources de disque
  - Mise en forme et monter le disque de la ressource
  - Configuration de l’espace de swap

* **Mise en réseau**
  - Gère les itinéraires pour améliorer la compatibilité avec les serveurs DHCP de plate-forme
  - Garantit la stabilité du nom de l’interface réseau

* **Noyau**
  - Configure virtual NUMA (désactiver pour noyau < 2.6.37)
  - Consomme une entropie de Hyper-V pour/dev/Random
  - Configure les délais d’expiration SCSI pour le périphérique racine (qui pourrait être à distance)

* **Tests de diagnostic**
  - Redirection de console sur le port série

* **Déploiements de SCVMM**
    - Détecte et amorce l’agent VMM pour Linux lors de l’exécution dans un environnement de System Center Virtual Machine Manager 2012 R2

* **Extension de la machine virtuelle**
    - Injecter le composant créé par Microsoft et ses partenaires dans Linux VM (IaaS) pour activer le logiciel et de l’automatisation de la configuration
    - Implémentation de référence d’Extension de la machine virtuelle sur [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


## <a name="communication"></a>Communication
Le flux d’informations à l’agent à partir de la plate-forme se produit via deux canaux :

* Un démarrage joint DVD pour les déploiements de IaaS. Ce DVD comprend un fichier de configuration compatibles OVF qui inclut toutes les informations de configuration que les paires de clés SSH réel.
* Un point de terminaison TCP exposant une API REST est utilisée pour obtenir le déploiement et la configuration de la topologie.


## <a name="requirements"></a>Configuration requise
Les systèmes suivants ont été testés et sont connus pour fonctionner avec l’Agent de Linux Azure :

> [AZURE.NOTE] Cette liste peut être différente de la liste officielle des systèmes pris en charge sur la plate-forme Microsoft Azure, comme indiqué ici : [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Autres systèmes pris en charge :

* FreeBSD 10 + (Agent de Linux Azure v2.0.10 +)

Certains packages de système dépend de l’agent de Linux pour fonctionner correctement :

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Les utilitaires de système de fichiers : sfdisk, fdisk, mkfs, partitionnées
* Outils de mot de passe : chpasswd, sudo
* Outils de traitement de texte : sed, grep
* Outils de réseau : itinéraire-ip
* Noyau prend en charge pour le montage de systèmes de fichiers UDF.

## <a name="installation"></a>Installation
Installation à l’aide d’un fichier RPM ou un package DEB à partir du référentiel de package de votre distribution est la méthode préférée de l’installation et la mise à niveau de l’Agent de Linux Azure. Tous les l' [a approuvé des fournisseurs de distribution](virtual-machines-linux-endorsed-distros.md) d’intégrer le package de l’agent d’Azure Linux leurs images et les référentiels.

Reportez-vous à la documentation dans [mis en pension Azure Linux Agent sur Github](https://github.com/Azure/WALinuxAgent) pour les options d’installation avancées, telles que l’installation à partir de la source ou à des emplacements personnalisés ou de préfixes.


## <a name="command-line-options"></a>Options de ligne de commande

### <a name="flags"></a>Indicateurs

- verbose : augmenter le niveau de détail de la commande spécifiée
- force : ignorer la confirmation interactive pour certaines commandes

### <a name="commands"></a>Commandes

- aide : répertorie les indicateurs et les commandes prises en charge.

- annulation : essayez de nettoyer le système et adapté à la mise en service de nouveau. Cette opération a supprimé les éléments suivants :
 * Toutes les clés d’hôte SSH (si Provisioning.RegenerateSshHostKeyPair est « y » dans le fichier de configuration)
 * Configuration du serveur de noms dans /etc/resolv.conf
 * Mot de passe racine à partir / etc/shadow (si Provisioning.DeleteRootPassword est « y » dans le fichier de configuration)
 * Baux de client DHCP en mémoire cache
 * Nom d’hôte réinitialise localhost.localdomain


> [AZURE.WARNING] Mise hors service ne garantit pas que l’image est désactivée de toutes les informations sensibles et adapté à la redistribution.


- annulation + utilisateur : exécute tout sous - annulation (ci-dessus) et également supprimer le dernier compte d’utilisateur configuré (obtenu à partir de /var/lib/waagent) et les données associées. Ce paramètre est lors de la mise hors service une image qui a été précédemment mise en service sur Azure pour être capturé et réutilisé.

- version : affiche la version de waagent

- serialconsole : Configure GRUB pour marquer les ttyS0 (le premier port série) que la console de démarrage. Cela garantit que les journaux de démarrage du noyau sont envoyées au port série et mis à disposition pour le débogage.

- démon : exécuter waagent en tant que démon pour gérer l’interaction avec la plate-forme. Cet argument est fourni pour waagent dans le script d’initialisation de waagent.

- Démarrer : exécuter waagent en tant que processus d’arrière-plan


## <a name="configuration"></a>Configuration de

Un fichier de configuration (/ etc/waagent.conf) contrôle les actions de waagent. Vous trouverez ci-dessous un exemple de fichier de configuration :

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None

Les différentes options de configuration sont décrites en détail ci-dessous. Options de configuration sont de trois types ; Booléen, chaîne ou nombre entier. Les options de configuration Boolean peuvent être spécifiées en tant que « y » ou « n ». Le mot clé spécial « None » peut être utilisé pour une chaîne type entrées de configuration comme indiqué ci-dessous.

**Provisioning.Enabled :**  
Type : Boolean  
Par défaut : y

Cela permet à l’utilisateur d’activer ou de désactiver la fonctionnalité de mise en service de l’agent. Les valeurs valides sont « y » ou « n ». Si la mise en service est désactivé, clés de hôte et utilisateur SSH dans l’image sont conservées et aucune configuration spécifiée dans l’API de mise en service de Azure est ignorée.

> [AZURE.NOTE] Le `Provisioning.Enabled` paramètre valeur par défaut est « n » sur les Images de nuage Ubuntu qui utilisent init-nuage pour la mise en service.

  
**Provisioning.DeleteRootPassword :**  
Type : Boolean  
Par défaut : n

Si le jeu, le mot de passe racine dans le fichier/etc/shadow est effacé au cours du processus de provisionnement.


**Provisioning.RegenerateSshHostKeyPair :**  
Type : Boolean  
Par défaut : y

Si le jeu de tous les SSH hôte des paires de clés (ecdsa, dsa et rsa) est supprimée pendant le processus de provisionnement de/etc/ssh /. Et une paire de clés fraîche unique est générée.

Le type de cryptage pour la nouvelle paire de clés est configurable par l’entrée Provisioning.SshHostKeyPairType. Veuillez noter que certaines distributions recrée des paires de clés de SSH pour tous les types de cryptage manquant lors du redémarrage du démon SSH (par exemple, après un redémarrage).


**Provisioning.SshHostKeyPairType :**  
Type : chaîne  
Par défaut : rsa

Cette option peut être définie pour un type d’algorithme de cryptage qui est pris en charge par le démon SSH sur l’ordinateur virtuel. Les valeurs généralement pris en charge sont « rsa », « dsa » et « ecdsa ». Notez que « putty.exe » sur Windows ne gère pas « ecdsa ». Par conséquent, si vous envisagez d’utiliser putty.exe sur Windows pour se connecter à un déploiement Linux, utilisez « rsa » ou « dsa ».


**Provisioning.MonitorHostName :**  
Type : Boolean  
Par défaut : y

Si la valeur, waagent analysera la machine virtuelle Linux pour les modifications de nom d’hôte (tel qu’il est renvoyé par la commande « hostname ») et mettre automatiquement à jour la configuration du réseau dans l’image pour refléter la modification. Pour distribuer le changement de nom vers les serveurs DNS, mise en réseau sera redémarré sur l’ordinateur virtuel. Ainsi, en bref perte de la connectivité Internet.


**Provisioning.DecodeCustomData**  
Type : Boolean  
Par défaut : n

Si la valeur, waagent décodera CustomData de Base64.


**Provisioning.ExecuteCustomData**  
Type : Boolean  
Par défaut : n

Si la valeur, waagent exécute CustomData après le déploiement.


**Provisioning.PasswordCryptId**  
Type : chaîne  
Par défaut : 6

Algorithme utilisé par le cryptage lors de la génération du hachage de mot de passe.  
 1 - MD5  
 2 a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  


**Provisioning.PasswordCryptSaltLength**  
Type : chaîne  
Par défaut : 10

Longueur de salt aléatoire utilisé lors de la génération du hachage de mot de passe.


**ResourceDisk.Format :**  
Type : Boolean  
Par défaut : y

Si la valeur, le disque de ressources fourni par la plate-forme sera formaté et monté par waagent si le type de système de fichiers demandé par l’utilisateur dans « ResourceDisk.Filesystem » n’est pas « ntfs ». Une partition unique de type Linux (83) sera disponible sur le disque. Notez que cette partition ne sera pas formatée si elle peut être montée avec succès.


**ResourceDisk.Filesystem :**  
Type : chaîne  
Par défaut : ext4

Spécifie le type de système de fichiers pour le disque de la ressource. Valeurs prises en charge varient par distribution Linux. Si la chaîne est X, puis mkfs. X doit être présent sur l’image de Linux. SLES 11 images doivent en général utiliser 'ext3'. Images de FreeBSD doivent utiliser 'ufs2' ici.


**ResourceDisk.MountPoint :**  
Type : chaîne  
Par défaut : / mnt/ressource 

Spécifie le chemin d’accès auquel le disque de la ressource est monté. Notez que le disque de ressources est un disque *temporaire* et doit être vidé lorsque la machine virtuelle est arrêtée.


**ResourceDisk.MountOptions**  
Type : chaîne  
Par défaut : aucun

Spécifie les options de montage de disque à passer à la commande -o de montage. Il s’agit d’une liste séparée par des virgules des valeurs, ex. « nodev, nosuid ». Consultez mount(8) pour plus de détails.


**ResourceDisk.EnableSwap :**  
Type : Boolean  
Par défaut : n

Si la valeur, un fichier d’échange (/ fichier d’échange) est créé sur le disque de ressources et ajouté à l’espace de swap système.


**ResourceDisk.SwapSizeMB :**  
Type : entier  
Par défaut : 0

La taille du fichier d’échange en mégaoctets.


**Logs.Verbose :**  
Type : Boolean  
Par défaut : n

Si le jeu de commentaires du journal est augmentée. Waagent ouvre /var/log/waagent.log et tire parti de la fonctionnalité du système logrotate pour faire pivoter des journaux.


**SYSTÈME D’EXPLOITATION. EnableRDMA**  
Type : Boolean  
Par défaut : n

Si la valeur, l’agent tentera d’installer et charger un pilote de noyau RDMA qui correspond à la version du firmware du matériel sous-jacent.


**SYSTÈME D’EXPLOITATION. RootDeviceScsiTimeout :**  
Type : entier  
Par défaut : 300

Cela configure le délai d’attente SCSI en quelques secondes sur les lecteurs de disque et les données du système d’exploitation. Si la valeur n’est pas définie, le système par défaut sont utilisées.


**SYSTÈME D’EXPLOITATION. OpensslPath :**  
Type : chaîne  
Par défaut : aucun

Cela peut servir à spécifier un autre chemin pour openssl binaire à utiliser pour les opérations de chiffrement.


**HttpProxy.Host, HttpProxy.Port**  
Type : chaîne  
Par défaut : aucun

Si défini, l’agent utilisera ce serveur proxy pour accéder à internet. 


## <a name="ubuntu-cloud-images"></a>Images de nuage d’Ubuntu

Notez que Ubuntu nuage Images utilisent [init-nuage](https://launchpad.net/ubuntu/+source/cloud-init) pour effectuer de nombreuses tâches de configuration qui seraient sinon être gérés par l’Agent de Linux Azure.  Notez les différences suivantes :


- **Provisioning.Enabled** valeur par défaut est « n » sur Images Ubuntu Cloud-nuage init permet d’effectuer des tâches de provisionnement.

- Les paramètres de configuration suivants sont sans effet sur les Images Ubuntu Cloud-nuage init permet de gérer le ressources disque et d’espace swap :

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Consultez les ressources suivantes pour configurer le point de montage de ressources disque et d’espace sur Ubuntu nuage Images de swap au cours de la mise en service :

 - [Wiki d’Ubuntu : Configurer des Partitions de Swap](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Injection de données personnalisées dans une Machine virtuelle Azure](virtual-machines-windows-classic-inject-custom-data.md)

