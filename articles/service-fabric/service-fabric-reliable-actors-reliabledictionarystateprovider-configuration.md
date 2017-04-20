<properties
   pageTitle="Vue d’ensemble de la configuration d’Azure Service Fabric fiable acteurs ReliableDictionaryActorStateProvider | Microsoft Azure"
   description="Obtenir des informations sur la configuration des acteurs avec état d’Azure Fabric de Service de type ReliableDictionaryActorStateProvider."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/18/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configuration des acteurs fiables--ReliableDictionaryActorStateProvider
Vous pouvez modifier la configuration par défaut de ReliableDictionaryActorStateProvider en modifiant le fichier settings.xml généré dans la racine du package Visual Studio sous le dossier Config de l’acteur spécifié.

Le runtime d’Azure Fabric de Service recherche des noms de section prédéfinie dans le fichier settings.xml et utilise les valeurs de configuration lors de la création des composants runtime sous-jacent.

>[AZURE.NOTE] Effectuez **pas** supprimer ou modifier les noms de section des configurations suivantes dans le fichier settings.xml généré dans la solution Visual Studio.

Il existe également des paramètres globaux qui affectent la configuration de ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuration globale

La configuration globale est spécifiée dans le manifeste de cluster du cluster sous la section KtlLogger. Il permet la configuration de la taille et l’emplacement du journal partagé ainsi que les limites de mémoire global utilisés par le journal. Notez que dans le manifeste du cluster affecte tous les services qui utilisent ReliableDictionaryActorStateProvider et fiable avec état.

Le manifeste de cluster est un fichier XML qui contient les paramètres et les configurations qui s’appliquent à tous les nœuds et les services dans le cluster. Le fichier est généralement appelé ClusterManifest.xml. Vous pouvez voir le cluster manifeste pour votre cluster à l’aide de la commande powershell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Noms de configuration

|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|Kilo-octets|8388608|Nombre minimal de Ko à allouer en mode noyau pour le pool de mémoire tampon écriture journal. Ce pool de mémoire est utilisé pour la mise en cache des informations d’état avant l’écriture sur le disque.|
|WriteBufferMemoryPoolMaximumInKB|Kilo-octets|Aucune limite|Taille maximale à laquelle le journal écrire le pool de mémoire tampon peut augmenter.|
|SharedLogId|GUID|""|Spécifie un GUID unique à utiliser pour identifier le fichier journal partagé par défaut utilisé par tous les services fiables sur tous les nœuds du cluster qui ne spécifient pas la SharedLogId dans leur configuration spécifique au service. Si SharedLogId est spécifié, alors SharedLogPath doit également être spécifié.|
|SharedLogPath|Nom de chemin d’accès complet|""|Spécifie le chemin d’accès qualifié complet dans lequel le fichier journal partagé utilisé par tous les services fiables sur tous les nœuds du cluster qui ne spécifient pas la SharedLogPath dans leur configuration spécifique au service. Toutefois, si SharedLogPath est spécifié, puis SharedLogId doit également être spécifié.|
|SharedLogSizeInMB|Mégaoctets|8192|Spécifie le nombre de Mo d’espace disque à allouer de manière statique pour le journal partagé. La valeur doit être supérieur ou égal à 2048.|

### <a name="sample-cluster-manifest-section"></a>Section de manifeste de cluster exemple
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Remarques
L’enregistreur dispose d’un pool global de mémoire alloué à partir de la mémoire pour le noyau non paginée disponible pour tous les services fiables sur un nœud pour mettre en cache les données sur l’état avant écrits dans le journal dédié associé avec le réplica d’un service fiable. La taille du pool est contrôlée par les paramètres WriteBufferMemoryPoolMinimumInKB et WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB spécifie la taille initiale de ce pool de mémoire et de la taille de la plus basse à laquelle le pool de mémoire peut réduire. WriteBufferMemoryPoolMaximumInKB est la taille maximale que peut atteindre le pool de mémoire. Chaque réplica de service fiable qui est ouvert peut augmenter la taille du pool de mémoire par un montant déterminé de système WriteBufferMemoryPoolMaximumInKB. S’il existe plusieurs demande de mémoire du pool de mémoire supérieure à celle disponible, les demandes de mémoire seront retardées jusqu'à ce que la mémoire est disponible. Par conséquent, si le pool de mémoire tampon écriture est trop petit pour une configuration particulière de performances peuvent pâtir.

Les paramètres SharedLogId et SharedLogPath sont toujours utilisés ensemble pour définir le GUID et l’emplacement du journal de partagé par défaut pour tous les nœuds du cluster. Le journal partagé par défaut est utilisé pour tous les services fiables qui ne spécifient pas les paramètres dans le settings.xml pour le service spécifique. Pour optimiser les performances, les fichiers de journal partagé doivent être placés sur des disques qui sont utilisés uniquement pour le fichier journal partagé pour réduire les conflits.

SharedLogSizeInMB spécifie la quantité d’espace disque à allouer pour le journal de partagé par défaut sur tous les nœuds.  SharedLogId et SharedLogPath n’avez pas besoin d’être spécifiée pour SharedLogSizeInMB doit être spécifié.

## <a name="replicator-security-configuration"></a>Configuration de la sécurité Replicator
Les configurations de sécurité Replicator utilisées pour sécuriser le canal de communication utilisé lors de la réplication. Cela signifie que les services ne peuvent pas voir l’autre le trafic de réplication, garantissant ainsi les données de disponibilité élevée sont également sûre.
Par défaut, une section de configuration de sécurité vide empêche la sécurité de la réplication.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuration de Replicator
Configurations de réplicateur sont utilisées pour configurer le réplicateur qui est responsable de l’état du fournisseur d’état acteur très fiable par la réplication et la persistance de l’état local.
La configuration par défaut est générée par le modèle Visual Studio et devrait suffire. Cette section décrit les configurations supplémentaires qui sont disponibles pour optimiser le réplicateur.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Noms de configuration

|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondes|0.015|Période pour laquelle le réplicateur sur le secondaire attend après avoir reçu une opération avant de l’envoyer à un accusé de réception principal. Tous les accusés de réception à envoyer pour les opérations traitées dans cet intervalle sont envoyés sous la forme d’une réponse.||
|ReplicatorEndpoint|N/A|Aucune valeur par défaut : paramètre requis|Adresse IP et le port que le réplicateur principal/secondaire utilisera pour communiquer avec d’autres fabricants dans le réplica définissent. Cela doit faire référence à un point de terminaison TCP ressource dans le manifeste du service. Consultez les [ressources de manifeste de Service](service-fabric-service-manifest-resources.md) pour en savoir plus sur la définition des ressources du point de terminaison dans le manifeste du service. |
|MaxReplicationMessageSize|Octets|50 MO|Taille maximale des données de réplication qui peuvent être transmises dans un seul message.|
|MaxPrimaryReplicationQueueSize|Nombre d’opérations|8192|Nombre maximal d’opérations dans la file d’attente principale. Une opération est libérée après que le réplicateur principal reçoit un accusé de réception auprès de tous les fabricants secondaires. Cette valeur doit être supérieure à 64 et une puissance de 2.|
|MaxSecondaryReplicationQueueSize|Nombre d’opérations|16384|Nombre maximal d’opérations dans la file d’attente secondaire. Une opération est libérée une fois son état hautement disponible par le biais de persistance. Cette valeur doit être supérieure à 64 et une puissance de 2.|
|CheckpointThresholdInMB|MO|200|Quantité d’espace de fichier journal après laquelle l’état est contrôlée.|
|MaxRecordSizeInKB|KO|1024|Taille d’enregistrement maximale le réplicateur peut écrire dans le journal. Cette valeur doit être un multiple de 4 et supérieur à 16.|
|OptimizeLogForLowerDiskUsage|Valeur booléenne|valeur True|Lorsque la valeur est true, le journal est configuré pour que les fichiers journal dédié du réplica sont créé en utilisant un fichier sparse de NTFS. Cela réduit l’utilisation de l’espace disque pour le fichier. Lorsque la valeur est false, le fichier est créé avec les allocations fixes, qui offrent que les meilleures performances d’écriture.|
|SharedLogId|GUID|""|Spécifie un guid unique à utiliser pour identifier le fichier journal partagé utilisé avec ce réplica. En règle générale, services ne doivent pas utiliser ce paramètre. Toutefois, si SharedLogId est spécifié, puis SharedLogPath doit également être spécifié.|
|SharedLogPath|Nom de chemin d’accès complet|""|Spécifie le chemin d’accès complet où sera créé le fichier journal partagé pour ce réplica. En règle générale, services ne doivent pas utiliser ce paramètre. Toutefois, si SharedLogPath est spécifié, puis SharedLogId doit également être spécifié.|


## <a name="sample-configuration-file"></a>Exemple de fichier de configuration

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>Remarques
Le paramètre BatchAcknowledgementInterval contrôle la latence de réplication. La valeur '0' entraîne la latence minimale, au détriment de débit (comme les messages d’accusé de réception supplémentaires devant être envoyées et traitées, contenant chacune des accusés de réception moins).
La valeur de BatchAcknowledgementInterval est grande, plus le débit de réplication global, au détriment de la latence d’opération plus élevé. Cela se traduit directement à la latence de validations de transaction.

Le paramètre CheckpointThresholdInMB contrôle la quantité d’espace disque que le réplicateur permet de stocker des informations d’état dans le fichier journal dédié de son. Cette augmentation d’une valeur supérieure à la valeur par défaut peut entraîner la reconfiguration plus rapides lorsqu’un nouveau réplica est ajouté à l’ensemble. Cela est dû le transfert partielle de l’état qui a lieu en raison de la disponibilité des détails de l’historique des opérations dans le journal. Cela peut potentiellement accroître le temps de récupération d’un réplica après une panne.

Si vous définissez OptimizeForLowerDiskUsage sur true, espace de fichier journal sera sur-approvisionné afin que les réplicas actifs peuvent stocker des informations d’état plus dans leurs fichiers journaux, tandis que les réplicas inactifs utilise moins d’espace disque. Cela rend possible d’héberger plusieurs réplicas sur un nœud. Si vous définissez OptimizeForLowerDiskUsage sur false, les informations d’état sont écrites dans les fichiers journaux plus rapidement.

Le paramètre MaxRecordSizeInKB définit la taille maximale d’un enregistrement qui peut être écrite par le réplicateur dans le fichier journal. Dans la plupart des cas, la taille de l’enregistrement de 1024 Ko par défaut est optimale. Toutefois, si le service est à l’origine des éléments de données plus grandes partie des informations d’état, puis cette valeur peut-être être augmentée. Il existe peu d’intérêt dans la fabrication de MaxRecordSizeInKB inférieure à 1024, comme des enregistrements plus petits utilisent uniquement l’espace nécessaire pour l’enregistrement de plus petit. Nous pensons que cette valeur doit être modifiée uniquement dans de rares cas.

Les paramètres SharedLogId et SharedLogPath sont toujours utilisés ensemble à un service d’utiliser un journal distinct et partagé à partir du journal partagé par défaut pour le nœud. Pour plus d’efficacité, que de nombreux services que possible doivent spécifier le même journal partagé. Les fichiers de journaux partagés doivent être placés sur des disques qui sont utilisés uniquement pour le fichier journal partagé, afin de réduire la contention de mouvement de la tête. Nous espérons que ces valeurs devra être changé uniquement dans de rares cas.
