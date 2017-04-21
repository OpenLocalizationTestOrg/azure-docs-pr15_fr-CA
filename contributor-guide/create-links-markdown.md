<properties
   pageTitle="Créer des liens dans les articles de démarque" description="Explique comment coder des croisements de démarque." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="02/03/2015" ms.author="tysonn" />

# <a name="linking-guidance-for-azure-technical-content"></a>Conseils pour Azure contenu technique de liaison

### <a name="links-from-one-acom-article-to-another"></a>Liens à partir d’un article ACOM à un autre

Pour créer un lien inline à partir d’un article technique ACOM à un autre article technique ACOM, utilisez la syntaxe de lien suivant :  

- Un article dans un service répertoire de liens à un autre article dans le même répertoire de service :

  `[link text](article-name.md)`

- Un article des liens à partir d’un sous-répertoire de service à un article dans le répertoire racine :

  `[link text](../article-name.md)`

- Un article dans les liens du répertoire racine à un article dans un sous-répertoire de service : 

  `[link text](./service-directory/article-name.md)`

- Un article dans un service sous-répertoire est liée à un article dans un autre sous-répertoire de service :

  `[link text](../service-directory/article-name.md)`
 

## <a name="links-to-anchors"></a>Liens vers des ancres

Vous n’êtes pas obligé de créer des points d’ancrage : ils sont générés automatiquement au moment pour tous les titres de H2 de publication. La seule chose que vous avez à faire est de créer des liens vers les sections de H2.

- Pour lier à un titre dans le même article :

  `[link](#the-text-of-the-H2-section-separated-by-hyphens)`  
  `[Create cache](#create-cache)`

- Pour lier à un point d’ancrage dans un autre article dans le même sous-répertoire :

  `[link text](article-name.md#anchor-name)`
  `[Configure your profile](media-services-create-account.md#configure-your-profile)`

- Pour lier à un point d’ancrage dans un autre sous-répertoire de service :

  `[link text](../service-directory/article-name.md#anchor-name)`
  `[Configure your profile](../service-directory/media-services-create-account.md#configure-your-profile)`

Une façon d’automatiser le processus de création de liens dans vos articles pour les liens générés automatiquement le point d’ancrage est [MarkdownAnchorLinkGenerator - un outil permettant de générer des liens d’ancrage pour ACOM dans le format approprié](https://github.com/Azure/Azure-CSI-Content-Tools/tree/master/Tools/ACOMMarkdownAnchorLinkGenerator).

## <a name="links-from-includes"></a>Inclut des liens à partir de

Inclure dans la mesure où les fichiers sont situés dans un autre répertoire, vous devez utiliser plus les chemins d’accès relatifs, comme illustré ci-dessous. Pour lier un article à partir d’un fichier include, utilisez ce format :

    [link text](../articles/service-folder/article-name.md)
    
Pour en savoir plus sur l’utilisation d’un fichier include dans les [directives d’extensions personnalisées démarque](custom-markdown-extensions.md#includes).

## <a name="links-in-selectors"></a>Liens de sélecteurs

Si vous avez des sélecteurs qui sont incorporés dans une inclusion, vous utilisez ce type de liaison : 

    > [AZURE. LISTE de la sélection (Dropdown1 | Dropdown2)]     -  [(Text1 | Exemple1)](../articles/service-folder/article-name1.md)
    - [(Text1 | Exemple2)](../articles/service-folder/article-name2.md)
    - [(Texte2 | Exemple3)](../articles/service-folder/article-name3.md)
    - [(Texte2 | Exemple4)](../articles/service-folder/article-name4.md)


## <a name="reference-style-links"></a>Référence des liens

Vous pouvez utiliser des liens de style de référence pour faciliter la lecture de votre contenu source. Les liens de style référence remplacent la syntaxe de liaison en ligne avec la syntaxe simplifiée qui permet de déplacer les URL longues à la fin de l’article. Voici un exemple de Daring boule de feu :

Texte de inline :

    I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].

Lier des références à la fin de l’article :

    <!--Reference links in article-->
    [1]: http://google.com/
    [2]: http://search.yahoo.com/  
    [3]: http://search.msn.com/

Veillez à qu'inclure l’espace après le signe deux-points, avant le lien. Lorsque vous liez à d’autres articles techniques, si vous avez oublié d’inclure un espace, va rompre le lien dans l’article publié. 

## <a name="link-to-acom-pages-that-are-not-part-of-the-technical-documentation-set"></a>Lien vers les pages ACOM qui ne font pas partie de la documentation technique

Pour créer un lien vers une page ACOM (par exemple, une page de tarification, SLA page ou tout autre élément qui n’est pas un article de la documentation), utiliser une URL absolue, en omettant les paramètres régionaux. L’objectif ici est que le travail des liens dans les GitHub et sur le site de rendu :

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)


## <a name="link-to-msdn-or-technet"></a>Lien vers MSDN ou TechNet

Lorsque vous avez besoin créer un lien vers MSDN ou TechNet, cliquez sur le lien total à la rubrique et supprimer l’en-us aux paramètres régionaux de langue à partir du lien. 

### <a name="use-friendly-link-text-for-all-links"></a>Utilisez un texte convivial pour tous les liens

Les mots que vous incluez dans un lien doivent être convivial, en d’autres termes, ils doivent être des mots anglais normales ou le titre de la page que vous établissez la liaison. N’utilisez pas « cliquez ici ». Il est incorrect de SEO et ne décrire de façon adéquate la cible.

**Corriger :**

- `For more information, see the [contributor guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

- `For more details, see the [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx) reference.`

**Incorrect :**

- `For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).`

- `For more information, click [here](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`


## <a name="fwlinks"></a>FWLinks

Évitez de FWLinks (notre système de redirection) dans le contenu de azure.microsoft.com. Ils doivent être utilisés uniquement en dernier recours lorsque vous avez besoin créer un lien vers une page dont vous ne connaissez pas encore l’URL. Elles ne sont pratiquement jamais réellement nécessaires. Pour ACOM, vous définissez le nom du fichier, afin que vous sachiez qu’il sera à l’avance. Pour une rubrique de la bibliothèque qui n’est pas encore publiée, vous pouvez créer un lien qui utilise la GUID de la rubrique de sorte que vous n’êtes pas obligé d’utiliser un FWLink.

Si vous devez utiliser un FWLink sur une page web, incluez le paramètre P pour en faire une redirection permanente :

    http://go.microsoft.com/fwlink/p/?LinkId=389595

Lorsque vous collez l’URL cible dans l’outil FWLink, n’oubliez pas de supprimer les paramètres régionaux si votre lien cible est bibliothèque ACOM, ou MSDN ou TechNet.

## <a name="remember-the-azure-library-chrome"></a>N’oubliez pas du chrome bibliothèque Azure !
Si vous souhaitez créer un lien vers une rubrique de la bibliothèque Azure qui réside sous [ce nœud](https://msdn.microsoft.com/library/azure), n’oubliez pas de spécifier l’Azure chrome dans le lien (azure /). Le chrome Azure partage les options de navigation ACOM et affiche uniquement le contenu Azure de MSDN library. Un lien correctement étendue ressemble à ceci :

    http://msdn.microsoft.com/library/azure/dd163896.aspx

Dans le cas contraire, la page affichera dans la vue standard de MSDN, l’ensemble arbre de MSDN.

### <a name="contributors-guide-links"></a>Liens du Guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)

<!--image references-->
[1]: ./media/create-tables-markdown/table-markdown.png
[2]: ./media/create-tables-markdown/break-tables.png
