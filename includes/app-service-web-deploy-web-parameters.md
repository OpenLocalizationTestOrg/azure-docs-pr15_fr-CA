Avec le Gestionnaire de ressources d’Azure, vous définissez des paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section appelée de paramètres qui contient toutes les valeurs de paramètre.
Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui restent toujours la même. Chaque valeur de paramètre est utilisé dans le modèle pour définir les ressources qui sont déployés. 

Lors de la définition des paramètres, utilisez le champ **allowedValues** pour spécifier les valeurs d’un utilisateur peut fournir au cours du déploiement. Le champ **defaultValue** permet d’assigner une valeur au paramètre, si aucune valeur n’est fournie pendant le déploiement.

Nous décrirons chaque paramètre dans le modèle.

### <a name="sitename"></a>nom du site

Le nom de l’application web que vous souhaitez créer.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName

Le nom du plan de Service de l’application à utiliser pour héberger l’application web.
    
    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>point de stock

Le niveau de prix pour le plan de l’hébergement.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Le modèle définit les valeurs autorisées pour ce paramètre et affecte une valeur par défaut (S1) si aucune valeur n’est spécifiée.

### <a name="workersize"></a>workerSize

La taille de l’instance du plan d’hébergement (petite, moyenne ou grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
Le modèle définit les valeurs autorisées pour ce paramètre (0, 1 ou 2) et affecte une valeur par défaut (0) si aucune valeur n’est spécifiée. Les valeurs correspondent aux petites, moyennes et grandes.
