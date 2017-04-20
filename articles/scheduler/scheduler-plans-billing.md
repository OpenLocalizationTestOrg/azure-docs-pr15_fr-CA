<properties
 pageTitle="Plans et facturation dans le planificateur Azure"
 description="Plans et facturation dans le planificateur Azure"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="plans-and-billing-in-azure-scheduler"></a>Plans et facturation dans le planificateur Azure

## <a name="job-collection-plans"></a>Plan de collecte de travail

Les collections de projet sont l’entité facturable dans le planificateur d’Azure. Collections de travail contiennent un certain nombre de tâches et se présentent sous trois plans – gratuit, Standard et Premium – qui sont décrites ci-dessous.

|**Plan de collecte de travail**|**Nombre maximum de travaux par Collection de travail**|**Périodicité de max**|**Collections de max travail par abonnement**|**Limites**|
|:---|:---|:---|:---|:---|
|**Gratuit**|5 tâches par collection de travail|Une fois par heure. Impossible d’exécuter les tâches plus fréquemment qu’une fois par heure|Un abonnement est autorisé jusqu'à 1 collection de travail libre|Ne peut pas utiliser [l’objet d’autorisation sortant HTTP](scheduler-outbound-authentication.md)
|**Standard**|50 tâches par collection de travail|Une fois par minute. Impossible d’exécuter les tâches plus fréquemment qu’une fois par minute|Un abonnement est autorisé jusqu'à 100 collections de tâche standard|Accès à l’ensemble complet des fonctionnalités du planificateur|
|**P10 Premium**|50 tâches par collection de travail|Une fois par minute. Impossible d’exécuter les tâches plus fréquemment qu’une fois par minute|Un abonnement est autorisé jusqu'à 10 000 collections de travail P10 Premium. Pour plus d’informations, <a href="mailto:wapteams@microsoft.com">Contactez-nous</a> .|Accès à l’ensemble complet des fonctionnalités du planificateur|
|**P20 Premium**|travaux de 1000 par collection de travail|Une fois par minute. Impossible d’exécuter les tâches plus fréquemment qu’une fois par minute|Un abonnement est autorisé jusqu'à 10 000 collections de travail P20 Premium. Pour plus d’informations, <a href="mailto:wapteams@microsoft.com">Contactez-nous</a> .|Accès à l’ensemble complet des fonctionnalités du planificateur|

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Mises à niveau et rétrogradations de Plans de travail Collection

Vous pouvez promouvoir ou rétrograder un plan de collecte de travail à tout moment entre les plans libre, Standard et Premium. Toutefois, lors d’un déclassement à une collection de travail libre, le déclassement peut échouer pour l’une des raisons suivantes :

- Une tâche libre collection existe déjà dans l’abonnement
- Une tâche dans la collection de travail avec une périodicité plus élevée que la limite autorisée pour les projets de collections de travail libre. La périodicité maximale autorisée dans une collection de travail libre est une fois par heure
- Il existe plus de 5 tâches dans la collection de travail
- Un travail de la collection de la tâche a une action de HTTP ou HTTPS qui utilise un [objet d’autorisation sortant HTTP](scheduler-outbound-authentication.md)

## <a name="billing-and-azure-plans"></a>Plans de facturation et Azure

Les abonnements ne seront pas imputés gratuitement les collections de projet. Si vous avez plus de 100 collections de travail standard (10 unités facturation standard), puis il est une meilleure affaire pour que toutes les collections de projet dans le plan de prime.

Si vous avez une seule collection de tâche standard et une collection de travail premium, vous êtes facturé un standard facturation _et_ une prime facturation unité. Les opérations de service Planificateur basées sur le nombre de collections de tâche active qui sont définies sur standard ou prime ; ce thème est expliqué plus loin dans les deux sections suivantes.

## <a name="standard-billable-units"></a>Unités facturables standard

Une unité facturable standard peut inclure jusqu'à 10 collections de travail standard. Dans la mesure où une collection de travail standard peut comporter jusqu'à 50 tâches par collection de travail, une unité de facturation standard permet un abonnement pour que les tâches jusqu'à 500 – jusqu'à 22 millions d’exécution de travaux par mois.

Si vous avez entre 1 et 10 collections de travail standard, vous serez facturé pour 1 unité de facturation standard. Si vous avez entre 11 et 20 collections de travail standard, vous serez facturé pour 2 unités de facturation standard. Si vous avez entre 21 et collections de 30 tâches standard, vous serez facturé pour 3 unités de facturation standard et ainsi de suite.

## <a name="p10-premium-billable-units"></a>P10 Unités facturables de prime

Une unité de facturable P10 premium peut inclure jusqu'à 10 000 collections de travail P10 premium. Dans la mesure où une collection de travail P10 premium peut avoir jusqu'à 50 tâches par collection de travail, une unité de facturation de prime permet un abonnement d’avoir jusqu'à 500 000 tâches – jusqu'à l’exécution des travaux près 22 milliards de dollars par mois.

Si vous avez entre 1 et 10 000 collections de travail premium, vous serez facturé pour 1 unité de facturation premium P10. Si vous avez entre 10,001 et des collections de travail 20 000 premium, vous allez être facturé pour les 2 unités de facturation premium P10 et ainsi de suite.

Ainsi, les collections de travail P10 premium ont les mêmes fonctionnalités que les collections de projet standard mais fournissent une rupture de prix dans le cas où votre application nécessite un grand nombre de collections de la tâche.

## <a name="p20-premium-billable-units"></a>P20 Unités Premium facturable

Une unité de facturable P20 premium peut inclure jusqu'à 5 000 collections de travail P20 premium. Dans la mesure où une collection de travail P20 premium peut avoir jusqu'à 1 000 tâches par collection de travail, une unité de facturation premium permet un abonnement d’avoir jusqu'à 5 000 000 tâches – jusqu'à l’exécution des travaux près 220 milliards de dollars par mois.

Collections de travail P20 premium offre les mêmes fonctionnalités que les collections de projet P10 premium, mais prend également en charge un travaux numéro supérieur par collection de travail et un plus grand nombre total de travaux globales à prime P10 vous permettant d’accroître l’évolutivité.

## <a name="billing-and-active-status"></a>Facturation et Active l’état

Les collections de projet sont toujours actives, à moins que votre abonnement complet ont été investis dans un état désactivé temporaire en raison de problèmes de facturation. La seule manière de garantir qu’une collection de travail n’est pas facturée est soit lui affecter le plan _libre_ ou à supprimer de la collection de la tâche.

Bien que vous pouvez désactiver toutes les tâches au sein d’une collection de travail en une seule opération, elle ne modifie pas le statut de la facturation de la collection de travail – la collection de travail sera _toujours_ facturé. De même, les collections de projet vides sont considérées comme actives et seront facturées.

## <a name="pricing"></a>Tarification

Pour connaître les prix, consultez [Tarification du planificateur](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Voir aussi


 [Quel est le planificateur ?](scheduler-intro.md)

 [Hiérarchie d’entités, terminologie et des concepts du planificateur Azure](scheduler-concepts-terms.md)

 [Mise en route à l’aide du planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Référence des API de reste de planificateur Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence d’applets de commande PowerShell de planificateur Azure](scheduler-powershell-reference.md)

 [Azure planificateur haute disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et les codes d’erreur](scheduler-limits-defaults-errors.md)

 [Azure d’authentification sortante du planificateur](scheduler-outbound-authentication.md)
