<properties
   pageTitle="Forum aux questions (FAQ) le centre de sécurité Azure | Microsoft Azure"
   description="Ce forum aux questions répond aux questions concernant le centre de sécurité Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-frequently-asked-questions-faq"></a>Centre de sécurité Azure Forum aux questions (FAQ)

Ce forum aux questions répond aux questions concernant le centre de sécurité Azure, un service qui vous permet de prévenir, détecter et répondre aux menaces avec une meilleure visibilité et de contrôle sur la sécurité de vos ressources Microsoft Azure.

## <a name="general-questions"></a>Questions d’ordre générales

### <a name="what-is-azure-security-center"></a>Quel est le centre de sécurité Azure ?
Le centre de sécurité Azure vous aide à empêcher, détecter et répondre aux menaces avec une meilleure visibilité et de contrôle sur la sécurité de vos ressources d’Azure. Il fournit la gestion de stratégie et de surveillance de la sécurité intégrée sur vos abonnements, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec un vaste écosystème de solutions de sécurité.

### <a name="how-do-i-get-azure-security-center"></a>Comment obtenir le centre de sécurité Azure ?
Le centre de sécurité Azure est activé avec votre abonnement Microsoft Azure et accessible à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). ([Se connecter au portail](https://portal.azure.com), sélectionnez **Parcourir**et faites défiler vers le **Centre de sécurité**).  

## <a name="billing"></a>Facturation

### <a name="how-does-billing-work-for-azure-security-center"></a>Fonctionnement de facturation pour le centre de sécurité Azure
Centre de sécurité est proposé en deux niveaux : libre et Standard.

La couche libre vous permet de définir des stratégies de sécurité et de recevoir des alertes de sécurité, des incidents et des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires. Avec la couche libre, vous pouvez également surveiller l’état de la sécurité de vos ressources Azure et les solutions de partenaires intégrées avec votre abonnement Azure.

La couche Standard fournit les détections de fonctionnalités plus avancées de couche libre : menaces intelligence, analyse comportementale, analyse des incidents et détection des anomalies. Une évaluation gratuite de 90 jours de la couche Standard est disponible. Pour mettre à niveau, sélectionnez le niveau de prix dans la [stratégie de sécurité](security-center-policies.md#setting-security-policies-for-subscriptions). Pour en savoir plus, consultez [le centre de sécurité de tarification](security-center-pricing.md) .

## <a name="data-collection"></a>Collecte de données

Centre de sécurité collecte les données de vos machines virtuelles pour évaluer l’état de leur sécurité, fournir des recommandations de sécurité et vous avertir des menaces. Lorsque vous accédez le centre de sécurité, la collecte des données est activée sur tous les ordinateurs virtuels dans votre abonnement. Collecte de données est recommandée, mais vous pouvez annulations en [désactivant la collecte de données](#how-do-i-disable-data-collection) dans la stratégie du centre de sécurité.

### <a name="how-do-i-disable-data-collection"></a>Comment désactiver la collecte de données ?

Vous pouvez désactiver **la collecte des données** d’un abonnement dans la stratégie de sécurité à tout moment. ([Se connecter au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, sélectionnez le **Centre de sécurité**et sélectionnez **stratégie.)**  Lorsque vous sélectionnez un abonnement, une nouvelle lame s’ouvre et vous permet de désactiver la **collecte de données** . Sélectionnez l’option **Supprimer les agents** dans le ruban supérieur à supprimer les agents des ordinateurs virtuels existants.

> [AZURE.NOTE] Les stratégies de sécurité peuvent être définies au niveau de groupe ressources et niveau d’abonnement Azure, mais vous devez sélectionner un abonnement pour désactiver la collecte de données.

### <a name="how-do-i-enable-data-collection"></a>Comment faire pour activer la collecte des données ?
Vous pouvez activer la collecte de données pour votre abonnement Azure dans la stratégie de sécurité. Pour activer la collecte de données, [Connectez-vous au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, sélectionnez le **Centre de sécurité**et sélectionnez **stratégie**. **Collecte de données** **sur** et configurer les comptes de stockage où vous souhaitez que les données puissent être collectées à (voir la question «[où sont stockées mes données ?](#where-is-my-data-stored)»). Lors de la **collecte de données** est activé, il recueille automatiquement les informations de configuration et les événements de sécurité de tous les ordinateurs virtuels pris en charge dans l’abonnement.

> [AZURE.NOTE] Les stratégies de sécurité peuvent être définies au niveau de groupe ressources et niveau d’abonnement Azure, mais la configuration de la collecte de données se produit au niveau de l’abonnement uniquement.

### <a name="what-happens-when-data-collection-is-enabled"></a>Que se passe-t-il lors de la collecte de données est activée ?
Collecte de données est activée via l’Agent de surveillance Azure et l’extension du contrôle de la sécurité Azure. L’extension du contrôle de la sécurité Azure analyse pour différentes configuration de sécurité pertinentes et les envoie dans les traces de [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). En outre, le système d’exploitation crée des entrées de journal des événements.  L’Agent de surveillance Azure lit les entrées de journal des événements et ETW trace et les copie dans votre compte de stockage pour l’analyse.  Il s’agit du compte de stockage que vous avez configuré dans la stratégie de sécurité. Pour plus d’informations sur le compte de stockage, consultez la question «[où sont stockées mes données ?](#where-is-my-data-stored)»

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>L’extension de l’Agent de surveillance ou de contrôle de la sécurité influe sur les performances de mon serveur ?
L’agent et l’extension consomme une quantité nominale des ressources système et doit avoir le peu d’impact sur les performances.

### <a name="where-is-my-data-stored"></a>Où mes données sont-elles stockées ?
Pour chaque région dans laquelle vous avez des ordinateurs virtuels en cours d’exécution, vous choisissez le compte de stockage où sont stockées les données collectées à partir de ces ordinateurs virtuels. Cela rend facile à conserver les données dans la même zone géographique à des fins données relevant de la souveraineté et de la confidentialité. Vous choisissez le compte de stockage pour un abonnement dans la stratégie de sécurité. ([Se connecter au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, sélectionnez le **Centre de sécurité**et sélectionnez **stratégie.)** Lorsque vous cliquez sur un abonnement, une nouvelle lame s’ouvre. Sélectionnez **les comptes de stockage choisir** sélectionner une région.

> [AZURE.NOTE] Les stratégies de sécurité peuvent être définies au niveau de groupe ressources et niveau d’abonnement Azure, mais la sélection d’une région pour votre compte de stockage se produit au niveau de l’abonnement uniquement.

Pour en savoir plus sur le stockage Azure et les comptes de stockage, consultez la [Documentation du stockage](https://azure.microsoft.com/documentation/services/storage/) et des [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

## <a name="using-azure-security-center"></a>À l’aide du centre de sécurité Azure

### <a name="what-is-a-security-policy"></a>Ce qu’est une stratégie de sécurité ?
Une stratégie de sécurité définit le jeu de contrôles qui sont recommandés pour les ressources au sein de l’abonnement spécifié ou d’un groupe de ressources. Dans le centre de sécurité Azure, vous définissez des stratégies pour vos abonnements Azure et les groupes de ressources en fonction des besoins de sécurité de votre société et le type des applications ou des données dans chaque abonnement.

Par exemple, les ressources permettant de développement ou de test peuvent avoir des exigences de sécurité différentes de ceux utilisés pour les applications de production. De même, les applications avec données réglementées comme informations d’identification personnelle (informations d’identification personnelle) peuvent nécessiter un niveau de sécurité supérieur. Les stratégies de sécurité activés dans le centre de sécurité Azure déterminera les recommandations de sécurité et de surveillance. Pour plus d’informations sur les stratégies de sécurité, consultez [sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md).

> [AZURE.NOTE] En cas de conflit entre la stratégie de niveau d’abonnement et de la stratégie de niveau de groupe de ressources, la stratégie de niveau de groupe de ressources est prioritaire.

### <a name="who-can-modify-a-security-policy"></a>Qui peut modifier une stratégie de sécurité ?
Stratégies de sécurité sont configurés pour chaque abonnement ou d’un groupe de ressources. Pour modifier une stratégie de sécurité au niveau de groupe de ressources ou niveau d’abonnement, vous devez être un propriétaire ou un collaborateur de cet abonnement.

Pour savoir comment configurer une stratégie de sécurité, consultez [définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Ce qu’est une recommandation de sécurité ?
Le centre de sécurité Azure analyse l’état de la sécurité de vos ressources d’Azure. Lors de l’identification de failles de sécurité potentielles, les recommandations sont créées. Les recommandations vous guident tout au long du processus de configuration du contrôle nécessaire. Des exemples sont :

- Mise en service des logiciels anti-programmes malveillants pour aider à identifier et à supprimer des logiciels malveillants
- Configuration des [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) et des règles pour contrôler le trafic vers des machines virtuelles
- Mise en service d’un pare-feu d’application web pour aider à se défendre contre les attaques qui ciblent vos applications web
- Déploiement des mises à jour manquantes de système
- Adressage des configurations de système d’exploitation qui ne correspondent pas à des lignes de base recommandées

Uniquement des recommandations qui sont activées dans les stratégies de sécurité sont présentées ici.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Comment afficher l’état actuel de la sécurité de mes ressources Azure ?
Une mosaïque de **santé de ressources** sur la lame du **Centre de sécurité** affiche la posture de sécurité globale de votre environnement par des machines virtuelles, les applications web et d’autres ressources. Chaque ressource a un affichage de l’indicateur si les failles de sécurité potentielles ont été identifiées. Cliquez sur la mosaïque de la santé des ressources affiche vos ressources et identifie où l’attention est nécessaire ou problèmes peuvent exister.

### <a name="what-triggers-a-security-alert"></a>Ce qui déclenche une alerte de sécurité ?
Le centre de sécurité Azure recueille, analyse automatiquement et fusionne les données du journal à partir de vos ressources d’Azure, le réseau et les solutions des partenaires telles que contre les logiciels malveillants et des pare-feux. Lorsque des menaces sont détectées, une alerte de sécurité est créée. Détection de quelques exemples :

- Compromis de machines virtuelles communiquent avec des adresses IP malveillantes connues
- Avancée contre les logiciels malveillants détectés à l’aide du rapport d’erreurs Windows
- Attaques en force contre les machines virtuelles
- Alertes de sécurité à partir des solutions de sécurité des partenaires intégrées telles que les logiciels anti-programme malveillant ou pare-feu d’Application Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Quelle est la différence entre les menaces détectées et averti sur par le Microsoft Security Response Center et le centre de sécurité Azure ?
Le centre de réponse sécurité Microsoft (MSRC) effectue une analyse de sécurité select de l’infrastructure et du réseau Azure et reçoit les plaintes de l’intelligence et les abus de menace par des tiers. Lorsque le MSRC constate que les données client a accédé par une partie illégale ou non autorisée ou qu’utilise le client la Azure ne respecte pas les termes pour utiliser Acceptable, un gestionnaire des incidents de sécurité avertit le client. Notification se produit généralement par l’envoi d’un courrier électronique aux contacts de sécurité spécifiés dans le centre de sécurité Azure ou le propriétaire de l’abonnement Azure si un contact de sécurité n’est pas spécifié.

Centre de sécurité est un service Azure qui surveille l’environnement du client Azure et s’applique analytique pour détecter automatiquement un large éventail d’activités potentiellement malveillantes. Ces détections sont surfacées comme les alertes de sécurité dans le tableau de bord du centre de sécurité.

### <a name="how-are-permissions-handled-in-azure-security-center"></a>La gestion des autorisations dans le centre de sécurité Azure ?
Centre de sécurité Azure prend en charge l’accès par rôle. Pour en savoir plus sur le contrôle d’accès basé sur les rôles (RBAC) dans Azure, consultez [Contrôle d’accès basé sur le rôle de répertoire actif Azure](../active-directory/role-based-access-control-configure.md).

Lorsqu’un utilisateur ouvre le centre de sécurité, uniquement des recommandations et produira les alertes liées à l’utilisateur a accès à des ressources. Cela signifie que les utilisateurs voient uniquement les éléments associés aux ressources, où l’utilisateur est le rôle de propriétaire, collaborateur ou lecteur de l’abonnement ou le groupe de ressources auquel appartient une ressource.

Si vous avez besoin :

- **Modifier une stratégie de sécurité**, vous devez être un propriétaire ou un collaborateur de l’abonnement.
- **Appliquer une recommandation**, vous devez être un propriétaire ou un collaborateur de l’abonnement.
- **Avoir une visibilité sur l’état de la sécurité sur l’ensemble de vos abonnements**, vous devez être un propriétaire, collaborateur ou lecteur (IT Admin, équipe de sécurité) de chaque abonnement.
- **Avoir une visibilité sur l’état de la sécurité de vos ressources**, vous devez être un groupe de ressources propriétaire, collaborateur ou lecteur (DevOps).

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Les ressources Azure sont contrôlés par le centre de sécurité Azure ?
Le centre de sécurité Azure surveille les ressources Azure suivantes :

- Machines virtuelles (VM) (y compris les [Services en nuage](../cloud-services/cloud-services-choose-me.md))
- Réseaux virtuels Azure
- Service SQL Azure
- Solutions de partenaires intégrées avec votre abonnement Azure telles qu’un pare-feu d’applications web sur des ordinateurs virtuels et sur [l’Environnement de Service d’application](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>Ordinateurs virtuels

### <a name="what-types-of-virtual-machines-will-be-supported"></a>Quels types d’ordinateurs virtuels seront pris en charge ?
Surveillance de la santé de la sécurité et des recommandations sont disponibles pour les machines virtuelles (VM), créées à l’aide [classique et les modèles de déploiement Gestionnaire de ressources](../azure-classic-rm.md).

Prise en charge des ordinateurs virtuels de Windows :

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

Machines virtuelles de Linux prises en charge :

- Versions d’Ubuntu 12.04, 14.04, 16.04
- Debian versions 7, 8
- CentOS versions 6. \*, 7.*
- Versions de Red Hat Enterprise Linux (RHEL) 6. \*, 7.*
- SUSE Linux Enterprise Server (SLES) versions 11. \*, 12.*
- Versions de Linux Oracle 6. \*, 7.*

Ordinateurs virtuels en cours d’exécution dans un service en nuage sont également pris en charge. Nuage uniquement les services les rôles web et worker en cours d’exécution dans la production d’emplacements surveillés. Pour en savoir plus sur le service en nuage, consultez [vue d’ensemble des Services en nuage](../cloud-services/cloud-services-choose-me.md).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Pourquoi le centre de sécurité Azure ne reconnaît pas la solution contre les logiciels malveillants en cours d’exécution sur mon Azure VM ?

Centre de sécurité Azure n’a de visibilité contre les logiciels malveillants installés par l’intermédiaire des extensions Azure. Par exemple, le centre de sécurité n’est pas en mesure de détecter des logiciels anti-programmes malveillants qui était déjà installé sur une image que vous avez fournie ou si vous avez installé des logiciels anti-programmes malveillants sur vos ordinateurs virtuels à l’aide de votre propre processus (tels que les systèmes de gestion de configuration).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Je reçois le message « Manquant des données d’analyse » de mon ordinateur virtuel ?

Il peut prendre un certain temps (généralement moins d’une heure) pour les données d’analyse remplir une fois la collecte de données est activée dans le centre de sécurité Azure. Analyses ne va pas peupler des ordinateurs virtuels dans un état arrêté.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Je reçois le message « Agent de la machine virtuelle est manquant ? »

L’Agent de la machine virtuelle doit être installé sur les machines virtuelles afin de permettre la collecte de données. L’Agent de la machine virtuelle est installé par défaut pour les ordinateurs virtuels qui sont déployés à partir de l’Azure Marketplace. Pour plus d’informations sur l’installation de l’Agent de la machine virtuelle sur d’autres machines virtuelles, consultez le blog de [l’Agent de la machine virtuelle et les Extensions](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
