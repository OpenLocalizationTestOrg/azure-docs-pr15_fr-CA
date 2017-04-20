1. Dans **l’Explorateur de solutions**Visual Studio, cliquez sur le projet, puis sélectionnez **Ajouter > prise en charge de Docker** dans le menu contextuel.

    ![Ajouter du menu contextuel de prise en charge de Docker](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. Ajout de la prise en charge de Docker à un noyau d’ASP.NET projet web entraîne l’ajout de plusieurs fichiers liés à la Docker est ajouté au projet, y compris composer de Docker les fichiers, les scripts de déploiement de Windows PowerShell et les fichiers de propriétés de Docker. 

    ![Fichiers de docker ajoutés à un projet](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE]Si vous utilisez le [Docker pour Windows version bêta](https://beta.docker.com), ouvrez Properties\Docker.props, supprimez la valeur par défaut et redémarrez Visual Studio pour la valeur prenne effet.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
