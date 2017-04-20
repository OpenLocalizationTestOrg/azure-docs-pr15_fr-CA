<properties
   pageTitle="L’utilisation de Microsoft Azure et activer des API de RateCard Cloudyn pour fournir des ITFM pour les clients | Microsoft Azure"
   description="Fournit un point de vue unique à partir de la facturation de Microsoft Azure partenaire Cloudyn, sur leurs expériences, intégration de l’API de facturation Azure dans leur produit.  Ceci est particulièrement utile pour les clients d’Azure et Cloudyn qui sont intéressés à l’aide de/lors de la Cloudyn de Services Azure."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>L’utilisation de Microsoft Azure et activer des API de RateCard Cloudyn pour fournir des ITFM pour les clients

Cloudyn, un partenaire de développement de Microsoft et des principaux fournisseurs de fonctionnalités de gestion de cloud, a été choisi pour afficher un aperçu privé des nouvelles RateCard APIs et utilisation des ressources Microsoft Azure.  L’utilisation des API donne accès à des données de consommation Azure estimée pour un abonnement. L’API RateCard fournit des informations de tarification complètes de tous les services Azure, pour les clients non - Enterprise-accord-EA. Intégrés ensemble, ces API fournit une base d’informations complètes pour l’entrée dans des outils tels que ceux fournis par Cloudyn informatique Financial Management (ITFM).

## <a name="introduction"></a>Introduction

Dite « multiplication » des données à partir de l’API de l’utilisation des données à partir de l’API RateCard (prix de l’utilisation [unités] [$unit] = détaillée de l’utilisation et le coût) crée des informations les plus précises, précises et fiables facturation disponibles pour Azure dès aujourd'hui.

![Vue d’ensemble ITFM][1]

Utilisation de ces API fournit des informations clés sur l’utilisation de clients et les coûts, ce qui permet de Cloudyn pour analyser les comptes client de manière simple, par programmation et pour effectuer les diverses tâches ITFM pour ses clients.

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>Intégration de Cloudyn avec le RateCard et l’utilisation des API
L’API RateCard requiert plusieurs paramètres d’entrée, telles que les informations de région, de devise et de paramètres régionaux--mais le plus important est OfferDurableID, qui spécifie le type d’Azure offre le client utilise (paiement à l’utilisation, plans d’engagement hérités de 6 et 12 mois, MSDN propose, MPN offres, offres promotionnelles et autres). Vous trouverez le OfferDurableID dans l' [utilisation d’Azure et portail de facturation](https://account.windowsazure.com/Subscriptions), sous le « ID offre » pour l’abonnement spécifié.

Lors de l’enregistrement pour les services de [Cloudyn pour Azure](https://www.cloudyn.com/microsoft-azure/) , les clients peuvent ajouter leur code OfferDurableID, qui permet d’extraire les informations de tarification appropriées par le biais de l’API RateCard Cloudyn.  Informations sur les différents types d’offres se trouve une la page de [Détails de proposer Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) .

![Vue d’ensemble du moteur de Cloudyn ITFM][2]

Cloudyn utilise l’utilisation et la APIs RateCard, en plus de l’API de performances Azure, pour créer des couches supplémentaires de visualisation, analytique, des alertes, reporting, gestion du coût et des recommandations exploitables, fournissant Azure clients un outil ITFM de nuage entreprise fiable.

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>Activé par l’intégration des API de RateCard et l’utilisation des exemples d’utilisation de Cloudyn ITFM
Activé par l’utilisation des exemples d’utilisation de courant Cloudyn ITFM et RateCard APIs comprennent :

+ **Analyse des coûts** - permet de cloud coûts ventilés selon n’importe quelle dimension identification native (fournisseur, service, compte, zone, etc.). L’utilisation d’Azure RateCard APIs transformer une tâche facile, grâce à la répartition plus granulaire de l’utilisation et données par compte, puis regroupé et filtré par Cloudyn et présentée à l’utilisateur, sous forme de graphique ou d’un tableau de coûts.

![Graphique à secteurs d’analyse des coûts][3]

+ **Coût Allocation 360** - permet aux intérêts et aux responsables informatiques de découvrir la répartition des coûts réels, les pilotes et les tendances de leur déploiement de cloud. Il plus permet aux responsables de frais de déploiement facile à associer avec les divisions, départements, régions et plus, en fournissant des analyses sans précédent dans les coûts de nuage et de faciliter les showbacks et les rejets d’entreprise. L’utilisation d’Azure et RateCard APIs servent d’entrée pour moteur d’allocation du Cloudyn coût, qui complète les API en définissant des méthodes et la logique d’activité d’allocation de ressources non balisés ou untaggable.

![Graphique de répartition 360][4]

+ **Économique dimensionnement** - fournit des recommandations de dimensionnement droite pour les machines virtuelles sous-utilisés, réduisant ainsi les dépenses du client sur les machines trop volumineux ou trop mis en service. Il le fait en examinant l’ordinateur virtuel du processeur et des métriques de RAM (via l’API de performances), les heures d’exécution (via l’API de l’utilisation) et les coûts (via l’API de RateCard). Cloudyn fournit des recommandations de dimensionnement droite en fonction des ressources UC ou mémoire vive sous-utilisés (performances), puis calcule les économies estimées en multipliant le delta de prix (RateCard) entre les ordinateurs virtuels par la temps-une utilisation réelle (utilisation) de la machine sous-utilisés.

![Dimensionnement rentable][5]

+ Fournit des **Recommandations de portage de nuage** - conseils financiers sur le nuage de portage. Il examine les coûts actuels de l’utilisateur des ressources de cloud qui sont déployés sur les fournisseurs principaux de nuage et la compare au coût d’un déploiement équivalent dans Azure. Il fournit ensuite granulaire, par ressource, financièrement base de portage de recommandations pour Azure. Après avoir évalué le déploiement équivalent requis sur Azure (basé sur les préférences utilisateur et de mesures de performances), Cloudyn utilise l’API de RateCard pour évaluer le coût de déploiement équivalent dans Azure.

+ **Les rapports de performances** - activé par les résultats d’Azure API, ces rapports fournissent un ensemble de fonctionnalités à partir de l’utilisation du processeur et la RAM de recommandations d’optimisation. Voici un exemple de rapport de l’utilisation de l’instance, présentant la répartition de l’instance par l’utilisation moyenne du processeur.

![Rapports de performances][6]

+ **Gestionnaire des catégories** - une fonctionnalité puissante de Cloudyn qui apporte de l’ordre pour les ressources de cloud désorganisées. Il offre aux utilisateurs la liberté de créer leurs propres catégories uniques (balises) de mesure et de reporting efficace qui est conforme aux pratiques commerciales. En outre, les utilisateurs peuvent facilement régler et classer le balisage incohérent (fautes de frappe et autres écarts) et détecter automatiquement les ressources non balisés pour attribution de précis sur le coût.

![Gestionnaire des catégories][7]

## <a name="video"></a>Vidéo

Voici une courte vidéo qui montre comment un client Azure peut utiliser Cloudyn pour Azure et les API de facturation Azure, pour obtenir des idées à partir de leurs données de consommation Azure.

> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## <a name="next-steps"></a>Étapes suivantes

+ Démarrer une version d’évaluation gratuite du [Cloudyn pour Azure](https://www.cloudyn.com/microsoft-azure/) pour voir comment vous pouvez obtenir la transparence du coût des rapports personnalisés et analytique pour votre déploiement de cloud Microsoft Azure.
+ Reportez-vous à [intégrer votre consommation de ressources de Microsoft Azure](billing-usage-rate-card-overview.md) pour une vue d’ensemble de l’utilisation des ressources Azure et RateCard APIs.
+ Consultez la [Référence des API reste Azure facturation](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) pour plus d’informations sur les API qui font partie de l’ensemble des API fournies par le Gestionnaire de ressources Azure.
+ Si vous souhaitez plonger directement dans l’exemple de code, consultez notre Microsoft Azure facturation API Code Samples sur des [Échantillons de Code Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Pour en savoir plus
+ Pour en savoir plus sur les offres d’accord d’entreprise de Microsoft Azure (EA), visitez le site [Azure de licences pour l’entreprise] (https://azure.microsoft.com/pricing/enterprise-agreement/)
+ Consultez l’article [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour en savoir plus sur le Gestionnaire de ressources Azure.
+ Pour plus d’informations sur la suite d’outils nécessaires pour faciliter la compréhension du nuage passent, reportez-vous à l’article de Gartner [Guide du marché pour les outils de gestion financière d’informatique (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png
