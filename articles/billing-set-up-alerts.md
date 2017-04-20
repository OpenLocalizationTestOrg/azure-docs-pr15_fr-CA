<properties
    pageTitle="Définir des alertes pour vos abonnements Microsoft Azure de facturation | Microsoft Azure"
    description="Décrit comment vous pouvez définir des alertes sur votre facture Azure afin d’éviter les surprises facturation."
    services=""
    documentationCenter=""
    authors="vikdesai"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/18/2016"
    ms.author="vikdesai"/>

# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Configurer des alertes de facturation pour vos abonnements Microsoft Azure

Êtes-vous préoccupé par combien vous dépensez chaque mois pour votre abonnement Azure ? Si vous êtes l’administrateur du compte pour un abonnement Azure, vous pouvez utiliser le Service d’alerte de facturation Azure pour créer personnalisé alertes de facturation qui vous aident à surveiller et gérer les activités de facturation pour vos comptes Azure.

Ce service est un service d’aperçu, la première chose que vous avez à faire est d’ouvrir il. Visitez [la page Aperçu des fonctionnalités](https://account.windowsazure.com/PreviewFeatures) dans le portail de gestion de compte Azure pour activer cette fonctionnalité.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Définir les destinataires alertes de seuil et de la messagerie

Après avoir reçu le courriel de confirmation que le service de facturation est activé pour votre abonnement, visitez [la page d’abonnements](https://account.windowsazure.com/Subscriptions) dans le portail de compte. Cliquez sur l’abonnement que vous souhaitez surveiller, puis cliquez sur **alertes**.

![][Image1]

Ensuite, cliquez sur **Ajouter une alerte** pour créer votre premier - vous pouvez configurer un total de cinq alertes de facturation par abonnement, avec un seuil différent et deux destinataires de courrier électronique pour chaque alerte.

![][Image2]

Lorsque vous ajoutez une alerte, vous lui donnez un nom unique, choisissez un seuil de dépense et sélectionnez les adresses de messagerie où seront envoyés les alertes. Lorsque vous définissez le seuil, vous pouvez choisir une **Facturation totale** ou un **Crédit monétaire** dans la liste **D’alerte** . Pour un total de facturation, une alerte est envoyée lors de l’inscription des dépenses dépassent le seuil. Pour obtenir un crédit monétaire, une alerte est envoyée lorsque les crédits monétaires en-dessous de la limite. Crédits monétaires s’applique généralement à des versions d’évaluation gratuites et les abonnements associés à des comptes MSDN.

![][Image3]

Azure prend en charge de n’importe quelle adresse de messagerie, mais ne vérifie pas que les travaux d’adresse e-mail, vérifiez donc vos fautes de frappe.

## <a name="check-on-your-alerts"></a>Vérifier vos alertes

Après avoir défini des alertes, le centre compte les répertorie et indique combien de plus vous pouvez configurer. Pour chaque alerte, vous voyez la date et heure de qu'envoi, qu’il s’agisse d’une alerte pour la facturation totale ou crédit monétaire et la limite que vous configurez. Le format de date et d’heure est 24 heures heure universelle coordonnée (UTC) et la date est le format aaaa-mm-jj. Cliquez sur le signe plus pour une alerte dans la liste pour la modifier, ou cliquez sur la Corbeille pour le supprimer.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
