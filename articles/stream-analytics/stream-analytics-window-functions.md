<properties
    pageTitle="Présentation des fonctions de fenêtre Analytique de flux | Microsoft Azure"
    description="Obtenir des informations sur les trois fonctions de fenêtre dans les flux Analytique (turbulent, saut, décalée)."
    keywords="distribution automatique de fenêtre, fenêtre, saut de fenêtre défilante"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="introduction-to-stream-analytics-window-functions"></a>Présentation des fonctions de fenêtre Analytique de flux

Dans en temps réel de nombreux scénarios de diffusion en continu, il est nécessaire effectuer des opérations uniquement sur les données contenues dans les fenêtres temporelles. Prise en charge native pour les fonctions de fenêtrage est une fonctionnalité clé d’Analytique de flux Azure qui déplace l’aiguille sur la productivité des développeurs lors de la création des travaux de traitement de flux de données complexes. Analytique de flux permet aux développeurs d’utiliser les fenêtres [**turbulent**](https://msdn.microsoft.com/library/dn835055.aspx), [**saut**](https://msdn.microsoft.com/library/dn835041.aspx) et [**coulissante**](https://msdn.microsoft.com/library/dn835051.aspx) pour effectuer des opérations temporelles sur des données de transmission en continu. Il est important de noter que toutes les opérations de la [fenêtre](https://msdn.microsoft.com/library/dn835019.aspx) de résultats à la **fin** de la fenêtre Sortie. La sortie de la fenêtre sera le seul événement basé sur la fonction d’agrégation utilisée. L’événement aura l’horodatage de la fin de la fenêtre, et toutes les fonctions de fenêtre sont définies avec une longueur fixe. Enfin, il est important de noter que toutes les fonctions de fenêtre doivent être utilisées dans une clause [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) .

![Concepts des fonctions de fenêtre Analytique de flux](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Qui s’écroulent fenêtre

Distribution automatique de fenêtre fonctions sont utilisées pour les segments d’un flux de données en segments de temps distincts et d’exécuter une fonction contre eux, comme dans l’exemple ci-dessous. Les principaux avantages concurrentiels d’une fenêtre qui s’écroulent sont qu’ils apparaissent, ne se chevauchent pas et un événement ne peut pas appartenir à plus d’une fenêtre qui s’écroulent.

![Fonctions de fenêtre d’Analytique de flux turbulent intro](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Fenêtre de saut

Saut saut de fonctions de fenêtre vers l’avant dans le temps d’un point fixe. Il peut être facile de considérer qu’elles qui s’écroulent windows qui peuvent se chevaucher, afin que les événements peuvent appartenir à plusieurs jeux de résultats de fenêtre de saut. Pour rendre une fenêtre saut identique à un qui s’écroulent fenêtre un spécifiez simplement la taille du saut pour être la même que la taille de la fenêtre. 

![Fonctions de fenêtre d’Analytique de flux saut intro](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Fenêtre coulissante

Les fonctions de fenêtre glissante, contrairement à windows qui s’écroulent ou saut, produisent d’une sortie **seulement** lorsqu’un événement se produit. Chaque fenêtre a au moins un événement et la fenêtre en continu se déplace vers l’avant par un € (epsilon). Comme les fenêtres de sauts, les événements peuvent appartenir à plus d’une fenêtre coulissante.

![Fonctions de fenêtre d’Analytique de flux coulissant intro](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Obtention d’aide sur les fonctions de fenêtre

Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
