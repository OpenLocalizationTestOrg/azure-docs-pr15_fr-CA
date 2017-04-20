<properties
    pageTitle="Désactiver SSH de mots de passe sur votre VM Linux en configurant SSHD | Microsoft Azure"
    description="Sécuriser votre VM Linux sur Azure par la désactivation des connexions de mot de passe pour SSH."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="v-livech"/>

# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>Désactiver SSH de mots de passe sur votre VM Linux en configurant SSHD

Cet article se concentre sur la façon de verrouiller la sécurité de la connexion de votre VM Linux.  Dès que la voie SSH 22 est ouvert jusqu’au début de robots monde tente de se connecter en devinant les mots de passe.  Nous procéderons dans cet article est de désactiver les connexions de mot de passe via SSH.  En supprimant complètement la possibilité d’utiliser des mots de passe, nous protéger la VM Linux à partir de ce type d’attaque en force.  La prime est, nous allons configurer SSHD Linux pour autoriser uniquement les connexions via SSH des clés publiques et privées, de loin la méthode la plus sûre pour vous connecter à Linux.  Les combinaisons possibles de celui-ci a besoin de deviner la clé privée est immense et par conséquent décourage les robots de déranger même pour essayer de force brute SSH clés.


## <a name="goals"></a>Objectifs

- Configurer SSHD à interdire :
  - Connexions de mot de passe
  - Connexion de l’utilisateur racine
  - Authentification par stimulation-réponse
- Configurer SSHD pour permettre :
  - Seules les connexions de clé SSH
- Redémarrez SSHD pendant encore connecté
- Testez la nouvelle configuration SSHD

## <a name="introduction"></a>Introduction

[SSH défini](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD est le serveur SSH s’exécute sur la VM Linux.  SSH est un client qui s’exécute à partir d’un shell sur votre station de travail MacBook ou Linux.  SSH est également le protocole utilisé pour sécuriser et crypter les communications entre votre station de travail et de la VM Linux.

Pour cet article, il est très important de conserver une connexion d’accès à votre VM Linux ouvrir pour la parcourir ensemble.  Pour cette raison, nous allons ouvrir deux terminaux et SSH de Linux VM de chacun d’eux.  Nous allons utiliser le premier terminal à apporter les modifications au fichier de configuration SSHDs et redémarrez le service SSHD.  Nous allons utiliser le deuxième terminal pour tester ces modifications une fois que le service est redémarré.  Parce que nous allons désactiver SSH de mots de passe et de s’appuyer strictement sur les clés SSH, si vos clés SSH ne sont pas corrects et que vous fermez la connexion à la machine virtuelle, la machine virtuelle seront définitivement verrouillé et personne ne pourra pas pour vous connecter à ce qu’elle doit être supprimée et recréée.

## <a name="prerequisites"></a>Conditions préalables

- [Créer des clés SSH sur Linux et Mac pour les machines virtuelles de Linux dans Azure](virtual-machines-linux-mac-create-ssh-keys.md)
- Compte Azure
  - [inscription gratuite](https://azure.microsoft.com/pricing/free-trial/)
  - [Azure portal](http://portal.azure.com)
- VM Linux exécute sur azure
- SSH paire de clés publique et privée dans`~/.ssh/`
- Clé publique SSH `~/.ssh/authorized_keys` sur la VM Linux
- Sudo des droits sur l’ordinateur virtuel
- Port ouvert de 22

## <a name="quick-commands"></a>Commandes rapides

_Les Linux Admins expérimentés qui souhaitent juste de la version TLDR, commencez ici.  Pour toute autre personne qui souhaite l’explication détaillée et procédure pas à pas ignorent cette section._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## <a name="detailed-walk-through"></a>Parcourir détaillée

Connexion de VM Linux sur le terminal 1 (T1).  Connexion de Linux VM sur Terminal Server 2 (T2).

Sur T2, nous allons modifier le fichier de configuration SSHD.  

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

À partir de là, nous allez modifier simplement les paramètres pour désactiver les mots de passe et activer des connexions SSH à des clés.  Il existe de nombreux paramètres dans ce fichier que vous devez effectuer des recherches et des modifications pour sécuriser Linux & SSH que vous le souhaitez.

#### <a name="disable-password-logins"></a>Désactivez les connexions d’un mot de passe

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>Activer l’authentification par clé publique

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>Désactiver la connexion Root

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>Désactiver l’authentification stimulation / réponse

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>Redémarrez SSHD

À partir du shell de T1, vérifiez que vous êtes toujours connecté.  Ceci est important afin de vous ne pas obtenir verrouillé votre ordinateur virtuel si vos clés SSH ne sont pas correctes car les mots de passe sont maintenant désactivés.  Si les paramètres sont incorrects sur le VM Linux T1 vous permet de corriger les sshd_config que vous serez toujours connecté et SSH conservera la connexion actif pendant le service SSHD redémarrer.

À partir de T2 exécuter :

##### <a name="on-the-debian-family"></a>Dans la famille Debian

```
username@macbook$ sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>Dans la famille RedHat

```
username@macbook$ sudo service sshd restart
```

Mots de passe sont maintenant désactivés sur votre ordinateur virtuel protège des tentatives de connexion de mot de passe par force brute.  Avec uniquement la SSH clés autorisée, vous serez en mesure d’ouvrir une session plus rapidement et plus sécurisé.
