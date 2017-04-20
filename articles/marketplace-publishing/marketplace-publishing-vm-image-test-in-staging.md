<properties
   pageTitle="Tester votre offre de la machine virtuelle pour le marché | Microsoft Azure"
   description="Comprendre comment tester votre image de machine virtuelle pour le marché Azure."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="hascipio" />

# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Tester votre offre de la machine virtuelle à Azure Marketplace en transit

Transit signifie le déploiement de votre point de stock privé « sandbox », où vous pouvez tester et valider ses fonctionnalités avant de le déployer sur le marché. Le point de stock s’affiche dans la zone de transit comme il le ferait pour un client qui a déployé. Votre image de machine virtuelle doit être certifié vers la mise en attente.

## <a name="step-1-push-your-offer-to-staging"></a>Étape 1 : Distribuer votre offre de transit

1. Sous l’onglet **Publier** , cliquez sur **pousser à mis en œuvre**.

    ![dessin](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Si le portail de publication vous avertit de toutes les erreurs, les corriger.
3.  Dans la **qui peut accéder à votre offre progressif ?** boîte de dialogue, entrez la liste des abonnements Azure que vous utiliserez pour afficher un aperçu de votre offre dans le [portail d’aperçu Azure](https://portal.azure.com).

    >[AZURE.NOTE] En cas de Solution et des Machines virtuelles modèles, veuillez **ne pas** les abonnements d’autorisation de type fournisseur de services cryptographiques, DreamSpark ou Azure dans Ouvrir.


    > Dans le cas d’ordinateurs virtuels, lorsque vous cliquez sur le bouton **PUSH pour la mise en œuvre**, les étapes suivantes sont effectuées derrière la scène. Vous ne pourrez pas afficher la progression de chaque étape sous l’onglet Publier dans la publication portal. Vous devez vérifier cette page à intervalles réguliers (jusqu'à ce que l’état affiche intermédiaire) pour les informations sur l’échec nécessitant une correction à partir de votre fin.

    > - Tout d’abord votre demande d’intermédiaire est transmise à l’équipe de certification qui valide le disque dur virtuel. Toutefois, si votre demande a marketing uniquement les modifications, puis la certification étape est ignorée.
    > - Une fois que la certification est terminée, la réplication de l’offre démarrer sur tous les centres de données Azure. En général, il prend 24-48hours pour la réplication mais peut prendre jusqu'à une semaine selon la taille du disque dur virtuel. Toutefois, si votre demande a marketing uniquement des modifications, la réplication est plus rapide.
    > - Lorsque la réplication est terminée, l’offre sera disponible dans le [portail Azure](http:/portal.azure.com). À ce moment, l’état devient intermédiaire dans la publication portal. Une offre progressif est visible dans le [portail Azure](http:/portal.azure.com) qu’à l’aide de l’ID e-mail associés à l’abonnement avec lequel l’offre est mis en place.

4. Connectez-vous pour [Afficher un aperçu de Azure portal](https://portal.azure.com) en utilisant l’un des abonnements Azure répertoriés à l’étape précédente.
5. Trouvez votre offre et valider vos points d’image de machine virtuelle :
  - Assurez-vous que marketing de contenu s’affiche correctement dans le marché.
  - Déploiement de bout en bout de l’image de machine virtuelle.

      ![IMG-map-portail](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [AZURE.IMPORTANT] Votre offre reste dans la zone de transit jusqu'à ce que vous notifier Microsoft via le portail de publication [onglet**Publier** > cliquez sur le bouton **« demander approbation à envoyé en Production »**] que vous êtes prêt à pousser vers la production. C’est le moment idéal pour que tous les membres de votre équipe de vérification sur tous les éléments de préparation de votre offre va répertoriés.

> La plate-forme de test est conçue pour tester l’offre en mode Aperçu par l’éditeur. Nous déconseillons fortement à l’aide de cette platofrm à des fins de commerce.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre offre est « intermédiaire » et que vous avez testé ses fonctionnalités et la commercialisation de contenu, vous pouvez passer à la phase de publication finale, **étape 4**: [déploiement de votre offre sur le marché](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Voir aussi
- [Mise en route : comment publier une offre sur le marché d’Azure](marketplace-publishing-getting-started.md)
