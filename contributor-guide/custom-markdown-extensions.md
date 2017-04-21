<properties
    title="required"
    pageTitle="Extensions de démarque personnalisées utilisées dans nos articles techniques"
    description="Répertorie les extensions de démarque personnalisés qui permettent des vidéos incorporées, notes et conseils, contenu réutilisable et autres éléments figurant dans les articles techniques sur azure.microsoft.com."
    services=""
    solutions=""
    documentationCenter=""
    authors="tysonn"
    manager="carolz"
    editor=""/>

<tags
    ms.service="contributor-guide"
    ms.devlang=""
    ms.topic="article"
    ms.tgt_pltfrm=""
    ms.workload=""
    ms.date="01/22/2015"
    ms.author="tysonn"/>

## <a name="markdown-for-azuremicrosoftcom"></a>Démarque pour Azure.microsoft.com

Pour obtenir des conseils généraux de démarque, consultez [Notions de base de démarque](https://help.github.com/articles/markdown-basics/) et notre [Fiche récapitulative de démarque](./media/documents/markdown-cheatsheet.pdf?raw=true). Si vous avez besoin créer des liaisons croisées d’article de démarque, voir le [liaison] (. / create-links-markdown.md#markdown-syntax-for-acom-relative-links.md/).

Azure.Microsoft.com prend en charge les [extraits de blocs de code](https://help.github.com/articles/github-flavored-markdown/#fenced-code-blocks) et de [mise en surbrillance de la syntaxe](https://help.github.com/articles/github-flavored-markdown/#syntax-highlighting). Toutefois, ACOM prend en charge qu’une syntaxe mise en surbrillance du jeu de couleurs, indépendamment de la langue que vous spécifiez dans un bloc de code.

## <a name="custom-markdown-extensions-used-in-our-technical-articles"></a>Extensions de démarque personnalisées utilisées dans nos articles techniques

Nos articles d’utilisent démarque AROMATISES de GitHub pour mettre en forme la plupart des article - paragraphes, des liens, des listes, des en-têtes, etc.. Mais nous utilisons les extensions personnalisées démarque où nous avons besoin d’une mise en forme plus riches dans les pages de rendu sur azure.microsoft.com. Voici les extensions que nous utilisons :

+ [Remarques et conseils]
+ [Inclut les]
+ [Vidéos incorporées]
+ [Sélecteurs de technologie et de la plate-forme]

## <a name="notes-and-tips"></a>Remarques et conseils

Vous pouvez choisir parmi 4 types de remarques et conseils :

- AZURE. REMARQUE
- AZURE. AVERTISSEMENT
- AZURE. TIPss
- AZURE. IMPORTANT

###<a name="usage"></a>Utilisation de
En règle générale, utilisez des remarques et conseils avec parcimonie tout au long de vos articles. Lorsque vous les utilisez, choisissez le type de note ou de conseil approprié :

- Utilisez AZURE. Remarque pour mettre en évidence des informations neutres ou positives qui soulignent ou complètent les points importants du texte principal. Une note fournit des informations qui s’appliquent uniquement dans des cas particuliers.

  ![](./media/custom-markdown-extensions/Notes-note.PNG)

- Utilisez AZURE. AVERTISSEMENT pour alerter l’utilisateur d’une condition susceptible de provoquer un problème à l’avenir. Par exemple, sélection d’une option de certaine ou de rendre un certain choix peut définitivement vous limiter à un scénario particulier.

  ![](./media/custom-markdown-extensions/Notes-warning.PNG)

- Utilisez AZURE. Conseil pour aider vos utilisateurs à appliquer les techniques et les procédures décrites dans le texte à leurs besoins spécifiques. Une info-bulle peut également suggérer des méthodes alternatives qui peuvent ne pas être évidentes. Conseils, toutefois, ne sont pas essentiels à la compréhension de base du texte.

  ![](./media/custom-markdown-extensions/Notes-tip.PNG)

- Utilisez AZURE. Il est IMPORTANT de fournir des informations qui sont essentielles à l’exécution d’une tâche.

  ![](./media/custom-markdown-extensions/Notes-important.PNG)

Ces remarques et les conseils prend en charge les liens, des images, des listes et des blocs de code, essayez de conserver vos notes et les conseils des plus simples. Si vous créez des notes complexes avec beaucoup de mise en forme, qui peut être le signe que vous n’avez besoin que d’une autre section dans le texte principal de l’article. Et bien, trop de notes dans un article peuvent être gênants et difficile à analyser ou à lire.

###<a name="sample-markdown"></a>Démarque de l’échantillon

Les exemples de tous les affichent une AZURE. REMARQUE. Pour utiliser une astuce, avertissement ou IMPORTANT, remplacez « NOTE » dans la démarque :

    > [AZURE.TIP]

    > [AZURE.WARNING]

    > [AZURE.IMPORTANT]

Paragraphe :

    > [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Microsoft Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes.

Multiparagraph :

    > [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Microsoft Azure actif.
    >
    > Si vous n’avez pas un compte, vous pouvez [créer un compte d’essai gratuit](http://www.windowsazure.com/pricing/free-trial/) dans quelques minutes.

## <a name="includes"></a>Inclut les

Texte réutilisable dans notre référentiel GitHub réside dans les fichiers de ce que nous appelons « contient ». Une fois que le texte qui doit être utilisé dans plusieurs articles, vous incluez une référence à ce fichier d’informations réutilisables. L’inclure lui-même est un fichier simple démarque (.md). Il peut contenir n’importe quel démarque valide, y compris le texte, les liens et les images. Tous incluent démarque fichiers doivent se trouver dans [le comprend le répertoire](https://github.com/Azure/azure-content/tree/master/includes) à la racine du référentiel. Lorsque l’article est publié, le texte à inclure s’intègrent dans la rubrique publiée.

- Nous utilisons une syntaxe spécifique à une inclusion de référence.

- Fichiers multimédias que vous placez dans une inclusion doivent être créés dans un dossier de supports spécifiques à inclure. Inclut des dossiers de support pour appartiennent dans [le dossier azure-contenu/inclut/media](https://github.com/Azure/azure-content/tree/master/includes/media). Le répertoire de support ne doit pas contenir des images dans la racine. Si l’inclusion n’a pas d’images, un répertoire de média correspondant n’est pas requis.

###<a name="usage"></a>Utilisation de

- Utilisation inclut où vous souhaitez le même texte à afficher dans plusieurs articles.

- Inclut sont destinés à être utilisés pour des quantités importantes de contenu - un paragraphe ou deux, une procédure partagée ou une section partagée. Ne les utilisez pas pour quoi que ce soit inférieure à celle d’une phrase ; **ils ne sont pas des noms de produits**.

- Assurez-vous que tout le texte dans une inclusion est écrit des phrases complètes ou des expressions qui ne dépendent pas de texte précédent ou suivant dans l’article qui fait référence à l’inclure. Sans tenir compte de ce guide de crée une chaîne non convertible dans l’article qui divise l’expérience localisée. 

- Ne pas incorporer inclut les autres inclut. Ils ne sont pas pris en charge par le système de publication de DPS.

- Ne pas partager des médias entre les fichiers. Utilisez un fichier séparé avec un nom unique pour chaque article et les inclure. Stocker le fichier multimédia dans le dossier de média associé à l’inclure.

- N’utilisez pas une inclusion en tant que seul le contenu d’un article.  Inclut sont censées être supplémentaire pour le contenu dans le reste de l’article.

- Comme toutes les inclusions doit figurer dans le comprend le répertoire, le chemin d’accès à une inclusion d’un article est toujours

    .. comprend

- Ne répétez pas une référence de nom de fichier de lien ou une image dans l’article et de l’inclure. Ajouter «-inclure » pour le nom du fichier du lien référence ou support pour éviter la répétition de la référence :

 **Référence de lien**

 Modification : odata.org à : odata.org-include

 **Référence de l’image**

 Modification de : table.png à : include.png de la table

###<a name="sample-markdown"></a>Démarque de l’échantillon
La syntaxe pour l’ajout d’une inclusion dans un article de la documentation est la suivante :

    [AZURE.INCLUDE [include-short-name](../includes/include-file-name.md)]

Exemple

    [AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

La première partie de l’inclure est l’inclure le nom sans le chemin d’accès et sans extension .md. La deuxième partie est le chemin d’accès relatif à l’inclure dans le répertoire, avec l’extension .md de comprend.

###<a name="rendering"></a>Rendu

Dans la page rendue de GitHub, l’inclusion sera restitué comme suit :

 [AZURE. INCLURE le stockage d’objets blob faire]

Dans le code HTML restitué sur azure.microsoft.com, le code HTML de l’inclut est fusionné dans le reste du document HTML. Toutefois, le code HTML contient un code HTML commentaire avec l’original incluent la démarque de nom de fichier et le hachage de validation GitHub. Ce commentaire est inclus pour des fins de dépannage afin que le contenu de la source peut être facilement identifié et trouvé dans GitHub :

  ![](./media/custom-markdown-extensions/include.png)


## <a name="embedded-videos"></a>Vidéos incorporées

Nos articles techniques prennent en charge les vidéos embeddeded articles techniques tant que les vidéos se trouvent sur le site [Channel 9](http://channel9.msdn.com/) de Microsoft. Les vidéos de Channel 9 doivent être intégrés à [l’azure.microsoft.com Centre de vidéo](http://azure.microsoft.com/documentation/videos/home/). Nous avons actuellement ne pas prennent en charge les vidéos de YouTube incorporées ; Si vous êtes un collaborateur de la Communauté, n’hésitez pas à créer un lien vers YouTube si la vidéo que vous voulez ajouter la validation il. Les collaborateurs de Microsoft doivent utiliser Channel 9 et sur le centre de la vidéo.

### <a name="usage"></a>Utilisation de

- Assurez-vous que la vidéo est au centre de la vidéo.

- Copiez l’ID de vidéo à partir de l’URL conviviale de la vidéo sur Channel 9, ou à partir du centre de vidéo Azure. Par exemple, l’ID de vidéo pour la vidéo à [http://azure.microsoft.com/documentation/videos/azure-scheduler-unusual-schedules/](http://azure.microsoft.com/documentation/videos/azure-scheduler-unusual-schedules/) est **azure planificateur-inhabituels-planifie**.

### <a name="syntax"></a>Syntaxe

    > [AZURE.VIDEO video-id-string]

### <a name="rendering"></a>Rendu

Sur GitHub : [https://github.com/Azure/azure-content-pr/blob/master/articles/web-sites-backup.md](https://github.com/Azure/azure-content-pr/blob/master/articles/web-sites-backup.md)

Article publié : [http://azure.microsoft.com/documentation/articles/web-sites-backup/](http://azure.microsoft.com/documentation/articles/web-sites-backup/)


## <a name="technology-and-platform-selectors"></a>Sélecteurs de technologie et de la plate-forme

Utilisez mélangeurs de plate-forme et de la technologie des articles techniques lorsque vous créez plusieurs versions du même article aux différences d’adresse dans la mise en œuvre sur des plates-formes ou des technologies. C’est en général plus applicable pour le contenu de notre plate-forme mobile pour les développeurs. Il existe actuellement deux types de sélecteurs [sélecteurs simples](#simple-selectors) et de [sélecteurs bidirectionnelles](#two-way-selectors).

Puisque la démarque même sélecteur s’affiche dans chaque rubrique de la sélection, nous vous recommandons de placer le sélecteur de votre sujet dans une inclusion, puis en référençant ce inclure dans tous vos rubriques qui utilisent le même sélecteur.

###<a id="simple-selectors"></a>Sélecteurs simples

Les sélecteurs (unidirectionnels) simples rendu sous la forme d’un ensemble de cases d’option juste sous le titre. Utilisez ces boutons lorsque les clients ont besoin de rubriques dans un jeu de plate-forme ou technologie unique, notamment .NET, Node.js et Java.  Utiliser l’extension démarque personnalisés pour les sélecteurs de-ne pas utiliser de code HTML pour les sélecteurs.  

Reportez-vous à la section [mise en route de concentrateurs de Notification](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started/) pour voir comment l’auteur a créé 8 versions du même article, mais les sélecteurs utilisés pour activer la navigation au sein de tous les.

![Exemple de sélection simple](./media/custom-markdown-extensions/selectors.PNG)

####<a name="syntax"></a>Syntaxe

    > [AZURE.SELECTOR]
    - [Lier l’étiquette #1](link #1 url)
    - [étiquette du lien n ° 2](link #2 url)

Exemple :

    > [AZURE.SELECTOR]
    - [Universel de Windows](../articles/notification-hubs-windows-store-dotnet-get-started/)
    - [De Windows Phone](../articles/notification-hubs-windows-phone-get-started/)
    - [e/s](../articles/notification-hubs-ios-get-started/)
    - [Android](../articles/notification-hubs-android-get-started/)
    - [Kindle](../articles/notification-hubs-kindle-get-started/)
    - [Baidu](../articles/notification-hubs-baidu-get-started/)
    - [Xamarin.iOS](../articles/partner-xamarin-notification-hubs-ios-get-started/)
    - [Xamarin.Android](../articles/partner-xamarin-notification-hubs-android-get-started/)

#### <a name="rendering"></a>Rendu

L’image ci-dessus montre le rendu sur azure.microsoft.com. Sur les pages de GitHub rendues, les sélecteurs de rendu sous la forme d’une liste à puces des liens.

###<a id="two-way-selectors"></a>Sélecteurs bidirectionnelles

Sélecteurs bidirectionnelles permet aux utilisateurs de sélectionner un rubriques à partir d’une matrice de deux voies. Ceci est essentiel quand une technologie Azure, tels que les Services mobiles, prend en charge plusieurs plates-formes de back-end, ainsi que plusieurs clients. Gardez à l’esprit les éléments suivants :

- Pendant qu’il a été conçu en tant que `(Platform | Backend)`, le texte de dropwdown peut désormais être personnalisé.
- Vous n’avez pas besoin d’un élément de liste pour chaque point dans votre matrice, mais avez un élément où une URL de la rubrique existe et qu’il n’est pas un doublon.
- Le lien peut être toute URL, bien qu’il soit généralement une autre rubrique de GitHub.

Pour voir comment l’auteur créé 15 versions du même article (9 plates-formes de clients mobiles et les plates-formes de back-end 2), mais les sélecteurs utilisés pour activer la navigation au sein de tous les, consultez [mise en route de Services mobiles](http://azure.microsoft.com/en-us/documentation/articles/mobile-services-ios-get-started/) . Notez que 3 articles ne pas avoir les deux versions de back-end.

![Exemple de sélecteurs bidirectionnelle](./media/custom-markdown-extensions/selector-list.png)

####<a name="syntax"></a>Syntaxe

    > [AZURE. LISTE de la sélection (Dropdown1 | Dropdown2)]     -  [(Dropdown1Text1 | Dropdown2Text1)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text1 | Dropdown2Text2)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text2 | Dropdown2Text3)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text3 | Dropdown2Text4)](../articles/dropdown1-text1-dropdown2-text1.md)

Exemple :

    > [AZURE. LISTE de la sélection (plate-forme | Back-end)]     -  [(iOS | .NET)](./mobile-services-dotnet-backend-ios-get-started-push.md)
    - [(iOS | JavaScript)](./mobile-services-javascript-backend-ios-get-started-push.md)
    - [(Windows universel C# | .NET)](./mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md)
    - [(Windows universel C# | JavaScript)](./mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md)
    - [(Windows Phone | .NET)](./mobile-services-dotnet-backend-windows-phone-get-started-push.md)
    - [(Windows Phone | JavaScript)](./mobile-services-javascript-backend-windows-phone-get-started-push.md)
    - [(Android | .NET)](./mobile-services-dotnet-backend-android-get-started-push.md)
    - [(Android | JavaScript)](./mobile-services-javascript-backend-android-get-started-push.md)
    - [(e/s de Xamarin | JavaScript)](./partner-xamarin-mobile-services-ios-get-started-push.md)
    - [(Xamarin Android | JavaScript)](./partner-xamarin-mobile-services-android-get-started-push.md)

#### <a name="rendering"></a>Rendu

L’image ci-dessus montre le rendu sur azure.microsoft.com. Sur les pages de GitHub rendues, les sélecteurs de rendu sous la forme d’une liste à puces des liens.

<!--Anchors-->
[Remarques et conseils]: #notes-and-tips
[Inclut les]: #includes
[Vidéos incorporées]: #embedded-videos
[Sélecteurs de technologie et de la plate-forme]: #technology-and-platform-selectors

###<a name="contributors-guide-links"></a>Liens du Guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)
