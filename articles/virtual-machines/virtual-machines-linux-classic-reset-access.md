<properties
        pageTitle="Réinitialiser le mot de passe Linux VM et code SSH à partir de l’interface CLI | Microsoft Azure"
        description="Comment faire pour utiliser l’extension de VMAccess à partir de l’Interface de ligne de commande (CLI) Azure pour réinitialiser un mot de passe Linux VM ou un code SSH, corrigez la configuration SSH et vérifier la cohérence des disques"
        services="virtual-machines-linux"
        documentationCenter=""
        authors="cynthn"
        manager="timlt"
        editor=""
        tags="azure-service-management"/>

<tags
        ms.service="virtual-machines-linux"
        ms.workload="infrastructure-services"
        ms.tgt_pltfrm="vm-linux"
        ms.devlang="na"
        ms.topic="article"
        ms.date="06/14/2016"
        ms.author="cynthn"/>

# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Comment faire pour réinitialiser un mot de passe Linux VM ou un code SSH, corrigez la configuration SSH et vérifier la cohérence des disques à l’aide de l’extension VMAccess


Impossible de se connecter à un ordinateur virtuel de Linux sur Azure en raison d’un mot de passe oublié, Secure Shell (SSH) clé incorrecte, ou un problème avec la configuration SSH, utilisez l’extension VMAccessForLinux avec la CLI d’Azure pour réinitialiser le mot de passe ou un code SSH, corrigez la configuration SSH et vérifiez la cohérence des disques. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Avec la CLI d’Azure, utilisez la commande **set d’azure vm extension** de l’interface de ligne de commande (Bash, Terminal, invite de commandes) pour accéder aux commandes. Exécuter l' **aide azure vm extension est définie** pour l’utilisation d’une extension détaillées.

Avec la CLI d’Azure, vous pouvez effectuer les tâches suivantes :

+ [Réinitialiser le mot de passe](#pwresetcli)
+ [Réinitialiser le code SSH](#sshkeyresetcli)
+ [Réinitialiser le mot de passe et le code SSH](#resetbothcli)
+ [Créer un nouveau compte d’utilisateur sudo](#createnewsudocli)
+ [Réinitialiser la configuration SSH](#sshconfigresetcli)
+ [Supprimer un utilisateur](#deletecli)
+ [Afficher l’état de l’extension VMAccess](#statuscli)
+ [Vérifier la cohérence des disques](#checkdisk)
+ [Réparation de disques sur votre VM Linux](#repairdisk)


## <a name="prerequisites"></a>Conditions préalables

Vous devez effectuer les opérations suivantes :

- Vous devez [installer l’interface CLI d’Azure](../xplat-cli-install.md) et [se connecter à votre abonnement](../xplat-cli-connect.md) utiliser les ressources Azure associés à votre compte.
- Définissez le mode approprié pour le modèle de déploiement classique en tapant ce qui suit à l’invite de commande :
        
        azure config mode asm
        
- Avoir un nouveau mot de passe ou un jeu de clés SSH, si vous souhaitez rétablir le d’eux. Vous n’avez besoin si vous souhaitez rétablir la configuration SSH.


## <a name="pwresetcli"></a>Réinitialiser le mot de passe

1. Créez un fichier nommé PrivateConf.json contenant ces lignes. Remplacer les crochets et le & #60 ; espace réservé & #, 62 ; valeurs par vos propres informations.

        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour & #60 ; nom de la machine virtuelle & #, 62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>Réinitialiser le code SSH

1. Créez un fichier nommé PrivateConf.json avec ce contenu. Remplacer les crochets et le & #60 ; espace réservé & #, 62 ; valeurs par vos propres informations.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour & #60 ; nom de la machine virtuelle & #, 62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Réinitialiser le mot de passe et le code SSH

1. Créez un fichier nommé PrivateConf.json avec ce contenu. Remplacer les crochets et le & #60 ; espace réservé & #, 62 ; valeurs par vos propres informations.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour & #60 ; nom de la machine virtuelle & #, 62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>Créer un nouveau compte d’utilisateur sudo

Si vous avez oublié votre nom d’utilisateur, vous pouvez utiliser VMAccess pour créer un nouveau avec l’autorité sudo. Dans ce cas, le nom d’utilisateur et le mot de passe ne seront pas modifiés.

Pour créer un nouvel utilisateur sudo avec accès de mot de passe, utilisez le script de [Réinitialiser le mot de passe](#pwresetcli) et spécifier le nouveau nom d’utilisateur.

Pour créer un nouvel utilisateur sudo avec accès de clé SSH, utilisez le script de [Réinitialiser la clé SSH](#sshkeyresetcli) et spécifier le nouveau nom d’utilisateur.

Vous pouvez également utiliser la commande [Réinitialiser le mot de passe et le code SSH](#resetbothcli) pour créer un nouvel utilisateur avec mot de passe et accès SSH de clé.

## <a name="sshconfigresetcli"></a>Réinitialiser la configuration SSH

Si la configuration SSH est dans un état indésirable, vous pourriez perdre également l’accès à la machine virtuelle. Vous pouvez utiliser l’extension VMAccess pour réinitialiser la configuration à son état par défaut. Pour ce faire, il vous suffit de définir la clé « reset_ssh », « True ». L’extension va redémarrer le serveur SSH, ouvrir le port SSH sur votre machine virtuelle et réinitialiser la configuration SSH pour les valeurs par défaut. Le compte d’utilisateur (nom, mot de passe ou des clés SSH) ne changera pas.

> [AZURE.NOTE] Le fichier de configuration SSH est réinitialisé se trouve dans /etc/ssh/sshd_config.

1. Créez un fichier nommé PrivateConf.json avec ce contenu.

        {
        "reset_ssh":"True"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour & #60 ; nom de la machine virtuelle & #, 62 ;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>Supprimer un utilisateur

Si vous souhaitez supprimer un compte d’utilisateur sans se connecter à la machine virtuelle directement, vous pouvez utiliser ce script.

1. Créez un fichier nommé PrivateConf.json avec ce contenu, en substituant le nom de l’utilisateur à supprimer pour & #60 ; usernametoremove & #, 62 ;. 

        {
        "remove_user":"<usernametoremove>"
        }

2. Exécutez la commande suivante, en remplaçant le nom de votre machine virtuelle pour & #60 ; nom de la machine virtuelle & #, 62 ;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>Afficher l’état de l’extension VMAccess

Pour afficher le statut de l’extension VMAccess, exécutez la commande suivante.

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a>< un name = 'checkdisk' <</a>de vérifier la cohérence des disques

Pour exécuter la commande fsck sur tous les disques de votre ordinateur virtuel de Linux, vous devez effectuer les opérations suivantes :

1. Créez un fichier nommé PublicConf.json avec ce contenu. Vérification du disque prend une valeur booléenne pour vérifier les disques connectés à votre ordinateur virtuel ou non. 

        {   
        "check_disk": "true"
        }

2. Exécutez cette commande à exécuter, en substituant le nom de votre machine virtuelle pour & #60 ; nom de la machine virtuelle & #, 62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>Disquettes de réparation 

Pour réparer les disques qui ne sont pas monter ou des erreurs de configuration de montage, utilisez l’extension VMAccess pour réinitialiser la configuration de montage sur votre machine virtuelle de Linux. En remplaçant le nom de votre disque pour & #60 ; yourdisk & #, 62 ;.

1. Créez un fichier nommé PublicConf.json avec ce contenu. 

        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }

2. Exécutez cette commande à exécuter, en substituant le nom de votre machine virtuelle pour & #60 ; nom de la machine virtuelle & #, 62 ;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## <a name="next-steps"></a>Étapes suivantes

* Si vous souhaitez utiliser les applets de commande PowerShell de Azure ou modèles du Gestionnaire de ressources Azure pour réinitialiser le mot de passe ou un code SSH, corrigez la configuration SSH et vérifier la cohérence des disques, reportez-vous à la [documentation d’extension VMAccess sur GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* Vous pouvez également utiliser le [portail Azure](https://portal.azure.com) pour réinitialiser le mot de passe ou code SSH d’un VM Linux déployés dans le modèle de déploiement classique. Vous ne pouvez pas utiliser actuellement le portail ne pour une VM Linux déployé dans le modèle de déploiement du Gestionnaire de ressources.

* Pour plus d’informations sur l’utilisation des extensions de la machine virtuelle pour les machines virtuelles Azure, consultez [à propos des fonctionnalités et extensions de la machine virtuelle](virtual-machines-linux-extensions-features.md) .
