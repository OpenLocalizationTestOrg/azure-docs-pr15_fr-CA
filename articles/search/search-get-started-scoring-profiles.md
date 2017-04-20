<properties 
    pageTitle="Comment faire pour utiliser la notation profils dans Azure recherche | Microsoft Azure | Service de recherche de nuage hébergé" 
    description="Ajuster le classement par notation des profils dans la recherche d’Azure, un service de recherche de nuage hébergé sur Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="how-to-use-scoring-profiles-in-azure-search"></a>Comment faire pour utiliser des profils de score dans Azure recherche

Profils de score sont une fonctionnalité de Microsoft Azure recherche qui personnalisent le calcul des résultats de la recherche, qui influencent la façon dont les éléments sont classés dans une liste de résultats de recherche. Vous pouvez considérer score profils comme un moyen à la pertinence du modèle, par l’augmentation des éléments répondant aux critères prédéfinis. Par exemple, supposons que votre application est un site de réservation d’hôtel en ligne. Par l’augmentation de la `location` champ, recherches qui incluent un terme comme Seattle entraînera des scores élevés pour les éléments qui ont des Seattle dans le `location` champ. Notez que vous pouvez avoir plus d’un profil d’évaluation, ou aucune, si l’évaluation de la valeur par défaut est suffisant pour votre application.

Pour vous aider à faire des essais avec les profils d’évaluation, vous pouvez télécharger un exemple d’application qui utilise la notation de profils pour modifier l’ordre de classement des résultats de recherche. L’exemple est une application de console – ne sont peut-être pas très réaliste pour le développement d’applications réelles, mais utile néanmoins comme outil d’apprentissage. 

L’exemple d’application illustre les comportements de score à l’aide de données fictives, appelées le `musicstoreindex`. La simplicité de l’exemple d’application rend faciles à modifier les profils d’évaluation et des requêtes et de voir les effets immédiats sur l’ordre de classement lors de l’exécution du programme.

<a id="sub-1"></a>
## <a name="prerequisites"></a>Conditions préalables

L’exemple d’application est écrite en C# à l’aide de Visual Studio 2013. Essayez libre [2013 de Visual Studio Express edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) si vous ne disposez pas d’une copie de Visual Studio.

Vous aurez besoin d’un abonnement Azure et un service de recherche d’Azure pour ce didacticiel. Pour vous aider à configurer le service, consultez [Création d’un service de recherche dans le portail](search-create-service-portal.md) .

[AZURE. INCLURE [vous avez besoin d’un compte Azure pour compléter ce didacticiel :](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## <a name="download-the-sample-application"></a>Télécharger l’exemple d’application

Accédez à [La démonstration de profils score d’Azure recherche](https://azuresearchscoringprofiles.codeplex.com/) sur codeplex pour télécharger l’exemple d’application décrit dans ce didacticiel.

Sous l’onglet Code Source, cliquez sur **Télécharger** pour obtenir un fichier zip de la solution. 

 ![][12]

<a id="sub-3"></a>
## <a name="edit-appconfig"></a>Modifiez l’app.config

1. Après avoir extrait les fichiers, ouvrez la solution dans Visual Studio pour modifier le fichier de configuration.
1. Dans l’Explorateur de solutions, double-cliquez sur **app.config**. Ce fichier Spécifie le point de terminaison de service et un `api-key` utilisé pour authentifier la demande. Vous pouvez obtenir ces valeurs à partir du portail classique.
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accéder au tableau de bord de service de recherche d’Azure.
1. Cliquez sur la mosaïque de **Propriétés** pour copier l’URL du service
1. Cliquez sur la mosaïque de **touches** pour copier les `api-key`.

Lorsque vous avez terminé d’ajouter l’URL et de `api-key` à app.config, les paramètres de l’application doivent ressembler à ceci :

   ![][11]


<a id="sub-4"></a>
## <a name="explore-the-application"></a>Explorer l’application

Vous êtes presque prêt à générer et exécuter l’application, mais avant de procéder, examinez les fichiers JSON permet de créer et de remplir l’index.

**Schema.JSON** définit l’index, y compris les profils de score sont mis en évidence au cours de cette démonstration. Notez que le schéma définit tous les champs utilisés dans l’index, y compris les champs non-indexé tel que `margin`, que vous pouvez utiliser dans un profil d’évaluation. Notation de syntaxe de profil est documenté dans [Ajout d’un profil d’évaluation à un index de recherche d’Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Data1-3.json** fournit les données, 246 albums sur quelques genres. Les données sont une combinaison d’informations album et l’artiste réelles, avec des champs fictifs comme `price` et `margin` utilisée pour illustrer les opérations de recherche. Les fichiers de données sont conformes à l’index et sont téléchargés vers votre service de recherche d’Azure. Une fois que les données sont téléchargées et indexées, vous pouvez émettre des requêtes.

**Program.cs** effectue les opérations suivantes :

- Ouvre une fenêtre de console.

- Se connecte à la recherche d’Azure à l’aide de l’URL du service et `api-key`.

- Supprime la `musicstoreindex` si elle existe.

- Crée un nouveau `musicstoreindex` à l’aide du fichier schema.json.

- Remplit l’index à l’aide de fichiers de données.

- Interroge l’index à l’aide de quatre requêtes. Notez que les profils d’évaluation sont spécifiées sous la forme d’un paramètre de requête. Toutes les requêtes de recherche pour le même terme, « meilleure ». La première requête montre de score par défaut. Les trois autres requêtes utilisent un profil d’évaluation.

<a id="sub-5"></a>
## <a name="build-and-run-the-application"></a>Générer et exécuter l’application

Pour éliminer la connectivité ou des problèmes de référence l’assembly, générez et exécutez l’application pour vous assurer qu’aucun problème pour utiliser en premier. Vous devez voir une application de console ouvre en arrière-plan. Tous les quatre requêtes s’exécutent dans séquence sans interruption. Sur de nombreux systèmes, la totalité du programme s’exécute en moins de 15 secondes. Si l’application console inclut un message indiquant « terminé. Appuyez sur ENTRÉE pour continuer », le programme s’est terminée correctement. 

Pour comparer les exécutions de la requête, vous pouvez marquer-copier-coller les résultats de la requête à partir de la console et les collez dans un fichier Excel. 

L’illustration suivante montre les résultats de la première trois requêtes en parallèle. Toutes les requêtes utilisent le même terme de recherche, « meilleur des cas », qui s’affiche dans les nombreux titres d’album.

   ![][10]

La première requête utilise la notation de la valeur par défaut. Dans la mesure où le terme de recherche apparaît uniquement dans les titres d ' album, et aucun critère n’est spécifié, les éléments ayant « mieux » dans le titre de l’album sont retournés dans l’ordre dans lequel le service de recherche trouve les. 

La deuxième requête utilise un profil d’évaluation, mais notez que le profil n’avait aucun effet. Les résultats sont identiques à celles de la première requête. C’est parce que le profil de définition de score augmente un champ ('genre') qui n’est pas pertinentes pour la requête. Le terme « meilleures » n’existent pas dans n’importe quel champ de 'genre' de n’importe quel document. Lorsqu’un profil d’évaluation n’a aucun effet, les résultats sont identiques sous forme de notation par défaut.  

La troisième requête est la preuve premier de l’évaluation d’impact de profil. Le terme de recherche est toujours « meilleur » et nous travaillons en collaboration avec le même ensemble d’albums, mais étant donné que le profil d’évaluation fournit des critères supplémentaires qui stimule la « notation » et 'dernière mise à jour', certains éléments sont propulsés plus haut dans la liste.

L’illustration suivante montre la requête de la quatrième et dernière, amplifiée par « marge ». Le champ « marge » est non consultable et ne peut pas être renvoyé dans les résultats de la recherche. La valeur « marge » a été ajoutée manuellement à la feuille de calcul afin d’illustrer le point avec des marges plus élevées, les éléments s’affichent plus dans la liste de résultats de recherche. 

   ![][9]

Maintenant que vous avez terminé les exercices profils score, essayez de modifier le programme à utiliser la syntaxe de requête différente, notation des profils ou des données plus complètes. Liens dans la section suivante fournissent plus d’informations.

<a id="next-steps"></a>
## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les scores des profils. Pour plus d’informations, consultez [Ajouter un profil de score à un index de recherche d’Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx) .

Pour en savoir plus sur les paramètres de syntaxe et de requête de recherche. Pour plus d’informations, reportez-vous à la section [Rechercher des Documents (Azure recherche reste API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

Si vous souhaitez revenir en arrière et en savoir plus sur la création de l’index ? [Regardez cette vidéo](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) pour comprendre les notions de base.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 