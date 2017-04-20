<properties
   pageTitle="Introduction au centre de sécurité Azure | Microsoft Azure"
   description="Obtenir des informations sur le centre de sécurité Azure, ses fonctionnalités clées et comment il fonctionne."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# <a name="introduction-to-azure-security-center"></a>Introduction au centre de sécurité Azure

Obtenir des informations sur le centre de sécurité Azure, ses fonctionnalités clées et comment il fonctionne.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.

## <a name="what-is-azure-security-center"></a>Quel est le centre de sécurité Azure ?
 Centre de sécurité vous aide à empêcher, détecter et répondre aux menaces avec une meilleure visibilité et de contrôle sur la sécurité de vos ressources d’Azure. Il fournit la gestion de stratégie et de surveillance de la sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec un vaste écosystème de solutions de sécurité.

##  <a name="key-capabilities"></a>Fonctionnalités clés
 Centre de sécurité offre des fonctions de prévention, de détection et de réponse qui sont intégrées facile à utiliser et efficace des menaces pour Azure. Principales fonctionnalités sont les suivantes :

| | |
|----- |-----|
| Empêcher | Surveille l’état de la sécurité de vos ressources Azure |
| | Définit les stratégies pour vos abonnements Azure et les groupes de ressources en fonction des besoins de sécurité de votre entreprise, les types d’applications que vous utilisez et de la sensibilité de vos données |
| | Recommandations de sécurité utilise pilotés par stratégie pour guider les propriétaires de services à travers le processus de mise en œuvre nécessaire des contrôles |
| | Déployer rapidement des services de sécurité et des appliances de Microsoft et partenaires |
| Détecter |Collecte et analyse les données de sécurité à partir de vos ressources d’Azure, le réseau et les solutions des partenaires telles que les programmes contre les logiciels malveillants et des pare-feux automatiquement |
| | La menace exploite global intelligence à partir de produits de Microsoft, Microsoft Digital Crimes unité (DCU), le centre de réponse de sécurité Microsoft (MSRC) et les services des alimentations externes |
| | S’applique à analytique avancée, y compris l’apprentissage automatique et l’analyse comportementale |
| Répondre | Fournit des incidents de sécurité hiérarchisés et des alertes |
| | Propose des informations sur la source de l’attaque et les ressources d’impactés |
| | Suggère des façons d’empêcher l’attaque en cours et d’empêcher les attaques futures |

## <a name="introductory-walkthrough"></a>Procédure pas à pas d’introduction
 Pour accéder à Security Center à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). [Connectez-vous au portail](https://portal.azure.com), sélectionnez **Parcourir**et puis faites défiler jusqu'à l’option **Centre de sécurité** ou la mosaïque du **Centre de sécurité** que vous avez épinglés précédemment au tableau de bord de portail.

![Mosaïque de sécurité Azure portal][1]

À partir du centre de sécurité, vous pouvez définir des stratégies de sécurité, surveiller les configurations de sécurité et afficher les alertes de sécurité.

### <a name="security-policies"></a>Stratégies de sécurité

Vous pouvez définir des stratégies pour vos abonnements Azure et les groupes de ressources en fonction des besoins de sécurité de votre entreprise. Vous pouvez également personnaliser les types d’applications que vous utilisez ou la sensibilité des données dans chaque abonnement. Par exemple, les ressources utilisées pour le développement ou le test peuvent avoir des exigences de sécurité différentes de ceux utilisés pour les applications de production. De même, les applications avec données réglementées comme informations d’identification personnelle peuvent nécessiter un niveau de sécurité supérieur.

> [AZURE.NOTE] Pour modifier une stratégie de sécurité au niveau du groupe de ressources ou du niveau d’abonnement, vous devez être le propriétaire de l’abonnement ou un collaborateur à celui-ci.

Sur la blade **Centre de sécurité** , sélectionnez la vignette de la **stratégie** pour obtenir la liste de vos abonnements et les groupes de ressources.   

![Lame de centre de sécurité][2]

Sur la lame de la **stratégie de sécurité** , sélectionnez un abonnement pour afficher les détails de la stratégie.

![Abonnement de lame de stratégie de sécurité][3]

**Collecte de données** (voir ci-dessus) permet la collecte de données pour une stratégie de sécurité. L’activation permet :

- Analyse quotidienne de tous les de prise en charge des machines virtuelles pour la surveillance de la sécurité et de recommandations.
- Collection d’événements de sécurité pour la détection de l’analyse et les menaces.

**Choisissez un compte de stockage par région** (voir ci-dessus) vous permet de choisir, pour chaque région dans laquelle vous avez des ordinateurs virtuels en cours d’exécution, le compte de stockage où sont stockées les données collectées à partir de ces ordinateurs virtuels. Si vous ne choisissez pas un compte de stockage pour chaque région, il sera créé pour vous. Les données collectées sont isolées de façon logique à partir des données d’autres clients pour des raisons de sécurité.

> [AZURE.NOTE] Collecte de données et en choisissant un compte de stockage par région est configuré au niveau de l’abonnement.

Sélectionnez **stratégie de prévention** (voir ci-dessus) pour ouvrir la lame de la **politique de prévention** . **Afficher des recommandations pour** vous permet de choisir les contrôles de sécurité que vous souhaitez analyser et recommander en fonction des besoins de sécurité des ressources au sein de l’abonnement.

Ensuite, sélectionnez un groupe de ressources pour afficher les détails de la stratégie.

![Groupe de ressources de lame de politique de sécurité][4]

**Héritage** (voir ci-dessus) vous permet de définir le groupe de ressources en tant que :

- Hérité (par défaut) qui signifie que toutes les stratégies de sécurité pour ce groupe de ressources sont héritées à partir du niveau d’abonnement.
- Unique qui signifie que le groupe de ressources aura une stratégie de sécurité personnalisée. Vous devez apporter des modifications sous **Afficher les recommandations pour**.

> [AZURE.NOTE] S’il existe un conflit entre la stratégie de niveau d’abonnement et de la stratégie de niveau de groupe de ressources, la stratégie de niveau de groupe de ressources est prioritaire.

### <a name="security-recommendations"></a>Recommandations de sécurité

 Centre de sécurité analyse l’état de la sécurité de vos ressources Azure pour identifier les failles de sécurité potentielles. Une liste de recommandations vous guide tout au long du processus de configuration des contrôles nécessaires. Voici quelques exemples :

- Mise en service des logiciels anti-programmes malveillants pour aider à identifier et à supprimer des logiciels malveillants
- Configuration des groupes de sécurité réseau et des règles pour contrôler le trafic vers des machines virtuelles
- Mise en service de pare-feu d’application web pour aider à se défendre contre les attaques qui ciblent vos applications web
- Déploiement des mises à jour manquantes de système
- Adressage des configurations de système d’exploitation qui ne correspondent pas à des lignes de base recommandées

Cliquez sur la mosaïque de **recommandations** pour une liste de recommandations. Cliquez sur chaque recommandation pour afficher des informations supplémentaires ou prendre des mesures pour résoudre le problème.

![Recommandations de sécurité dans le centre de sécurité Azure][5]

### <a name="resource-health"></a>Santé de la ressource

La mosaïque de **l’état de sécurité de ressource** indique la posture de sécurité globale de l’environnement par type de ressource, y compris les ordinateurs virtuels, les applications web et autres ressources.   

Sélectionnez un type de ressource dans la fenêtre **d’état de sécurité de ressource** pour afficher plus d’informations, notamment une liste des vulnérabilités de sécurité potentielles qui ont été identifiés. (Les**ordinateurs virtuels** est sélectionné dans l’exemple ci-dessous).

![Mosaïque de santé de ressources][6]

### <a name="security-alerts"></a>Alertes de sécurité

 Centre de sécurité automatiquement recueille, analyse et intègre les données de journal à partir de vos ressources d’Azure, le réseau et les solutions des partenaires telles que les programmes contre les logiciels malveillants et des pare-feux. Lorsque des menaces sont détectées, une alerte de sécurité est créée. Détection de quelques exemples :

- Compromis de machines virtuelles communiquent avec des adresses IP malveillantes connues
- Avancée contre les logiciels malveillants détectés à l’aide de rapport d’erreurs Windows
- Attaques en force contre les machines virtuelles
- Alertes de sécurité à partir de programmes intégrée contre les logiciels malveillants et des pare-feux

Cliquez sur la mosaïque **d’alertes de sécurité** pour afficher une liste d’alertes par ordre de priorité.

![Alertes de sécurité][7]

Sélection d’une alerte affiche plus d’informations sur l’attaque et les suggestions d’utilisation plus.

![Détails de l’alerte sécurité][8]

### <a name="partner-solutions"></a>Solutions partenaires

La mosaïque de **solutions partenaires** vous permet de vous moniteur en un clin de œil l’état de vos solutions de partenaires intégrée avec votre abonnement Azure. Centre de sécurité affiche des alertes provenant de solutions.

Sélectionnez la mosaïque de **solutions partenaires** . Une blade s’ouvre en affichant une liste de toutes les solutions partenaire connecté.

![Solutions partenaires][9]

## <a name="get-started"></a>Mise en route
Pour vous familiariser avec le centre de sécurité, vous avez besoin d’un abonnement à Microsoft Azure. Centre de sécurité est activé avec votre abonnement Azure. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

 Pour accéder à Security Center à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Consultez la [documentation du portail](https://azure.microsoft.com/documentation/services/azure-portal/) pour en savoir plus.

[Mise en route avec le centre de sécurité Azure](security-center-get-started.md) Guide rapidement vous via le contrôle de la sécurité et les composants de gestion de la stratégie du centre de sécurité.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous ont été présentées le centre de sécurité, ses principales fonctionnalités et la mise en route. Pour plus d’informations, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer les stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de contrôle partenaire avec le centre de sécurité Azure](security-center-partner-solutions.md) : Apprenez à surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité d’Azure](http://blogs.msdn.com/b/azuresecurity/) , obtenir les dernières informations de sécurité Azure et les informations.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png
