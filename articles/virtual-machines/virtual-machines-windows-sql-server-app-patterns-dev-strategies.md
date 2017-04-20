<properties
    pageTitle="Modèles d’Application SQL Server sur des machines virtuelles | Microsoft Azure"
    description="Cet article traite les modèles d’application pour SQL Server sur Azure VM. Il fournit les développeurs et les architectes de solution une base pour la conception et l’architecture de la bonne application."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="luisherring"
    manager="jhubbard"
    editor=""
    tags="azure-service-management,azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="lvargas" />

# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Modèles d’application et les stratégies de développement de SQL Server sur des Machines virtuelles Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="summary"></a>Résumé :
Déterminer quel modèle d’application ou les modèles à utiliser pour vos applications basées sur le serveur SQL Azure environnement est une décision de conception important et nécessite une bonne compréhension du fonctionnement de SQL Server et chaque composant de l’infrastructure d’Azure. Avec les SQL Server dans les Services d’Infrastructure Azure, vous pouvez facilement migrer, gérer et surveiller vos applications SQL Server existantes Windows Server intégrée sur des machines virtuelles dans Azure.

L’objectif de cet article est de fournir les développeurs et les architectes de solution une base pour la conception, qu’il peut utiliser lors de la migration d’applications existantes vers Azure ainsi que de développer de nouvelles applications dans Azure et l’architecture de la bonne application.

Pour chaque modèle d’application, vous trouverez un scénario sur site, sa solution de cloud respective et les recommandations techniques connexes. En outre, l’article aborde les stratégies de développement Azure spécifiques afin que vous pouvez concevoir correctement vos applications. En raison des nombreux modèles application possible, il est recommandé que les architectes et les développeurs doivent choisir le modèle adapté pour leurs applications et leurs utilisateurs.

**Des contributeurs techniques :** Luis Carlos Vargas hareng, Madhan Arumugam Ramakrishnan

**Réviseurs techniques :** Corey Sanders, Drew McDaniel, Narayan Annamalai, Mashkowski de Nir, Sanjay Mishra, Coriani Silvano, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introduction

Vous pouvez développer différents types d’applications n-couche en séparant les composants des couches application différents sur des ordinateurs différents, ainsi que dans des composants séparés. Par exemple, vous pouvez placer l’application cliente et un ordinateur, la couche web frontal et composants de couche d’accès aux données dans un autre ordinateur et un niveau de base de données back-end dans un autre ordinateur, les composants des règles d’entreprise. Ce type de structure permet d’isoler les chaque couche de l’autre. Si vous modifiez la provenance des données, vous n’avez pas besoin de modifier la client ou une application web, mais uniquement les composants de niveau de l’accès aux données.

Une application de type *n-couche* inclut la couche de présentation, la couche métier et la couche de données :


| Couche              | Description                                                                                                                                                                     |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Présentation** | La *couche de présentation* (couche web, couche frontale) est la couche dans laquelle les utilisateurs interagissent avec une application.                                                                      |
| **Entreprise**     | Le *niveau de l’entreprise* (niveau intermédiaire) est la couche que la présentation et la couche de données utilisent pour communiquer entre eux et inclut les fonctionnalités de base du système. |
| **Données**         | La *couche données* correspond essentiellement au serveur qui stocke les données d’une application (par exemple, un serveur exécutant SQL Server).                                                             |

Couches d’application décrivent les regroupements logiques des fonctionnalités et des composants d’une application ; alors que les niveaux décrire la distribution physique des fonctionnalités et des composants sur des serveurs physiques distincts, les ordinateurs, les réseaux ou les emplacements distants. Les couches d’une application peuvent résider sur le même ordinateur physique (la même couche) ou peuvent être distribués sur des ordinateurs distincts (n-couche) et les composants de chaque couche de communiquent avec les composants des autres couches via des interfaces bien définies. Vous pouvez considérer le terme niveau comme faisant référence à des modèles de distribution physiques par exemple à deux couches et multicouches à trois niveaux. Un **modèle d’application de couche 2** contient deux niveaux de l’application : serveur d’applications et le serveur de base de données. La communication directe se produit entre le serveur d’applications et le serveur de base de données. Le serveur d’application contient des composants web de couche et de niveau d’entreprise. Dans le **modèle d’application de niveau 3**, il y a trois niveaux de l’application : serveur web, serveur d’application, qui contient la couche de logique métier et/ou les composants d’accès aux données métiers couche et le serveur de base de données. La communication entre le serveur web et le serveur de base de données se produit sur le serveur d’application. Pour obtenir des informations détaillées sur les niveaux et les couches de l’application, voir le [Guide Architecture d’applications Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Avant de commencer la lecture de cet article, vous devez avoir des connaissances sur les concepts fondamentaux de SQL Server et d’Azure. Pour plus d’informations, consultez la [Documentation en ligne de SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) et [Azure.com](https://azure.microsoft.com/).

Cet article décrit plusieurs modèles d’application pouvant être adaptés à vos applications simples, ainsi que les applications d’entreprise très complexe. Avant le détaillant chaque modèle, nous vous recommandons que vous devez vous familiariser avec les services de stockage de données disponibles dans Azure, par exemple [Le stockage Azure](../storage/storage-introduction.md), [SQL Azure](../sql-database/sql-database-technical-overview.md)et [SQL Server dans une Machine virtuelle Azure](virtual-machines-windows-sql-server-iaas-overview.md). Pour prendre les meilleures décisions de conception de vos applications, apprendre à utiliser le service de stockage de données clairement.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Choisissez de SQL Server sur une Machine virtuelle Azure, lorsque :

- Vous avez besoin de contrôle sur SQL Server et Windows. Par exemple, cela peut inclure la version de SQL Server des correctifs spéciales, configuration des performances, etc..

- Vous avez besoin d’une compatibilité totale avec SQL Server sur site et que vous souhaitez déplacer des applications existantes vers Azure comme-est.

- Vous voulez exploiter les fonctionnalités de l’environnement Azure mais la base de données de SQL Azure ne prend pas en charge toutes les fonctionnalités que votre application requiert. Cela peut inclure les éléments suivants :

    - **Taille de la base de données**: au moment de la mise à jour de cet article, la base de données de SQL prend en charge une base de données de 1 To de données. Si votre application nécessite plus de 1 To de données et que vous ne souhaitez pas mettre en œuvre les solutions personnalisées ont, il est recommandé d’utiliser SQL Server dans une Machine virtuelle Azure. Pour plus d’informations, reportez-vous à la section [Mise à l’échelle des bases SQL Azure](https://msdn.microsoft.com/library/azure/dn495641.aspx) et [les niveaux de performances et de niveaux de Service de base de données SQL Azure](../sql-database/sql-database-service-tiers.md).
    - **Respect des réglementations HIPAA**: clients de soins de santé et les éditeurs de logiciels indépendants (ISV) peut choisir [SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) au lieu de la [Base de données de SQL Azure](../sql-database/sql-database-technical-overview.md) , car SQL Server dans une Machine virtuelle Azure est couverte par contrat associer des professionnels d’HIPAA (BA). Pour plus d’informations sur la conformité, consultez [Microsoft Azure Trust Center : conformité](https://azure.microsoft.com/support/trust-center/compliance/).
    - **Fonctionnalités au niveau de l’instance**: pour l’instant, base de données de SQL ne prend en charge les fonctionnalités live en dehors de la base de données (par exemple des serveurs liés, l’Agent des travaux, FileStream, Service Broker, etc..). Pour plus d’informations, consultez [instructions de bases de données SQL Azure et des Limitations](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>niveau 1 (simple) : machine virtuelle unique

Dans ce modèle d’application, vous déployez votre application de SQL Server et la base de données à un ordinateur virtuel d’autonome dans Azure. La même machine virtuelle contient votre application/web du client, les composants métier, couche d’accès aux données et le serveur de base de données. La présentation, métier et le code d’accès aux données sont séparées de façon logique, mais se trouvent physiquement dans une machine serveur unique. La plupart des clients commencent par ce modèle d’application et ensuite, ils évoluer en ajoutant plus de rôles web ou d’ordinateurs virtuels à leur système.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez effectuer une migration simple vers la plateforme Azure pour évaluer si la plate-forme répond aux exigences de votre application ou non.

- Vous souhaitez conserver tous les niveaux de l’application hébergés dans le même ordinateur virtuel dans le même centre de données Azure afin de réduire la latence entre les niveaux.

- Vous souhaitez rapidement provisionner le développement et les environnements de test pour de courtes périodes.

- Vous souhaitez effectuer des tests pour les différents niveaux de charge de travail de stress, mais en même temps vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

Le diagramme suivant montre une simple sur site scénario et comment vous pouvez déployer sa solution de cloud activé sur une machine virtuelle unique dans Azure.

![modèle d’application de niveau 1](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Déploiement de la couche métier (logique métier et les composants d’accès aux données) sur le même niveau physique que la couche de présentation peut optimiser les performances de l’application, sauf si vous devez utiliser une couche séparée en raison de problèmes d’évolutivité ou la sécurité.

Dans la mesure où il s’agit pour commencer avec un modèle très courant, vous intéresser l’article suivant sur la migration pour déplacer vos données vers votre ordinateur virtuel de SQL Server : [migration d’une base de données SQL Server sur un ordinateur virtuel d’Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>niveau 3 (simple) : plusieurs machines virtuelles

Dans ce modèle d’application, vous déployez une application de niveau 3 dans Azure en plaçant chaque couche de l’application dans une machine virtuelle différente. Cela fournit un environnement souple pour une évolution verticale et horizontale des scénarios simples. Lorsqu’une machine virtuelle contient votre application/web du client, l’autre un héberge vos composants métier et autre héberge le serveur de base de données.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez effectuer une migration des applications de base de données complexe à Azure Virtual Machines.

- Vous voulez que les différents niveaux d’application devant figurer dans les différentes régions. Par exemple, vous avez peut-être partagé les bases de données qui sont déployés dans plusieurs régions pour les rapports.

- Vous souhaitez déplacer des applications d’entreprise à partir des plates-formes virtualisées sur site pour Azure Virtual Machines. Pour plus d’informations sur les applications d’entreprise, voir [ce qui est une Application d’entreprise](https://msdn.microsoft.com/library/aa267045.aspx).

- Vous souhaitez rapidement provisionner le développement et les environnements de test pour de courtes périodes.

- Vous souhaitez effectuer des tests pour les différents niveaux de charge de travail de stress, mais en même temps vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

Le diagramme suivant illustre comment vous pouvez placer une application à 3 couches simple dans Azure en plaçant chaque couche de l’application dans une machine virtuelle différente.

![modèle d’application de couche 3](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Dans ce modèle d’application, est seule machine virtuelle (VM) dans chaque couche. Si vous avez plusieurs ordinateurs virtuels dans Azure, nous vous recommandons de configurer un réseau virtuel. [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md) crée une limite de sécurité approuvés et permet également aux machines virtuelles de communiquer entre eux sur l’adresse IP privée. En outre, assurez-vous toujours que toutes les connexions Internet atteindre uniquement la couche de présentation. Lorsqu’ils suivent ce modèle d’application, gérer les règles de groupe de sécurité de réseau pour contrôler l’accès. Pour plus d’informations, reportez-vous à la section [Autoriser l’accès externe à votre machine virtuelle via le portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Dans le diagramme, les protocoles Internet peut être TCP, UDP, HTTP ou HTTPS.

>[AZURE.NOTE] Configuration d’un réseau virtuel dans Azure est gratuit. Toutefois, vous êtes chargé de la passerelle VPN qui se connecte à local. Cette taxe est basée sur la durée de connexion est mis en service et disponible.

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>couche 2 et couche 3 avec la couche présentation avec montée

Dans ce modèle d’application, vous déployez l’application de base de données de niveau 2 ou niveau 3 pour Azure Virtual Machines en plaçant chaque couche de l’application dans une machine virtuelle différente. En outre, vous mettre à l’échelle à la couche de présentation en raison de l’augmentation du volume des demandes des clients.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez déplacer des applications d’entreprise à partir des plates-formes virtualisées sur site pour Azure Virtual Machines.

- Vous souhaitez évoluer de la couche de présentation en raison de l’augmentation du volume des demandes des clients.

- Vous souhaitez rapidement provisionner le développement et les environnements de test pour de courtes périodes.

- Vous souhaitez effectuer des tests pour les différents niveaux de charge de travail de stress, mais en même temps vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

- Vous voulez posséder un environnement d’infrastructure qui peut évoluer à la demande.

Le diagramme suivant illustre comment vous pouvez placer les niveaux de l’application sur plusieurs machines virtuelles dans Azure en faisant évoluer de la couche de présentation en raison de l’augmentation du volume des demandes des clients. Comme illustré dans le diagramme, équilibrage de la charge Azure est responsable de la distribution du trafic sur plusieurs machines virtuelles et également déterminer quel serveur web pour vous connecter à. La présence de plusieurs instances des serveurs web situés derrière un équilibreur de charge garantit la haute disponibilité de la couche de présentation.

![Modèle d’application - présentation couche mise à l’échelle](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Méthodes conseillées pour les modèles de couche 2, 3 niveaux ou à n niveaux qui ont plusieurs ordinateurs virtuels dans un niveau

Il est recommandé de placer les ordinateurs virtuels qui appartiennent au même niveau dans le même service de cloud et dans la même la disponibilité définie. Par exemple, placer un ensemble de serveurs web dans un ensemble de serveurs de base de **CloudService2** et de **AvailabilitySet2**et de **AvailabilitySet1** et de **CloudService1** . Une disponibilité définie dans Azure vous permet de placer les nœuds haute disponibilité dans des domaines distincts des pannes et de mise à niveau de domaines.

Pour tirer parti de plusieurs instances de machine virtuelle d’un niveau, vous devez configurer l’équilibreur de charge Azure entre les niveaux de l’application. Pour configurer l’équilibrage de charge de chaque niveau, vous devez créer un point de terminaison avec équilibrage de charge sur les ordinateurs virtuels de chaque niveau séparément. Pour un niveau spécifique, tout d’abord créer des ordinateurs virtuels dans le même service de cloud. Cela garantit qu’ils ont la même adresse IP virtuelle publique. Ensuite, créez un point de terminaison sur l’un des ordinateurs virtuels sur ce niveau. Ensuite, affectez le même point de terminaison pour les autres ordinateurs virtuels à ce niveau pour l’équilibrage de charge. En créant un ensemble équilibré, vous répartissez le trafic entre plusieurs machines virtuelles et également permettre l’équilibrage de charge déterminer le nœud de connexion lors de la défaillance d’un nœud d’ordinateur virtuel back-end. Par exemple, la présence de plusieurs instances des serveurs web situés derrière un équilibreur de charge garantit la haute disponibilité de la couche de présentation.

Pour obtenir les meilleurs résultats, assurez-vous toujours que toutes les connexions internet allez d’abord à la couche de présentation. La couche de présentation accède à la couche métier et la couche d’entreprise accède ensuite à la couche données. Pour plus d’informations sur la façon d’autoriser l’accès à la couche de présentation, reportez-vous à la section [Autoriser l’accès externe à votre machine virtuelle via le portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Notez que l’équilibreur de charge dans Azure fonctionne de la même charger équilibreurs dans un environnement sur site. Pour plus d’informations, voir [équilibrage de la charge pour les services d’infrastructure Azure](virtual-machines-windows-load-balance.md).

En outre, nous vous recommandons de configurer un réseau privé pour vos ordinateurs virtuels à l’aide de réseau virtuel d’Azure. Cela leur permet de communiquer entre eux sur l’adresse IP privée. Pour plus d’informations, reportez-vous à la section [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>couche 2 et couche 3 avec montée de niveau entreprise

Dans ce modèle d’application, vous déployez l’application de base de données de niveau 2 ou niveau 3 pour Azure Virtual Machines en plaçant chaque couche de l’application dans une machine virtuelle différente. En outre, vous pouvez souhaiter distribuer les composants de serveur d’application à plusieurs machines virtuelles en raison de la complexité de votre application.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez déplacer des applications d’entreprise à partir des plates-formes virtualisées sur site pour Azure Virtual Machines.

- Vous souhaitez distribuer les composants de serveur d’application à plusieurs machines virtuelles en raison de la complexité de votre application.

- Vous voulez déplacer lourds de logique métier locale LOB les applications (line-of-business) pour Azure Virtual Machines. Les applications métiers sont un ensemble d’applications critiques d’ordinateur qui sont essentielles à une entreprise, tels que comptabilité, ressources humaines (RH), paie, gestion de la chaîne d’approvisionnement et les applications de planification des ressources en cours d’exécution.

- Vous souhaitez rapidement provisionner le développement et les environnements de test pour de courtes périodes.

- Vous souhaitez effectuer des tests pour les différents niveaux de charge de travail de stress, mais en même temps vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

- Vous voulez posséder un environnement d’infrastructure qui peut évoluer à la demande.

Le diagramme suivant illustre un scénario local et sa solution de cloud activé. Dans ce scénario, vous placez les niveaux de l’application sur plusieurs machines virtuelles dans Azure par la mise à l’échelle la couche d’entreprise, qui contient la couche de logique métier et les composants d’accès aux données. Comme illustré dans le diagramme, équilibrage de la charge Azure est responsable de la distribution du trafic sur plusieurs machines virtuelles et également déterminer quel serveur web pour vous connecter à. La présence de plusieurs instances des serveurs d’applications derrière un équilibreur de charge garantit la haute disponibilité de la couche d’entreprise. Pour plus d’informations, consultez [méthodes conseillées pour le niveau 2, niveau-3 ou modèles d’architecture à n niveaux qui ont plusieurs machines virtuelles dans un niveau](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Modèle d’application avec l’entreprise niveau mise à l’échelle](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>couche 2 et 3 de la couche présentation et cloisonnée de niveaux entreprise et HADR

Dans ce modèle d’application, déployer l’application de base de données de niveau 2 ou niveau 3 pour Azure Virtual Machines, distribution de la couche de présentation (serveur web) et les composants (serveur d’application) la couche métier à plusieurs machines virtuelles. En outre, vous implémentez des solutions haute disponibilité et de reprise pour vos bases de données sur des machines virtuelles Azure.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez déplacer des applications d’entreprise à partir des plates-formes virtualisées sur site vers Azure en mettant en œuvre SQL Server haute disponibilité et reprise après sinistre.

- Vous souhaitez mettre à l’échelle de la couche de présentation et la couche d’entreprise en raison d’une augmentation du volume des demandes des clients et de la complexité de votre application.

- Vous souhaitez rapidement provisionner le développement et les environnements de test pour de courtes périodes.

- Vous souhaitez effectuer des tests pour les différents niveaux de charge de travail de stress, mais en même temps vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

- Vous voulez posséder un environnement d’infrastructure qui peut évoluer à la demande.

Le diagramme suivant illustre un scénario local et sa solution de cloud activé. Dans ce scénario, vous mettre à l’échelle la couche de présentation et les composants de la couche métier sur plusieurs machines virtuelles dans Azure. En outre, vous implémentez haute disponibilité et reprise après sinistre les techniques de récupération (HADR) pour les bases de données SQL Server dans Azure.

Plusieurs copies d’une application en cours d’exécution dans les différents ordinateurs virtuels Assurez-vous que vous sont demandes équilibrage entre eux. Lorsque vous avez plusieurs machines virtuelles, vous devez vous assurer que tous vos ordinateurs virtuels sont accessibles et en cours d’exécution à un moment donné dans le temps. Si vous configurez l’équilibrage de charge, équilibreur de charge Azure effectue le suivi de l’état de santé des ordinateurs virtuels et dirige les appels entrants vers les nœuds de machine virtuelle sains fonctionne correctement. Pour plus d’informations sur la façon de configurer l’équilibrage de la charge des machines virtuelles, consultez [services d’infrastructure Azure équilibrage de la charge](virtual-machines-windows-load-balance.md). Présence de plusieurs instances de serveurs web et d’application derrière un équilibreur de charge garantit la haute disponibilité des présentation et les niveaux métier.

![Évolutivité et haute disponibilité](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Méthodes conseillées pour les modèles d’application nécessitant SQL HADR

Lorsque vous configurez SQL Server haute disponibilité et de reprise après sinistre dans Azure Virtual Machines, la configuration d’un réseau virtuel pour vos ordinateurs virtuels à l’aide de [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md) est obligatoire.  Machines virtuelles au sein d’un réseau virtuel ont une adresse IP privée de stable même après une interruption de service, ainsi vous pouvez éviter le temps de mise à jour nécessaire pour la résolution de nom DNS. En outre, le réseau virtuel vous permet d’étendre votre réseau local vers Azure et crée une limite de sécurité approuvé. Par exemple, si votre application a des restrictions de domaine de l’entreprise (par exemple, l’authentification Windows, Active Directory), la configuration de [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md) est nécessaire.

La plupart des clients, qui exécutent le code de production sur Azure, conservez les réplicas primaires et secondaires dans Azure.

Pour des informations détaillées et des didacticiels sur la haute disponibilité et des techniques de récupération d’urgence, consultez [haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>couche 2 et couche 3 à l’aide de machines virtuelles d’Azure et les Services en nuage

Dans ce modèle d’application, vous déployez les 2 ou 3 niveaux application Azure à l’aide de ces deux [Services en nuage Azure](../cloud-services/cloud-services-choose-me.md#tellmecs) (rôles web et worker - plateforme en tant que Service (PaaS)) et les [ordinateurs virtuels Azure](virtual-machines-windows-about.md) (Infrastructure en tant que Service (IaaS)). L’utilisation de [Services en nuage Azure](https://azure.microsoft.com/documentation/services/cloud-services/) pour la couche métier/de couche de présentation et de SQL Server sur [des Machines virtuelles Azure](virtual-machines-windows-about.md) pour la couche données est bénéfique pour la plupart des applications s’exécutant sur Azure. La raison est qu’une instance de calcul en cours d’exécution sur les Services en nuage offre une simplicité de gestion, de déploiement, de surveillance et horizontale.

Avec les Services Cloud, Azure gère l’infrastructure pour vous, effectue la maintenance de routine, correctifs les systèmes d’exploitation et tente de récupérer des défaillances de matériel et de service. Lorsque votre application doit être horizontale, les options horizontale, manuelles et automatiques sont disponibles pour votre projet de service cloud en augmentant ou en diminuant le nombre d’instances ou les ordinateurs virtuels qui sont utilisés par votre application. En outre, vous pouvez utiliser Visual Studio de locaux pour déployer votre application dans un projet de service cloud dans Azure.

En résumé, si vous ne souhaitez pas approfondies des tâches administratives pour les entreprises de présentation/niveau de votre application ne pas exiger une configuration complexe de logiciels ou de système d’exploitation, utiliser les Services en nuage Azure. Si la base de données de SQL Azure ne supporte pas toutes les fonctionnalités que vous recherchez, utilisez SQL Server dans une Machine virtuelle Azure pour la couche données. Exécution d’une application dans Azure Cloud Services et le stockage des données dans Azure Virtual Machines combinant les avantages des deux services. Pour une comparaison détaillée, reportez-vous à la section de cette rubrique sur les [stratégies de développement de comparaison dans Azure](#comparing-web-development-strategies-in-azure).

Dans ce modèle d’application, la couche de présentation inclut un rôle web, qui est un composant de Services en nuage en cours d’exécution dans l’environnement d’exécution Azure et elle est personnalisée pour la programmation d’applications web, prise en charge par IIS et ASP.NET. La couche métier ou back-end inclut un rôle worker, qui est un composant de Services en nuage en cours d’exécution dans l’environnement d’exécution Azure et il est utile pour le développement généralisé et peut effectuer le traitement en arrière-plan pour un rôle web. La couche de base de données réside sur un ordinateur virtuel de SQL Server dans Azure. La communication entre la couche présentation et la couche de base de données se produit directement ou via la couche d’entreprise – composants de rôle de travail.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez déplacer des applications d’entreprise à partir des plates-formes virtualisées sur site vers Azure en mettant en œuvre SQL Server haute disponibilité et reprise après sinistre.

- Vous voulez posséder un environnement d’infrastructure qui peut évoluer à la demande.

- Une base de données SQL Azure ne prend pas en charge toutes les fonctionnalités nécessaires à votre application ou votre base de données.

- Vous souhaitez effectuer des tests pour les différents niveaux de charge de travail de stress, mais en même temps vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

Le diagramme suivant illustre un scénario local et sa solution de cloud activé. Dans ce scénario, vous placez la couche de présentation dans les rôles de web, le niveau de l’entreprise dans les rôles de travail mais la couche de données sur des machines virtuelles dans Azure. Exécuter plusieurs copies de la couche de présentation dans des rôles différents web garantit pour charger des demandes de solde entre eux. Lorsque vous combinez des Services en nuage Azure avec Azure Virtual Machines, nous vous recommandons de configurer [Les réseaux virtuels Azure](../virtual-network/virtual-networks-overview.md) ainsi. Avec un [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md), vous pouvez avoir stables et persistantes les adresses IP privées au sein du même service de cloud dans le nuage. Une fois que vous définissez un réseau virtuel pour vos ordinateurs virtuels et services en nuage, ils peuvent démarrer de communiquer entre eux sur l’adresse IP privée. En outre, ayant des machines virtuelles et les rôles d’Azure web/travailleur sur le même [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md) fournit le faible latence et une connectivité plus sécurisée. Pour plus d’informations, consultez [qu’est un service en nuage](../cloud-services/cloud-services-choose-me.md).

Comme illustré dans le diagramme, Azure équilibreur de charge répartit le trafic sur plusieurs machines virtuelles et détermine quel serveur web ou un serveur d’application pour se connecter à. Présence de plusieurs instances des serveurs web et application derrière un équilibreur de charge garantit la haute disponibilité de la couche de présentation et la couche d’entreprise. Pour plus d’informations, consultez [méthodes conseillées pour les modèles d’application nécessitant SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Modèles d’application avec les Services Cloud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Une autre approche pour implémenter ce modèle d’application consiste à utiliser un rôle web consolidée qui contient la couche de présentation et de composants de la couche métier comme illustré dans le diagramme suivant. Ce modèle d’application est utile pour les applications qui requièrent la création dynamique. Dans la mesure où Azure fournit des nœuds de calcul sans état sur rôles web et worker, nous vous recommandons d’implémenter une logique pour stocker l’état de session à l’aide d’une des technologies suivantes : [La mise en cache d’Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [Le stockage Azure Table](../storage/storage-dotnet-how-to-use-tables.md) ou [Base de données de SQL Azure](../sql-database/sql-database-technical-overview.md).

![Modèles d’application avec les Services Cloud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Modèle avec le Service d’application Azure (applications Web), de la base de données SQL Azure et Azure VM

Le principal objectif de ce modèle d’application est de vous montrer comment combiner l’infrastructure Azure comme un composants de service (IaaS) avec des composants de plate-forme en tant que service (PaaS) Azure dans votre solution. Ce modèle est axé sur la base de données de SQL Azure pour le stockage de données relationnelles. Il n’inclut pas de SQL Server sur une machine virtuelle Azure, qui fait partie de l’infrastructure Azure sous la forme d’une offre de service.

Dans ce modèle d’application, vous déployez une application de base de données Azure en plaçant les couches présentation et métier sur le même ordinateur virtuel et en accédant à une base de données dans les serveurs de base de données SQL Azure (de base de données SQL). Vous pouvez implémenter la couche de présentation à l’aide de solutions de site web IIS traditionnel. Ou bien, vous pouvez implémenter une présentation combinée et la couche d’entreprise à l’aide [d’Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Ce modèle d’application est utile dans les cas suivants :

- Vous avez déjà un serveur de base de données SQL configuré dans Azure et que vous souhaitez tester votre application rapidement.

- Vous souhaitez tester les fonctionnalités d’environnement Azure.

- Vous souhaitez rapidement provisionner le développement et les environnements de test pour de courtes périodes.

- Vos composants d’accès de logique et les données métier peuvent être autonomes dans une application web.

Le diagramme suivant illustre un scénario local et sa solution de cloud activé. Dans ce scénario, vous placez les niveaux de l’application sur une machine virtuelle unique dans Azure et accès aux données dans la base de données de SQL Azure.

![Modèle d’application mixte](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Si vous choisissez d’implémenter un web et la couche application à l’aide d’applications Web de Azure, nous vous conseillons de conserver le niveau intermédiaire ou de l’application en tant que bibliothèques de liens dynamiques (DLL) dans le contexte d’une application web.

De plus, passez en revue les recommandations de la section de [comparaison des stratégies de développement web dans Azure](#comparing-web-development-strategies-in-azure) à la fin de cet article pour en savoir plus sur les techniques de programmation.

## <a name="n-tier-hybrid-application-pattern"></a>Modèle d’application à N niveaux hybrides

Dans le modèle d’application à n niveaux hybrides, vous implémentez votre application dans plusieurs niveaux répartie entre les locaux et Azure. Par conséquent, vous créez un système hybride flexibles et réutilisables, que vous pouvez modifier ou ajouter une couche spécifique sans modifier les autres niveaux. Pour étendre votre réseau d’entreprise vers le nuage, vous utilisez service [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md) .

Ce modèle d’application hybride est utile dans les cas suivants :

- Vous souhaitez créer des applications qui s’exécutent en partie dans le nuage et partiellement sur site.

- Vous souhaitez migrer certains ou tous les éléments d’une application sur site existante vers le nuage.

- Vous souhaitez déplacer des applications d’entreprise à partir des plates-formes virtualisées sur site vers Azure.

- Vous voulez posséder un environnement d’infrastructure qui peut évoluer à la demande.

- Vous souhaitez rapidement provisionner le développement et les environnements de test pour de courtes périodes.

- Vous souhaitez un moyen rentable d’effectuer des sauvegardes de base de données aux applications d’entreprise.

Le diagramme suivant illustre un modèle d’application à n niveaux hybrides qui s’étend sur Azure et locaux. Comme illustré dans le diagramme, infrastructure sur site inclut le contrôleur de domaine [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) pour prendre en charge l’autorisation et l’authentification des utilisateurs. Notez que le diagramme illustre un scénario, où certaines parties de la couche données live dans un centre de données sur site alors que certaines parties de la couche données live dans Azure. En fonction des besoins de votre application, vous pouvez implémenter plusieurs autres scénarios hybride. Par exemple, vous pouvez conserver la couche de présentation et la couche d’entreprise dans un environnement sur site mais la couche données dans Azure.

![Modèle d’application à N niveaux](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Dans Azure, vous pouvez utiliser Active Directory comme annuaire nuage autonome pour votre organisation, ou vous pouvez également intégrer Active Directory existant sur site [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Comme illustré dans le diagramme, les composants de la couche métier peuvent accéder à plusieurs sources de données, telles qu’à [SQL Server dans Azure](virtual-machines-windows-sql-server-iaas-overview.md) via une adresse IP interne privée, de SQL Server sur site via le [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md), ou à la propriété [De base de données SQL](../sql-database/sql-database-technical-overview.md) à l’aide de technologies de fournisseur de données.NET Framework. Dans ce diagramme, la base de données de SQL Azure est un service de stockage de données facultatif.

Dans le modèle d’application à n niveaux hybrides, vous pouvez implémenter le workflow suivant dans l’ordre spécifié :

1. Identifier les applications de base de données d’entreprise devant être déplacée vers le haut pour le cloud à l’aide [Microsoft Assessment and Planning (MAP) Shared Computer Toolkit](http://microsoft.com/map). Le mappage Shared Computer Toolkit rassemble les données d’inventaire et de performances à partir des ordinateurs que vous envisagez pour la virtualisation et fournit des recommandations sur la capacité et la planification de l’évaluation.

1. Planifier les ressources et la configuration requise de la plateforme Azure, tels que les comptes de stockage et les machines virtuelles.

1. Configurer la connectivité réseau entre le réseau d’entreprise locaux et [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md). Pour configurer la connexion entre les réseau d’entreprise locaux et une machine virtuelle dans Azure, utilisez une des deux méthodes suivantes :

    1. Établir une connexion entre des locaux et Azure via des points de terminaison publics sur une machine virtuelle dans Azure. Cette méthode fournit une installation facile et vous permet d’utiliser l’authentification SQL Server sur votre ordinateur virtuel. En outre, configurer vos règles de groupe de sécurité de réseau pour contrôler le trafic public de la machine virtuelle. Pour plus d’informations, reportez-vous à la section [Autoriser l’accès externe à votre machine virtuelle via le portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).

    1. Établir une connexion entre des locaux et Azure par biais du tunnel Azure Virtual Private network (VPN). Cette méthode vous permet d’étendre les stratégies de domaine à un ordinateur virtuel dans Azure. En outre, vous pouvez définir des règles de pare-feu et utiliser l’authentification Windows sur votre ordinateur virtuel. Actuellement, Azure prend en charge des VPN de site à site sécurisé et les connexions VPN site-à-point :

        - Avec une connexion sécurisée de site à site, vous pouvez établir la connectivité réseau entre votre réseau local et de réseau virtuel dans Azure. Il est recommandé pour la connexion de votre environnement de centre de données local à Azure.

        - Avec une connexion point-à-site sécurisée, vous pouvez établir la connectivité réseau entre votre réseau virtuel dans Azure et vos ordinateurs individuels en cours d’exécution. Il est surtout recommandé à des fins de développement et de test.

    Pour plus d’informations sur la façon de se connecter à SQL Server dans Azure, voir [se connecter à un ordinateur de virtuel de SQL Server sur Azure](virtual-machines-windows-classic-sql-connect.md).

1. Paramétrer des tâches planifiées et alertes que sauvegarder des données sur site dans un disque de l’ordinateur virtuel dans Azure. Pour plus d’informations, consultez [SQL Server sauvegarde et restauration avec le Service de stockage de Blob Azure](https://msdn.microsoft.com/library/jj919148.aspx) et [sauvegarde et restauration pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

1. En fonction des besoins de votre application, vous pouvez implémenter un des trois scénarios courants suivants :

    1. Vous pouvez conserver votre serveur web, serveur d’applications et données non sensibles dans un serveur de base de données dans Azure que vous conservez les données sensibles sur site.

    1. Vous pouvez conserver votre serveur web et serveur d’application local que le serveur de base de données sur une machine virtuelle dans Azure.

    1. Vous pouvez conserver votre serveur de base de données, le serveur web et serveur d’application local que vous conservez les réplicas de la base de données sur des machines virtuelles dans Azure. Ce paramètre permet des serveurs sur site web ou des applications de création de rapports pour accéder aux répliques de base de données dans Azure. Par conséquent, vous pouvez obtenir pour réduire la charge de travail dans une base de données sur site. Nous vous recommandons d’implémenter ce scénario de lourds lire les charges de travail et à des fins de développement. Pour plus d’informations sur la création de répliques de base de données dans Azure, consultez groupes de disponibilité AlwaysOn à [haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparaison des stratégies de développement web dans Azure

Pour implémenter et déployer une n-tier application de SQL Server dans Azure, vous pouvez utiliser une des deux méthodes de programmation suivantes :

- Configurer un serveur web traditionnel (IIS - Internet Information Services) dans Azure et l’accès aux bases de données de SQL Server dans Azure Virtual Machines.

- Implémenter et déployer un service cloud sur Azure. Puis, assurez-vous que ce service de cloud peut accéder des bases de données de SQL Server sur des machines virtuelles Azure. Un service en nuage peut inclure plusieurs rôles web et worker.

Le tableau suivant fournit une comparaison du développement web traditionnel avec les Services en nuage Azure et d’Azure Web Apps par rapport à SQL Server dans Azure Virtual Machines. Le tableau comprend Azure Web Apps tel qu’il est possible d’utiliser les SQL Server dans Azure VM sous la forme d’une source de données pour les applications Web Azure via son adresse IP publique virtuelle ou le nom DNS.

||Développement web traditionnel dans Azure Virtual Machines|Services en nuage dans Azure|Hébergement avec les applications Web Azure|
|---|---|---|---|
|**Migration d’applications en local**|Les applications existantes en tant que-est.|Les applications ont besoin de rôles web et worker.|Les applications existantes sous la forme-mais convient pour les applications autonomes et les services web nécessitant l’évolutivité rapide.|
|**Développement et déploiement**|Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, PowerShell, Gestionnaire des services IIS.|Visual Studio, Azure SDK, TFS, PowerShell. Chaque service en nuage a deux environnements sur lesquels vous pouvez déployer votre package de service et de la configuration : intermédiaires et production. Vous pouvez déployer un service cloud dans l’environnement intermédiaire pour le tester avant de vous le promouvoir en production.|Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, échange, GitHub, mercurienne, TFS, Web déployer, PowerShell.|
|**Installation et administration**|Vous êtes chargé de tâches administratives sur les applications, données, règles de pare-feu, réseau virtuel et système d’exploitation.|Vous êtes chargé de tâches administratives sur l’application, de données, de règles de pare-feu et de réseau virtuel.|Vous êtes chargé de tâches administratives sur des applications et des données uniquement.|
|**Haute disponibilité et reprise d’activité (HADR)**|Nous vous recommandons de placer les ordinateurs virtuels dans le même jeu de disponibilité et dans le même service de cloud. Conserver vos ordinateurs virtuels dans le même jeu de disponibilité permet d’Azure pour placer les nœuds haute disponibilité dans des domaines distincts des pannes et la mise à niveau des domaines. De la même façon, l’équilibrage de charge permet de maintenir vos ordinateurs virtuels dans le même service de cloud et les ordinateurs virtuels peuvent communiquer directement entre eux sur le réseau local dans un centre de données Azure.<br/><br/>Vous êtes responsable de la mise en œuvre une solution de reprise après sinistre pour SQL Server et une haute disponibilité dans Azure Virtual Machines afin d’éviter les temps d’arrêt. Pour les technologies HADR prises en charge, reportez-vous à [haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Vous êtes responsable de la sauvegarde de vos propres données et votre application.<br/><br/>Azure peut déplacer vos machines virtuelles en cas de défaillance de l’ordinateur hôte dans le centre de données en raison de problèmes de matériel. En outre, il peut y avoir des temps d’arrêt planifié de votre ordinateur virtuel lorsque l’ordinateur hôte est mise à jour de logiciel ou de sécurité mises à jour. Par conséquent, nous vous recommandons de maintenir au moins deux VM dans chaque couche de l’application pour garantir la disponibilité continue. Azure ne fournit pas de SLA pour une seule machine virtuelle. Pour plus d’informations, consultez les [conseils techniques de résilience Azure](../resiliency/resiliency-technical-guidance.md).|Azure gère les pannes résultant du logiciel de matériel ou de système d’exploitation sous-jacent. Nous vous recommandons d’implémenter plusieurs instances d’un rôle web ou travailleur pour garantir la haute disponibilité de votre application. Pour plus d’informations, consultez [les Services en nuage, les ordinateurs virtuels, contrat de niveau de Service de réseau virtuel](http://www.microsoft.com/download/details.aspx?id=38427) et [reprise après sinistre et une haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>Vous êtes responsable de la sauvegarde de vos propres données et votre application.<br/><br/>Pour les bases de données résidant dans une base de données SQL Server dans un ordinateur virtuel d’Azure, vous êtes responsable de la mise en œuvre d’une solution de récupération après sinistre et de disponibilité élevée pour éviter les temps d’arrêt. Pour les technologies HDAR pris en charge, reportez-vous à la section haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines.<br/><br/>**Mise en miroir de base de données de SQL Server**: utilisation avec les Services de Cloud Azure (rôles web/travailleur). Machines virtuelles de SQL Server et un projet de service cloud peuvent être sur le même réseau virtuel Azure. Si la machine virtuelle de SQL Server ne se trouve pas dans le même réseau virtuel, vous devez créer un Alias de SQL Server pour acheminer les communications vers l’instance de SQL Server. En outre, le nom d’alias doit correspondre à celui de SQL Server.|Haute disponibilité est héritée à partir de la base de données de SQL Azure, stockage blob Azure et rôles worker Azure. Par exemple, le stockage Azure gère trois réplicas de tous les blob, table et les données de file d’attente. À tout moment, base de données de SQL Azure conserve trois répliques des données en cours d’exécution, un réplica principal et deux réplicas secondaires. Pour plus d’informations, consultez [Stockage Azure](https://azure.microsoft.com/documentation/services/storage/) et [Base de données de SQL Azure](../sql-database/sql-database-technical-overview.md).<br/><br/>Lors de l’utilisation de SQL Server dans Azure VM sous la forme d’une source de données pour les applications Web Azure, gardez à l’esprit que Azure Web Apps ne gère pas le réseau virtuel d’Azure. En d’autres termes, toutes les connexions à partir d’applications Web de Azure aux machines virtuelles de SQL Server dans Azure doivent traverser des publics de points de terminaison de machines virtuelles. Cela peut provoquer des limites pour la haute disponibilité et de récupération d’urgence. Par exemple, l’application cliente sur Azure Web Apps se connectant à la machine virtuelle de SQL Server avec la mise en miroir de base de données ne serait pas en mesure de se connecter au nouveau serveur principal, comme la mise en miroir de base de données requiert la définition d’Azure de réseau virtuel entre des machines virtuelles des hôtes SQL Server dans Azure. Par conséquent, l’utilisation de la **Mise en miroir de base de données de SQL Server** avec Azure Web Apps n'est pas actuellement pris en charge.<br/><br/>**Groupes de disponibilité AlwaysOn de SQL Server**: vous pouvez définir des groupes de disponibilité AlwaysOn lors de l’utilisation d’Azure Web Apps avec les machines virtuelles de SQL Server dans Azure. Mais vous devez configurer un récepteur, groupe de disponibilité AlwaysOn pour acheminer la communication pour le réplica principal via des points de terminaison publics avec équilibrage de charge.|
|**Connectivité d’entre différents locaux**|Vous pouvez utiliser le réseau virtuel d’Azure pour se connecter à des locaux.|Vous pouvez utiliser le réseau virtuel d’Azure pour se connecter à des locaux.|Azure réseau virtuel est pris en charge. Pour plus d’informations, consultez [Intégration de réseau virtuel Web applications](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/).|
|**Évolutivité**|Échelle-up est en augmentation de la taille de la machine virtuelle ou en ajoutant des disques supplémentaires. Pour plus d’informations sur les tailles de la machine virtuelle, voir [Taille de Machine virtuelle pour Azure](virtual-machines-windows-sizes.md).<br/><br/>**Pour le serveur de base de données**: horizontale est disponible par l’intermédiaire de techniques de partitionnement de base de données et groupes de disponibilité AlwaysOn de SQL Server.<br/><br/>Pour les charges de travail en lecture, vous pouvez utiliser des [Groupes de disponibilité AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) sur plusieurs nœuds secondaires ainsi que la réplication SQL Server.<br/><br/>Pour les charges de travail en écriture, vous pouvez implémenter des données de partitionnement horizontales sur plusieurs serveurs physiques pour fournir d’application horizontale.<br/><br/>En outre, vous pouvez implémenter une montée à l’aide [De SQL Server avec le routage dépendant des données](https://technet.microsoft.com/library/cc966448.aspx). Avec données dépendantes routage (DDR), vous devez implémenter le mécanisme de partitionnement dans l’application client, généralement dans la couche d’entreprise, pour router les requêtes de base de données à plusieurs nœuds de SQL Server. Le niveau métier contient les mappages à la façon dont les données sont partitionnées et le nœud qui contient les données.<br/><br/>Vous pouvez adapter des applications qui exécutent des ordinateurs virtuels. Pour plus d’informations, consultez [comment mettre à l’échelle d’une Application](../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Remarque importante**: la fonction **AutoScale** dans Azure permet d’augmenter ou de diminuer les ordinateurs virtuels qui sont utilisés par votre application automatiquement. Cette fonctionnalité garantit que l’expérience de l’utilisateur final n’est pas affecté négatif au cours des périodes de pointe et VMs sont fermés lorsque la demande est faible. Il est recommandé que vous ne définissez pas l’option d’échelle pour votre service cloud si elle inclut des machines virtuelles de SQL Server. La raison est que la fonctionnalité d’échelle automatique permet d’Azure à une machine virtuelle sous tension lors de l’utilisation du processeur dans cette machine virtuelle est supérieure à un seuil et désactiver un ordinateur virtuel lorsque l’utilisation du processeur est inférieure à celui qu’il. La fonctionnalité de l’échelle automatique est utile pour les applications sans état, tels que les serveurs web, où une machine virtuelle peut gérer la charge de travail sans aucune référence à n’importe quel état précédent. Toutefois, la fonctionnalité de l’échelle n’est pas utile pour les applications avec état, tel que SQL Server, où une seule instance permet l’écriture dans la base de données.|L’extrapolation est disponible à l’aide de plusieurs rôles web et worker. Pour plus d’informations sur les tailles de machine virtuelle pour les rôles web et worker, consultez [Configurer les tailles des Services en nuage](../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Lors de l’utilisation des **Services en nuage**, vous pouvez définir plusieurs rôles afin de répartir le traitement et également la mise à l’échelle flexible de votre application. Chaque service en nuage comprend un ou plusieurs rôles web et/ou des rôles worker, chacun avec ses propres fichiers d’application et de la configuration. Vous pouvez augmenter un service en nuage en augmentant le nombre d’instances de rôle (machines virtuelles) déployé pour un rôle ou échelle vers le bas un service cloud en réduisant le nombre d’instances de rôle. Pour plus d’informations, consultez [Modèles de d’exécution Azure](../cloud-services/cloud-services-choose-me.md).<br/><br/>Horizontale est disponible via la prise en charge de haute disponibilité intégrée Azure via les [Services en nuage, les ordinateurs virtuels, contrat de niveau de Service de réseau virtuel](http://www.microsoft.com/download/details.aspx?id=38427) et équilibrage de la charge.<br/><br/>Pour une application à plusieurs niveaux, nous vous recommandons application web/travailleur rôles de base de données de serveur de machines virtuelles via un réseau virtuel d’Azure. En outre, Azure fournit équilibrage de charge pour les machines virtuelles dans le même service de cloud, répartissant les demandes de l’utilisateur sur les. Les ordinateurs virtuels connectés de cette manière peuvent communiquer directement entre eux sur le réseau local dans un centre de données Azure.<br/><br/>Vous pouvez configurer **AutoScale** sur le portail classique Azure, ainsi que les heures de planification. Pour plus d’informations, consultez [comment mettre à l’échelle d’une Application](../cloud-services/cloud-services-how-to-scale.md).|**Évoluer**: vous pouvez augmenter/diminuer la taille de l’instance (VM) réservé pour votre site web.<br/><br/>Mise à l’échelle : vous pouvez ajouter des instances plus réservés (VMs) de votre site web.<br/><br/>Vous pouvez configurer **AutoScale** sur le portail, ainsi que les heures de planification. Pour plus d’informations, reportez-vous [à l’échelle les applications Web](../app-service-web/web-sites-scale.md).|

Pour plus d’informations sur le choix entre ces méthodes, reportez-vous à la section [Azure Web applications, les Services en nuage et les ordinateurs virtuels : À utiliser](../app-service-web/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’exécution de SQL Server dans des machines virtuelles d’Azure, consultez [SQL Server sur la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
