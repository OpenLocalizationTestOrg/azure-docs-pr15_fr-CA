<properties
   pageTitle="Intégrer votre consommation de ressources de Microsoft Azure | Microsoft Azure"
   description="Fournit une vue d’ensemble conceptuelle de l’utilisation de facturation Azure et les RateCard, APIs, qui sont utilisés pour fournir des informations sur la consommation des ressources Azure et des tendances."
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

# <a name="gain-insights-into-your-microsoft-azure-resource-consumption"></a>Intégrer votre consommation de ressources de Microsoft Azure

Clients et partenaires requièrent la possibilité de prévoir avec précision et de gérer leurs coûts d’Azure.  Elles passent d’un Capex à un modèle d’Opex, ils doivent également la possibilité de faire des showback et l’analyse de refacturation, ainsi que fournir la fidélité de mode dans l’estimation et la facturation, en particulier pour les déploiements dans le cloud de grande taille.

Le Azure l’utilisation des ressources et taux carte API abordés cette adresse l’article ces besoins, grâce à la nouvelle vue d’ensemble de votre consommation de ressources Azure.  

## <a name="introducing-the-azure-resource-usage-and-ratecard-apis"></a>Présentation des API de RateCard et utilisation des ressources Azure

L’utilisation des ressources Azure et RateCard APIs sont implémentés comme un fournisseur de ressources, dans le cadre de la famille d’API exposées par le Gestionnaire de ressources Azure.  

### <a name="azure-resource-usage-api-preview"></a>Utilisation des ressources Azure API (aperçu)
Clients et partenaires permet l’API de l’utilisation de ressources Azure obtiennent leurs données de consommation Azure estimée. Les fonctionnalités incluent :

- **Contrôle d’accès basé sur les rôles d’azure** - clients et partenaires peut configurer leurs stratégies d’accès sur le [portail Azure](https://portal.azure.com) ou via les [applets de commande PowerShell de Azure](powershell-install-configure.md) pour spécifier les utilisateurs ou les applications peuvent accéder aux données d’utilisation de l’abonnement. Les appelants doivent utiliser des jetons Azure Active Directory standard pour l’authentification. L’appelant doit également être ajouté au rôle du lecteur, propriétaire ou collaborateur pour accéder aux données d’utilisation pour un abonnement Azure particulier.

- **Toutes les heures ou les agrégations quotidiennes** - les appelants peuvent spécifier s’ils souhaitent que leurs données d’utilisation Azure dans les compartiments horaires ou quotidiennes compartiments. La valeur par défaut est tous les jours.

- **Instance les métadonnées fournies (y compris les balises de la ressource)** – détails au niveau de l’Instance, tels que l’uri de ressource complet (/subscriptions/ {id d’abonnement} /...), ainsi que la ressource de balises d’informations et des ressources de groupe seront fournies dans la réponse. Cela aide les clients de façon déterministe et par programmation d’allouer l’utilisation par les balises, pour des cas d’utilisation comme charge entre.

- **Métadonnées de ressources fournies** - informations sur les ressources du compteur nom catégorie de compteur, compteur de sous-catégorie, unité et région est également passé dans la réponse, une meilleure compréhension de ce qui a été utilisée pour les appelants. Nous nous efforçons également d’aligner la terminologie des métadonnées de ressource sur le portail Azure, utilisation Azure CSV, EA CSV et autres expériences accessible au public, pour aider les clients à mettre en corrélation des données sur les expériences de facturation.

- **L’utilisation pour tous les types d’offre** – les données d’utilisation seront accessibles pour offrent de tous types, y compris le paiement à l’utilisation, MSDN, engagement monétaire, crédit monétaire et EA parmi d’autres.

### <a name="azure-resource-ratecard-api-preview"></a>RateCard de ressources Azure API (aperçu)
Clients et partenaires peuvent utiliser l’API de RateCard de ressources Azure pour obtenir la liste des ressources d’Azure disponibles, avec estimation du prix pour chaque. Les fonctionnalités incluent :

- **Contrôle d’accès basé sur les rôles d’azure** - clients et partenaires peuvent configurer leurs stratégies d’accès sur [Azure portal](https://portal.azure.com) , ou via les [applets de commande PowerShell de Azure](powershell-install-configure.md) pour spécifier les utilisateurs ou les applications peuvent accéder aux données RateCard. Les appelants doivent utiliser des jetons Azure Active Directory standard pour l’authentification. L’appelant doit également être ajouté au rôle du lecteur, propriétaire ou collaborateur pour accéder aux données d’utilisation pour un abonnement Azure particulier.

- **Prise en charge de paiement MSDN, monétaire et votre engagement monétaires de crédit offre (EA non pris en charge)** - cette API fournit des informations de Azure au niveau de l’offre de taux, et le niveau d’abonnement.  L’appelant de cette API doit passer les informations de l’offre pour obtenir le taux et les détails de la ressource.  Comme EA offres personnalisé taux par inscription, nous ne pouvons pas fournir le taux EA en ce moment.

## <a name="scenarios"></a>Scénarios

Voici quelques scénarios qui sont rendues possibles grâce à la combinaison de l’utilisation et la APIs RateCard :

- **Azure se passent au cours du mois** , les clients peuvent utiliser l’utilisation et la RateCard APIs en combinaison pour obtenir les mieux appréhender leur nuage passent au cours du mois, en analysant les compartiments horaires et journalier des estimations de l’utilisation et des frais.

- **Configurer des alertes** – clients et partenaires pouvez définir ressource ou monétaire alertes sur leur consommation de nuage en obtenant la consommation estimée et l’estimation des frais à l’aide de l’API RateCard et l’utilisation.

- **Predict nomenclature** – clients et les partenaires peuvent obtenir leur consommation estimée et nuage dépenser et d’appliquer des algorithmes d’apprentissage machine afin de prévoir quelle est leur facture à la fin de la période de facturation.

- **Analyse des coûts de consommation avant** – can de clients également l’utilisation de l’API RateCard pour prédire combien leurs facture serait si elles ont été déplacement leurs charges de travail dans Azure, en fournissant souhaité numéros d’utilisation. Si les clients ont des charges de travail existants dans d’autres nuages ou les nuages privés, ils peuvent également mapper leur utilisation de Azure passent de taux pour obtenir une meilleure estimation de leur Azure estimée. Cela fournit une meilleure représentation de ce qui peut être obtenu via la [Calculatrice de tarification Azure](https://azure.microsoft.com/pricing/calculator/), (par exemple) nos partenaires de facturation fournissent la possibilité offre de tableau croisé dynamique et les comparer entre les types d’offre différents au-delà de paiement à l’utilisation, y compris les engagements monétaires et du crédit monétaire. Les API fournissent également la possibilité de des changements d’estimation par région, coûts activant le type d’analyse requis pour prendre des décisions de déploiement, comme les ressources de déploiement de contrôleurs de domaine différents dans le monde entier peuvent avoir un impact direct sur le coût total.

- **Analyse de scénarios** -

    - Clients et partenaires peuvent déterminer si il serait plus rentable pour exécuter leurs charges de travail dans une autre région ou dans une autre configuration de la ressource Azure. Ressource Azure coûts peuvent différer en fonction de la région Azure dans lequel ils sont en cours d’exécution, et ainsi, les clients et les partenaires à obtenir des optimisations de coût.

    - Clients et partenaires peuvent également déterminer si un autre type d’Azure offre offre un meilleur débit sur une ressource d’Azure.

## <a name="partner-solutions"></a>Solutions partenaires

[L’utilisation de Microsoft Azure et RateCard API activer Cloudyn pour fournir une ITFM pour les clients](billing-usage-rate-card-partner-solution-cloudyn.md) décrit l’expérience d’intégration proposé par les partenaires de l’API de facturation Azure [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  Cet article fournit une couverture détaillée de leurs expériences, y compris une courte vidéo qui montre comment un client Azure peut utiliser Cloudyn et les API de facturation Azure aux analyses de gains à partir de leurs données de consommation Azure.

[Cruiser de nuage et d’intégration de l’API de facturation Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) décrit [Express du nuage Cruiser pour le Pack d’Azure](http://www.cloudcruiser.com/partners/microsoft/) fonctionnement directement depuis le portail WAP, permettant aux clients de gérer facilement les aspects opérationnels et financiers de leur nuage de Microsoft Azure privée ou publique à partir d’une interface utilisateur unique.   

## <a name="next-steps"></a>Étapes suivantes
+ Consultez la [Référence des API reste facturation Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) pour plus de détails sur les deux API qui font partie de l’ensemble des API fournies par le Gestionnaire de ressources Azure.
+ Si vous souhaitez plonger directement dans l’exemple de code, consultez notre Microsoft Azure facturation API Code Samples sur des [Échantillons de Code Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Pour en savoir plus
+ Consultez l’article [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour en savoir plus sur le Gestionnaire de ressources Azure.
+ Pour plus d’informations sur la suite d’outils nécessaires pour faciliter la compréhension du nuage passent, reportez-vous à l’article de Gartner [Guide du marché pour les outils de gestion financière d’informatique (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).
