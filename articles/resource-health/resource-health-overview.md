<properties
   pageTitle="Vue d’ensemble de santé ressource Azure | Microsoft Azure"
   description="Vue d’ensemble de la santé des ressources d’Azure"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# <a name="azure-resource-health-overview"></a>Vue d’ensemble de santé ressource Azure

Santé des ressources Azure est un service qui expose l’état de santé de chaque ressource Azure et fournit des instructions pour résoudre les problèmes. Dans un environnement de cloud où il n’est pas possible d’accéder directement à des serveurs ou des éléments de l’infrastructure, l’objectif de la santé des ressources est de réduire le temps aux clients sur la résolution des problèmes, en particulier en réduisant le temps de déterminer si la cause du problème se fixe à l’intérieur de l’application, ou si elle est provoquée par un événement à l’intérieur de la plateforme Azure.

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>Ce qui est considéré comme une ressource et la santé des ressources en quoi décide si la ressource est saine ou non ? 
Une ressource est une instance d’un type de ressource fourni par un service, par exemple utilisateur créé : une machine virtuelle, une application Web ou une base de données SQL. 

Santé de la ressource s’appuie sur les signaux émis par le service et/ou de la ressource pour déterminer si une ressource est saine ou non. Il est important de noter qu’actuellement santé seuls comptes de ressources pour la santé d’une ressource spécifique tapez et ne tient pas compte des autres éléments qui peuvent contribuer à l’état de santé global. Par exemple, lors de la déclaration de l’état d’un ordinateur virtuel, seule la partie du calcul de l’infrastructure est considérée comme, c'est-à-dire les problèmes du réseau n’apparaît pas dans l’état de la ressource, sauf s’il existe une panne de service déclarés, dans ce cas, il sera soient facilement identifiables par l’intermédiaire de la bannière en haut de la lame. Plus d’informations sur l’interruption de service sont proposées plus loin dans cet article. 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>Comment diffère de santé des ressources à partir du tableau de bord de santé Service ?

Les informations fournies par la santé des ressources sont plus précises que celui fourni par le tableau de bord de santé Service. SHD communique des événements ayant un impact sur la disponibilité d’un service dans une région, santé des ressources expose des informations relatives à une ressource spécifique, par exemple, il va exposer des événements ayant un impact sur la disponibilité d’une machine virtuelle, une application web ou une base de données SQL. Par exemple, si un nœud redémarre de façon inattendue, les clients dont les ordinateurs virtuels étaient exécutées sur ce nœud sera en mesure d’obtenir la raison pourquoi leur machine virtuelle n’est pas disponible pour une période donnée.   

## <a name="how-to-access-resource-health"></a>L’accès de santé des ressources
Les services disponibles par le biais de santé de la ressource, il existe 2 façons à la santé des ressources.

### <a name="azure-portal"></a>Azure Portal
La lame de santé des ressources dans le portail d’Azure, fournit des informations détaillées sur l’état de santé de la ressource comme recommandé des actions qui dépendent de l’état actuel de la ressource. Cette blade fournit la meilleure expérience possible lors de l’interrogation de santé de la ressource, comme il facilite l’accès à d’autres ressources à l’intérieur du portail. Comme mentionné précédemment, l’ensemble des actions recommandées dans la lame de santé ressource varie en fonction de l’état actuel :

* Ressources en bon état : dans la mesure où aucun problème qui pourrait avoir un impact sur la santé de la ressource n’a été détecté, les actions visent à aider le processus de dépannage. Par exemple, il fournit un accès direct à la lame de résolution des problèmes, ce qui offre des conseils sur la façon de résoudre les principaux problèmes clients sont confrontés.
* Ressource non intègre : pour les problèmes causés par Azure, la lame affichera des actions Microsoft est en cours (ou a fait) pour récupérer la ressource. Pour les problèmes causés par l’utilisateur actions lancées, la volonté de lame une liste de clients d’actions peut prendre pour résoudre le problème et récupérer la ressource.  

Une fois que vous êtes connecté au portail d’Azure, il existe deux façons d’accéder à la lame de la santé des ressources : 

###<a name="open-the-resource-blade"></a>Ouvrez la lame de ressource
Ouvrez la lame de ressource pour une ressource donnée. Sur la lame de paramètres s’ouvre en regard de la lame de ressource, cliquez sur la santé des ressources pour ouvrir la lame de la santé des ressources. 

![Lame de santé de ressource](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>Aide et lame de prise en charge
Ouvrez la blade d’aide et de Support en cliquant sur le point d’interrogation dans le coin supérieur droit puis en sélectionnant aide + prise en charge. 

**Dans la barre de navigation supérieure**

![Aide + prise en charge](./media/resource-health-overview/HelpAndSupport.png)

Cliquez sur la mosaïque pour ouvrir la blade d’abonnement de santé ressource qui répertorie toutes les ressources de votre abonnement. En regard de chaque ressource, il existe une icône indiquant son état de santé. En cliquant sur chaque ressource, la lame de la santé des ressources s’ouvre.

**Mosaïque d’intégrité de ressource**

![Mosaïque d’intégrité de ressource](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>Que signifie mon état de santé des ressources ?
Il existe 4 statuts sanitaires différents que vous pouvez consulter pour votre ressource.

### <a name="available"></a>Disponible
Le service n’a pas détecté de problème dans la plate-forme qui peut être un impact sur la disponibilité de la ressource. Cela est indiqué par une icône de coche verte. 

![Ressource n’est disponible](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Non disponible

Dans ce cas le service a détecté un problème en cours dans la plate-forme qui affecte la disponibilité de cette ressource, par exemple, le nœud exécutant de la machine virtuelle a été redémarré de manière inattendue. Cela est indiqué par une icône d’avertissement rouge. Des informations supplémentaires sur le problème sont fournies dans la section du milieu de la lame, y compris : 

1.  Les actions Microsoft est en train de récupérer la ressource 
2.  Une chronologie détaillée du problème, y compris le temps de résolution prévue
3.  Une liste des actions utilisateurs recommandée 

![Ressource n’est pas disponible](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>Non disponible – client initié
La ressource est indisponible en raison d’une demande de client comme l’arrêt d’une ressource ou demande de redémarrage de l’ordinateur. Cela est indiqué par une icône d’informations bleue. 

![Ressource n’est pas disponible en raison de l’utilisateur une action initiée](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Inconnu
Le service n’a pas reçu d’informations sur cette ressource pendant plus de 5 minutes. Cela est indiqué par une icône de point d’interrogation gris. 

Il est important de noter qu’il ne s’agit pas d’une indication définitive qu’il existe un problème au niveau d’une ressource, afin que les clients doivent suivre ces recommandations :

* Si la ressource s’exécute comme prévu mais son état est inconnu dans la santé de la ressource, il n’y a aucun problème, et vous pouvez attendre de l’état de la ressource à mettre à jour à Sain après quelques minutes.
* S’il existe des problèmes d’accès à la ressource et son état sont inconnu dans la santé de la ressource, cela peut être une indication précoce, il peut y avoir un problème et à des investigations supplémentaires convient jusqu'à ce que l’état de santé est mis à jour à Sain ou non

![Santé de la ressource est inconnue](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>Impact sur les événements du service
Si les ressources peuvent être affectés par un événement ayant un impact sur Service, une bannière s’affichera en haut de la lame de la santé des ressources. En cliquant sur la bannière ouvrira la blade d’événements d’Audit, qui affiche des informations supplémentaires sur la panne.

![Santé des ressources peut-être être affectée par un SIE](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>Que dois-je savoir sur la santé des ressources ?

### <a name="signal-latency"></a>Latence de signal
Les signaux de santé de la ressource d’alimentation, peut-être jusqu'à 15 min retardée, qui peuvent entraîner des écarts entre l’état actuel de la ressource et de sa disponibilité réelle. Il est important de garder cela à l’esprit qu’il vous aide à éliminer les problèmes possibles de temps consacré inutilement chargées. 

### <a name="special-case-for-sql"></a>Cas spécial pour SQL 
Santé des ressources indique l’état de la base de données SQL, et non sur le serveur SQL. Passer cet itinéraire fournit une image plus réaliste de la santé, elle requiert que plusieurs composants et services de prendre en considération pour déterminer l’état de la base de données. Le signal en cours s’appuie sur des connexions d’accès à la base de données, ce qui signifie que pour les bases de données recevant des connexions régulières (ce qui inclut, entre autres, recevoir des demandes d’exécution de requête) de l’état de santé état régulièrement affichera. Si la base de données n’ont pas été utilisée pendant une période de 10 minutes ou plus, il est déplacé vers l’état inconnu. Cela ne signifie pas que la base de données est indisponible, simplement qu’aucun signal n’a été émis, car aucune connexion n’ont été effectuées. Connexion à la base de données et exécuter une requête émet les signaux nécessaires pour déterminer et mettre à jour de l’état de la base de données.

## <a name="feedback"></a>Commentaires
Nous sommes toujours ouverts aux commentaires et suggestions ! Veuillez nous envoyer vos [suggestions](https://feedback.azure.com/forums/266794-support-feedback). En outre, vous pouvez engager avec nous via [Twitter](https://twitter.com/azuresupport) ou les [forums MSDN](https://social.msdn.microsoft.com/Forums/azure).
