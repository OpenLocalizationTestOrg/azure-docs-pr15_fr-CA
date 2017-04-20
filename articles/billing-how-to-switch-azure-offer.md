<properties
    pageTitle="Utiliser votre abonnement Azure offre un autre | Microsoft Azure"
    description="Découvrez comment modifier votre abonnement Azure et basculer vers une autre offre via le portail de gestion d’abonnement"
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="genli"/>

# <a name="switch-your-azure-subscription-to-another-offer"></a>Basculer votre abonnement Azure pour une autre offre

En tant que [paiement](https://azure.microsoft.com/offers/ms-azr-0003p/) client, vous pourrez passer votre abonnement Azure pour une autre offre dans le [Centre de compte](https://account.windowsazure.com/Subscriptions). Par exemple, vous pouvez utiliser cette fonction pour tirer parti des [crédits mensuels pour les abonnés de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si vous êtes sur la [Version d’évaluation gratuite](https://azure.microsoft.com/free/), découvrez comment [mettre à niveau pour le paiement](billing-upgrade-azure-subscription.md).

#### <a name="whats-supported"></a>Ce qui est pris en charge :

| De                                                              | À                                                                                      |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Paiement à l’utilisation de développement/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)              |
| [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Professionnel](https://azure.microsoft.com/offers/ms-azr-0059p/)          |
| [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)     |
| [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Plates-formes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)                      |
| [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            |
| [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [AZURE.NOTE] D’autres offrent des modifications, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
    
## <a name="switch-subscription-offer"></a>Offres d’abonnement à commutateur

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.  Connectez-vous au [Centre du compte Azure](https://account.windowsazure.com/Subscriptions).

2.  Sélectionnez votre abonnement de paiement.

3.  Cliquez sur **Basculer vers une autre offre**. Le bouton n’est disponible que si vous êtes sur le paiement à l’utilisation et une fonctionnalité accrue avec la première période de facturation.

    ![Notez le bouton offre de basculer sur le côté droit de la page](./media/billing-how-to-switch-azure-offer/switchbutton.png)
    
4.  À partir de la liste des offres d’en que votre abonnement peut être basculé, **Sélectionnez celle de que votre choix** . Cette liste varie en fonction de l’appartenance qui est associé à votre compte. Si rien n’est disponible, vérifiez la [liste des offres disponibles, vers que vous pouvez basculer](#whats-supported) et assurez-vous que vous disposez de l’appartenance à droite. 

    ![Sélectionnez une offre que vous voulez basculer vers](./media/billing-how-to-switch-azure-offer/selectoffer.png)

5.  En fonction de l’offre que vous envisagez de passer à, vous pouvez voir une note sur l’impact du passage. Parcourez cette liste soigneusement et suivez les instructions avant de continuer.

    ![Passez en revue les notes](./media/billing-how-to-switch-azure-offer/thingstonote.png)

6.  Vous pouvez renommer votre abonnement. Par défaut, nous la définissons le nouveau nom de l’offre. Cliquez sur **Commutateur offre** pour terminer le processus.

    ![Cliquez sur le bouton vert](./media/billing-how-to-switch-azure-offer/confirmpage.png)

7.  Succès ! Votre abonnement est devenu la nouvelle offre.

## <a name="why-cant-i-switch-offers"></a>Pourquoi n’arrive pas à changer les offres ?

Vous ne voyez pas de **Basculer vers une autre offre** si :

- Vous n’êtes pas sur le [paiement](https://azure.microsoft.com/offers/ms-azr-0003p/). Actuellement uniquement les abonnements de paiement à l’utilisation peuvent être basculés en une autre offre.

    - Si vous êtes sur la [Version d’évaluation gratuite](https://azure.microsoft.com/free/), découvrez comment [mettre à niveau pour le paiement](billing-upgrade-azure-subscription.md).

    - Pour passer de proposer un autre abonnement, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- Vous êtes toujours dans votre première période de facturation ; Vous devez attendre votre première période de facturation se termine avant de pouvoir basculer offres.

Vous voyez **Aucune offre disponible dans votre région ou votre pays à ce moment** si :

- Vous n’avez pas droit à tous les commutateurs offre. Vérifiez la [liste des offres disponibles, vers que vous pouvez basculer](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>En quoi consiste à mon service de commutation Azure offre et de facturation ?

Voici les détails de ce qui se passe lorsque vous basculez Azure plans dans le centre de compte.

### <a name="access-to-services"></a>Accès aux services

Il n’y a aucun temps d’arrêt de service pour tous les utilisateurs associés à l’abonnement. Cependant, l’offre que vous basculez vers peut-être avoir des restrictions. Par exemple, certaines offres interdisent l’utilisation en production, vous devez déplacer les ressources de production vers un autre abonnement.

### <a name="billing"></a>Facturation

Le jour que vous passez, une facture est générée pour tous les frais en suspens. Ensuite, votre abonnement est facturé par les conditions de tarification de la nouvelle offre. La date de facturation d’abonnement change à la date à laquelle vous avez modifié des offres. L’utilisation et les données de facturation avant la modification de l’offre n’est pas conservée, nous vous recommandons de télécharger une copie avant de basculer.

> [AZURE.NOTE] En raison de contraintes liées à la facturation, les commutateurs de l’offre ne sont pas possibles dans le premier cycle de facturation après la création d’un abonnement.

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Puis-je migrer à partir de paiement pour le [Fournisseur de solutions de Cloud](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) ou [Accord entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA) ?

Nous avons actuellement ne prend en charge commutateur offre de fournisseur de services cryptographiques ou EA dans le centre de comptes. Pour déplacer votre abonnement existant dans EA, avoir votre administrateur d’inscription ajouter votre compte à l’AE. Puis, vous recevez un message électronique d’invitation. Lorsque vous suivez les instructions à accepter l’invitation, vos abonnements sont automatiquement déplacés dans l’accord de l’entreprise. Pour migrer vers un fournisseur de services cryptographiques, reportez-vous à la section [Migration d’abonnement Azure pour fournisseur de services cryptographiques](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Gérer les rôles d’administrateur](billing-add-change-azure-subscription-administrator.md) pour votre abonnement

- Effectuer le suivi de l’utilisation en [téléchargeant des données d’utilisation et de la facture](billing-download-azure-invoice-daily-usage-date.md)

## <a name="need-help-contact-support"></a>Besoin d’aide ? Contactez le support technique.

Si vous en avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir votre problème résolu rapidement.
