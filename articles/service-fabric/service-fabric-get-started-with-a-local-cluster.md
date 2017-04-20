<properties
   pageTitle="Mise en route de déploiement et de mise à niveau des applications sur votre cluster local | Microsoft Azure"
   description="Configurer un cluster Service Fabric local, déployer une application existante et puis mise à niveau de cette application."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Mise en route de déploiement et de mise à niveau des applications sur votre cluster local
Le Service de Fabric Azure SDK inclut un environnement de développement local complet que vous pouvez utiliser pour démarrer rapidement avec le déploiement et la gestion d’applications sur un cluster local. Dans cet article, vous créez un cluster local déployez une application existante et puis mise à niveau de cette application vers une nouvelle version, à partir de Windows PowerShell.

> [AZURE.NOTE] Cet article suppose que vous déjà [configuré votre environnement de développement](service-fabric-get-started.md).

## <a name="create-a-local-cluster"></a>Créer un cluster local
Un cluster à structure de Service représente un ensemble de ressources matérielles que vous pouvez déployer des applications. En règle générale, un cluster est constitué de n’importe quel endroit de cinq à plusieurs milliers d’ordinateurs. Toutefois, le Kit de développement de tissu de Service inclut une configuration de cluster qui peut s’exécuter sur un seul ordinateur.

Il est important de comprendre que le cluster local Service Fabric n’est pas un émulateur ou un simulateur. Il exécute le même code de plate-forme se trouvant sur les clusters comportant plusieurs ordinateurs. La seule différence est qu’il s’exécute les processus de plate-forme qui sont normalement répartis sur cinq ordinateurs sur une seule machine.

Le SDK propose deux façons de configurer un cluster local : un script Windows PowerShell et l’application de barre d’état système Gestionnaire de Cluster Local. Dans ce didacticiel, nous utilisons le script PowerShell.

> [AZURE.NOTE] Si vous avez déjà créé un cluster local par le déploiement d’une application à partir de Visual Studio, vous pouvez ignorer cette section.


1. Ouvrir une nouvelle fenêtre de PowerShell en tant qu’administrateur.

2. Exécutez le script de configuration du cluster à partir du dossier du Kit de développement logiciel :

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    Le programme d’installation de cluster prend un certain temps. Une fois l’installation terminée, vous devriez voir une sortie similaire à :

    ![Sortie du programme d’installation de cluster][cluster-setup-success]

    Vous êtes maintenant prêt à essayer le déploiement d’une application à votre cluster.

## <a name="deploy-an-application"></a>Déploiement d’une application
Le Kit de développement de tissu de Service comprend un large éventail de développeurs des outils de création d’applications et les infrastructures. Si vous souhaitez savoir comment créer des applications dans Visual Studio, consultez [créer votre première application Fabric du Service dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

Dans ce didacticiel, nous utiliserons un exemple d’application existant (appelé WordCount) afin que nous puissions nous concentrer sur les aspects de la gestion de la plate-forme, y compris le déploiement, la surveillance et la mise à niveau.


1. Ouvrir une nouvelle fenêtre de PowerShell en tant qu’administrateur.

2. Importer le module PowerShell de SDK Service Fabric.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Créez un répertoire pour stocker l’application que vous téléchargez et déployez, par exemple C:\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Télécharger l’application WordCount](http://aka.ms/servicefabric-wordcountapp) à l’emplacement que vous avez créé.  Remarque : le navigateur de Microsoft Edge enregistre le fichier avec une extension *.zip* .  Modifier l’extension de fichier à *.sfpkg*.

5. Connectez-vous au cluster local :

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Créez une nouvelle application à l’aide de la commande de déploiement du Kit de développement avec un nom et un chemin d’accès pour le package d’application.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Si tout se passe bien, vous devriez voir la sortie comme suit :

    ![Déployer une application sur le cluster local][deploy-app-to-local-cluster]

7. Pour voir l’application en action, lancez le navigateur et accédez à [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Vous devez voir :

    ![Interface utilisateur d’application déployée][deployed-app-ui]

    L’application WordCount est très simple. Il inclut le code de JavaScript côté client pour générer aléatoires cinq caractères « mots », qui sont alors relayés à l’application via l’API de Web ASP.NET. Un service avec état effectue le suivi du nombre de mots pris en compte. Ils sont partitionnés selon le premier caractère du mot. Vous pouvez trouver le code source pour l’application WordCount dans les [exemples de mise en route](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).

    L’application que nous avons déployé contient quatre partitions. Pour les mots commençant par les lettres A à G sont stockées dans la première partition, mots commençant par H à N sont stockés dans la deuxième partition et ainsi de suite.

## <a name="view-application-details-and-status"></a>Afficher les détails et l’état
Maintenant que nous avons déployé l’application, examinons certains détails app dans PowerShell.

1. La requête déployées toutes les applications sur le cluster :

    ```powershell
    Get-ServiceFabricApplication
    ```

    En supposant que vous avez déployé uniquement de l’application WordCount, vous voyez quelque chose de similaire à :

    ![Interroger les applications tout déployées dans PowerShell][ps-getsfapp]

2. Atteindre le niveau suivant en interrogeant l’ensemble de services qui sont inclus dans l’application WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Liste des services d’application dans PowerShell][ps-getsfsvc]

    L’application est composée de deux services--le serveur web frontal et le service d’état qui gère les mots.

3. Enfin, examinez la liste des partitions pour WordCountService :

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Afficher les partitions service dans PowerShell][ps-getsfpartitions]

    Le jeu de commandes que vous avez utilisé, ainsi que toutes les commandes de Service Fabric PowerShell, sont disponibles pour n’importe quel cluster que vous pouvez vous connecter, local ou distant.

    Pour obtenir un moyen plus visuel interagir avec le cluster, vous pouvez utiliser l’outil Explorateur de tissu de Service basée sur le web, accédez à [Http://localhost:19080/Explorateur](http://localhost:19080/Explorer) dans le navigateur.

    ![Afficher les détails d’application dans l’Explorateur de tissu de Service][sfx-service-overview]

    > [AZURE.NOTE] Pour en savoir plus sur le Service Fabric Explorer, reportez-vous à la section [visualisation de votre cluster avec Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## <a name="upgrade-an-application"></a>Mise à niveau d’une application
Service de Fabric fournit les mises à niveau sans interruption de service non en surveillant la santé de l’application qu’il déploie dans le cluster. Nous allons effectuer une simple mise à niveau de l’application WordCount.

La nouvelle version de l’application maintenant compte uniquement les mots qui commencent par une voyelle. Comme déploie la mise à niveau, nous voyons deux modifications de comportement de l’application. Tout d’abord, la fréquence à laquelle le nombre augmente doit ralentir, étant donné que moins de mots sont comptés. Ensuite, dans la mesure où la première partition a deux voyelles (A et E), et toutes les autres partitions ne contiennent qu’une seule chaque, son décompte doit finalement commencer à beaucoup plus rapide que les autres.

1. [Télécharger le package de v2 WordCount](http://aka.ms/servicefabric-wordcountappv2) vers le même emplacement que celui où vous avez téléchargé le package v1.

2. Revenir à la fenêtre PowerShell et commande de mise à niveau du Kit de développement permet d’enregistrer la nouvelle version du cluster. Commencer la mise à niveau de fabric : / application de WordCount.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Vous devez voir la sortie dans PowerShell qui ressemble à ce qui suit la mise à niveau commence.

    ![Mise à niveau de progression dans PowerShell][ps-appupgradeprogress]

3. Lors de la mise à niveau est en cours, il peut s’avérer plus facile de surveiller son état à partir de l’Explorateur de tissu de Service. Lancez une fenêtre de navigateur et naviguez jusqu'à [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Développez des **Applications** dans l’arborescence sur la gauche, puis choisissez **WordCount**et enfin **fabric : / WordCount**. Dans l’onglet essentials, vous verrez l’état de la mise à niveau mesure qu’elles traversent les domaines de mise à niveau du cluster.

    ![Progression dans l’Explorateur de tissu de Service de mise à niveau][sfx-upgradeprogress]

    Que la mise à niveau s’effectue par le biais de chaque domaine, les vérifications de la santé sont effectuées pour s’assurer que l’application se comporte correctement.

4. Si vous réexécutez la requête précédente pour l’ensemble des services du fabric : / application WordCount, notez que la version de WordCountService est modifiée mais que la version de WordCountWebService n’a pas :

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Services d’application de requête après mise à niveau][ps-getsfsvc-postupgrade]

    Cela met en surbrillance le Service Fabric gère les mises à niveau de l’application. Il concerne uniquement le jeu de services (ou les packages/configuration de code au sein de ces services) qui ont été modifiés, qui rend le processus de mise à niveau plus rapide et plus fiable.

5. Enfin, retourner au navigateur pour observer le comportement de la nouvelle version de l’application. Comme prévu, le nombre progresse plus lentement, et la première partition finit avec légèrement plus du volume.

    ![Permet d’afficher la nouvelle version de l’application dans le navigateur][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Nettoyage des

Avant de conclure, il est important de se rappeler que le cluster local est bien réel. Les applications continuent à s’exécuter en arrière-plan jusqu'à ce que vous les supprimiez.  Selon la nature de vos applications, une application en cours d’exécution peut prendre énormément de ressources sur votre ordinateur. Vous disposez de plusieurs options pour gérer les applications et le cluster :

1. Pour supprimer une application individuelle et toutes ses données, exécutez la commande suivante :

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    Ou bien, supprimez l’application depuis le menu **ACTIONS** du Service Fabric Explorer ou le menu contextuel de l’affichage de la liste application du volet gauche.

    ![Supprimer une application est le Service Fabric Explorer][sfe-delete-application]

2. Après la suppression de l’application du cluster, vous pouvez annuler l’enregistrement de versions 1.0.0 et 2.0.0 WordCount application de type. Suppression supprime les packages d’application, y compris le code et la configuration, à partir du magasin d’image du cluster.

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    Ou bien, dans l’Explorateur de tissu de Service, choisissez **Le Type de la mise hors service** de l’application.

3. Pour arrêter le cluster mais conserver les données d’application et les traces, cliquez sur **Arrêter le Cluster Local** dans l’application de barre d’état système.

4. Pour supprimer la totalité du cluster, cliquez sur **Supprimer le Cluster Local** dans l’application de barre d’état système. Cette option entraîne un autre déploiement lent la prochaine fois que vous appuyez sur F5 dans Visual Studio. Supprimer le cluster local uniquement si vous ne souhaitez pas l’utiliser pendant un certain temps, ou si vous avez besoin récupérer des ressources.

## <a name="1-node-and-5-node-cluster-mode"></a>1 nœud et 5 en mode cluster de nœud

Lorsque vous travaillez avec le cluster local pour développer des applications, vous souvent amené à effectuer des itérations rapides d’écriture de code, de débogage, la modification de code, etc. de débogage. Pour optimiser ce processus, le cluster local peut s’exécuter dans deux modes : 1 nœud ou 5. Les deux modes de cluster ont leurs avantages.
Le mode cluster nœud 5 vous permet d’utiliser avec un cluster réel. Vous pouvez tester les scénarios de basculement, travailler avec plusieurs instances et les répliques de vos services.
1 mode de cluster du nœud est optimisé pour effectuer un déploiement rapide et l’enregistrement des services, pour vous aider à valider rapidement le code à l’aide de l’exécution du Service Fabric.

Mode de cluster du 1 nœud et 5 en mode cluster de nœud n’est pas un émulateur ou un simulateur. Il exécute le même code de plate-forme se trouvant sur les clusters comportant plusieurs ordinateurs.

> [AZURE.NOTE] Cette fonctionnalité est disponible dans le Kit de développement SDK version 5.2 et supérieures.

Pour modifier le mode de cluster pour un cluster à nœud 1, utilisez le Gestionnaire de Cluster Service Fabric Local ou PowerShell comme suit :

1. Ouvrir une nouvelle fenêtre de PowerShell en tant qu’administrateur.

2. Exécutez le script de configuration du cluster à partir du dossier du Kit de développement logiciel :

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```

    Le programme d’installation de cluster prend un certain temps. Une fois l’installation terminée, vous devriez voir une sortie similaire à :
    
    ![Sortie du programme d’installation de cluster][cluster-setup-success-1-node]

Si vous utilisez le Gestionnaire de Cluster Service Fabric Local :

![Mode cluster][switch-cluster-mode]

> [AZURE.WARNING] Lorsque vous modifiez le mode de cluster, le cluster actuel est supprimé de votre système et un nouveau cluster est en cours de création. Les données doivent enregistrées dans le cluster, seront supprimées lorsque vous modifiez le mode de cluster.

## <a name="next-steps"></a>Étapes suivantes
- Maintenant que vous avez déployés et mis à niveau de certaines applications intégrées, vous pouvez [essayer de créer votre propre dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Les actions exécutées sur le cluster local dans cet article peuvent être effectuées sur un [cluster Azure](service-fabric-cluster-creation-via-portal.md) .
- La mise à niveau que nous avons effectué dans cet article a été base. Consultez [documentation de mise à niveau](service-fabric-application-upgrade.md) pour en savoir plus sur la puissance et la flexibilité des mises à niveau de Fabric du Service.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
