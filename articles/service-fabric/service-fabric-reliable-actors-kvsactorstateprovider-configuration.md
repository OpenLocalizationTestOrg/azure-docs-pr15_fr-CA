<properties
   pageTitle="Vue d’ensemble de la configuration d’Azure Service Fabric fiable acteurs KVSActorStateProvider | Microsoft Azure"
   description="Obtenir des informations sur la configuration des acteurs avec état d’Azure Fabric de Service de type KVSActorStateProvider."
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
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configuration des acteurs fiables--KVSActorStateProvider
Vous pouvez modifier la configuration par défaut de KVSActorStateProvider en modifiant le fichier settings.xml généré dans la racine du package Visual Studio de Microsoft dans le dossier Config de l’acteur spécifié.

Le runtime d’Azure Fabric de Service recherche des noms de section prédéfinie dans le fichier settings.xml et utilise les valeurs de configuration lors de la création des composants runtime sous-jacent.

>[AZURE.NOTE] Effectuez **pas** supprimer ou modifier les noms de section des configurations suivantes dans le fichier settings.xml généré dans la solution Visual Studio.

## <a name="replicator-security-configuration"></a>Configuration de la sécurité Replicator
Les configurations de sécurité Replicator utilisées pour sécuriser le canal de communication utilisé lors de la réplication. Cela signifie que les services ne peuvent pas voir l’autre le trafic de réplication, garantissant que les données de disponibilité élevée sont également sécurisées.
Par défaut, une section de configuration de sécurité vide empêche la sécurité de la réplication.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuration de Replicator
Configurations de réplicateur configurer le réplicateur qui est responsable de l’état du fournisseur d’état acteur extrêmement fiable.
La configuration par défaut est générée par le modèle Visual Studio et devrait suffire. Cette section décrit les configurations supplémentaires qui sont disponibles pour optimiser le réplicateur.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Noms de configuration

|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondes|0.015|Période pour laquelle le réplicateur sur le secondaire attend après avoir reçu une opération avant de l’envoyer à un accusé de réception principal. Tous les accusés de réception à envoyer pour les opérations traitées dans cet intervalle sont envoyés sous la forme d’une réponse.|
|ReplicatorEndpoint|N/A|Aucune valeur par défaut : paramètre requis|Adresse IP et le port que le réplicateur principal/secondaire utilisera pour communiquer avec d’autres fabricants dans le réplica définissent. Cela doit faire référence à un point de terminaison TCP ressource dans le manifeste du service. Consultez les [ressources de manifeste de Service](service-fabric-service-manifest-resources.md) pour en savoir plus sur la définition des ressources du point de terminaison dans le manifeste du service. |
|RetryInterval|Secondes|5|Période après laquelle le réplicateur de retransmission un message si elle ne reçoit pas un accusé de réception pour une opération.|
|MaxReplicationMessageSize|Octets|50 MO|Taille maximale des données de réplication qui peuvent être transmises dans un seul message.|
|MaxPrimaryReplicationQueueSize|Nombre d’opérations|1024|Nombre maximal d’opérations dans la file d’attente principale. Une opération est libérée après que le réplicateur principal reçoit un accusé de réception auprès de tous les fabricants secondaires. Cette valeur doit être supérieure à 64 et une puissance de 2.|
|MaxSecondaryReplicationQueueSize|Nombre d’opérations|2048|Nombre maximal d’opérations dans la file d’attente secondaire. Une opération est libérée une fois son état hautement disponible par le biais de persistance. Cette valeur doit être supérieure à 64 et une puissance de 2.|

## <a name="store-configuration"></a>Configuration de la banque
Magasin de configurations permettent de configurer le magasin local qui est utilisé pour conserver l’état en cours de réplication.
La configuration par défaut est générée par le modèle Visual Studio et devrait suffire. Cette section décrit les configurations supplémentaires qui sont disponibles pour optimiser le magasin local.

### <a name="section-name"></a>Nom de la section
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Noms de configuration

|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|MaxAsyncCommitDelayInMilliseconds|Millisecondes|200|Définit la valeur maximale que le traitement par lots d’intervalle pour les validations de magasin local durable.|
|MaxVerPages|Nombre de pages|16384|Le nombre maximal de pages de version dans le local de stocker la base de données. Il détermine le nombre maximal de transactions en attente.|

## <a name="sample-configuration-file"></a>Exemple de fichier de configuration

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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
