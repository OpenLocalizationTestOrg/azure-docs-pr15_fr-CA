# <a name="azure-technical-documentation-contributor-guide"></a>Guide du contributeur Azure Documentation technique

Vous avez trouvé le référentiel GitHub qui héberge la source de la documentation technique qui est publiée sur le centre de Documentation d’Azure à [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Ce référentiel contient également des conseils pour vous aider à contribuer à notre documentation technique.  Pour obtenir une liste des articles du guide des collaborateurs, consultez [l’index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Contribuer à la documentation d’Azure

Nous vous remercions de votre intérêt dans la documentation Azure !

* [Moyens de contribute](#ways-to-contribute)
* [Code de conduite](#code-of-conduct)
* [À propos de vos contributions au contenu Azure](#about-your-contributions-to-azure-content)
* [Organisation du référentiel](#repository-organization)
* [Utiliser ce référentiel, Git et GitHub](#use-github-git-and-this-repository)
* [L’utilisation de démarque pour mettre en forme votre rubrique](#how-to-use-markdown-to-format-your-topic)
* [Commentaires, les commentaires et la prise en charge](./contributor-guide/feedback-and-comments.md)
* [Plus de ressources](#more-resources)
* [Index des articles du guide des tous les collaborateurs](./contributor-guide/contributor-guide-index.md) (ouvre la nouvelle page)

## <a name="ways-to-contribute"></a>Moyens de contribute 

Vous pouvez contribuer aux [Azure documentation](http://azure.microsoft.com/documentation/) de différentes manières :

* Participer à un [forum de discussion](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Soumettre des commentaires Disqus au bas des articles.
* Vous pouvez facilement contribuer à des articles techniques dans l’interface utilisateur de GitHub. Soit de trouver l’article dans ce référentiel, ou reportez-vous à l’article sur [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) et cliquez sur le lien dans l’article passe à la source de GitHub pour l’article.
* Si vous apportez des modifications substantielles à un article existant, ajout ou modification d’images ou faisant partie d’un nouvel article, vous devez une branche dans ce référentiel, installer Git Bash, pavé de démarque et apprendre à utiliser certaines commandes git.

##<a name="code-of-conduct"></a>Code de conduite

Ce projet a adopté le [Microsoft Open Source Code de conduite](https://opensource.microsoft.com/codeofconduct/). Pour plus d’informations, consultez le [Code de conduite du forum](https://opensource.microsoft.com/codeofconduct/faq/) ou le contact [opencode@microsoft.com](mailto:opencode@microsoft.com) avec des questions supplémentaires ou des commentaires.

##<a name="about-your-contributions-to-azure-content"></a>À propos de vos contributions au contenu Azure

###<a name="minor-corrections"></a>Corrections mineures

Des corrections mineures ou des éclaircissements que vous soumettez pour les exemples de code et de la documentation dans cette mis en pension sont couverts par l' [Azure site Web conditions d’utilisation (conditions d’utilisation)](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###<a name="larger-submissions"></a>Envois de plus grandes

Si vous soumettez une demande d’extraction avec des nouvelles ou des modifications à la documentation et les exemples de code, nous vous ferons parvenir un commentaire dans GitHub vous invitant à envoyer un accord de licence en ligne Contribution (CLA) si vous êtes dans un de ces groupes :

* Membres du groupe de Technologies ouvertes de Microsoft.
* Collaborateurs qui ne travaillent pour Microsoft.

Nous devons vous permet de terminer le formulaire en ligne avant que nous pouvons accepter votre demande d’extraction.

Tous les détails sont disponibles à l’adresse [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## <a name="repository-organization"></a>Organisation du référentiel

Le contenu dans le référentiel de contenu azure provient de l’organisation de la documentation sur [Azure.Microsoft.com](http://azure.microsoft.com). Ce référentiel contient deux dossiers racine :

### <a name="articles"></a>\articles

Le dossier *\articles* contient les articles de la documentation au format démarque avec une extension *.md* .

Articles dans le répertoire racine sont publiés à Azure.Microsoft.com dans le chemin d’accès *http://azure.microsoft.com/documentation/articles/ {l’article-nom-sans-md} /*.

* **Noms de fichiers d’article :** Consultez [nos conseils d’attribution de noms de fichier](./contributor-guide/file-names-and-locations.md).

Articles dans leur propre dossier de service sont publiés à Azure.Microsoft.com dans le chemin d’accès *http://azure.microsoft.com/documentation/articles/service-folder/ {l’article-nom-sans-md} /*

* **Les sous-dossiers Media :** Le dossier *\articles* contient le dossier *\media* pour les fichiers de média directory article racine, à l’intérieur qui sont des sous-dossiers avec les images pour chaque article.  Les dossiers de service contiennent un dossier de médias distincts pour les articles dans chaque dossier de service. Les dossiers d’image de l’article sont nommés de manière identique au fichier de l’article, moins l’extension de fichier *.md* .

### <a name="includes"></a>\Includes

Vous pouvez créer des sections de contenu réutilisables à inclure dans un ou plusieurs articles. Reportez-vous à la section [extensions personnalisées utilisées dans notre contenu technique](./contributor-guide/custom-markdown-extensions.md).

### <a name="markdown-templates"></a>modèles de \markdown

Ce dossier contient notre modèle de démarque standard avec la mise en forme de base démarque vous avez besoin d’un article.

### <a name="contributor-guide"></a>\Contributor-Guide

Ce dossier contient des articles qui font partie du guide des nos collaborateurs.  

## <a name="use-github-git-and-this-repository"></a>Utiliser ce référentiel, Git et GitHub

Pour plus d’informations sur comment contribuer, l’utilisation de la GitHub UI pour y apporter des petites modifications et à une branche et cloner le référentiel pour plus importantes contributions, consultez [installer et configurer les outils de création de GitHub](./contributor-guide/tools-and-setup.md).

Si vous installez GitBash et que vous choisissez de travailler localement, les étapes de la création d’une nouvelle branche de travail local, apporter des modifications et soumettre les modifications sur la branche principale sont répertoriées dans le [Git des commandes pour la création d’un nouvel article ou de mise à jour d’un article existant](./contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>Branches

Nous vous recommandons de créer des branches de travail local qui ciblent une étendue spécifique de modification. Chaque branche doit être limité à un seul article/concept à la fois pour rationaliser les flux de travail et de réduire les risques de conflits de fusion.  Les efforts suivants sont l’étendue appropriée pour une nouvelle branche :

* Un nouvel article (et les images associées)
* Modifications de l’orthographe et de grammaire sur un article.
* Application d’une modification de mise en forme unique sur un ensemble étendu d’articles (par exemple, nouveau copyright pied de page).

## <a name="how-to-use-markdown-to-format-your-topic"></a>L’utilisation de démarque pour mettre en forme votre rubrique

Tous les articles dans ce référentiel utilisent démarque AROMATISES de GitHub.  Voici une liste de ressources.

- [Notions de base de démarque](https://help.github.com/articles/markdown-basics/)

- [Fiche récapitulative des promotions imprimable](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Pour notre liste d’éditeurs de démarque, consultez les [Outils et la configuration](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## <a name="article-metadata"></a>Métadonnées de l’article

Les métadonnées de l’article permet de certaines fonctionnalités sur le site web de azure.microsoft.com, attribution d’auteur, attribution de collaborateur, Ariane, description de l’article, des SEO optimisations aussi bien que la création de rapports Microsoft utilise pour évaluer les performances du contenu. Par conséquent, les métadonnées sont importante ! [Voici le Guide pour vous assurer que vos métadonnées s’effectue vers la droite](./contributor-guide/article-metadata.md).

### <a name="labels"></a>Étiquettes

Automatisée d’étiquettes attribuée à extraire des demandes pour nous aider à gérer le workflow de demande d’extraction et vous permettent de savoir ce qui se passe avec votre demande d’extraction :

* Contrat de licence de contribution liés
    * CLA non requis : la modification est relativement mineure et n’exige pas que vous signez un CLA.
    * CLA obligatoires : l’étendue de la modification est relativement important et nécessite que vous vous connectez à un CLA.
    * signé-CLA : le collaborateur signé le CLA, afin que la demande d’extraction peut maintenant se déplacer en avant pour la révision.
* Pilier étiquettes : étiquettes telles que PnP, applications modernes et CDC permettent de classer les demandes d’extraction par l’organisation interne qui doit réviser la demande d’extraction.
* Envoyé à l’auteur de modifications : l’auteur a été informé de la demande d’extraction en attente.

## <a name="more-resources"></a>Plus de ressources

Consultez l' [index du guide de nos collaborateurs](./contributor-guide/contributor-guide-index.md) pour toutes nos rubriques.
