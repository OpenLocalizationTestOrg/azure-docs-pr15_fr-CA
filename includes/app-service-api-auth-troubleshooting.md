La plupart des erreurs d’authentification temps résultat de paramètres de configuration incorrectes ou incohérentes. Voici quelques suggestions spécifiques pour les éléments à vérifier.

* Assurez-vous que vous n’avez pas manquer le bouton **Enregistrer** n’importe où. Il est souvent facile à faire, et le résultat est que vous consulterez les valeurs correctes sur une page de portail, mais ils n’ont pas été enregistrés dans l’environnement Azure ou l’application de AD Azure.
* Pour les paramètres configurés dans la lame de **Paramètres d’Application** du portail Azure, assurez-vous que l’application d’API appropriée ou web app a été sélectionné lorsque les paramètres ont été entrés.  Assurez-vous également que les paramètres ont été entrés en tant que **paramètres de l’application** et pas de **chaînes de connexion**, comme le format des deux sections est similaire.
* Pour l’authentification à un front end de JavaScript, téléchargez à nouveau pour vérifier que le manifeste de le `oauth2AllowImplicitFlow` a été remplacé par `true`.
* Vérifiez que vous avez utilisé HTTPS chaque fois que vous avez configuré des URL :

    * Dans le code de projet
    * Dans les CORS
    * Dans les paramètres de l’application pour chaque application d’API et d’une application web environnement Azure
    * Dans les paramètres de l’application Azure AD.
    
    Notez que si vous copiez l’URL de l’application API à partir du portail, il souvent a `http://` et vous devez le modifier manuellement à `https://`.

* Assurez-vous que les modifications de code ont été correctement déployées. Par exemple, dans une solution à plusieurs projets, il est possible de modifier le code d’un projet et choisissez accidentellement l’une des autres lorsque vous projetez de déployer le changement.
* Assurez-vous que vous vous apprêtez à URL HTTPS dans votre navigateur, pas des URL HTTP. Par défaut, Visual Studio crée des profils avec des URL HTTP de publication, et c’est ce qui s’ouvre dans le navigateur une fois que vous déployez un projet.
* Pour l’authentification à un front end de JavaScript, assurez-vous que CORS est correctement configuré sur l’application de l’API du code JavaScript appelant. En cas de doute sur si le problème est lié au CORS, essayez « * » comme l’URL d’origine autorisé. 
* Pour un front end de JavaScript, ouvrez l’onglet de Console de développeur d’outils de votre navigateur pour obtenir plus d’informations et examiner les requêtes HTTP sur le réseau. Toutefois, les messages d’erreur de Console peuvent être trompeurs. Si vous obtenez un message indiquant une erreur CORS, la vraie question est peut-être l’authentification. Vous pouvez vérifier si c’est le cas en exécutant l’application avec l’authentification temporairement désactivée.
* Pour une application API .NET, assurez-vous que vous obtenez toutes les informations dans les messages d’erreur que possible en définissant le [mode customErrors à Off](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Pour une application API .NET, démarrez une [session de débogage distant](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)et examiner les valeurs des variables qui sont passées au code qui utilise ADAL pour obtenir un jeton de support ou code qui vérifie les créances sur le code principal de service attendu. Notez que votre code peut prendre les valeurs de configuration à partir de nombreuses sources différentes, il est possible de trouver des surprises de cette manière. Par exemple, si vous tapez `ida:ClientId` en tant que `ida:ClientID` lors de la configuration des paramètres d’environnement de Service d’application Azure, le code peut obtenir le `ida:ClientId` valeur qu’il recherche dans le fichier Web.config, en ignorant le paramètre Service d’application Azure. 
* Si les choses ne fonctionnent pas dans une fenêtre Internet Explorer normale, une journal complémentaire existante peut-être interférer ; Essayez InPrivate et Chrome ou Firefox.
