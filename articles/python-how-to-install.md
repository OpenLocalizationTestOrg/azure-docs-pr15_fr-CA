<properties
    pageTitle="Installer les Python et le Kit de développement logiciel - Azure"
    description="Apprenez à installer les Python et le Kit de développement logiciel à utiliser avec Azure."
    services=""
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="installing-python-and-the-sdk"></a>L’installation du Kit de développement logiciel et les Python

Python est facile au programme d’installation de Windows et est préinstallé sur [Bash pour Windows](https://msdn.microsoft.com/commandline/wsl/about), Linux et Mac. Ce guide vous guide d’installation et de la préparation de votre ordinateur pour une utilisation avec Azure.

## <a name="whats-in-the-python-azure-sdk"></a>Nouveautés dans les Python Azure SDK ?

Le Kit de développement Azure pour les Python inclut des composants qui vous permettent de développer, déployer et gérer des applications de Python pour Azure. Plus précisément, le Kit de développement Azure pour les Python inclut les éléments suivants :

* **Les bibliothèques de gestion**. Ces bibliothèques de classes fournissent une interface de gestion des ressources d’Azure, tels que les comptes de stockage, des ordinateurs virtuels.

* **Bibliothèques d’exécution**. Ces bibliothèques de classes fournissent une interface pour accéder aux fonctionnalités Azure, telles que les bus de stockage et de service.

## <a name="which-python-and-which-version-to-use"></a>Les Python et la version à utiliser

Il existe plusieurs versions d’interpréteurs de Python - exemples :

* CPython - l’interpréteur Python standard et les plus couramment utilisée
* PyPy - autre implémentation rapide, conforme à la CPython
* IronPython - interpréteur de Python qui s’exécute sur .net/CLR
* Jython - interpréteur de Python qui s’exécute sur la Machine virtuelle Java

**CPython** version2.7 ou v3.3 + et PyPy 5.4.0 sont testés et pris en charge pour les Python Azure SDK.

## <a name="where-to-get-python"></a>Où obtenir les Python ?

Il existe plusieurs façons d’obtenir CPython :

* Directement à partir de [www.python.org][]
* À partir d’un distro dignes de confiance, tel que [www.continuum.io][], [www.enthought.com][] ou [www.activestate.com][]
* Créer à partir de la source !

À moins d’avoir un besoin spécifique, nous vous recommandons des deux premières options.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Installation du Kit de développement logiciel sur Windows, Linux et Mac OS (uniquement pour les bibliothèques de client)

Si vous avez déjà les Python installé, vous pouvez utiliser les pip pour installer une offre groupée de toutes les bibliothèques client dans votre environnement de Python 3.3 + ou l’existant Python 2.7. Les packages sont alors téléchargées à partir de l' [Index de Package Python][] (PyPI).

Vous devrez peut-être des droits d’administrateur :

- Linux et Mac OS, utilisez le `sudo` commande : `sudo pip install azure-mgmt-compute`.
- Windows : Ouvrez votre invite de commande PowerShell en tant qu’administrateur

Vous pouvez installer individuellement chaque bibliothèque pour chaque service Azure :

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Aperçu packages peuvent être installés à l’aide de la `--pre` indicateur :

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

Vous pouvez également installer un ensemble de bibliothèques d’Azure dans une seule ligne à l’aide de la `azure` meta-package. Puisque pas tous les packages dans ce package de métadonnées sont publiés par encore, stable la `azure` meta-package est toujours dans l’aperçu. Toutefois, les packages de base, à partir de perspectives de qualité/conformité de code peuvent être considérés comme « stables » pour l’instant
- Il porte officiellement l’étiquette en tant que tel synchronisés avec d’autres langages dès que possible. Nous n'avons pas l’intention de changements plus importants jusqu’alors.

Dans la mesure où il s’agit d’une version d’évaluation, vous devez utiliser le `--pre` indicateur :

```console
   $ pip install --pre azure
```
   
ou directement

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Lors de l’obtention des Packages

Les [Python Package Index][] (PyPI) a une sélection complète de bibliothèques de Python.  Si vous avez choisi d’installer une Distro, vous aurez déjà la plupart des bits intéressants pour différents scénarios de développement web à l’informatique technique.


## <a name="python-tools-for-visual-studio"></a>Outils de Python pour Visual Studio

[Outils de Python pour Visual Studio][] (PTVS) est un plug-in gratuit/systèmes d’exploitation de Microsoft, ce qui transforme VS dans un IDE de Python à part entière :

![Comment-à-installation-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

À l’aide de PTVS est facultative, mais il est recommandé car il permet la prise en charge de Python et Solution/projet Web, le débogage, profilage, fenêtre interactive, la modification du modèle et Intellisense.

PTVS facilite également le déploiement vers Microsoft Azure, avec prise en charge pour le déploiement sur les [Services en nuage][] et les [sites Web][].

PTVS fonctionne avec votre 2013 de Visual Studio existants ou installation de 2015.  Pour la documentation, des téléchargements et des discussions, voir [Outils de Python pour Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python scénarios Azure pour Linux et Mac OS

Pour Linux ou MacOS, principaux scénarios Azure qui sont prises en charge :

1. Consommation de Services Azure en utilisant les bibliothèques clientes pour les Python

2. L’exécution de votre application sur un ordinateur virtuel de Linux

3. Développement et publication sur les sites Web d’Azure à l’aide de Git

Le premier scénario vous permet de créer des applications web riches qui tirent parti des fonctionnalités telles que le [stockage des objets blob][], [stockage de la file d’attente][], le [stockage de table][] etc. via Pythonic wrappers Azure PaaS pour les API de reste Azure. Ces fonctionne de la même manière sur Windows, Mac et Linux.  Vous pouvez également utiliser ces bibliothèques client à partir de votre ordinateur de développement local ou une VM Linux exécute sur Azure.

Pour le scénario de la machine virtuelle, vous simplement démarrez une VM Linux de votre choix (Ubuntu, CentOS, Suse) et que vous souhaitez exécuter et gérer.  Par exemple, vous pouvez exécuter des portables REPL [IPython][] sur votre ordinateur Windows/Mac/Linux et pointez votre navigateur sur un Linux ou une machine virtuelle multiprocesseur de Windows exécutant le moteur IPython sur Azure. Consultez le didacticiel [Portable IPython sur Azure][] pour plus d’informations.

Pour plus d’informations sur l’installation une VM Linux, consultez le didacticiel [créer un Linux exécutant d’ordinateur virtuel][] .

À l’aide du déploiement de Git, vous pouvez développer une application web de Python et publier sur un site Web d’Azure à partir de n’importe quel système d’exploitation.  Lorsque vous appuyez sur votre référentiel vers Azure, il crée automatiquement un environnement virtuel et pip installer vos packages requis.

Pour plus d’informations sur le développement et la publication de sites Web d’Azure, consultez les didacticiels pour la [Création de sites Web avec Django][], [Création de sites Web avec bouteille][]et [Création de sites Web avec ballon][]. Pour plus d’informations générales sur l’utilisation de n’importe quelle infrastructure compatible WSGI, reportez-vous à la section [Configuration les Python avec Azure des sites Web][].


## <a name="additional-software-and-resources"></a>Des logiciels supplémentaires et ressources :

* [Azure SDK pour les Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK pour les Python Github](https://github.com/Azure/azure-sdk-for-python)
* [Échantillons d’Azure officiels pour les Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribution de Python continuum Analytique][]
* [Les Python Enthought Distribution][]
* [Distribution d’ActiveState Python][]
* [SciPy - une suite de bibliothèques de Python scientifique][]
* [NumPy - une bibliothèque valeurs numériques pour les Python][]
* [Projet Django - framework/CMS web adultes][]
* [IPython - un portable/REPL avancé pour les Python][]
* [Bloc-notes de IPython sur Azure][]
* [Outils de Python pour Visual Studio sur GitHub][]
* [Centre de développement de Python](/develop/python/)

[Distribution de Python continuum Analytique]: http://continuum.io
[Les Python Enthought Distribution]: http://www.enthought.com
[Distribution d’ActiveState Python]: http://www.activestate.com
[www.Python.org]: http://www.python.org
[www.continuum.IO]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - une suite de bibliothèques de Python scientifique]: http://www.scipy.org
[NumPy - une bibliothèque valeurs numériques pour les Python]: http://www.numpy.org
[Projet Django - framework/CMS web adultes]: http://www.djangoproject.com
[IPython - un portable/REPL avancé pour les Python]: http://ipython.org
[IPython]: http://ipython.org
[Bloc-notes de IPython sur Azure]: virtual-machines-linux-jupyter-notebook.md
[Services en nuage]: cloud-services-python-ptvs.md
[Sites Web]: web-sites-python-ptvs-django-mysql.md
[Outils de Python pour Visual Studio]: http://aka.ms/ptvs
[Outils de Python pour Visual Studio sur GitHub]: https://github.com/microsoft/ptvs
[Index de Package Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Créer un ordinateur virtuel exécutant Linux]: virtual-machines-linux-quick-create-cli.md
[Création de sites Web avec Django]: web-sites-python-create-deploy-django-app.md
[Création de sites Web avec bouteille]: web-sites-python-create-deploy-bottle-app.md
[Création de sites Web avec ballon]: web-sites-python-create-deploy-flask-app.md
[Configurer les Python avec des sites Web Azure]: web-sites-python-configure.md
[stockage de table]: storage-python-how-to-use-table-storage.md
[stockage de la file d’attente]: storage-python-how-to-use-queue-storage.md
[stockage des objets BLOB]: storage-python-how-to-use-blob-storage.md
