<properties
    pageTitle="Contrôler le trafic d’application web Azure avec Azure Traffic Manager"
    description="Cet article fournit des informations de synthèse pour le Gestionnaire de trafic Azure par rapport à des applications web Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Contrôler le trafic d’application web Azure avec Azure Traffic Manager

> [AZURE.NOTE] Cet article fournit des informations de résumé pour le Gestionnaire de trafic Microsoft Azure par rapport à un Service Web Apps Azure App. Vous trouverez plus d’informations sur le Gestionnaire de trafic Azure lui-même en consultant les liens à la fin de cet article.

## <a name="introduction"></a>Introduction
Azure Traffic Manager vous permet de contrôler la répartition des demandes de clients web à des applications web dans le Service d’application Azure. Lorsque les points de terminaison web app sont ajoutés à un profil de gestionnaire de trafic d’Azure, Azure Traffic Manager conserve des informations l’état de vos applications web (en cours d’exécution, arrêté ou supprimé) afin qu’elle peut décider lequel de ces points de terminaison doit recevoir le trafic.

## <a name="load-balancing-methods"></a>Méthodes d’équilibrage de charge
Azure Traffic Manager utilise trois méthodes d’équilibrage de la charge différents. Elles sont décrites dans la liste suivante comme ils se rapportent à des applications web Azure.

* **Basculement sur incident**: Si vous avez des clones d’application web dans différentes régions, vous pouvez utiliser cette méthode pour configurer une application web pour tout le trafic de client web et configurer une application web dans une région différente pour gérer ce trafic en cas d’indisponibilité de la première application web.

* **Round Robin**: Si vous avez des clones d’application web dans différentes régions, vous pouvez utiliser cette méthode pour répartir le trafic de manière égale entre les applications web dans différentes régions.

* **Performances**: méthode de performances le distribue le trafic basé sur le temps d’aller-retour le plus court pour les clients. La méthode de performances peut être utilisée pour les applications web au sein de la même région ou dans différentes régions.

##<a name="web-apps-and-traffic-manager-profiles"></a>Les applications Web et des profils de trafic Manager
Pour configurer le contrôle du trafic d’application web, vous créez un profil dans Azure Traffic Manager utilise un des trois charge équilibrage des méthodes décrites précédemment et puis ajoutez les points de terminaison (dans ce cas, les applications web) pour lequel vous souhaitez contrôler le trafic dans le profil. État de votre application web (en cours d’exécution, arrêté ou supprimé) est régulièrement communiquée au profil afin que Azure Traffic Manager peut diriger le trafic en conséquence.

Lorsque vous utilisez le Gestionnaire de trafic Azure avec Azure, gardez à l’esprit les points suivants :

* Pour les déploiements de seule application web dans la même région, Web Apps fournit déjà le basculement et la fonctionnalité de répétition alternée quel que soit le mode d’application web.

* Pour les déploiements dans la même région qui utilisent des applications Web en conjonction avec un autre service de cloud Azure, vous pouvez combiner les deux types de points de terminaison pour activer les scénarios hybride.

* Vous ne pouvez spécifier qu’un seul point de terminaison d’application web par région dans un profil. Lorsque vous sélectionnez une application web sous la forme d’un point de terminaison d’une région, les applications web restants dans cette zone ne sont plus disponibles pour la sélection de ce profil.

* Les points de terminaison d’application web que vous spécifiez dans un profil d’Azure Traffic Manager seront affiche dans la section **Noms de domaine** sur la page de configuration de l’application web dans le profil, mais ne sera pas configurables il.

* Après avoir ajouté une application web à un profil, l' **URL du Site** du tableau de bord de page de l’application web du portail s’affiche l’URL de domaine personnalisé de l’application web, si vous avez défini un. Dans le cas contraire, il affiche l’URL de profil du Gestionnaire de trafic (par exemple, `contoso.trafficmgr.com`). Le nom de domaine directement de l’application web et l’URL du Gestionnaire de trafic sera visible sur la page de configuration de l’application web dans la section **Noms de domaine** .

* Les noms de votre domaine personnalisé ne fonctionnent pas comme prévu, mais en plus de les ajouter à vos applications web, vous devez également configurer votre plan de DNS pour pointer vers l’URL du Gestionnaire de trafic. Pour plus d’informations sur la façon de configurer un domaine personnalisé pour une application web d’Azure, consultez [configuration d’un nom de domaine personnalisé pour un site web Azure](web-sites-custom-domain-name.md).

* Vous ne pouvez ajouter que des applications web qui sont en mode standard à un profil de gestionnaire de trafic Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour une présentation conceptuelle et technique du Gestionnaire de trafic Azure, consultez [Vue d’ensemble du Gestionnaire de trafic](../traffic-manager/traffic-manager-overview.md).

Pour plus d’informations sur l’utilisation de Traffic Manager avec les applications Web, consultez que le blog valide [à l’aide d’Azure Traffic Manager avec les Sites Web Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) et [Azure Traffic Manager permet maintenant d’intégrer avec des Sites Web Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
