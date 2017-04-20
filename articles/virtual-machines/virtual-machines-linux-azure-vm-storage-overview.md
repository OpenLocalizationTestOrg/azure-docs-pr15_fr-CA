<properties
  pageTitle="Azure et stockage de Virtual Media Linux | Microsoft Azure"
  description="Décrit les Azure Standard et Premium stockage avec des machines virtuelles de Linux."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines-linux"
  authors="vlivech"
  manager="timlt"
  editor=""/>

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure"
  ms.date="10/04/2016"
  ms.author="v-livech"/>

# <a name="azure-and-linux-vm-storage"></a>Stockage Azure et Linux VM

Le stockage Azure est la solution de stockage cloud pour les applications modernes qui s’appuient sur la durabilité, de disponibilité et d’évolutivité pour répondre aux besoins de leurs clients.  Outre ce qui permet aux développeurs de créer des applications à grande échelle pour prendre en charge de nouveaux scénarios, le stockage Azure fournit la base du stockage pour Azure Virtual Machines.

## <a name="azure-storage-standard-and-premium"></a>Stockage Azure : Standard et Premium

Azure VM peut être créées sur des disques de stockage standard ou des disques de stockage de prime.  Lorsque vous utilisez le portail pour choisir votre ordinateur virtuel, vous devez activer/désactiver une liste déroulante sur l’écran des notions de base pour afficher les disques standard et premium.  La capture d’écran ci-dessous met en évidence ce menu Activer/désactiver.  Lors de la passe à SSD, seul le stockage premium VMs activés seront affiche, toutes assorties de SSD lecteurs.  Lorsque basculée sur le disque dur, stockage standard activés VMs rotation sauvegardés les disques s’affichera, avec stockage premium SSD bénéficient des ordinateurs virtuels.

  ![Écran1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

Lors de la création d’une machine virtuelle à partir de la `azure-cli` vous avez le choix entre standard et premium lors du choix de la taille de la mémoire virtuelle via le `-z` ou `--vm-size` cli indicateur.

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>Créer un ordinateur virtuel avec le stockage standard VM sur la cli

L’indicateur cli `-z` choisit Standard_A1 avec A1 est un standard de stockage de base Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>Créer un ordinateur virtuel avec un stockage de prime sur l’infrastructure du langage commun

L’indicateur cli `-z` choisit Standard_DS1 avec DS1 est un stockage de prime en fonction des Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Stockage standard

Stockage Standard Azure est le type par défaut de stockage.  Stockage standard est rentable tout en étant performant.  

## <a name="premium-storage"></a>Stockage de prime

Stockage de prime Azure offre une prise en charge de disque hautes performances et à faible latence pour les machines virtuelles exécutant des charges d’e/S intensives. Disques de machine virtuelle (VM) qui utilisent le stockage de la prime stockent les données sur les disques (SSD). Vous pouvez migrer des disques de mémoire virtuelle de votre application vers le stockage Azure Premium pour tirer parti de la vitesse et les performances de ces disques.

Fonctionnalités de stockage de prime :

- Prime de disques de stockage : Stockage Azure de prime prend en charge les disques VM qui peuvent être associés à la série DS, DSv2 ou GS Azure VM.

- Blob de Page prime : Stockage de prime prend en charge les objets BLOB Azure de Page, qui sont utilisés pour contenir des disques persistantes pour les Machines virtuelles Azure (VMs).

- Premium stockage redondant localement : Un compte de stockage de la prime seulement prend en charge les localement redondants stockage (LRS) en tant que l’option de réplication et conserve trois copies des données au sein d’une seule région.

- [Stockage de prime](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Stockage de prime prise en charge des machines virtuelles

Stockage de prime prend en charge la gamme DS, DSv2, série GS-series et les Machines virtuelles de Azure Fs-series (VMs). Vous pouvez utiliser des disques de stockage Standard et Premium avec stockage Premium prise en charge des ordinateurs virtuels. Mais vous ne pouvez pas utiliser disques de stockage de la prime sur la série de machine virtuelle, qui n’est pas compatibles à prime stockage.

Voici les Distributions Linux nous validés avec le stockage de la prime.

| Distribution | Version                 | Noyau pris en charge    |
|--------------|-------------------------|---------------------|
| Ubuntu       | 12.04                   | 3.2.0-75.110+       |
| Ubuntu       | 14.04                   | 3.13.0-44.73+       |
| Debian       | 7.x, 8.x                | 3.16.7-ckt4-1+      |
| SLES         | SLES 12                 | 3.12.36-38.1+       |
| SLES         | SLES 11 SP4             | 3.0.101-0.63.1+     |
| CoreOS       | 584.0.0+                | 3.18.4+             |
| CentOS       | 6.5, 6.6, 6.7, 7.0, 7.1 | 3.10.0-229.1.2.el7+ |
| RHEL         | 6.8 +, 7.2 +              |                     |


## <a name="file-storage"></a>Stockage de fichiers

Stockage de fichier Azure offre des partages de fichiers dans le nuage en utilisant le protocole SMB standard. Fichiers d’Azure, vous pouvez migrer des applications d’entreprise s’appuient sur des serveurs de fichiers pour Azure. Applications qui s’exécutent dans Azure peuvent monter facilement les partages de fichiers à partir des ordinateurs virtuels Azure exécutant Linux. Et avec la version la plus récente de stockage de fichiers, vous pouvez également monter un partage de fichiers à partir d’une application locale qui prend en charge SMB 3.0.  Étant donné que les partages de fichiers sont des partages SMB, vous pouvez y accéder via les API du système de fichiers standard.

Stockage de fichiers est construit sur la même technologie que le stockage Blob, Table et file d’attente, afin que le stockage de fichiers offre la redondance géographique qui est intégrée à la plate-forme de stockage Azure, durabilité, d’évolutivité et disponibilité. Pour plus d’informations sur les limites et les objectifs de performances de stockage de fichier, reportez-vous à la section des cibles de performances et de l’évolutivité du stockage Azure.

- [Comment faire pour utiliser le stockage Azure avec Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Stockage à chaud

Le niveau de stockage à chaud Azure est optimisé pour stocker les données fréquemment sollicitées.  Stockage à chaud est le type de stockage par défaut pour les magasins de blob.

## <a name="cool-storage"></a>Stockage sympa

La couche de stockage pratique Azure est optimisée pour le stockage des données rarement consultées et de durée de vie longue. Exemple utilisation de stockage sympa, citons les sauvegardes, contenu multimédia, des données scientifiques, de conformité et données d’archivage. En règle générale, toutes les données auxquelles vous accédez rarement sont un candidat idéal pour le stockage de refroidir.

|                             | Niveau de stockage à chaud      | Niveau de frais de stockage     |
|:----------------------------|:---------------------:|:---------------------:|
| Disponibilité                | 99,9 %                 | 99 %                   |
| Disponibilité (lectures GRS-RA) | 99,99 %                | 99,9 %                 |
| Frais d’utilisation               | Coûts de stockage élevés  | Réduction des coûts de stockage   |
|                             | Accès inférieur          | Accès plus élevé         |
|                             | et les coûts de transaction | et les coûts de transaction |


## <a name="redundancy"></a>Redondance

Les données de votre compte de stockage Microsoft Azure sont toujours répliquées pour garantir la durabilité et une haute disponibilité, les contrats du stockage Azure SLA même face à des défaillances matérielles en régime transitoire.

Lorsque vous créez un compte de stockage, vous devez sélectionner une des options de réplication suivantes :

- Stockage redondant localement (LRS)
- Stockage redondant de zone (ZRS)
- Stockage redondant geo (GRS)
- Accès en lecture de stockage redondant geo (GRS-RA)

### <a name="locally-redundant-storage"></a>Stockage redondant localement

Stockage redondant localement (LRS) réplique les données au sein de la région dans laquelle vous avez créé votre compte de stockage. Pour optimiser la durabilité, chaque demande effectuée sur les données de votre compte de stockage est répliquée trois fois. Ces trois répliques résident dans les domaines de pannes distinct et mise à niveau.  Une requête est retournée avec succès qu’une fois qu’il a été écrit dans tous les réplicas de trois.

### <a name="zone-redundant-storage"></a>Stockage redondant de zone

Stockage redondant de zone (ZRS) réplique vos données sur deux à trois installations au sein d’une même région ou entre les deux régions, offrant une durabilité élevée que LRS. Si votre compte de stockage a ZRS activée, vos données sont durables, même en cas de défaillance à l’une des installations.

### <a name="geo-redundant-storage"></a>Stockage redondant geo

Stockage redondant geo (GRS) réplique vos données sur une zone secondaire des centaines de miles en dehors de la zone primaire. Si votre compte de stockage a GRS activée, vos données sont durables, même dans le cas d’une panne régionale complète ou un sinistre dans lequel la région principale n’est pas récupérable.

### <a name="read-access-geo-redundant-storage"></a>Stockage redondant geo d’accès en lecture

Stockage geo redondant d’accès en lecture (RA-GRS) optimise la disponibilité de votre compte de stockage, en fournissant un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la réplication entre les deux régions de GRS. Dans le cas où les données ne sont plus disponibles dans la région principale, votre application peut lire des données à partir de la zone secondaire.

Pour une connaissance approfondie voir de redondance de stockage Azure :

- [Réplication du stockage Azure](../storage/storage-redundancy.md)

## <a name="scalability"></a>Évolutivité

Stockage Azure étant hautement évolutive, vous pouvez stocker et processus des centaines de téraoctets de données pour prendre en charge les scénarios de données volumineuses requises par l’analyse scientifique, financière et applications multimédia. Ou bien, vous pouvez stocker les petites quantités de données requises pour un site Web de petite entreprise. Chaque fois que vos besoins sont, vous payez uniquement les données que vous stockez. Stockage Azure actuellement stocke des dizaines de codage des objets de client unique et gère des millions de requêtes par seconde en moyenne.

Pour les comptes de stockage standard : un compte de stockage standard a une vitesse maximale de la demande totale de 20 000 IOPS. L’e/s total sur l’ensemble de vos disques de l’ordinateur virtuel dans un compte de stockage standard ne doit pas dépasser cette limite.

Pour les comptes de stockage premium : un compte de stockage premium a un débit total maximal de Gbits/50 s. Le débit total sur l’ensemble de vos disques de l’ordinateur virtuel ne doit pas dépasser cette limite.

## <a name="availability"></a>Disponibilité

Nous garantissons qu’au moins 99,99 % (99,9 % pour la couche d’accès aux frais) du temps, nous va réussir à traiter les demandes pour lire des données à partir des comptes de stockage redondant de géo-accès en lecture (RA-GRS), sous réserve que les échecs de tentatives de lire les données de la zone primaire sont retentées sur la zone secondaire.

Nous garantissons qu’au moins 99,9 % (99 % pour la couche d’accès aux frais) du temps, nous va réussir à traiter les demandes pour lire des données à partir de stockage localement redondants (LRS), stockage redondant de Zone (ZRS) et les comptes de stockage redondants de Geo (GRS).

Nous garantissons qu’au moins 99,9 % (99 % pour la couche d’accès aux frais) du temps, nous va réussir à traiter les demandes pour écrire des données dans le stockage local redondants (LRS), Zone redondants stockage (ZRS), comptes de stockage redondants de Geo (GRS) et des comptes de stockage redondant de géo-accès en lecture (RA-GRS).

- [Azure SLA pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)


## <a name="regions"></a>Régions

Azure est généralement disponible dans 30 régions du monde et a annoncé pour 4 régions supplémentaires. Expansion géographique est une priorité pour Azure car il permet à nos clients atteindre des performances plus élevées, et il prend en charge ses besoins et les préférences en ce qui concerne l’emplacement des données.  Azures dernière la zone de lancement est en Allemagne.

L’Allemagne de Cloud Microsoft fournit une option différenciée pour les services de Cloud Microsoft déjà disponibles en Europe, création de meilleures opportunités de l’innovation et croissance économique pour les partenaires hautement réglementés et les clients en Allemagne, de l’Union européenne (UE) et de l’Association européenne de libre-échange (AELE).

Les données client dans ces nouveaux centres de données, de Magdeburg-Francfort, sont gérées sous le contrôle d’un tiers de confiance de données, T-Systems International, une société allemande indépendante et une filiale de Deutsche Telekom. Services en nuage commercial de Microsoft dans les centres de données conformes à la gestion des règlements de données allemandes et fournir aux clients des options supplémentaires de comment et où les données sont traitées.


- [Mappage de régions Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Sécurité

Le stockage Azure fournit un ensemble complet de fonctionnalités de sécurité qui ensemble permettent aux développeurs de créer des applications sécurisées. Le compte de stockage lui-même peut être sécurisé à l’aide du contrôle d’accès basée sur les rôles et Azure Active Directory. Données peuvent être sécurisées en transit entre une application et d’Azure à l’aide de cryptage côté Client, HTTPS ou SMB 3.0. Données peuvent être définies pour être automatiquement cryptés lors de l’écriture dans le stockage Azure à l’aide du cryptage de Service de stockage (SSE). Disques du système d’exploitation et des données utilisés par les machines virtuelles peuvent être définies afin d’être crypté à l’aide du cryptage de disque Azure. Accès délégué aux objets de données dans le stockage Azure peuvent être accordées à l’aide de Signatures de l’accès partagé.

### <a name="management-plane-security"></a>Sécurité du plan d’administration

Le plan de gestion comprend les ressources utilisées pour gérer votre compte de stockage. Dans cette section, nous allons parler du modèle de déploiement d’Azure le Gestionnaire de ressources et comment utiliser le contrôle d’accès basée sur les rôles (RBAC) pour contrôler l’accès à vos comptes de stockage. Nous parlerons également la gestion de vos clés de compte de stockage et comment les régénérer.

### <a name="data-plane-security"></a>Sécurité de plan de données

Dans cette section, nous allons étudier l’accès aux objets de données réelles dans votre compte de stockage, par exemple les blobs, les fichiers, les files d’attente et les tables, à l’aide de Signatures de l’accès partagé et stockées les stratégies d’accès. Nous allons aborder les associations de sécurité de niveau de service et les associations de sécurité au niveau des comptes. Nous verrons également comment limiter l’accès à une adresse IP spécifique (ou plage d’adresses IP), le protocole utilisé pour HTTPS de limiter et comment révoquer une Signature accès partagé sans attendre qu’il expire.

## <a name="encryption-in-transit"></a>Cryptage en Transit

Cette section explique comment sécuriser des données lors de son transfert dans ou en dehors du stockage Azure. Nous nous pencherons sur l’utilisation recommandée de HTTPS et le chiffrement utilisé par les PME/PMI 3.0 Azure partages de fichiers. Nous allons également au cryptage côté Client, ce qui vous permet de chiffrer les données avant leur transfert dans le stockage dans une application cliente et pour décrypter les données après transfert de stockage.

## <a name="encryption-at-rest"></a>Cryptage au repos

Nous parlerons de stockage Service de cryptage (SSE) et comment vous pouvez l’activer pour un compte de stockage, entraînant votre bloc BLOB, BLOB de la page et ajouter des objets BLOB sont automatiquement cryptés lors de l’écriture dans le stockage Azure. Nous examinerons également comment vous pouvez utiliser le cryptage de disque Azure et découvrez les différences de base et les cas de cryptage de disque et de SSE par rapport au cryptage côté Client. Nous examinerons brièvement conformité FIPS pour les ordinateurs du gouvernement américain.

- [Guide de sécurité de stockage Azure](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>Réduction des coûts

- [Coût de stockage](https://azure.microsoft.com/pricing/details/storage/)

- [Calculateur des coûts de stockage](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Limites de stockage

- [Limites du Service de stockage](../azure-subscription-service-limits.md#storage-limits)
