<properties
    pageTitle="Gérer les points de terminaison dans le Gestionnaire de trafic Azure | Microsoft Azure"
    description="Cet article vous aidera à ajouter, de supprimer, d’activer et de désactiver les points de terminaison à partir du Gestionnaire de trafic Azure."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="add-disable-enable-or-delete-endpoints"></a>Ajouter, désactiver, activer ou supprimer des points de terminaison

La fonctionnalité d’applications Web dans le Service d’application Azure fournit déjà le basculement et la fonctionnalité de routage de trafic de la répétition alternée pour les sites Web au sein d’un centre de données, quel que soit le mode site Web. Azure Traffic Manager vous permet de spécifier le basculement et le routage du trafic de répétition alternée pour les services de cloud et de sites Web dans différents centres de données. La première étape nécessaire pour fournir ces fonctionnalités consiste à ajouter le point de terminaison service ou le site Web du nuage vers le Gestionnaire de trafic.

>[AZURE.NOTE]  Cet article explique comment utiliser le portail classique. Le portail classique Azure prend uniquement en charge la création et l’affectation de services en nuage et les applications Web en tant que points de terminaison. Le nouveau [portail Azure](https://portal.azure.com) est l’interface par défaut.

Vous pouvez également désactiver les points de terminaison individuels qui font partie d’un profil de gestionnaire de trafic. La désactivation d’un point de terminaison le laisse dans le cadre du profil, mais le profil se comporte comme si le point de terminaison n’est pas inclus dans celui-ci. Cette action est utile pour supprimer temporairement un point de terminaison est en mode maintenance ou en cours de redéploiement. Une fois que le point de terminaison est en cours d’exécution, une nouvelle fois, il peut être activé.

>[AZURE.NOTE] Désactivation d’un point de terminaison n’a rien à voir avec l’état de déploiement dans Azure. Un point de terminaison sain reste opérationnel et en mesure de recevoir le trafic même si désactivée dans le Gestionnaire de trafic. En outre, la désactivation d’un point de terminaison d’un profil n’affecte pas son état dans un autre profil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Pour ajouter un point de terminaison service ou le site Web du nuage

1. Dans le volet Gestionnaire de trafic dans le portail classique Azure, recherchez le profil de Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche à droite du nom du profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. En bas de la page, cliquez sur **Ajouter** pour accéder à la page **Ajouter des points de terminaison de Service** . Par défaut, la page répertorie les services nuage de **Points de terminaison de Service**.
4. Pour les services en nuage, sélectionnez les services en nuage dans la liste pour les ajouter en tant que points de terminaison pour ce profil. L’effacement du nom de service cloud supprime de la liste des points de terminaison.
5. Pour les sites Web, cliquez sur la liste déroulante **Type de Service** , puis sélectionnez **application Web**.
6. Sélectionnez les sites Web dans la liste pour les ajouter en tant que points de terminaison pour ce profil. L’effacement du nom de site Web supprime de la liste des points de terminaison. Vous pouvez sélectionner uniquement un site Web par le centre de données Azure (également connu sous le nom d’une région). Lorsque vous sélectionnez le premier site Web, les autres sites Web dans le même centre de données ne sont plus disponibles pour la sélection. Notez également que les sites Web Standard uniquement sont répertoriés.
7. Après avoir sélectionné les points de terminaison pour ce profil, cliquez sur la coche dans le coin inférieur droit pour enregistrer vos modifications.

>[AZURE.NOTE] Une fois que vous ajoutez ou supprimez un point de terminaison à partir d’un profil à l’aide de la méthode de routage de trafic de *basculement sur incident* , la liste de priorité de basculement ne peut pas être commandé qu’ils comme vous le souhaitez. Vous pouvez ajuster l’ordre de la liste de priorité de basculement sur la page de Configuration. Pour plus d’informations, consultez [basculement de configurer le routage du trafic](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Pour désactiver un point de terminaison

1. Dans le volet Gestionnaire de trafic dans le portail classique Azure, recherchez le profil de Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche à droite du nom du profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez désactiver, puis cliquez sur **désactiver** au bas de la page.
4. Les clients continuent à envoyer du trafic vers le point de terminaison pour la durée de Time-to-Live (TTL). Vous pouvez modifier la durée de vie de la page de Configuration du profil Traffic Manager.

## <a name="to-enable-an-endpoint"></a>Pour activer un point de terminaison

1. Dans le volet Gestionnaire de trafic dans le portail classique Azure, recherchez le profil de Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche à droite du nom du profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez activer, puis cliquez sur **Activer** en bas de la page.
4. Clients sont dirigés vers le point de terminaison activé comme défini par le profil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Pour supprimer un point de terminaison service ou le site Web du nuage

1. Dans le volet Gestionnaire de trafic dans le portail classique Azure, recherchez le profil de Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche à droite du nom du profil.
2. En haut de la page, cliquez sur les **points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. Dans la page points de terminaison, cliquez sur le nom du point de terminaison que vous souhaitez supprimer le profil.
4. En bas de la page, cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les profils de Traffic Manager](traffic-manager-manage-profiles.md)
* [Configurer des méthodes de routage](traffic-manager-configure-routing-method.md)
* [Résolution des problèmes de Traffic Manager dégradées état](traffic-manager-troubleshooting-degraded.md)
* [Considérations relatives aux performances de Traffic Manager](traffic-manager-performance-considerations.md)
* [Opérations sur le Gestionnaire de trafic (référence des API reste)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
