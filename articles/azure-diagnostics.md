<properties
    pageTitle="Vue d’ensemble des Diagnostics de Windows Azure | Microsoft Azure"
    description="Utilisez les diagnostics de Windows Azure pour débogage, mesure des performances, de surveillance, l’analyse du trafic dans les services en nuage, les ordinateurs virtuels et tissu de service"
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
    ms.date="06/02/2016"
    ms.author="robb"/>


# <a name="what-is-microsoft-azure-diagnostics"></a>Nouveautés Microsoft Azure Diagnostics


Diagnostics de Windows Azure est la capacité d’Azure qui active la collecte de données de diagnostic dans une application déployée. Vous pouvez utiliser l’extension de tests de diagnostic à partir d’un certain nombre de sources différentes. Prise en charge actuellement sont Azure Cloud Service rôles Web et Worker, Azure Machines virtuelles Microsoft Windows et le Service Fabric. Autres services Azure ont leur propre diagnostic distinct.

## <a name="data-you-can-collect"></a>Vous pouvez collecter des données

Diagnostics de Windows Azure peut collecter les types de données suivants :

Source de données|Description
---|---
Compteurs de performance | Système d’exploitation et les compteurs de performance personnalisés
Journaux d’application     | Messages de trace écrites par votre application.
Journaux des événements Windows   | Informations envoyées vers le système de journalisation des événements de Windows
Source de l’événement .NET    | Code d’écrire des événements à l’aide de la classe .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)
Journaux IIS             | Informations sur les sites web IIS
Manifeste de base ETW   | Événements de traçage pour Windows générés par les processus
Vidages sur incident          | Informations sur l’état du processus en cas d’une panne de l’application
Journaux d’erreurs personnalisées    | Journaux créés par votre application ou service
Journaux de l’infrastructure de Diagnostic Azure|Informations sur les Diagnostics lui-même

L’extension de diagnostics de Windows Azure peut transférer ces données vers un compte de stockage Azure ou de l’envoyer aux services tels que les [Perspectives de l’Application](./application-insights/app-insights-cloudservices.md). Vous pouvez utiliser les données pour le débogage et résolution des problèmes de mesure des performances, de la surveillance de l’utilisation des ressources, de l’analyse du trafic et de planification de la capacité et l’audit.


## <a name="versioning"></a>Contrôle de version
Consultez [l’historique des versions de Diagnostics de Windows Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Étapes suivantes
Choisissez le service que vous essayez de collecter des tests de diagnostic sur et utilisez les articles suivants pour commencer. Utilisez les liens de diagnostics de Windows Azure général de référence pour des tâches spécifiques.

## <a name="web-apps"></a>Applications Web
Notez que les applications Web n’utilisent pas de Diagnostics d’Azure. Trouver des informations équivalentes à des [Applications Web](./app-service-web/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Services en nuage à l’aide des tests de diagnostic Azure
- Si vous utilisez Visual Studio, consultez [Utilisation de Visual Studio à une application de Services en nuage de trace](./vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Dans le cas contraire, reportez-vous à la section
- [Comment faire pour surveiller les services en nuage à l’aide des tests de diagnostic Azure](./cloud-services/cloud-services-how-to-monitor.md)
- [Configurer des Diagnostics d’Azure dans une Application de Services de Cloud](./cloud-services/cloud-services-dotnet-diagnostics.md)

Pour des sujets plus avancés, consultez

- [À l’aide des Diagnostics de Windows Azure avec aperçus de l’Application des Services en nuage](./application-insights/app-insights-cloudservices.md)
- [Suivre le flux d’une application de Services en nuage avec Azure Diagnostics](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Utilisation de PowerShell pour configurer des tests de diagnostic sur les Services en nuage](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## <a name="virtual-machines-using-azure-diagnostics"></a>Machines virtuelles à l’aide des tests de diagnostic Azure
- Si vous utilisez Visual Studio, consultez [Utilisation de Visual Studio à tracer ordinateurs virtuels Azure](./vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Dans le cas contraire, reportez-vous à la section
- [Configurer des Diagnostics d’Azure sur une Machine virtuelle de Azure](./virtual-machines-dotnet-diagnostics.md)

Pour des sujets plus avancés, consultez

- [Utilisation de PowerShell pour configurer les diagnostics Azure machines virtuelles en fonctionnement](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Créer un ordinateur virtuel Windows avec la surveillance et de diagnostic à l’aide du modèle de gestionnaire de ressources Azure](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## <a name="service-fabric-using-azure-diagnostics"></a>TISSU de service à l’aide des tests de diagnostic Azure
Mise en route au [Moniteur d’une application de Service de Fabric](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). De nombreux autres articles de diagnostics Fabric de Service sont disponibles dans l’arborescence de navigation sur la gauche une fois que vous accédez à cet article.

## <a name="general-azure-diagnostics-articles"></a>Articles de tests de diagnostic général Azure
- [Schéma de Configuration de Diagnostics d’azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) - Apprenez à modifier le fichier de schéma pour la collecte et de router les données de diagnostic. Notez que vous pouvez également utiliser Visual Studio pour modifier le fichier de schéma.
- [Diagnostics de Azure comment les données sont stockées dans le stockage Azure](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - connaître les noms des tables et des objets BLOB dans lequel les données de diagnostic sont écrit.
- Apprenez à [utiliser les compteurs de Performance dans les tests de diagnostic Azure](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Apprenez à [Azure d’acheminer des informations de diagnostic aux analyses de l’Application](./azure-diagnostics-configure-applicationinsights.md)
- Si vous avez des problèmes de démarrage ou la recherche de vos données dans les tables de stockage Azure de diagnostic, consultez [Résolution des problèmes de diagnostic Azure](./azure-diagnostics-troubleshooting.md)
