<properties 
    pageTitle="Sélection des noms d’utilisateur pour Linux | Microsoft Azure" 
    description="Apprenez à sélectionner des noms d’utilisateur pour une machine virtuelle Linux dans Azure." 
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



#<a name="selecting-user-names-for-linux-on-azure"></a>Sélection des noms d’utilisateur pour Linux sur Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Lorsque vous configurez un ordinateur virtuel de Linux sur Azure, vous devez spécifier le nom d’un utilisateur non racine que vous pouvez utiliser ultérieurement pour vous connecter à la machine virtuelle. Vous pouvez choisir le nom du nouvel utilisateur, ou si la mise en service par l’intermédiaire du portail classique Azure, vous pouvez accepter le nom par défaut « azureuser ».

Dans la plupart des cas, cet utilisateur n’existe pas sur l’image de base et sera créé pendant le processus de déploiement. Si l’utilisateur existe sur l’image de machine virtuelle de base, puis l’agent Azure Linux configure simplement le mot de passe ou un code SSH pour cet utilisateur basé sur les informations que vous avez spécifié lors de la création de la machine virtuelle.

**Toutefois**, Linux définit un ensemble de noms d’utilisateur ne doit pas être utilisé. Le processus de déploiement va **échouer** si vous essayez de mettre en service une VM Linux à l’aide d’un utilisateur du système existant, qui est défini en tant qu’utilisateur avec UID 0 et 99. Un exemple typique est la `root` utilisateur, ce qui a des UID 0.

 - Voir aussi : [plages de Base Standard de Linux - ID de l’utilisateur](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Voici une liste des utilisateurs de système intégré commun pour CentOS et Ubuntu que vous devez éviter d’utiliser lors de la mise en service d’une machine virtuelle de Linux sur Azure. Cette liste n’est qu’un exemple, reportez-vous à la documentation de votre distribution pour s’assurer que le nom d’utilisateur que vous choisissez n’est pas en conflit avec un utilisateur du système existant.


## <a name="centos"></a>CentOS
- Abrt
- ADM
- audio
- emplacement
- lecteur de CD-ROM
- cgred
- démon
- dbus
- appels distant
- DIP
- disque
- lecteur de disquette
- FTP
- FTP
- jeux
- Gopher
- haldaemon
- arrêter
- kmem
- verrou
- LP
- courrier
- homme
- mém.
- nfsnobody
- ne pas partager
- NTP
- opérateur
- oprofile
- postdrop
- suffixe
- qpidd
- racine
- RPC
- rpcuser
- saslauth
- arrêt
- slocate
- sshd
- stapdev
- stapusr
- synchronisation
- Sys
- bande
- test
- tcpdump
- TTY
- utilisateurs
- utempter
- utmp
- UUCP
- vcsa
- vidéo
- ROUE


## <a name="ubuntu"></a>Ubuntu
- ADM
- Admin
- audio
- sauvegarde
- emplacement
- lecteur de CD-ROM
- crontab
- démon
- appels distant
- DIP
- disque
- télécopie
- lecteur de disquette
- fusible
- jeux
- gnats
- IRC
- kmem
- Paysage
- libuuid
- liste
- LP
- courrier
- homme
- bus de messages des
- mlocate
- NETDEV
- Actualités
- ne pas partager
- nogroup
- opérateur
- plugdev
- proxy
- racine
- SASL
- cliché instantané
- src
- SSH
- sshd
- personnel
- sudo
- synchronisation
- Sys
- journal système
- bande
- TTY
- utilisateurs
- utmp
- UUCP
- vidéo
- voix
- whoopsie
- www-données

 