<properties
    pageTitle="Publier une application sur un cluster distant avec Visual Studio | Microsoft Azure"
    description="Découvrez comment publier une application à un cluster de tissu de service distant à l’aide de Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>Publier une application sur un cluster distant à l’aide de Visual Studio

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

L’extension de Azure Service Fabric pour Visual Studio fournit un moyen facile, reproductible et pouvant contenir des scripts pour publier une application à un cluster de Service Fabric.

## <a name="the-artifacts-required-for-publishing"></a>Les artefacts requis pour la publication

### <a name="deploy-fabricapplicationps1"></a>FabricApplication.ps1 de déploiement

Il s’agit d’un script PowerShell qui utilise un chemin d’accès du profil de publication sous la forme d’un paramètre de publication d’applications Fabric de Service. Dans la mesure où ce script fait partie de votre application, n’hésitez pas à le modifier selon les besoins de votre application.

### <a name="publish-profiles"></a>Publier les profils

Un dossier dans le projet d’application Fabric de Service appelé **PublishProfiles** contient des fichiers XML qui stockent des informations essentielles pour la publication d’une application, telle que :

- Paramètres de connexion de Fabric du service cluster
- Chemin d’accès à un fichier de paramètres d’application
- Paramètres de mise à niveau

Par défaut, votre application inclura deux profils de publication : Local.xml et Cloud.xml. Vous pouvez ajouter d’autres profils en copiant et collant un des fichiers par défaut.

### <a name="application-parameter-files"></a>Fichiers de paramètres d’application

Un dossier dans le projet d’application Fabric de Service appelé **ApplicationParameters** contient des fichiers XML pour les valeurs de paramètre manifeste d’application spécifiée par l’utilisateur. Fichiers manifeste d’application peuvent être paramétrées de sorte que vous pouvez utiliser des valeurs différentes pour les paramètres de déploiement. Pour en savoir plus sur le paramétrage de votre application, consultez [gérer plusieurs environnements fabric du Service](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE] Pour les services de l’acteur, vous devez générer le projet avant d’éditer le fichier dans un éditeur ou par le biais de la boîte de dialogue Publier. C’est parce que la partie des fichiers de manifeste est généré pendant la génération.

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>Pour publier une application à l’aide de la boîte de dialogue Publier une Application de tissu Service

Les étapes suivantes expliquent comment publier une application à l’aide de la boîte de dialogue **Publier une Application de tissu Service** fournie par les outils de tissu de Service de Visual Studio.

1. Dans le menu contextuel du projet d’Application de tissu de Service, cliquez sur **publier...** Pour afficher la boîte de dialogue **Publier une Application de tissu Service** .

    ![Le ** boîte de dialogue Publier Service Fabric Application **][0]

    Le fichier sélectionné dans la zone de liste déroulante **profil cible** est où tous les paramètres, à l’exception des **versions du manifeste**, sont enregistrés. Vous pouvez réutiliser un profil existant ou créez-en un nouveau en cliquant sur **< gérer les profils... >** dans la zone de liste déroulante **profil cible** . Lorsque vous choisissez un profil de publication, son contenu apparaît dans les champs correspondants de la boîte de dialogue. Pour enregistrer vos modifications à tout moment, cliquez sur le lien **Enregistrer le profil** .    

2. Dans la section de **point de terminaison de connexion** , spécifiez le point de terminaison local ou distant Service Fabric d’un cluster publication. Pour ajouter ou modifier le point de terminaison de connexion, cliquez sur la liste déroulante de **Point de terminaison de connexion** . La liste affiche des points finaux de connexion sur lequel vous pouvez publier en fonction de vos abonnements Azure le cluster Service Fabric disponible. Notez que si vous n’êtes pas déjà connecté à Visual Studio, vous êtes invité à le faire.

    Utilisez la boîte de dialogue de sélection de cluster pour choisir à partir de l’ensemble des abonnements disponibles et des clusters.

    ![Le ** boîte de dialogue Sélectionnez Service Fabric Cluster **][1]

    >[AZURE.NOTE] Si vous souhaitez publier sur un point de terminaison arbitraire (par exemple, un cluster de partie), reportez-vous à la section de **publication à un point de terminaison arbitraire de cluster** ci-dessous.

    Une fois que vous cliquez sur un point de terminaison, Visual Studio vérifie la connexion au cluster Service Fabric sélectionné. Si le cluster n’est pas sécurisé, Visual Studio y connecter immédiatement. Toutefois, si le cluster est sécurisé, vous devez installer un certificat sur votre ordinateur local avant de continuer. Pour plus d’informations, consultez [comment configurer des connexions sécurisées](service-fabric-visualstudio-configure-secure-connections.md) . Lorsque vous avez terminé, cliquez sur le bouton **OK** . Le cluster sélectionné s’affiche dans la boîte de dialogue **Publier une Application de tissu Service** .

3. Dans la zone de liste déroulante du **Fichier de paramètres d’Application** , accédez à un fichier de paramètres d’application. Un fichier de paramètres d’application conserve les valeurs spécifiées par l’utilisateur pour les paramètres dans le fichier de manifeste d’application. Pour ajouter ou modifier un paramètre, cliquez sur le bouton **Modifier** . Permet d’entrer ou de modifier la valeur du paramètre dans la grille de **paramètres** . Lorsque vous avez terminé, cliquez sur le bouton **Enregistrer** .

    ![Le ** boîte de dialogue Modifier les paramètres **][2]

4. Utilisez la case à cocher pour spécifier si cette publication d’action de **mise à niveau de l’Application** est une mise à niveau. Mise à niveau de publier les actions diffèrent normal publier les actions. Pour obtenir une liste des différences, consultez [Application de tissu de Service de mise à niveau](service-fabric-application-upgrade.md) . Pour configurer les paramètres de mise à niveau, cliquez sur le lien **Configurer les paramètres de mise à niveau** . L’éditeur de paramètres de mise à niveau s’affiche. Voir la rubrique [configurer la mise à niveau d’une application de Service de Fabric](service-fabric-visualstudio-configure-upgrade.md) pour en savoir plus sur les paramètres de mise à niveau.

5. Choisissez le **manifeste des Versions...** bouton pour afficher la boîte de dialogue **Modifier les Versions** . Vous devez mettre à jour des applications et des versions de service pour une mise à niveau aura lieu. Voir [application Fabric du Service de mise à niveau de didacticiel](service-fabric-application-upgrade-tutorial.md) pour apprendre comment service manifestes d’application et impact sur un processus de mise à niveau de versions.

    ![Le ** boîte de dialogue Modifier les Versions **][3]

    Si l’application et les versions de service utilisent sémantique version 1.0.0 ou les valeurs numériques au format 1.0.0.0, sélectionnez l’option **mettre à jour automatiquement des applications et des versions de service** . Lorsque vous choisissez cette option, le service et les numéros de version d’application sont automatiquement mis à jour chaque fois qu’un code, config ou données de version du package sont mise à jour. Si vous préférez modifier les versions manuellement, désactivez la case à cocher pour désactiver cette fonctionnalité.

    >[AZURE.NOTE] Pour toutes les entrées de package apparaissent pour un projet d’acteur, tout d’abord créer le projet pour générer les entrées dans les fichiers manifeste du Service.

6. Lorsque vous avez terminé spécifiant tous les paramètres nécessaires, cliquez sur le bouton **Publier** pour publier votre application sur le cluster Service Fabric sélectionné. Les paramètres que vous avez spécifiés sont appliqués pour le processus de publication.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publier sur un point de terminaison du cluster arbitraire (y compris les clusters partie)

L’expérience de la publication de Visual Studio est optimisé pour la publication sur des clusters à distance associé à un de vos abonnements Azure. Toutefois, il est possible de publier sur des points de terminaison arbitraires (par exemple, les clusters de partie Service Fabric) en modifiant directement le profil de publication XML. Comme indiqué ci-dessus, deux publier les profils sont fournis par défaut :**Local.xml** et **Cloud.xml**--mais n’hésitez pas à créer des profils supplémentaires pour les différents environnements. Par exemple, vous pouvez souhaiter créer un profil pour la publication sur des clusters de tiers, par exemple nommé **Party.xml**.

Si vous vous connectez à un cluster non sécurisé, tout ce qui est nécessaire est le point de terminaison de connexion cluster, tel que `partycluster1.eastus.cloudapp.azure.com:19000`. Dans ce cas, le point de terminaison de connexion dans le profil de publication ressemblerait à ceci :

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Si vous vous connectez à un cluster sécurisé, vous devez également fournir les détails du certificat client à partir du magasin local à utiliser pour l’authentification. Pour plus d’informations, consultez [Configuration des connexions sécurisées à un Fabric du Service de cluster](service-fabric-visualstudio-configure-secure-connections.md).

  Une fois votre profil de publication est défini, vous pouvez la référencer dans la boîte de dialogue Publier comme indiqué ci-dessous.

  ![Nouveau profil de la publication dans la boîte de dialogue Publier][4]

  Notez que dans ce cas, le nouveau profil de publication pointe vers un des fichiers de paramètre d’application par défaut. Ceci est approprié si vous souhaitez publier la configuration de l’application même pour un certain nombre d’environnements. En revanche, dans le cas où vous souhaitez avoir des configurations différentes pour chaque environnement dans lequel vous souhaitez publier, il serait judicieux de créer un fichier de paramètre d’application correspondant.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment automatiser le processus de publication dans un environnement d’intégration continue, consultez [configurer l’intégration continue de tissu de Service](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
