### <a name="app-service-plan"></a>Plan de Service d’application

Crée le plan de service pour l’hébergement de l’application web. Vous fournissez le nom du plan via le paramètre **hostingPlanName** . L’emplacement du plan est le même emplacement que celui utilisé pour le groupe de ressources. La taille de couche et de travailleur tarification sont spécifiés dans les paramètres de **référence sku** et **workerSize**

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

