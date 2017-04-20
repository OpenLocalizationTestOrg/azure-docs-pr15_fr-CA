<properties
    pageTitle="Contrôler, diagnostiquer et résoudre les problèmes de stockage | Microsoft Azure"
    description="Utiliser des fonctionnalités telles qu’analytique de stockage, l’enregistrement côté client et d’autres outils de tiers pour identifier, diagnostiquer et résoudre les problèmes liés au stockage d’Azure."
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="jahogg"/>

# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Contrôler, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure

[AZURE.INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Vue d’ensemble

Diagnostic et résolution des problèmes dans une application distribuée est hébergée dans un environnement de nuage peuvent être plus complexes que dans les environnements traditionnels. Les applications peuvent être déployées dans une infrastructure PaaS ou IaaS, sur place, sur un appareil mobile, ou dans une combinaison de ces. En règle générale, le trafic du réseau de votre application peut-être traverser les réseaux publics et privés et votre application peut utiliser plusieurs technologies de stockage telles que les Tables de stockage de Microsoft Azure, les Blobs, les files d’attente ou par exemple stocke les fichiers ainsi que les autres données relationnelles et les bases de données de document.

Pour gérer ces applications avec succès, vous devez les surveiller de manière proactive et comprendre comment diagnostiquer et résoudre les problèmes de tous les aspects de leurs technologies dépendantes et les. En tant qu’utilisateur des services de stockage Azure, vous devez en permanence surveiller les services de stockage que votre application utilise des modifications inattendues de comportement (tel qu’il est plus lent que le temps de réponse habituelle) et utiliser pour collecter des données plus détaillées et d’analyser en profondeur un problème de l’enregistrement. Les informations de diagnostic que vous obtenez à partir de la surveillance et la journalisation vous aidera à déterminer la cause du problème rencontrée par votre application. Puis, vous pouvez résoudre le problème et déterminer les mesures appropriées, que vous pouvez prendre plus. Stockage Azure est un service Azure de base et constitue une partie importante de la majorité des solutions aux clients de déployer l’infrastructure Azure. Stockage Azure inclut des fonctions pour simplifier la surveillance, de diagnostic et de résolution des problèmes de stockage dans vos applications basées sur le nuage.

> [AZURE.NOTE] Des comptes de stockage avec un type de réplication de stockage redondant Zone (ZRS) n’ont pas les métriques ou la fonction de journalisation pour l’instant. En outre, le Service de fichiers Azure ne gère pas l’enregistrement pour l’instant.

Pour un guide pratique de bout en bout pour la résolution des problèmes dans les applications de stockage Azure, consultez [Bout à bout dépannage à l’aide des indicateurs du stockage Azure et journalisation, AzCopy et l’Analyseur de Message](storage-e2e-troubleshooting.md).

+ [Introduction]
    + [L’organisation de ce guide]
+ [Surveillance de votre service de stockage]
    + [Surveillance de l’état du service]
    + [Analyse de la capacité]
    + [Analyse de la disponibilité]
    + [Analyse des performances]
+ [Diagnostic des problèmes de stockage]
    + [Problèmes de service de santé]
    + [Problèmes de performances]
    + [Diagnostic des erreurs]
    + [Problèmes d’émulateur de stockage]
    + [Outils de journalisation de stockage]
    + [À l’aide d’outils d’enregistrement de réseau]
+ [Suivi de bout en bout]
    + [Mise en corrélation des données de journal]
    + [ID de la demande client]
    + [ID du serveur de requête]
    + [Estampilles]
+ [Guide de dépannage]
    + [Affichent les mesures AverageE2ELatency haute et basse AverageServerLatency]
    + [Affichent les mesures de faible AverageE2ELatency et AverageServerLatency de faible mais que le client est confronté à une latence élevée]
    + [Afficher les mesures de haute AverageServerLatency]
    + [Vous rencontrez des retards inattendus dans la remise des messages dans une file d’attente]
    + [Mesures montrent une augmentation de PercentThrottlingError]
    + [Mesures montrent une augmentation de PercentTimeoutError]
    + [Mesures montrent une augmentation de PercentNetworkError]
    + [Le client reçoit les messages HTTP 403 (refusé)]
    + [Le client reçoit les messages HTTP 404 (introuvable)]
    + [Le client reçoit les messages HTTP 409 (conflit)]
    + [Faible PercentSuccess afficher les mesures ou les entrées de journal d’analytique avoir des opérations avec le statut de la transaction de ClientOtherErrors]
    + [Mesures de capacité affichent une augmentation inattendue de l’utilisation de capacité de stockage]
    + [Vous rencontrez des redémarrages inattendus des Machines virtuelles disposant d’un grand nombre de disques durs virtuels reliés]
    + [Votre problème découle de l’utilisation de l’émulateur de stockage de développement ou de test]
    + [Vous rencontrez des problèmes pour installer le SDK Azure pour .NET]
    + [Vous avez un autre problème avec un service de stockage]
    + [Résolution des problèmes de fichiers d’Azure avec Windows et Linux](storage-troubleshoot-file-connection-problems.md)
+ [Annexes]
    + [Appendice 1 : À l’aide de Fiddler pour capturer le trafic HTTP et HTTPS]
    + [Appendice 2 : À l’aide de Wireshark pour capturer le trafic réseau]
    + [Appendice 3 : À l’aide de Microsoft Message Analyzer pour capturer le trafic réseau]
    + [Appendice 4 : À l’aide d’Excel pour afficher les mesures et les données du journal]
    + [Appendice 5 : Analyse avec des aperçus de l’Application pour Visual Studio Team Services]

## <a name="introduction"></a>Introduction

Ce guide vous explique comment utiliser des fonctionnalités telles qu’Analytique du stockage Azure, enregistrement dans la bibliothèque cliente du stockage Azure et d’autres outils tiers à identifier, diagnostiquer et résoudre les problèmes de stockage Azure côté client les problèmes.

![][1]

*Figure 1 surveillance, Diagnostics et la résolution des problèmes*

Ce guide est destiné à être lu principalement par les développeurs de services en ligne qui utilisent des Services de stockage Azure et les professionnels de l’informatique responsables de la gestion de ces services en ligne. Les objectifs de ce guide sont les suivants :

- Pour vous aider à préserver la santé et les performances de vos comptes de stockage Azure.
- Pour vous fournir les outils pour vous aider à décider si un problème dans une application ou un problème lié au stockage Azure et les processus nécessaires.
- Pour vous fournir des instructions pour la résolution des problèmes liés au stockage Azure.

### <a name="how-this-guide-is-organized"></a>L’organisation de ce guide

La section «[surveillance de votre service de stockage]» explique comment surveiller la santé et les performances de vos services de stockage Azure à l’aide d’indicateurs d’Analytique du stockage Azure (indicateurs de stockage).

La section «[diagnostic des problèmes de stockage]» décrit comment diagnostiquer les problèmes à l’aide d’Azure stockage Analytique Logging (journalisation de stockage). Il décrit également comment activer la journalisation côté client à l’aide de l’une des bibliothèques client telles que la bibliothèque de Client de stockage pour .NET ou l’Azure SDK pour Java.

La section «[suivi de bout en bout]» décrit comment vous pouvez mettre en corrélation les informations contenues dans les différents fichiers journaux et les données de métrique.

La section «[conseils de résolution des problèmes]» fournit des conseils de dépannage pour les problèmes liés au stockage courants que vous pouvez rencontrer.

«[Annexes]» incluent des informations sur l’utilisation d’autres outils tels que Wireshark et Netmon pour analyser les données de paquet réseau, Fiddler d’analyse de messages HTTP/HTTPS et l’Analyseur de Message de Microsoft pour la mise en corrélation des données de journal.


## <a name="monitoring-your-storage-service"></a>Surveillance de votre service de stockage

Si vous êtes familiarisé avec la surveillance des performances de Windows, vous pouvez considérer des mesures de stockage comme un équivalent de stockage Azure des compteurs de l’Analyseur de performances Windows. Dans les mesures de stockage, vous trouverez un ensemble complet de mesures (compteurs dans la terminologie de l’Analyseur de performances Windows) telles que la disponibilité de service, le nombre total de demandes de service, ou le pourcentage de demandes ayant abouti au service. Pour obtenir une liste complète des mesures disponibles, consultez le [Schéma de la Table Analytique métriques de stockage](http://msdn.microsoft.com/library/azure/hh343264.aspx). Vous pouvez spécifier si vous souhaitez que le service stockage collectent et regroupent les métriques de toutes les heures ou toutes les minutes. Pour plus d’informations sur l’activation des mesures et de surveiller vos comptes de stockage, voir [activation des indicateurs de stockage et l’affichage des données de métrique](http://go.microsoft.com/fwlink/?LinkId=510865).

Vous pouvez choisir les mesures de toutes les heures que vous souhaitez afficher dans le [Portail Azure](https://portal.azure.com) et de configurer des règles qui avertissent les administrateurs par e-mail chaque fois qu’une mesure horaire dépasse un seuil particulier. Pour plus d’informations, reportez-vous à [Recevoir des Notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). 

Le service de stockage collecte des mesures à l’aide d’un maximum d’efforts, mais ne peut pas enregistrer chaque opération de stockage.

Dans le portail d’Azure, vous pouvez afficher les mesures de disponibilité, nombre total de demandes et de numéros de latence moyenne pour un compte de stockage. Une règle de notification a également été configurée pour alerter un administrateur si la disponibilité est inférieur à un certain niveau. À partir de l’affichage de ces données, une zone possible pour l’enquête est le pourcentage de réussite de service de table est inférieure à 100 % (pour plus d’informations, reportez-vous à la section «[faible PercentSuccess afficher les mesures ou les entrées de journal analytique comporter des opérations avec statut de transaction des ClientOtherErrors]»).

Vous devez en permanence surveiller vos applications Azure pour vous assurer qu’ils sont en bon état et réaliser comme prévu par :

- Établir des métriques de base pour une application qui vous permet de comparer les données actuelles et d’identifier les modifications importantes dans le comportement de votre application et de stockage Azure. Les valeurs de vos mesures de base sera, dans de nombreux cas, spécifique à l’application et vous devez établir les lorsque vous testez les performances de votre application.
- Enregistrement des métriques de minute et de les utiliser pour surveiller activement des erreurs inattendues et des anomalies telles que des pics d’activité d’erreur compte ou taux de demandes.
- Enregistrement des mesures toutes les heures et les utiliser pour surveiller les valeurs moyennes telles que moyenne des décomptes d’erreurs et taux de demandes.
- Rechercher les problèmes potentiels à l’aide des outils de diagnostic comme expliqué plus loin dans la section «[diagnostic des problèmes de stockage]. »

Les graphiques de la Figure 3 ci-dessous illustrent comment le calcul de la moyenne qui se produit pour les mesures horaires peut masquer pics dans l’activité. Les métriques de toutes les heures s’affichent pour afficher une vitesse constante de demandes, tout en la minute mesures font apparaître les variations qui sont réellement en cours.

![][3]

Le reste de cette section décrit les mesures que vous devez surveiller et pourquoi.

### <a name="monitoring-service-health"></a>Surveillance de l’état du service

Vous pouvez utiliser le [Portail Azure](https://portal.azure.com) pour afficher l’état de santé du service stockage (et d’autres services Azure) dans toutes les régions Azure dans le monde entier. Cela vous permet de voir immédiatement si un problème hors de votre contrôle affecte le service de stockage dans la région que vous utilisez pour votre application.

Le [Portail Azure](https://portal.azure.com) peut également fournir des notifications d’incidents qui affectent les différents services Azure.
Remarque : Ces informations étaient précédemment disponibles, ainsi que des données historiques sur le [Tableau de bord de Service Azure](http://status.azure.com).

Alors que le [Portail Azure](https://portal.azure.com) recueille des informations d’intégrité dans les centres de données Azure (surveillance intérieur-extérieur), vous pouvez également envisager d’adopter une approche extérieur-intérieur pour générer des transactions synthétiques qui accèdent régulièrement à votre application web hébergé Azure à partir de plusieurs emplacements. Les services offerts par les [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) et les perspectives d’Application pour Visual Studio Team Services sont des exemples de cette approche extérieur-intérieur. Pour plus d’informations sur les aspects de l’Application pour Visual Studio Team Services, consultez l’annexe «[l’appendice 5 : surveillance à l’aide des informations d’Application pour Visual Studio Team Services](#appendix-5). »

### <a name="monitoring-capacity"></a>Analyse de la capacité

Indicateurs de stockage ne stocke que des mesures de capacité pour le service d’objet blob parce que BLOB en général la plus grande proportion de données stockées (au moment de la rédaction, il n’est pas possible d’utiliser des indicateurs de stockage pour analyser la capacité de vos tables et les files d’attente). Si vous avez activé l’analyse pour le service d’objet Blob, vous pouvez trouver ces données dans la table **$MetricsCapacityBlob** . Indicateurs de stockage enregistrent ces données une fois par jour, et vous pouvez utiliser la valeur de la **RowKey** pour déterminer si la ligne contient une entité qui est lié à des données de l’utilisateur ( **données**de la valeur) ou analytique (valeur **analytique**). Chaque entité stockée contient des informations sur la quantité de stockage utilisée (**capacité** exprimée en octets) et le nombre de conteneurs (**ContainerCount**) et les objets BLOB (**ObjectCount**) en cours d’utilisation dans le compte de stockage. Pour plus d’informations sur les mesures de capacité stockées dans la table **$MetricsCapacityBlob** , consultez le [Schéma de la Table Analytique métriques de stockage](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [AZURE.NOTE] Vous devez surveiller ces valeurs pour un avertissement que vous approchez de votre compte de stockage, les limites de capacité. Dans le portail d’Azure, vous pouvez ajouter des règles d’alerte pour vous avertir si l’utilisation du stockage d’agrégation dépasse ou tombe en dessous des seuils que vous spécifiez.

Pour estimer la taille de plusieurs objets de stockage tels que les objets BLOB de l’aide, consultez le blog [Compréhension Azure stockage facturation – bande passante, Transactions et capacité](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Analyse de la disponibilité

Vous devez surveiller la disponibilité des services de stockage dans votre compte de stockage en surveillant la valeur dans la colonne **disponibilité** dans les tables de mesures toutes les heures ou les minutes, **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. La colonne **disponibilité** indique une valeur en pourcentage qui indique la disponibilité du service ou de l’opération d’API représenté par la ligne (l’illustre **RowKey** si la ligne contient des mesures pour le service dans son ensemble ou pour une opération d’API spécifique).

Toute valeur inférieure à 100 % indique que certaines demandes de stockage sont défectueux. Vous pouvez voir pourquoi ils échouent en examinant les autres colonnes dans les données de mesures qui indiquent le nombre de demandes avec différents types d’erreurs, tels que **ServerTimeoutError**. Vous devez vous attendre à voir **disponibilité** inférieurs temporairement 100 % pour des raisons de délais du serveur temporaire si le service déplace les partitions à demande de mieux équilibrer la charge ; la logique de nouvelle tentative dans votre application cliente doit gérer ces conditions par intermittence. Le [stockage Analytique connecté les opérations et les Messages d’état](http://msdn.microsoft.com/library/azure/hh343260.aspx) répertorie les types de transactions, des indicateurs de stockage inclut dans son calcul de **disponibilité** .

Dans le [Portail Azure](https://portal.azure.com), vous pouvez ajouter des règles d’alerte pour vous avertir de la **disponibilité** d’un service est inférieur à un seuil que vous spécifiez.

La section «[Guide de résolution des problèmes]» de ce guide décrit certains problèmes de service de stockage courantes relatives à la disponibilité.

### <a name="monitoring-performance"></a>Analyse des performances

Pour surveiller les performances des services de stockage, vous pouvez utiliser les métriques suivantes dans les tables de mesures toutes les heures et les minutes.

- Les valeurs dans les colonnes **AverageE2ELatency** et de **AverageServerLatency** affichent la durée moyenne du service de stockage ou le type d’opération API prend pour traiter les requêtes. **AverageE2ELatency** est une mesure de la latence de bout en bout qui inclut le temps de lire la demande et envoyer une réponse ainsi que le temps nécessaire pour traiter la demande (par conséquent inclut la latence du réseau lorsque la requête atteint le service de stockage) ; **AverageServerLatency** est une mesure de la durée du traitement et par conséquent exclut toute latence réseau liée à la communication avec le client. Voir la section «[affichent les mesures AverageE2ELatency élevé et AverageServerLatency bas]» plus loin dans ce guide pour savoir pourquoi il peut y avoir une différence importante entre ces deux valeurs.
- Les valeurs dans les colonnes **TotalIngress** et **TotalEgress** indiquent le volume total des données, en octets, entrant et allez sur votre service de stockage ou à un type spécifique d’opération API.
- Les valeurs dans la colonne de **TotalRequests** affichent le nombre total de demandes qui reçoit le service de stockage de l’opération sur l’API. **TotalRequests** est le nombre total de demandes que le service de stockage reçoit.

En règle générale, vous allez surveiller des changements inattendus dans une de ces valeurs en tant qu’indicateur que vous avez un problème nécessitant une enquête.

Dans le [Portail Azure](https://portal.azure.com), vous pouvez ajouter des règles d’alerte pour vous avertir le cas échéant, des mesures de performance pour ce service ne passe en dessous ou dépassent un seuil que vous spécifiez.

La section «[Guide de résolution des problèmes]» de ce guide décrit certains problèmes de service de stockage courants liés aux performances.


## <a name="diagnosing-storage-issues"></a>Diagnostic des problèmes de stockage

Il existe un certain nombre de façons peut prendre connaissance d’un problème ou un problème dans votre application, notamment :

- Une défaillance majeure qui entraîne l’application à se bloquer ou cesser de fonctionner.
- Des modifications significatives à partir des valeurs de planification initiale dans les mesures vous surveillez comme décrit dans la section précédente, «[surveillance de votre service de stockage]. »
- Les rapports sur les utilisateurs de votre application qu’une opération particulière n’est pas terminé comme prévu ou que certaines fonctionnalités ne fonctionnent pas.
- Les erreurs générées dans votre application qui apparaissent dans les fichiers journaux ou une autre méthode de notification.

En général, les problèmes liés aux services de stockage Azure se répartissent en quatre grandes catégories :

- Votre application présente un problème de performances, signalés par les utilisateurs, soit révélé par des changements dans les mesures de performances.
- Il existe un problème avec l’infrastructure de stockage Azure dans une ou plusieurs régions.
- Votre application rencontre une erreur, signalés par les utilisateurs, soit révélée par une augmentation de l’une des mesures de comptage erreur que vous surveillez.
- Lors du développement et de test, vous pouvez utiliser l’émulateur de stockage local ; Vous pouvez rencontrer certains problèmes qui se rapportent spécifiquement à l’utilisation de l’émulateur de stockage.

Les sections suivantes décrivent les étapes à suivre pour diagnostiquer et résoudre les problèmes de chacune de ces quatre catégories. La section «[Guide de résolution des problèmes]» plus loin dans ce guide fournit plus de détails sur certains problèmes courants que vous pouvez rencontrer.

### <a name="service-health-issues"></a>Problèmes de service de santé

Problèmes de service de santé sont généralement hors de votre contrôle. Le [Portail Azure](https://portal.azure.com) fournit des informations sur des problèmes en cours avec les services Azure, y compris les services de stockage. Si vous avez choisi pour le stockage redondant géo-accès en lecture lorsque vous avez créé votre compte de stockage, puis en cas de vos données n’est pas disponible dans l’emplacement principal, votre application peut passer momentanément à la copie en lecture seule dans l’emplacement secondaire. Pour ce faire, votre application doit être en mesure de basculer entre les emplacements de stockage principal et secondaire et être en mesure de fonctionner dans un mode à fonctionnalité réduite avec des données en lecture seule. Les bibliothèques clientes de stockage Azure permettent de définir une stratégie de nouvelles tentatives que peut lire à partir du stockage secondaire en cas d’échec d’une lecture à partir du stockage principal. Votre application doit également savoir que les données dans l’emplacement secondaire sont finalement cohérentes. Pour plus d’informations, consultez le blog [Options de redondance de stockage Azure et stockage redondant géographique de l’accès en lecture](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problèmes de performances

Les performances d’une application peuvent être subjective, surtout du point de vue utilisateur. Par conséquent, il est important de disposer de métriques de base disponibles pour vous aider à identifier dans le cas où il y a peut-être un problème de performances. De nombreux facteurs peuvent affecter les performances d’un service de stockage Azure à partir du point de vue de l’application client. Ces facteurs peuvent fonctionner dans le service de stockage, dans le client ou dans l’infrastructure réseau ; Par conséquent, il est important de disposer d’une stratégie permettant d’identifier l’origine du problème de performances.

Après avoir identifié l’emplacement probable de la cause du problème de performances à partir de la mesure, vous pouvez ensuite utiliser les fichiers journaux pour trouver des informations détaillées pour diagnostiquer et résoudre le problème.

La section «[conseils de résolution des problèmes]» plus loin dans ce guide fournit plus d’informations sur certaines des performances courants liés problèmes que vous pouvez rencontrer.

### <a name="diagnosing-errors"></a>Diagnostic des erreurs

Les utilisateurs de votre application peuvent vous avertir des erreurs signalées par l’application cliente. Indicateurs de stockage enregistre également le nombre de différents types d’erreurs à partir de vos services de stockage, tels que **NetworkError**, **ClientTimeoutError**ou **AuthorizationError**. Alors que les mesures de stockage n’enregistre que nombre de différents types d’erreurs, vous pouvez obtenir plus de détails sur les demandes individuelles en examinant les journaux du réseau côté serveur et côté client. En général, le code d’état HTTP retourné par le service de stockage donne une indication de la raison pour laquelle la demande a échoué.

> [AZURE.NOTE] Gardez à l’esprit que vous devriez voir des erreurs intermittentes : par exemple, les erreurs dues à des conditions de réseau temporaires, ou les erreurs d’application.

Les ressources suivantes sont utiles pour comprendre les codes d’état et d’erreur liés au stockage :

- [Codes d’erreur de l’API reste courants](http://msdn.microsoft.com/library/azure/dd179357.aspx)
- [Codes d’erreur de Service de BLOB](http://msdn.microsoft.com/library/azure/dd179439.aspx)
- [Codes d’erreur de Service de file d’attente](http://msdn.microsoft.com/library/azure/dd179446.aspx)
- [Codes d’erreur de Service de table](http://msdn.microsoft.com/library/azure/dd179438.aspx)
- [Codes d’erreur de Service de fichiers](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problèmes d’émulateur de stockage

Le Kit de développement Azure comprend un émulateur de stockage que vous pouvez exécuter sur une station de travail de développement. Cet émulateur simule la plupart du comportement des services de stockage Azure et est utile lors du développement et de test, ce qui vous permet d’exécuter des applications qui utilisent les services de stockage Azure sans la nécessité d’un abonnement Azure et un compte de stockage Azure.

La section «[Guide de résolution des problèmes]» de ce guide décrit certains problèmes courants rencontrés à l’aide de l’émulateur de stockage.

### <a name="storage-logging-tools"></a>Outils de journalisation de stockage

Enregistrement de stockage fournit un enregistrement côté serveur des demandes de stockage de votre compte de stockage Azure. Pour plus d’informations sur la façon d’activer la journalisation du côté serveur et d’accéder aux données du journal, consultez [activation de la journalisation du stockage et l’accès à des données de journal](http://go.microsoft.com/fwlink/?LinkId=510867).

La bibliothèque cliente de stockage pour .NET vous permet de collecter des données de journal côté client qui se rapportent aux opérations de stockage effectuées par votre application. Pour plus d’informations, consultez [ouverture de côté Client avec la bibliothèque cliente du stockage .NET](http://go.microsoft.com/fwlink/?LinkId=510868).

> [AZURE.NOTE] Dans certaines circonstances (par exemple, les échecs d’autorisation de SAS), un utilisateur peut signaler une erreur pour laquelle vous ne pouvez trouver aucune donnée de demande dans les journaux de stockage côté serveur. Vous pouvez utiliser les fonctions d’enregistrement de la bibliothèque de Client de stockage pour déterminer si la cause du problème se trouve sur le client ou utiliser des outils de surveillance réseau pour analyser le réseau.

### <a name="using-network-logging-tools"></a>À l’aide d’outils d’enregistrement de réseau

Vous pouvez capturer le trafic entre le client et le serveur pour fournir des informations détaillées sur les données que le client et le serveur échangent et les conditions de réseau sous-jacent. Outils de journalisation utiles du réseau sont les suivantes :

- [Fiddler](http://www.telerik.com/fiddler) est un site web gratuit qui vous permet d’examiner les en-têtes et les données de charge utile des messages de demande et de réponse HTTP et HTTPS proxy de débogage. Pour plus d’informations, consultez [l’appendice 1 : à l’aide de Fiddler pour capturer le trafic HTTP et HTTPS](#appendix-1).
- [Microsoft Network Monitor (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) et [Wireshark](http://www.wireshark.org/) sont les analyseurs de protocole de réseau libre qui vous permettent d’afficher des informations détaillées de paquets pour un large éventail de protocoles réseau. Pour plus d’informations sur Wireshark, consultez «[l’appendice 2 : à l’aide de Wireshark pour capturer le trafic réseau](#appendix-2)».
- Analyseur de Message de Microsoft est un outil de Microsoft qui remplace Netmon et qui en plus de la capture des données de paquet réseau, vous permet d’afficher et d’analyser les données de journal capturées à partir d’autres outils. Pour plus d’informations, consultez «[l’appendice 3 : à l’aide de Microsoft Message Analyzer pour capturer le trafic réseau](#appendix-3)».
- Si vous souhaitez effectuer un test de la connectivité de base pour vérifier que votre ordinateur client peut se connecter au service de stockage Azure via le réseau, vous ne pouvez pas cela à l’aide de l’outil standard **ping** sur le client. Toutefois, vous pouvez utiliser l' [outil de **tcping** ](http://www.elifulkerson.com/projects/tcping.php) pour vérifier la connectivité.

Dans de nombreux cas, les données du journal à partir de l’enregistrement de stockage et de la bibliothèque de Client de stockage sera suffisantes pour diagnostiquer un problème, mais dans certains scénarios, vous devrez peut-être des informations plus détaillées que ces outils de connexion réseau peuvent fournir. Par exemple, pour afficher les messages HTTP et HTTPS à l’aide de Fiddler permet de vous permet d’afficher l’en-tête et la charge utile des données envoyées vers et depuis les services de stockage, ce qui vous permettrait d’examiner comment une application cliente tente de renvoyer des opérations de stockage. Analyseurs de protocole tel que Wireshark fonctionnent au niveau du paquet, ce qui vous permet d’afficher des données TCP, qui vous permet de résoudre les problèmes de connectivité et de la perte de paquets. Analyseur de message peut fonctionner à des couches à la fois HTTP et TCP.

## <a name="end-to-end-tracing"></a>Suivi de bout en bout

Suivi de bout en bout à l’aide d’une variété de fichiers journaux est une technique utile pour rechercher les problèmes potentiels. Vous pouvez utiliser les informations de date/heure à partir de vos données métriques comme une indication de l’endroit où commencer à rechercher dans les fichiers journaux pour les informations détaillées qui vous aideront à résoudre le problème.

### <a name="correlating-log-data"></a>Mise en corrélation des données de journal

Trace de réseau lors de l’affichage de journaux à partir d’applications clientes, et il est essentiel de pouvoir faire correspondre l’enregistrement côté serveur, stockage demande sur les différents fichiers journaux. Les fichiers journaux comprennent un certain nombre de différents champs qui sont utiles en tant qu’identificateurs de corrélation. L’id de demande du client est le champ plus utile à utiliser pour mettre en corrélation les entrées dans les différents journaux. Toutefois dans certains cas, il peut être utile d’utiliser l’id de demande de serveur ou les estampilles. Les sections suivantes fournissent plus de détails sur ces options.

### <a name="client-request-id"></a>ID de la demande client

La bibliothèque cliente de stockage génère automatiquement un id de demande client unique pour chaque demande.

- Dans le journal côté client qui crée de la bibliothèque de Client de stockage, l’id de la demande client s’affiche dans le champ **ID de la demande Client** de chaque entrée de journal relatif à la demande.
- Sur une trace réseau, par exemple une capture par Fiddler, l’id de la demande client est visible dans les messages de requête en tant que valeur de l’en-tête **x-ms-client--id de demande** HTTP.
- Dans le journal de l’enregistrement de stockage côté serveur, l’id de la demande client s’affiche dans la colonne d’ID de demande Client.

> [AZURE.NOTE]Il est possible que plusieurs demandes de partager le même id de demande client car le client peut affecter cette valeur (bien que la bibliothèque cliente de stockage affecte une nouvelle valeur automatiquement). Dans le cas des tentatives du client, toutes les tentatives de partagent le même id de demande du client. Dans le cas d’un lot envoyé par le client, le lot présente l’id d’une demande client unique.


### <a name="server-request-id"></a>ID du serveur de requête

Le service de stockage génère automatiquement un ID de demande de serveur.

- Dans le journal de l’enregistrement de stockage côté serveur, l’id de demande de serveur s’affiche à la colonne **d’en-tête de l’ID de la demande** .
- Sur une trace réseau, par exemple une capture par Fiddler, l’id de demande de serveur s’affiche dans les messages de réponse en tant que valeur de l’en-tête **x-ms--id de la demande** HTTP.
- Dans le journal côté client qui crée de la bibliothèque de Client de stockage, l’id de demande de serveur s’affiche dans la colonne de **Texte de l’opération** pour l’entrée de journal indiquant les détails de la réponse du serveur.

> [AZURE.NOTE] Le service de stockage toujours affecte un serveur unique id de demande pour toutes les requêtes qu’il reçoit, afin que chaque nouvelle tentative du client et chaque opération inclus dans un lot possède un id de demande de serveur unique.

Si la bibliothèque Client de stockage lève une **StorageException** dans le client, la propriété **RequestInformation** contient un objet **RequestResult** qui contient une propriété **ServiceRequestID%%** . Vous pouvez également accéder à un objet **RequestResult** à partir d’une instance de **OperationContext** .

L’exemple de code ci-dessous montre comment définir une valeur **ClientRequestId** personnalisée en attachant un objet **OperationContext** la demande au service de stockage. Il indique également comment récupérer la valeur de **ServerRequestId** dans le message de réponse.

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="timestamps"></a>Estampilles

Vous pouvez également utiliser les horodatages pour localiser les entrées du journal associées, mais soyez prudent de tout décalage horaire entre le client et le serveur qui peut-être exister. Vous devez rechercher plus ou moins 15 minutes pour la correspondance des entrées côté serveur en fonction de l’horodatage sur le client. Gardez à l’esprit que les métadonnées de l’objet blob pour les objets BLOB qui contiennent les métriques indiquent la plage de temps pour les métriques stockées dans l’objet blob ; Cela est utile si vous avez de nombreux objets BLOB de mesures pour la même heure ou de minute.

## <a name="troubleshooting-guidance"></a>Guide de dépannage

Cette section vous aidera au diagnostic et la résolution des problèmes de certains des problèmes courants de votre application peuvent rencontrer lorsque vous utilisez les services de stockage Azure. Utilisez la liste ci-dessous pour trouver les informations relatives à votre problème spécifique.

**Arbre de décision de dépannage**

----------

Votre problème est-il relié à l’exécution de l’un des services de stockage ?

- [Affichent les mesures AverageE2ELatency haute et basse AverageServerLatency]
- [Affichent les mesures de faible AverageE2ELatency et AverageServerLatency de faible mais que le client est confronté à une latence élevée]
- [Afficher les mesures de haute AverageServerLatency]
- [Vous rencontrez des retards inattendus dans la remise des messages dans une file d’attente]

----------

Votre problème est-il relié à la disponibilité de l’un des services de stockage ?

- [Mesures montrent une augmentation de PercentThrottlingError]
- [Mesures montrent une augmentation de PercentTimeoutError]
- [Mesures montrent une augmentation de PercentNetworkError]

----------

Votre application client reçoit une réponse HTTP en 4XX (par exemple, 404) à partir d’un service de stockage ?

- [Le client reçoit les messages HTTP 403 (refusé)]
- [Le client reçoit les messages HTTP 404 (introuvable)]
- [Le client reçoit les messages HTTP 409 (conflit)]

----------

[Faible PercentSuccess afficher les mesures ou les entrées de journal d’analytique avoir des opérations avec le statut de la transaction de ClientOtherErrors]

----------

[Mesures de capacité affichent une augmentation inattendue de l’utilisation de capacité de stockage]

----------

[Vous rencontrez des redémarrages inattendus des Machines virtuelles disposant d’un grand nombre de disques durs virtuels reliés]

----------

[Votre problème découle de l’utilisation de l’émulateur de stockage de développement ou de test]

----------

[Vous rencontrez des problèmes pour installer le SDK Azure pour .NET]

----------

[Vous avez un autre problème avec un service de stockage]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Affichent les mesures AverageE2ELatency haute et basse AverageServerLatency

L’illustration ci-dessous à partir du [Portail Azure](https://portal.azure.com) , outil de surveillance montre où **AverageE2ELatency** est sensiblement plus élevés que **AverageServerLatency**.

![][4]

Notez que le service de stockage seulement calcule la mesure **AverageE2ELatency** de demandes réussies et, contrairement à **AverageServerLatency**, comprend le temps que le client accepte d’envoyer les données et de recevoir un accusé de réception à partir du service de stockage. Par conséquent, une différence entre **AverageE2ELatency** et **AverageServerLatency** peut être due à l’application cliente est lent à répondre, soit en raison de conditions sur le réseau.

> [AZURE.NOTE] Vous pouvez également afficher des **E2ELatency** et des **ServerLatency** pour les opérations de stockage individuels dans les données du journal enregistrement de stockage.

#### <a name="investigating-client-performance-issues"></a>Étude des problèmes de performances client

Pour le client répond lentement causes possibles ayant un nombre limité de threads ou de connexions disponibles, ou faible sur les ressources de la bande passante de processeur, de mémoire ou le réseau. Vous serez peut-être en mesure de résoudre le problème en modifiant le code client pour être plus efficace (par exemple à l’aide des appels asynchrones au service de stockage), ou à l’aide d’une Machine virtuelle de grande taille (avec plus de cœurs et plus de mémoire).

Pour les services de table et de la file d’attente, l’algorithme de Nagle empêche également haute **AverageE2ELatency** par rapport à **AverageServerLatency**: pour plus d’informations, consultez la publication [algorithme de Nagle est pas convivial aux petites requêtes](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Vous pouvez désactiver l’algorithme Nagle dans le code à l’aide de la classe **ServicePointManager** dans l’espace de noms **System.Net** . Vous devez faire cela avant de procéder à tous les appels à la table ou les services de file d’attente dans votre application, dans la mesure où cela n’affecte pas les connexions qui sont déjà ouverts. L’exemple suivant provient de la méthode **Application_Start** dans un rôle worker.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Vous devez vérifier les journaux côté client pour afficher le nombre de requêtes que l’envoi de votre application cliente et .NET générales concernant les goulots d’étranglement dans votre client de processeur, le garbage collection .NET, l’utilisation du réseau ou de mémoire. Comme point de départ pour le dépannage des applications clientes .NET, consultez [débogage, le traçage et le profilage](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Étude des problèmes de latence réseau

En général, la latence élevée de bout en bout a provoqué par le réseau est en raison de conditions temporaires. Vous pouvez étudier les deux problèmes de réseau temporaires et permanents, tels que la perte de paquets à l’aide d’outils tels que Wireshark ou l’Analyseur de Message de Microsoft.

Pour plus d’informations sur l’utilisation de Wireshark pour résoudre les problèmes de réseau, consultez «[l’appendice 2 : pour capturer le trafic réseau à l’aide de Wireshark]. »

Pour plus d’informations sur l’Analyseur de Message de Microsoft pour résoudre les problèmes de réseau, reportez-vous à la section «[l’appendice 3 : pour capturer le trafic réseau à l’aide de Microsoft Message Analyzer]. »

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Affichent les mesures de faible AverageE2ELatency et AverageServerLatency de faible mais que le client est confronté à une latence élevée

Dans ce scénario, la cause la plus probable est un retard dans les demandes de stockage atteignant le service de stockage. Vous devez rechercher pourquoi les demandes du client ne sont pas à l’aide du blob service.

Une des raisons possibles pour retarder l’envoi de demandes de client sont qu’il existe un nombre limité de connexions disponibles ou les threads.

Vous devez également vérifier si le client effectue plusieurs tentatives et étudier la raison pour laquelle, si c’est le cas. Pour déterminer si le client effectue plusieurs tentatives, vous pouvez :

- Examinez les journaux de stockage Analytique. Si plusieurs tentatives sont produisent, vous verrez plusieurs opérations avec le même ID de demande du client, mais avec la demande de l’autre serveur ID.
- Examinez les journaux du client. La journalisation détaillée indique qu’une nouvelle tentative s’est produite.
- Déboguer votre code et vérifiez les propriétés de l’objet **OperationContext** associé à la demande. Si l’opération a retenté, la propriété **RequestResults** inclut plusieurs demande de serveur unique ID. Vous pouvez également vérifier les heures de début et de fin pour chaque demande. Pour plus d’informations, consultez l’exemple de code dans la section [ID de demande de serveur].

S’il n’y a pas de problèmes dans le client, vous devez analyser les problèmes réseau potentiels, tels que la perte de paquets. Vous pouvez utiliser des outils tels que Wireshark ou Microsoft Message Analyzer pour étudier des problèmes de réseau.

Pour plus d’informations sur l’utilisation de Wireshark pour résoudre les problèmes de réseau, consultez «[l’appendice 2 : pour capturer le trafic réseau à l’aide de Wireshark]. »

Pour plus d’informations sur l’Analyseur de Message de Microsoft pour résoudre les problèmes de réseau, reportez-vous à la section «[l’appendice 3 : pour capturer le trafic réseau à l’aide de Microsoft Message Analyzer]. »

### <a name="metrics-show-high-AverageServerLatency"></a>Afficher les mesures de haute AverageServerLatency

Dans le cas de haute **AverageServerLatency** pour les demandes de téléchargement de blob, vous devez utiliser les journaux de journalisation du stockage pour voir s’il existe des demandes répétées pour le même objet blob (ou un ensemble d’objets BLOB). Demandes de téléchargement pour les blob, vous devez examiner le bloc utilise la taille du client (par exemple, bloque moins de 64 Ko peut entraîner de frais généraux à moins que les lectures sont également moins de 64K segments), et si plusieurs clients sont téléchargement des blocs sur le même objet blob en parallèle. Vous devez également vérifier les mesures par minute de pics dans le nombre de demandes d’entraîner le dépassement par seconde cibles d’évolutivité : Voir aussi «[une augmentation de PercentTimeoutError afficher les métriques]».

Si vous voyez de haute **AverageServerLatency** de blob télécharger les demandes lorsqu’il sont a des demandes répétées même blob ou l’ensemble des objets BLOB, vous devez envisager la mise en cache de ces objets BLOB à l’aide d’Azure Cache ou le réseau de livraison de contenu (CDN) d’Azure. Pour les demandes de téléchargement, vous pouvez améliorer le débit en utilisant une plus grande taille de bloc. Pour les requêtes à des tables, il est également possible d’implémenter la mise en cache du côté client sur les clients qui effectuent les mêmes opérations de requête et où les données ne changent fréquemment.

Des valeurs élevées **AverageServerLatency** peuvent également être un symptôme de tables mal conçues ou les requêtes que le résultat dans les opérations d’analyse ou qui suivent l’anti-modèle ajouter/ajouter. Pour plus d’informations, consultez «[mesures afficher une augmentation de PercentThrottlingError]».

> [AZURE.NOTE] Vous trouverez ici une liste des performances de liste de contrôle complète : [Microsoft Azure Storage Performance et l’évolutivité de liste de contrôle](storage-performance-checklist.md).

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Vous rencontrez des retards inattendus dans la remise des messages dans une file d’attente

Si vous rencontrez un délai entre le moment qu'une application ajoute un message à une file d’attente et le temps, qu'il devient disponible pour lire à partir de la file d’attente, vous devez alors les étapes suivantes pour diagnostiquer le problème :

- Vérifiez que l’application est ajouté avec succès les messages à la file d’attente. Vérifiez que l’application est ne pas réessayer la méthode **AddMessage** plusieurs fois avant de réussir. Les journaux de la bibliothèque de Client de stockage affichera les tentatives répétées d’opérations de stockage.
- Vérifiez qu’il n’y a aucune horloge maximal autorisé entre le rôle de collaborateur qui ajoute le message à la file d’attente et le rôle de collaborateur qui lit le message de la file d’attente qui s’affichent comme s’il existe un retard dans le traitement.
- Vérifiez si le rôle de collaborateur qui lit les messages de la file d’attente est défectueux. Si un client de la file d’attente appelle la méthode **GetMessage** mais ne répond pas avec un accusé de réception, le message reste invisible sur la file d’attente jusqu'à l’expiration de la période **d’invisibilityTimeout** . À ce stade, le message devient disponible pour le traitement de nouveau.
- Vérifiez si la longueur de la file d’attente augmente au fil du temps. Cela peut se produire si vous n’avez pas suffisamment travailleurs disponibles pour traiter tous les messages autres travailleurs sont mise dans la file d’attente. Vous devez également vérifier les métriques pour voir si les requêtes de delete sont répétées défectueux et le nombre de dequeue sur les messages, ce qui peut indiquer des tentatives pour supprimer le message.
- Examinez les journaux de journalisation de stockage pour toutes les opérations de file d’attente sont supérieur à celui des valeurs attendues de **E2ELatency** et de **ServerLatency** sur une longue période de temps que d’habitude.


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Mesures montrent une augmentation de PercentThrottlingError

Limitation des erreurs se produisent lorsque vous dépassez les cibles de l’évolutivité d’un service de stockage. Le service de stockage pour cela pour vous assurer qu’aucun client unique ou le locataire ne peut utiliser le service au détriment des autres. Pour plus d’informations, consultez [cibles de performances et de l’évolutivité du stockage Azure](storage-scalability-targets.md) pour plus d’informations sur les cibles de l’évolutivité pour les comptes de stockage et des cibles de performances pour des partitions dans les comptes de stockage.

Si la métrique de **PercentThrottlingError** affiche une augmentation du pourcentage de demandes qui ont échoué avec une erreur de limitation, vous devez examiner un des deux scénarios :

- [Augmentation temporaire des PercentThrottlingError]
- [Augmentation permanente dans l’erreur de PercentThrottlingError]

Une augmentation de **PercentThrottlingError** se produit souvent en même temps qu’une augmentation du nombre de demandes de stockage, ou lorsque vous avez initialement votre application de test de charge. Cela peut également se manifester dans le client en tant que « 503 serveur occupé » ou des messages d’état « 500 opération Timeout » HTTP à partir d’opérations de stockage.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Augmentation temporaire des PercentThrottlingError

Si vous ne voyez pas les pics de la valeur de **PercentThrottlingError** qui coïncident avec des périodes de forte activité de l’application, vous devez implémenter une exponentielle (non linéaire) d’interruption stratégie de nouvelles tentatives dans votre client : cela à votre application afin de lisser les pointes de trafic et réduire la charge immédiate sur la partition. Pour plus d’informations sur la mise en œuvre des stratégies de nouvelle tentative à l’aide de la bibliothèque Client de stockage, reportez-vous à la section [Microsoft.WindowsAzure.Storage.RetryPolicies Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [AZURE.NOTE] Vous pouvez également voir des pics qui ne coïncident pas avec des périodes de forte activité pour l’application de la valeur de **PercentThrottlingError** : la cause la plus probable est le service de stockage de déplacer des partitions pour améliorer l’équilibrage de charge.

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Augmentation permanente dans l’erreur de PercentThrottlingError

Si vous constatez une valeur constamment élevée pour **PercentThrottlingError** après une augmentation permanente de vos volumes de transactions, ou lorsque vous effectuez le chargement initial des tests sur votre application, puis vous devez évaluer la manière dont votre application utilise les partitions de stockage, et il arrive que les cibles de l’évolutivité d’un compte de stockage. Par exemple, si vous voyez la limitation des erreurs sur une file d’attente (qui est considérée comme une partition unique), puis vous tenez à l’aide de files d’attente supplémentaires afin de répartir les transactions en plusieurs partitions. Si vous voyez la limitation des erreurs dans une table, vous devez envisager d’utiliser un modèle de partitionnement différent afin de répartir vos transactions en plusieurs partitions à l’aide d’un plus grand nombre de valeurs de clé de partition. Une cause fréquente de ce problème est le prepend/Ajout anti-modèle où vous permet de sélectionner la date en tant que clé de partition et ensuite toutes les données sur un jour donné est écrit sur une partition : sous charge, cela peut provoquer un goulot d’étranglement de l’écriture. Vous devez prendre en compte d’un autre modèle de partitionnement ou évaluer si l’utilisation de stockage des objets blob peut être une meilleure solution. Vous devez également vérifier si la limitation se produit à la suite de pointes dans le trafic et étudier les façons de lissage de votre modèle de demandes.

Si vous distribuez vos transactions sur plusieurs partitions, vous devez toutefois faire connaître les limites de l’évolutivité définie pour le compte de stockage. Par exemple, si vous avez utilisé les files d’attente de dix à chaque traitement de la valeur maximale de 2 000 messages de 1 Ko par seconde, vous serez à la limite globale de 20 000 messages par seconde pour le compte de stockage. Si vous avez besoin traiter des entités de plus de 20 000 par seconde, vous devez envisager d’utiliser plusieurs comptes de stockage. Vous devez également garder à l’esprit que la taille de vos demandes et les entités a une incidence sur la lorsque le service de stockage limite vos clients : Si vous avez des entités et des requêtes plus grande, vous pouvez être limitée plus tôt.

Création de requête inefficace peut également provoquer vous atteint les limites de l’évolutivité pour les partitions de table. Par exemple, une requête avec un filtre qui sélectionne uniquement un pour cent des entités dans une partition mais qui analyse toutes les entités dans une partition devez accéder à chaque entité. Chaque entité lire comptera dans le nombre total de transactions dans cette partition ; Par conséquent, vous pouvez facilement atteindre les cibles de l’évolutivité.

> [AZURE.NOTE] Vos tests de performances de doit faire apparaître aucun modèle de requête inefficace dans votre application.

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Mesures montrent une augmentation de PercentTimeoutError

Vos mesures montrent une augmentation dans **PercentTimeoutError** pour l’un de vos services de stockage. Dans le même temps, le client reçoit un volume important de messages d’état « 500 opération Timeout » HTTP à partir d’opérations de stockage.

> [AZURE.NOTE] Vous pouvez voir des erreurs de délai d’attente temporairement en tant que le service de stockage charger les soldes des demandes en déplaçant d’une partition vers un nouveau serveur.

La métrique de **PercentTimeoutError** est un regroupement des métriques suivantes : **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**et **SASServerTimeoutError**.

Les délais d’attente du serveur sont due à une erreur sur le serveur. Les délais d’attente du client se produisent car une opération sur le serveur a dépassé le délai d’attente spécifié par le client ; par exemple, un client à l’aide de la bibliothèque de Client de stockage peut définir un délai d’attente pour une opération à l’aide de la propriété **ServerTimeout** de la classe **QueueRequestOptions** .

Délais du serveur indiquent un problème avec le service de stockage qui requiert des investigations. Vous pouvez utiliser des mesures pour voir si vous atteignez les limites de l’évolutivité du service et pour identifier les pics de trafic qui peuvent être à l’origine de ce problème. Si le problème est intermittent, cela peut être dû à l’équilibrage de charge l’activité du service. Si le problème est persistant et n’est pas provoqué par votre application atteint les limites de l’évolutivité du service, vous devez déclencher un problème de prise en charge. Pour les délais d’attente du client, vous devez décider si le délai d’expiration est définie sur une valeur appropriée dans le client et modifier la valeur de délai d’attente définie dans le client ou examiner comment vous pouvez améliorer les performances des opérations dans le service de stockage, par exemple par l’optimisation de vos requêtes de table ou de réduction de la taille de vos messages.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Mesures montrent une augmentation de PercentNetworkError

Vos mesures montrent une augmentation dans **PercentNetworkError** pour l’un de vos services de stockage. La métrique de **PercentNetworkError** est un regroupement des métriques suivantes : **NetworkError**, **AnonymousNetworkError**et **SASNetworkError**. Ces événements se produisent lorsque le service de stockage détecte une erreur de réseau lorsque le client émet une demande de stockage.

La cause la plus courante de cette erreur est un client de se déconnecter avant l’expiration d’un délai d’attente dans le service de stockage. Vous devez examiner le code dans votre client de comprendre pourquoi et quand le client se déconnecte le service de stockage. Vous pouvez également utiliser Wireshark, l’Analyseur de Message Microsoft ou Tcping pour étudier les problèmes de connectivité réseau à partir du client. Ces outils sont décrits dans les [annexes].

### <a name="the-client-is-receiving-403-messages"></a>Le client reçoit les messages HTTP 403 (refusé)

Si votre application cliente est lever les erreurs HTTP 403 (refusé), une cause probable est que le client utilise une Signature de l’accès partagé (SAS) a expiré lors de l’envoi d’une demande de stockage (bien que les autres causes possibles sont les clés d’inclinaison, non valide horloge et en-têtes vides). Si une clé SAS a expiré est à l’origine, vous ne verrez pas toutes les entrées dans les données du journal enregistrement de stockage côté serveur. Le tableau suivant présente un exemple du journal côté client généré par la bibliothèque cliente de stockage qui illustre ce problème :

Source|Niveau de détail|Niveau de détail|Id de la demande client|Texte de l’opération
---|---|---|---|---
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|Début d’opération avec l’emplacement principal pour chaque mode de l’emplacement PrimaryOnly.
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|Démarrage d’une demande synchrone pour https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;si = mypolicy&amp;sig = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % 3D&amp;version de l’api = 14-02-2014.
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|Attente d’une réponse.
Microsoft.WindowsAzure.Storage|Avertissement|2|85d077ab-...|Exception levée lors de l’attente d’une réponse : le serveur distant a renvoyé une erreur : (403) interdit...
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|Réponse reçue. Code d’état = 403, ID de demande = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 =, ETag =.
Microsoft.WindowsAzure.Storage|Avertissement|2|85d077ab-...|Exception levée pendant l’opération : le serveur distant a renvoyé une erreur : (403) interdit...
Microsoft.WindowsAzure.Storage|Informations|3 |85d077ab-...|Vérification si l’opération doit être retentée. Nombre de tentatives = 0, code d’état HTTP = 403, Exception = le serveur distant a renvoyé une erreur : (403) interdit...
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|L’emplacement suivant a été défini à principal, en fonction du mode d’emplacement.
Microsoft.WindowsAzure.Storage|Erreur|1|85d077ab-...|Stratégie de nouvelles tentatives n’a pas été autorisé pour une nouvelle tentative. Échec avec le serveur distant a renvoyé une erreur : (403) interdit.

Dans ce scénario, vous devez rechercher pourquoi le jeton SAS arrive à expiration avant que le client envoie le jeton pour le serveur :

- En règle générale, vous ne devez pas définir une heure de début lorsque vous créez un SAS à un client pour utiliser immédiatement. S’il existe des différences de petite horloge entre l’hôte générant les associations de sécurité en utilisant le service de stockage et de l’heure actuelle, il est possible que le service de stockage recevoir un SAS n’est pas encore valide.
- Vous ne devez pas définir un délai très court dans un SAS. Là encore, les différences de petite horloge entre l’hôte générant les associations de sécurité et le service de stockage peuvent entraîner un SAS apparemment expirant au plus tôt que prévu.
- Est le paramètre de version de la clé des associations de sécurité (par exemple **VP = 04/2015 / 05**) correspond à la version de la bibliothèque de Client de stockage vous utilisez ? Nous vous recommandons de toujours utiliser la dernière version de la [Bibliothèque de stockage Client](https://www.nuget.org/packages/WindowsAzure.Storage/).
- Si vous régénérez vos clés d’accès de stockage, cela peut invalider les jetons d’associations de sécurité existants. Cela peut être un problème si vous générez des jetons SAS avec un délai long pour les applications clientes à mettre en cache.

Si vous utilisez la bibliothèque de Client de stockage pour générer des jetons d’associations de sécurité, il est facile de créer un jeton valide. Toutefois, si vous utilisez l’API REST de stockage et construction les jetons SAS à la main doivent lire attentivement la rubrique [Délégation de l’accès avec une Signature d’accès partagé](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Le client reçoit les messages HTTP 404 (introuvable)
Si l’application cliente reçoit un message HTTP 404 (introuvable) à partir du serveur, cela signifie que l’objet que le client a tenté d’utiliser (par exemple, une entité, une table, blob, conteneur ou file d’attente) n’existe pas dans le service de stockage. Il existe un certain nombre de raisons possibles à cela, de tels que :

- [Le client ou un autre processus précédemment supprimé l’objet]
- [Un problème d’autorisation de Signature de l’accès partagé (SAS)]
- [Code de JavaScript côté client n’a pas l’autorisation d’accéder à l’objet]
- [Panne réseau]

#### <a name="client-previously-deleted-the-object"></a>Le client ou un autre processus précédemment supprimé l’objet
Dans les scénarios où le client tente de lire, mettre à jour ou supprimer des données dans un service de stockage, il est facile d’identifier, dans les journaux du côté serveur, une opération précédente qui a supprimé l’objet en question à partir du service de stockage. Très souvent, les données du journal indiquent qu’un autre utilisateur ou processus supprimé de l’objet. Dans le journal de l’enregistrement de stockage côté serveur, le type d’opération et les colonnes de la clé de l’objet demandé affichent par un client lors de la suppression un objet.

Dans le scénario dans lequel un client tente d’insérer un objet, il ne peut pas être immédiatement évidente pourquoi il en résulte une réponse HTTP 404 (introuvable) étant donné que le client crée un nouvel objet. Toutefois, si le client crée un objet blob doit être en mesure de trouver le conteneur d’objet blob, si le client crée un message, qu'il doit être en mesure de trouver une file d’attente, et si le client ajoute une ligne qu’il doit être en mesure de trouver la table.

Vous pouvez utiliser le journal côté client à partir de la bibliothèque de Client de stockage pour obtenir une compréhension plus détaillée des lorsque le client envoie des requêtes spécifiques au service de stockage.

Le journal suivant de côté client généré par la bibliothèque cliente de stockage illustre le problème lorsque le client ne trouve pas le conteneur pour le blob de que création. Ce journal contient les détails des opérations de stockage suivantes :

ID de la demande|Opération
---|---
07b26a5d-...|Méthode de **DeleteIfExists** pour supprimer le conteneur de l’objet blob. Notez que cette opération implique une requête **HEAD** pour vérifier l’existence du conteneur.
e2d06d78...|Méthode **CreateIfNotExists** pour créer le conteneur de l’objet blob. Notez que cette opération implique une requête **HEAD** qui vérifie l’existence du conteneur. La **tête** renvoie un message 404 mais se poursuit.
de8b1c3c-...|Méthode **UploadFromStream** pour créer l’objet blob. La requête **PUT** échoue avec un message 404

Écritures de journal :

ID de la demande |  Texte de l’opération
---|---
07b26a5d-...|Démarrage d’une demande synchrone pour https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-...|StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 2014 de juin 03 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-...|Attente d’une réponse.
07b26a5d-... | Réponse reçue. Code d’état = 200, ID de demande = eeead849... Content-MD5 =, ETag = &quot;0x8D14D2DC63D059B&quot;.
07b26a5d-... | En-têtes de réponse ont été traités avec succès, continuer le reste de l’opération.
07b26a5d-... | Télécharger le corps de la réponse.
07b26a5d-... | Opération terminée avec succès.
07b26a5d-... | Démarrage d’une demande synchrone pour https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-... | StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 2014 de juin 03 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-... | Attente d’une réponse.
07b26a5d-... | Réponse reçue. Code d’état = 202, ID de demande = 6ab2a4cf-..., Content-MD5 =, ETag =.
07b26a5d-... | En-têtes de réponse ont été traités avec succès, continuer le reste de l’opération.
07b26a5d-... | Télécharger le corps de la réponse.
07b26a5d-... | Opération terminée avec succès.
e2d06d78-... | Démarre une demande asynchrone à https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
e2d06d78-... | StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 2014 de juin 03 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-...| Attente d’une réponse.
de8b1c3c-... | Démarrage d’une demande synchrone pour https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... |  StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-MS-BLOB-type:BlockBlob.x-MS-client-Request-ID:de8b1c3c-...x-MS-date:TUE, 2014 de juin 03 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | Préparation pour écrire les données de la demande.
e2d06d78-... | Exception levée lors de l’attente d’une réponse : le serveur distant a renvoyé une erreur : (404) introuvable...
e2d06d78-... | Réponse reçue. Code d’état = 404, ID de demande = 353ae3bc-..., Content-MD5 =, ETag =.
e2d06d78-... | En-têtes de réponse ont été traités avec succès, continuer le reste de l’opération.
e2d06d78-... | Télécharger le corps de la réponse.
e2d06d78-... | Opération terminée avec succès.
e2d06d78-... | Démarre une demande asynchrone à https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
e2d06d78-...|StringToSign = PUT... 0...x-MS-client-Request-ID:e2d06d78-...x-MS-date:TUE, 2014 de juin 03 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-... | Attente d’une réponse.
de8b1c3c-... | Écriture de données de requête.
de8b1c3c-... | Attente d’une réponse.
e2d06d78-... | Exception levée lors de l’attente d’une réponse : le serveur distant a renvoyé une erreur : conflit (409)...
e2d06d78-... | Réponse reçue. Code d’état = 409, ID de demande = c27da20e-..., Content-MD5 =, ETag =.
e2d06d78-... | Télécharger le corps de réponse d’erreur.
de8b1c3c-... | Exception levée lors de l’attente d’une réponse : le serveur distant a renvoyé une erreur : (404) introuvable...
de8b1c3c-... | Réponse reçue. Code d’état = 404, ID de demande = 0eaeab3e-..., Content-MD5 =, ETag =.
de8b1c3c-...| Exception levée pendant l’opération : le serveur distant a renvoyé une erreur : (404) introuvable...
de8b1c3c-... | Stratégie de nouvelles tentatives n’a pas été autorisé pour une nouvelle tentative. Échec avec le serveur distant a renvoyé une erreur : (404) introuvable...
e2d06d78-... | Stratégie de nouvelles tentatives n’a pas été autorisé pour une nouvelle tentative. Échec avec le serveur distant a renvoyé une erreur : conflit (409)...

Dans cet exemple, le journal indique que le client est entrelacement des demandes à partir de la méthode **CreateIfNotExists** (demande id e2d06d78...) avec les demandes à partir de la méthode **UploadFromStream** (de8b1c3c-...) ; Cela se produit parce que l’application cliente appelle ces méthodes de façon asynchrone. Vous devez modifier le code asynchrone dans le client pour s’assurer qu’il crée le conteneur avant d’essayer de télécharger des données à un objet blob dans ce conteneur. Dans l’idéal, vous devez créer tous vos conteneurs, à l’avance.

#### <a name="SAS-authorization-issue"></a>Un problème d’autorisation de Signature de l’accès partagé (SAS)

Si l’application cliente tente d’utiliser une clé SAS qui n’inclut pas les autorisations nécessaires pour l’opération, le service de stockage renvoie un message HTTP 404 (introuvable) au client. Dans le même temps, vous verrez également une valeur différente de zéro pour **SASAuthorizationError** dans les mesures.

Le tableau suivant présente un exemple de message de journal du côté serveur du fichier journal de l’enregistrement de stockage :

<table>
  <tr>
    <td>Heure de début de demande</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Type d’opération</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>Statut de la demande</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>Code d’état HTTP</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Type d’authentification</td>
    <td>SAS</td>
  </tr>
  <tr>
    <td>Type de service</td>
    <td>Objet BLOB</td>
  </tr>
  <tr>
    <td>URL de la demande</td>
    <td>
    https://domemaildist.BLOB.Core.Windows.NET/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp ; sr = c&amp;amp ; si = mypolicy&amp;amp ; sig = XXXXX&amp;amp ; la version de l’api = 14-02-2014&amp;amp ;</td>
  </tr>
  <tr>
    <td>En-tête de l’id de requête</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>ID de la demande client</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

Vous devez rechercher pourquoi votre application cliente tente d’effectuer une opération qu’il n’a pas reçu d’autorisations pour.

#### <a name="JavaScript-code-does-not-have-permission"></a>Code de JavaScript côté client n’a pas l’autorisation d’accéder à l’objet

Si vous utilisez un client JavaScript et le service de stockage retourne les messages HTTP 404, vous vérifiez les erreurs JavaScript suivants dans le navigateur :

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] Vous pouvez utiliser les outils de développement F12 dans Internet Explorer permet de suivre les messages échangés entre le navigateur et le service de stockage lorsque vous dépannez des problèmes de JavaScript côté client.

Ces erreurs se produisent parce que le navigateur web implémente la restriction de sécurité de [stratégie de même origine](http://www.w3.org/Security/wiki/Same_Origin_Policy) qui empêche l’appel d’une API dans un domaine différent du domaine que la page provient d’une page web.

Pour contourner le problème de JavaScript, vous pouvez configurer entre origine ressource partage (CORS) pour le service de stockage que le client accède. Pour plus d’informations, consultez [prise en charge du partage de ressources entre origine (CORS) pour les Services de stockage Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

L’exemple de code suivant montre comment configurer votre service d’objet blob pour permettre de JavaScript qui s’exécute dans le domaine Contoso accéder à un objet blob dans votre service de stockage blob :

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>Panne réseau

Dans certaines circonstances, le service de stockage retourne au client les messages HTTP 404 peuvent entraîner les paquets réseau perdus. Par exemple, lorsque votre application cliente est la suppression d’une entité à partir du service de la table vous voir le client lève une exception de stockage reporting un « HTTP 404 (introuvable) » message d’état à partir du service de la table. Lorsque vous examinez la table dans le service de stockage de table, vous voyez que le service n’a supprimé l’entité comme demandé.

Les détails de l’exception dans le client incluent l’id de la demande (7e84f12d...) affecté par le service de table pour la demande : vous pouvez utiliser ces informations pour rechercher les détails de la demande dans les journaux de stockage côté serveur en effectuant une recherche dans la colonne **d’en-tête de l’id de demande** dans le fichier journal. Vous pourriez également utiliser les mesures pour identifier lorsque les échecs de ce se produisent et puis recherchez les fichiers journaux en fonction du temps que les mesures enregistrement cette erreur. Cette entrée de journal indique que la suppression a échoué avec un message d’état « Client HTTP (404) autre erreur ». La même entrée de journal inclut également l’id de demande généré par le client dans la colonne **d’id de demande client** (813ea74f...).

Le journal côté serveur inclut également une autre entrée avec la même valeur **d’id de demande client** (813ea74f...) pour une opération de suppression de réussite pour la même entité et à partir du même client. Cette opération de suppression réussie a eu lieu très peu de temps avant l’échec de la suppression de demande.

La cause la plus probable de ce scénario est que le client a envoyé une demande de suppression de l’entité pour le service de la table, qui a réussi, mais n’a pas reçu d’accusé de réception du serveur (peut-être en raison d’un problème de réseau temporaire). Le client puis automatiquement essayée à nouveau l’opération (en utilisant le même **id de demande client**), et cette tentative a échoué car l’entité a déjà été supprimée.

Si ce problème se produit fréquemment, vous devez rechercher pourquoi le client ne peut pas recevoir les accusés de réception à partir du service de la table. Si le problème est intermittent, intercepter l’erreur « HTTP (404) introuvable » et ouvrez une session sur le client, mais autoriser le client à continuer.

### <a name="the-client-is-receiving-409-messages"></a>Le client reçoit les messages HTTP 409 (conflit)

Le tableau suivant montre un extrait du journal côté serveur pour les deux opérations de client : **DeleteIfExists** suivi immédiatement par **CreateIfNotExists** en utilisant le même nom de conteneur de blob. Notez que les résultats des opérations de chaque client dans deux demandes envoyées au serveur, tout d’abord une demande de **GetContainerProperties** pour vérifier si le conteneur existe, suivie de la demande de **DeleteContainer** ou **CreateContainer** .

Horodatage|Opération|Résultat|Nom du conteneur|Id de la demande client
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-...
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-...
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-...
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-...

Le code dans l’application cliente supprime et recrée ensuite immédiatement un conteneur blob en utilisant le même nom : la méthode **CreateIfNotExists** (demande de Client ID bc881924-...) finit par échouer avec l’erreur HTTP 409 (conflit). Lorsqu’un client supprime les conteneurs blob, des tableaux ou des files d’attente est une brève période avant le nom devient à nouveau disponible.

L’application cliente doit utiliser des noms de conteneur unique chaque fois qu’il crée de nouveaux conteneurs si le modèle de supprimer/recréer est commun.

### <a name="metrics-show-low-percent-success"></a>Faible PercentSuccess afficher les mesures ou les entrées de journal d’analytique avoir des opérations avec le statut de la transaction de ClientOtherErrors

La mesure de le **PercentSuccess** capture le pourcentage d’opérations réussies en fonction de leur Code d’état HTTP. Opérations avec des codes d’état de 2XX comptent comme étant réussies, alors que les opérations avec des codes d’état dans les plages 3XX, 4XX et 5XX sont considérées comme ayant échoué et de réduire la valeur métrique de **PercentSucess** . Dans les fichiers journaux de stockage côté serveur, ces opérations sont enregistrées avec un statut de transaction de **ClientOtherErrors**.

Il est important de noter que ces opérations s’est terminée avec succès et par conséquent n’affectent pas d’autres mesures de disponibilité. Voici quelques exemples d’opérations qui s’exécuter avec succès, mais qui peut entraîner des codes d’état HTTP échouées :
- **ResourceNotFound** (404 introuvable), par exemple à partir d’une demande GET pour un objet blob qui n’existe pas.
- **ResouceAlreadyExists** (Conflit 409), par exemple à partir d’une opération de **CreateIfNotExist** où la ressource existe déjà.
- **ConditionNotMet** (Non modifié 304), par exemple à partir d’une opération conditionnelle, par exemple lorsqu’un client envoie une valeur **ETag** et un en-tête HTTP **If-None-Match** pour demander une image que si elle a été mis à jour depuis la dernière opération.

Vous trouverez une liste des codes d’erreur courants API REST renvoyant les services de stockage sur la page de [Codes d’erreur courants autres API](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Mesures de capacité affichent une augmentation inattendue de l’utilisation de capacité de stockage


Si vous voyez soudaine, des changements inattendus dans l’utilisation de la capacité de votre compte de stockage, que vous pouvez étudier les raisons en examinant en premier votre métriques de disponibilité ; par exemple, une augmentation du nombre de suppressions ayant échoué demandes peuvent entraîner une augmentation de stockage des objets blob que vous utilisez comme les opérations de nettoyage spécifique de l’application que vous pouvez vous attendre à être de libérer de l’espace ne fonctionne ne peut-être pas comme prévu (par exemple, parce que les jetons d’associations de sécurité utilisés pour la libération d’espace ont expiré).

### <a name="you-are-experiencing-unexpected-reboots"></a>Vous rencontrez des redémarrages inattendus des ordinateurs virtuels Azure qui ont un grand nombre de disques durs virtuels reliés

Si un Azure Machine virtuelle (VM) possède un grand nombre de disques durs virtuels reliés qui sont dans le même compte de stockage, vous pourriez dépasser les cibles de l’évolutivité d’un compte de stockage à l’origine de la machine virtuelle à échouer. Vous devez vérifier les mesures de minute pour le compte de stockage (**TotalRequests**/**TotalIngress**/**TotalEgress**) des pics dépassant les cibles de l’évolutivité d’un compte de stockage. Consultez la section «[qu'une augmentation de PercentThrottlingError afficher les mesures]» pour déterminer si la limitation de l’aide s’est produite sur votre compte de stockage.

En général, chaque entrée individuelle ou une opération de sortie sur un disque dur virtuel à partir d’un ordinateur virtuel se traduit par des opérations **Obtenir une Page** ou **Placer** sur le blob de la page sous-jacente. Par conséquent, vous pouvez utiliser l’estimation IOPS pour votre environnement pour optimiser le nombre de disques durs virtuels peuvent avoir dans un compte de stockage unique basée sur le comportement spécifique de votre application. Il est déconseillé d’avoir plus de 40 disques dans un compte de stockage unique. Voir [l’évolutivité du stockage Azure et des cibles de performances](storage-scalability-targets.md) pour plus de détails des cibles l’évolutivité en cours pour les comptes de stockage, en particulier le taux de demande totale et de la bande passante totale pour le type de compte de stockage que vous utilisez.
Si vous dépassez les cibles de l’évolutivité de votre compte de stockage, vous devez placer vos disques durs virtuels dans plusieurs comptes de stockage différents pour réduire l’activité de chaque compte.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Votre problème découle de l’utilisation de l’émulateur de stockage de développement ou de test

En général, vous utilisez l’émulateur de stockage au cours du développement et testez pour éviter la nécessité d’un compte de stockage Azure. Les problèmes courants qui peuvent se produire lorsque vous utilisez l’émulateur de stockage sont les suivantes :

- [La fonctionnalité « X » ne fonctionne pas dans l’émulateur de stockage]
- [Erreur « la valeur d’un des en-têtes HTTP n’est pas au format correct » lors de l’utilisation de l’émulateur de stockage]
- [L’émulateur de stockage en cours d’exécution requiert des privilèges d’administrateur]

#### <a name="feature-X-is-not-working"></a>La fonctionnalité « X » ne fonctionne pas dans l’émulateur de stockage

L’émulateur de stockage ne prend pas en charge toutes les fonctionnalités des services de stockage Azure, tels que le service de fichiers. Pour plus d’informations, voir [utilisation de l’émulateur de stockage Azure pour le développement et les tests](storage-use-emulator.md).

Pour les fonctionnalités de l’émulateur de stockage ne prenant pas en charge, utilisez le service de stockage Azure dans le nuage.

#### <a name="error-HTTP-header-not-correct-format"></a>Erreur « la valeur d’un des en-têtes HTTP n’est pas au format correct » lors de l’utilisation de l’émulateur de stockage

Vous testez votre application qui utilisent la bibliothèque Client de stockage par rapport à l’émulateur de stockage local et appels de méthodes tels que **CreateIfNotExists** échoue avec le message d’erreur « la valeur d’un des en-têtes HTTP n’est pas au format correct. » Cela indique que la version de l’émulateur de stockage que vous utilisez ne gère pas la version de la bibliothèque de client stockage que vous utilisez. La bibliothèque cliente de stockage ajoute l' en-tête **x-ms-version** à toutes les demandes qu’il effectue. Si l’émulateur de stockage ne reconnaît pas la valeur de l’en-tête **x-ms-version** , il rejette la demande.

Vous pouvez utiliser les journaux de Storage bibliothèque Client pour voir la valeur de l' **en-tête x-ms-version** qu’il envoie. Vous pouvez également voir la valeur de l' **en-tête x-ms-version** si vous utilisez Fiddler pour les demandes de trace à partir de votre application cliente.

Ce scénario se produit généralement si vous installez et que vous utilisez la dernière version de la bibliothèque de Client de stockage sans mise à jour de l’émulateur de stockage. Vous devez installer la dernière version de l’émulateur de stockage, ou utiliser le stockage en nuage au lieu de l’émulateur de développement et de test.

#### <a name="storage-emulator-requires-administrative-privileges"></a>L’émulateur de stockage en cours d’exécution requiert des privilèges d’administrateur

Lorsque vous exécutez l’émulateur de stockage, vous êtes invité pour les informations d’identification d’administrateur. Cela se produit uniquement lors de l’initialisation de l’émulateur de stockage pour la première fois. Une fois que vous avez initialisé l’émulateur de stockage, il est inutile des privilèges d’administrateur pour exécuter de nouveau.

Pour plus d’informations, voir [utilisation de l’émulateur de stockage Azure pour le développement et les tests](storage-use-emulator.md). Notez que vous pouvez également initialiser l’émulateur de stockage dans Visual Studio, ce qui nécessite également des privilèges d’administrateur.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Vous rencontrez des problèmes pour installer le SDK Azure pour .NET

Lorsque vous essayez d’installer le Kit de développement, il échoue tente d’installer l’émulateur de stockage sur votre ordinateur local. Le journal d’installation contient l’un des messages suivants :

- CAQuietExec : Erreur : Impossible d’accéder à SQL instance
- CAQuietExec : Erreur : Impossible de créer la base de données

La cause est un problème avec l’installation existante de LocalDB. Par défaut, l’émulateur de stockage utilise LocalDB pour conserver les données lorsqu’il simule les services de stockage Azure. Vous pouvez réinitialiser votre instance LocalDB en exécutant les commandes suivantes dans une fenêtre d’invite de commandes avant d’essayer d’installer le Kit de développement logiciel.

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

La commande **Supprimer** supprime les anciens fichiers de base de données à partir d’installations antérieures de l’émulateur de stockage.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Vous avez un autre problème avec un service de stockage

Si les sections de dépannage précédentes n’incluent pas le problème que vous rencontrez avec un service de stockage, à adopter l’approche suivante pour le diagnostic et la résolution de votre problème.

- Vérifiez vos mesures pour voir s’il existe aucune modification dans le comportement attendu de la ligne de base. À partir de la métrique, vous pourrez peut-être déterminer si le problème est temporaire ou permanent, et les opérations de stockage le problème affecte.
- Vous pouvez utiliser les informations de métriques pour vous aider à rechercher vos données de journal côté serveur pour plus d’informations sur les erreurs qui se produisent. Ces informations peuvent vous aider à dépanner et résoudre le problème.
- Si les informations contenues dans les journaux de côté serveur ne sont pas suffisants pour résoudre le problème avec succès, vous pouvez utiliser les journaux de côté client de bibliothèque Client de stockage à étudier le comportement de votre application cliente et outils de Fiddler Wireshark et Microsoft Message Analyzer pour analyser votre réseau.

Pour plus d’informations sur l’utilisation de Fiddler, consultez «[l’appendice 1 : à l’aide de Fiddler pour capturer le trafic HTTP et HTTPS]. »

Pour plus d’informations sur l’utilisation de Wireshark, consultez «[l’appendice 2 : pour capturer le trafic réseau à l’aide de Wireshark]. »

Pour plus d’informations sur l’Analyseur de Message de Microsoft, reportez-vous à la section «[appendice 3 : pour capturer le trafic réseau à l’aide de Microsoft Message Analyzer]. »

## <a name="appendices"></a>Annexes

Les annexes décrivent plusieurs outils que vous pouvez trouver utile, diagnostic et résolution des problèmes avec le stockage Azure (et d’autres services). Ces outils ne font pas partie du stockage Azure et d’autres produits tiers. En tant que tel, les outils abordés dans ces annexes ne relèvent pas d’un contrat de support que peut avoir avec Microsoft Azure ou stockage Azure, et par conséquent, dans le cadre de votre processus d’évaluation, vous devez examiner les options de prise en charge et de gestion de licences disponibles à partir des fournisseurs de ces outils.

### <a name="appendix-1"></a>Appendice 1 : À l’aide de Fiddler pour capturer le trafic HTTP et HTTPS

[Fiddler](http://www.telerik.com/fiddler) est un outil très utile pour analyser le trafic HTTP et HTTPS entre votre application cliente et le service de stockage Azure que vous utilisez.

> [AZURE.NOTE] Fiddler peut décoder le trafic HTTPS ; Vous devez lire la documentation Fiddler attentivement afin de comprendre comment cela et de bien comprendre les implications de sécurité.

Cette annexe fournit une brève procédure pas à pas comment configurer Fiddler pour capturer le trafic entre l’ordinateur local où vous avez installé Fiddler et les services de stockage Azure.

Une fois que vous avez lancé Fiddler, il commencera capture le trafic HTTP et HTTPS sur votre ordinateur local. Certaines des commandes utiles pour le contrôle de Fiddler sont les suivantes :

- Arrêter et démarrer la capture du trafic. Dans le menu principal, accédez au **fichier** et puis cliquez sur **Capturer le trafic** pour activer et désactiver la capture.
- Enregistrer les données relatives au trafic capturé. Dans le menu principal, accédez au **fichier**, cliquez sur **Enregistrer**, puis cliquez sur **Toutes les Sessions**: cela vous permet d’enregistrer le trafic dans un fichier d’Archive de la Session. Vous pouvez recharger une Archive de Session pour l’analyse, ou envoyer si demande de support technique Microsoft.

Pour limiter la quantité de trafic qui capture de Fiddler, vous pouvez utiliser des filtres que vous configurez dans l’onglet **filtres** . La capture d’écran suivante montre un filtre de ne capture que le trafic vers le point de terminaison de stockage **contosoemaildist.table.core.windows.net** :

![][5]

### <a name="appendix-2"></a>Appendice 2 : À l’aide de Wireshark pour capturer le trafic réseau

[Wireshark](http://www.wireshark.org/) est un analyseur de protocole réseau qui vous permet d’afficher les informations détaillées de paquets pour un large éventail de protocoles réseau.

La procédure suivante vous indique comment faire pour capturer les informations détaillées des paquets pour le trafic à partir de l’ordinateur local où vous avez installé Wireshark au service de la table dans votre compte de stockage Azure.

1.  Lancer Wireshark sur votre ordinateur local.
2.  Dans la section **Démarrer** , sélectionnez l’interface de réseau local ou les interfaces qui sont connectées à internet.
3.  Cliquez sur **Options de Capture**.
4.  Ajouter un filtre à la zone de texte **Filtre de Capture** . Par exemple, **contosoemaildist.table.core.windows.net d’hôte** configure Wireshark pour capturer uniquement les paquets envoyés vers ou à partir du point de terminaison du service de table dans le compte de stockage **contosoemaildist** . Consultez la [liste complète des filtres de Capture](http://wiki.wireshark.org/CaptureFilters).

    ![][6]

5.  Cliquez sur **Démarrer**. Wireshark est prêt à capturer tous les paquets d’envoi vers ou à partir du point de terminaison de service de table que vous utilisez votre application client sur votre ordinateur local.
6.  Lorsque vous avez terminé, dans le menu principal, cliquez sur **capturer** , puis sur **Arrêter**.
7.  Pour enregistrer les données capturées dans un fichier de Capture Wireshark, dans le menu principal, cliquez sur **fichier** , puis sur **Enregistrer**.

WireShark met en évidence les erreurs figurant dans la fenêtre **packetlist** . Vous pouvez également utiliser la fenêtre **Infos de l’Expert** (cliquez sur **analyser**, puis **Expert Info**) pour afficher un résumé des erreurs et des avertissements.

![][7]

Vous pouvez également choisir d’afficher les données TCP comme la couche d’application qu’il voit en cliquant sur les données TCP et en sélectionnant **Suivez les flux TCP**. Ceci est particulièrement utile si vous avez capturé votre image sans un filtre de capture. Pour plus d’informations, reportez-vous à la section [suite de flux de données TCP] (http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [AZURE.NOTE] Pour plus d’informations sur l’utilisation de Wireshark, consultez le [Guide d’utilisation de Wireshark](http://www.wireshark.org/docs/wsug_html_chunked).

### <a name="appendix-3"></a>Appendice 3 : À l’aide de Microsoft Message Analyzer pour capturer le trafic réseau

Vous pouvez utiliser l’Analyseur de Message Microsoft pour capturer le trafic HTTP et HTTPS de façon similaire à Fiddler et capturer le trafic réseau d’une manière similaire à Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configurer une session de suivi web à l’aide de Microsoft Message Analyzer

Pour configurer une session de suivi web pour le trafic HTTP et HTTPS à l’aide de Microsoft Message Analyzer, exécuter l’application Analyseur de Message de Microsoft, puis dans le menu **fichier** , cliquez sur **Trace de la Capture**. Dans la liste des scénarios de trace disponibles, sélectionnez le **Proxy Web**. Puis, dans le panneau de **Configuration du scénario de suivi** , dans la zone de texte **HostnameFilter** , ajoutez les noms de vos points de terminaison de stockage (vous pouvez rechercher ces noms dans le [Portail Azure](https://portal.azure.com)). Par exemple, si le nom de votre compte de stockage Azure est **contosodata**, vous devez ajouter les éléments suivants à la zone de texte **HostnameFilter** :

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [AZURE.NOTE] Un espace sépare les noms d’hôtes.

Lorsque vous êtes prêt à commencer à collecter des données de trace, cliquez sur le bouton **Démarrer avec** .

Pour plus d’informations sur la trace de l' Analyseur de Message Microsoft **Proxy Web** , consultez le [Fournisseur de Microsoft-PEF-WebProxy](http://technet.microsoft.com/library/jj674814.aspx).

La trace de **Proxy Web** intégrée dans l’Analyseur de Message de Microsoft est basée sur Fiddler ; Il peut capturer le trafic HTTPS de côté client et afficher les messages non cryptés HTTPS. La trace de **Proxy Web** fonctionne en configurant un proxy local pour tout trafic HTTP et HTTPS qui lui donne accès aux messages non chiffrés.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnostic des problèmes de réseau à l’aide de Microsoft Message Analyzer

Outre l’utilisation de la trace de l' Analyseur de Message Microsoft **Proxy Web** pour capturer les détails du trafic HTTP/HTTPs entre l’application cliente et le service de stockage, vous pouvez également utiliser la trace de la **Couche de liaison locale** intégrée pour capturer des informations sur les paquets réseau. Ainsi, vous permet de capturer des données similaires à celles que vous pouvez capturer avec Wireshark et diagnostiquer les problèmes de réseau tel que les paquets ignorés.

La capture d’écran suivante montre un exemple de trace de **Couche de liaison locale** avec quelques messages **d’information** dans la colonne **DiagnosisTypes** . En cliquant sur une icône dans la colonne **DiagnosisTypes** indique les détails du message. Dans cet exemple, le serveur retransmis message #305, car il n’a pas reçu d’accusé de réception du client :

![][9]

Lorsque vous créez la session de trace dans l’Analyseur de Message de Microsoft, vous pouvez spécifier des filtres pour réduire la quantité de bruit dans la trace. Dans la page **Capture /trace** où vous définissez la trace, cliquez sur le lien **configurer** en regard de **Microsoft-Windows-NDIS-PacketCapture**. La capture d’écran suivante illustre une configuration qui permet de filtrer le trafic TCP pour les adresses IP des trois services de stockage :

![][10]

Pour plus d’informations sur la trace de la couche de liaison locale Microsoft Message Analyzer, consultez le [Fournisseur de Microsoft-PEF-NDIS-PacketCapture](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Appendice 4 : À l’aide d’Excel pour afficher les mesures et les données du journal

De nombreux outils vous permettent de télécharger les données des indicateurs de stockage à partir de stockage par table Azure dans un format délimité qui permet de charger les données dans Excel pour affichage et analyse. Données de l’enregistrement de stockage depuis le stockage blob Azure sont déjà dans un format délimité que vous pouvez charger dans Excel. Toutefois, vous devez ajouter les en-têtes de colonnes appropriés dans les informations au [Format de stockage du journal Analytique](http://msdn.microsoft.com/library/azure/hh343259.aspx) et de [Schéma de la Table Analytique métriques de stockage](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Pour importer vos données d’enregistrement de stockage dans Excel après que l’avoir téléchargé depuis le stockage blob :

- Dans le menu **données** , cliquez sur **à partir du texte**.
- Recherchez le fichier journal que vous souhaitez afficher et cliquez sur **Importer**.
- À l’étape 1 de l' **Assistant Importation de texte**, sélectionnez **délimité**.

Lors de l’étape 1 de l' **Assistant Importation de texte**, sélectionnez le **point-virgule** comme séparateur uniquement et choisissez guillemet comme **qualificateur de texte**. Cliquez sur **Terminer** , puis choisissez un emplacement pour placer les données dans votre classeur.

### <a name="appendix-5"></a>Appendice 5 : Analyse avec des aperçus de l’Application pour Visual Studio Team Services

Vous pouvez également utiliser la fonctionnalité Aperçu de l’Application pour Visual Studio Team Services dans le cadre de votre analyse des performances et disponibilité. Cet outil permet de :

- Assurez-vous que votre service web est disponible et la réactivité. Si votre application est un site web ou une application de périphérique qui utilise un service web, il peut tester votre URL à quelques minutes d’intervalle à partir d’emplacements dans le monde entier et vous permettent de savoir s’il existe un problème.
- Diagnostiquer rapidement les problèmes de performances ou des exceptions dans votre service web. Découvrez si le processeur ou autres ressources sont étirées, obtenir des traces de la pile d’exceptions et facilement rechercher les traces de journal. Si les performances de l’application passe en dessous des limites acceptables, nous pouvons vous envoyer un message électronique. Vous pouvez surveiller les services web .NET et Java.

Vous trouverez plus d’informations à [ce qui est des idées d’Application ?](../application-insights/app-insights-overview.md).

<!--Anchors-->
[Introduction]: #introduction
[L’organisation de ce guide]: #how-this-guide-is-organized

[Surveillance de votre service de stockage]: #monitoring-your-storage-service
[Surveillance de l’état du service]: #monitoring-service-health
[Analyse de la capacité]: #monitoring-capacity
[Analyse de la disponibilité]: #monitoring-availability
[Analyse des performances]: #monitoring-performance

[Diagnostic des problèmes de stockage]: #diagnosing-storage-issues
[Problèmes de service de santé]: #service-health-issues
[Problèmes de performances]: #performance-issues
[Diagnostic des erreurs]: #diagnosing-errors
[Problèmes d’émulateur de stockage]: #storage-emulator-issues
[Outils de journalisation de stockage]: #storage-logging-tools
[À l’aide d’outils d’enregistrement de réseau]: #using-network-logging-tools

[Suivi de bout en bout]: #end-to-end-tracing
[Mise en corrélation des données de journal]: #correlating-log-data
[ID de la demande client]: #client-request-id
[ID du serveur de requête]: #server-request-id
[Estampilles]: #timestamps

[Guide de dépannage]: #troubleshooting-guidance
[Affichent les mesures AverageE2ELatency haute et basse AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Affichent les mesures de faible AverageE2ELatency et AverageServerLatency de faible mais que le client est confronté à une latence élevée]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Afficher les mesures de haute AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Vous rencontrez des retards inattendus dans la remise des messages dans une file d’attente]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Mesures montrent une augmentation de PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Augmentation temporaire des PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Augmentation permanente dans l’erreur de PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[Mesures montrent une augmentation de PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Mesures montrent une augmentation de PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Le client reçoit les messages HTTP 403 (refusé)]: #the-client-is-receiving-403-messages
[Le client reçoit les messages HTTP 404 (introuvable)]: #the-client-is-receiving-404-messages
[Le client ou un autre processus précédemment supprimé l’objet]: #client-previously-deleted-the-object
[Un problème d’autorisation de Signature de l’accès partagé (SAS)]: #SAS-authorization-issue
[Code de JavaScript côté client n’a pas l’autorisation d’accéder à l’objet]: #JavaScript-code-does-not-have-permission
[Panne réseau]: #network-failure
[Le client reçoit les messages HTTP 409 (conflit)]: #the-client-is-receiving-409-messages

[Faible PercentSuccess afficher les mesures ou les entrées de journal d’analytique avoir des opérations avec le statut de la transaction de ClientOtherErrors]: #metrics-show-low-percent-success
[Mesures de capacité affichent une augmentation inattendue de l’utilisation de capacité de stockage]: #capacity-metrics-show-an-unexpected-increase
[Vous rencontrez des redémarrages inattendus des Machines virtuelles disposant d’un grand nombre de disques durs virtuels reliés]: #you-are-experiencing-unexpected-reboots
[Votre problème découle de l’utilisation de l’émulateur de stockage de développement ou de test]: #your-issue-arises-from-using-the-storage-emulator
[La fonctionnalité « X » ne fonctionne pas dans l’émulateur de stockage]: #feature-X-is-not-working
[Erreur « la valeur d’un des en-têtes HTTP n’est pas au format correct » lors de l’utilisation de l’émulateur de stockage]: #error-HTTP-header-not-correct-format
[L’émulateur de stockage en cours d’exécution requiert des privilèges d’administrateur]: #storage-emulator-requires-administrative-privileges
[Vous rencontrez des problèmes pour installer le SDK Azure pour .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Vous avez un autre problème avec un service de stockage]: #you-have-a-different-issue-with-a-storage-service

[Annexes]: #appendices
[Appendice 1 : À l’aide de Fiddler pour capturer le trafic HTTP et HTTPS]: #appendix-1
[Appendice 2 : À l’aide de Wireshark pour capturer le trafic réseau]: #appendix-2
[Appendice 3 : À l’aide de Microsoft Message Analyzer pour capturer le trafic réseau]: #appendix-3
[Appendice 4 : À l’aide d’Excel pour afficher les mesures et les données du journal]: #appendix-4
[Appendice 5 : Analyse avec des aperçus de l’Application pour Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
