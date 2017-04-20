Certains packages ne peuvent pas installer à l’aide de la pip lorsqu’il est exécuté dans Azure.  Il est peut-être simplement que le package n’est pas disponible sur l’Index du Package Python.  Il pourrait être qu’un compilateur est requis (un compilateur n’est pas disponible sur l’ordinateur exécutant l’application web dans le Service d’application Azure).

Dans cette section, nous allons étudier comment faire face à ce problème.

### <a name="request-wheels"></a>Demande de roues

Si l’installation du package nécessite un compilateur, vous devez essayer de contacter le propriétaire du lot pour demander que les roues sont disponibles pour le package.

Avec la disponibilité récente du [Compilateur Microsoft Visual C++ pour les Python 2.7][], il est désormais plus facile de créer des lots qui ont du code natif pour les Python 2.7.

### <a name="build-wheels-requires-windows"></a>Générer des roues (nécessite Windows)

Remarque : Lorsque vous utilisez cette option, veillez à compiler le package à l’aide d’un environnement de Python qui correspond à la plate-forme / / version d’architecture qui est utilisée dans l’application web dans le Service d’application Azure (Windows/32-bit/2.7 ou 3.4).

Si le package ne s’installe pas, car elle requiert un compilateur, vous pouvez installer le compilateur sur votre ordinateur local et créer une roulette pour le package, puis inclure dans votre référentiel.

Utilisateurs Mac/Linux : Si vous n’avez pas accès à un ordinateur Windows, reportez-vous à la section [Création d’ordinateur virtuel exécutant Windows][] pour savoir comment créer un ordinateur virtuel sur Azure.  Vous pouvez l’utiliser pour générer des roues, les ajouter au référentiel et ignorer la machine virtuelle si vous le souhaitez. 

Pour les Python 2.7, vous pouvez installer le [Compilateur Microsoft Visual C++ pour les Python 2.7][].

Pour les Python 3.4, vous pouvez installer [Microsoft Visual C++ 2010 Express][].

Pour générer des roues, vous aurez besoin du package roue :

    env\scripts\pip install wheel

Vous allez utiliser `pip wheel` pour compiler une dépendance :

    env\scripts\pip wheel azure==0.8.4

Cette opération crée un fichier .whl dans le dossier \wheelhouse.  Ajouter les fichiers du dossier et de roue \wheelhouse pour votre référentiel.

Modifier votre requirements.txt d’ajouter le `--find-links` option en haut. Cette option indique à pip pour rechercher une correspondance exacte dans le dossier local avant de passer à l’index de package python.

    --find-links wheelhouse
    azure==0.8.4

Si vous souhaitez inclure toutes les dépendances dans le dossier \wheelhouse et pas utiliser du tout l’index de package python, vous pouvez forcer pip pour ignorer l’index de package en ajoutant `--no-index` en haut de votre requirements.txt.

    --no-index

### <a name="customize-installation"></a>Personnaliser l’installation

Vous pouvez personnaliser le script de déploiement pour installer un package dans l’environnement virtuel en utilisant un autre programme d’installation, tel que simple\_installer.  Consultez deploy.cmd pour obtenir un exemple qui est commenté.  Assurez-vous que ces packages ne sont pas répertoriés dans requirements.txt, pour empêcher le programme d’installation pip.

Ajouter au script de déploiement :

    env\scripts\easy_install somepackage

Vous pourrez également utiliser facilement\_install pour l’installer à partir d’un programme d’installation exe (certains sont zip compatible, facile\_installation prend en charge les).  Ajouter le programme d’installation pour votre référentiel et d’appeler facilement\_installer en passant le chemin d’accès au fichier exécutable.

Ajouter au script de déploiement :

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Inclure l’environnement virtuel dans le référentiel (nécessite Windows)

Remarque : Lorsque vous utilisez cette option, assurez-vous d’utiliser un environnement virtuel qui correspond à la plate-forme / / version d’architecture qui est utilisée dans l’application web dans le Service d’application Azure (Windows/32-bit/2.7 ou 3.4).

Si vous incluez l’environnement virtuel dans le référentiel, vous pouvez empêcher le script de déploiement gestion de l’environnement virtuel sur Azure en créant un fichier vide :

    .skipPythonDeployment

Nous vous recommandons de supprimer l’environnement virtuel existant sur l’application, afin d’empêcher les fichiers à partir de lorsque l’environnement virtuel a été géré automatiquement.


[Créer un ordinateur virtuel exécutant Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Compilateur Microsoft Visual C++ pour les Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
