<properties
   pageTitle="Utilisation du Code VS avec modèles du Gestionnaire de ressources | Microsoft Azure"
   description="Affiche la configuration de Code de Visual Studio pour créer des modèles d’Azure le Gestionnaire de ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="chmatsk;tomfitz"/>

# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Utilisation des modèles du Gestionnaire de ressources Azure dans le Code de Visual Studio

Azure modèles du Gestionnaire de ressources sont des fichiers JSON qui décrivent une ressource et les dépendances connexes. Ces fichiers peuvent parfois être volumineux et complexes et il est donc important de prise en charge des outils. Le Code de Visual Studio est un éditeur de code nouveau, léger, open source, multiplates-formes. Il prend en charge la création et la modification des modèles de gestionnaire de ressources via une [nouvelle extension](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Code de VS s’exécute partout et ne nécessite pas un accès à Internet, sauf si vous souhaitez également déployer des modèles de votre gestionnaire de ressources.

Si vous n’avez pas encore de Code de VS, vous pouvez l’installer à [https://code.visualstudio.com/](https://code.visualstudio.com/).

## <a name="install-the-resource-manager-extension"></a>Installer l’extension du Gestionnaire de ressources

Pour travailler avec les modèles JSON dans le Code de VS, vous devez installer une extension. Les étapes suivantes, téléchargement et installez la prise en charge pour les modèles du Gestionnaire de ressources JSON :

1. Lancement du Code de VS 
2. Ouvrir Ouvrir rapide (Ctrl + P) 
3. Exécutez la commande suivante : 

        ext install azurerm-vscode-tools

4. Redémarrez le Code VS lorsque vous êtes invité à activer l’extension. 

 Travail est terminé !

## <a name="set-up-resource-manager-snippets"></a>Configurer le Gestionnaire de ressources extraits

Les étapes précédentes installé la prise en charge des outils, mais nous devons maintenant configurer le Code VS pour utiliser des extraits de modèle JSON.

1. Copiez le contenu du fichier à partir du référentiel [d’azure-xplat-bras-outillage](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) à votre Presse-papiers.
2. Lancement du Code de VS 
3. Dans le Code de VS, vous pouvez ouvrir le fichier extraits JSON en naviguant vers le **fichier** -> **Préférences** -> **Utilisateur extraits** -> **JSON**, ou en sélectionnant **F1** et en tapant les **Préférences** jusqu'à ce que vous pouvez sélectionner **Préférences : extraits de**.

    ![extraits de préférence](./media/resource-manager-vs-code/preferences-snippets.png)

    Parmi les options, sélectionnez **JSON**.

    ![Sélectionnez json](./media/resource-manager-vs-code/select-json.png)

4. Coller le contenu du fichier à l’étape 1 dans votre fichier d’extraits de code utilisateur avant le dernier «} » 
5. Assurez-vous que le JSON recherche OK et il n’y a aucun tildes n’importe où. 
6. Enregistrez et fermez le fichier d’extraits de code utilisateur.

C’est tout ce qui est nécessaire pour commencer à utiliser les extraits de code du Gestionnaire de ressources. Ensuite, nous allons présenter ce programme d’installation pour le test.

## <a name="work-with-template-in-vs-code"></a>Travailler avec le modèle de Code de VS

La façon la plus simple pour commencer à travailler avec un modèle est à saisir une rapide démarrer modèles de disponibles sur [Github](https://github.com/Azure/azure-quickstart-templates) ou utiliser votre propre. Vous pouvez facilement [Exporter un modèle](resource-manager-export-template.md) pour un de vos groupes de ressources via le portail. 

1. Si vous avez exporté un modèle à partir d’un groupe de ressources, ouvrez les fichiers extraits dans le Code de VS.

    ![afficher les fichiers](./media/resource-manager-vs-code/show-files.png)

2. Ouvrez le fichier template.json afin que vous pouvez modifier et ajouter des ressources supplémentaires. Après le **« ressources » : [** appuyez sur ENTRÉE pour commencer une nouvelle ligne. Si vous tapez **arm**, s’affiche avec une liste d’options. Ces options sont les extraits de modèle que vous avez installé. Il doit ressembler à ceci : 

    ![afficher les extraits de code](./media/resource-manager-vs-code/type-snippets.png)

3. Cliquez sur l’extrait de code que vous le souhaitez. Pour cet article, j’ai opté **bras-ip** pour créer une nouvelle adresse IP publique. Placez une virgule après l’accolade fermante «} » de la ressource nouvellement créée pour vous assurer que votre modèle de syntaxe est valide.

     ![Ajoutez une virgule](./media/resource-manager-vs-code/add-comma.png)

4. Code de VS a IntelliSense intégré. Lorsque vous modifiez vos modèles, VS Code suggère les valeurs disponibles. Par exemple, pour ajouter une section sur les variables de votre modèle, ajoutez **« »** (deux guillemets droits) et sélectionnez **Ctrl + Espace** entre les guillemets. S’affiche avec les options, y compris les **variables**.

    ![Ajoutez des variables](./media/resource-manager-vs-code/add-variables.png)

5. IntelliSense peut également suggérer des valeurs disponibles ou des fonctions. Pour définir une propriété à une valeur de paramètre, créez une expression avec **« [] »** et **Ctrl + Espace**. Vous pouvez commencer à taper le nom d’une fonction. Sélectionnez **l’onglet** lorsque vous avez trouvé la fonction souhaitée.

    ![Ajouter un paramètre](./media/resource-manager-vs-code/select-parameters.png)

6. Sélectionnez de nouveau **Ctrl + Espace** dans la fonction pour afficher la liste des paramètres disponibles dans votre modèle.

    ![Ajouter un paramètre](./media/resource-manager-vs-code/select-avail-parameters.png)

7. Si vous avez des problèmes de validation de schéma dans votre modèle, vous verrez les tildes familiers dans l’éditeur. Vous pouvez afficher la liste des erreurs et avertissements en tapant **Ctrl + Maj + M** ou en sélectionnant les glyphes dans la barre d’état gauche inférieure.

    ![erreurs](./media/resource-manager-vs-code/errors.png)

    Validation de votre modèle peut vous aider à détecter les problèmes de syntaxe ; Toutefois, vous pouvez également voir les erreurs que vous pouvez ignorer. Dans certains cas, l’éditeur compare votre modèle par rapport à un schéma qui n’est pas à jour et signale par conséquent une erreur même si vous savez qu’il est correct. Par exemple, supposons qu’une fonction a été récemment ajoutée au Gestionnaire de ressources, mais le schéma n’a pas été mis à jour. L’éditeur indique une erreur en dépit du fait que la fonction fonctionne correctement lors du déploiement.

    ![message d’erreur](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Déployer vos nouvelles ressources

Lorsque votre modèle est prêt, vous pouvez déployer les nouvelles ressources suivant les instructions ci-dessous : 

### <a name="windows"></a>Windows

1. Ouvrez une invite de commande PowerShell 
2. Type de connexion : 

        Login-AzureRmAccount 

3. Si vous disposez de plusieurs abonnements, obtenir la liste des abonnements avec :

        Get-AzureRmSubscription

    Et sélectionnez l’abonnement pour utiliser.
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. Mettre à jour les paramètres dans votre fichier parameters.json
5. Exécuter le Deploy.ps1 pour déployer votre modèle sur Azure

### <a name="osxlinux"></a>OSX/Linux

1. Ouvrez une fenêtre de terminal 
2. Type de connexion :

        azure login 

3. Si vous disposez de plusieurs abonnements, sélectionnez l’abonnement avec :

        azure account set <subscriptionNameOrId> 

4. Mettre à jour les paramètres dans le fichier parameters.json.
5. Pour déployer le modèle, exécutez :

        azure group deployment create -f <PathToTemplate> 

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les modèles, consultez [modèles de création d’un gestionnaire de ressources Azure](resource-group-authoring-templates.md).
- Pour en savoir plus sur les fonctions de modèle, voir [fonctions de modèle de gestionnaire de ressources Azure](resource-group-template-functions.md).
- Pour plus d’exemples d’utilisation de Code de Visual Studio, consultez [génération des applications de cloud avec le Code de Visual Studio](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) à partir de la connexion de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [Démo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Pour plus des Démarrages rapides à partir de la démo de HealthClinic.biz, consultez les [Démarrages rapides outils de développeur de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
