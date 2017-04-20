<properties
   pageTitle="Configuration de votre projet Azure à l’aide de plusieurs configurations de service | Microsoft Azure"
   description="Découvrez comment configurer un projet de service cloud Azure en modifiant les fichiers ServiceDefinition.csdef et ServiceConfiguration.cscfg."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Configuration de votre projet Azure à l’aide de plusieurs Configurations de Service

Un projet de service cloud Azure comprend deux fichiers de configuration : ServiceDefinition.csdef et ServiceConfiguration.cscfg. Ces fichiers sont fournis avec votre application de service de cloud Azure et déployés sur Azure.

- Le fichier **ServiceDefinition.csdef** contient les métadonnées qui sont requis par l’environnement Azure pour les besoins de votre application de service cloud, y compris les rôles qu’il contient. Ce fichier contient également les paramètres de configuration qui s’appliquent à toutes les instances. Ces paramètres de configuration peuvent être lues lors de l’exécution à l’aide de l’API du Runtime Azure Service d’hébergement. Ce fichier ne peut pas être mis à jour lorsque votre service s’exécute dans Azure.

- Le fichier **ServiceConfiguration.cscfg** définit des valeurs pour les paramètres de configuration définis dans le fichier de définition de service et indique le nombre d’instances à exécuter pour chaque rôle. Ce fichier peut être mis à jour pendant l’exécution de votre service cloud dans Azure.

L’Azure Tools pour Visual Studio de Microsoft fournissent des pages de propriétés que vous pouvez utiliser pour définir les paramètres de configuration stockés dans ces fichiers. Pour accéder aux pages de propriétés, double-cliquez sur la référence de rôle sous le projet de service cloud Azure dans l’Explorateur de solutions, ou avec le bouton droit de la référence du rôle et choisissez **Propriétés**, comme illustré dans la figure suivante.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Pour plus d’informations sur les schémas sous-jacente pour la définition de service et les fichiers de configuration de service, consultez la [Référence du schéma](https://msdn.microsoft.com/library/azure/dd179398.aspx). Pour plus d’informations sur la configuration du service, consultez [comment configurer les Services en nuage](./cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Configuration des propriétés de rôle

Les pages de propriétés pour un rôle web et un rôle worker sont semblables, bien qu’il existe quelques différences, précisés dans les sections suivantes.

À partir de la page **mise en cache** , vous pouvez configurer l’Azure services de mise en cache.

### <a name="configuration-page"></a>Page de configuration

Sur la page de **Configuration** , vous pouvez définir ces propriétés :

**Instances**

Définissez la propriété de nombre **d’Instance** pour le nombre d’instances, que le service doit s’exécuter pour ce rôle.

Définissez la propriété de **taille de mémoire virtuelle** à **Très petite**, **petite**, **moyenne**, **grande**ou **Très grande**.  Pour plus d’informations, reportez-vous à la section [tailles pour les Services en nuage](./cloud-services/cloud-services-sizes-specs.md).

**Action de démarrage** (Rôle web uniquement)

Définir cette propriété pour spécifier que Visual Studio doit lancer un navigateur web pour les points de terminaison HTTP ou les points de terminaison HTTPS ou les deux lorsque vous démarrez le débogage.

L’option de point de terminaison HTTPS est disponible uniquement si vous avez déjà défini un point de terminaison HTTPS pour votre rôle. Vous pouvez définir un point de terminaison HTTPS sur la page de propriétés des **points de terminaison** .

Si vous avez déjà ajouté un point de terminaison HTTPS, l’option de point de terminaison HTTPS est activée par défaut, et Visual Studio lance un navigateur pour ce point de terminaison lorsque vous démarrez le débogage, en plus d’un navigateur pour votre point de terminaison HTTP. Cela suppose que les deux options de démarrage sont activées.

**Tests de diagnostic**

Par défaut, les tests de diagnostic est activé pour le rôle Web. Le compte de service cloud Azure projet et le stockage sont définies pour utiliser l’émulateur de stockage local. Lorsque vous êtes prêt à déployer sur Azure, vous pouvez sélectionner le bouton Générateur (**...**) pour mettre à jour le compte de stockage pour utiliser le stockage Azure dans le nuage. Vous pouvez transférer les données de diagnostic pour le compte de stockage à la demande ou à intervalles planifiés automatiquement. Pour plus d’informations sur les diagnostics de Windows Azure, consultez [Activation des Diagnostics dans Azure Services en nuage et les Machines virtuelles](./cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Page Paramètres

Dans la page **paramètres** , vous pouvez ajouter des paramètres de configuration de votre service. Paramètres de configuration sont des paires nom-valeur. Le code s’exécutant dans le rôle peut lire les valeurs de vos paramètres de configuration au moment de l’exécution à l’aide des classes fournies par la [Bibliothèque managée d’Azure](http://go.microsoft.com/fwlink?LinkID=171026). En particulier, la méthode [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) renvoie la valeur d’un paramètre de configuration nommée lors de l’exécution.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Configuration d’une chaîne de connexion à un compte de stockage

Une chaîne de connexion est un paramètre de configuration qui fournit des informations de connexion et d’authentification pour l’émulateur de stockage ou d’un compte de stockage Azure. Chaque fois que votre code doit accéder aux données de services de stockage Azure – c'est-à-dire, blob, file d’attente ou les données de table – à partir du code s’exécutant dans un rôle, vous devez définir une chaîne de connexion pour ce compte de stockage.

Une chaîne de connexion qui pointe vers un compte de stockage Azure doit utiliser un format défini. Pour plus d’informations sur la façon de créer des chaînes de connexion, consultez [Chaînes de connexion de stockage Azure configurer](./storage/storage-configure-connection-string.md).

Lorsque vous êtes prêt à tester votre service contre les services de stockage Azure, ou lorsque vous êtes prêt à déployer votre service cloud sur Azure, vous pouvez modifier la valeur de toute chaîne de connexion pour pointer vers votre compte de stockage Azure. Sélectionnez (****), sélectionnez les **informations d’identification de compte de stockage entrée**. Entrez vos informations de compte qui inclut votre nom de compte et votre clé de compte. Dans la boîte de dialogue **Chaîne de connexion de compte de stockage** , vous pouvez également indiquer si vous souhaitez utiliser les points de terminaison HTTPS par défaut (l’option par défaut), les points de terminaison HTTP par défaut, les points de terminaison personnalisés. Vous pouvez décider d’utiliser des points de terminaison personnalisés si vous avez enregistré un nom de domaine personnalisé pour votre service, comme décrit dans [configuration d’un nom de domaine personnalisé pour les données blob dans un compte de stockage Azure](./storage/storage-custom-domain-name.md).

>[AZURE.IMPORTANT] Vous devez modifier vos chaînes de connexion pour pointer vers un compte de stockage Azure avant le déploiement de votre service. Ne parvient pas à effectuer cette opération risque de votre rôle de ne pas démarrer ou pour passer en revue les États lors de l’initialisation, occupés et l’arrêt.

## <a name="endpoints-page"></a>Page points de terminaison

Un rôle worker peut avoir n’importe quel nombre de points de terminaison HTTP, HTTPS ou TCP. Points de terminaison peuvent être des points de terminaison d’entrée, qui sont disponibles pour les clients externes, ou des points de terminaison internes, qui sont disponibles à d’autres rôles qui sont exécutent dans le service.

- Pour un point de terminaison HTTP à disposition des clients externes et les navigateurs Web, modifier le type de point de terminaison d’entrée et spécifiez un nom et un numéro de port publics.

- Pour libérer un point de terminaison HTTPS pour les clients externes et les navigateurs Web, remplacez le type de point de terminaison **d’entrée**et spécifiez un nom, un numéro de port publics et un nom de certificat de gestion.

    Notez que, avant de pouvoir spécifier un certificat de gestion, vous devez définir le certificat sur la page de propriétés de **certificats** .

- Pour libérer un point de terminaison pour l’accès interne par d’autres rôles dans le service cloud, changez le type de point de terminaison interne et spécifiez un nom et des ports privés possibles pour ce point de terminaison.

## <a name="local-storage-page"></a>Page de stockage local

Vous pouvez utiliser la page de propriétés de **Stockage Local** pour réserver un ou plusieurs des ressources de stockage local pour un rôle. Une ressource de stockage local est un répertoire réservé dans le système de fichiers de la machine virtuelle Azure dans lequel une instance d’un rôle est en cours d’exécution.

## <a name="certificates-page"></a>Page certificats

Sur la page de **certificats** , vous pouvez associer des certificats à votre rôle. Les certificats que vous ajoutez peuvent être utilisés pour configurer vos points de terminaison HTTPS sur la page de propriétés des **points de terminaison** .

La page de propriétés de **certificats** ajoute des informations sur vos certificats à votre configuration de service. Notez que vos certificats ne sont pas compressées avec votre service ; Vous devez télécharger vos certificats séparément sur Azure via [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

Pour associer un certificat à votre rôle, fournissez un nom pour le certificat. Vous utilisez ce nom pour faire référence au certificat lorsque vous configurez un point de terminaison HTTPS sur la page de propriétés des **points de terminaison** . Ensuite, spécifiez si le magasin de certificats est **Local Machine** ou **Utilisateur actuel** et le nom de la banque. Enfin, vous devez entrer l’empreinte du certificat. Si le certificat est dans l’actuel\Personnel (My), vous pouvez entrer l’empreinte du certificat en sélectionnant le certificat à partir d’une liste. Si elle se trouve dans un autre emplacement, entrez la valeur de l’empreinte numérique à la main.

Lorsque vous ajoutez un certificat du magasin de certificats, les certificats intermédiaires sont ajoutés automatiquement aux paramètres de configuration pour vous. Ces certificats intermédiaires doivent également être téléchargés vers Azure pour configurer correctement votre service pour SSL.

Les certificats de gestion que vous associez à votre service s’appliquent à votre service uniquement lorsqu’il s’exécute dans le nuage. Lorsque votre service est en cours d’exécution dans l’environnement de développement local, il utilise un certificat qui est géré par l’émulateur de calcul standard.

## <a name="configuring-the-azure-cloud-service-project"></a>Configuration du projet de service cloud Azure

Pour configurer les paramètres qui s’appliquent à un projet de service cloud d’Azure ensemble, vous ouvrez le menu contextuel pour le nœud de projet pour la première fois, et puis que vous choisissez Propriétés pour ouvrir ses pages de propriétés. Le tableau suivant montre les pages de propriétés.

|Page de propriétés|Description|
|---|---|
|Application|À partir de cette page, vous pouvez afficher des informations sur la version des outils Azure qui utilise de ce projet de service cloud et vous pouvez mettre à niveau vers la version actuelle des outils.|
|Événements de génération|À partir de cette page, vous pouvez définir des événements avant génération et après génération.|
|Développement|À partir de cette page, vous pouvez spécifier des instructions de configuration de build et les conditions dans lesquelles les événements post-build sont exécutés.|
|Web|À partir de cette page, vous pouvez configurer les paramètres relatifs au serveur web.|
