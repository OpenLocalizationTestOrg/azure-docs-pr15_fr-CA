<properties 
    pageTitle="Utiliser des privilèges racine sur les ordinateurs virtuels Linux | Microsoft Azure" 
    description="Apprenez à utiliser des privilèges racine sur un ordinateur virtuel de Linux dans Azure." 
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


# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>À l’aide des privilèges racine sur les machines virtuelles de Linux dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Par défaut, le `root` utilisateur est désactivé sur les ordinateurs virtuels de Linux dans Azure. Les utilisateurs peuvent exécuter des commandes avec des privilèges élevés à l’aide de la `sudo` commande. Toutefois, l’expérience peut varier en fonction de la façon dont le système a été mis en service.

1. **Code SSH et le mot de passe ou uniquement** - la machine virtuelle a été configurée à l’aide soit d’un certificat (`.CER` fichier) ou code SSH ainsi qu’un mot de passe, ou simplement un nom d’utilisateur et mot de passe. Dans ce cas `sudo` vous invite pour le mot de passe de l’utilisateur avant d’exécuter la commande.

2. **Clé SSH uniquement** - l’ordinateur virtuel a été configuré avec un certificat (`.cer`, `.pem`, ou `.pub` fichier) ou code SSH, mais aucun mot de passe.  Dans ce cas `sudo` **pas** d’invite pour le mot de passe de l’utilisateur avant d’exécuter la commande.


## <a name="ssh-key-and-password-or-password-only"></a>SSH clé et mot de passe ou seulement

Se connecter à la machine virtuelle Linux à l’aide de l’authentification par mot de passe ou clé SSH, puis exécuter des commandes à l’aide de `sudo`, par exemple :

    # sudo <command>
    [sudo] password for azureuser:

Dans ce cas, l’utilisateur sera invité pour un mot de passe. Après avoir entré le mot de passe `sudo` exécute la commande avec `root` des privilèges.

Vous pouvez également activer sudo passwordless en modifiant le `/etc/sudoers.d/waagent` de fichiers, par exemple :

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Cette modification permettra sudo passwordless par l’utilisateur « azureuser ».

## <a name="ssh-key-only"></a>SSH clé uniquement

Se connecter à la machine virtuelle Linux à l’aide de l’authentification par clé SSH, puis exécuter des commandes à l’aide de `sudo`, par exemple :

    # sudo <command>

Dans ce cas l’utilisateur sera **pas** entrer un mot de passe. Après avoir appuyé sur `<enter>`, `sudo` exécute la commande avec `root` des privilèges.

 
