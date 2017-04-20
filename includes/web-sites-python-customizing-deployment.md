Azure détermine que votre application utilise les Python **si les deux conditions suivantes sont remplies**:

- fichier Requirements.txt dans le dossier racine
- n’importe quel fichier .py dans le dossier racine ou un runtime.txt qui spécifie les python

Lorsque c’est le cas, il utilise un script de déploiement spécifiques de Python, qui effectue la synchronisation standard de fichiers, ainsi que des opérations de Python supplémentaires telles que :

- Gestion automatique de l’environnement virtuel
- Installation des packages répertoriés dans requirements.txt à l’aide du pip
- Création du fichier web.config approprié en fonction de la version sélectionnée de Python.
- Collecter les fichiers statiques pour des applications Django

Vous pouvez contrôler certains aspects de la procédure de déploiement par défaut sans avoir à personnaliser le script.

Si vous souhaitez ignorer toutes les étapes de déploiement spécifiques de Python, vous pouvez créer ce fichier vide :

    \.skipPythonDeployment

Pour mieux contrôler le déploiement, vous pouvez remplacer le script de déploiement par défaut en créant les fichiers suivants :

    \.deployment
    \deploy.cmd

Vous pouvez utiliser l' [interface de ligne de commande Azure][] pour créer les fichiers.  Utilisez cette commande à partir de votre dossier de projet :

    azure site deploymentscript --python

Lorsque ces fichiers n’existent pas, Azure crée un script de déploiement temporaire et l’exécute.  Il est identique à celui que vous créez à l’aide de la commande ci-dessus.

[Interface de ligne de commande Azure]: http://azure.microsoft.com/downloads/
