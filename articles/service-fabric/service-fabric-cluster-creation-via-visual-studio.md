<properties
   pageTitle="Configuration d’un cluster de Service Fabric à l’aide de Visual Studio | Microsoft Azure"
   description="Décrit comment configurer un cluster Service Fabric à l’aide du modèle de gestionnaire de ressources Azure créé par un projet du groupe de ressources Azure dans Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Configurer un cluster Service Fabric à l’aide de Visual Studio
Cet article décrit comment configurer un cluster Azure Fabric de Service à l’aide de Visual Studio et un modèle de gestionnaire de ressources Azure. Nous utiliserons un projet de groupe de ressources de Visual Studio Azure pour créer le modèle. Une fois que le modèle a été créé, il peut être déployé directement vers Azure à partir de Visual Studio. Il peut également être utilisé à partir d’un script ou dans le cadre de la facilité d’intégration continue (CI).

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Créer un modèle de cluster Service Fabric à l’aide d’un projet de groupe ressource Azure
Pour commencer, ouvrez Visual Studio et créez un projet de groupe ressource Azure (il est disponible dans le dossier de **nuage** ) :

![Boîte de dialogue Nouveau projet avec le projet du groupe de ressources Azure sélectionné][1]

Vous pouvez créer une nouvelle solution Visual Studio pour ce projet ou ajouter à une solution existante.

>[AZURE.NOTE] Si vous ne voyez pas le projet de groupe de ressources Azure sous le nœud Cloud, vous n’avez pas de l’Azure SDK installé. Lancement de Web Platform Installer ([Installer maintenant](http://www.microsoft.com/web/downloads/platform.aspx) si vous ne l’avez pas déjà), puis recherchez « Azure SDK for.NET » et installer la version qui est compatible avec votre version de Visual Studio.

Après avoir appuyé sur le bouton OK, Visual Studio vous demande de sélectionner le modèle de gestionnaire de ressources que vous souhaitez créer :

![Sélectionnez la boîte de dialogue modèle d’Azure avec le modèle de Service de Cluster de tissu sélectionné][2]

Sélectionnez le modèle de Service de Cluster de Fabric et cliquez de nouveau sur le bouton OK. Le projet et le modèle du Gestionnaire de ressources ont été créés.

## <a name="prepare-the-template-for-deployment"></a>Préparer le modèle pour le déploiement
Avant de déployer le modèle pour créer le cluster, vous devez fournir des valeurs pour les paramètres de modèle requis. Ces valeurs de paramètre sont lus à partir de la `ServiceFabricCluster.parameters.json` fichier, qui se trouve dans le `Templates` dossier du projet du groupe de ressources. Ouvrez le fichier et fournir les valeurs suivantes :

|Nom du paramètre           |Description|
|-----------------------  |--------------------------|
|adminUserName            |Le nom du compte administrateur pour les ordinateurs du Service Fabric (nœuds).|
|certificateThumbprint    |L’empreinte numérique du certificat qui sécurise le cluster.|
|sourceVaultResourceId    |L' *ID de ressource* du coffre-fort clé où est stocké le certificat qui sécurise le cluster.|
|certificateUrlValue      |URL du certificat de sécurité du cluster.|

Le modèle de gestionnaire de ressources de Visual Studio Service Fabric crée un cluster sécurisé qui est protégé par un certificat. Ce certificat est identifié par les paramètres de trois derniers modèle (`certificateThumbprint`, `sourceVaultValue`, et `certificateUrlValue`), et il doit exister dans un **Coffre-fort de clé Azure**. Pour plus d’informations sur la façon de créer le certificat de sécurité de cluster, consultez l’article le [Fabric du Service de cluster les scénarios de sécurité](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) .

## <a name="optional-change-the-cluster-name"></a>Facultatif : changez le nom du cluster
Chaque cluster Service Fabric a un nom. Création d’un cluster de tissu dans Azure, nom de cluster détermine (avec la région Azure) le nom de système de nom de domaine (DNS) pour le cluster. Par exemple, si vous nommez votre cluster `myBigCluster`et l’emplacement (région Azure) du groupe de ressources qui hébergera le nouveau cluster est des États-Unis, le nom DNS du cluster sera `myBigCluster.eastus.cloudapp.azure.com`.

Par défaut, le nom de cluster est automatiquement généré et rendu unique en attachant un suffixe aléatoire à un préfixe « cluster ». Cela rend très facile d’utiliser le modèle en tant que partie d’un système d' **intégration continue** (CI). Si vous souhaitez utiliser un nom spécifique pour votre cluster, qui soit significatif pour vous, définissez la valeur de la `clusterName` variable dans le fichier de modèle de gestionnaire de ressources (`ServiceFabricCluster.json`) pour le nom de votre choix. Il s’agit de la première variable définie dans ce fichier.

## <a name="optional-add-public-application-ports"></a>Facultatif : ajoutez des ports d’application publics
Vous pouvez également modifier les ports d’application publique pour le cluster avant de le déployer. Par défaut, le modèle s’ouvre deux ports TCP public (80 et 8081). Si vous avez besoin de plus de vos applications, modifier la définition de l’équilibreur de charge Azure dans le modèle. La définition est stockée dans le fichier de modèle principal (`ServiceFabricCluster.json`). Ouvrez ce fichier et recherchez les `loadBalancedAppPort`. Chaque port est associée à trois objets :

1. Une variable de modèle qui définit la valeur du port TCP pour le port :

    ```json
    "loadBalancedAppPort1": "80"
    ```

2. Une *sonde* qui définit la fréquence et la durée pendant laquelle la charge Azure équilibreur tente d’utiliser un nœud de Service Fabric spécifique avant de basculer sur un autre. Les sondes font partie de la ressource d’équilibrage de la charge. Voici la définition de la sonde pour le premier port d’application par défaut :

    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```

3. Une *règle d’équilibrage de charge* qui relie le port et la sonde, ce qui permet l’équilibrage de charge sur un jeu de nœuds de cluster Service Fabric :

    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
Si les applications que vous envisagez de déployer sur le cluster ont besoin de davantage de ports, vous pouvez les ajouter en création sonde supplémentaire et les définitions de règle de l’équilibrage de charge. Pour plus d’informations sur la façon de travailler avec un équilibreur de charge Azure grâce aux modèles du Gestionnaire de ressources, consultez [créer un équilibreur de charge interne à l’aide d’un modèle](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Déployer le modèle à l’aide de Visual Studio
Après avoir enregistré toutes les valeurs de paramètre requis dans le`ServiceFabricCluster.param.dev.json` le fichier, vous êtes prêt à déployer le modèle et créer votre cluster Service Fabric. Droit sur le projet du groupe de ressources dans l’Explorateur de solutions Visual Studio et choisissez **déployer | Nouveau déploiement...**. Si nécessaire, Visual Studio affiche la boîte de dialogue **déployer au groupe de ressources** , vous demandant de s’authentifier sur Azure :

![Déployer, boîte de dialogue groupe de ressources][3]

La boîte de dialogue vous permet de choisir un groupe de ressources existant de gestionnaire de ressources pour le cluster et vous donne la possibilité de créer un nouveau. Normalement, il est judicieux d’utiliser un groupe de ressources séparé pour un cluster de Service Fabric.

Après avoir appuyé sur le bouton déployer, Visual Studio vous invite à confirmer les valeurs de paramètre de modèle. Appuyez sur le bouton **Enregistrer** . Un paramètre n’a pas une valeur persistante : le mot de passe du compte d’administrateur pour le cluster. Vous devez fournir une valeur de mot de passe lorsque Visual Studio vous en demande un.

>[AZURE.NOTE] Commençant avec Azure SDK 2.9, Visual Studio prend en charge la lecture des mots de passe à partir de la **Chambre forte de clé Azure** pendant le déploiement. Dans la boîte de dialogue de paramètres de modèle Remarquez que la `adminPassword` zone de texte paramètre a une petite icône « clé » à droite. Cette icône vous permet de sélectionner un secret de clé coffre-fort existant comme mot de passe d’administration pour le cluster. Assurez-vous simplement d’activer le premier accès d’Azure le Gestionnaire de ressources pour le déploiement du modèle dans les stratégies d’accès avancés de votre coffre-fort de clé. 

Vous pouvez surveiller la progression du processus de déploiement dans la fenêtre Sortie de Visual Studio. Une fois le modèle de déploiement est terminé, votre nouveau cluster est prêt à être utilisé !

>[AZURE.NOTE] Si PowerShell n’a jamais été utilisée pour administrer d’Azure à partir de l’ordinateur que vous utilisez, vous devez effectuer une maintenance un peu.
>1. Activer l’écriture de scripts PowerShell en exécutant le [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) commande. Pour les ordinateurs de développement, stratégie de « illimité » est généralement acceptable.
>2. Décider s’il faut autoriser la collecte de données de diagnostic à partir des commandes PowerShell d’Azure et exécutez [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) si nécessaire. Cela permet d’éviter les invites inutiles lors du déploiement du modèle.

Si des erreurs sont présentes, accédez au [portail Azure](https://portal.azure.com/) et ouvrez le groupe de ressources que vous avez déployés à. Cliquez sur **tous les paramètres**, puis cliquez sur les **déploiements** sur la lame de paramètres. Informations de diagnostic détaillées l’il laisse d’échec du déploiement des groupes de ressources.

>[AZURE.NOTE] Clusters de service Fabric nécessitent un certain nombre de nœuds à être opérationnel pour maintenir la disponibilité et la conservation de l’état - dénommé « maintenance de quorum ». Par conséquent, il n’est pas sûr d’arrêter tous les ordinateurs du cluster, sauf si vous avez effectué en premier lieu une [sauvegarde complète de votre état](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur la configuration de cluster à structure de Service via le portail Azure](service-fabric-cluster-creation-via-portal.md)
- [Découvrez comment gérer et déployer des applications de Service Fabric à l’aide de Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png
