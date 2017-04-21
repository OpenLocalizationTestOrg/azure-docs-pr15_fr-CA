<properties title="" pageTitle="Écriture de documentation Azure - Fiche récapitulative de voix et style" description="Informations de style et de la voix pour vous aider à créer du contenu technique du centre de documentation Azure." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="12/16/2014" ms.author="glenga" />

#<a name="writing-azure-documentation---style-and-voice-cheat-sheet"></a>Écriture de documentation Azure - Fiche récapitulative de voix et style

Voici un mémento qui contient des pointeurs sur la façon d’écrire des articles techniques, de technologies et de services Azure. Ces instructions s’appliquent si vous créez la nouvelle documentation ou mise à jour de la documentation existante.

Au minimum, veuillez :

- Vérification de l’orthographe et la grammaire vérifient vos rubriques, même si vous avez couper et coller dans Word pour le faire.
- Utilisez une voix occasionnelle et conviviale, comme vous vous adressiez à une autre personne en tête-à-tête.
- Utilisez des phrases simples. Ils sont plus faciles à comprendre, et ils sont plus facilement traduits par des traducteurs humains et postes.

Les sections suivantes contiennent plus d’informations :

+ [Utilisez une voix convivial]
+ [Envisagez de localisation et traduction]
+ [Autres problèmes de voix et de style à surveiller]


##<a name="use-a-customer-friendly-voice"></a>Utilisez une voix convivial

Nous aspirons à suivre ces principes pour la rédaction technique pour Azure. Nous ne pouvons pas toujours faire, mais nous devons d’essayer !

- **Utiliser des mots du vocabulaire courant**: essayez d’utiliser le langage naturel, les mots que vos clients utilisent ; être moins formelle, mais pas moins techniques ; Donnez des exemples qui expliquent les nouveaux concepts.

- **Type**: ne perdez pas de mots et de ne plus expliquer. Être favorable et n’utilisez pas de mots ou de lots de qualificateurs. Conserver les phrases courts et concis. Conserver votre rubrique ciblé. Si une tâche possède un qualificateur, placez-le au début de la phrase ou un paragraphe. En outre, conserver le nombre de notes à un minimum. Utilisez une capture d’écran lorsqu’il peut enregistrer des mots.

- **Facile à analyser**: placer tout d’abord les choses les plus importantes. Utilisez les sections de segmentation des procédures longues en groupes plus faciles à gérer des opérations (procédures avec plus de 12 étapes sont probablement trop longs). Utilisez une capture d’écran lors de l’ajout de clarté.

- **Preuve de compassion**: utilisez un ton soutien dans l’article et maintenir des décharges de responsabilité à un minimum. Honnêtement, appelez les zones qui peut se révéler frustrantes pour les clients. Assurez-vous que l’article met l’accent sur ce qui est important pour le client, ne donnent qu’une conférence technique.

##<a name="consider-localization-and-machine-translation"></a>Envisagez de localisation et traduction
Nos articles techniques sont traduites dans de nombreuses autres langues, et certains sont modifiés pour les marchés particuliers. Personnes peut également être à l’aide de traduction automatique sur le web pour lire les articles techniques. Par conséquent, écrivez avec les conseils suivants à l’esprit :

- **Assurez-vous que l’article ne contient aucune grammaire, orthographe ou des erreurs de ponctuation**: il s’agit de quelque chose que nous devons faire en général. Pavé de démarque 2.0 a un vérificateur d’orthographe de base, mais vous devez également coller le rendu HTML () contenu dans l’article dans Word, ce qui a un vérificateur de grammaire et l’orthographe plus robuste.

- **Rendre vos phrases aussi courtes que possible**: composé de phrases ou chaînes de clauses compliquer traduction. Diviser les phrases Si vous pouvez le faire sans être trop redondants ou étrange de sondage. Nous ne voulons pas vraiment écrits dans un langage non naturelle, soit des articles.

- **Construction de phrase simple et cohérente d’utilisation**: la cohérence est préférable pour la traduction. Éviter les parentheticals et côté et ont pour sujet comme au début de la phrase que possible. Découvrez quelques rubriques publiées - si le sujet a un convivial, facile à lire le style, l’utiliser comme modèle.

- **Mise en majuscules et formulation cohérente d’utilisation**: une fois encore, la cohérence est la clé. Azure utilise la casse de la phrase pour les titres, n’est jamais mettre en majuscule un mot n’est pas au début d’une phrase ou un nom correct.

- **Inclure les « petits mots »**: mots que nous considérons petites et sans importance en anglais, car ils sont comprises pour le contexte (telles que « a », « le », « qui » et « est ») sont essentiels pour la traduction automatique - veillez à inclure les !

##<a name="other-style-and-voice-issues-to-watch-for"></a>Autres problèmes de voix et de style à surveiller

- Empêcher le fractionnement des étapes avec commentaires ou côté.

- Pour les étapes qui comportent des extraits de code, placez le plus d’informations sur l’étape dans le code sous forme de commentaires. Cela permet de réduire la quantité de texte ont des personnes à lire, et les informations de clé sont copiées dans le projet de code en rappelant au personnel de ce que fait le code lorsqu’ils référer ultérieurement.

- Le nom officiel du produit est « Microsoft Azure », mais nous pouvons presque toujours dire « Azure », comme dans « Services de Mobile Azure ».

- Ne créez pas d’acronymes qui commencent par « MA » ou « a ». Utilisez simplement « Azure » à la première référence avant un nom de service ou la fonctionnalité et faites-le glisser (par exemple, « Services de Mobile Azure » devient « Services mobiles » après la première utilisation). Essayez d’éviter les acronymes en général - ils des malentendus.

- Azure utilise la casse de la phrase pour tous les titres.

- Utilisez « sign in » et non « journal-in ».

- Inclure les mots « suivant » ou « comme suit » dans chaque phrase qui précède un extrait de code ou de la liste.

- « Base de données SQL » est la fonctionnalité Azure. Un « SQL de base de données » est une instance de base de données en cours d’exécution sur la base de données de SQL.

- Stockage Azure inclut plusieurs « données management services » qui inclut le service de la Table, le service d’objet Blob et le service de file d’attente. (Il n'a pas appelé le « service de stockage de Table Azure »).




###<a name="contributors-guide-links"></a>Liens du Guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)



<!--Anchors-->
[Utilisez une voix convivial]: #use-a-customer-friendly-voice
[Envisagez de localisation et traduction]: #consider-localization-and-machine-translation
[autres problèmes de voix et de style à surveiller]: #other-style-and-voice-issues-to-watch-for
