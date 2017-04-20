<properties
   pageTitle="Environnements de développement et de test | Microsoft Azure"
   description="Apprenez à utiliser les modèles du Gestionnaire de ressources Azure rapidement et de manière cohérente les créer et supprimer le développement et les environnements de test."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# <a name="development-and-test-environments-in-microsoft-azure"></a>Environnements de test et de développement dans Microsoft Azure

Applications personnalisées sont généralement déployées sur plusieurs environnements de développement et tests avant le déploiement en production. Lorsque les environnements sont créés dans les locaux, les ressources informatiques sont achetées ou alloués pour chaque environnement pour chaque application. Les environnements incluent souvent plusieurs ordinateurs physiques ou virtuels avec des configurations spécifiques qui sont déployées manuellement ou à l’aide des scripts d’automatisation complexes. Souvent, les déploiements prennent des heures et entraînent de configurations incohérentes dans des environnements.

## <a name="scenario"></a>Scénario ##

Lorsque vous configurez des environnements de développement et test de Microsoft Azure, vous ne payez que pour les ressources que vous utilisez.  Cet article explique comment rapidement et régulièrement vous pouvez créer, mettre à jour et suppression de développement et test des environnements à l’aide du Gestionnaire de ressources Azure modèles et fichiers de paramètres, comme illustré ci-dessous.

![Scénario](./media/solution-dev-test-environments/scenario.png)

Trois environnements de test et de développement sont présentés ci-dessus.  Chacun a les applications web et des ressources de base de données SQL qui sont spécifiées dans un fichier de modèle.  Les noms de l’application et la base de données dans chaque environnement sont différents et sont spécifiés dans les fichiers de paramètre unique pour chaque environnement.  

Si vous n’êtes pas familiarisé avec les concepts du Gestionnaire de ressources d’Azure, il est recommandé de lire l’article [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) avant la lecture de cet article.

Vous souhaiterez peut-être tout d’abord passer par les étapes décrites dans cet article comme indiqué sans lire les articles référencés pour rapidement une expérience à l’aide des modèles du Gestionnaire de ressources Azure. Une fois que vous avez été les étapes qu’une seule fois, vous serez en mesure d’obtenir des réponses à la plupart des questions qui sont survenus votre premier temps via par expérimentation supplémentaire avec les opérations et en lisant les articles référencés.

## <a name="plan-azure-resource-use"></a>Planifier l’utilisation des ressources Azure
Une fois que vous avez une conception de haut niveau pour votre application, vous pouvez définir :

- Les ressources Azure inclut votre application. Vous pouvez générer votre application et le déployer comme une application Web de Azure avec une base de données de SQL Azure.  Vous pouvez créer votre application sur des machines virtuelles à l’aide de PHP et MySQL ou IIS et de SQL Server ou d’autres composants. L’article de [Service d’application Azure, les Services en nuage et comparaison des Machines virtuelles]( app-service-web/choose-web-site-cloud-service-vm.md) vous permet de décider quelles ressources Azure que vous souhaitez utiliser pour votre application.
- Les niveaux de service requis disponibilité, sécurité et échelle répondant à votre application.

## <a name="download-an-existing-template"></a>Télécharger un modèle existant
Un modèle de gestionnaire de ressources Azure définit toutes les ressources Azure que votre application utilise. Plusieurs modèles existent déjà que vous pouvez déployer directement dans le portail Azure, ou télécharger, modifier et enregistrer dans un système de contrôle de code source avec le code de votre application.  Suivez les étapes ci-dessous pour télécharger un modèle existant.

1. Parcourir les modèles existants dans le référentiel de GitHub de [Modèles de démarrage rapide d’Azure](https://github.com/Azure/azure-quickstart-templates/) . Dans la liste, vous verrez un dossier «[201-web-app--base de données sql](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)». Étant donné que de nombreuses applications personnalisées incluent une application web et la base de données SQL, ce modèle est utilisé à titre d’exemple dans le reste de cet article pour vous aider à comprendre comment utiliser les modèles. Il est au-delà de la portée de cet article pour bien expliquer tout ce modèle crée et configure, mais si vous prévoyez de l’utiliser pour créer des environnements réels dans votre organisation, vous souhaiterez comprendre complètement qu’en lisant l’article de la [disposition d’une application web avec une base de données SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md) .
Remarque : cet article a été écrit pour la version de décembre 2015 du modèle [201-web-app--base de données sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) . Les liens ci-dessous renvoient aux modèle et des fichiers de paramètre pour cette version du modèle.
2. Cliquez sur le fichier [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) dans le dossier 201-web-app--base de données sql pour afficher son contenu. C’est le fichier de modèle de gestionnaire de ressources Azure. 
3. Dans le mode d’affichage, cliquez sur le bouton «[Raw](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)». 
4. Avec votre souris, sélectionnez le contenu de ce fichier et les enregistrer sur votre ordinateur sous la forme d’un fichier nommé « TestApp1-Template.json ». 
5. Examinez le contenu du modèle et notez les éléments suivants :
 - Section de **ressources** : cette section définit les types de ressources Azure créés par ce modèle. Parmi les autres types de ressources, ce modèle crée des ressources [Azure Web App](app-service-web/app-service-web-overview.md) et la [Base de données de SQL Azure](sql-database/sql-database-technical-overview.md) . Si vous préférez exécuter et de gérer les serveurs web et SQL dans des machines virtuelles, vous pouvez utiliser les modèles «[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)» ou «[feu-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)», mais les instructions fournies dans cet article sont basés sur le modèle de [201-web-app--base de données sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) .
 - **Paramètres de** section : cette section définit les paramètres de chaque ressource peut être configuré avec. Certains des paramètres spécifiés dans le modèle ont des propriétés de « defaultValue », tandis que d’autres ne le font pas. Lorsque vous déployez des ressources Azure avec un modèle, vous devez fournir des valeurs pour tous les paramètres qui n’ont pas de propriétés ValeurParDéfaut (DefaultValue) spécifiées dans le modèle.  Si vous ne fournissez pas de valeurs pour les paramètres avec les propriétés defaultValue, la valeur spécifiée pour le paramètre valeur par défaut dans le modèle est utilisée.

Un modèle définit les ressources Azure sont créées et les paramètres de chaque ressource peut être configuré avec. Pour plus d’informations sur les modèles et la création de votre propre par la lecture de l’article [méthodes conseillées pour la conception de modèles de gestionnaire de ressources Azure](best-practices-resource-manager-design-templates.md) .

## <a name="download-and-customize-an-existing-parameter-file"></a>Télécharger et personnaliser un fichier de paramètres existant

Bien que vous souhaiterez probablement la *même* Azure ressources créées dans chaque environnement, vous souhaiterez probablement la configuration des ressources pour être *différente* dans chaque environnement.  Il s’agit des fichiers de paramètre interviennent. Créer des fichiers de paramètres qui contiennent des valeurs uniques dans chaque environnement en suivant les étapes ci-dessous.   

1. Permet d’afficher le contenu du fichier [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) dans le dossier 201-web-app--base de données sql. Il s’agit du fichier de paramètres pour le fichier de modèle que vous avez enregistré dans la section précédente. 
2. Dans le mode d’affichage, cliquez sur le bouton «[Raw](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)». 
3. Avec votre souris, sélectionnez le contenu de ce fichier et les enregistrer dans les trois fichiers distincts sur votre ordinateur avec les noms suivants :
 - Paramètres-TestApp1-Development.json
 - Paramètres-TestApp1-Test.json
 - TestApp1-paramètres-Pre-Production.json

3. À l’aide de n’importe quel texte ou un éditeur de JSON, modifiez le fichier de paramètres d’environnement développement créé à l’étape 3, en remplaçant les valeurs répertoriées à droite des valeurs de paramètre dans le fichier avec les *valeurs* répertoriées à droite des **paramètres** ci-dessous : 
 - **nom du site**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *États-Unis centre*
 - **nom du serveur**: *testapp1devsrv*
 - **serverLocation**: *États-Unis centre*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *remplacer votre mot de passe*
 - **databaseName**: *testapp1devdb*

4. À l’aide de n’importe quel texte ou un éditeur de JSON, modifier le fichier de paramètres d’environnement Test créé à l’étape 3, en remplaçant les valeurs répertoriées à droite des valeurs de paramètre dans le fichier avec les *valeurs* répertoriées à droite des **paramètres** ci-dessous :
 - **nom du site**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**: *États-Unis centre*
 - **nom du serveur**: *testapp1testsrv*
 - **serverLocation**: *États-Unis centre*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *remplacer votre mot de passe*
 - **databaseName**: *testapp1testdb*

5. À l’aide de n’importe quel texte ou un éditeur de JSON, modifiez le fichier de paramètres de préproduction que vous avez créé à l’étape 3.  Remplacez tout le contenu du fichier avec les nouveautés ci-dessous :

        {
          "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion" : "1.0.0.0",
          "parameters" : {
        "administratorLogin" : {
          "value" : "testApp1Admin"
        },
        "administratorLoginPassword" : {
          "value" : "replace with your password"
        },
        "databaseName" : {
          "value" : "testapp1preproddb"
        },
        "hostingPlanName" : {
          "value" : "TestApp1PreProdPlan"
        },
        "serverLocation" : {
          "value" : "Central US"
        },
        "serverName" : {
          "value" : "testapp1preprodsrv"
        },
        "siteLocation" : {
          "value" : "Central US"
        },
        "siteName" : {
          "value" : "TestApp1PreProdApp"
        },
        "sku" : {
          "value" : "Standard"
        },
            "requestedServiceObjectiveName" : {
              "value" : "S1"
        }
          }
        }

Dans le fichier de paramètres de préproduction ci-dessus, les paramètres de **référence sku** et **requestedServiceObjectiveName** ont été *ajoutés*, considérant qu’ils n’ont pas été ajoutés dans les fichiers de paramètres de Test et de développement. C’est parce qu’il existe des valeurs par défaut spécifiées pour ces paramètres dans le modèle et dans les environnements de développement et de Test, les valeurs par défaut sont utilisés, mais dans la Production des valeurs d’environnement par défaut pour ces paramètres sont utilisés.

La raison pour laquelle les valeurs par défaut sont utilisées pour ces paramètres dans l’environnement de pré-production est pour tester les valeurs de ces paramètres vous pouvez choisir pour votre environnement de Production, afin qu’ils peuvent également être testés.  Ces paramètres à tous les concernent la Azure [plans d’hébergement Web App](https://azure.microsoft.com/pricing/details/app-service/), ou **point de stock** et Azure [De base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/)ou **requestedServiceObjectiveName** qui sont utilisés par l’application.  Références SKU différentes et les noms de service objectif ont des coûts différents et fonctionnalités et prend en charge des mesures au niveau de service différent.

Le tableau ci-dessous répertorie les valeurs par défaut pour ces paramètres spécifiés dans le modèle et les valeurs qui sont utilisées au lieu des valeurs par défaut dans le fichier de paramètres de préproduction.

| Paramètre | Valeur par défaut | Valeur du paramètre |
|---|---|---|
| **point de stock** | Gratuit | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## <a name="create-environments"></a>Créer des environnements
Toutes les ressources Azure doivent être créés au sein d’un [Groupe de ressources Azure](azure-resource-manager/resource-group-overview.md). Groupes de ressources permettent de regrouper des ressources Azure afin qu’ils peuvent être gérés collectivement.  [Autorisations](./active-directory/role-based-access-built-in-roles.md) peuvent être attribuées à des groupes de ressources, tels que certaines personnes au sein de votre organisation peuvent créer, modifier, supprimer ou afficher, ainsi que les ressources qu’ils contiennent.  Alertes et informations de facturation pour les ressources dans le groupe de ressources peuvent être affichés dans le [Portail Azure](https://portal.azure.com). Groupes de ressources sont créés dans une [région](https://azure.microsoft.com/regions/)d' Azure.  Dans cet article, toutes les ressources sont créées dans la région Amérique centrale. Lorsque vous démarrez la création d’environnements réels, vous allez choisir la région qui correspond le mieux à vos besoins. 

Créer des groupes de ressources pour chaque environnement en utilisant l’une des méthodes ci-dessous.  Toutes les méthodes va obtenir le même résultat.

###<a name="azure-command-line-interface-cli"></a>Interface de ligne de commande (CLI) pour Azure

Vérifiez que vous disposez du CLI [installé](xplat-cli-install.md) sur soit Windows, OS X, ou ordinateur Linux et que vous avez [connecté](xplat-cli-connect.md) votre [compte Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (également appelé un compte de travail ou à l’école) à votre abonnement Azure. À partir de la ligne de commande CLI, tapez la commande suivante pour créer le groupe de ressources de l’environnement de développement.

    azure group create "TestApp1-Development" "Central US"

Si elle réussit, la commande renverra le texte suivant :

    info:    Executing command group create
    + Getting resource group TestApp1-Development
    + Creating resource group TestApp1-Development
    info:    Created resource group TestApp1-Development
    data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
    data:    Name:                TestApp1-Development
    data:    Location:            centralus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Pour créer le groupe de ressources de l’environnement de Test, tapez la commande suivante :

    azure group create "TestApp1-Test" "Central US"

Pour créer le groupe de ressources de l’environnement de préproduction, tapez la commande suivante :

    azure group create "TestApp1-Pre-Production" "Central US"

###<a name="powershell"></a>PowerShell

Assurez-vous que vous avez Azure PowerShell 1.01 ou supérieur installé sur un ordinateur Windows et connecté votre [compte Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (également appelé un compte de travail ou à l’école) à votre abonnement, comme détaillé dans l’article [comment installer et configurer Azure PowerShell](powershell-install-configure.md) . À partir d’une invite de commande PowerShell, tapez la commande suivante pour créer le groupe de ressources de l’environnement de développement.

    New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

Si elle réussit, la commande renverra le texte suivant :

    ResourceGroupName : TestApp1-Development
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Pour créer le groupe de ressources de l’environnement de Test, tapez la commande suivante :

    New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Pour créer le groupe de ressources de l’environnement de préproduction, tapez la commande suivante :

    New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###<a name="azure-portal"></a>Azure portal

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (également appelé une école ou travail). Cliquez sur--nouvelles Gestion--> groupe de ressources et entrez « TestApp1-développement » dans la zone de nom de groupe de ressources votre abonnement et sélectionnez « États-Unis » dans la zone emplacement du groupe ressource comme indiqué dans l’image ci-dessous.
   ![Portail](./media/solution-dev-test-environments/rgcreate.png)
2. Cliquez sur le bouton Créer pour créer le groupe de ressources.
3. Cliquez sur Parcourir, faites défiler la liste pour les groupes de ressources et cliquez sur les groupes de ressources, comme indiqué ci-dessous.
   ![Portail](./media/solution-dev-test-environments/rgbrowse.png) 
4. Après avoir cliqué sur les groupes de ressources, vous verrez la lame de groupes de ressources avec votre nouveau groupe de ressources.
   ![Portail](./media/solution-dev-test-environments/rgview.png)
5. Création du Test de TestApp1 et ressources TestApp1-Pre-Production groupes de la même façon que vous avez créé le groupe de ressources de développement TestApp1 ci-dessus.

##<a name="deploy-resources-to-environments"></a>Déployer des ressources dans les environnements

Déployer les ressources Azure pour les groupes de ressources pour chaque environnement en utilisant le fichier de modèle de la solution et les fichiers de paramètres pour chaque environnement à l’aide d’une des méthodes ci-dessous.  Les deux méthodes seront obtenir le même résultat.

###<a name="azure-command-line-interface-cli"></a>Interface de ligne de commande (CLI) pour Azure

À partir de la ligne de commande CLI, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources créé pour l’environnement de développement, remplacement [chemin] avec le chemin d’accès vers les fichiers que vous avez enregistré au cours des étapes précédentes.

    azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Lorsque vous voyez un message « En attente de déploiement terminer » pendant quelques minutes, la commande renvoie les informations suivantes si elle réussit :

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "Deployment1"
    + Waiting for deployment to complete
    data:    DeploymentName     : Deployment1
    data:    ResourceGroupName  : TestApp1-Development
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
    data:    Mode               : Incremental
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ----------------------------
    data:    siteName                       String        TestApp1DevApp
    data:    hostingPlanName                String        TestApp1DevPlan
    data:    siteLocation                   String        Central US
    data:    sku                            String        Free
    data:    workerSize                     String        0
    data:    serverName                     String        testapp1devsrv
    data:    serverLocation                 String        Central US
    data:    administratorLogin             String        testapp1Admin
    data:    administratorLoginPassword     SecureString  undefined
    data:    databaseName                   String        testapp1devdb
    data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
    data:    edition                        String        Standard
    data:    maxSizeBytes                   String        1073741824
    data:    requestedServiceObjectiveName  String        S0
    info:    group deployment create command OKx

Si la commande échoue, résoudre des messages d’erreur et essayez de nouveau.  Les problèmes courants sont à l’aide des valeurs de paramètre qui ne respectent pas les contraintes d’attribution de noms de ressources Azure. Vous trouverez d’autres conseils de dépannage dans l’article de [déploiements de groupe de ressources de résolution des problèmes dans Azure](./resource-manager-troubleshoot-deployments-cli.md) .

À partir de la ligne de commande CLI, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources créé pour l’environnement de Test, remplacement [chemin] avec le chemin d’accès vers les fichiers que vous avez enregistré au cours des étapes précédentes.

    azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

À partir de la ligne de commande CLI, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources créé pour l’environnement de préproduction, remplacement [chemin] avec le chemin d’accès vers les fichiers que vous avez enregistré au cours des étapes précédentes.

    azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###<a name="powershell"></a>PowerShell

À partir d’une invite de commandes PowerShell d’Azure (version 1,01 ou supérieure), tapez la commande ci-dessous pour déployer les ressources au groupe de ressources créé pour l’environnement de développement, remplacement [chemin] avec le chemin d’accès vers les fichiers que vous avez enregistré au cours des étapes précédentes.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Lorsque vous voyez un curseur qui clignote pendant quelques minutes, la commande renvoie les informations suivantes si elle réussit :

    DeploymentName    : Deployment1
    ResourceGroupName : TestApp1-Development
    ProvisioningState : Succeeded
    Timestamp         : XX/XX/XXXX 2:44:48 PM
    Mode              : Incremental
    TemplateLink      : 
    Parameters        : 
                        Name             Type                       Value     
                        ===============  =========================  ==========
                        siteName         String                     TestApp1DevApp
                        hostingPlanName  String                     TestApp1DevPlan
                        siteLocation     String                     Central US
                        sku              String                     Free      
                        workerSize       String                     0         
                        serverName       String                     testapp1devsrv
                        serverLocation   String                     Central US
                        administratorLogin  String                     testapp1Admin
                        administratorLoginPassword  SecureString                         
                        databaseName     String                     testapp1devdb
                        collation        String                     SQL_Latin1_General_CP1_CI_AS
                        edition          String                     Standard  
                        maxSizeBytes     String                     1073741824
                        requestedServiceObjectiveName  String                     S0        
                        
    Outputs           :

  Si la commande échoue, résoudre des messages d’erreur et essayez de nouveau.  Les problèmes courants sont à l’aide des valeurs de paramètre qui ne respectent pas les contraintes d’attribution de noms de ressources Azure. Vous trouverez d’autres conseils de dépannage dans l’article de [déploiements de groupe de ressources de résolution des problèmes dans Azure](./resource-manager-troubleshoot-deployments-powershell.md) .

  À partir d’une invite de commande PowerShell, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources créé pour l’environnement de Test, remplacement [chemin] avec le chemin d’accès vers les fichiers que vous avez enregistré au cours des étapes précédentes.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  À partir d’une invite de commande PowerShell, tapez la commande ci-dessous pour déployer les ressources au groupe de ressources créé pour l’environnement de préproduction, remplacement [chemin] avec le chemin d’accès vers les fichiers que vous avez enregistré au cours des étapes précédentes.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Les fichiers de modèle et de paramètre peuvent être créée et mise à jour avec le code de votre application dans un système de contrôle de code source.  Vous pouvez également enregistrer les commandes ci-dessus pour les fichiers de script et de les enregistrer avec votre code.

> [AZURE.NOTE] Vous pouvez déployer le modèle sur Azure directement en cliquant sur le bouton « Déployer sur Azure » sur l’article de la [disposition d’une application Web avec une base de données SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) .  Cela peut s’avérer utile d’en savoir plus sur les modèles, mais cela ainsi ne pas vous permettre de modifier, de version et enregistrer vos valeurs de paramètre de modèle avec le code de votre application, afin que les détails supplémentaires sur cette méthode n’est pas abordé dans cet article.

## <a name="maintain-environments"></a>Des environnements
Pendant le développement, la configuration des ressources dans les différents environnements Azure peut-être être modifiée incohérente par accident ou intentionnellement.  Cela peut entraîner des dépannages et la résolution des problèmes au cours du cycle de développement d’application.

1. Modifier les environnements en ouvrant le [portail Azure](https://portal.azure.com). 
2. Signer dedans avec le même compte que celui utilisé pour effectuer les étapes ci-dessus. 
3. Comme indiqué dans l’image ci-dessous, cliquez sur Parcourir >--> les groupes de ressources (vous devrez peut-être faire défiler pour voir les groupes de ressources).
   ![Portail](./media/solution-dev-test-environments/rgbrowse.png)
4. Après avoir cliqué sur les groupes de ressources dans l’image ci-dessus, vous verrez la lame de groupes de ressources et les groupes de trois ressources que vous avez créé dans une étape précédente, comme illustré dans l’image ci-dessous. Cliquez sur le groupe de ressources de développement TestApp1 et vous verrez la lame qui répertorie les ressources créées par le modèle dans le déploiement de groupe de ressource TestApp1-développement que vous terminé dans une étape précédente.  Supprimer la ressource de l’application Web de TestApp1DevApp en cliquant sur les TestApp1DevApp de la lame de groupe de ressources de développement de TestApp1, puis en cliquant sur Supprimer dans la blade d’application Web de TestApp1DevApp.
   ![Portail](./media/solution-dev-test-environments/portal2.png)
5. Cliquez sur « Oui » lorsque le portail vous demande d’indiquer si vous êtes sûr de que vouloir supprimer la ressource. Fermeture de la lame de groupe de ressources de développement TestApp1 et en ouvrant de nouveau seront affichera il sans l’application Web que vous venez de supprimer.  Le contenu du groupe de ressources est maintenant différent de ce qu’ils doivent être. Vous pouvez tester davantage par la suppression de plusieurs ressources à partir de plusieurs groupes de ressources, ou encore modifier les paramètres de configuration de certaines ressources. Au lieu d’utiliser le portail Azure pour supprimer une ressource d’un groupe de ressources, vous pouvez utiliser la commande PowerShell [Supprimer-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) ou l’ou les commande « Supprimer ressource azure » à partir de la CLI pour accomplir la même tâche.
6. Pour obtenir toutes les ressources et la configuration qui sont supposés pour être dans les groupes de ressources à l’état dans, déployez de nouveau les environnements pour les groupes de ressources en utilisant les mêmes commandes que vous avez utilisé dans la section [ressources de déploiement pour des environnements](#deploy-resources-to-environments) , mais remplacer « Deployment1 » avec « Deployment2 ».
7.  Comme indiqué dans la section Résumé de la lame de TestApp1-développement dans l’image affichée à l’étape 4, vous verrez que l’application Web que vous avez supprimé dans le portail dans l’étape précédente existe encore, comme toutes les autres ressources vous avez choisi de supprimer. Si vous avez modifié la configuration de toutes les ressources, vous pouvez également vérifier qu’ils été définies précédent pour les valeurs dans les fichiers de paramètres trop. Un des avantages du déploiement de vos environnements avec le Gestionnaire de ressources Azure modèles est que vous pouvez facilement redéployer les environnements vers un état connu à tout moment.
8. Si vous cliquez sur le texte sous « Déploiement » dans l’image ci-dessous, vous verrez une lame qui affiche l’historique de déploiement pour le groupe de ressources.  Puisque vous avez utilisé le nom « Deployment1 » pour le premier déploiement et « Deployment2 » pour le deuxième déploiement, vous aurez deux entrées.  Cliquez sur un déploiement pour afficher une lame qui affiche les résultats pour chaque déploiement.
  ![Portail](./media/solution-dev-test-environments/portal3.png)



## <a name="delete-environments"></a>Supprimer des environnements
Une fois que vous avez fini d’utiliser un environnement, vous souhaiterez probablement supprimer afin de vous éviter d’avoir des frais d’utilisation de ressources Azure, que vous n’utilisez plus.  Suppression des environnements est encore plus facile que de les créer.  Dans les étapes précédentes, groupes de ressources individuels Azure ont été créés pour chaque environnement et ensuite les ressources ont été déployés dans les groupes de ressources. 

Supprimer les environnements à l’aide d’une des méthodes ci-dessous.  Toutes les méthodes va obtenir le même résultat.

### <a name="azure-cli"></a>CLI Azure

À partir d’une invite de commandes CLI, tapez ce qui suit :

    azure group delete "TestApp1-Development"

Lorsque vous y êtes invité, entrez y et appuyez sur ENTRÉE pour supprimer l’environnement de développement et de toutes ses ressources. Après quelques minutes, la commande renvoie les éléments suivants :

    info:    group delete command OK

À partir d’une invite de commandes CLI, tapez la commande suivante pour supprimer les autres environnements :

    azure group delete "TestApp1-Test"
    azure group delete "TestApp1-Pre-Production"
  
### <a name="powershell"></a>PowerShell

À partir d’une invite de commandes PowerShell d’Azure (version 1,01 ou supérieure), tapez la commande ci-dessous pour supprimer le groupe de ressources et tout son contenu.    

    Remove-AzureRmResourceGroup -Name TestApp1-Development

Lorsque vous y êtes invité si vous êtes sûr de que vouloir supprimer le groupe de ressources, entrez y, suivie de la touche ENTRÉE.

Tapez la commande suivante pour supprimer les autres environnements :

    Remove-AzureRmResourceGroup -Name TestApp1-Test
    Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### <a name="azure-portal"></a>Azure portal

1. Dans le portail Azure, accédez aux groupes de ressources comme vous le faisiez dans les étapes précédentes. 
2. Sélectionnez le groupe de ressources de développement TestApp1 et puis cliquez sur Supprimer dans la lame de groupe de ressources de développement-TestApp1. Une nouvelle blade s’affiche. Entrez le nom de groupe de ressources, puis cliquez sur le bouton Supprimer.
![Portail](./media/solution-dev-test-environments/rgdelete.png)
3. Supprimer le Test TestApp1 et ressources TestApp1-Pre-Production groupes de la même façon que vous avez supprimé le groupe de ressources de développement-TestApp1.

Quelle que soit la méthode que vous utilisez, les groupes de ressources et toutes les ressources que leur contenus n’existe plus et vous allez n’est plus entraîner des frais de facturation pour les ressources.  

Pour réduire l’Azure ressource l’utilisation des dépenses au cours du développement d’application [Azure Automation](automation/automation-intro.md) vous permet de planifier des travaux qui :

- Arrêter des ordinateurs virtuels à la fin de chaque journée et les redémarrer au début de chaque jour.
- Supprimer des environnements tout à la fin de chaque jour et recréer au début de chaque jour.
 
Maintenant que vous avez rencontré il est facile à créer, mettre à jour et suppression de développement et test des environnements, vous pouvez en savoir plus sur ce que vous avez simplement faire a encore expérimenter les étapes ci-dessus et en lisant les références contenues dans cet article.

## <a name="next-steps"></a>Étapes suivantes

- [Déléguer le contrôle administratif](./active-directory/role-based-access-control-configure.md) pour des ressources différentes dans chaque environnement en affectant Microsoft Azure AD groupes ou des utilisateurs à des rôles spécifiques qui ont la capacité d’exécuter un sous-ensemble des opérations sur des ressources Azure.
- [Assigner des balises](resource-group-using-tags.md) pour les groupes de ressources pour chaque environnement et/ou les ressources individuelles. Vous pouvez ajouter une balise « Environnement » à vos groupes de ressources et définir sa valeur pour qu’elles correspondent à des noms de votre environnement. Balises peuvent être particulièrement utiles lorsque vous devez organiser les ressources pour la facturation ou de gestion.
- Alertes de l’analyseur et de facturation pour les ressources du groupe ressources dans le [portail Azure](https://portal.azure.com).
