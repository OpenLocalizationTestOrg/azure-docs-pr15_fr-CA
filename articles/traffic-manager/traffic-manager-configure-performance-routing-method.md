<properties
   pageTitle="Configurer la méthode de routage de trafic de performances | Microsoft Azure"
   description="Cet article vous aidera à configurer la méthode de routage du trafic performances dans le Gestionnaire de trafic"
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

# <a name="configure-performance-traffic-routing-method"></a>Configurer la méthode de routage de trafic de performances

Pour acheminer le trafic pour les services en nuage et les sites Web (points de terminaison) qui sont trouvent dans des centres de données différents dans le monde entier (également connu sous le nom des régions), vous pouvez diriger le trafic entrant au point de terminaison avec la latence la plus faible à partir du client demandeur. En règle générale, le centre de données avec la latence la plus faible correspond à la plus proche dans la distance géographique. La méthode de routage de trafic de performances vous permettra de distribuer en fonction de la latence la plus faible, mais il ne peut pas prendre en compte les changements en temps réel dans la configuration du réseau ou de chargement. Pour plus d’informations sur les méthodes de routage de trafic différents Azure Traffic Manager fournit, consultez [méthodes le routage du trafic de sur le Gestionnaire de trafic](traffic-manager-routing-methods.md).

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>Acheminement du trafic basé sur une latence plus faible sur un ensemble de points de terminaison :

1. Dans le portail Azure classique, dans le volet gauche, cliquez sur l’icône du **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic. Si vous n’avez pas encore créé votre profil de Traffic Manager, reportez-vous à la section [Gérer les profils de Traffic Manager](traffic-manager-manage-profiles.md) pour connaître les étapes pour créer un profil de Traffic Manager de base.
2. Dans le portail Azure classique, dans le volet Gestionnaire de trafic, recherchez le profil de Traffic Manager qui contient les paramètres que vous souhaitez modifier et puis cliquez sur la flèche à droite du nom du profil. Ceci ouvrira la page de paramètres pour le profil.
3. Sur la page de votre profil, cliquez sur les **points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents. Pour ajouter ou supprimer des points de terminaison de votre profil, voir [Gérer les points de terminaison de trafic dans le Gestionnaire de périphériques](traffic-manager-endpoints.md).
4. Sur la page de votre profil, cliquez sur **configurer** pour ouvrir la page de configuration en haut.
5. Pour les **paramètres de méthode de routage de trafic**, vérifiez que la méthode de routage de trafic est * *performances*. S’il n’est pas le cas, cliquez sur * *performances** dans la liste déroulante.
6. Vérifiez que les **Paramètres de contrôle** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés du trafic. Afin de contrôler les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Notez qu’une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et implique que le fichier est dans le répertoire racine (par défaut). Pour plus d’informations sur la surveillance, consultez [Sur l’analyse de Traffic Manager](traffic-manager-monitoring.md).
7. Après avoir terminé vos modifications de configuration, cliquez sur **Enregistrer** en bas de la page.
8. Tester les modifications de votre configuration. Pour plus d’informations, consultez [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md).
9. Une fois votre profil de Traffic Manager est le programme d’installation et de travail, modifier l’enregistrement DNS sur le serveur DNS faisant autorité pour le nom de domaine de votre entreprise au nom de domaine Traffic Manager. Pour plus d’informations sur la procédure à suivre, voir [Point un domaine Internet de l’entreprise à un domaine de Traffic Manager](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Étapes suivantes


[Pointez un domaine Internet de l’entreprise à un domaine de Traffic Manager](traffic-manager-point-internet-domain.md)

[Méthodes de routage du Gestionnaire de trafic](traffic-manager-routing-methods.md)

[Configurer la méthode d’acheminement de basculement](traffic-manager-configure-failover-routing-method.md)

[Configurer la méthode de routage alternée](traffic-manager-configure-round-robin-routing-method.md)

[Résolution des problèmes de Traffic Manager dégradées état](traffic-manager-troubleshooting-degraded.md)

[Trafic Manager - désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager - désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

