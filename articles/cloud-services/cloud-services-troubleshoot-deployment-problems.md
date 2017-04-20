<properties
 pageTitle="Résoudre les problèmes de déploiement de service nuage | Microsoft Azure"
 description="Il existe quelques problèmes courants que vous risquez de rencontrer lors du déploiement d’un service en nuage à Azure. Cet article fournit des solutions à certains d'entre eux."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-deployment-problems"></a>Résoudre les problèmes de déploiement de service cloud

Lorsque vous déployez un package d’application de service cloud sur Azure, vous pouvez obtenir des informations sur le déploiement dans le volet **Propriétés** dans Azure portal. Vous pouvez utiliser les détails dans ce volet pour vous aider à résoudre les problèmes avec le service en nuage, et vous pouvez fournir ces informations pour prendre en charge des Azure lors de l’ouverture d’une nouvelle demande de support.

Vous pouvez trouver le volet des **Propriétés** comme suit :

* Dans le portail Azure, cliquez sur le déploiement de votre service cloud, sur **tous les paramètres**, puis cliquez sur **Propriétés**.
* Dans le portail Azure classique, cliquez sur le déploiement de votre service cloud, **tableau de bord**, situé dans le coin inférieur droit de la page (sous le **coup de œil rapide**). Sachez qu’il n’y a aucune étiquette « Propriétés » sur ce volet.

> [AZURE.NOTE] Vous pouvez copier le contenu du volet **Propriétés** dans le Presse-papiers en cliquant sur l’icône dans le coin supérieur droit du volet.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problème : Impossible d’accéder à mon site Web, mais mon déploiement est démarré et toutes les instances de rôle sont prêts

Le lien URL de site Web indiqué dans le portail n’inclut pas le port. Le port par défaut pour les sites Web est 80. Si votre application est configurée pour s’exécuter sur un autre port, vous devez ajouter le numéro de port correct de l’URL lors de l’accès au site Web.

1. Dans le portail Azure, cliquez sur le déploiement de votre service cloud.
2. Dans le volet **Propriétés** du portail Azure, vérifiez les ports pour les instances de rôle (sous les **Points de terminaison d’entrée**).
3. Si le port n’est pas 80, ajoutez la valeur de port correct à l’URL lorsque vous accédez à l’application. Pour spécifier un port non défini par défaut, tapez l’URL, suivie de deux-points ( :), suivi du numéro de port, sans espaces.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problème : Mon rôle instances recyclés sans me rien faire

Rétablissement de service se produit automatiquement lorsque Azure détecte les nœuds problème et par conséquent déplace les instances de rôle de nouveaux nœuds. Lorsque cela se produit, vous pouvez voir vos instances de rôles recyclage automatique. Pour savoir si la cicatrisation de service s’est produite :

1. Dans le portail Azure, cliquez sur le déploiement de votre service cloud.
2. Dans le volet **Propriétés** du portail Azure, examinez les informations et déterminer si la cicatrisation de service s’est produite au moment où vous avez observé les rôles de recyclage.

Rôles également recycle environ une fois par mois pendant les mises à jour du système d’exploitation invité et de système d’exploitation hôte.  
Pour plus d’informations, consultez le blog [Rôle Instance redémarre en raison de mises à niveau du système d’exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problème : je n’arrive pas à effectuer une permutation VIP et recevez une erreur

Une permutation VIP n’est pas autorisée si une mise à jour du déploiement est en cours. Mises à jour de déploiement peuvent se produire automatiquement lorsque :

* Un nouveau système d’exploitation invité est disponible et vous sont configurées pour des mises à jour automatiques.
* Rétablissement de service se produit.

Pour savoir si une mise à jour automatique vous empêche d’effectuer une permutation VIP :

1. Dans le portail Azure, cliquez sur le déploiement de votre service cloud.
2. Dans le volet **Propriétés** du portail Azure, examinez la valeur de **l’état**. S’il est **prêt**, puis vérifiez **dernière opération** pour voir si un récemment s’est produit qui risque d’empêcher l’échange VIP.
3. Répétez les étapes 1 et 2 pour le déploiement en production.
4. Si une mise à jour automatique est en cours, attendez qu’elle se termine avant de tenter d’effectuer l’échange VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problème : Une instance de rôle en boucle entre démarré, initialisation, occupé (e) et arrêté

Cette condition peut indiquer un problème avec votre fichier de code, de lot ou de configuration d’application. Dans ce cas, vous devez être en mesure de voir l’état de la modification de quelques minutes, et le portail Azure peut dire quelque chose comme le **recyclage**, **occupé**ou **initialisation**. Cela indique qu’il existe une anomalie avec l’application qui empêche l’instance du rôle en cours d’exécution.

Pour plus d’informations sur la façon de résoudre ce problème, consultez le blog [PaaS Azure Compute des données de diagnostic](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) et [les problèmes courants qui provoquent des rôles à recycler](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problème : Mon application a cessé de fonctionner

1. Dans le portail Azure, cliquez sur l’instance de rôle.
2. Dans le volet **Propriétés** du portail Azure, considérez les conditions suivantes pour résoudre votre problème :
   * Si l’instance de rôle a été arrêté récemment (vous pouvez vérifier la valeur du **nombre d’abandons**), le déploiement peut être mise à jour. Patientez pour voir si l’instance de rôle reprend fonctionne sur sa propre.
   * Si l’instance de rôle est **occupé (e)**, vérifiez que votre code d’application pour voir si l’événement [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) est gérée. Vous devrez peut-être ajouter ou à rectifier du code qui gère cet événement.
   * Parcourez les données de diagnostic et les scénarios de résolution des problèmes dans le billet de blog [PaaS Azure Compute des données de diagnostic](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

>[AZURE.WARNING] Si vous recyclez votre service cloud, vous réinitialisez les propriétés pour le déploiement, l’effacement efficacement les informations pour le problème d’origine.

## <a name="next-steps"></a>Étapes suivantes

Permet d’afficher plusieurs [articles de dépannage](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) des services en nuage.

Pour savoir comment résoudre les problèmes de rôle de service cloud à l’aide de données de diagnostic d’ordinateur Azure PaaS, consultez [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
