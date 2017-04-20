<properties
   pageTitle="Configuration des Diagnostics pour les Services en nuage Azure et les Machines virtuelles | Microsoft Azure"
   description="Décrit comment configurer les informations de diagnostic pour le débogage des services d’Azure cloude et les machines virtuelles (VM) dans Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Configuration des Diagnostics pour les Services en nuage Azure et les Machines virtuelles

Lorsque vous avez besoin résoudre les problèmes d’un service cloud Azure Azure ordinateur ou virtuel, vous pouvez configurer plus facilement les diagnostics de Windows Azure à l’aide de Visual Studio. Diagnostics de Windows Azure capture les données système et enregistrement des données sur les machines virtuelles et les instances de machine virtuelle qui s’exécutent à votre service cloud et transfère ces données dans un compte de stockage de votre choix. Pour plus d’informations sur les diagnostics logging dans Azure, consultez [Activer l’enregistrement des diagnostics pour les applications web dans le Service d’application Azure](./app-service-web/web-sites-enable-diagnostic-log.md) .

Cette rubrique vous indique comment faire pour activer et configurer les diagnostics de Windows Azure dans Visual Studio, à la fois avant et après le déploiement, ainsi que dans des machines virtuelles Azure. Il vous indique également comment sélectionner les types d’informations de diagnostic pour collecter et afficher les informations après sa collecte.

Vous pouvez configurer des Diagnostics d’Azure dans l’une des manières suivantes :

- Vous pouvez modifier les paramètres de configuration de diagnostics par le biais de la boîte de dialogue **Configuration de Diagnostics** dans Visual Studio. Les paramètres sont enregistrés dans un fichier appelé diagnostics.wadcfgx (diagnostics.wadcfg dans Azure SDK 2.4 ou version antérieure). Vous pouvez également modifier directement le fichier de configuration. Si vous modifiez manuellement le fichier, les modifications de configuration prendront effet la prochaine fois que vous déployez le cloud service vers Azure ou exécuter le service dans l’émulateur.

- Utilisez le **Cloud Explorer** ou **l’Explorateur de serveurs** dans Visual Studio pour modifier les paramètres de diagnostic pour un service en nuage en cours d’exécution ou de la machine virtuelle.

## <a name="azure-26-diagnostics-changes"></a>Modifications de diagnostics Azure 2.6

Pour les projets d’Azure SDK 2.6 dans Visual Studio, les modifications suivantes ont été apportées. (Ces modifications s’appliquent également aux versions ultérieures du Kit de développement logiciel Azure.)

- L’émulateur local prend désormais en charge les tests de diagnostic. Cela signifie que vous pouvez collecter des données de diagnostics et assurez-vous que votre application crée les traces de droite alors que vous êtes de développement et de test dans Visual Studio. La chaîne de connexion `UseDevelopmentStorage=true` permet la collecte de données de diagnostic lorsque vous exécutez votre projet de service cloud dans Visual Studio à l’aide de l’émulateur de stockage Azure. Toutes les données de diagnostic sont collectées dans le compte de stockage (stockage de développement).

- La chaîne de connexion de compte stockage diagnostics (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) est stockée une fois de plus dans le fichier de configuration (.cscfg) du service. Le compte de stockage diagnostics Azure SDK 2.5 a été spécifié dans le fichier diagnostics.wadcfgx.

Il existe des différences notables entre la façon dont la chaîne de connexion a travaillé dans Azure SDK 2.4 et les versions antérieures et de son fonctionnement dans Azure SDK 2.6 et versions ultérieures.

- Dans Azure SDK 2.4 et versions antérieures, la chaîne de connexion a été utilisée comme un runtime par le plug-in de diagnostics pour obtenir les informations de compte de stockage pour le transfert des journaux de diagnostics.

- Dans Azure SDK 2.6 et les versions ultérieures, la chaîne de connexion de diagnostic est utilisée par Visual Studio pour configurer l’extension de diagnostics avec les informations de compte de stockage approprié lors de la publication. La chaîne de connexion vous permet de définir des comptes de stockage différents pour les configurations de services différentes que Visual Studio utilisera lors de la publication. Toutefois, étant donné que le plug-in de diagnostics n’est plus disponible (après Azure SDK 2.5), le fichier .cscfg par lui-même ne peut pas activer l’Extension des tests de diagnostic. Vous devez activer l’extension séparément par le biais d’outils tels que Visual Studio ou PowerShell.

- Pour simplifier le processus de configuration de l’extension de diagnostics avec PowerShell, la sortie du package à partir de Visual Studio contient également le fichier XML de configuration public pour l’extension des tests de diagnostic pour chaque rôle. Visual Studio utilise la chaîne de connexion de diagnostic pour remplir les informations de compte de stockage présentes dans la configuration du publique. Les fichiers de configuration public sont créés dans le dossier Extensions et suivent le modèle PaaSDiagnostics. &lt;RoleName >. PubConfig.xml. Les déploiements de PowerShell basé peuvent utiliser ce modèle pour mapper chaque configuration à un rôle.

- La chaîne de connexion dans le fichier .cscfg est également utilisée par le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) à accéder aux données de diagnostic, afin qu’il peut apparaître dans l’onglet **analyse** . La chaîne de connexion est nécessaire pour configurer le service pour afficher les données d’analyse détaillées dans le portail.

## <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migration de projets vers Azure SDK 2.6 et versions ultérieures

Lors de la migration à partir d’Azure SDK 2.5 vers Azure SDK 2.6 ou version ultérieure, si vous avez un compte de stockage diagnostics spécifié dans le fichier .wadcfgx, puis il restera il. Pour tirer parti de la souplesse d’utilisation des comptes de stockage différents pour les configurations de stockage différents, vous devrez ajouter manuellement la chaîne de connexion à votre projet. Si vous migrez un projet à partir d’Azure SDK 2.4 ou version antérieure vers Azure SDK 2.6, les chaînes de connexion de diagnostic sont conservés. Toutefois, notez les modifications apportées à la façon dont les chaînes de connexion sont traités dans Azure SDK 2.6 comme spécifié dans la section précédente.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Comment Visual Studio détermine le compte de stockage de diagnostics

- Si une chaîne de connexion de diagnostic est spécifiée dans le fichier .cscfg, Visual Studio utilise pour configurer l’extension de diagnostic lors de la publication et lors de la génération des fichiers xml de configuration public lors de l’emballage.

- Si aucune chaîne de connexion de diagnostic n’est spécifié dans le fichier .cscfg, puis Visual Studio revient à l’aide du compte de stockage spécifié dans le fichier .wadcfgx pour configurer l’extension de diagnostic lors de la publication et de générer les fichiers xml de configuration public lors de la compression.

- La chaîne de connexion de diagnostics dans le fichier .cscfg est prioritaire sur le compte de stockage dans le fichier .wadcfgx. Si une chaîne de connexion de diagnostic est spécifiée dans le fichier .cscfg, puis Visual Studio qui utilise et ignore le compte de stockage dans .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Signification des « mise à jour développement stockage chaînes de connexion... » case à cocher faire ?

La case à cocher pour les **chaînes de connexion de stockage de développement de mise à jour pour les Diagnostics et la mise en cache des informations d’identification de compte de stockage Microsoft Azure lors de la publication de Microsoft Azure** vous offre un moyen pratique pour mettre à jour les chaînes de connexion de compte de stockage développement avec le compte de stockage Azure spécifié lors de la publication.

Par exemple, supposons que vous activez cette case à cocher et la chaîne de connexion de diagnostic indique `UseDevelopmentStorage=true`. Lorsque vous publiez le projet vers Azure, Visual Studio met automatiquement à jour la chaîne de connexion de diagnostic avec le compte de stockage que vous avez spécifié dans l’Assistant Publication. Toutefois, si un compte de stockage réel a été spécifié comme la chaîne de connexion de diagnostic, ce compte est utilisé à la place.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Différences de fonctionnalités de diagnostics entre Azure SDK 2.4 et antérieur et Azure SDK, 2.5 et ultérieur

Si vous mettez à niveau votre projet à partir d’Azure SDK 2.4 Azure SDK 2.5 ou une version ultérieure, vous devez garder à l’esprit les différences de fonctionnalités de diagnostic suivantes.

- **API de configuration sont déconseillées** – configuration par programme des tests de diagnostic n’est disponible dans Azure SDK 2,4 ou des versions antérieures, mais est déconseillée dans Azure SDK 2.5 et versions ultérieures. Si votre configuration de diagnostics n’est actuellement définie dans le code, vous devrez reconfigurer ces paramètres à partir de zéro dans le projet migré dans l’ordre pour les tests de diagnostic pour continuer à travailler. Le fichier de configuration de diagnostics pour Azure SDK 2.4 est diagnostics.wadcfg et diagnostics.wadcfgx pour Azure SDK 2.5 et versions ultérieures.

- **Diagnostics pour les applications de service cloud peut uniquement être configuré au niveau du rôle et pas au niveau de l’instance.**

- **Chaque fois que vous déployez votre application, la configuration des diagnostics est mis à jour** , cela peut entraîner des problèmes de parité si vous modifiez votre configuration de diagnostics à partir de l’Explorateur de serveurs et redéployez votre application.

- **Dans Azure SDK 2.5 et les dumps d’une version ultérieures, subissent des défaillances sont configurés dans le fichier de configuration de diagnostics, pas dans le code** – si vous avez des vidages sur incident configurés dans le code, vous devrez transférer manuellement de la configuration à partir du code dans le fichier de configuration car les vidages sur incident ne sont pas transférées au cours de la migration vers Azure SDK 2.6.

## <a name="enable-diagnostics-in-cloud-service-projects-before-deploying-them"></a>Activer les diagnostics de projets de service cloud avant de les déployer

Dans Visual Studio, vous pouvez choisir de collecter des données de diagnostic pour les rôles qui s’exécutent dans Azure, lorsque vous exécutez le service dans l’émulateur avant de le déployer. Toutes les modifications apportées aux paramètres de diagnostics dans Visual Studio sont enregistrées dans le fichier de configuration diagnostics.wadcfgx. Ces paramètres de configuration spécifient le compte de stockage où les données de diagnostic sont enregistrées lorsque vous déployez votre service cloud.

### <a name="to-enable-diagnostics-in-visual-studio-before-deployment"></a>Pour activer les tests de diagnostic avant le déploiement dans Visual Studio

1. Dans le menu contextuel pour le rôle qui vous intéresse, choisissez **Propriétés**et cliquez sur l’onglet **Configuration** dans la fenêtre de **Propriétés** du rôle.

1. Dans la section **Diagnostics** , assurez-vous que la case à cocher **Activer les tests de diagnostic** est activée.

    ![Accès à l’option Activer les Diagnostics](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)

1. Cliquez sur le bouton de sélection (...) pour spécifier le compte de stockage où vous souhaitez stocker les données de diagnostic. Le compte de stockage que vous choisissez sera l’emplacement où sont stockées les données de diagnostic.

    ![Spécifier le compte de stockage à utiliser](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)

1. Dans la boîte de dialogue **Chaîne de connexion de stockage de créer** , indiquez si vous souhaitez vous connecter à l’aide de l’émulateur de stockage Azure, un abonnement Azure, ou saisis manuellement les informations d’identification.

    ![Boîte de dialogue compte de stockage](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)

  - Si vous choisissez Microsoft Azure émulateur de stockage option, la chaîne de connexion est UseDevelopmentStorage = true.

  - Si vous choisissez l’option de votre abonnement, vous pouvez choisir l’abonnement Azure que vous souhaitez utiliser et le nom du compte. Vous pouvez cliquez sur le bouton Gérer les comptes pour gérer vos abonnements d’Azure.

  - Si vous choisissez l’option informations d’identification saisies manuellement, vous êtes invité à entrer le nom et la clé du compte Azure que vous souhaitez utiliser.

1. Cliquez sur le bouton **configurer** pour afficher la boîte de dialogue **configuration de Diagnostics** . Chaque onglet (à l’exception de **générale** et les **Répertoires des journaux**) représente une source de données de diagnostic que vous pouvez collecter. L’onglet par défaut, **Général**, vous propose les options de collection de données de diagnostic suivants : **erreurs uniquement**, **toutes les informations**et **plan de personnalisé**. L’option par défaut, **uniquement les erreurs**, dure moins de stockage car il ne transfère pas les avertissements ou les messages de traçage. L’option toutes les informations transfère le maximum d’informations et est, par conséquent, l’option la plus coûteuse en termes de stockage.

    ![Activer la configuration et les diagnostics de Windows Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

1. Dans cet exemple, sélectionnez l’option **personnalisé plan** afin que vous puissiez personnaliser les données collectées.

1. La zone de **Quota de disque en Mo** spécifie combien d’espace vous souhaitez allouer dans votre compte de stockage pour les données de diagnostic. Vous pouvez modifier la valeur par défaut si vous le souhaitez.

1. Sous chaque onglet de vous souhaitez collecter des données de diagnostic, sélectionnez son **Activer les transferts de <log type> ** case à cocher. Par exemple, si vous souhaitez collecter les journaux de l’application, activez la case à cocher **Activer le transfert de journaux d’applications** sur l’onglet **Journaux des applications** . En outre, spécifiez toutes les autres informations requises par chaque type de données de diagnostic. Reportez-vous à la section **configurer des sources de données de diagnostic** plus loin dans cette rubrique pour des informations sur la configuration de chaque onglet.

1. Une fois que vous avez activé la collection de toutes les données de diagnostic que vous souhaitez, cliquez sur le bouton **OK** .

1. Exécutez votre projet de service cloud Azure dans Visual Studio comme d’habitude. Lorsque vous utilisez votre application, les informations du journal que vous avez activé sont enregistrées dans le compte de stockage Azure spécifié.

## <a name="enable-diagnostics-in-azure-virtual-machines"></a>Activer les diagnostics sur des machines virtuelles Azure

Dans Visual Studio, vous pouvez choisir de collecter des données de diagnostics pour les ordinateurs virtuels Azure.

### <a name="to-enable-diagnostics-in-azure-virtual-machines"></a>Pour activer les diagnostics sur des machines virtuelles Azure

1. Dans l' **Explorateur de serveurs**, choisissez le nœud Azure et se connecter à votre abonnement Azure, si vous n’êtes pas déjà connecté.

1. Développez le nœud **ordinateurs virtuels** . Vous pouvez créer un ordinateur virtuel, ou sélectionnez-en un qui existe déjà.

1. Dans le menu contextuel pour l’ordinateur virtuel qui vous intéresse, cliquez sur **configurer**. Affiche la boîte de dialogue de configuration de machine virtuelle.

    ![Configuration d’un ordinateur virtuel Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)

1. S’il n’est pas déjà installé, ajoutez l’extension de Diagnostics de l’Agent de surveillance de Microsoft. Cette extension vous permet de collecter des données de diagnostic pour la machine virtuelle Azure. Dans la liste Extensions installées, choisissez Sélectionner un menu de liste déroulante d’extension disponible, puis Diagnostics de l’Agent de surveillance de Microsoft.

    ![Installation d’une extension Azure VM](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)

    >[AZURE.NOTE] Autres extensions de diagnostic sont disponibles pour vos ordinateurs virtuels. Pour plus d’informations, reportez-vous à la section Azure VM Extensions et fonctionnalités.

1. Cliquez sur le bouton **Ajouter** pour ajouter l’extension et afficher la boîte de dialogue **configuration de Diagnostics** .

1. Cliquez sur le bouton **configurer** pour spécifier un compte de stockage et cliquez sur le bouton **OK** .

    Chaque onglet (à l’exception de **générale** et les **Répertoires des journaux**) représente une source de données de diagnostic que vous pouvez collecter.

    ![Activer la configuration et les diagnostics de Windows Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

    L’onglet par défaut, **Général**, vous propose les options de collection de données de diagnostic suivants : **erreurs uniquement**, **toutes les informations**et **plan de personnalisé**. L’option par défaut, **uniquement les erreurs**, dure moins de stockage car il ne transfère pas les avertissements ou les messages de traçage. L’option **toutes les informations** transfère le maximum d’informations et est, par conséquent, l’option la plus coûteuse en termes de stockage.

1. Dans cet exemple, sélectionnez l’option **personnalisé plan** afin que vous puissiez personnaliser les données collectées.

1. La zone de **Quota de disque en Mo** spécifie combien d’espace vous souhaitez allouer dans votre compte de stockage pour les données de diagnostic. Vous pouvez modifier la valeur par défaut si vous le souhaitez.

1. Sous chaque onglet de vous souhaitez collecter des données de diagnostic, sélectionnez son **Activer les transferts de <log type> ** case à cocher.

    Par exemple, si vous souhaitez collecter les journaux de l’application, activez la case à cocher **Activer le transfert de journaux d’applications** sur l’onglet **Journaux des applications** . En outre, spécifiez toutes les autres informations requises par chaque type de données de diagnostic. Reportez-vous à la section **configurer des sources de données de diagnostic** plus loin dans cette rubrique pour des informations sur la configuration de chaque onglet.

1. Une fois que vous avez activé la collection de toutes les données de diagnostic que vous souhaitez, cliquez sur le bouton **OK** .

1. Enregistrez le projet mis à jour.

    Vous verrez un message dans la fenêtre du **Journal d’activité de Microsoft Azure** , que l’ordinateur virtuel a été mis à jour.

## <a name="configure-diagnostics-data-sources"></a>Configurer les sources de données de diagnostic

Une fois que la collection de données de diagnostic, vous pouvez choisir exactement quelles sources de données que vous souhaitez collecter et quelles informations sont collectées. Voici une liste des onglets de la boîte de dialogue **configuration de Diagnostics** et de ce que chaque option de configuration.

### <a name="application-logs"></a>Journaux d’application

**Journaux d’application** contiennent des informations de diagnostic produites par une application web. Si vous souhaitez capturer les journaux d’application, sélectionnez la case à cocher **Activer le transfert de journaux d’applications** . Vous pouvez augmenter ou diminuer le nombre de minutes lorsque les journaux d’application sont transférés à votre compte de stockage, en modifiant la valeur de la **Période de transfert (min)** . Vous pouvez également modifier la quantité d’informations capturées dans le journal en définissant la valeur de niveau de journal. Par exemple, vous pouvez choisir des **commentaires** pour obtenir plus d’informations ou choisissez **critique** pour capturer uniquement les erreurs critiques. Si vous avez un fournisseur de diagnostics spécifiques qui émet des journaux d’applications, vous pouvez capturer les en ajoutant les GUID du fournisseur dans la zone de **GUID de fournisseur** .

  ![Journaux d’application](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Pour plus d’informations sur les journaux d’application, consultez [Activer l’enregistrement des diagnostics pour les applications web dans le Service d’application Azure](./app-service-web/web-sites-enable-diagnostic-log.md) .

### <a name="windows-event-logs"></a>Journaux des événements Windows

Si vous souhaitez capturer des journaux des événements Windows, activez la case à cocher **Activer le transfert de journaux d’événements Windows** . Vous pouvez augmenter ou diminuer le nombre de minutes lorsque les journaux des événements sont transférés à votre compte de stockage, en modifiant la valeur de la **Période de transfert (min)** . Activez les cases à cocher pour les types d’événements que vous souhaitez suivre.

  ![Journaux des événements](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Si vous utilisez Azure SDK version 2.6 ou ultérieur et que vous souhaitez spécifier une source de données personnalisée, entrez dans le **<Data source name>** texte zone, puis cliquez sur le bouton **Ajouter** en regard de celui-ci. La source de données est ajoutée au fichier diagnostics.cfcfg.

Si vous utilisez Azure SDK 2.5 et que vous souhaitez spécifier une source de données personnalisé, vous pouvez l’ajouter à la `WindowsEventLog` section de la diagnostics.wadcfgx de fichier, comme dans l’exemple suivant.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Compteurs de performance

Les informations de compteur de performance peuvent vous aider à localiser les goulots d’étranglement du système et ajuster les performances du système et des applications. Pour plus d’informations, consultez [Création et utilisation des compteurs de Performance dans une Application Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx) . Si vous souhaitez capturer des compteurs de performance, sélectionnez la case à cocher **Activer le transfert de compteurs de Performance** . Vous pouvez augmenter ou diminuer le nombre de minutes lorsque les journaux des événements sont transférés à votre compte de stockage, en modifiant la valeur de la **Période de transfert (min)** . Activez les cases à cocher pour les compteurs de performance que vous souhaitez suivre.

  ![Compteurs de performance](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Pour effectuer le suivi d’un compteur de performance n’est pas répertorié, entrez-la à l’aide de la syntaxe proposée et cliquez sur le bouton **Ajouter** . Le système d’exploitation sur l’ordinateur virtuel détermine les compteurs de performance que vous pouvez effectuer le suivi. Pour plus d’informations sur la syntaxe, consultez [spécification d’un chemin d’accès du compteur](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Journaux de l’infrastructure

Si vous souhaitez capturer des journaux d’infrastructure, qui contiennent des informations sur l’infrastructure de diagnostic Azure, le module d’accès à distance et le module RemoteForwarder, activez la case à cocher **Activer le transfert des journaux de l’Infrastructure** . Vous pouvez augmenter ou diminuer le nombre de minutes lorsque les journaux sont transférés à votre compte de stockage, en modifiant la valeur de la période de transfert (min).

  ![Journaux de diagnostics Infrastructure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Pour plus d’informations, reportez-vous à la section [Collecter des données de journalisation à l’aide des diagnostics Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) .

### <a name="log-directories"></a>Répertoires des journaux

Si vous souhaitez capturer des répertoires des journaux, qui contiennent des données collectées à partir des répertoires des journaux pour les requêtes Internet Information Services (IIS), les demandes qui ont échoué, ou les dossiers que vous choisissez, activez la case à cocher **Activer le transfert des répertoires des journaux** . Vous pouvez augmenter ou diminuer le nombre de minutes lorsque les journaux sont transférés à votre compte de stockage, en modifiant la valeur de la **Période de transfert (min)** .

Vous pouvez sélectionner les cases des journaux que vous souhaitez collecter, tels que les **Journaux de IIS** et de **La demande a échoué** . Noms de conteneur de stockage par défaut sont fournies, mais vous pouvez modifier le nom si vous le souhaitez.

En outre, vous pouvez capturer des journaux à partir de n’importe quel dossier. Il suffit de spécifier le chemin d’accès dans la section **journal de répertoire absolu** , puis choisissez le bouton **Ajouter un répertoire** . Les journaux seront capturées pour les conteneurs spécifiés.

  ![Répertoires des journaux](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>Journaux ETW

Si vous utilisez [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803(v=vs.85).aspx) (ETW) et que vous souhaitez capturer les journaux ETW, activez la case à cocher **Activer le transfert de journaux d’ETW** . Vous pouvez augmenter ou diminuer le nombre de minutes lorsque les journaux sont transférés à votre compte de stockage, en modifiant la valeur de la **Période de transfert (min)** .

Les événements sont capturées à partir de sources d’événements et les fichiers manifestes d’événements que vous spécifiez. Pour spécifier une source d’événements, entrez un nom dans la section **Sources d’événements** et cliquez sur le bouton **Ajouter Source de l’événement** . De la même façon, vous pouvez spécifier un manifeste de l’événement dans la section **Événements manifestes** et puis cliquez sur le bouton **Ajouter un événement manifeste** .

  ![Journaux ETW](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  Le cadre d’ETW est pris en charge dans ASP.NET par l’intermédiaire de classes dans le [System.Diagnostics.aspx] (espace de noms https://msdn.microsoft.com/library/system.diagnostics (v=vs.110). L’espace de noms Microsoft.WindowsAzure.Diagnostics, qui hérite d’et étend standard [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) des classes, permet l’utilisation de [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics (v=vs.110) sous la forme d’un cadre d’enregistrement dans l’environnement Azure. Pour plus d’informations, reportez-vous à [prendre le contrôle de journalisation et de traçage dans Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) et [L’activation des Diagnostics dans Azure Services en nuage et les Machines virtuelles](./cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Vidages sur incident

Si vous voulez capturer des informations lorsqu’une instance de rôle se bloque, activez la case à cocher **Activer le transfert de Crash Dumps** . (Car ASP.NET gère la plupart des exceptions, il est généralement utile que pour les rôles worker.) Vous pouvez augmenter ou diminuer le pourcentage d’espace de stockage dédié pour les vidages sur incident en modifiant la valeur de **Quota de répertoire (%)** . Vous pouvez modifier le conteneur de stockage où sont stockées les vidages sur incident et vous pouvez sélectionner si vous souhaitez capturer une image **complète** ou **Mini** .

Les processus est suivis actuellement sont répertoriés. Activez les cases à cocher pour les processus que vous souhaitez capturer. Pour ajouter un autre processus à la liste, entrez le nom du processus et cliquez sur le bouton **Ajouter des processus** .

  ![Vidages sur incident](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Voir [prendre le contrôle de journalisation et de traçage dans Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) et [Microsoft Azure Diagnostics partie 4 : composants de journalisation personnalisé et Azure Diagnostics 1.3 modifications](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/) pour plus d’informations.

## <a name="view-the-diagnostics-data"></a>Permet d’afficher les données de diagnostic

Une fois que vous avez collecté les données de diagnostic pour un service en nuage ou une machine virtuelle, vous pouvez l’afficher.

### <a name="to-view-cloud-service-diagnostics-data"></a>Pour afficher les données de diagnostic de service cloud

1. Déployez votre service cloud comme d’habitude, puis exécutez-le.

1. Vous pouvez afficher les données de diagnostic dans un rapport que Visual Studio génère ou tables dans votre compte de stockage. Pour afficher les données dans un rapport, ouvrez **Cloud Explorer** ou **l’Explorateur de serveurs**, ouvrir le menu contextuel du nœud pour le rôle qui vous intéresse, puis choisissez **Afficher les données de Diagnostic**.

    ![Afficher les données de diagnostic](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)

    Un état qui affiche les données disponibles s’affiche.

    ![Rapport de Diagnostics de Microsoft Azure dans Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)

    Si les données les plus récentes n’apparaissent pas, vous devrez peut-être attendre le délai de transfert s’écouler.

    Choisissez le lien **Actualiser** pour mettre immédiatement à jour les données, ou un intervalle dans la zone de liste déroulante **Actualisation automatique** pour que les données mises à jour automatiquement. Pour exporter les données d’erreur, cliquez sur le bouton **Exporter au format CSV** pour créer un fichier de valeurs séparées par des virgules que vous pouvez ouvrir dans une feuille de calcul.

    Dans le **Cloud Explorer** ou **l’Explorateur de serveurs**, ouvrez le compte de stockage associé au déploiement.

1. Ouvrez les tables de tests de diagnostic dans la visionneuse de la table et puis passez en revue les données que vous avez collectées. Pour les journaux IIS et les journaux personnalisés, vous pouvez ouvrir un conteneur d’objet blob. En consultant le tableau suivant, vous trouverez le conteneur de table ou d’objet blob qui contient les données qui vous intéressent. Outre les données de ce fichier journal, les entrées du tableau contiennent des EventTickCount, DeploymentId, rôle et RoleInstance pour vous aider à identifier les machines virtuelles et le rôle généré les données et à quel moment. 

  	|Données de diagnostic|Description|Emplacement|
  	|---|---|---|
  	|Journaux d’application|Journaux de votre code génère en appelant les méthodes de la classe de System.Diagnostics.Trace.|WADLogsTable|
  	|Journaux des événements|Ces données sont les journaux d’événements Windows sur les ordinateurs virtuels. Windows stocke les informations de ces journaux, mais les applications et services également les utilisent pour signaler les erreurs ou enregistrement des informations.|WADWindowsEventLogsTable|
  	|Compteurs de performance|Vous pouvez collecter des données sur n’importe quel compteur de performance qui est disponible sur l’ordinateur virtuel. Le système d’exploitation fournit des compteurs de performances, qui comprennent des statistiques telles que le temps processeur et l’utilisation de mémoire.|WADPerformanceCountersTable|
  	|Journaux de l’infrastructure|Ces journaux est générés à partir de l’infrastructure de diagnostics lui-même.|WADDiagnosticInfrastructureLogsTable|
  	|Journaux IIS|Ces journaux enregistrent les demandes web. Si votre service cloud Obtient un volume de trafic considérable, ces journaux peut être relativement long, vous devez collecter et stocker des données uniquement lorsque vous en avez besoin.|Vous pouvez trouver les journaux de demandes ayant échoué dans le conteneur de l’objet blob sous wad-iis-failedreqlogs sous un chemin d’accès pour ce déploiement, le rôle et l’instance. Vous pouvez trouver des journaux complets sous wad logfiles d’iis. Les entrées de chaque fichier sont effectuées dans la table WADDirectories.|
  	|Vidages sur incident|Ces informations fournissent des images binaires du processus de votre service cloud (généralement un rôle worker).|conteneur WAD-crush-vidages blob|
  	|Fichiers journaux personnalisés|Journaux de données que vous avez prédéfinies.|Vous pouvez spécifier dans le code de l’emplacement des fichiers journaux personnalisés dans votre compte de stockage. Par exemple, vous pouvez spécifier un conteneur d’objet blob personnalisé.|

1. Si les données de n’importe quel type sont tronquées, vous pouvez essayer de l’augmentation de la mémoire tampon pour les données de ces type ou raccourcir l’intervalle entre les transferts de données à partir de la machine virtuelle à votre compte de stockage.

1. (facultatif) Purger les données à partir du compte de stockage occasionnellement pour réduire les coûts globaux de stockage.

1. Lorsque vous effectuez un déploiement complet, le fichier diagnostics.cscfg (.wadcfgx pour Azure SDK 2.5) est mis à jour dans Azure et votre service cloud reprend toutes les modifications à votre configuration de diagnostics. Si, à la place, mettre à jour un déploiement existant, le fichier .cscfg n’est pas mis à jour dans Azure. Vous pouvez toujours modifier les paramètres de diagnostic, cependant, en suivant les étapes décrites dans la section suivante. Pour plus d’informations sur l’exécution d’un déploiement complet et de mise à jour d’un déploiement existant, reportez-vous à la section [Publier Assistant Création d’applications Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Pour afficher les données de diagnostic d’ordinateur virtuel

1. Dans le menu contextuel pour l’ordinateur virtuel, choisissez **Afficher les données de diagnostic**.

    ![Afficher les données de diagnostic dans Azure VM](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)

    La fenêtre **Diagnostics résumé** s’ouvre.

    ![Diagnostics d’Azure VM résumé](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  

    Si les données les plus récentes n’apparaissent pas, vous devrez peut-être attendre le délai de transfert s’écouler.

    Choisissez le lien **Actualiser** pour mettre immédiatement à jour les données, ou un intervalle dans la zone de liste déroulante **Actualisation automatique** pour que les données mises à jour automatiquement. Pour exporter les données d’erreur, cliquez sur le bouton **Exporter au format CSV** pour créer un fichier de valeurs séparées par des virgules que vous pouvez ouvrir dans une feuille de calcul.

## <a name="configure-cloud-service-diagnostics-after-deployment"></a>Configurer des tests de diagnostic de service cloud après le déploiement

Si vous effectuez des recherches sur un problème avec un nuage de service qui en cours d’exécution, vous pouvez collecter des données que vous n’avez pas spécifié avant que vous avez déployé initialement le rôle. Dans ce cas, vous pouvez démarrer à recueillir des données en utilisant les paramètres dans l’Explorateur de serveurs. Vous pouvez configurer des diagnostics pour une instance unique ou de toutes les instances d’un rôle, selon que vous ouvrez la boîte de dialogue de Configuration des tests de diagnostic dans le menu contextuel pour l’instance ou du rôle. Si vous configurez le nœud de rôle, les modifications s’appliquent à toutes les instances. Si vous configurez le nœud d’instance, les modifications s’appliquent à cette instance uniquement.

### <a name="to-configure-diagnostics-for-a-running-cloud-service"></a>Pour configurer des diagnostics pour un service cloud

1. Dans l’Explorateur de serveurs, développez le nœud **Services de Cloud** et puis développez les nœuds pour rechercher le rôle ou l’instance que vous souhaitez examiner ou les deux.

    ![Configuration des Diagnostics](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)

1. Dans le menu contextuel pour un nœud d’instance ou un rôle, choisissez les **Paramètres de tests de diagnostic de mise à jour**et puis choisissez les paramètres de diagnostics que vous souhaitez collecter.

    Pour plus d’informations sur les paramètres de configuration, consultez **Configuration des sources de données de diagnostic** dans cette rubrique. Pour plus d’informations sur la façon d’afficher les données de diagnostic, afficher **les données de diagnostic** dans cette rubrique.

    Si vous modifiez la collection de données dans **l’Explorateur de serveurs**, ces changements restent en vigueur jusqu'à ce que vous redéployez entièrement votre service cloud. Si vous utilisez la valeur par défaut, paramètres de publication, les modifications ne sont pas remplacées, depuis la publication de la valeur par défaut est mettre à jour le déploiement existant, plutôt que d’effectuer un redéploiement complet. Pour vous assurer que les paramètres désactiver au moment du déploiement, accédez à l’onglet **Paramètres avancés** dans l’Assistant Publication et désactivez la case à cocher **mise à jour du déploiement** . Lorsque vous redéployez avec cette case à cocher désactivée, les paramètres de les rétablissement dans le fichier .wadcfgx (ou .wadcfg) tel que défini par le biais de l’éditeur de propriétés pour le rôle. Si vous mettez à jour votre déploiement, Azure conserve les anciens paramètres.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Résolution des problèmes de service cloud Azure

Si vous rencontrez des problèmes avec vos projets de service cloud, notamment un rôle qui reste bloqué dans un état « occupé », à plusieurs reprises recycle ou génère une erreur de serveur interne, il existe des outils et techniques que vous pouvez utiliser pour diagnostiquer et résoudre ces problèmes. Pour des exemples de problèmes courants et solutions spécifiques, ainsi que d’une vue d’ensemble des concepts et des outils permettant de diagnostiquer et de résoudre ces erreurs, consultez [PaaS Azure Compute des données de diagnostic](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Q & r

**Quelle est la taille de la mémoire tampon et la taille doit-il être ?**

Sur chaque instance de la machine virtuelle, quotas de limitent la quantité de données diagnostic peut être stockée sur le système de fichiers local. En outre, vous spécifiez une taille de mémoire tampon pour chaque type de données de diagnostic ne sont disponibles. Cette taille de tampon agit comme un quota individuel pour ce type de données. En vérifiant le bas de la boîte de dialogue, vous pouvez déterminer le quota global et la quantité de mémoire qui reste. Si vous spécifiez des mémoires tampons plus volumineuses ou davantage de types de données, vous allez aborder le contingent global. Vous pouvez modifier le volume global du contingent en modifiant le fichier de configuration diagnostics.wadcfg/.wadcfgx. Les données de diagnostic sont stockées sur le même système de fichiers en tant que données de votre application, donc si votre application utilise beaucoup d’espace disque, vous ne devez pas augmenter le quota de diagnostics globale.

**Quelle est la période de transfert, et combien de temps il convient ?**

La période de transfert est le laps de temps qui s’écoule entre les données de capture. Après chaque période de transfert, les données sont déplacées dans le système de fichiers local sur un ordinateur virtuel à des tableaux dans votre compte de stockage. Si la quantité de données collectées dépasse le quota avant la fin d’une période de transfert, les données plus anciennes sont ignorées. Vous pouvez souhaiter réduire la période de transfert si vous perdez des données car vos données dépassent la taille de la mémoire tampon ou le contingent global.

**Les horodatages dans quel fuseau horaire sont-elles**

Les horodatages sont dans le fuseau horaire local du centre de données qui héberge votre service cloud. Les suivant trois colonnes timestamp dans les tables du journal sont utilisés.

  - **PreciseTimeStamp** est l’horodatage de l’événement ETW. Autrement dit, l’heure de l’événement est enregistré à partir du client.

  - **TIMESTAMP** est arrondi à la limite de fréquence de chargement de PreciseTimeStamp. Ainsi, si votre fréquence de téléchargement est de 5 minutes et l’événement heure 00:17:12, horodatage sera 00:15:00.

  - **Timestamp** est l’horodatage auquel l’entité a été créée dans la table Azure.

**Comment gérer les coûts lors de la collecte des informations de diagnostic ?**

Les paramètres par défaut (**niveau du journal** **d’erreur** et **transférer la période** paramétrée sur **1 minute**) sont conçus pour réduire les coûts. Vos coûts de calcul augmente si vous recueillir plus de données de diagnostics ou de Réduisez la période de transfert. Ne pas collecter plus de données que vous avez besoin et n’oubliez pas de désactiver la collecte de données lorsque vous n’avez plus besoin il. Vous pouvez toujours l’activer à nouveau, même au moment de l’exécution, comme indiqué dans la section précédente.

**Comment collecter les journaux de demandes ayant échoué à partir d’IIS ?**

Par défaut, IIS ne collecter les journaux de demandes ayant échoué. Vous pouvez configurer IIS de façon à les regrouper si vous modifiez le fichier web.config pour votre rôle web.

**Je ne reçois les informations de trace à partir de méthodes de RoleEntryPoint comme OnStart. Qu'est-ce qui ne va pas ?**

Les méthodes de RoleEntryPoint sont appelés dans le cadre de WAIISHost.exe, pas d’IIS. Par conséquent, les informations de configuration dans le fichier web.config qui active le traçage ne s’applique pas normalement. Pour résoudre ce problème, ajoutez un fichier .config à votre projet de rôle web et nommez le fichier correspondant à l’assembly de sortie qui contient le code de RoleEntryPoint. Dans le projet de rôle web par défaut, le nom du fichier .config serait WAIISHost.exe.config. Puis ajoutez les lignes suivantes à ce fichier :

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Maintenant, dans la fenêtre **Propriétés** , affectez la propriété **Copy to Output Directory** à **toujours copier**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les diagnostics logging dans Azure, reportez-vous à la section [Activation des Diagnostics dans Azure Services en nuage et les Machines virtuelles](./cloud-services/cloud-services-dotnet-diagnostics.md) et [Activer l’enregistrement des diagnostics pour les applications web dans le Service d’application Azure](./app-service-web/web-sites-enable-diagnostic-log.md).
