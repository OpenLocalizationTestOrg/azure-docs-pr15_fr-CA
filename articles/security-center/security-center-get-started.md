<properties
   pageTitle="Guide de démarrage rapide du centre de sécurité Azure | Microsoft Azure"
   description="Cet article vous aide à vous familiariser rapidement avec le centre de sécurité Azure, en vous guidant à travers les composants de gestion de surveillance et de la stratégie de sécurité et aux étapes suivantes."
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
   ms.date="10/28/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-quick-start-guide"></a>Guide de démarrage rapide du centre de sécurité Azure

Cet article vous aide à commencer rapidement avec le centre de sécurité Azure en vous guidant dans les de surveillance et de la stratégie de gestion des composants de sécurité du centre de sécurité.

> [AZURE.NOTE] Cet article présente le service à l’aide d’un exemple de déploiement. Cet article n’est pas un guide pas à pas.

## <a name="prerequisites"></a>Conditions préalables

Pour vous familiariser avec le centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

La couche libre du centre de sécurité est automatiquement activée avec votre abonnement et procure une visibilité sur l’état de la sécurité de vos ressources d’Azure. Il fournit des gestion de stratégie de sécurité de base, les recommandations de sécurité et intégration avec les produits de sécurité et les services de partenaires Azure.

Pour accéder à Security Center à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Pour en savoir plus sur le portail Azure, consultez la [documentation du portail](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="data-collection"></a>Collecte de données

Centre de sécurité collecte les données de vos machines virtuelles (VM) pour évaluer l’état de leur sécurité, fournir des recommandations de sécurité et vous avertir des menaces. Lorsque vous accédez le centre de sécurité, la collecte des données est activée sur tous les ordinateurs virtuels dans votre abonnement. Collecte de données est recommandée, mais vous pouvez vous désabonner en désactivant la collecte de données dans la stratégie du centre de sécurité.

Les étapes suivantes décrivent comment accéder et utiliser les composants du centre de sécurité. Dans ces étapes, nous vous montrer comment désactiver la collecte de données si vous choisissez d’abandonner.

## <a name="access-security-center"></a>Centre de sécurité de l’accès

Dans le portail, procédez comme suit pour accéder au centre de sécurité :

1. Dans le menu **Microsoft Azure** , sélectionnez le **Centre de sécurité**.
![Menu Azure][1]

2. Si vous accédez pour la première fois le centre de sécurité, la lame de **Bienvenue** s’ouvre. Sélectionnez **Oui ! Je souhaite lancer le centre de sécurité Azure** pour ouvrir la **Centre de sécurité** de lame et pour activer la collecte de données.
![Écran d’accueil][10]

3. Une fois que vous lancez le centre de sécurité de la lame de bienvenue ou sélectionnez le centre de sécurité dans le menu Microsoft Azure, la lame du **Centre de sécurité** s’ouvre. Pour accéder facilement à la lame du **Centre de sécurité** , à l’avenir, sélectionnez l’option de **lame de broche pour le tableau de bord** (en haut à droite).
![Lame de broche pour le tableau de bord][2]

## <a name="use-security-center"></a>Utilisez le centre de sécurité

Vous pouvez configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources. Nous allons configurer une stratégie de sécurité pour votre abonnement :

1. Sur la blade **Centre de sécurité** , sélectionnez la vignette de la **stratégie** .
![Stratégie de sécurité][3]

2. Sur la lame de la **stratégie de sécurité - définir la stratégie de groupe d’abonnement ou d’une ressource** , sélectionnez un abonnement.
3. Sur la lame de la **stratégie de sécurité** , **la collecte des données** est activé pour collecter automatiquement les journaux. L’extension de contrôle est configurée sur tous les ordinateurs virtuels actuels et nouveaux de l’abonnement. (Vous pouvez vous désinscrire la collecte de données en définissant la **collection de données** **Off**, mais cela empêche le centre de sécurité de fournir des recommandations et des alertes de sécurité).
4. Sur la lame de la **stratégie de sécurité** , sélectionnez **Choisir un compte de stockage par région**. Pour chaque région dans laquelle vous avez des ordinateurs virtuels en cours d’exécution, vous choisissez le compte de stockage où sont stockées les données collectées à partir de ces ordinateurs virtuels. Si vous ne choisissez pas un compte de stockage pour chaque région, il est créé pour vous. Les données collectées sont isolées de façon logique à partir des données d’autres clients pour des raisons de sécurité.

     > [AZURE.NOTE] Nous vous recommandons d’activer la collecte de données et choisissez d’abord un compte de stockage au niveau de l’abonnement. Les stratégies de sécurité peuvent être définies au niveau de groupe ressources et niveau d’abonnement Azure, mais la configuration de compte de stockage et de collecte de données se produit au niveau de l’abonnement uniquement.

5. Sur la lame de la **stratégie de sécurité** , sélectionnez la **politique de prévention**. La lame de la **politique de prévention** s’ouvre.
![Politique de prévention][4]

6. Sur la lame de la **politique de prévention** , activez les recommandations que vous souhaitez voir dans le cadre de votre stratégie de sécurité. Exemples :

 - Définition des **mises à jour du système** à **On** analyse tous les ordinateurs virtuels pris en charge pour les mises à jour du système d’exploitation.
 - Définition des **vulnérabilités du système d’exploitation** à **On** analyse tous les ordinateurs virtuels pris en charge pour identifier les configurations de système d’exploitation qui peuvent rendre l’ordinateur virtuel plus vulnérable aux attaques.

### <a name="view-recommendations"></a>Recommandations relatives aux vues

1. Revenez à la lame du **Centre de sécurité** et sélectionnez le carreau de **recommandations** . Centre de sécurité analyse périodiquement l’état de sécurité de vos ressources d’Azure. Lorsque le centre de sécurité identifie les failles de sécurité potentielles, il affiche des recommandations sur la lame de **recommandations** .
![Recommandations dans le centre de sécurité Azure][5]

2.  Sélectionnez une recommandation sur la lame de **recommandations** pour afficher plus d’informations ou effectuer une action pour résoudre le problème.

### <a name="view-the-health-and-security-state-of-your-resources"></a>Afficher l’état de santé et la sécurité de vos ressources

1.  Revenir à la lame du **Centre de sécurité** . La mosaïque de **l’état de sécurité de ressources** contient des indicateurs de l’état de la sécurité pour les machines virtuelles, réseau, données et applications.
2.  Sélectionnez les **ordinateurs virtuels** pour afficher plus d’informations. La lame de **machines virtuelles** s’ouvre et affiche un résumé de l’état des programmes de logiciels anti-programmes malveillants, mises à jour système, redémarre et les vulnérabilités du système d’exploitation de vos ordinateurs virtuels.
![La mosaïque de la santé des ressources dans le centre de sécurité Azure][6]

3.  Sélectionnez une recommandation au titre des **Recommandations de la MACHINE virtuelle** pour plus d’informations ou de prendre des mesures pour configurer les contrôles nécessaires.
4.  Sélectionnez une machine virtuelle sous des **machines virtuelles** pour afficher des détails supplémentaires.

### <a name="view-security-alerts"></a>Afficher les alertes de sécurité

1.  Revenez à la lame du **Centre de sécurité** et sélectionnez la mosaïque **d’alertes de sécurité** . La blade **d’alertes de sécurité** s’ouvre et affiche la liste des alertes. L’analyse du centre de sécurité de vos journaux de sécurité et l’activité réseau génère ces alertes. Alertes des solutions de partenaires intégrées sont inclus.
![Alertes de sécurité dans le centre de sécurité Azure][7]

    > [AZURE.NOTE] Alertes de sécurité sont disponibles uniquement si la couche Standard du centre de sécurité est activée. Une version d’évaluation gratuite de 90 jours de la couche Standard est disponible. Consultez les [étapes suivantes](#next-steps) pour plus d’informations sur la façon d’obtenir le niveau Standard.

2.  Sélectionnez une alerte pour afficher des informations supplémentaires. Dans cet exemple, nous allons sélectionner **découvert des fichiers binaires du système modifié**. Les lames qui fournissent des détails supplémentaires sur l’alerte s’ouvre.
![Détails de l’alerte sécurité dans le centre de sécurité Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Afficher l’état de santé de vos solutions de partenaire

1. Revenir à la lame du **Centre de sécurité** . La mosaïque de **solutions partenaires** vous permet de surveiller, en un coup de œil, l’état de vos solutions de partenaires intégrées avec votre abonnement Azure.
2. Sélectionnez la mosaïque de **solutions partenaires** . Une blade s’ouvre et affiche une liste de vos solutions partenaire connecté au centre de sécurité.
![Solutions partenaires][9]

3. Sélectionnez une solution partenaire. Dans cet exemple, nous allons sélectionner la solution **WAF-F5** .  Une blade s’ouvre et affiche le statut de la solution de partenaires et des ressources associées de la solution. Sélectionnez la **console de la Solution** pour ouvrir l’environnement de gestion de partenaire pour cette solution.

## <a name="next-steps"></a>Étapes suivantes
Cet article vous présente les de surveillance et de la stratégie de gestion des composants de sécurité du centre de sécurité. Maintenant que vous êtes familiarisé avec le centre de sécurité, procédez comme suit :

- Configurer une stratégie de sécurité pour votre abonnement Azure. Pour plus d’informations, consultez [définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md).
- Utilisez les recommandations dans le centre de sécurité pour vous aider à protéger vos ressources d’Azure. Pour plus d’informations, consultez [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md).
- Consulter et gérer vos alertes de sécurité en cours. Pour plus d’informations, consultez [gestion et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md).
- En savoir plus sur l' [avancée des fonctionnalités de détection de menace](security-center-detection-capabilities.md) qui accompagnent la [couche Standard](security-center-pricing.md) du centre de sécurité. Une version d’évaluation gratuite de 90 jours de la couche Standard est disponible.
- Si vous avez des questions sur l’utilisation du centre de sécurité, consultez le [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
