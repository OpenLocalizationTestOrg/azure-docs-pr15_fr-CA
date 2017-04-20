<properties
   pageTitle="Configurer la méthode de routage de trafic de basculement de Traffic Manager | Microsoft Azure"
   description="Cet article vous permettra de configurer la méthode de routage du trafic basculement dans le Gestionnaire de trafic"
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

# <a name="configure-failover-routing-method"></a>Configurer la méthode d’acheminement de basculement

Souvent, une organisation souhaite fournir la fiabilité de ses services. Il effectue cette opération en fournissant des services de sauvegarde au cas où leur service principal tombe en panne. Un modèle commun pour le basculement du service consiste à fournir un ensemble de services identiques et à envoyer le trafic à un service principal, tout en conservant une liste configurée d’une ou plusieurs des services de sauvegarde. Vous pouvez configurer ce type de sauvegarde avec les services cloud Azure et les sites Web en suivant les procédures ci-dessous.

Notez que des sites Web d’Azure fournit déjà le trafic de basculement gamme de fonctionnalités de méthode pour les sites Web au sein d’un centre de données (également appelé une région), quel que soit le mode site Web. Traffic Manager vous permet de spécifier la méthode de routage du trafic de basculement pour les sites Web dans différents centres de données.

## <a name="to-configure-failover-traffic-routing-method"></a>Pour configurer la méthode de routage de trafic de basculement :

1. Dans le portail Azure classique, dans le volet gauche, cliquez sur l’icône du **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic. Si vous n’avez pas encore créé votre profil de Traffic Manager, reportez-vous à la section [Gérer les profils de Traffic Manager](traffic-manager-manage-profiles.md) pour obtenir la procédure pour créer un profil de Traffic Manager de base.
2. Dans le volet Gestionnaire de trafic dans le portail classique Azure, recherchez le profil de Traffic Manager qui contient les paramètres que vous souhaitez modifier et puis cliquez sur la flèche à droite du nom du profil. Ceci ouvrira la page de paramètres pour le profil.
3. Sur votre page de profil, cliquez sur les **points de terminaison** en haut de la page et vérifiez que les deux services en nuage et les sites Web (extrémités) que vous souhaitez inclure dans votre configuration sont présents. Pour ajouter ou supprimer des points de terminaison, voir [Gérer les points de terminaison de trafic dans le Gestionnaire de périphériques](traffic-manager-endpoints.md).
4. Sur votre page de profil, cliquez sur **configurer** pour ouvrir la page de configuration en haut.
5. Pour les **paramètres de méthode de routage de trafic**, vérifiez que la méthode de routage de trafic est le **basculement**. S’il n’est pas le cas, cliquez sur **reprise** dans la liste déroulante.
6. Pour la **Liste de priorité de basculement sur incident**, ajustez l’ordre de basculement pour vos points de terminaison. Lorsque vous sélectionnez la méthode de routage de trafic de **basculement** , l’ordre des points de terminaison sélectionnés est important. Le point de terminaison principal se trouve au-dessus. Utilisez les flèches et bas pour modifier l’ordre en fonction des besoins. Pour plus d’informations sur la façon de définir la priorité de basculement à l’aide de Windows PowerShell, voir [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Vérifiez que les **Paramètres de contrôle** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés du trafic. Afin de contrôler les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Notez qu’une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et implique que le fichier est dans le répertoire racine (par défaut). Pour plus d’informations sur l’analyse, consultez [Analyse de Traffic Manager](traffic-manager-monitoring.md).
8. Après avoir terminé vos modifications de configuration, cliquez sur **Enregistrer** en bas de la page.
9. Tester les modifications de votre configuration. Pour plus d’informations, reportez-vous à la section [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md) .
10. Une fois votre profil de Traffic Manager est le programme d’installation et de travail, modifier l’enregistrement DNS sur le serveur DNS faisant autorité pour le nom de domaine de votre entreprise au nom de domaine Traffic Manager. Pour plus d’informations sur la procédure à suivre, voir [Point un domaine Internet de l’entreprise à un domaine de Traffic Manager](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Étapes suivantes

[Pointez un domaine Internet de l’entreprise à un domaine de Traffic Manager](traffic-manager-point-internet-domain.md)

[Méthodes de routage du Gestionnaire de trafic](traffic-manager-routing-methods.md)

[Configurer la méthode de routage alternée](traffic-manager-configure-round-robin-routing-method.md)

[Configurer la méthode de routage de performances](traffic-manager-configure-performance-routing-method.md)

[Résolution des problèmes de Traffic Manager dégradées état](traffic-manager-troubleshooting-degraded.md)

[Trafic Manager - désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager - désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

