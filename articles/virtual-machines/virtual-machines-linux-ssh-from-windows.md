<properties 
    pageTitle="Utiliser les touches SSH avec Windows pour les machines virtuelles de Linux | Microsoft Azure" 
    description="Découvrez comment générer et utiliser des clés SSH sur un ordinateur Windows pour se connecter à un ordinateur virtuel de Linux sur Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="squillace" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="rasquill"/>

# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Comment faire pour utiliser SSH clés avec Windows Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)

Lorsque vous vous connectez à Linux, machines virtuelles (VM) dans Azure, vous devez utiliser la [cryptographie à clé publique](https://wikipedia.org/wiki/Public-key_cryptography) afin de fournir un moyen plus sûr pour vous connecter à votre VM Linux. Ce processus implique un échange de clés publique et privé à l’aide de la commande SSH (secure shell) pour l’authentification de vous-même au lieu d’un nom d’utilisateur et votre mot de passe. Les mots de passe sont vulnérables aux attaques, en particulier sur la VM pour Internet, tels que des serveurs web par force brute. Cet article fournit une vue d’ensemble de clés SSH et comment générer les clés appropriées sur un ordinateur Windows.


## <a name="overview-of-ssh-and-keys"></a>Vue d’ensemble des clés et SSH

Vous pouvez en toute sécurité vous connecter à votre VM Linux à l’aide de clés publiques et privées :

- La **clé publique** est placée sur votre VM Linux ou tout autre service que vous souhaitez utiliser avec la cryptographie à clé publique.
- La **clé privée** est ce que vous présentez à votre VM Linux lorsque vous vous connectez, afin de vérifier votre identité. Protéger cette clé privée. Ne la partagez.

Ces clés publiques et privées peuvent être utilisés sur plusieurs machines virtuelles et services. Vous n’avez pas besoin d’une paire de clés pour chaque machine virtuelle ou le service que vous souhaitez accéder. Pour un aperçu plus détaillé, consultez [cryptographie à clé publique](https://wikipedia.org/wiki/Public-key_cryptography).

SSH est un protocole de connexion chiffrée qui permet des connexions sécurisées sur des connexions non sécurisées. Il est le protocole de connexion par défaut pour les machines virtuelles de Linux hébergé dans Azure. Bien que SSH lui-même fournit une connexion cryptée, à l’aide de mots de passe avec des connexions SSH laisse encore l’ordinateur virtuel vulnérable aux attaques ou en devinant les mots de passe en force. Une méthode plus sûre et recommandée, de se connecter à un ordinateur virtuel à l’aide de SSH est à l’aide de ces clés publiques et privées, également connu sous le nom des clés SSH.

Si vous ne souhaitez pas utiliser les touches SSH, vous pouvez toujours vous connecter à vos ordinateurs virtuels de Linux à l’aide d’un mot de passe. Si votre ordinateur virtuel n’est pas exposé à Internet, à l’aide de mots de passe peut être suffisante. Toutefois, vous devez toujours gérer vos mots de passe pour chaque VM de Linux et de tenir à jour les stratégies de mot de passe correct et pratiques, telles que la longueur minimale de mot de passe et les mettre à jour régulièrement. L’utilisation de clés SSH réduit la complexité de la gestion des informations d’identification individuelles sur plusieurs ordinateurs virtuels.


## <a name="windows-packages-and-ssh-clients"></a>Les packages de Windows et les clients SSH

Vous connecter à et gérer des ordinateurs virtuels de Linux dans Azure à l’aide d’un client **ssh** . Ordinateurs Windows ne disposent pas en général un **ssh** client installé. Communs Windows SSH clients que vous pouvez installer sont inclus dans les packages suivants :

- [GIT pour Windows](https://git-for-windows.github.io/)
- [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)

> [AZURE.NOTE] La dernière mise à jour Windows 10 anniversaire comporte Bash pour Windows. Cette fonctionnalité vous permet d’exécuter le sous-système Windows pour Linux et l’accès aux utilitaires comme un client SSH. Bash pour Windows est en cours de développement et est considéré comme une version bêta. Pour plus d’informations sur Bash pour Windows, reportez-vous à la section [Bash sur Ubuntu sous Windows](https://msdn.microsoft.com/commandline/wsl/about).


## <a name="which-key-files-do-you-need-to-create"></a>Les fichiers de clés devez-vous créer ?

**Ssh rsa** Azure requiert au moins 2048 bits, format des clés publiques et privées. Si vous gérez les ressources Azure en utilisant le modèle de déploiement standard, vous devez également générer un PEM (`.pem` fichier).

Voici les scénarios de déploiement et les types de fichiers que vous utilisez dans chaque :

1. **SSH rsa** clés sont requises pour un déploiement à l’aide du [portail Azure](https://portal.azure.com)et les déploiements du Gestionnaire de ressources à l’aide de la [CLI d’Azure](../xplat-cli-install.md).
    - Ces clés sont généralement toutes la plupart des utilisateurs ont besoin.
2. `.pem`fichier est requis pour créer des ordinateurs virtuels à l’aide du [portail de classique](https://manage.windowsazure.com). Ces clés sont également pris en charge dans les déploiements standard qui utilisent l' [Infrastructure du langage commun Azure](../xplat-cli-install.md).
    - Vous devez uniquement créer ces certificats et clés supplémentaires si vous gérez les ressources créées à l’aide du modèle de déploiement standard.


## <a name="install-git-for-windows"></a>Installer Git pour Windows

La section précédente répertoriés plusieurs packages qui incluent le `openssl` outil pour Windows. Cet outil est nécessaire pour créer des clés publiques et privées. Les exemples suivants détaillent la manière d’installer et d’utiliser **Git pour Windows**, même si vous pouvez choisir n’importe quel package que vous préférez. **GIT pour Windows** vous permet d’accéder à des outils logiciels open source supplémentaires ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) et des utilitaires qui peuvent être utiles lorsque vous travaillez avec des machines virtuelles de Linux.

1. Téléchargez et installez le **Git pour Windows** à partir de l’emplacement suivant : [https://git-for-windows.github.io/](https://git-for-windows.github.io/).

2. Acceptez les options par défaut pendant le processus d’installation, sauf si vous avez besoin de les modifier.

3. Exécutez **Git Bash** à partir du **Menu Démarrer** > **Git** > **Git Bash**. La console est similaire à l’exemple suivant :

    ![Shell de GIT pour Windows Bash](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)


## <a name="create-a-private-key"></a>Créer une clé privée

1. Dans la fenêtre **Git Bash** , utilisez `openssl.exe` pour créer une clé privée. L’exemple suivant crée une clé nommée `myPrivateKey` et certificat nommé `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    La sortie ressemble à l’exemple suivant :

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

2. Répondez aux invites pour le nom du pays, emplacement, nom de l’organisation, etc..

3. Votre nouvelle clé privée et le certificat sont créés dans votre répertoire de travail actuel. Pour les meilleures pratiques de sécurité, vous devez définir les autorisations sur votre clé privée afin que seulement vous pouvez y accéder :

    ```bash
    chmod 0600 myPrivateKey
    ```

4. Si vous devez également gérer les ressources de classique, convertir le `myCert.pem` à `myCert.cer` (binaire codé DER X509 certificat). Effectuez cette étape facultative uniquement si vous devez gérer spécifiquement les anciennes ressources de classique. 

    Convertir le certificat en utilisant la commande suivante :

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Créer une clé privée pour PuTTY

PuTTY est un client SSH courant pour Windows. Vous êtes libre d’utiliser n’importe quel client SSH que vous souhaitez. Pour utiliser PuTTY, vous devez créer un autre type de clé : une clé privée PuTTY (PPK). Si vous ne souhaitez pas utiliser de PuTTY, ignorez cette section.

L’exemple suivant crée cette clé privée supplémentaire spécialement pour PuTTY à utiliser :

1. Utilisez **Git Bash** pour convertir votre clé privée d’une clé privée RSA que PuTTYgen peut comprendre. L’exemple suivant crée une clé nommée `myPrivateKey_rsa` à partir de la clé existante nommée `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Pour les meilleures pratiques de sécurité, vous devez définir les autorisations sur votre clé privée afin que seulement vous pouvez y accéder :

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```

2. Téléchargez et exécutez PuTTYgen depuis l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

3. Cliquez sur le menu : **fichier** > **charge une clé privée**

4. Recherchez votre clé privée (`myPrivateKey_rsa` dans l’exemple précédent). Le répertoire par défaut lorsque vous démarrez **Git Bash** est `C:\Users\%username%`. Modifier le filtre de fichiers pour afficher les **tous les fichiers (\*.\*)**:

    ![Charger la clé privée existante PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)

5. Cliquez sur **Ouvrir**. Un message indique que la clé a été importée avec succès :

    ![Clé importé avec succès à la PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)

6. Cliquez sur **OK** pour fermer l’invite de commandes.

7. La clé publique est affichée en haut de la fenêtre de **PuTTYgen** . Vous copiez et collez cette clé publique dans le portail Azure ou dans le Gestionnaire de ressources Azure modèle lorsque vous créez une VM Linux. Vous pouvez également cliquer sur **Enregistrer la clé publique** pour enregistrer une copie sur votre ordinateur :

    ![Enregistrer le fichier de clé publique mastic](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    L’exemple suivant montre comment vous souhaitez de copier et de coller cette clé publique dans le portail d’Azure, lorsque vous créez une VM Linux. La clé publique est stockée en général puis dans `~/.ssh/authorized_keys` sur votre nouvelle machine virtuelle.

    ![Utiliser la clé publique lorsque vous créez un ordinateur virtuel dans le portail Azure](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)

7. Dans **PuTTYgen**, cliquez sur **Enregistrer la clé privée**:

    ![Enregistrer le fichier de clé privée de PuTTY](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

    > [AZURE.WARNING] Une invite vous demande si vous souhaitez continuer sans entrer un mot de passe pour votre clé. Une phrase de passe est similaire à un mot de passe associé à votre clé privée. Même si quelqu'un pour obtenir votre clé privée, ils ne seraient pas être en mesure de s’authentifier en utilisant uniquement la clé. Ils doivent également le mot de passe. Sans un mot de passe, si quelqu'un obtient votre clé privée, ils peuvent se connecter à un ordinateur virtuel ou d’un service qui utilise cette clé. Nous vous recommandons de que vous créez un mot de passe. Toutefois, si vous oubliez le mot de passe, il n’existe aucun moyen de le récupérer.

    Si vous souhaitez entrer un mot de passe, cliquez sur **non**, entrez un mot de passe dans la fenêtre principale de la PuTTYgen et puis cliquez à nouveau sur **Enregistrer la clé privée** . Dans le cas contraire, cliquez sur **Oui** pour continuer sans fournir le mot de passe facultatif.

8. Entrez un nom et un emplacement pour enregistrer votre fichier PPK.


## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Utilisez Putty pour SSH sur une Machine Linux

Là encore, PuTTY est un client SSH courant pour Windows. Vous êtes libre d’utiliser n’importe quel client SSH que vous souhaitez. Les étapes suivantes expliquent comment utiliser votre clé privée se pour authentifier avec votre Azure VM à l’aide de SSH. Les étapes sont similaires dans d’autres clients clés SSH, en termes de devoir charger votre clé privée pour authentifier la connexion SSH.

1. Téléchargement et exécution de putty depuis l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Renseignez le nom d’hôte ou l’adresse IP de votre machine virtuelle à partir du portail Azure :

    ![Ouvrir une nouvelle mastic connexion](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)

3. Avant de sélectionner **Ouvrir**, cliquez sur **la connexion** > **SSH** > onglet**d’Auth** . Recherchez et sélectionnez votre clé privée :

    ![Sélectionnez votre clé privée de PuTTY pour l’authentification](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)

4. Cliquez sur **Ouvrir** pour vous connecter à votre machine virtuelle
 

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également générer les clés publiques et privées [à l’aide d’OS X et Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Pour plus d’informations sur Bash pour Windows et les avantages d’avoir les outils des systèmes d’exploitation disponibles sur votre ordinateur Windows, reportez-vous à la section [Bash sur Ubuntu sous Windows](https://msdn.microsoft.com/commandline/wsl/about).

Si vous avez des problèmes lors de l’utilisation du protocole SSH pour se connecter à vos ordinateurs virtuels de Linux, consultez [résoudre les problèmes de connexions SSH à une machine virtuelle Linux de Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).