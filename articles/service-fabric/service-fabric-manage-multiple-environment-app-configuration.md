<properties
   pageTitle="Gérer plusieurs environnements fabric du Service | Microsoft Azure"
   description="Applications de service Fabric peuvent être exécutées sur des clusters dont la taille d’une machine à des milliers d’ordinateurs. Dans certains cas, vous devez configurer votre application différemment en fonction de ces différents environnements. Cet article explique comment définir des paramètres d’application par l’environnement."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/19/2016"
   ms.author="seanmck"/>

# <a name="manage-application-parameters-for-multiple-environments"></a>Gérer les paramètres de l’application pour plusieurs environnements

Vous pouvez créer des clusters d’Azure Fabric de Service à l’aide de n’importe quel endroit de l’un à plusieurs milliers d’ordinateurs. Alors que les fichiers binaires d’application peuvent s’exécuter sans modification sur ce large éventail d’environnements, vous souhaiterez souvent configurer différemment, de l’application en fonction du nombre de machines que vous déployez sur.

Un exemple simple, il est conseillé de `InstanceCount` d’un service sans état. Lorsque vous exécutez des applications dans Azure, vous souhaitez généralement définir ce paramètre avec la valeur particulière «-1 ». Cela garantit que votre service s’exécute sur chaque nœud du cluster. Toutefois, cette configuration n’est pas appropriée pour un seul ordinateur cluster dans la mesure où vous ne pouvez pas avoir plusieurs processus à l’écoute sur le même point de terminaison sur un seul ordinateur. Au lieu de cela, vous allez généralement définir `InstanceCount` « 1 ».

## <a name="specifying-environment-specific-parameters"></a>Spécification des paramètres spécifiques à l’environnement

La solution à ce problème de configuration est un ensemble de services par défaut paramétrée et les fichiers de paramètres d’application qui remplissent ces valeurs de paramètre pour un environnement donné. Services par défaut et les paramètres de l’application sont configurés dans les manifestes d’application et des services. La définition de schéma pour les fichiers ServiceManifest.xml et ApplicationManifest.xml est installée avec le Kit de développement logiciel de Service de Fabric et des outils à *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Services par défaut

Applications de service Fabric sont constituées d’une collection d’instances de service. S’il est possible de créer une application vide et puis créer dynamiquement toutes les instances de service, la plupart des applications possèdent un ensemble de services essentiels qui doivent toujours être créés lorsque l’application est instanciée. Ceux-ci sont appelés « services par défaut ». Ils sont spécifiés dans le manifeste d’application, avec des espaces réservés pour la configuration de per-environnement inclus entre crochets :

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Chacun des paramètres nommés doit être défini dans l’élément Paramètres du manifeste d’application :

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

L’attribut DefaultValue spécifie la valeur à utiliser en l’absence d’un paramètre plus spécifiques pour un environnement donné.

>[AZURE.NOTE] Pas tous les paramètres d’instance de service ne conviennent pas pour la configuration de per-environnement. Dans l’exemple ci-dessus, les valeurs LowKey et HighKey pour le schéma de partitionnement du service sont explicitement définis pour toutes les instances du service dans la mesure où la plage de partition est une fonction du domaine de données, pas l’environnement.


### <a name="per-environment-service-configuration-settings"></a>Paramètres de configuration de service par l’environnement

Le [modèle d’application de Service de Fabric](service-fabric-application-model.md) permet aux services d’inclure les colis de configuration qui contient des paires clé-valeur personnalisées qui peuvent être lus au moment de l’exécution. Les valeurs de ces paramètres peuvent également être différenciés par d’environnement en spécifiant un `ConfigOverride` dans le manifeste d’application.

Supposons que vous ayez le paramètre suivant dans le fichier Config\Settings.xml pour le `Stateful1` service :


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Pour substituer cette valeur pour une paire d’environnement / d’application spécifique, créez un `ConfigOverride` lorsque vous importez le manifeste de service dans le manifeste d’application.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Ce paramètre peut ensuite être configuré par l’environnement comme indiqué ci-dessus. Vous pouvez le faire en déclarant dans la section Paramètres du manifeste d’application et en spécifiant des valeurs spécifiques à l’environnement dans les fichiers de paramètres d’application.

>[AZURE.NOTE] Dans le cas des paramètres de configuration de service, il existe trois emplacements où la valeur d’une clé peut être définie : le package de configuration du service, le manifeste d’application et le fichier de paramètres d’application. Service de Fabric choisira toujours à partir du fichier de paramètre d’application premier (si spécifié), puis le manifeste d’application et enfin le colis configuration.


### <a name="application-parameter-files"></a>Fichiers de paramètres d’application

Le projet d’application Fabric de Service peut inclure un ou plusieurs fichiers de paramètre d’application. Chacun d’eux définit les valeurs spécifiques pour les paramètres qui sont définis dans le manifeste d’application :

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Par défaut, une nouvelle application comprend deux fichiers de paramètre d’application, nommés Local.xml et Cloud.xml :

![Fichiers de paramètres d’application dans l’Explorateur de solutions][app-parameters-solution-explorer]

Pour créer un nouveau fichier de paramètre, simplement copier et coller un existant et lui donner un nouveau nom.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identification des paramètres spécifiques à l’environnement au cours du déploiement

Au moment du déploiement, vous devez choisir le fichier de paramètre approprié à appliquer à votre application. Vous pouvez procéder de la sorte par le biais de la boîte de dialogue Publier dans Visual Studio ou via PowerShell.

### <a name="deploy-from-visual-studio"></a>Déployer à partir de Visual Studio

Lorsque vous publiez votre application dans Visual Studio, vous pouvez choisir dans la liste des fichiers de paramètres disponibles.

![Choisissez un fichier de paramètres dans la boîte de dialogue Publier][publishdialog]

### <a name="deploy-from-powershell"></a>Déployer à partir de PowerShell

Le `Deploy-FabricApplication.ps1` inclus dans le modèle de projet application de script PowerShell accepte un profil de publication en tant que paramètre et le PublishProfile contient une référence au fichier de paramètres de l’application.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur certains des principaux concepts décrits dans cette rubrique, consultez la [présentation technique du Service de Fabric](service-fabric-technical-overview.md). Pour plus d’informations sur les autres fonctionnalités de gestion d’application qui sont disponibles dans Visual Studio, reportez-vous à la section [gestion de vos applications de Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
