<properties
    pageTitle="Créer des clés SSH sur Linux et Mac | Microsoft Azure"
    description="Générer et utiliser des clés SSH sur Linux et Mac pour le Gestionnaire de ressources et les modèles de déploiement classique sur Azure."
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="v-livech"/>

# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Créer des clés SSH sur Linux et Mac pour les machines virtuelles de Linux dans Azure

Avec une paire de clés SSH, vous pouvez créer des ordinateurs virtuels sur Azure qui par défaut à l’aide de clés SSH pour l’authentification, éliminant le besoin de mots de passe pour vous connecter.  Mots de passe peuvent être devinés et ouvrir vos ordinateurs virtuels jusqu'à brutale implacable tente de deviner votre mot de passe. Ordinateurs virtuels créés avec des modèles d’Azure ou le `azure-cli` peut intégrer votre clé publique SSH du déploiement, de la suppression d’une configuration de déploiement post.  Si vous vous connectez à une VM Linux à partir de Windows, reportez-vous à la section [ce document.](virtual-machines-linux-ssh-from-windows.md)

L’article nécessite :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).

- la [CLI d’Azure](../xplat-cli-install.md) , connecté à l’aide`azure login`

- le mode d’Azure le Gestionnaire de ressources _doit être dans_ Azure CLI`azure config mode arm`

## <a name="quick-commands"></a>Commandes rapides

Dans les commandes suivantes, remplacez les exemples avec votre propre choix.

SSH clés sont par défaut conservée dans le `.ssh` répertoire.  

```bash
cd ~/.ssh/
```

Si vous n’avez pas une `~/.ssh` répertoire du `ssh-keygen` commande créera pour vous avec les autorisations appropriées.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

Entrez le nom du fichier qui est enregistré dans le `~/.ssh/` répertoire :

```bash
id_rsa
```

Entrez le mot de passe pour id_rsa :

```bash
correct horse battery staple
```

Il existe désormais une `id_rsa` et `id_rsa.pub` paire de clés SSH dans le `~/.ssh` répertoire.

```bash
ls -al ~/.ssh
```

Ajoutez la clé nouvellement créée à `ssh-agent` sous Linux et Mac (également ajouté à la chaîne de clé OSX) :

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Copiez la clé publique SSH vers votre serveur Linux :

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

Tester la connexion d’accès à l’aide de touches au lieu d’un mot de passe :

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

À l’aide de clés publiques et privées de SSH est le moyen le plus simple pour vous connecter à vos serveurs Linux. [Cryptographie à clé publique](https://en.wikipedia.org/wiki/Public-key_cryptography) offre une manière beaucoup plus sûre pour vous connecter à votre Linux ou la VM BSD dans Azure que les mots de passe, qui peuvent être imposée par les attaques beaucoup plus facilement. Votre clé publique peut être partagée avec tout le monde ; mais seulement vous (ou votre infrastructure de sécurité locale) posséder votre clé privée.  La clé privée de SSH doit avoir un [mot de passe très sécurisé](https://www.xkcd.com/936/) (source :[xkcd.com](https://xkcd.com)) à protéger.  Ce mot de passe est simplement pour accéder à la clé privée de SSH et **n’est pas** le mot de passe du compte utilisateur.  Lorsque vous ajoutez un mot de passe pour votre clé SSH, il crypte la clé privée, afin que la clé privée est inutile sans le mot de passe pour le déverrouiller.  Si un pirate a volé votre clé privée et que cette clé ne disposait pas d’un mot de passe, ils pourront utiliser cette clé privée pour vous connecter à des serveurs qui possèdent la clé publique correspondante.  Si une clé privée est protégée mot de passe ne peut pas être utilisé par ce pirate, en fournissant une couche de sécurité supplémentaire pour votre infrastructure sur Azure.

Cet article crée des fichiers de clé *ssh rsa* mis en forme, qui sont recommandés pour les déploiements sur le Gestionnaire de ressources.  clés *SSH rsa* sont requis sur le [portail](https://portal.azure.com) pour les déploiements standard et de gestionnaire de ressources.

## <a name="create-the-ssh-keys"></a>Créer les clés SSH

Ssh rsa Azure requiert au moins 2048 bits, format des clés publiques et privées. Pour créer l’utilisation de clés `ssh-keygen`, qui vous pose une série de questions, puis l’écrit une clé privée et une clé publique correspondante. Lors de la création d’une machine virtuelle d’Azure, la clé publique est copiée dans `~/.ssh/authorized_keys`.  Clés SSH dans `~/.ssh/authorized_keys` sont utilisés pour un défi au client correspond à la clé privée correspondante sur une connexion SSH.

## <a name="using-ssh-keygen"></a>À l’aide de ssh keygen

Cette commande crée un mot de passe sécurisé (crypté) SSH paire de clés à l’aide de RSA de 2048 bits et il est commenté afin de le pour identifier facilement.  

Commencez par modifier les répertoires, afin que toutes vos clés ssh sont créés dans le répertoire.

```bash
cd ~/.ssh
```

Si vous n’avez pas une `~/.ssh` répertoire du `ssh-keygen` commande créera pour vous avec les autorisations appropriées.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

_Commande expliqué_

`ssh-keygen`= le programme utilisé pour créer les clés

`-t rsa`= type de clé pour créer ce qui est le [RSA format](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`= bits de la clé

`-C "myusername@myserver"`= un commentaire ajouté à la fin du fichier clé publique afin de l’identifier facilement.  Normalement, un e-mail est utilisé comme commentaire mais vous pouvez utiliser tout ce qui fonctionne le mieux pour votre infrastructure.

### <a name="using-pem-keys"></a>À l’aide des touches PEM

Si vous utilisez le standard déployer le modèle (portail classique d’Azure ou la CLI de gestion de Service Azure `asm`), vous devrez peut-être utiliser le PEM au format clés SSH pour accéder à vos ordinateurs virtuels de Linux.  Voici comment procéder pour créer une clé PEM à partir d’une SSH clé publique existante et un x509 existant certificat.

Pour créer un PEM mise en forme de clé à partir d’une clé publique SSH existante :

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exemple de ssh keygen

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Enregistrer les fichiers de clés :

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

Le nom de la paire de clés pour cet article.  Une paire de clés nommée **id_rsa** est la valeur par défaut et certains outils prévu le nom du fichier de clé privée **id_rsa** afin qu’une est une bonne idée. Le répertoire `~/.ssh/` est l’emplacement par défaut des paires de clés SSH et le fichier de configuration SSH.

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
Une liste de la `~/.ssh` répertoire. `ssh-keygen`crée le `~/.ssh` répertoire s’il n’est pas présente et définit également les modes de propriété et le fichier appropriés.

Ce mot de passe :

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`fait référence à un mot de passe en tant que « mot de passe. »  Il est *fortement* recommandé d’ajouter un mot de passe pour vos paires de clés. Sans un mot de passe protégeant la paire de clés, personnes avec le fichier de clé privée peuvent l’utiliser pour vous connecter à tout serveur qui possède la clé publique correspondante. Ajout d’un mot de passe offre une protection plus efficace dans le cas où un utilisateur est en mesure d’accéder à votre fichier de clé privée, vous donné temps à modifier les clés utilisées pour vous authentifier.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>À l’aide de ssh-agent pour stocker votre mot de passe de clé privée

Pour éviter de taper votre mot de passe du fichier de clé privée avec chaque connexion SSH, vous pouvez utiliser `ssh-agent` en mémoire cache votre mot de passe du fichier de clé privée. Si vous utilisez un Mac, le trousseau OSX stocke en toute sécurité les mots de passe de clé privées lorsque vous appelez `ssh-agent`.

Vérifiez d’abord que `ssh-agent` est en cours d’exécution

```bash
eval "$(ssh-agent -s)"
```

Maintenant ajouter la clé privée à `ssh-agent` à l’aide de la commande `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Le mot de passe de la clé privée est maintenant stockée dans `ssh-agent`.

## <a name="create-and-configure-an-ssh-config-file"></a>Créer et configurer un fichier de configuration SSH

Il est recommandé pour créer et configurer un `~/.ssh/config` fichier pour accélérer les ouvertures de session et d’optimisation de votre comportement de client SSH.

L’exemple suivant montre une configuration standard.

### <a name="create-the-file"></a>Créer le fichier

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Modifiez le fichier pour ajouter la nouvelle configuration SSH :

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Exemple `~/.ssh/config` fichier :

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User myusername
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Cette configuration SSH vous donne des sections pour chaque serveur à activer chacun de sa propre paire de clés dédiée. Les paramètres par défaut (`Host *`) sont pour tous les hôtes qui ne correspondent pas à des hôtes spécifiques plus haut dans le fichier de configuration.


### <a name="config-file-explained"></a>Fichier de configuration indiqué

`Host`= le nom de l’hôte sur le terminal.  `ssh fedora22`indique `SSH` pour utiliser les valeurs dans le bloc de paramètres intitulée `Host fedora22` Remarque : il peut s’agir de n’importe quelle étiquette qui est logique pour votre utilisation et ne représente pas le nom d’hôte réel de n’importe quel serveur.

`Hostname 102.160.203.241`= l’adresse IP ou le nom DNS du serveur en cours d’accès.

`User myusername`= le compte d’utilisateur à distance à utiliser lors de la connexion au serveur.

`PubKeyAuthentication yes`= Indique SSH vous souhaitez utiliser un code SSH pour vous connecter.

`IdentityFile /home/myusername/.ssh/id_id_rsa`= la clé privée de SSH et la clé publique à utiliser pour l’authentification.


## <a name="ssh-into-linux-without-a-password"></a>SSH dans Linux sans mot de passe

Maintenant que vous avez une paire de clés SSH et un fichier de configuration SSH configuré, vous êtes en mesure de se connecter à votre VM Linux rapidement et en toute sécurité. La première fois que vous vous connectez à un serveur à l’aide d’un code SSH les invites de commande vous la phrase de passe pour ce fichier de clé.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Commande expliqué

Lors de la `ssh fedora22` est exécuté tout d’abord, SSH localise et charge tous les paramètres à partir de la `Host fedora22` bloc, puis charge tous les paramètres restants dans le dernier bloc, `Host *`.

## <a name="next-steps"></a>Étapes suivantes

Ensuite consiste à créer des ordinateurs virtuels Linux de Azure à l’aide de la nouvelle clé publique SSH.  Azure VM qui est créés avec une clé publique SSH la connexion est mieux sécurisés que les ordinateurs virtuels créés avec la méthode de connexion par défaut, les mots de passe.  Azure VM créés à l’aide de SSH clés est par défaut configuré avec le mot de passe désactivé, éviter des tentatives de déchiffrement imposée par l’attaque.

- [Créer une VM Linux sécurisé à l’aide d’un modèle d’Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Créer une VM Linux sécurisé via le portail Azure](virtual-machines-linux-quick-create-portal.md)
- [Créer une VM Linux sécurisé à l’aide de la CLI d’Azure](virtual-machines-linux-quick-create-cli.md)
