<properties
    pageTitle="Activer l’affinité de Session à l’aide de la Shared Computer Toolkit Azure pour Éclipse"
    description="Apprenez à activer l’affinité de session à l’aide de la Shared Computer Toolkit Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# <a name="enable-session-affinity"></a>Activer l’affinité de Session #

Au sein de la Shared Computer Toolkit Azure pour Eclipse, vous pouvez activer l’affinité de session HTTP, ou « sticky sessions », pour vos rôles. L’image suivante montre les propriétés **d’Équilibrage de la charge** que boîte de dialogue est utilisée pour activer la fonctionnalité de l’affinité de session :

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>À activer l’affinité de session pour votre rôle. ##

1. Cliquez sur le rôle dans l’Explorateur de projet du Eclipse et cliquez sur **Azure**, puis cliquez sur **L’équilibrage de charge**.
1. Dans la boîte de dialogue **Propriétés de l’équilibrage de la charge WorkerRole1** :
    1. Vérifiez **affinité de session activer HTTP (sessions persistantes) pour ce rôle.**
    1. De **point de terminaison d’entrée à utiliser**, sélectionnez un point de terminaison d’entrée à utiliser, par exemple, **http (public : 80 privé : 8080)**. Votre application doit utiliser ce point de terminaison en tant que son point de terminaison HTTP. Vous pouvez activer plusieurs points de terminaison pour votre rôle, mais vous pouvez en sélectionner qu’un seul d'entre eux pour prendre en charge les sessions persistantes.
    1. Régénérez votre application.

Une fois activé, si vous avez plus d’une instance de rôle, les demandes HTTP en provenance d’un client particulier continuera géré par la même instance de rôle.

Le Shared Computer Toolkit Eclipse permet cela en installant un module IIS spécial appelé Application demande routage (ARR) dans chacune de vos instances de rôles. ARR redirige les demandes HTTP vers l’instance de rôle approprié. La trousse à outils reconfigure automatiquement le point de terminaison sélectionné afin que le trafic HTTP entrant est tout d’abord routé vers le logiciel ARR. La boîte à outils crée également un nouveau point de terminaison interne que votre serveur Java est configuré pour écouter. Qui est le point de terminaison utilisé par ARR pour rediriger le trafic HTTP à l’instance de rôle approprié. De cette façon, chaque instance de rôle dans votre déploiement multi-instance sert de proxy inverse pour toutes les autres instances, l’activation de sessions persistantes.

## <a name="notes-about-session-affinity"></a>Remarques sur l’affinité de session ##

* Affinité de session ne fonctionne pas dans l’émulateur de calcul. Les paramètres peuvent être appliqués dans l’émulateur de calcul sans interférer avec votre processus de génération ou d’exécution d’émulateur de calcul, mais la fonction elle-même ne fonctionne pas dans l’émulateur de calcul.
* En activant l’affinité de session entraîne une augmentation de l’espace disque occupé par votre déploiement dans Azure, comme des logiciels supplémentaires seront téléchargés et installés dans vos instances de rôles lorsque votre service est démarré dans le nuage Azure.
* Le temps d’initialiser chaque rôle prendra plus de temps.
* Un point de terminaison interne, de fonctionner comme un rerouter le trafic comme indiqué ci-dessus, seront added.ss

Pour obtenir un exemple de la façon de mettre à jour les données de session lorsque l’affinité de session est activée, consultez [comment mettre à jour les données de Session avec l’affinité de la Session][].

## <a name="see-also"></a>Voir aussi ##

[Azure Shared Computer Toolkit pour Éclipse][]

[Création d’une Application du monde Hello pour Azure dans Éclipse][]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse][] 

[Comment faire pour mettre à jour les données de Session avec l’affinité de Session][]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Comment faire pour mettre à jour les données de Session avec l’affinité de Session]: http://go.microsoft.com/fwlink/?LinkID=699539
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png
