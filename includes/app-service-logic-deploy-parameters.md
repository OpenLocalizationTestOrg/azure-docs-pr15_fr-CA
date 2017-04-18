Avec le Gestionnaire de ressources d’Azure, vous définissez des paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section appelée de paramètres qui contient toutes les valeurs de paramètre.
Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui restent toujours la même. Chaque valeur de paramètre est utilisé dans le modèle pour définir les ressources qui sont déployer. 

Lors de la définition des paramètres, utilisez le champ **allowedValues** pour spécifier les valeurs d’un utilisateur peut fournir au cours du déploiement. Le champ **defaultValue** permet d’assigner une valeur au paramètre, si aucune valeur n’est fournie pendant le déploiement.

Nous décrirons chaque paramètre dans le modèle.

### <a name="logicappname"></a>logicAppName

Le nom de l’application logique à créer.

    "logicAppName": {
        "type": "string"
    }