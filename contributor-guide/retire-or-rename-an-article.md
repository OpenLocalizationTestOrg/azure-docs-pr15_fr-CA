# <a name="steps-to-follow-when-you-retire-or-change-the-name-of-an-acom-technical-article"></a>Étapes à suivre lorsque vous retirer ou modifiez le nom d’un article technique ACOM

Ces instructions sont fournies pour les PME qui est répertoriés comme l’auteur d’un article qui doit être retiré de la section de la documentation technique de azure.microsoft.com. Les étapes s’appliquent également si un fichier est renommé.

Si vous êtes un membre de notre communauté Azure et si vous pensez qu’un article doit être retiré pour une raison quelconque, laissez un commentaire dans le flux de commentaire de Disqus pour l’article informer l’auteur que quelque chose ne va pas avec l’article.

Les auteurs de PME doivent suivre plusieurs étapes pour désactiver correctement contenu afin que les utilisateurs du site Web n’ont pas une mauvaise expérience lorsque nous mettre hors service le contenu à partir du site. Suppression de l’article ou de la modification de son nom doit être la dernière chose qui se passe !

## <a name="step-1-set-the-article-to-no-indexno-follow-and-republish-it-recommended"></a>Étape 1 : Définir l’article d’index non-suivre non et republier (recommandé)

La première chose à faire est de republier l’article en tant que non-index/non-suivez quelques semaines avant de le supprimer réellement. Ceci est considéré comme la meilleure pratique « préliminaires » de suppression de contenu. Cette opération supprime l’article index des moteurs de recherche afin que les personnes ne trouvera pas l’article dans la recherche. [Consultez le wiki interne pour plus de détails.](https://microsoft.sharepoint.com/teams/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx)

## <a name="step-2-manage-inbound-links-required"></a>Étape 2 : Gérer les liens entrants (requis)

Déterminer s’il existe des liens entrants de non Microsoft à votre contenu. Fréquemment, blogs, forums et autres contenus sur le web pointe vers les articles. Fréquemment, vous pouvez travailler avec les propriétaires du blog pour changer ces liens, et vous pouvez supprimer ou mettre à jour les liens à partir des publications de forums. Outils d’analytique Web peuvent vous indiquer s’il existe des liens entrants de trafic élevé que vous devrez peut-être gérer de cette façon.

## <a name="step-3-remove-all-crosslinks-to-the-article-from-the-technical-content-repository-required"></a>Étape 3 : Suppression de tous les croisements l’article à partir du référentiel de contenu technique (obligatoire)

Ne comptez pas sur les redirections de s’occuper de croisements d’autres articles. Mise à jour ou supprimer la croix fait référence à l’article que vous supprimez ou renommez, y compris dans les articles détenus par d’autres personnes.

1. Assurez-vous que vous travaillez dans une succursale locale à jour – exécuter `git pull upstream master` (ou la variante appropriée sur cette commande.

2.  Analyser le dossier azure-contenu-pr/articles et le dossier azure-contenu-pr/inclut des articles et comprend que le lien vers l’article que vous voulez retirer et supprimer les croisements ou les remplace par un croisements de nouveau approprié. Vous pouvez utiliser une recherche et remplacer l’utilitaire pour trouver les croisements si vous avez installé. Si vous ne le faites pas, vous pouvez utiliser Windows PowerShell gratuitement ! Voici comment utiliser PowerShell pour trouver les croisements :

 une barre d’outils. Démarrez Windows PowerShell.

 b. À l’invite de PowerShell, passez dans le dossier azure-contenu-pr\articles :

 `cd azure-content-pr\articles`

 c. Tapez cette commande, qui répertorie tous les fichiers qui contiennent une référence à l’article que vous supprimez :

 `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name`

  Si vous préférez envoyer la liste des noms de fichiers dans un fichier texte (dans ce psoutput.txt cas, nommé), vous pouvez :

  `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name | Out-File C:\Users\<your account>\psoutput.txt`

3. Ajouter et valider toutes vos modifications, les pousser à votre branche et créer une demande d’extraction pour déplacer vos modifications à partir de votre branche vers la branche principale du référentiel principal.

## <a name="step-4-update-the-fwlink-tool-required"></a>Étape 4 : Mettre à jour l’outil FWLink (obligatoire)

Recherchez l’outil FWLink tout FWLinks qui peut pointer vers l’article. Pointez tout FWLinks au contenu de remplacement ; Si vous n’êtes pas sur l’alias auquel appartient le lien, la joindre. Si les propriétaires de ne mettre à jour le lien, un ticket avec MSCOM pour que le lien de modification du fichier. Plus d’infos - [wiki interne](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Manage%20inbound%20links%20to%20retired%20topics.aspx).

## <a name="step-5-remove-all-crosslinks-to-the-article-from-other-pages-on-azuremicrosoftcom-and-create-a-redirect-for-the-retired-page-if-appropriate-required"></a>Étape 5 : Supprimer tous les croisements l’article à partir d’autres pages sur azure.microsoft.com et créer une redirection pour la page déclassée, si approprié (obligatoire)

Vous devrez travailler avec la personne qui gère et met à jour de la page de lancement de documentation à votre service pour cet article. Contactez votre partenaire de l’équipe de contenu si vous ne savez pas qui est cette personne. La personne qui gère et met à jour la page de lancement doc devez faire deux choses :

1. Dans Visual Studio, analyse **l’ensemble** ACOM web de la solution pour des références croisées à retirer le fichier. Supprimer des références croisées ou les remplacer par une référence croisée mis à jour. Vous devez supprimer les liens HTML ainsi que les chaînes de ressource associée pour les liens HTML. Plus d’Infos - consultez le [wiki interne](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Create%20or%20edit%20a%20service%20landing%20page%20or%20left%20nav.aspx)

2. Si un article de remplacement existe, créez une redirection. Plus d’Infos - consultez le [wiki interne](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx).

3. Vérifiez les modifications dans le référentiel.

## <a name="step-6-retire-the-article"></a>Étape 6 : Supprimer l’article

Une fois que vous avez terminé les étapes précédentes, et ces modifications sont actives, vous pouvez supprimer l’article à partir du référentiel. 

**Important :** Lorsque vous supprimez des fichiers, vous devez utiliser le `git add --all` commande.

## <a name="step-7-remove-links-from-msdn-required"></a>Étape 7 : Supprimer les liens à partir de MSDN (obligatoire)

Passez en revue l’outil d’assurance qualité contenu les liens rompus à la rubrique supprimée ou renommée et supprimer/corriger les liens dans toutes les rubriques MSDN affectés.

## <a name="step-8-remove-cached-pages-from-search-engines-only-if-absolutely-necessary"></a>Étape 8 : Supprimer les pages mises en cache à partir de moteurs de recherche (uniquement si cela est absolument nécessaire)

Effectuez ce uniquement si le contenu doit être supprimé rapidement en raison de problèmes de client juridique ou grave. Par les méthodes conseillées à partir de Google, suppressions de page priorité normale doivent simplement être gérées par processus de moteur de recherche naturelle. Accédez à ces pages web à supprimer les pages web mises en cache à partir de moteurs de recherche :

[Bing](https://www.bing.com/webmaster/tools/content-removal?rflid=1)
[Google](https://www.google.com/webmasters/tools/removals?pli=1)


### <a name="contributors-guide-links"></a>Liens du guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)
