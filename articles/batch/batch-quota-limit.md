<properties
    pageTitle="Quotas du service et limites de commandes | Microsoft Azure"
    description="En savoir plus sur les contraintes, les limites et les quotas par défaut lot d’Azure, et comment demander le quota augmente"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2016"
    ms.author="marsma"/>

# <a name="quotas-and-limits-for-the-azure-batch-service"></a>Les quotas et les limites pour le service de traitement par lots d’Azure

Comme avec d’autres services Azure sont les limites de certaines ressources associées au service de traitement par lots. La plupart de ces limites sont appliquées par Azure au niveau du compte d’abonnement ou les quotas par défaut. Cet article décrit les paramètres par défaut, et comment vous pouvez demander le quota augmente.

Si vous prévoyez d’exécuter des charges de travail dans un lot, vous devrez augmenter une ou plusieurs des contingents au-dessus de la valeur par défaut. Si vous souhaitez déclencher un quota, vous pouvez ouvrir une ligne [demande de support client](#increase-a-quota) sans surcoût.

>[AZURE.NOTE] Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, veuillez contacter le support Azure.

## <a name="subscription-quotas"></a>Quotas d’abonnement
**Ressources**|**Limite par défaut**|**Limite maximale**
---|---|---
Comptes de lot par région par abonnement | 1 | 50

## <a name="batch-account-quotas"></a>Compte les quotas de lot
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Autres limites
**Ressources**|**Limite maximale**
---|---
[Tâches simultanées](batch-parallel-node-tasks.md) par nœud de calcul | 4 x nombre de cœurs de nœud
[Applications](batch-application-packages.md) par compte de lot        | 20
Packages d’applications par application  | 40
Taille de package d’application (chacun)       | Environ 195 Go,<sup>1</sup>

Limite de stockage azure <sup>1</sup> pour la taille de bloc maximale blob

## <a name="view-batch-quotas"></a>Afficher les quotas de lot

Permet d’afficher votre compte les quotas de traitement par lots dans [Azure portal][portal].

1. Sélectionnez les **comptes du lot** dans le portail, puis sélectionnez le compte de traitement par lots que qui vous intéressent.

2. Sélectionnez les **Propriétés** sur la lame de menu du compte par lots

3. La lame de propriétés affiche les **quotas** actuellement appliqué au compte de traitement par lots

    ![Compte les quotas de lot][account_quotas]

## <a name="increase-a-quota"></a>Un quota de

Suivez les étapes ci-dessous pour demander un quota augmentent l’utilisation du [portail Azure][portal].

1. Sélectionnez la mosaïque **aide + prise en charge** sur votre tableau de bord de portail ou le point d’interrogation (**?**) dans le coin supérieur droit du portail.

2. Sélectionnez **nouvelle demande d’assistance** > **Notions de base**.

3. Sur la lame de **Notions de base** :

    une barre d’outils. **Type de problème** > **Quota**

    b. Sélectionnez votre abonnement.

    c. **Type de quota** > **par lots**

    d. **Prise en charge de plan de** > **prise en charge de Quota - inclus**

    Cliquez sur **suivant**.

4. Sur la lame du **problème** :

    une barre d’outils. Sélectionnez une **gravité** en fonction de votre [impact sur l’activité][support_sev].

    b. Dans les **Détails**, spécifiez chaque quota que vous souhaitez modifier le nom de compte et la nouvelle limite.

    Cliquez sur **suivant**.

5. Sur la blade **d’informations de Contact** :

    une barre d’outils. Sélectionnez une **méthode de contact préférée**.

    b. Vérifiez et entrez les informations de contact requises.

    Cliquez sur **créer** pour soumettre la demande de prise en charge.

Une fois que vous avez soumis votre demande de support, prise en charge Azure prendra contact avec vous. Notez que la fin de la demande peut prendre jusqu'à 2 jours ouvrés.

## <a name="related-topics"></a>Rubriques connexes

* [Créer un compte Azure lot via le portail Azure](batch-account-create-portal.md)

* [Présentation des fonctionnalités de traitement par lots Azure](batch-api-basics.md)

* [Abonnement Azure et des limites de service, des quotas et des contraintes](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
