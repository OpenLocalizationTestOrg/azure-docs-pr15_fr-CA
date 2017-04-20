<properties
   pageTitle="Les projets Visual Studio de ressource groupe Azure | Microsoft Azure"
   description="Utilisez Visual Studio pour créer un projet de groupe ressource Azure et déployer les ressources sur Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="tomfitz" />

# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Créer et déployer des groupes de ressources Azure via Visual Studio

Avec Visual Studio et le [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/), vous pouvez créer un projet qui déploie votre infrastructure et votre code vers Azure. Par exemple, vous pouvez définir l’hôte web, un site web et une base de données pour votre application et déployer cette infrastructure avec le code. Ou bien, vous pouvez définir une Machine virtuelle, le réseau virtuel et le compte de stockage et de déployer cette infrastructure avec un script qui est exécuté sur une Machine virtuelle. Le projet de déploiement de **Groupe de ressources Azure** vous permet de déployer toutes les ressources nécessaires dans une opération unique et reproductible. Pour plus d’informations sur le déploiement et la gestion de vos ressources, consultez [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md).

Projets du groupe de ressources Azure contiennent des modèles Azure le Gestionnaire de ressources JSON, qui définissent les ressources que vous déployez sur Azure. Pour en savoir plus sur les éléments du modèle de gestionnaire de ressources, consultez [modèles de création d’un gestionnaire de ressources Azure](resource-group-authoring-templates.md). Visual Studio vous permet de modifier ces modèles et fournit des outils qui simplifient le travail avec des modèles.

Dans cette rubrique, vous déployez une application web et la base de données de SQL. Toutefois, les étapes sont pratiquement identiques pour n’importe quel type de ressource. Vous pouvez facilement déployer une Machine virtuelle et à ses ressources connexes. Visual Studio fournit de nombreux modèles de starter différents pour des scénarios courants de déploiement.

Cet article explique 2015 mise à jour 2 de Visual Studio et le Kit de développement Microsoft Azure pour .NET 2.9. Si vous utilisez Visual Studio 2013 avec Azure SDK 2.9, votre expérience est en grande partie identique. Vous pouvez utiliser des versions du Kit de développement Azure de 2.6 ou version ultérieure ; Toutefois, votre expérience de l’interface utilisateur peut être différente de l’interface utilisateur illustrée dans cet article. Nous recommandons vivement d’installer la version la plus récente du [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/) avant de commencer les étapes ci-dessous. 

## <a name="create-azure-resource-group-project"></a>Projet de création de groupe de ressources Azure

Dans cette procédure, vous créez un projet du groupe de ressources Azure avec un modèle **d’application Web + SQL** .

1. Dans Visual Studio, choisissez **fichier**, **Nouveau projet**, choisissez **C#** ou **Visual Basic**. Puis choisissez **Cloud**, puis choisissez projet de **Groupe de ressources Azure** .

    ![Projet de déploiement de cloud](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Choisissez le modèle que vous souhaitez déployer vers le Gestionnaire de ressources Azure. Notez qu’il existe plusieurs options différentes en fonction du type de projet que vous souhaitez déployer. Pour cette rubrique, choisissez le modèle **application Web + SQL** .

    ![Choisissez un modèle](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Le modèle que vous choisissez est simplement un point de départ ; Vous pouvez ajouter et supprimer des ressources pour répondre à votre scénario.

    >[AZURE.NOTE] Visual Studio récupère la liste des modèles disponibles en ligne. La liste peut changer.

    Visual Studio crée un projet de déploiement de groupe de ressources pour l’application web et de base de données SQL.

1. Pour voir ce que vous avez créée, développez les nœuds dans le projet de déploiement.

    ![afficher les nœuds](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Puisque nous avons choisi le Web app + modèle SQL pour cet exemple, vous consultez les fichiers suivants : 

  	|Nom de fichier|Description|
  	|---|---|
  	|AzureResourceGroup.ps1 de déploiement|Un script PowerShell qui appelle des commandes PowerShell pour déployer vers le Gestionnaire de ressources Azure.<br />**Remarque** Visual Studio utilise ce script PowerShell pour déployer votre modèle. Les modifications que vous apportez à ce script affectent le déploiement dans Visual Studio, soyez donc prudent.|
  	|WebSiteSQLDatabase.json|Le modèle de gestionnaire de ressources qui définit l’infrastructure que vous souhaitez déployer sur Azure et les paramètres que vous pouvez fournir au cours du déploiement. Il définit également les dépendances entre les ressources, afin que le Gestionnaire de ressources déploie les ressources dans l’ordre correct.|
  	|WebSiteSQLDatabase.parameters.json|Un fichier de paramètres qui contient les valeurs requises par le modèle. Vous passez des valeurs de paramètre pour personnaliser chaque déploiement.|

    Tous les projets de déploiement de groupe de ressources contiennent ces fichiers de base. Autres projets peuvent contenir des fichiers supplémentaires pour prendre en charge d’autres fonctionnalités.

## <a name="customize-the-resource-manager-template"></a>Personnaliser le modèle de gestionnaire de ressources

Vous pouvez personnaliser un projet de déploiement en modifiant les modèles JSON qui décrivent les ressources que vous souhaitez déployer. Est l’abréviation de JavaScript Object Notation JSON et est un format de données sérialisées qui est facile à utiliser. Les fichiers JSON utilisent un schéma que vous référencez en haut de chaque fichier. Si vous souhaitez comprendre le schéma, vous pouvez les télécharger et les analyser. Le schéma définit quels éléments sont valides, les types et les formats des champs, les valeurs possibles de valeurs énumérées et ainsi de suite. Pour en savoir plus sur les éléments du modèle de gestionnaire de ressources, consultez [modèles de création d’un gestionnaire de ressources Azure](resource-group-authoring-templates.md).

Pour travailler sur votre modèle, ouvrez **WebSiteSQLDatabase.json**.

L’éditeur de Visual Studio fournit des outils pour vous aider à modifier le modèle de gestionnaire de ressources. La fenêtre **Structure de JSON** rend facile de voir les éléments définis dans votre modèle.

![afficher la structure JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Les éléments de la sélection dans le plan permet d’accéder à la partie du modèle et souligne le JSON correspondant.

![naviguer dans JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Vous pouvez ajouter une ressource en sélectionnant le bouton **Ajouter une ressource** dans la partie supérieure de la fenêtre du plan de JSON, ou en cliquant sur **ressources** et en sélectionnant **Ajouter une nouvelle ressource**.

![ajouter des ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Pour ce didacticiel, sélectionnez le **Compte de stockage** et lui donner un nom. Fournir un nom qui n’est pas plus de 11 caractères, ne contient que des chiffres et des lettres en minuscules.

![ajouter du stockage](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Notez que non seulement les ressources ajoutées, mais également un paramètre pour le compte de stockage de type et une variable pour le nom du compte de stockage.

![afficher le plan](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Le paramètre **storageType** est prédéfini avec les types autorisés et d’un type par défaut. Vous pouvez laisser ces valeurs ou les modifier pour votre scénario. Si vous ne souhaitez pas que tout le monde à déployer un compte de stockage **Premium_LRS** par l’intermédiaire de ce modèle, le supprimer à partir des types autorisés. 

    "storageType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

Visual Studio fournit également intellisense pour vous aider à comprendre quelles propriétés sont disponibles lors de la modification du modèle. Par exemple, pour modifier les propriétés pour votre plan de Service d’application, accédez à la ressource **HostingPlan** et ajouter une valeur pour les **Propriétés**. Notez qu’intellisense répertorie les valeurs disponibles et fournit une description de cette valeur.

![Afficher intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Vous pouvez définir **numberOfWorkers** sur 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## <a name="deploy-the-resource-group-project-to-azure"></a>Déployer le projet de groupe de ressources vers Azure

Vous êtes maintenant prêt à déployer votre projet. Lorsque vous déployez un projet du groupe de ressources d’Azure, vous le déployez à un groupe de ressources Azure. Le groupe de ressources est un regroupement logique de ressources qui partagent un cycle de vie commune.

1. Dans le menu contextuel du nœud du projet de déploiement, choisissez **Deploy** > **Nouveau déploiement**.

    ![Élément de menu nouveau déploiement, déployer](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

    La boîte de dialogue **déployer au groupe de ressources** s’affiche.

    ![Déployer, boîte de dialogue groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. Dans la liste déroulante **groupe de ressources** , choisissez un groupe de ressources existant ou créez-en un nouveau. Pour créer un groupe de ressources, ouvrez la liste déroulante **Groupe de ressources** et cliquez sur **Créer un nouveau**.

    ![Déployer, boîte de dialogue groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    La boîte de dialogue **Créer un groupe de ressources** s’affiche. Attribuer à votre groupe un nom et un emplacement et cliquez sur le bouton **créer** .

    ![Créer la boîte de dialogue groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. Modifier les paramètres pour le déploiement en sélectionnant le bouton **Modifier les paramètres** .

    ![Modifier le bouton Paramètres](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)

1. Fournir des valeurs pour les paramètres vides et cliquez sur le bouton **Enregistrer** . Les paramètres vides sont **hostingPlanName**, **administratorLogin**, **administratorLoginPassword**et **databaseName**.

    **hostingPlanName** spécifie un nom pour le [plan de l’application de Service](./app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) à créer. 
    
    **administratorLogin** Spécifie le nom d’utilisateur de l’administrateur de SQL Server. N’utilisez pas des noms communs comme **administrateur système** ou **administrateur**admin. 
    
    **administratorLoginPassword** spécifie un mot de passe administrateur de SQL Server. L’option **Enregistrer les mots de passe en texte brut dans le fichier de paramètres** n’est pas sécurisée ; Par conséquent, ne sélectionnez pas cette option. Dans la mesure où le mot de passe n’est pas enregistré en tant que texte brut, vous devez fournir ce mot de passe à nouveau pendant le déploiement. 
    
    **databaseName** Spécifie le nom de la base de données à créer. 

    ![Modifier la boîte de dialogue Paramètres](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
1. Cliquez sur le bouton de **déploiement** pour déployer le projet vers Azure. Une console PowerShell s’ouvre en dehors de l’instance de Visual Studio. Dans la console PowerShell lorsque vous y êtes invité, entrez le mot de passe administrateur de SQL Server. **Votre console PowerShell est masqué derrière d’autres éléments ou réduite dans la barre des tâches.** Recherchez cette console et sélectionnez-le pour fournir le mot de passe.

    >[AZURE.NOTE] Visual Studio peut vous demander d’installer les applets de commande PowerShell d’Azure. Vous devez les applets de commande PowerShell de Azure pour déployer avec succès des groupes de ressources. Si vous y êtes invité, installez-les.
    
1. Le déploiement peut prendre quelques minutes. Dans la **sortie** de windows, vous voyez l’état du déploiement. Lorsque le déploiement est terminé, le dernier message indique un déploiement réussi avec quelque chose de similaire à :

        ... 
        18:00:58 - Successfully deployed template 'c:\users\user\documents\visual studio 2015\projects\azureresourcegroup1\azureresourcegroup1\templates\websitesqldatabase.json' to resource group 'DemoSiteGroup'.


1. Dans un navigateur, ouvrez le [portail Azure](https://portal.azure.com/) et vous connecter à votre compte. Pour voir le groupe de ressources, sélectionnez les **groupes de ressources** et le groupe de ressources que vous avez déployé sur.

    ![Sélectionnez le groupe](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Affiche toutes les ressources déployées. Notez que le nom du compte de stockage n’est pas exactement ce que vous avez spécifié lors de l’ajout de cette ressource. Le compte de stockage doit être unique. Le modèle ajoute automatiquement une chaîne de caractères pour le nom que vous avez fourni pour fournir un nom unique. 

    ![afficher les ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Si vous apportez des modifications et que vous souhaitez redéployer votre projet, cliquez sur le groupe de ressources existant dans le menu contextuel du projet de groupe ressource Azure. Dans le menu contextuel, choisissez **Deploy**, puis vous avez déployé le groupe de ressources.

    ![Groupe de ressource Azure déployés](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Déployer le code de votre infrastructure

À ce stade, vous avez déployé l’infrastructure de votre application, mais il n’y a aucun code réel déployé avec le projet. Cette rubrique indique comment déployer une application web et les tables de la base de données SQL pendant le déploiement. Si vous déployez un ordinateur virtuel au lieu d’une application web, vous souhaitez exécuter du code sur l’ordinateur dans le cadre du déploiement. Le processus de déploiement de code pour une application web ou pour la configuration d’une Machine virtuelle est presque le même.

1. Ajoutez un projet à votre solution Visual Studio. Avec le bouton droit de la solution et sélectionnez **Ajouter** > **Nouveau projet**.

    ![ajouter le projet](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Ajouter une **Application Web d’ASP.NET**. 

    ![Ajoutez l’application web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Sélectionnez **MVC** et effacer le champ de **l’hôte dans le cloud** , car le projet de groupe de ressources exécute cette tâche.

    ![Sélectionnez MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Une fois que Visual Studio créé votre application web, vous voyez les deux projets dans la solution.

    ![afficher les projets](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Maintenant, vous devez vous assurer que votre projet de groupe de ressources est informé du nouveau projet. Revenir à votre projet de groupe de ressources (AzureResourceGroup1). Cliquez sur **références** et sélectionnez **Ajouter une référence**.

    ![Ajouter une référence](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Sélectionnez le projet d’application web que vous avez créé.

    ![Ajouter une référence](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    En ajoutant une référence, vous liez le projet d’application web au projet du groupe de ressources et définissez automatiquement les trois propriétés de clé. Vous consultez ces propriétés dans la fenêtre **Propriétés** de la référence.

      ![voir Guide de référence](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
    
    Les propriétés sont les suivantes :

    - Les **Propriétés supplémentaires** contient le package de déploiement web emplacement qui est basculé vers le stockage Azure intermédiaire. Notez le dossier (ExampleApp) et le fichier (package.zip). Vous devez fournir ces valeurs en tant que paramètres lors du déploiement de l’application. 
    - Le **Chemin d’accès des fichiers Include** contient le chemin d’accès où le package est créé. **Inclure les cibles** contient la commande que le déploiement s’exécute. 
    - Créer de la valeur par défaut de **; Package** permet le déploiement générer et créer un package de déploiement web (package.zip).  
    
    Vous n’avez pas besoin d’un profil de publication comme le déploiement Obtient les informations nécessaires à partir des propriétés pour créer le package.
      
1. Ajoutez une ressource au modèle.

    ![ajouter des ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Cette fois-ci sélectionnez le **Déploiement Web pour les applications Web**. 

    ![Ajouter web déployer](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Permet de redéployer votre projet du groupe de ressources pour le groupe de ressources. Cette fois il y a certains nouveaux paramètres. Vous n’êtes pas obligé de fournir des valeurs pour **_artifactsLocation** ou **_artifactsLocationSasToken** , car Visual Studio génère automatiquement ces valeurs. Toutefois, vous devez définir le dossier et le nom de fichier pour le chemin d’accès qui contient le package de déploiement (illustré dans l’image suivante en tant que **ExampleAppPackageFolder** et **ExampleAppPackageFileName** ). Fournir les valeurs que vous avez vu précédemment dans les propriétés de référence (**ExampleApp** et **package.zip**).

    ![Ajouter web déployer](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    Pour le **compte de stockage artefact**, sélectionnez celui déployé avec ce groupe de ressources.
    
1. Une fois le déploiement terminé, sélectionnez votre application web sur le portail. Sélectionnez l’URL pour accéder au site.

    ![Parcourir le site](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Notez que vous avez correctement déployé l’application ASP.NET par défaut.

    ![afficher l’application déployée](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la gestion de vos ressources via le portail, consultez [l’aide du portail Azure pour gérer vos ressources Azure](./azure-portal/resource-group-portal.md).
- Pour en savoir plus sur les modèles, consultez [modèles de création d’un gestionnaire de ressources Azure](resource-group-authoring-templates.md).
