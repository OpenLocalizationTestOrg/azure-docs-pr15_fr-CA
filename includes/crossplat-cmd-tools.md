#<a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Comment utiliser les outils de ligne de commande de Azure pour Mac et Linux

Ce guide décrit comment utiliser les outils de ligne de commande de Azure pour Mac et Linux pour créer et gérer les services dans Azure. Les scénarios présentés incluent **l’installation des outils**, **l’importation de vos paramètres de publication**, **Création et gestion de sites Web d’Azure**et **créer et gérer des ordinateurs virtuels Azure**. Pour une documentation de référence complète, consultez [outil de ligne de commande Azure pour Mac et Linux Documentation][reference-docs]. 

##<a name="table-of-contents"></a>Table des matières
* [Quels sont les outils de ligne de commande de Azure pour Mac et Linux](#Overview)
* [Comment faire pour installer les outils de ligne de commande de Azure pour Mac et Linux](#Download)
* [Comment faire pour créer un compte Azure](#CreateAccount)
* [Comment faire pour télécharger et importer les paramètres de publication](#Account)
* [Comment créer et gérer un Site Web d’Azure](#WebSites)
* [Comment faire pour créer et gérer une Machine virtuelle de Azure](#VMs)


<h2><a id="Overview"></a>Quels sont les outils de ligne de commande de Azure pour Mac et Linux</h2>

Les outils de ligne de commande de Azure pour Mac et Linux sont un ensemble d’outils de ligne de commande pour le déploiement et la gestion des services Azure.
 
Les tâches prises en charge sont les suivants :

* Importer les paramètres de publication.
* Créer et gérer des sites Web d’Azure.
* Créer et gérer des ordinateurs virtuels Azure.

Pour obtenir une liste complète des commandes prises en charge, tapez `azure -help` après avoir installé les outils de ligne de commande ou consultez la [documentation de référence][reference-docs].

<h2><a id="Download">Comment faire pour installer les outils de ligne de commande de Azure pour Mac et Linux</a></h2>

La liste suivante contient des informations pour l’installation des outils de ligne de commande, selon votre système d’exploitation :

* **Mac**: Téléchargez [programme d’installation du Kit de développement logiciel Azure][mac-installer]. Ouvrez le fichier .pkg téléchargé et terminer la procédure d’installation lorsque vous y êtes invité.

* **Linux**: installer la dernière version de [Node.js] [ nodejs-org] (voir [Installer Node.js via le Gestionnaire de package][install-node-linux]), puis exécutez la commande suivante :

        npm install azure-cli -g

    **Remarque**: vous devrez peut-être exécuter cette commande avec des privilèges élevés :

        sudo npm install azure-cli -g

* **Windows**: exécutez le programme d’installation Winows (fichier .msi), qui est disponible ici : [Outils de ligne de commande Azure][windows-installer].


Pour tester l’installation, tapez `azure` à l’invite de commande. Si l’installation a réussi, vous verrez une liste de tous les disponibles `azure` commandes.

<h2><a id="CreateAccount"></a>Comment faire pour créer un compte Azure</h2>

Pour utiliser les outils de ligne de commande de Azure pour Mac et Linux, vous aurez besoin d’un compte Azure.

Ouvrez un navigateur web et accédez à [http://www.windowsazure.com] [ windowsazuredotcom] et cliquez sur **version d’évaluation gratuite** dans le coin supérieur droit.

![Site Web de Azure][Azure Web Site]

Suivez les instructions pour la création d’un compte.

<h2><a id="Account"></a>Comment faire pour télécharger et importer les paramètres de publication</h2>

Pour commencer, vous devez d’abord télécharger et importer vos paramètres de publication. Cela vous permettra d’utiliser les outils pour créer et gérer des Services Azure. Pour télécharger vos paramètres de publication, utilisez la `account download` commande :

    azure account download

Vous ouvrez votre navigateur par défaut et vous invite à vous connecter sur le portail de gestion. Une fois connecté, votre `.publishsettings` fichier sera téléchargé. Prenez note de l’emplacement de ce fichier.

Ensuite, importez le `.publishsettings` fichier en exécutant la commande suivante, en remplaçant `{path to .publishsettings file}` avec le chemin d’accès à votre `.publishsettings` fichier :

    azure account import {path to .publishsettings file}

Vous pouvez supprimer toutes les informations stockées par le <code>import</code> commande à l’aide de la <code>account clear</code> commande :

    azure account clear

Pour afficher une liste d’options de `account` commandes, utiliser le `-help` option :

    azure account -help

Après avoir importé vos paramètres de publication, vous devez supprimer la `.publishsettings` fichier pour des raisons de sécurité.

> [AZURE.NOTE] Lorsque vous importez des paramètres de publication, les informations d’identification pour accéder à votre abonnement Azure sont stockées à l’intérieur de votre `user` dossier. Votre `user` dossier est protégé par votre système d’exploitation. Toutefois, il est recommandé d’utiliser des étapes supplémentaires pour crypter votre `user` dossier. Vous pouvez le faire de plusieurs façons :    
> 
> - Sous Windows, modifiez les propriétés du dossier ou utiliser BitLocker.
> - Sur Mac, activez FileVault pour le dossier.
> - Sur Ubuntu, utilisez la fonction Encrypted Home directory. Autres distributions de Linux offrent des fonctionnalités équivalentes.

Vous êtes maintenant prêt à en cours de création et de gestion de sites Web d’Azure et d’Azure Virtual Machines.  

<h2><a id="WebSites"></a>Comment faire pour créer et gérer un site Web d’Azure</h2>

###<a name="create-a-website"></a>Créer un site Web

Pour créer un site Web d’Azure, commencez par créer un répertoire vide appelé `MySite` et recherchez dans ce répertoire.

Ensuite, exécutez la commande suivante :

    azure site create MySite --git

La sortie de cette commande contiendra l’URL par défaut pour le site Web nouvellement créé. Le `--git` option vous permet d’utiliser git pour publier sur votre site Web en créant des référentiels git dans votre répertoire d’application local et dans le centre de données de votre site Web. Notez que si votre dossier local est déjà un référentiel git, la commande Ajouter un nouvel appel distant au référentiel existant, qui pointe vers le référentiel dans le centre de données de votre site Web.

Notez que vous pouvez exécuter le `azure site create` avec une des options suivantes :

* `--location [location name]`. Cette option vous permet de spécifier l’emplacement du centre de données dans laquelle votre site Web est créé (par exemple, « ouest nous »). Si vous omettez cette option, vous serez invite à choisir un emplacement.
* `--hostname [custom host name]`. Cette option vous permet de spécifier un nom d’hôte personnalisé pour votre site Web.

Vous pouvez ensuite ajouter le contenu dans le répertoire de votre site Web. Utilisez le flux normal git (`git add`, `git commit`) pour valider le contenu. Utilisez la commande git suivante pour pousser le contenu de votre site Web vers Azure : 

    git push azure master

###<a name="set-up-publishing-from-github"></a>Configurer la publication à partir de GitHub

Pour configurer une publication en continu à partir d’un référentiel de GitHub, utilisez la `--GitHub` option lors de la création d’un site :

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Si vous avez un clone local d’un référentiel GitHub ou si vous disposez d’un référentiel avec une référence distante à un référentiel GitHub, cette commande sera automatiquement publier le code dans le référentiel de GitHub à votre site. Dès lors, toutes les modifications vers le référentiel GitHub être publiées automatiquement sur votre site.

Lorsque vous configurez une publication à partir de GitHub, la branche par défaut utilisée est la branche principale. Pour spécifier une autre branche, exécutez la commande suivante à partir de votre référentiel local :

    azure site repository <branch name>

###<a name="configure-app-settings"></a>Configurer les paramètres de l’application

Paramètres de l’application sont des paires clé-valeur qui sont disponibles à votre application lors de l’exécution. Lorsque vous définissez pour un site Web d’Azure, valeurs de paramètre d’application remplace les paramètres avec la même clé que celle qui sont définis dans le fichier Web.config de votre site. Pour les applications Node.js et PHP, les paramètres de l’application sont disponibles sous forme de variables d’environnement. L’exemple suivant vous montre comment définir une paire clé-valeur :

    azure site config add <key>=<value> 

Pour afficher la liste de toutes les paires clé/valeur, utilisez la commande suivante :

    azure site config list 

Ou, si vous connaissez la clé et que vous souhaitez afficher la valeur, vous pouvez utiliser :

    azure site config get <key> 

Si vous souhaitez modifier la valeur d’une clé existante, vous devez commencer par effacer la clé existante et puis l’ajouter de nouveau. La commande clear est :

    azure site config clear <key> 

###<a name="list-and-show-sites"></a>Répertorier et afficher les sites

Pour la liste de vos sites Web, utilisez la commande suivante :

    azure site list

Pour obtenir des informations détaillées sur un site, utilisez les `site show` commande. L’exemple suivant affiche les détails de `MySite`:

    azure site show MySite

###<a name="stop-start-or-restart-a-site"></a>Arrêter, démarrer ou redémarrer un site

Vous pouvez arrêter, démarrer ou redémarrer un site avec la `site stop`, `site start`, ou `site restart` commandes :

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

###<a name="delete-a-site"></a>Supprimer un site

Enfin, vous pouvez supprimer un site avec la `site delete` commande :

    azure site delete MySite

Notez que si vous exécutez l’un des au-dessus des commandes à l’intérieur du dossier où vous avez exécuté `site create`, vous n’avez pas besoin de spécifier le nom du site `MySite` comme dernier paramètre.

Pour voir une liste complète des `site` commandes, utiliser le `-help` option :

    azure site -help 

<h2><a id="VMs"></a>Comment faire pour créer et gérer une Machine virtuelle de Azure</h2>

une Machine virtuelle de Azure est créé à partir d’une image d’ordinateur virtuel (fichier .vhd) que vous fournissez ou qui est disponible dans la galerie d’Image. Pour afficher les images disponibles, utilisez la `vm image list` commande :

    azure vm image list

Vous pouvez configurer et démarrer une machine virtuelle à partir d’une des images disponibles avec le `vm create` commande. L’exemple suivant montre comment créer une machine virtuelle Linux (appelée `myVM`) à partir d’une image dans la galerie d’Image (CentOS 6.2). Le nom racine de l’utilisateur et le mot de passe pour l’ordinateur virtuel sont `myusername` et `Mypassw0rd` respectivement. (Notez que la `--location` paramètre spécifie le centre de données dans lequel l’ordinateur virtuel est créé. Si vous omettez le `--location` paramètre, vous serez invité à choisir un emplacement.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Vous pouvez envisager le passage de la `--ssh` indicateur (Linux) ou `--rdp` indicateur (Windows) pour `vm create` pour activer les connexions à distance à l’ordinateur virtuel nouvellement créé.

Si vous devez serait plutôt configurer une machine virtuelle à partir d’une image personnalisée, vous pouvez créer une image à partir d’un fichier .vhd avec la `vm image create` de commande, puis utilisez la `vm create` commande pour configurer l’ordinateur virtuel. L’exemple suivant montre comment créer une image de Linux (appelée `myImage`) à partir d’un fichier .vhd local. (Le `--location` paramètre spécifie les données dans lequel l’image est stockée.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Au lieu de créer une image à partir d’un local .vhd, vous pouvez créer une image à partir d’un .vhd stocké dans le stockage Blob Azure. Vous pouvez faire avec le `blob-url` paramètre :

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Après avoir créé une image, vous pouvez configurer un ordinateur virtuel à partir de l’image à l’aide de `vm create`. La commande suivante crée un ordinateur virtuel appelé `myVM` de l’image créé ci-dessus (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Une fois que vous avez configuré un ordinateur virtuel, vous souhaiterez peut-être créer des points de terminaison pour permettre l’accès à votre ordinateur virtuel (par exemple). L’exemple suivant utilise le `vm create endpoint` commande pour ouvrir un port externe 22 et port local 22 sur `myVM`:

    azure vm endpoint create myVM 22 22

Vous pouvez obtenir des informations détaillées sur une machine virtuelle (y compris l’adresse IP, nom DNS et les informations de point de terminaison) avec le `vm show` commande :

    azure vm show myVM

Pour arrêter, Démarrer, ou redémarrez l’ordinateur virtuel, utilisez une des commandes suivantes :

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Enfin, pour supprimer l’ordinateur virtuel, utilisez le `vm delete` commande :

    azure vm delete myVM

Pour obtenir une liste complète des commandes pour créer et gérer des machines virtuelles, vous devez utiliser le `-h` option :

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com

