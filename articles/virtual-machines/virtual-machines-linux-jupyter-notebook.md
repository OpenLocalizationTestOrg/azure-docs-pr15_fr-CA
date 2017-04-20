<properties
    pageTitle="Créer un bloc-notes de Jupyter/IPython | Microsoft Azure"
    description="Découvrez comment déployer le bloc-notes Jupyter/IPython sur une machine virtuelle de Linux créée avec le modèle de déploiement de gestionnaire de ressources dans Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="crwilcox"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="crwilcox"/>

# <a name="jupyter-notebook-on-azure"></a>Bloc-notes de Jupyter sur Azure

Le [projet de Jupyter](http://jupyter.org), anciennement [IPython projet](http://ipython.org), fournit un ensemble d’outils d’informatique scientifique à l’aide de coques interactifs puissants qui combinent l’exécution de code lors de la création d’un document de calcul direct. Ces fichiers de l’ordinateur portable peuvent contenir du texte arbitraire, des formules mathématiques, code d’entrée, résultats, graphiques, vidéos et tout autre type de média qu’un navigateur web moderne est capable d’afficher. Si vous êtes absolument pour les Python et souhaitez apprendre dans un environnement agréable et interactif ou certains informatique parallèle et techniques graves, le Jupyter est un bon choix.

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) packages à l’aide de SciPy et Matplotlib pour analyser la structure d’un enregistrement audio.


## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Jupyter de deux manières : Ordinateurs portables Azure ou le déploiement personnalisé
Azure fournit un service qui vous permet de [rapidement démarrer à l’aide de Jupyter ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).  En utilisant le Service de carnet d’Azure, vous pouvez accéder facilement à l’interface des ressources informatiques évolutifs avec toute la puissance de Python accessibles par le web de Jupyter et de ses nombreuses bibliothèques.  Dans la mesure où l’installation est gérée par le service, les utilisateurs peuvent accéder à ces ressources, sans la nécessité pour l’administration et de configuration par l’utilisateur.

Si le service de l’ordinateur portable ne fonctionne pas pour votre scénario continuez à lire cet article qui vous sera indique comment déployer le bloc-notes de Jupyter sur Microsoft Azure, à l’aide de machines virtuelles de Linux (VMs).

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Créer et configurer une machine virtuelle sur Azure

La première étape consiste à créer une machine virtuelle (VM) exécute sur Azure.
Cet ordinateur virtuel est un système d’exploitation dans le cloud et sera utilisé pour exécuter le bloc-notes de Jupyter. Azure est capable d’exécuter des ordinateurs virtuels Linux et Windows, et nous allons aborder le programme d’installation de Jupyter sur les deux types de machines virtuelles.

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Créer une VM Linux et d’ouvrir un port pour Jupyter

Suivez les instructions donnés [ici] [ portal-vm-linux] pour créer un ordinateur virtuel de la distribution *Ubuntu* . Ce didacticiel utilise Ubuntu Server 14.04 LTS. Nous supposons que de nom d’utilisateur *azureuser*.

Une fois que l’ordinateur virtuel déploie nous devons ouvrir une règle de sécurité sur le groupe de sécurité du réseau.  À partir du portail Azure, accédez aux **Groupes de sécurité réseau** et cliquez sur l’onglet du groupe de sécurité correspondant à votre machine virtuelle. Vous devez ajouter une règle de trafic entrant de sécurité avec les paramètres suivants : **TCP** pour le protocole, **\*** pour le port source (public) et **9999** pour le port de destination (privé).

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

Tandis que dans le groupe de sécurité de votre réseau, cliquez sur **Interfaces réseau** et notez l' **Adresse IP publique** , tel qu’il sera nécessaire pour se connecter à votre ordinateur virtuel à l’étape suivante.

## <a name="install-required-software-on-the-vm"></a>Installer les logiciels requis sur l’ordinateur virtuel

Pour exécuter le bloc-notes Jupyter sur notre ordinateur virtuel, nous devons tout d’abord installer Jupyter et ses dépendances. Se connecter à votre linux vm à l’aide de ssh et paire de nom d’utilisateur/mot de passe que vous avez choisi lors de la création de la machine virtuelle. Dans ce didacticiel, nous utiliser PuTTY et vous connecter à partir de Windows.

### <a name="installing-jupyter-on-ubuntu"></a>L’installation de Jupyter sur Ubuntu
Installation de Anaconda, une distribution de python de science de données courants, à l’aide d’un des liens fournis dans le [Continuum Analytique](https://www.continuum.io/downloads).  Au moment de la rédaction de ce document, les liens ci-dessous sont les plus à des versions de date.

#### <a name="anaconda-installs-for-linux"></a>Installe anaconda pour Linux
<table>
  <th>3.4 de Python</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>L’installation de Anaconda3 2.3.0 64 bits sur Ubuntu
Par exemple, voici comment vous pouvez installer Anaconda sur Ubuntu

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### <a name="configuring-jupyter-and-using-ssl"></a>Jupyter de configuration et l’utilisation de SSL
Après l’installation, il faut prendre un moment pour les configuration des fichiers d’installation de Jupyter. Si vous rencontrez des problèmes de configuration de Jupyter, il peut être utile d’examiner la [Documentation Jupyter pour l’exécution d’un serveur de l’ordinateur portable](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Suivante nous `cd` dans le répertoire de profil pour créer notre certificat SSL et modifier le fichier de configuration de profils.

Sous Linux, utilisez la commande suivante.

    cd ~/.jupyter

Utilisez la commande suivante pour créer le certificat SSL (Linux et Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Notez que, étant donné que nous allons créer un certificat SSL auto-signé, lors de la connexion à l’ordinateur portable, votre navigateur vous donne un avertissement de sécurité.  Pour la production à long terme, vous devez utiliser un certificat signé associé à votre organisation.  Dans la mesure où la gestion des certificats est dépasse le cadre de cette démonstration, nous nous en tiendrons à un certificat auto-signé pour le moment.

Outre l’utilisation d’un certificat, vous devez également fournir un mot de passe pour protéger votre ordinateur portable à partir de toute utilisation non autorisée.  Pour des raisons de sécurité Jupyter utilise les mots de passe cryptés dans son fichier de configuration, il vous faudra donc crypter votre mot de passe tout d’abord.  IPython fournit un utilitaire pour cela ; à l’invite de commande, exécutez la commande suivante.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Cela vous demandera un mot de passe et la confirmation et imprime ensuite le mot de passe. Notez ceci pour l’étape suivante.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Ensuite, nous allez modifier le fichier de configuration du profil, qui est la `jupyter_notebook_config.py` le fichier dans le répertoire.  Notez que ce fichier n’existe pas, créez simplement si c’est le cas.  Ce fichier comporte un nombre de champs et par défaut tous sont transformées en commentaires.  Vous pouvez ouvrir ce fichier avec un éditeur de texte de votre convenance, et vous devez vous assurer qu’il possède au moins le contenu suivant. **Veillez à remplacer le c.NotebookApp.password dans le fichier config avec le sha1 à partir de l’étape précédente**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Exécuter le bloc-notes de Jupyter

À ce stade, nous sommes prêts à démarrer le bloc-notes Jupyter. Pour ce faire, naviguez vers le répertoire que vous souhaitez stocker dans les portables et démarrer le serveur Jupyter portable avec la commande suivante.

    /anaconda3/bin/jupyter-notebook

Vous devez maintenant être en mesure d’accéder à votre bloc-notes Jupyter à l’adresse `https://[PUBLIC-IP-ADDRESS]:9999`.

Lorsque vous accédez à votre ordinateur portable tout d’abord, la page d’ouverture de session vous demande votre mot de passe. Et une fois que vous vous connectez, vous verrez le « Jupyter ordinateur portable du tableau de bord », qui est le centre de contrôle pour toutes les opérations de l’ordinateur portable.  À partir de cette page, vous pouvez créer de nouveaux ordinateurs portables et ouvrir des fichiers existants.

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>L’aide du carnet de Jupyter

Si vous cliquez sur le bouton **Nouveau** , vous verrez la page suivante.

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

La zone marquée avec une `In []:` invite est la zone d’entrée et vous pouvez entrer ici les Python code valide et il s’exécutera lorsque vous atteignez `Shift-Enter` ou cliquez sur l’icône de « Lecture » (le triangle pointant vers la droite dans la barre d’outils).

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>Un paradigme puissant : live documents informatiques avec les médias enrichis

L’ordinateur portable lui-même devrait sembler très naturelle à quiconque a utilisé les Python et un traitement de texte, car il s’agit d’une certaine manière une combinaison des deux : vous pouvez exécuter des blocs de code Python, mais vous pouvez aussi conserver notes et du texte en modifiant le style d’une cellule à partir de « Code » à « Démarque » à l’aide du menu déroulant dans la barre d’outils.

Jupyter est beaucoup plus qu’un traitement de texte, car elle permet le mélange de calcul et des médias enrichis (texte, graphiques, vidéo et pratiquement tout ce qu’un navigateur web moderne). Vous pouvez combiner, texte, code, des vidéos et bien plus encore !

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

Grâce à la puissance de nombreuses bibliothèques excellente de Python pour scientifique et technique informatique et, dans l’écran suivant, qu'un calcul simple peut être effectué avec la même facilité que l’analyse d’un réseau complexe, dans un environnement.

Ce paradigme de la puissance de l’Internet moderne en mélange avec calcul live offre de nombreuses possibilités et convient parfaitement pour le nuage. le bloc-notes peut être utilisé :

* Un calcul bloc-notes pour enregistrer exploratoire de travailler sur un problème.

* Pour partager les résultats avec vos collègues, dans le formulaire de calcul « live » ou dans des formats de papier (HTML, PDF).

* Pour distribuer et présenter des matériaux d’apprentissage direct qui impliquent le calcul, afin que les stagiaires peuvent immédiatement faire des essais avec le code réel, modifier et exécuter de nouveau interactivement.

* Pour fournir des « livres exécutables » qui présentent les résultats de la recherche d’une manière qui peut être reproduite, validée et étendue par d’autres utilisateurs d’immédiatement.

* En tant que plate-forme pour l’informatique collaborative : plusieurs utilisateurs peuvent se connecter sur le même serveur de bloc-notes pour partager une session de calcul.


Si vous ouvrez le [référentiel][]du code source IPython, vous trouverez un répertoire entier avec des exemples d’ordinateur portable que vous pouvez télécharger et expérimenter sur votre propre Jupyter d’Azure VM.  Il suffit de télécharger le `.ipynb` fichiers à partir du site et les télécharger sur le tableau de bord de votre bloc-notes Azure VM (ou les télécharger directement sur la machine virtuelle).

## <a name="conclusion"></a>Conclusion

Le bloc-notes de Jupyter fournit une interface puissante pour accéder au mode interactif de la puissance de l’écosystème de Python sur Azure.  Il couvre un large éventail de cas d’utilisation, y compris d’exploration simple et apprendre les Python, analyse des données et de visualisation, simulation et l’informatique parallèle. Les documents de bloc-notes résultants contiennent un enregistrement complet des calculs qui sont effectuées et peuvent être partagés avec d’autres utilisateurs de Jupyter.  Le bloc-notes de Jupyter peut être utilisé comme une application locale, mais il convient parfaitement pour les déploiements de nuage sur Azure

Les principales fonctionnalités de Jupyter sont également disponibles à l’intérieur de Visual Studio via les [Python des outils de Visual Studio][] (PTVS). PTVS est un texte libre et open source du plug-in de Microsoft Visual Studio se transforme en un environnement de développement Python avancé qui inclut un éditeur avancé avec IntelliSense, le débogage, profilage et parallèle intégration l'informatique.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre de développement de Python](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[référentiel]: https://github.com/ipython/ipython
[Outils de Python pour Visual Studio]: http://aka.ms/ptvs
