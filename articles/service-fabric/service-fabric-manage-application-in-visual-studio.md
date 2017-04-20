<properties
   pageTitle="Gestion de vos applications dans Visual Studio | Microsoft Azure"
   description="Visual Studio permet de créer, de développer, de package, de déployer et de déboguer vos applications Fabric de Service et les services."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Utilisez Visual Studio pour simplifier l’écriture et la gestion de vos applications de Service Fabric

Vous pouvez gérer vos applications de Service de Azure Fabric et services par l’intermédiaire de Visual Studio. Une fois que vous avez [configuré votre environnement de développement](service-fabric-get-started.md), vous pouvez utiliser Visual Studio pour créer des applications de Service Fabric, ajouter des services, ou package, un Registre et déployer des applications dans votre cluster de développement local.

## <a name="deploy-your-service-fabric-application"></a>Déployer votre application Fabric du Service

Par défaut, le déploiement d’une application combine les étapes suivantes en une seule opération simple :

1. Création du package d’application
2. Télécharger le package d’application pour le magasin d’image
3. Enregistrement du type d’application
4. Suppression des instances de l’application en cours d’exécution
5. Création d’une nouvelle instance de l’application

Dans Visual Studio, appuyez sur **F5** également déployer votre application et attacher le débogueur à toutes les instances de l’application. Vous pouvez utiliser **Ctrl + F5** pour déployer une application sans déboguer, ou vous pouvez publier sur un cluster local ou à distance à l’aide du profil de publication. Pour plus d’informations, voir [publication d’une application sur un cluster distant à l’aide de Visual Studio](service-fabric-publish-app-remote-cluster.md).

### <a name="application-debug-mode"></a>Mode de débogage d’application

Par défaut, Visual Studio supprime les instances existantes de votre type d’application lorsque vous arrêtez le débogage ou (si vous avez déployé l’application sans attacher le débogueur), lorsque vous redéployez l’application. Dans ce cas, toutes les données de l’application sont supprimées. Lors du débogage localement, vous souhaiterez peut-être conserver les données que vous avez déjà créées lors du test d’une nouvelle version de l’application, vous souhaitez conserver l’application s’exécute ou vous souhaitez que les sessions de débogage suivantes pour mettre à niveau de l’application. Outils tissu de Service Visual Studio fournissent une propriété appelée **Mode de débogage d’Application**, les contrôles si le **F5** doit désinstaller l’application, conservent l’application en cours d’exécution après une session de débogage se termine, ou permettre à l’application de mise à niveau sur les sessions de débogage suivantes, plutôt que supprimer et redéployé.

#### <a name="to-set-the-application-debug-mode-property"></a>Pour définir la propriété Mode débogage d’Application

1. Dans le menu de raccourcis du projet de l’application, choisissez **Propriétés** (ou appuyez sur la touche **F4** ).
2. Dans la fenêtre **Propriétés** , définissez la propriété **Mode débogage d’Application** .

    ![Définissez la propriété Mode de débogage d’Application][debugmodeproperty]

Voici les options du **Mode de débogage d’Application** disponibles.

1. **Mise à niveau automatique**: l’application continue de s’exécuter lors de la session de débogage se termine. La prochaine **F5** traite du déploiement sous la forme d’une mise à niveau à l’aide du mode auto non surveillée mettre rapidement à niveau l’application vers une version plus récente avec une chaîne de date ajoutée. Le processus de mise à niveau conserve toutes les données que vous avez entré dans une précédente session de débogage.

2. **Conserver l’Application**: l’application continue à fonctionner dans le cluster à la fin de la session de débogage. L’application sera supprimée dans la prochaine **F5** et l’application nouvellement créée sera déployée sur le cluster.

3. **Supprimer une Application** , l’application à supprimer lors de la session de débogage se termine.

Pour la **Mise à niveau automatique** , les données sont conservées en appliquant les fonctionnalités de mise à niveau d’application de Service de Fabric, mais il est réglé pour optimiser les performances plutôt que de sécurité. Pour plus d’informations sur la mise à niveau des applications et la façon dont vous pouvez effectuer une mise à niveau dans un environnement réel, reportez-vous à la section [mise à niveau de l’application de Service de Fabric](service-fabric-application-upgrade.md).

![Exemple de nouvelle version de l’application avec la date ajoutée][preservedata]

>[AZURE.NOTE] Cette propriété n’existe pas de Visual Studio antérieures à la version 1.1 des outils TISSU du Service. Avant 1.1, utilisez la propriété de **Conserver les données sur Démarrer** pour obtenir le même comportement. L’option « Conserver l’Application » a été introduite dans la version 1.2 des outils TISSU Service pour Visual Studio.

## <a name="add-a-service-to-your-service-fabric-application"></a>Ajouter un service à votre application de Service Fabric

Vous pouvez ajouter de nouveaux services à votre application pour étendre ses fonctionnalités.  Pour vous assurer que le service est inclus dans le package d’application, ajoutez le service par le biais de l’élément de menu **Nouveau Service Fabric...** .

![Ajouter un nouveau service de fabric à votre application][newservice]

Sélectionnez un type de projet de Service Fabric à ajouter à votre application et spécifier un nom pour le service.  Consultez [choix d’un cadre de votre service](service-fabric-choose-framework.md) pour vous aider à décider quel type de service à utiliser.

![Sélectionnez un type de projet de Service de Fabric pour ajouter à votre application][addserviceproject]

Le nouveau service sera ajouté à votre solution et le package d’application existant. Les références de service et une instance de service par défaut seront ajoutés au manifeste d’application. Le service est créé et démarré la prochaine fois que vous déployez l’application.

![Le nouveau service sera ajouté à votre manifeste d’application][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Empaquetez votre application Fabric du Service

Pour déployer l’application et ses services à un cluster, vous devez créer un package d’application.  Le package organise le manifeste d’application, les manifest(s) de service et autres fichiers nécessaires dans une disposition spécifique.  Visual Studio configure et gère le package dans le dossier du projet de l’application, dans le répertoire « pkg ».  En cliquant sur le **Package** à partir du menu de contexte **d’Application** crée ou met à jour le package d’application.  Voulez-vous effectuer cette opération si vous déployez l’application à l’aide des scripts PowerShell personnalisés.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Supprimer les types d’applications à l’aide de Cloud Explorer et les applications

Vous pouvez effectuer des opérations de gestion de cluster de base à partir de dans Visual Studio à l’aide de Cloud Explorer, vous pouvez lancer à partir du menu **affichage** . Par exemple, vous pouvez supprimer des applications et annule la configuration des types d’applications sur les clusters locaux ou distants.

![Supprimer une application](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] Pour les fonctionnalités de gestion de cluster plus riche, consultez [visualiser votre cluster Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

- [Modèle d’application Fabric du service](service-fabric-application-model.md)
- [Déploiement d’applications Fabric de service](service-fabric-deploy-remove-applications.md)
- [Gestion des paramètres de l’application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md)
- [Débogage de votre application de Service Fabric](service-fabric-debugging-your-application.md)
- [Visualiser votre cluster à l’aide de l’Explorateur Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
