Le script de déploiement ignorera la création de l’environnement virtuel sur Azure s’il détecte qu’un environnement virtuel compatible existe déjà.  Cela peut accélérer considérablement le déploiement.  Les packages qui sont déjà installés seront ignorés par pip.

Dans certaines situations, vous souhaiterez peut-être forcer la suppression de cet environnement virtuel.  Vous voudrez effectuer cette opération si vous décidez d’inclure un environnement virtuel en tant que partie de votre référentiel.  Vous pouvez également effectuer cette opération si vous avez besoin pour vous débarrasser de certains packages ou tester les modifications apportées à requirements.txt.

Il existe quelques options pour gérer l’environnement virtuel existant sur Azure :

### <a name="option-1-use-ftp"></a>Option 1 : Utilisez FTP

Avec un client FTP, se connecter au serveur, et vous serez en mesure de supprimer le dossier environnement.  Notez que certains clients FTP (par exemple, les navigateurs web) peuvent être en lecture seule et ne permettent pas de supprimer des dossiers, et il vous faudra veiller à utiliser un client FTP avec cette fonctionnalité.  Le nom d’hôte FTP et l’utilisateur sont affichés dans les lames de votre application web sur le [Portail Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Option 2 : Bascule runtime

C’est une alternative qui tire parti du fait que le script de déploiement va supprimer le dossier env lorsqu’elle ne correspond pas à la version souhaitée de Python.  Cela va supprimer l’environnement existant et créer un nouveau.

1. Basculer vers une autre version de Python (via runtime.txt ou la lame de **Paramètres d’Application** dans Azure Portal)
1. GIT push certaines modifications (ignorer les erreurs d’installation pip le cas échéant)
1. Revenir à la version initiale de Python
1. GIT push certaines modifications à nouveau

### <a name="option-3-customize-deployment-script"></a>Option 3 : Personnaliser le script de déploiement

Si vous avez personnalisé le script de déploiement, vous pouvez modifier le code afin de l’obliger à supprimer le dossier env deploy.cmd.
