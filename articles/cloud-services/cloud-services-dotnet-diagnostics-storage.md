<properties
    pageTitle="Banque d’informations et afficher des données de Diagnostic dans le stockage Azure | Microsoft Azure"
    description="Obtenir des données de diagnostics de Windows Azure dans le stockage Azure et l’afficher"
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="cloud-services"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="robb" />

# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Banque d’informations et afficher des données de diagnostic dans le stockage Azure

Données de diagnostic ne sont pas stockées définitivement, sauf si vous le transférer à l’émulateur de stockage Microsoft Azure, ou au stockage Azure. Une fois dans le stockage, il peut être affiché avec un des nombreux outils disponibles.

## <a name="specify-a-storage-account"></a>Spécifiez un compte de stockage

Vous spécifiez le compte de stockage que vous souhaitez utiliser dans le fichier ServiceConfiguration.cscfg. Les informations de compte sont définies sous la forme d’une chaîne de connexion dans un paramètre de configuration. L’exemple suivant montre la chaîne de connexion par défaut créée pour un nouveau projet de Service Cloud dans Visual Studio :


```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Vous pouvez modifier cette chaîne de connexion pour fournir des informations de compte pour un compte de stockage Azure.

Selon le type de données de diagnostic qui sont collectées, Diagnostics d’Azure utilise le service d’objet Blob ou le service de la Table. Le tableau suivant affiche les sources de données qui sont rendues persistantes et leur format.

|Source de données|Format de stockage|
|---|---|
|Journaux d’Azure|Table|
|Journaux IIS 7.0|Objet BLOB|
|Journaux d’infrastructure Diagnostics Azure|Table|
|Échec de journaux de suivi de la demande|Objet BLOB|
|Journaux des événements Windows|Table|
|Compteurs de performance|Table|
|Vidages sur incident|Objet BLOB|
|Journaux d’erreurs personnalisées|Objet BLOB|

## <a name="transfer-diagnostic-data"></a>Transfert de données de diagnostic

Pour le SDK 2.5 et les versions ultérieures, la demande de transfert de données de diagnostic peut se produire dans le fichier de configuration. Vous pouvez transférer des données de diagnostic à intervalles réguliers comme spécifié dans la configuration.

Pour le Kit de développement logiciel 2.4 et antérieures, vous pouvez demander à transférer les données de diagnostic via le fichier de configuration ainsi que de la programmation. Cette méthode vous permet également d’effectuer des transferts de la demande.


>[AZURE.IMPORTANT] Lorsque vous transférez des données de diagnostic pour un compte de stockage Azure, vous entraînez des coûts pour les ressources de stockage qui utilise de vos données de diagnostic.

## <a name="store-diagnostic-data"></a>Stocker des données de diagnostic

Les données du journal sont stockées dans le stockage Blob ou de Table avec les noms suivants :

**Tables**

- **WadLogsTable** - journaux écrits dans le code à l’aide de l’écouteur de la trace.

- **WADDiagnosticInfrastructureLogsTable** - Analyseur de Diagnostic et de configuration change.

- **WADDirectoriesTable** – répertoires dans lesquels le diagnostic surveille.  Cela inclut les journaux IIS, IIS ne peut pas les journaux de demandes et les répertoires personnalisés.  L’emplacement du fichier journal blob est spécifié dans le champ conteneur, et le nom de l’objet blob est dans le champ RelativePath.  Le champ AbsolutePath indique l’emplacement et le nom du fichier tel qu’il existait sur la machine virtuelle Azure.

- **WADPerformanceCountersTable** : les compteurs de Performance.

- **WADWindowsEventLogsTable** – les journaux des événements Windows.

**Objets BLOB**

- **conteneur de contrôle wad** – (uniquement pour le Kit de développement logiciel 2.4 et précédente) contient les fichiers de configuration XML qui contrôle les diagnostics de Windows Azure.

- Contient les informations à partir d’IIS n’a pas pu demander de **wad-iis-failedreqlogfiles** – enregistre.

- **wad logfiles d’iis** – contient les informations IIS se connecte.

- **« personnalisé »** : un conteneur personnalisé en fonction de la configuration des répertoires qui sont analysés par l’Analyseur de diagnostic.  Le nom de ce conteneur blob est spécifié dans WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Outils pour afficher les données de diagnostic
Plusieurs outils sont disponibles pour afficher les données une fois qu’il est transféré vers le stockage. Par exemple :

- Explorateur de serveurs dans Visual Studio - si vous avez installé les outils d’Azure pour Visual Studio de Microsoft, vous pouvez utiliser le nœud de stockage Azure dans l’Explorateur de serveurs pour afficher des blob en lecture seule et les données de la table à partir de vos comptes de stockage Azure. Vous pouvez afficher des données à partir de votre compte d’émulateur de stockage local et également à partir de comptes de stockage, vous avez créé pour Azure. Pour plus d’informations, reportez-vous à la section [de navigation et de gestion des ressources de stockage avec l’Explorateur de serveurs](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).

- [Explorateur de stockage Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome qui vous permet de travailler facilement avec des données de stockage Azure sur Windows et Linux OSX.

- [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) inclut le Gestionnaire de tests de diagnostic d’Azure qui vous permet de consulter, de télécharger et de gérer les données de diagnostic collectées par les applications qui s’exécutent sur Azure.


## <a name="next-steps"></a>Étapes suivantes

[Suivre le flux dans une application de Services en nuage avec Azure Diagnostics](cloud-services-dotnet-diagnostics-trace-flow.md)
