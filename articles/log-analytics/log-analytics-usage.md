<properties
    pageTitle="Analyser l’utilisation de données dans le journal Analytique | Microsoft Azure"
    description="Vous pouvez utiliser la page de l’utilisation de journal Analytique permet d’afficher la quantité de données est envoyé au service OMS."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="analyze-data-usage-in-log-analytics"></a>Analyser l’utilisation de données dans le journal Analytique

Analytique de journal dans la Suite de gestion des opérations (OMS) collecte les données et les envoie au service OMS périodiquement.  Vous pouvez utiliser la page de **l’utilisation** permet d’afficher la quantité de données est envoyé au service OMS. La page **d’utilisation** vous indique également la quantité de données est envoyé quotidiennement par les solutions et la fréquence à laquelle les serveurs envoient des données.

>[AZURE.NOTE] Si vous avez un compte gratuit créé en utilisant le [site Web d’OMS](http://www.microsoft.com/oms), vous êtes limité à l’envoi quotidien de 500 Mo de données au service OMS. Si vous atteignez la limite quotidienne, analyse des données va arrêter et reprendre au début de la journée suivante. Vous devrez également renvoyer des données qui n’a pas été acceptées ou traitées par l’OMS.

Vous pouvez consulter votre utilisation à l’aide de la mosaïque de **l’utilisation** du tableau de bord de **présentation** de l’OMS.

![mosaïque de l’utilisation](./media/log-analytics-usage/usage-tile.png)

Si vous avez dépassé votre limite quotidienne d’utilisation, ou si vous êtes proche de votre limite, vous pouvez éventuellement supprimer une solution pour réduire la quantité de données que vous envoyez au service OMS. Pour plus d’informations sur la suppression des solutions, reportez-vous à la section [Analytique de journal ajouter des solutions à partir de la galerie de Solutions](log-analytics-add-solutions.md).

![tableau de bord utilisation](./media/log-analytics-usage/usage-dashboard.png)

**L’utilisation** de page affiche les informations suivantes :

- Utilisation moyenne par jour
- Utilisation des données pour chaque solution au cours des 30 derniers jours
- Combien d’envoyer des données, les serveurs de votre environnement au service OMS au cours des 30 derniers jours
- Votre plan de données couche et le coût estimé de la tarification
- Informations sur votre contrat de niveau de service (SLA), y compris la durée OMS pour traiter vos données

## <a name="to-work-with-usage-data"></a>Pour travailler avec les données d’utilisation

1. Dans la page **vue d’ensemble** , cliquez sur la mosaïque de **l’utilisation** .
2. Sur la page **d’utilisation** , permet d’afficher les catégories d’utilisation qui montrent les zones que vous êtes préoccupé par.
3. Si vous avez une solution qui consomme trop de votre quota quotidien de téléchargement, vous pouvez envisager la suppression de cette solution.

## <a name="to-view-your-estimated-cost-and-billing-information"></a>Pour afficher le coût estimé et les informations de facturation
1. Dans la page **vue d’ensemble** , cliquez sur la mosaïque de **l’utilisation** .
2. Sur la page de **l’utilisation** , sous **utilisation**, cliquez sur le chevron (**>**) en regard de **coût de l’estimation**.
3. Dans l’étendue des détails de **votre plan de données** , vous pouvez voir votre estimation coût mensuel.  
    ![Votre plan de données](./media/log-analytics-usage/usage-data-plan.png)
4. Si vous souhaitez afficher vos informations de facturation, cliquez sur **affichage de ma facture** pour afficher les informations sur votre abonnement.
    - Dans la page Abonnements, cliquez sur votre abonnement pour afficher des informations et une liste d’éléments de la ligne de l’utilisation.  
        ![abonnement](./media/log-analytics-usage/usage-sub01.png)
    - Sur la page de résumé de votre abonnement, vous pouvez effectuer de nombreuses tâches de gérer et d’afficher des détails supplémentaires à propos de votre abonnement.  
        ![Détails de l’abonnement](./media/log-analytics-usage/usage-sub02.png)

## <a name="to-view-data-batches-for-your-sla"></a>Pour afficher les données des lots pour vos accords SLA
1. Dans la page **vue d’ensemble** , cliquez sur la mosaïque de **l’utilisation** .
2. **Contrat de niveau de Service**, cliquez sur **Détails de télécharger le contrat SLA**.
3. Un fichier Excel XLSX est téléchargé pour pouvoir les consulter.  
    ![Détails du contrat SLA](./media/log-analytics-usage/usage-sla-details.png)

## <a name="next-steps"></a>Étapes suivantes

- Reportez-vous à la section de [recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher des informations détaillées collectées par les solutions.
