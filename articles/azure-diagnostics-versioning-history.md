<properties
    pageTitle="Historique de Version de Diagnostics de Windows Azure"
    description="Explication des changements dans les différentes versions de diagnostics de Windows Azure comme livré avec différentes versions de kits de développement logiciel de Microsoft Azure."
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="robb"/>


# <a name="microsoft-azure-diagnostics-version-history"></a>Historique des versions de tests de diagnostic de Microsoft Azure

Nouveau pour les Diagnostics de Windows Azure ? Consultez [vue d’ensemble des Diagnostics de Windows Azure](azure-diagnostics.md).

Chaque version du SDK d’Azure est généralement fourni avec une nouvelle version de Diagnostics d’Azure. Le tableau ci-dessous décrit les versions du SDK d’Azure et d’Azure Diagnostics liées à la version du Kit de développement logiciel.



Version SDK Azure | Version de Diagnostics Azure | Modèle
--- | --- | ---
1.x      | 1.0 | plug-in
2.0 et 2.4| 1.0 | "
2.5      | 1.2 | extension
2.6      | 1.3 | "
2.7      | 1.4 | "
2.8      | 1.5 | "


La version la plus récente est de 1,5, ce qui est fourni avec Azure SDK 2.8. La version d’extension de Diagnostics d’Azure qui est fourni avec le Kit de développement est uniquement utilisée pour les scénarios d’émulateur local. Toute application déployée utilise automatiquement la dernière version lors de l’exécution dans Azure, indépendamment des version du SDK de l’application est générée avec. Toutefois, à moins d’installer la dernière version du SDK de Azure, vous n’avez pas tous les outils qui vous aident à utilisent les nouvelles fonctionnalités.

Diverses fonctionnalités et des modifications décrites ci-après.

## <a name="azure-sdk-28"></a>Azure SDK 2.8
Azure SDK 2.8 ajouté la possibilité d’envoyer des données de diagnostic pour les [Perspectives de l’Application](./application-insights/app-insights-cloudservices.md) , ce qui la rend plus facile à diagnostiquer des problèmes au sein de votre application, ainsi que le niveau de l’infrastructure système et.

## <a name="azure-26-diagnostics-changes"></a>Modifications de diagnostics Azure 2.6

Pour les projets de Service en nuage 2.6 SDK Azure dans Visual Studio, les modifications suivantes ont été apportées. (Ces modifications s’appliquent également aux versions ultérieures du Kit de développement logiciel Azure.)

- L’émulateur local prend désormais en charge les tests de diagnostic. Cela signifie que vous pouvez collecter des données de diagnostics et assurez-vous que votre application crée les traces de droite alors que vous êtes de développement et de test dans Visual Studio. La chaîne de connexion `UseDevelopmentStorage=true` permet la collecte de données de diagnostic lorsque vous exécutez votre projet de service cloud dans Visual Studio à l’aide de l’émulateur de stockage Azure. Toutes les données de diagnostic sont collectées dans le compte de stockage (stockage de développement).

- La chaîne de connexion de compte stockage diagnostics (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) est stockée une fois de plus dans le fichier de configuration (.cscfg) du service. Le compte de stockage diagnostics Azure SDK 2.5 a été spécifié dans le fichier diagnostics.wadcfgx.

Il existe des différences notables entre la façon dont la chaîne de connexion a travaillé dans Azure SDK 2.4 et les versions antérieures et de son fonctionnement dans Azure SDK 2.6 et versions ultérieures.

- Dans Azure SDK 2.4 et versions antérieures, la chaîne de connexion a été utilisée comme un runtime par le plug-in de diagnostics pour obtenir les informations de compte de stockage pour le transfert des journaux de diagnostics.

- Dans Azure SDK 2.6 et les versions ultérieures, la chaîne de connexion de diagnostic est utilisée par Visual Studio pour configurer l’extension de diagnostics avec les informations de compte de stockage approprié lors de la publication. La chaîne de connexion vous permet de définir des comptes de stockage différents pour les configurations de services différentes que Visual Studio utilisera lors de la publication. Toutefois, étant donné que le plug-in de diagnostics n’est plus disponible (après Azure SDK 2.5), le fichier .cscfg par lui-même ne peut pas activer l’Extension des tests de diagnostic. Vous devez activer l’extension séparément par le biais d’outils tels que Visual Studio ou PowerShell.

- Pour simplifier le processus de configuration de l’extension de diagnostics avec PowerShell, la sortie du package à partir de Visual Studio contient également le fichier XML de configuration public pour l’extension des tests de diagnostic pour chaque rôle. Visual Studio utilise la chaîne de connexion de diagnostic pour remplir les informations de compte de stockage présentes dans la configuration du publique. Les fichiers de configuration public sont créés dans le dossier Extensions et suivent le modèle PaaSDiagnostics. <RoleName>. PubConfig.xml. Les déploiements de PowerShell basé peuvent utiliser ce modèle pour mapper chaque configuration à un rôle.

- La chaîne de connexion dans le fichier .cscfg est également utilisée par le portail Azure à accéder aux données de diagnostic, afin qu’il peut apparaître dans l’onglet **analyse** . La chaîne de connexion est nécessaire pour configurer le service pour afficher les données d’analyse détaillées dans le portail.

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migration de projets vers Azure SDK 2.6 et versions ultérieures

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
