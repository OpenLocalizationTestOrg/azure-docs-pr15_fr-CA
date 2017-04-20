<properties 
   pageTitle="Table de limites de flux Analytique"
   description="Décrit les limites du système et la taille recommandée pour les connexions et les composants de flux de données Analytique."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jeffstok" />

| Identificateur de la limite | Limite de       | Commentaires |
|----------------- | ------------|--------- |
| Nombre maximal d’unités de diffusion en continu par abonnement par région | 50 | Une demande d’augmentation des unités de transmission en continu de votre abonnement au-delà de 50 est possible en contactant le [Support technique de Microsoft](https://support.microsoft.com/en-us). |
| Débit maximal d’une unité de transmission en continu | 1 Mo / s * | Débit maximal par SU dépend du scénario. Débit réel peut être inférieur et dépend de la complexité de la requête et de partitionnement. Vous trouverez plus de détails dans l’article de [travaux échelle Azure flux Analytique pour augmenter le débit](../articles/stream-analytics/stream-analytics-scale-jobs.md) . |
| Nombre maximal d’entrées par projet | 60 | Il existe une limite maximale de 60 entrées par la tâche de flux de données Analytique. |
| Nombre maximal de sorties par projet | 60 | Il existe une limite maximale de 60 sorties par la tâche de flux de données Analytique. |
| Nombre maximal de fonctions par projet | 60 | Il existe une limite maximale de 60 fonctions par la tâche de flux de données Analytique. |
| Nombre maximal de travaux par région | 1500 | Chaque abonnement peut avoir jusqu'à 1500 des travaux par région géographique. |