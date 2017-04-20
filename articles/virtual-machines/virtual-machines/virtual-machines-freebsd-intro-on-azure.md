<properties
   pageTitle="Présentation de FreeBSD sur Azure | Microsoft Azure"
   description="En savoir plus sur l’utilisation de machines virtuelles de FreeBSD sur Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# <a name="introduction-to-freebsd-on-azure"></a>Présentation de FreeBSD sur Azure
Cette rubrique fournit une vue d’ensemble de l’exécution d’une machine virtuelle de FreeBSD dans Azure.

## <a name="overview"></a>Vue d’ensemble
FreeBSD pour Microsoft Azure est un système d’exploitation avancé utilisé pour les serveurs modernes d’alimentation, ordinateurs de bureau et incorporés des plates-formes. L’image de FreeBSD 10.3 est fourni par Microsoft Corporation et est disponible dans Azure. Il est basé sur la version de FreeBSD 10.3 et Agent d’invité Azure VM [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) est installé. L’agent est responsable de la communication entre les VM FreeBSD et le tissu Azure pour des opérations, telles que la mise en service de la machine virtuelle à la première utilisation (nom d’utilisateur, mot de passe, nom d’hôte, etc.) et l’activation de la fonctionnalité pour les extensions de machine virtuelle sélectives.
En ce qui concerne les futures versions de FreeBSD, la stratégie consiste à rester à jour et de rendre les dernières versions disponibles peu de temps après qu’ils sont libérés par l’équipe d’ingénierie FreeBSD version. La prochaine version est [11 de FreeBSD](https://www.freebsd.org/releases/11.0R/schedule.html).

## <a name="deploying-a-freebsd-virtual-machine"></a>Déploiement d’un ordinateur virtuel de FreeBSD
Déploiement d’un ordinateur virtuel de FreeBSD est un processus simple à l’aide d’une image à partir de [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

## <a name="vm-extensions-for-freebsd"></a>Extensions de machine virtuelle pour FreeBSD
Voici des extensions VM prises en charge de FreeBSD.

### <a name="vmaccess"></a>VMAccess

L’extension de [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pouvez :

- Réinitialiser le mot de passe de l’utilisateur sudo d’origine.
- Créer un nouvel utilisateur sudo avec le mot de passe spécifié.
- Définir la clé d’hôte publique avec la clé fournie.
- Réinitialiser la clé publique d’hôte fournie lors de la configuration de machine virtuelle si la touche de l’hôte n’est pas fournie.
- Ouvrez le port SSH (22) et restaurer le sshd_config si reset_ssh a la valeur True.
- Supprimer l’utilisateur existant.
- Vérifier les disques.
- Réparer un disque ajouté.

### <a name="customscript"></a>CustomScript

L’extension de [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pouvez :

- Si fourni, télécharger les scripts personnalisés à partir de stockage Azure ou stockage public externe (par exemple, GitHub).
- Exécutez le script de point d’entrée.
- Prend en charge les commandes en ligne.
- Convertir automatiquement les Windows-style de saut de ligne dans l’interpréteur de commandes et de scripts de Python.
- Supprimer nomenclature dans l’interpréteur de commandes et de scripts de Python automatiquement.
- Protéger les données sensibles dans CommandToExecute.

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Authentification : noms d’utilisateur, mots de passe et clés SSH
Lorsque vous créez un ordinateur virtuel de FreeBSD via le portail d’Azure, vous devez fournir un nom d’utilisateur, le mot de passe ou la clé publique de SSH.
Noms d’utilisateur pour le déploiement d’un ordinateur virtuel de FreeBSD sur Azure ne doivent pas correspondre à des noms de comptes système (UID < 100) déjà présents dans l’ordinateur virtuel (« racine », par exemple).
Actuellement, seule la RSA clé SSH est pris en charge. Un code SSH multiligne doit commencer par «---BEGIN SSH2 clé publique--- » et se terminent par «---fin SSH2 clé publique--- ».

## <a name="obtaining-superuser-privileges"></a>Obtention des privilèges de superutilisateur
Le compte d’utilisateur qui est spécifié au cours du déploiement d’instance de machine virtuelle sur Azure est un compte privilégié. Le package de sudo a été installé dans l’image de FreeBSD publié.
Une fois que vous êtes connecté par le biais de ce compte d’utilisateur, vous pouvez exécuter des commandes en tant que root à l’aide de la syntaxe de la commande.

    # sudo <COMMAND>

Vous pouvez éventuellement obtenir un shell de racine à l’aide de sudo -s.

## <a name="next-steps"></a>Étapes suivantes
- Accédez à [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) pour créer une VM FreeBSD.
- Si vous souhaitez mettre votre propre FreeBSD sur Azure, reportez-vous à [Création et téléchargement d’un disque dur virtuel de FreeBSD sur Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).
