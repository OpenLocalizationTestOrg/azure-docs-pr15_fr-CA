<properties
    pageTitle="Installer l’Interface de ligne de commande Azure | Microsoft Azure"
    description="Installer Azure Interface de ligne de commande (CLI) pour Mac, Linux et Windows Démarrer l’utilisation des services Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="rasquill"/>
    
# <a name="install-the-azure-cli"></a>Installez l’interface CLI Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [CLI Azure](xplat-cli-install.md)

Installer rapidement l’Interface de ligne de commande de Azure (CLI Azure) pour utiliser un ensemble de commandes d’environnement open source pour la création et la gestion des ressources dans Microsoft Azure. Vous disposez de plusieurs options pour installer ces outils multiplates-formes sur votre ordinateur : 

* **npm package** - exécuter npm (le Gestionnaire de package pour JavaScript) pour installer le dernier package Azure CLI sur votre distribution Linux ou un système d’exploitation. Nécessite node.js et npm sur votre ordinateur.
* **Programme d’installation** - télécharger un programme d’installation pour une installation facile sur Mac ou Windows.
* **Conteneur de docker** - démarrer à l’aide la plus récente CLI dans un conteneur de Docker prête à exécuter. Nécessite un hôte Docker sur votre ordinateur.
    
Pour plus d’options et d’arrière-plan, consultez le référentiels du projet sur [GitHub](https://github.com/azure/azure-xplat-cli). 

Une fois que l’infrastructure du langage commun Azure est installé, [Connectez-vous avec votre abonnement Azure](xplat-cli-connect.md) et exécuter les commandes **azure** à partir de l’interface de ligne de commande (Bash, Terminal, invite de commandes et ainsi de suite) pour travailler avec vos ressources Azure.



## <a name="option-1-install-an-npm-package"></a>Option 1 : Installer un package npm

Pour installer l’interface CLI à partir d’un package npm, assurez-vous que vous avez téléchargé et installé les [dernières Node.js et npm](https://nodejs.org/en/download/package-manager/). Ensuite, exécutez **npm installer** pour installer le package azure-cli : 

    npm install -g azure-cli

Distributions Linux, vous devrez peut-être utiliser **sudo** pour exécuter avec succès la commande __npm__ , comme suit :

    sudo npm install -g azure-cli

> [AZURE.NOTE]Si vous avez besoin d’installer ou de mettre à jour Node.js et npm sur votre distribution Linux ou un système d’exploitation, nous vous recommandons d’installer la version la plus récente Node.js LTS (4.x). Si vous utilisez une version antérieure, vous pouvez obtenir des erreurs d’installation. 

Si vous préférez, téléchargez le [fichier tar] du dernier Linux[ linux-installer] pour le package npm localement. Ensuite, installez le package téléchargé npm comme suit (sur les distributions de Linux que vous devrez peut-être utiliser **sudo**) :

    npm install -g <path to downloaded tar file>

## <a name="option-2-use-an-installer"></a>Option 2 : Utiliser un programme d’installation

Si vous utilisez un ordinateur Mac ou Windows, les programmes d’installation CLI suivantes sont disponibles pour téléchargement :

* [Le programme d’installation de Mac OS X][mac-installer]

* [Windows MSI][windows-installer] 

>[AZURE.TIP]Sous Windows, vous pouvez également télécharger [Web Platform Installer](https://go.microsoft.com/?linkid=9828653) pour installer l’interface CLI. Ce programme d’installation vous donne l’option d’installation supplémentaires Azure SDK et les outils de ligne de commande après l’installation de l’interface CLI. 


## <a name="option-3-use-a-docker-container"></a>Option 3 : Utiliser un conteneur de Docker

Si vous avez configuré votre ordinateur comme un hôte [Docker](https://docs.docker.com/engine/understanding-docker/) , vous pouvez exécuter la dernière CLI Azure dans un conteneur de Docker. Exécutez la commande suivante (sur les distributions de Linux que vous devrez peut-être utiliser **sudo**) :

```
docker run -it microsoft/azure-cli
```


## <a name="run-azure-cli-commands"></a>Exécuter les commandes CLI d’Azure
Après avoir installé le CLI d’Azure, exécutez la commande **azure** à partir de votre interface utilisateur de ligne de commande (Bash, Terminal, invite de commandes et ainsi de suite). Par exemple, pour exécuter la commande d’aide, tapez ce qui suit :

```
azure help
```
> [AZURE.NOTE]Sur certaines versions de Linux, vous pouvez recevoir un message d’erreur semblable à `/usr/bin/env: ‘node’: No such file or directory`. Cette erreur provient des installations récentes de Node.js à /usr/bin/nodejs en cours d’installation. Pour y remédier, créez un lien symbolique vers /usr/bin/node en exécutant cette commande :

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Pour afficher la version de l’infrastructure CLI Azure que vous avez installé, tapez ce qui suit :

```
azure --version
```

Vous êtes maintenant prêt ! Pour accéder à toutes les commandes CLI pour travailler avec vos propres ressources, [se connecter à votre abonnement Azure à partir de la CLI d’Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Lorsque vous utilisez Azure CLI pour la première fois, vous voyez un message vous demandant si vous souhaitez autoriser Microsoft à recueillir des informations sur l’utilisation. La participation est volontaire. Si vous choisissez de participer, vous pouvez arrêter à tout moment en exécutant `azure telemetry --disable`. Pour permettre la participation à tout moment, exécutez `azure telemetry --enable`.


## <a name="update-the-cli"></a>Mise à jour de l’interface CLI

Microsoft publie régulièrement des versions mises à jour de l’infrastructure CLI d’Azure. Réinstallez l’interface CLI à l’aide du programme d’installation pour votre système d’exploitation ou exécuter le dernier conteneur de Docker. Ou bien, si vous avez les dernières Node.js et npm installé, la mise à jour en tapant le texte suivant (sur les distributions de Linux que vous devrez peut-être utiliser **sudo**).

```
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Activer la saisie automatique par tabulation

Tabulation de commandes CLI est prise en charge de Mac et Linux.

Pour l’activer dans zsh, exécutez la commande :

```
echo '. <(azure --completion)' >> .zshrc
```

Pour l’activer dans bash, exécutez :

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Étapes suivantes 

* [Se connecter à partir de l’interface CLI à votre abonnement Azure](xplat-cli-connect.md) pour créer et gérer des ressources Azure.

* Pour en savoir plus sur l’infrastructure du langage commun Azure, téléchargez le code source, signaler des problèmes ou contribuer au projet, visitez le [référentiel de GitHub pour la CLI d’Azure](https://github.com/azure/azure-xplat-cli).

* Si vous avez des questions sur l’utilisation de la CLI d’Azure, ou Azure, visitez les [Forums d’Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

* Si vous le souhaitez, vous pouvez également essayer l' basée sur les Python [Azure CLI 2.0 aperçu](https://github.com/azure/azure-cli).

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
