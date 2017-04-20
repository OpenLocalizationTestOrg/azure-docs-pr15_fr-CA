<properties
   pageTitle="Configurer le Gestionnaire de trafic round robin méthode de routage de trafic | Microsoft Azure"
   description="Cet article vous aidera à configurer la répétition alternée équilibrage de charge pour vos points de terminaison de Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-round-robin-routing-method"></a>Méthode de routage de configurer la répétition alternée

Un modèle commun méthode de routage de trafic consiste à fournir un ensemble de points de terminaison identiques, ce qui inclut les services en nuage et sites Web, et à acheminer le trafic vers chacun de façon alternée. Les étapes ci-dessous expliquent comment configurer le Gestionnaire de trafic pour effectuer ce type de méthode de routage du trafic. Pour plus d’informations sur les méthodes de routage de trafic différent, consultez [les méthodes de routage de trafic sur le Gestionnaire de trafic](traffic-manager-routing-methods.md).

>[AZURE.NOTE] Sites Web Azure fournit déjà des fonctionnalités pour les sites Web au sein d’un centre de données (également connu sous le nom d’une région) d’équilibrage de charge round robin. Traffic Manager vous permet de spécifier la méthode de routage de trafic de la répétition alternée pour les sites Web dans différents centres de données.

## <a name="routing-traffic-equally-round-robin-across-a-set-of-endpoints"></a>Routage du trafic également (round robin) sur un ensemble de points de terminaison :

1. Dans le portail Azure classique, dans le volet gauche, cliquez sur l’icône du **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic. Si vous n’avez pas encore créé votre profil de Traffic Manager, reportez-vous à la section [Gérer les profils de Traffic Manager](traffic-manager-manage-profiles.md) pour obtenir la procédure pour créer un profil de Traffic Manager de base.
2. Dans le portail Azure classique, dans le volet Gestionnaire de trafic, recherchez le profil de Traffic Manager qui contient les paramètres que vous souhaitez modifier et puis cliquez sur la flèche à droite du nom du profil. Ceci ouvrira la page de paramètres pour le profil.
3. Sur la page de votre profil, cliquez sur les **points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents. Pour ajouter ou supprimer des points de terminaison, voir [Gérer les points de terminaison de trafic dans le Gestionnaire de périphériques](traffic-manager-endpoints.md).
4. Sur votre page de profil, cliquez sur **configurer** pour ouvrir la page de configuration en haut.
5. Pour les **paramètres de la méthode de routage du trafic**, vérifiez que la méthode d’acheminement du trafic est **alternée**. S’il n’est pas le cas, cliquez sur **répétition alternée** , dans la liste déroulante.
6. Vérifiez que les **Paramètres de contrôle** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés du trafic. Afin de contrôler les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Notez qu’une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et implique que le fichier est dans le répertoire racine (par défaut). Pour plus d’informations sur la surveillance, consultez [Sur l’analyse de Traffic Manager](traffic-manager-monitoring.md).
7. Après avoir terminé vos modifications de configuration, cliquez sur **Enregistrer** en bas de la page.
8. Tester les modifications de votre configuration. Pour plus d’informations, consultez [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md).
9. Une fois votre profil de Traffic Manager est le programme d’installation et de travail, modifier l’enregistrement DNS sur le serveur DNS faisant autorité pour le nom de domaine de votre entreprise au nom de domaine Traffic Manager. Pour plus d’informations sur la procédure à suivre, voir [Point un domaine Internet de l’entreprise à un domaine de Traffic Manager](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Étapes suivantes


[Pointez un domaine Internet de l’entreprise à un domaine de Traffic Manager](traffic-manager-point-internet-domain.md)

[Méthodes de routage du Gestionnaire de trafic](traffic-manager-routing-methods.md)

[Configurer la méthode d’acheminement de basculement](traffic-manager-configure-failover-routing-method.md)

[Configurer la méthode de routage de performances](traffic-manager-configure-performance-routing-method.md)

[Résolution des problèmes de Traffic Manager dégradées état](traffic-manager-troubleshooting-degraded.md)

[Trafic Manager - désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager - désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

