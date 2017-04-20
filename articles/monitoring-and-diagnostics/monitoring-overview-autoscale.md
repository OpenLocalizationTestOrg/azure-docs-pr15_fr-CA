<properties
    pageTitle="Vue d’ensemble de l’échelle automatique dans les ordinateurs virtuels de Microsoft Azure, les Services en nuage et les applications Web | Microsoft Azure"
    description="Vue d’ensemble de l’échelle automatique dans Microsoft Azure. S’applique aux ordinateurs virtuels, les Services en nuage et les applications Web."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="robb"/>

# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Vue d’ensemble de l’échelle automatique dans les ordinateurs virtuels de Microsoft Azure, les Services en nuage et les applications Web

Cet article décrit l’autoscale Microsoft Azure, ses avantages et comment l’utiliser.  

Azure moniteur autoscale s’applique uniquement aux [Jeux d’échelle de Machine virtuelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/), les [Services de Cloud](https://azure.microsoft.com/services/cloud-services/)et [App Service - applications Web](https://azure.microsoft.com/services/app-service/web/).

>[AZURE.NOTE] Azure dispose de deux méthodes d’échelle automatique. Une version plus ancienne de l’échelle s’applique des machines virtuelles (jeux de disponibilité). Prise en charge de cette fonctionnalité est limitée et il est recommandé de migrer vers VM échelle jeux pour le support d’échelle plus rapide et plus fiable. Un lien sur la façon d’utiliser la technologie plus ancienne est inclus dans cet article.  


## <a name="what-is-autoscale"></a>Nouveautés d’échelle automatique ?

Échelle automatique vous permet de disposer de la bonne quantité de ressources en cours d’exécution pour gérer la charge sur votre application. Il vous permet d’ajouter des ressources pour gérer l’augmentation de la charge et également économiser de l’argent en supprimant les ressources qui sont assis inactif. Vous spécifiez un nombre minimum et maximum d’instances à exécuter et d’ajouter ou de supprimer des ordinateurs virtuels automatiquement en fonction d’un ensemble de règles. Ayant un minimum fait que votre application est toujours en cours d’exécution même sous aucune charge. Avoir un maximum de limite votre coût possible hourly. Vous automatiquement mettre à l’échelle entre ces deux extrêmes, à l’aide des règles que vous créez.

 ![Échelle automatique expliqué. Ajouter et supprimer des ordinateurs virtuels](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

Lorsque les conditions de la règle sont remplies, une ou plusieurs actions d’échelle automatique est déclenchée. Vous pouvez ajouter et supprimer des ordinateurs virtuels ou exécuter d’autres actions. Le schéma conceptuel suivant illustre ce processus.  

 ![Diagramme conceptuel d’échelle](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)


## <a name="autoscale-process-explained"></a>Processus d’échelle expliqué
L’explication suivante s’appliquent aux éléments du diagramme précédent.   

### <a name="resource-metrics"></a>Mesures de ressources
Ressources émettent des mesures, qui sont ensuite traités par les règles. Les mesures sont fournies via différentes méthodes.
Jeux d’échelle de machine virtuelle utilise les données de télémétrie d’agents de diagnostics de Windows Azure alors que la télémesure pour les applications Web et des services en nuage provient directement de l’Infrastructure d’Azure. Certaines statistiques couramment utilisées incluent l’utilisation du processeur, utilisation de la mémoire, le nombre de threads, longueur de la file d’attente et l’utilisation du disque. Pour obtenir la liste des données télémétriques, vous pouvez utiliser, voir [Métriques de l’échelle commune](insights-autoscale-common-metrics.md).

### <a name="time"></a>Heure
Règles basées sur les prévisions sont basées sur l’heure UTC. Vous devez définir votre fuseau horaire correctement lorsque vous configurez vos règles.  

### <a name="rules"></a>Règles
Le diagramme ne montre qu’une seule règle d’échelle automatique, mais vous pouvez avoir beaucoup. Vous pouvez créer des règles complexes qui se chevauchent en fonction des besoins de votre situation.  Incluent les types de règles  

 - **Base de métrique** - par exemple, effectuer cette action lors de l’utilisation du processeur est supérieure à 50 %.
 - **En temps** - par exemple, déclencheur un webhook chaque 8 heures le samedi dans un fuseau horaire donné.

Règles de mesure de mesurent la charge de l’application et ajouter ou supprimer des ordinateurs virtuels en fonction de cette charge. Règles basées sur les prévisions permettent de mettre à l’échelle lorsque vous voir modèles d’heure dans votre charge et que vous souhaitez mettre à l’échelle avant une augmentation de charge possibles ou négatif se produit.  


### <a name="actions-and-automation"></a>Actions et l’automatisation

Les règles peuvent déclencher un ou plusieurs types d’actions.

- **Échelle** : échelle VMs ou arrière
- **E-mail** - envoyer un e-mail aux administrateurs d’abonnement, co-administrateurs et/ou adresse e-mail supplémentaire que vous spécifiez
- **Automatisation via webhooks** - webhooks d’appel, qui peut déclencher plusieurs actions complexes à l’intérieur ou en dehors d’Azure. À l’intérieur d’Azure, vous pouvez démarrer une procédure opérationnelle Azure Automation, fonction d’Azure ou Azure logique d’application. Exemple de 3e partie URL, en dehors d’Azure comprennent des services tels que la marge et Twilio.


## <a name="autoscale-settings"></a>Paramètres d’échelle automatique
Échelle automatique utilisent la terminologie et la structure suivante.

- Un **paramètre d’échelle** est lu par le moteur de l’échelle automatique afin de déterminer s’il faut mettre à l’échelle vers le haut ou vers le bas. Il contient un ou plusieurs profils, des informations sur la ressource cible et les paramètres de notification.
    - Un **profil d’échelle automatique** est une combinaison d’une capacité de définition, un ensemble de règles régissant les déclencheurs et les actions d’échelle pour le profil et une récurrence. Vous pouvez avoir plusieurs profils, qui vous permettent de prendre en charge différentes exigences qui se chevauchent.
        - Un **paramètre de capacité** indique le minimum, maximum et les valeurs par défaut pour le nombre d’instances. [emplacement approprié à utiliser Fig. 1]
        - Une **règle** comprend un déclencheur — un métrique déclencheur ou un déclencheur de temps et une action de l’échelle, indiquant si autoscale doit évoluer vers le haut ou vers le bas lorsque cette règle est satisfaite.
        - Une **périodicité** indique lorsque autoscale doit mettre ce profil en vigueur. Vous pouvez avoir autoscale différents profils pour différents moments de la journée ou les jours de la semaine, par exemple.
- Un **paramètre de notification** définit quelles notifications doivent se produire lorsqu’un événement d’échelle automatique se produit répondant aux critères de l’un des profils du paramètre échelle automatique en fonction. Échelle automatique peut notifier une ou plusieurs adresses de messagerie ou effectuer des appels vers une ou plusieurs des webhooks.

![Paramètre d’échelle d’Azure, le profil et structure de règle](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

La liste complète des descriptions et des champs configurables est disponible dans l' [API REST de Autoscale](https://msdn.microsoft.com/library/dn931928.aspx).

Pour des exemples de code, reportez-vous à la section.

* [Configuration d’échelle automatique avancée à l’aide des modèles du Gestionnaire de ressources pour les jeux d’échelle de machine virtuelle](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Échelle automatique reste API](https://msdn.microsoft.com/library/dn931953.aspx)



## <a name="horizontal-vs-vertical-scaling"></a>Mise à l’échelle verticale du vs horizontales

Échelle augmente les ressources de prise en charge allant horizontalement, qui est une augmentation (« out ») ou diminuer (« in ») dans le nombre d’instances de la machine virtuelle.  Évolution horizontale, qui est plus flexible dans une situation de nuage, car il vous permet d’exécuter potentiellement des milliers d’ordinateurs virtuels pour gérer la charge. Mise à l’échelle verticale est différent. Il conserve le même nombre d’ordinateurs virtuels, mais rend la machine virtuelle (« up ») ou inférieur (« down ») puissant. Puissance fournie est mesurée dans la mémoire, la vitesse du processeur, espace disque, etc..  Mise à l’échelle verticale a plus de limitations. Il est dépendant de la disponibilité de matériel plus puissant, qui peut varier selon les pays et atteint rapidement et limite supérieure. Mise à l’échelle verticale nécessite généralement un arrêt de la machine virtuelle et le démarrer. Pour plus d’informations, voir [évolution verticale Azure machine virtuelle avec l’Automation d’Azure](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


## <a name="methods-of-access"></a>Méthodes d’accès
Vous pouvez définir l’échelle via

- [Azure portal](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [Interface de ligne de commande (CLI) multiplates-formes](insights-cli-samples.md#autoscale )
- [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931953.aspx )

## <a name="supported-services-for-autoscale"></a>Services pris en charge pour l’échelle automatique


| Service                              | Schéma et documentation                                       |
|--------------------------------------|-----------------------------------------------------|
| Applications Web                             | [Mise à l’échelle des applications Web](insights-how-to-scale.md)              |
| Services en nuage                       | [Échelle d’un Service en nuage](../cloud-services/cloud-services-how-to-scale.md) |
| Machines virtuelles : classique           | [Mise à l’échelle des ensembles de disponibilité des machines virtuelles classique](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Les Machines virtuelles : Jeux d’échelle Windows| [Mise à l’échelle des VM échelle définit dans Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)  |
| Les Machines virtuelles : Définit de Linux échelle  | [Mise à l’échelle des VM échelle définit sous Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Les Machines virtuelles : Exemple de Windows   | [Configuration d’échelle automatique avancée à l’aide des modèles du Gestionnaire de ressources pour les jeux d’échelle de machine virtuelle](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’échelle automatique, utiliser les Autoscale Walkthroughs répertoriés précédemment ou consultez les ressources suivantes :

- [Mesures courantes Azure moniteur échelle automatique](insights-autoscale-common-metrics.md)
- [Méthodes conseillées pour Azure moniteur autoscale](insights-autoscale-best-practices.md)
- [Actions d’échelle automatique permet d’envoyer des notifications d’alerte e-mail et webhook](insights-autoscale-to-webhook-email.md)
- [Échelle automatique reste API](https://msdn.microsoft.com/library/dn931953.aspx)
- [Échelle automatique dépannage jeux échelle de Machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
