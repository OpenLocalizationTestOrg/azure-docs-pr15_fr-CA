<properties
   pageTitle="Mise à niveau de l’application de tissu de service à l’aide de PowerShell | Microsoft Azure"
   description="Cet article décrit l’expérience de déploiement d’une application de Service Fabric, de la modification du code et de déploiement d’une mise à niveau à l’aide de PowerShell."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="service-fabric-application-upgrade-using-powershell"></a>Mise à niveau de l’application Fabric du service à l’aide de PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
- [Visual Studio](service-fabric-application-upgrade-tutorial.md)

<br/>

Les plus fréquemment utilisés et l’approche de mise à niveau recommandée est la mise à niveau propagée surveillé.  Azure Fabric de Service surveille la santé de l’application en cours de mise à niveau basée sur un ensemble de stratégies d’intégrité. Une fois la mise à niveau d’un domaine de mise à jour (UD), Service Fabric évalue l’intégrité de l’application et se poursuit dans le domaine de mise à jour suivant ou Échec de la mise à niveau en fonction des stratégies de santé.

Une mise à niveau de l’application surveillée peut être effectuée à l’aide de managé ou natif API, PowerShell, les autres. Pour obtenir des instructions sur l’exécution d’une mise à niveau à l’aide de Visual Studio, consultez [mise à niveau de votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md).

Avec les mises à niveau propagées du Fabric du Service surveillé, l’administrateur d’application peut configurer la stratégie d’évaluation health Service Fabric utilise pour déterminer si l’application est en bonne santé. En outre, l’administrateur peut configurer l’action à entreprendre en cas d’échec de l’évaluation de la santé (par exemple, effectuer une restauration automatique.) Cette section décrit une mise à niveau surveillé pour un des exemples de SDK par PowerShell.

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Étape 1 : Créer et déployer l’exemple d’objets visuels


Générer et publier l’application en cliquant sur le projet d’application, **VisualObjectsApplication** et en sélectionnant la commande **Publier** .  Pour plus d’informations, consultez [application Fabric du Service de mise à niveau du didacticiel](service-fabric-application-upgrade-tutorial.md).  Sinon, vous pouvez utiliser PowerShell pour déployer votre application.

> [AZURE.NOTE] Avant que les commandes Service Fabric peuvent être utilisés dans PowerShell, vous devez d’abord connecter au cluster à l’aide de la `Connect-ServiceFabricCluster` applet de commande. De même, il est supposé que le Cluster a déjà été créée sur votre ordinateur local. Voir l’article sur la [configuration de votre environnement de développement de Service Fabric](service-fabric-get-started.md).

Après avoir créé le projet dans Visual Studio, vous pouvez utiliser la commande PowerShell **ServiceFabricApplicationPackage-copie** pour copier le package d’application pour le ImageStore. L’étape suivante consiste à enregistrer l’application à l’exécution du Service Fabric à l’aide de l’applet de commande **Register-ServiceFabricApplicationPackage** . La dernière étape consiste à démarrer une instance de l’application à l’aide de l’applet de commande **New-ServiceFabricApplication** .  Ces trois étapes sont similaires à l’aide de l’élément de menu de **déploiement** dans Visual Studio.

Maintenant, vous pouvez utiliser [Explorateur de tissu de Service pour afficher le cluster et l’application](service-fabric-visualizing-your-cluster.md). L’application possède un service web qui peut être accédé dans Internet Explorer en tapant [http://localhost : 8081/visualobjects](http://localhost:8081/visualobjects) dans la barre d’adresses.  Vous devez voir certains objets visuels flottantes, déplacer dans l’écran.  En outre, vous pouvez utiliser **Get-ServiceFabricApplication** pour vérifier l’état de l’application.

## <a name="step-2-update-the-visual-objects-sample"></a>Étape 2 : Mettre à jour les exemples d’objets visuels

Vous remarquerez peut-être qu’avec la version qui a été déployée à l’étape 1, les objets visuels ne pivotent pas. Nous allons mettre à niveau de cette application à un endroit où les objets visuels également faire pivoter.

Sélectionnez le projet VisualObjects.ActorService dans la solution de VisualObjects et ouvrez le fichier StatefulVisualObjectActor.cs. Dans ce fichier, accédez à la méthode `MoveObject`, commentez `this.State.Move()`et `this.State.Move(true)`. Cette modification fait pivoter les objets une fois que le service est mis à niveau.

Nous devons également mettre à jour le fichier *ServiceManifest.xml* (sous PackageRoot) du projet **VisualObjects.ActorService**. Mise à jour du *CodePackage* et la version du service 2.0 et les lignes correspondantes dans le fichier *ServiceManifest.xml* .
Vous pouvez utiliser l’option de Visual Studio, *Modifier les fichiers manifeste* une fois avec le bouton droit sur la solution pour apporter les modifications de fichier manifeste.


Une fois les modifications effectuées, le manifeste doit se présenter comme suit (les parties en surbrillance permet d’afficher les modifications) :

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Le fichier *ApplicationManifest.xml* (situé sous le projet **VisualObjects** dans la solution de **VisualObjects** ) est maintenant mis à jour vers la version 2.0 du projet **VisualObjects.ActorService** . En outre, la version de l’Application est mise à jour à 2.0.0.0 de 1.0.0.0. *ApplicationManifest.xml* doit ressembler à l’extrait de code suivant :

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


Maintenant, générez le projet en sélectionnant simplement le projet **ActorService** , puis avec le bouton droit et sélectionnez l’option **Générer** dans Visual Studio. Si vous sélectionnez **Régénérer tout**, vous devez mettre à jour les versions de tous les projets, dans la mesure où le code aurait été modifié. Ensuite, nous allons l’application mise à jour du package par clic droit sur ***VisualObjectsApplication***, en sélectionnant le Menu Service du Fabric et choix de **Package**. Cette action crée un package d’application qui peut être déployé.  Votre application mise à jour est prête à être déployée.


## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Étape 3 : Décider de stratégies d’intégrité et les paramètres de mise à niveau

Vous devez vous familiariser avec les [paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md) et [processus de mise à niveau](service-fabric-application-upgrade.md) pour obtenir une bonne compréhension des différents paramètres de mise à niveau, les délais d’attente et le critère de santé appliqué. Pour cette procédure pas à pas, le critère d’évaluation de santé service est la valeur par défaut (et recommandé) valeurs, ce qui signifie que tous les services et les instances doivent être _en bon état_ après la mise à niveau.  

Toutefois, nous allons augmenter l' *HealthCheckStableDuration* à 60 secondes (de sorte que les services sont en bon état pendant au moins 20 secondes avant la mise à niveau se poursuit dans le domaine de mise à jour suivant).  Nous allons également définir *UpgradeDomainTimeout* à 1 200 secondes et *UpgradeTimeout* 3000 secondes.

Également définissons enfin *UpgradeFailureAction* pour la restauration. Cette option nécessite un Fabric Service restaurer l’application à la version précédente si elle rencontre des problèmes lors de la mise à niveau. Par conséquent, lors du démarrage de la mise à niveau (à l’étape 4), les paramètres suivants sont spécifiés :

FailureAction = restaurer

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## <a name="step-4-prepare-application-for-upgrade"></a>Étape 4 : Préparation de l’application pour la mise à niveau

Maintenant, l’application est généré et prêt à être mis à niveau. Si vous ouvrez une fenêtre PowerShell en tant qu’administrateur et tapez **Get-ServiceFabricApplication**, il doit vous informer qu’il est le type d’application 1.0.0.0 de **VisualObjects** qui a été déployé.  

Le package d’application est stocké sous le chemin d’accès relatif suivant où vous avez décompressé le SDK de Fabric Service - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Vous devez trouver un dossier de « Package » dans ce répertoire, où se trouve le package d’application. Vérifiez les horodatages pour vous assurer qu’il s’agit de la dernière version (vous devrez peut-être également modifier les chemins d’accès de manière appropriée).

Maintenant nous allons copier le package d’application mis à jour à la ImageStore de tissu Service (où les packages d’application sont stockées par le Service Fabric). Le paramètre *ApplicationPackagePathInImageStore* indique le Fabric du Service où il peut trouver le package d’application. Nous avons mis l’application mis à jour en « VisualObjects\_V2 » avec la commande suivante (il se pouvez que vous deviez modifier à nouveau correctement les chemins d’accès).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

L’étape suivante consiste à enregistrer cette application avec tissu de Service, qui peut être effectuée à l’aide de la commande suivante :

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Si la commande précédente n’aboutit pas, il est probable que vous avez besoin d’une reconstruction de tous les services. Comme indiqué à l’étape 2, il se peut que vous deviez mettre à jour votre version de WebService ainsi.

## <a name="step-5-start-the-application-upgrade"></a>Étape 5 : Commencer la mise à niveau de l’application

Maintenant, nous allons toutes les définir pour démarrer la mise à niveau de l’application à l’aide de la commande suivante :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Le nom de l’application est le même, comme cela a été décrit dans le fichier *ApplicationManifest.xml* . Service de Fabric utilise ce nom pour identifier l’application qui est mise en route de mise à niveau. Si vous définissez les délais d’attente trop courte, vous pouvez rencontrer un message d’erreur indiquant le problème. Reportez-vous à la section Dépannage, ou augmenter les délais d’attente.

Maintenant, comme le produit de mise à niveau de l’application, vous pouvez surveiller à l’aide de l’Explorateur Service Fabric ou de commande à l’aide du PowerShell suivant : **fabric de Get-ServiceFabricApplicationUpgrade : / VisualObjects**.

Dans quelques minutes, l’état que vous avez obtenu à l’aide de la commande PowerShell précédente, doit indiquer que tous les domaines de la mise à jour ont été mis à niveau (terminé). Et vous devez trouver que les objets visuels dans une fenêtre de votre navigateur ont commencé à rotation !

Vous pouvez essayer de mettre à niveau à partir de la version 2 à la version 3, ou à partir de la version 2 à la version 1 comme un exercice. Déplacement à partir de la version 2 à la version 1 est également considérée comme une mise à niveau. Jouez avec les délais d’attente et le faire vous-même familiarisé avec les stratégies d’intégrité. Lorsque vous déployez un cluster Azure, les paramètres doivent être définis correctement. Il est recommandé de définir les délais d’attente plus restrictives.


## <a name="next-steps"></a>Étapes suivantes

[Mise à niveau de votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

Contrôler comment votre application de mises à niveau à l’aide des [paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Assurer la compatibilité de vos mises à niveau de l’application en apprenant comment utiliser la [sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en vous reportant aux [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résoudre les problèmes courants dans les mises à niveau de l’application en vous reportant aux étapes de [dépannage à niveau d’applications](service-fabric-application-upgrade-troubleshooting.md).
