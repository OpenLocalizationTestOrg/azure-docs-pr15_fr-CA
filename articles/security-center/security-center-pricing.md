<properties
   pageTitle="Prix du centre de sécurité | Microsoft Azure"
   description="Cet article fournit des informations sur les prix pour le centre de sécurité Azure."
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
   ms.date="10/12/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-pricing"></a>Prix du centre de sécurité Azure

Le centre de sécurité Azure vous aide à empêcher, détecter et répondre aux menaces avec une meilleure visibilité et de contrôle sur la sécurité de vos ressources d’Azure. Il fournit la gestion de stratégie et de surveillance de la sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec un vaste écosystème de solutions de sécurité.

## <a name="pricing-tiers"></a>Niveaux de prix

Centre de sécurité est proposé en deux niveaux :

- La **couche libre** est automatiquement activé sur tous les abonnements d’Azure. La couche libre fournit une visibilité sur l’état de la sécurité de vos ressources Azure, stratégie de sécurité de base, recommandations de sécurité et intégration avec les produits de sécurité et les services de partenaires.
- La **couche Standard** ajoute des fonctionnalités de détection de menaces évoluées, y compris des informations sur les menaces, analyse comportementale, détection des anomalies, incidents de sécurité et de rapports d’évaluation des menaces. Une **version d’évaluation gratuite de 90 jours** est disponible pour la couche Standard.

Pour plus d’informations, consultez le centre de sécurité, [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).

> [AZURE.NOTE] Centre de sécurité utilise le stockage Azure pour enregistrer les données de sécurité générées à partir de vos nœuds protégés. Les coûts associés à ce système de stockage ne sont pas inclus dans le prix du service et sont facturés séparément à des [taux de stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/)régulière. Les frais de stockage s’appliquer même pendant la période d’évaluation.

## <a name="try-standard-free-for-90-days"></a>Essayez gratuitement pendant 90 jours du Standard

Une version d’évaluation gratuite de 90 jours est disponible pour la couche Standard. Pour obtenir la version d’évaluation gratuite de la couche Standard, sélectionnez la vignette de la **stratégie** sur la lame du **Centre de sécurité** . Sélectionnez l’abonnement que vous souhaitez mettre à niveau standard. Sur la lame de la **stratégie de sécurité** , sélectionnez le **niveau de prix**. Sur la lame de **Choisir votre niveau de tarification** , sélectionnez **Standard – version d’évaluation gratuite**.

![Version d’évaluation gratuite][1]

À la fin des 90 jours, si vous choisissez de continuer à utiliser le service, nous démarre automatiquement en cours de chargement pour l’utilisation.

## <a name="upgrade-to-standard"></a>Mise à niveau standard

Mettre à niveau la couche Standard pour ajouter une détection des menaces avancées. Pour obtenir la couche Standard, sélectionnez la vignette de la **stratégie** sur la lame du **Centre de sécurité** . Sélectionnez l’abonnement que vous souhaitez mettre à niveau standard. Sur la lame de la **stratégie de sécurité** , sélectionnez le **niveau de prix**. Sur la lame de **Choisir votre niveau de tarification** , sélectionnez **Standard**.

![Couche standard][2]

## <a name="why-upgrade-to-standard"></a>Pourquoi une mise à niveau standard ?

La couche Standard du centre de sécurité fournit toutes les fonctionnalités de la couche libre plus avancées de détection. Détection avancée permet d’identifier les actifs menaces ciblant vos ressources Azure et vous fournit les informations nécessaires pour répondre rapidement.

Centre de sécurité emploie analytique de sécurité avancée, qui dépassent largement les approches basées sur la signature. Les innovations dans l’apprentissage des technologies de l’ordinateur et les données sont exploitées pour évaluer des événements via le fabric entière cloud – détection des menaces qu’il seraient impossibles d’identifier l’utilisation des méthodes manuelles et de prédiction de l’évolution des attaques de.

Analytique de sécurité qui sont fournies avec la couche Standard est les suivantes :

- **Sur les menaces** - recherche des acteurs de mauvais connus à l’aide des menaces globales à partir des produits Microsoft et les services, l’unité de Crimes numérique de Microsoft, Microsoft Security Response Center et alimentations externes
- **Analyse comportementale** - applique des modèles connus pour découvrir un comportement malveillant
- **Détection d’anomalie** - utilise le profilage statistique pour créer une configuration de référence historique. Il signale les écarts par rapport à des configurations de référence établies qui se conforment à un vecteur d’attaque potentielle

De la lame **d’alertes de sécurité** ci-dessous, le centre de sécurité a détecté un **incident**de sécurité. Un incident de sécurité est un regroupement de toutes les alertes pour une ressource qui s’alignent avec les modèles de chaîne kill. Sélection de l’incident de sécurité révèle plus de détails sur l’incident et répertorie les alertes associées. Sélection d’une alerte fournit plus d’informations sur cette occurrence.

![Incident de sécurité][3]

L’alerte de **communication réseau** ci-dessous fournit des détails sur l’alerte. Les détails incluent sa description complète, sa gravité, son état actuel (qui dans ce cas est fermée, c'est-à-dire l’utilisateur a effectué l’action à faire disparaître), la ressource attaqué et les étapes de conversion. Il existe également une liste de liens vers des rapports de Microsoft sur les menaces. Ces rapports peuvent être utilisés à des fins défensives et les contre-mesures.

![Détails de l’alerte sécurité][4]

## <a name="enable-data-collection"></a>Activer la collecte des données

Pour activer l’analytique comportementale de machine virtuelle, la collecte des données doit être activée. Il se peut que vous deviez activer la collecte des données lors de votre premier accès à centre de sécurité, ou lorsque vous créez une stratégie de sécurité.

Pour valider que la collecte de données est activée, sélectionnez la vignette de la **stratégie** . La lame de la **stratégie de sécurité** s’ouvre et affiche votre abonnement Azure. Sélectionnez un abonnement. Si la **collection de données** est désactivé, attribuez-lui la valeur on et enregistrez la modification. Reportez-vous à la section [Activer la collecte de données dans le centre de sécurité Azure](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Étapes suivantes

- Dans ce document, vous avez abordé les prix pour le centre de sécurité. Pour des informations supplémentaires sur les prix, voir le centre de sécurité, [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).
- Pour en savoir plus sur les capacités de détection avancées du centre de sécurité, consultez [le centre de sécurité Azure des fonctionnalités de détection](security-center-detection-capabilities.md).
- Si vous avez des questions sur l’utilisation du centre de sécurité, consultez le [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md).
- Si vous avez encore des questions sur l’utilisation de centre de sécurité, ou Azure, visitez les [Forums d’Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png
