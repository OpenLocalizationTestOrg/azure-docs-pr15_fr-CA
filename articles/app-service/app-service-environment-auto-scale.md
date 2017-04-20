<properties
    pageTitle="AutoScaling et environnement de Service d’application | Microsoft Azure"
    description="AutoScaling et environnement de Service d’application"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"
/>

# <a name="autoscaling-and-app-service-environment"></a>AutoScaling et environnement de Service d’application

*Autoscaling*prend en charge les environnements de Service d’application Azure. Vous pouvez les pools de travailleur échelle automatique en fonction de mesures ou de planification.

![Options d’échelle pour un pool de travail.][intro]

AutoScaling optimise l’utilisation des ressources par la croissance et la réduction d’un environnement de Service d’application pour respecter votre budget, et de charger le profil automatiquement.

## <a name="configure-worker-pool-autoscale"></a>Configurer l’échelle de pool de travail

Vous pouvez accéder à la fonctionnalité de l’échelle à partir de l’onglet **paramètres** de travail du pool.

![Onglet Paramètres de travail du pool.][settings-scale]

À partir de là, que l’interface doit être relativement familier puisqu’il est la même expérience que vous voyez lorsque vous mettre à l’échelle un plan de Service de l’application. 

![Paramètres d’échelle manuelle.][scale-manual]

Vous pouvez également configurer un profil autoscale.

![Paramètres d’échelle.][scale-profile]

Profils d’échelle sont utiles définir des limites sur votre échelle. De cette façon, vous pouvez avoir des performances homogènes rencontrer en définissant une valeur d’échelle inférieure (1) et d’un embout de dépenses prévisibles en définissant une limite supérieure (2).

![Paramètres d’échelle dans le profil.][scale-profile2]

Après avoir défini un profil, vous pouvez ajouter des règles d’autoscale pour augmenter ou réduire le nombre d’instances dans le pool de travail dans les limites définies par le profil. Règles d’échelle sont basées sur les mesures.

![Règle d’échelle.][scale-rule]

 Tout pool de travail ou les mesures frontal peuvent servir à définir des règles de l’échelle. Ces mesures sont les mêmes mesures que vous pouvez surveiller dans les graphiques de lame de ressource ou définir des alertes pour.

## <a name="autoscale-example"></a>Exemple d’échelle automatique

Échelle automatique d’un environnement de Service d’application est illustré à la fois en progressant dans un scénario.

Cet article décrit toutes les considérations nécessaires lorsque vous configurez l’échelle automatique. L’article vous guide tout au long des interactions qui entrent en jeu si l'on autoscaling environnements de Service d’application qui sont hébergées dans un environnement de Service d’application.

### <a name="scenario-introduction"></a>Présentation du scénario

Frank est un administrateur système pour une entreprise qui a migré d’une partie des charges de travail qu’il gère pour un environnement de Service d’application.

L’environnement de Service d’application est configuré à l’échelle manuelle comme suit :

* **Avant de se termine :** 3
* **Pool de travail 1**: 10
* **Pool de travail 2**: 5
* **Pool de travail 3**: 5

Pool de travail 1 est utilisé pour les charges de travail, alors que le pool de travail 2 et pool de travail 3 sont utilisés pour les charges de travail de développement et de l’assurance qualité (AQ).

Les plans de Service d’application pour l’AQ et le développement sont configurés à l’échelle manuelle. La plan de Service de l’application de production est définie à l’échelle automatique pour traiter avec des variations de charge et le trafic.

Il est très familier avec l’application. Il sait que les heures de pointe de charge sont entre 9:00 AM et 6:00 PM, car il s’agit d’une application de métier (LOB) que les employés utilisent lorsqu’ils sont au bureau. Occupé est par la suite, lorsque les utilisateurs sont effectuées pour ce jour. En dehors des heures de pointe, il existe toujours une charge, car les utilisateurs peuvent accéder à distance à l’application à l’aide de leurs périphériques mobiles ou des ordinateurs à la maison. La plan de Service de l’application de production est déjà configurée à l’échelle automatique en fonction de l’utilisation du processeur avec les règles suivantes :

![Paramètres spécifiques de l’application métier.][asp-scale]

|   **Profil d’échelle automatique – jours de la semaine – service d’application**     |   **Profil d’échelle automatique – week-ends – service d’application**     |
|   ----------------------------------------------------    |   ----------------------------------------------------    |
|   **Nom :** Profil de jour de la semaine                               |   **Nom :** Profil de week-end                               |
|   **D’échelle :** Règles de performances et de planification            |   **D’échelle :** Règles de performances et de planification            |
|   **Profil :** Jours de la semaine                                   |   **Profil :** Week-end                                    |
|   **Type :** Périodicité                                    |   **Type :** Périodicité                                    |
|   **Plage de la cible :** instances de 5 à 20                     |   **Plage de la cible :** instances de 3 à 10                     |
|   **Jours :** Lundi, mardi, mercredi, jeudi, vendredi  |   **Jours :** Samedi, dimanche                              |
|   **Heure de début :** 9:00 AM                                 |   **Heure de début :** 9:00 AM                                 |
|   **Fuseau horaire :** UTC : 08                                   |   **Fuseau horaire :** UTC : 08                                   |
|                                                           |                                                           |
|   **Règle d’échelle (échelle vers le haut)**                           |   **Règle d’échelle (échelle vers le haut)**                           |
|   **Ressource :** Production (environnement de Service d’application)      |   **Ressource :** Production (environnement de Service d’application)      |
|   **Métrique :** % DE L’UC                                       |   **Métrique :** % DE L’UC                                       |
|   **Opération :** Supérieure à 60 %                         |   **Opération :** Supérieure à 80 %                         |
|   **Durée :** 5 Minutes                                 |   **Durée :** 10 Minutes                                |
|   **Heure d’agrégation :** Moyenne                           |   **Heure d’agrégation :** Moyenne                           |
|   **Action :** Augmenter le nombre de 2                         |   **Action :** Augmenter le nombre de 1                         |
|   **Refroidir (minutes) :** 15                             |   **Refroidir (minutes) :** 20                             |
|                                                           |                                                           |
  	|   **Règle d’échelle (échelle vers le bas)**                     |   **Règle d’échelle (échelle vers le bas)**                         |
|   **Ressource :** Production (environnement de Service d’application)      |   **Ressource :** Production (environnement de Service d’application)      |
|   **Métrique :** % DE L’UC                                       |   **Métrique :** % DE L’UC                                       |
|   **Opération :** Moins de 30 %                            |   **Opération :** Moins de 20 %                            |
|   **Durée :** 10 minutes                                |   **Durée :** 15 minutes                                |
|   **Heure d’agrégation :** Moyenne                           |   **Heure d’agrégation :** Moyenne                           |
|   **Action :** Diminuer le nombre par 1                         |   **Action :** Diminuer le nombre par 1                         |
|   **Refroidir (minutes) :** 20                             |   **Refroidir (minutes) :** 10                             |

### <a name="app-service-plan-inflation-rate"></a>Service de l’application du taux d’inflation plan

Les plans de Service d’application qui sont configurés à l’échelle automatique faire à un taux maximal par heure. Ce taux peut être calculé sur base les valeurs fournies dans la règle de l’échelle.

Il est important pour autoscale d’environnement de Service d’application de compréhension et du calcul du *taux d’inflation de plan de Service de l’application* , car les changements d’échelle à un pool de travail ne sont pas instantanées.

Le taux d’inflation App Service plan est calculé comme suit :

![Calcul de taux d’inflation de plan de Service de l’application.][ASP-Inflation]

Selon l’échelle automatique – évolution la règle pour le profil du jour de la semaine de la production de plan de Service de l’application :

![Service d’application les taux d’inflation de plan pour les jours de la semaine en fonction de l’échelle automatique – évolution de règle.][Equation1]

Dans le cas de l’échelle automatique – évolution la règle pour le profil de week-end de la production de plan de Service d’application, la formule se résoudra en :

![Service d’application les taux d’inflation de plan pour les week-ends en fonction de l’échelle automatique – évolution de règle.][Equation2]

Cette valeur peut également être calculée pour les opérations de réduction.

En fonction de l’échelle automatique – règle d’échelle vers le bas pour le profil du jour de la semaine de la production de plan de Service de l’application, il se présente comme suit :

![Service d’application les taux d’inflation de plan pour les jours de la semaine en fonction de l’échelle automatique – règle d’échelle vers le bas.][Equation3]

Dans le cas de l’échelle automatique – règle d’échelle vers le bas pour le profil de week-end de la production de plan de Service d’application, la formule se résoudra en :  

![Service d’application les taux d’inflation de plan pour les week-ends en fonction de l’échelle automatique – règle d’échelle vers le bas.][Equation4]

La plan de Service de l’application de production peut croître à un taux maximal de huit heures instances au cours de la semaine et quatre heures instances pendant le week-end. Il peut libérer des instances à un taux maximal de quatre instances/heure pendant la semaine et six instances/heure pendant les week-ends.

Si plusieurs plans de Service d’application sont hébergés dans un pool de travail, vous devez calculer le *taux d’inflation total* comme la somme du taux d’inflation pour tous les plans de Service d’application qui sont en cours d’hébergement dans ce pool de travail.

![Calcul du taux d’inflation total pour plusieurs plans de Service de l’application hébergée dans un pool de travail.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Le taux d’inflation App Service plan permet de définir des règles de travail pool échelle automatique

Travailleur qui hébergent des plans de Service d’application qui sont configurés à l’échelle automatique doivent être affectée à une mémoire tampon de la capacité des pools. Permet à la mémoire tampon pour les opérations d’échelle agrandir et réduire le plan de Service de l’application en fonction des besoins. La mémoire tampon minimum serait la calculé Total App Service Plan du taux d’Inflation.

Étant donné que les opérations d’échelle App Service environnement prennent un certain temps à appliquer, toute modification doit comptabiliser plus les modifications de la demande qui peuvent se produire lorsqu’une opération d’échelle est en cours. Pour prendre en charge cette latence, nous vous recommandons d’utiliser Total App Service planifier l’Inflation taux calculé comme le nombre minimal d’instances qui sont ajoutés pour chaque opération de l’échelle.

Avec ces informations, Frank peut définir les règles et le profil d’échelle suivantes :

![Règles de profil AutoScale par exemple de cœur de métier.][Worker-Pool-Scale]

|   **Profil d’échelle automatique – jours de la semaine**                        |   **Profil d’échelle automatique – les week-ends**                |
|   ----------------------------------------------------    |   --------------------------------------------    |
|   **Nom :** Profil de jour de la semaine                               |   **Nom :** Profil de week-end                       |
|   **D’échelle :** Règles de performances et de planification            |   **D’échelle :** Règles de performances et de planification    |
|   **Profil :** Jours de la semaine                                   |   **Profil :** Week-end                            |
|   **Type :** Périodicité                                    |   **Type :** Périodicité                            |
|   **Plage de la cible :** 13 à 25 instances                    |   **Plage de la cible :** instances de 6 à 15             |
|   **Jours :** Lundi, mardi, mercredi, jeudi, vendredi  |   **Jours :** Samedi, dimanche                      |
|   **Heure de début :** 7:00 AM                                 |   **Heure de début :** 9:00 AM                         |
|   **Fuseau horaire :** UTC : 08                                   |   **Fuseau horaire :** UTC : 08                           |
|                                                           |                                                   |
|   **Règle d’échelle (échelle vers le haut)**                           |   **Règle d’échelle (échelle vers le haut)**                   |
|   **Ressource :** Pool de travail 1                             |   **Ressource :** Pool de travail 1                     |
|   **Métrique :** WorkersAvailable                            |   **Métrique :** WorkersAvailable                    |
|   **Opération :** Inférieur à 8                              |   **Opération :** Moins de 3                      |
|   **Durée :** 20 minutes                                |   **Durée :** 30 minutes                        |
|   **Heure d’agrégation :** Moyenne                           |   **Heure d’agrégation :** Moyenne                   |
|   **Action :** Augmentez le nombre par 8                         |   **Action :** Augmentez le nombre par 3                 |
|   **Refroidir (minutes) :** 180                            |   **Refroidir (minutes) :** 180                    |
|                                                           |                                                   |
|   **Règle d’échelle (échelle vers le bas)**                         |   **Règle d’échelle (échelle vers le bas)**                 |
|   **Ressource :** Pool de travail 1                             |   **Ressource :** Pool de travail 1                     |
|   **Métrique :** WorkersAvailable                            |   **Métrique :** WorkersAvailable                    |
|   **Opération :** Supérieur à 8                           |   **Opération :** Supérieure à 3                   |
|   **Durée :** 20 minutes                                |   **Durée :** 15 minutes                        |
|   **Heure d’agrégation :** Moyenne                           |   **Heure d’agrégation :** Moyenne                   |
|   **Action :** Diminuer le nombre par 2                         |   **Action :** Diminuer le nombre par 3                 |
|   **Refroidir (minutes) :** 120                            |   **Refroidir (minutes) :** 120                    |

La plage cible définie dans le profil est calculée par les instances minimales définies dans le profil pour le service d’application + mémoire tampon.

La plage maximale est la somme de toutes les plages maximales pour tous les plans de Service de l’application hébergées dans le pool de travail.

Le nombre d’augmentation de l’échelle des règles doit être configuré au moins 1 X le taux d’Inflation de Plan d’application Service d’échelle.

Nombre de diminution peut être ajusté pour quelque chose entre 1/2 X ou 1 X le taux d’Inflation de Plan d’application Service d’échelle vers le bas.

### <a name="autoscale-for-front-end-pool"></a>Échelle automatique pour le pool frontal

Règles d’autoscale frontaux sont plus simples que pour les pools de travailleur. Essentiellement, vous devez  
Vérifiez que la durée de la mesure et les minuteurs de ralentissement que les opérations d’échelle sur un plan de Service d’application ne sont pas instantanées.

Dans ce scénario, Michel sait que le taux d’erreur augmente après que frontaux atteint 80 % de l’UC et définit la règle d’échelle automatique pour augmenter les instances comme suit :

![Paramètres d’échelle automatique pour un pool frontal.][Front-End-Scale]

|   **Profil d’échelle automatique – frontales**              |
|   --------------------------------------------    |
|   **Nom :** Échelle automatique – frontales                |
|   **D’échelle :** Règles de performances et de planification    |
|   **Profil :** Tous les jours                           |
|   **Type :** Périodicité                            |
|   **Plage de la cible :** instances de 3 à 10             |
|   **Jours :** Tous les jours                              |
|   **Heure de début :** 9:00 AM                         |
|   **Fuseau horaire :** UTC : 08                           |
|                                                   |
|   **Règle d’échelle (échelle vers le haut)**                   |
|   **Ressource :** Pool frontal                    |
|   **Métrique :** % DE L’UC                               |
|   **Opération :** Supérieure à 60 %                 |
|   **Durée :** 20 minutes                        |
|   **Heure d’agrégation :** Moyenne                   |
|   **Action :** Augmentez le nombre par 3                 |
|   **Refroidir (minutes) :** 120                    |
|                                                   |
|   **Règle d’échelle (échelle vers le bas)**                 |
|   **Ressource :** Pool de travail 1                     |
|   **Métrique :** % DE L’UC                               |
|   **Opération :** Moins de 30 %                    |
|   **Durée :** 20 Minutes                        |
|   **Heure d’agrégation :** Moyenne                   |
|   **Action :** Diminuer le nombre par 3                 |
|   **Refroidir (minutes) :** 120                    |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
