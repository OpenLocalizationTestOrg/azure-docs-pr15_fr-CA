<properties pageTitle="Commandes GIT pour la création d’un nouvel article ou de mise à jour d’un article existant" description="Étapes de travail avec la technique d’Azure GitHub référentiels de contenu." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="01/16/2015" ms.author="tysonn" />

# <a name="git-commands-for-creating-a-new-article-or-updating-an-existing-article"></a>Commandes GIT pour la création d’un nouvel article ou de mise à jour d’un article existant


## <a name="standard-process-working-from-master"></a>Processus standard (utilisation du masque)
Suivez les étapes décrites dans cet article pour créer une branche de travail local sur votre ordinateur de sorte que vous pouvez créer un nouvel article pour la section de la documentation technique de azure.microsoft.com ou mettre à jour un article existant.

1. Démarrez Git Bash (ou l’outil de ligne de commande que vous utilisez pour Git).

 **Remarque :** Si vous travaillez dans le référentiel public, modifier azure-contenu-pr azure-contenu dans toutes les commandes.

2. Remplacez azure-contenu-pr :

        cd azure-content-pr
3. Découvrez la branche principale :

        git checkout master

4. Créer une branche de travail local fraîches dérivée à partir de la branche principale :

        git pull upstream master:<working branch>


5. Déplacer dans la nouvelle branche de travail :

        git checkout <working branch>

6. Informez votre branche créée l’agence de travail :

        git push origin <working branch>

7. Créer votre nouvel article ou apporter des modifications à un article existant. Utilisez l’Explorateur Windows pour ouvrir et créer de nouveaux fichiers de démarque et utiliser Atom (http://atom.io) en tant qu’éditeur de démarque. Après avoir créé ou modifié votre article et les images, passez à l’étape suivante.

8. Ajouter et valider les modifications apportées :

        git add .
        git commit –m "<comment>"
        
   Ou bien, pour ajouter uniquement les fichiers modifiés :

        git add <file path>
        git commit –m "<comment>"

   Si vous avez supprimé les fichiers, vous devez utiliser ceci :
   
        git add --all
        git commit -m "<comment>"

9. Mise à jour de votre branche de travail local avec les modifications en amont :

        git pull upstream master

10. Distribuer les modifications de votre branche sur GitHub :

        git push origin <working branch>

12. Lorsque vous êtes prêt à présenter votre contenu vers la branche principale en amont de la zone de transit, validation, et/ou de publication, dans l’UI GitHub, créez une demande d’extraction à partir de votre branche vers la branche principale.

13. Si vous êtes un employé travaillant dans le référentiel privé, les modifications que vous envoyez sont automatiquement transférées et un lien de zone de transit est écrite à la demande d’extraction. Veuillez consulter votre contenu nouvellement créé et approbation dans les commentaires de demande d’extraction en ajoutant le commentaire **#signe hors tension** .  Cela indique que les modifications sont prêtes à être poussé live.  Si vous ne souhaitez pas que la demande d’extraction pour être accepté - si vous sont simplement mis en œuvre des modifications - Ajouter la note **#hold-off** à la demande d’extraction.

14. L’accepteur de demande d’extraction examine votre demande d’extraction, fournit des commentaires et/ou accepte votre demande d’extraction. 

15. Vous pouvez également vérifier votre article publié ou les modifications apportées à

 http://Azure.Microsoft.com/documentation/articles/*name-of-your-article-without-the-MD-extension*

## <a name="publishing"></a>Publication

- Articles sont publiés à environ 10 h 00 et 3 h 00 heure du Pacifique, du lundi au vendredi. Il peut prendre jusqu'à 30 minutes pour apparaître en ligne après la publication des articles. N’oubliez pas que votre demande d’extraction doit être fusionnées par un réviseur de demande d’extraction avant que les modifications peuvent être incluses dans la prochaine publication planifiée s’exécutée. Vous devez travailler avec votre réviseur de demande d’extraction à l’avance afin de garantir qu'une demande d’extraction est fusionnée pour une publication spécifique s’exécutée. Dans le cas contraire, PRs sont passés en revue dans l’ordre qu’ils ont été soumis.

- Si vous êtes un employé qui travaille dans le référentiel privé, toutes les requêtes d’extraction sont soumis aux règles de validation qui doivent être résolus avant la demande d’extraction peut être fusionnée. 

## <a name="working-with-release-branches"></a>Travail avec des branches de version

Lorsque vous travaillez avec une branche de la version, la meilleure façon de créer une branche de travail locale de la branche de la version est d’utiliser la syntaxe de cette commande :

    git checkout upstream/<upstream branch name> -b <local working branch name>

Cette opération crée la succursale locale directement à partir de la branche en amont, en évitant toute fusion locale.

