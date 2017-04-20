<properties
    pageTitle="Corriger 502 Passerelle incorrecte, 503 service indisponibles erreurs | Microsoft Azure"
    description="Résoudre les problèmes de 502 Passerelle incorrecte et 503 service non disponible dans votre application web hébergé dans le Service d’application Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="502 Passerelle incorrecte, 503 service non disponible, erreur 503, erreur 502"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Dépannage des erreurs HTTP de « 502 Passerelle incorrecte » et « 503 service non disponible » dans vos applications web Azure

« 502 Passerelle incorrecte » et « 503 service non disponible » sont les erreurs courantes dans votre application web hébergé dans le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Cet article vous aide à résoudre ces erreurs.

Si vous avez besoin d’obtenir de l’aide à tout moment dans cet article, vous pouvez contacter les experts Azure sur [l’Azure MSDN et les forums de débordement de pile](https://azure.microsoft.com/support/forums/). Sinon, vous pouvez également des fichiers un incident de support Azure. Accédez au [site de Support d’Azure](https://azure.microsoft.com/support/options/) et cliquez sur **Obtenir la prise en charge**.

## <a name="symptom"></a>Symptôme

Lorsque vous accédez à l’application web, il renvoie un HTTP Erreur de « 502 Passerelle incorrecte » ou un HTTP « 503 Service non disponible » erreur.

## <a name="cause"></a>Cause

Ce problème étant fréquemment causé par des problèmes au niveau des applications, telles que :

-   requêtes prenant beaucoup de temps
-   application à l’aide de la mémoire/CPU élevé
-   applications qui se bloquent suite à une exception.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Étapes de dépannage pour résoudre les erreurs « 503 service non disponible » et « 502 Passerelle incorrecte »

Résolution des problèmes peuvent être divisé en trois tâches distinctes, dans un ordre séquentiel :

1.  [Observer et contrôler le comportement de l’application](#observe)
2.  [Collecte de données](#collect)
3.  [Atténuer le problème.](#mitigate)

[Application Service Web Apps](/services/app-service/web/) vous offre différentes options à chaque étape.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. observer et contrôler le comportement de l’application

####    <a name="track-service-health"></a>Effectuer le suivi du fonctionnement du Service

Microsoft Azure publicizes chaque fois qu’il y a une dégradation de performances ou d’interruption de service. Vous pouvez suivre l’état de santé du service sur le [Portail Azure](https://portal.azure.com/). Pour plus d’informations, consultez [état de service de suivi](../monitoring-and-diagnostics/insights-service-health.md).

####    <a name="monitor-your-web-app"></a>Analyser votre application web

Cette option vous permet de savoir si votre application rencontre des problèmes. Dans les lames de votre application web, cliquez sur la mosaïque de **demandes et les erreurs** . La lame de **mesure** vous montrera toutes les mesures que vous pouvez ajouter.

Parmi les mesures que vous souhaitez surveiller pour votre application web

-   Jeu de travail de mémoire moyenne
-   Temps de réponse moyen
-   Temps processeur
-   Jeu de travail de mémoire
-   Demandes

![surveiller l’application web vers la résolution des erreurs HTTP 502 Passerelle incorrecte et 503 service non disponible](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Pour plus d’informations, voir :

-   [Moniteur Web Apps dans le Service d’application Azure](web-sites-monitor.md)
-   [Recevoir des notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />
### <a name="2-collect-data"></a>2. collecte de données

####    <a name="use-the-azure-app-service-support-portal"></a>Utiliser le portail de prise en charge du Service d’application Azure

Web applications vous offre la possibilité de résoudre les problèmes liés à votre application web en regardant HTTP journaux, journaux des événements, les vidages de processus et bien plus encore. Vous pouvez accéder à toutes ces informations à l’aide de notre portail de Support **http://&lt;nom de votre application >.scm.azurewebsites.net/Support**

Le portail du Service de l’application Azure prend en charge vous offre trois onglets distincts pour les trois étapes d’un scénario de résolution des problèmes courant de prise en charge :

1.  Observez le comportement actuel
2.  Analyser par la collecte des informations de diagnostics et l’exécution des analyseurs intégrés
3.  Réduire

Si le problème se produit dès maintenant, cliquez sur **analyser** > **Diagnostics** > **Diagnostiquer maintenant** pour créer une session de diagnostic, qui recueillera HTTP se connecte, journaux de l’Observateur d’événements, mémoire état processus de dumps, les journaux d’erreurs PHP et PHP.

Une fois les données collectées, il sera également exécuter une analyse sur les données et vous fournit un rapport HTML.

Au cas où vous souhaitez télécharger les données, par défaut, il doit être stocké dans le dossier D:\home\data\DaaS.

Pour plus d’informations sur le portail de Support de Service des applications Azure, consultez les [Nouvelles mises à jour pour Extension de Site de prise en charge pour les sites Web d’Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

####    <a name="use-the-kudu-debug-console"></a>Utilisez la Console de débogage Kudu

Web Apps est fourni avec une console de débogage que vous pouvez utiliser pour le débogage, exploration, le téléchargement de fichiers, ainsi que les points de terminaison JSON pour obtenir des informations sur votre environnement. Il s’agit de la _Console de le Kudu_ ou le _Tableau de bord de SCM_ pour votre application web.

Vous pouvez accéder à ce tableau de bord en cliquant sur le lien **https://&lt;nom de votre application >.scm.azurewebsites.net/**.

Certaines des choses que fournit des Kudu sont les suivantes :

-   paramètres d’environnement pour votre application
-   flux de journal
-   dump de diagnostic
-   déboguer la console dans laquelle vous pouvez exécuter les applets de commande Powershell et les commandes DOS de base.


Une autre fonctionnalité utile de Kudu est que, dans le cas où votre application est levée d’exceptions de première chance, vous pouvez utiliser Kudu et fait un dump de l’outil SysInternals Procdump pour la mémoire. Ces images de mémoire sont des instantanés du processus et peuvent souvent vous aider à résoudre les problèmes plus complexes avec votre application web.

Pour plus d’informations sur les fonctionnalités disponibles dans Kudu, voir les [outils en ligne de sites Web d’Azure que vous devez savoir sur le](/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />
### <a name="3-mitigate-the-issue"></a>3. réduire le problème

####    <a name="scale-the-web-app"></a>Échelle de l’application web

Dans le Service d’application Azure, pour améliorer les performances et le débit, vous pouvez ajuster l’échelle à partir duquel vous exécutez votre application. Mise à l’échelle d’une application web implique deux actions connexes : la modification de votre plan de Service de l’application à un niveau plus élevé de la tarification et la configuration de certains paramètres après être passé à la couche supérieure de tarification.

Pour plus d’informations sur la mise à l’échelle, consultez [l’échelle d’une application web dans le Service d’application Azure](web-sites-scale.md).

En outre, vous pouvez choisir d’exécuter votre application sur plus d’une instance. Non seulement vous offre une capacité de traitement plus, ce service vous donne également un montant de tolérance de pannes. Si le processus tombe en panne sur une instance, l’autre instance continue servir les requêtes.

Vous pouvez définir la mise à l’échelle pour être manuelle ou automatique.

####    <a name="use-autoheal"></a>Utiliser AutoHeal

AutoHeal recycle le processus de traitement pour votre application en fonction des paramètres que vous choisissez (comme les modifications de configuration, demandes, limites de mémoire ou le temps nécessaire à l’exécution d’une requête). La plupart du temps, le processus de recyclage est le moyen le plus rapide pour résoudre un problème. Même si vous pouvez toujours redémarrer l’application web de directement dans le portail d’Azure, AutoHeal fera automatiquement pour vous. Il vous souhaitez d’ajouter des certains déclencheurs de web.config racine pour votre application web. Notez que ces paramètres seraient fonctionne de la même manière, même si votre application n’est pas un .net une.

Pour plus d’informations, consultez [des Sites Web Azure autoréparation](/blog/auto-healing-windows-azure-web-sites/).


####    <a name="restart-the-web-app"></a>Redémarrez l’application web

Il s’agit souvent de la manière la plus simple pour récupérer des problèmes occasionnels. Sur le [Portail Azure](https://portal.azure.com/), sur les lames de votre application web, vous disposez des options pour arrêter ou redémarrer votre application.

 ![Redémarrez l’application pour résoudre les erreurs HTTP 502 Passerelle incorrecte et 503 service non disponible](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Vous pouvez également gérer votre application web à l’aide de Powershell d’Azure. Pour plus d’informations, reportez-vous [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).
