<properties
    pageTitle="Intégration continue dans VS Team Services à l’aide de projets de groupe de ressources Azure | Microsoft Azure"
    description="Décrit comment configurer l’intégration continue dans Visual Studio Team Services à l’aide de projets de déploiement de groupe de ressources Azure dans Visual Studio."
    services="visual-studio-online"
    documentationCenter="na"
    authors="mlearned"
    manager="erickson-doug"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="mlearned" />

# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Intégration continue dans Visual Studio Team Services à l’aide de projets de déploiement de groupe de ressources Azure

Pour déployer un modèle d’Azure, vous devez effectuer les tâches passent par différents stades : Build, Test, les copier vers Azure (également appelé « Stockage temporaire ») et de déployer le modèle.  Il existe deux méthodes différentes pour effectuer cette opération dans Visual Studio Team Services (VS Team Services). Les deux méthodes offrent les mêmes résultats, donc choisir celle qui convient le mieux à votre flux de travail.

-   Ajouter une étape unique à votre définition de build qui exécute le script PowerShell qui est inclus dans le projet de déploiement de groupe de ressources Azure (déployer-AzureResourceGroup.ps1). Le script copie les artefacts et puis déploie le modèle.
-   Ajouter des que services d’équipe VS plusieurs étapes de génération, chacun d’eux exécute une tâche de l’étape.

Cet article montre comment utiliser la première option (utilisez une définition de build pour exécuter le script PowerShell). Un des avantages de cette option sont le script utilisé par les développeurs de Visual Studio du même script qui est utilisé par les Services d’équipe VS. Cette procédure suppose que vous disposez déjà d’un projet de déploiement Visual Studio archivé dans VS Team Services.

## <a name="copy-artifacts-to-azure"></a>Copie les artefacts vers Azure 

Quel que soit le scénario, si vous disposez de tous les artefacts qui sont requis pour le déploiement du modèle, vous devez lui donner accès du Gestionnaire de ressources Azure leur. Ces artefacts peuvent inclure des fichiers tels que :

-   Modèles imbriqués
-   Les scripts de configuration et DSC
-   Fichiers binaires d’application

### <a name="nested-templates-and-configuration-scripts"></a>Les modèles imbriqués et des Scripts de Configuration
Lorsque vous utilisez les modèles fournis par Visual Studio (ou générée avec des extraits de code de Visual Studio), le script PowerShell stades non seulement les artefacts, il paramètre également l’URI pour les ressources des déploiements. Le script copie les artefacts dans un conteneur sécurisé dans Azure, crée un jeton d’associations de sécurité de ce conteneur, puis transmet ensuite ces informations sur le déploiement du modèle. Reportez-vous à la section [Création d’un modèle de déploiement](https://msdn.microsoft.com/library/azure/dn790564.aspx) pour plus d’informations sur les modèles imbriqués.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Configurer un déploiement continu dans VS Team Services

Pour appeler le script PowerShell dans VS Team Services, vous devez mettre à jour votre définition de build. En bref, les étapes sont les suivantes : 

1.  Modifier la définition de build.
1.  Mettre en place une autorisation Azure dans VS Team Services.
1.  Ajouter une étape de génération Azure PowerShell qui référence le script PowerShell dans le projet de déploiement de groupe de ressources Azure.
1.  Définissez la valeur du paramètre *- ArtifactsStagingDirectory* pour travailler avec un projet créé dans les Services d’équipe VS.

### <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

La procédure suivante vous guide dans les étapes nécessaires pour configurer le déploiement en continu dans VS Team Services 

1.  Modifiez votre définition de build et Team Services et ajoutez une étape de génération Azure PowerShell. Sélectionnez la définition de build dans la catégorie **des définitions de Build** , puis choisissez le lien **Modifier** .

    ![][0]

1.  Ajouter une nouvelle étape de build **Azure PowerShell** à la définition de build et choisissez **Ajouter créer étape...** bouton.

    ![][1]

1.  Choisissez la catégorie de **tâches de déploiement** , sélectionnez la tâche **Azure PowerShell** et puis cliquez sur le bouton **Ajouter** .

    ![][2]

1.  Choisissez l’étape de génération **Azure PowerShell** et remplissez ses valeurs.

    1.  Si vous disposez déjà d’un point de terminaison de service Azure ajouté à VS Team Services, sélectionnez l’abonnement dans **l’Abonnement Azure** liste déroulante et passez à la section suivante. 

        Si vous n’avez pas un point de terminaison de service Azure dans VS Team Services, vous devez en ajouter un. Cette sous-section passe en revue le processus. Si votre compte Azure utilise un compte Microsoft (comme Hotmail), vous devrez procéder comme suit pour obtenir une authentification Principal du Service.

    1.  Cliquez sur le lien **Gérer** en regard de l' **Abonnement Azure** déroulante de la zone de liste.

        ![][3]

    1. Choisissez la **Nouveau point de terminaison de Service** liste déroulante **Azure** .

        ![][4]

    1.  Dans la boîte de dialogue **Ajouter un abonnement Azure** , sélectionnez l’option de **Principal du Service** .

        ![][5]

    1.  Ajouter des informations sur votre abonnement Azure à la boîte de dialogue **Ajouter un abonnement Azure** . Vous devez disposer des éléments suivants :
        -   Id de l’abonnement
        -   Nom de l’abonnement
        -   Id d’entité de service
        -   Clé d’entité de service
        -   Id de clients

    1.  Ajouter un nom de votre choix dans la zone de nom **d’abonnement** . Cette valeur apparaîtra plus loin dans le menu déroulant dans VS Team Services de **l’Abonnement Azure** . 

    1.  Si vous ne connaissez pas votre ID d’abonnement Azure, vous pouvez utiliser une des commandes suivantes pour l’obtenir.
        
        Pour les scripts de PowerShell, utiliser :

        `Get-AzureRmSubscription`

        Pour la CLI d’Azure, utilisez :

        `azure account show`
    

    1.  Pour obtenir un ID d’entité de Service, clé de Principal du Service et le code de client, suivent la procédure décrite dans [l’application de création Active Directory et de principal du service à l’aide de portail](resource-group-create-service-principal-portal.md) ou [l’authentification d’un principal de service avec le Gestionnaire de ressources Azure](resource-group-authenticate-service-principal.md).

    1.  Ajoutez les valeurs de l’ID d’entité de Service, clé de Principal du Service et des ID de clients à la boîte de dialogue **Ajouter un abonnement Azure** et cliquez sur le bouton **OK** .

        Vous disposez maintenant d’un Principal de Service valide à utiliser pour exécuter le script PowerShell d’Azure.

1.  Modifier la définition de build et choisir l’étape de génération **Azure PowerShell** . Sélectionnez l’abonnement dans **l’Abonnement Azure** liste déroulante. (Si l’abonnement n’apparaît pas, cliquez sur le bouton **Actualiser** suivant le lien **Gérer** .) 

    ![][8]

1.  Fournir un chemin d’accès au script de déploiement-AzureResourceGroup.ps1 PowerShell. Pour ce faire, cliquez sur le bouton points de suspension (...) en regard de la zone de **Chemin d’accès du Script** , recherchez le script PowerShell de déployer-AzureResourceGroup.ps1 dans le dossier **Scripts** de votre projet, sélectionnez-le et puis cliquez sur le bouton **OK** . 

    ![][9]

1. Après avoir sélectionné le script, mettre à jour le chemin d’accès au script afin qu’il s’exécute à partir de la Build.StagingDirectory (le même répertoire que *ArtifactsLocation* a la valeur). Vous pouvez le faire en ajoutant « $(Build.StagingDirectory)/ « au début du chemin du script.

    ![][10]

1.  Dans la zone **Arguments de Script** , entrez les paramètres suivants (dans une seule ligne). Lorsque vous exécutez le script dans Visual Studio, vous pouvez voir comment VS utilise les paramètres dans la fenêtre **sortie** . Vous pouvez utiliser ceci comme point de départ pour définir les valeurs de paramètre dans votre étape de génération.

  	| Paramètre | Description|
  	|---|---|
  	| -ResourceGroupLocation           | La valeur de géolocalisation où le groupe de ressources se trouve, par exemple **eastus** ou **« Extrême-Orient US »**. (Ajouter des guillemets simples s’il existe un espace dans le nom). Pour plus d’informations, reportez-vous à la section [Régions d’Azure](https://azure.microsoft.com/en-us/regions/) .|                                                                                                                                                                                                                              |
  	| -ResourceGroupName               | Le nom du groupe de ressources utilisé pour ce déploiement.|                                                                                                                                                                                                                                                                                                                                                                                                                |
  	| -UploadArtifacts                 | Ce paramètre, le cas échéant, spécifie qu’artefacts doivent être téléchargées vers Azure à partir du système local. Vous devez uniquement définir cette option si le déploiement de votre modèle requiert des artefacts supplémentaires que vous souhaitez organiser à l’aide de script PowerShell (tels que les scripts de configuration ou les modèles imbriqués).                                                                                                                                                                 |
  	| -StorageAccountName              | Le nom du compte de stockage utilisé pour les artefacts de scène pour ce déploiement. Ce paramètre est requis uniquement si vous copiez des artefacts à Azure. Ce compte de stockage ne sera pas créé automatiquement par le déploiement, il doit déjà exister.|                                                                                                                                                                                                                     |
  	| -StorageAccountResourceGroupName | Le nom du groupe de ressources associé au compte de stockage. Ce paramètre est requis uniquement si vous copiez des artefacts à Azure.|                                                                                                                                                                                                                                                                                                                               |
  	| -TemplateFile                    | Le chemin d’accès au fichier modèle dans le projet de déploiement de groupe de ressources Azure. Pour améliorer la flexibilité, utilisez un chemin d’accès de ce paramètre dépend de l’emplacement du script PowerShell au lieu d’un chemin d’accès absolu.|
  	| -TemplateParametersFile          | Le chemin d’accès au fichier de paramètres dans le projet de déploiement de groupe de ressources Azure. Pour améliorer la flexibilité, utilisez un chemin d’accès de ce paramètre dépend de l’emplacement du script PowerShell au lieu d’un chemin d’accès absolu.|
  	| -ArtifactStagingDirectory        | Ce paramètre vous permet du PowerShell script connaître le dossier à partir duquel les fichiers binaires du projet doivent être copiés. Cette valeur remplace la valeur par défaut utilisée par le script PowerShell. Pour utiliser de VS Team Services, définissez la valeur sur : ArtifactStagingDirectory - $(Build.StagingDirectory)                                                                                                                                                                                              |

    Voici un exemple d’arguments de script (ligne interrompue pour une meilleure lisibilité) :

    ``` 
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)' 
    ```

    Lorsque vous avez terminé, la zone **Arguments de Script** doit ressembler à la suivante.

    ![][11]

1.  Après que vous avez ajouté que tous les éléments requis pour PowerShell Azure étape de génération, cliquez sur le bouton pour générer le projet de création de la **file d’attente** . L’écran **créer** affiche la sortie du script PowerShell.

## <a name="next-steps"></a>Étapes suivantes

Lisez la [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour en savoir plus sur le Gestionnaire de ressources Azure et groupes de ressources Azure.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
