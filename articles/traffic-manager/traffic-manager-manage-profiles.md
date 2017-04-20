<properties
    pageTitle="Gérer les profils d’Azure Traffic Manager | Microsoft Azure"
    description="Cet article vous aide à créer, désactiver, activer, supprimer et afficher l’historique d’un profil de gestionnaire de trafic Azure."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="manage-an-azure-traffic-manager-profile"></a>Gérer un profil Azure Traffic Manager

Profils de trafic Manager utilisent des méthodes de routage de trafic à contrôler la distribution du trafic sur vos services en nuage ou les points de terminaison de site Web. Cet article explique comment créer et gérer ces profils.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Créer un profil de gestionnaire de trafic à l’aide de création rapide

Vous pouvez rapidement créer un profil de gestionnaire de trafic à l’aide de création rapide dans Azure portal classique. Création rapide vous permet de créer des profils avec des paramètres de configuration de base. Toutefois, vous ne pouvez pas utiliser la création rapide de paramètres tels que le jeu de points de terminaison (sites Web et services en nuage), l’ordre de basculement pour la méthode de routage de trafic de basculement sur incident, ou des paramètres d’analyse. Après avoir créé votre profil, vous pouvez configurer ces paramètres dans Azure portal classique. Traffic Manager prend en charge jusqu'à 200 points de terminaison par profil. Toutefois, la plupart des scénarios d’utilisation ne requièrent que quelques exemples de points de terminaison.

### <a name="to-create-a-traffic-manager-profile"></a>Pour créer un profil de Traffic Manager

1. **Déployez vos services en nuage et les sites Web à votre environnement de production.** Pour plus d’informations sur les services en nuage, consultez [Les Services en nuage](http://go.microsoft.com/fwlink/p/?LinkId=314074). Pour plus d’informations sur les sites Web, consultez les [sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Connectez-vous au portail Azure classique.** Cliquez sur **Nouveau** dans le coin inférieur gauche du portail, cliquez sur **les Services réseau > Traffic Manager**, puis cliquez sur **Création rapide** pour commencer la configuration de votre profil.
3. **Configurez le préfixe DNS.** Donnez à votre profil de gestionnaire de trafic DNS unique nom de préfixe. Vous pouvez spécifier uniquement le préfixe pour un nom de domaine du Gestionnaire de trafic.
4. **Sélectionnez l’abonnement.** Sélectionnez l’abonnement Azure approprié. Chaque profil est associé à un seul abonnement. Si vous disposez d’un abonnement, cette option ne s’affiche pas.
5. **Sélectionnez la méthode de routage du trafic.** Sélectionnez la méthode d’acheminement du trafic dans la **stratégie de routage du trafic**. Pour plus d’informations sur les méthodes de routage du trafic, consultez [les méthodes de routage de trafic sur le Gestionnaire de trafic](traffic-manager-routing-methods.md).
6. **Cliquez sur « Créer » pour créer le profil**. Lors de la configuration du profil est terminée, vous pouvez localiser votre profil dans le volet Gestionnaire de trafic dans Azure portal classique.
7. **Configurer des points de terminaison, la surveillance et des paramètres supplémentaires dans Azure portal classique.** Création rapide uniquement configure les paramètres de base. Il est nécessaire de configurer des paramètres supplémentaires, tels que la liste des points de terminaison et l’ordre de basculement du point de terminaison.


## <a name="disable-enable-or-delete-a-profile"></a>Désactiver, activer ou supprimer un profil

Vous pouvez désactiver un profil existant afin que le Gestionnaire de trafic ne fait pas référence les demandes utilisateur vers les points de terminaison configurés. Lorsque vous désactivez un profil Traffic Manager, le profil et les informations contenues dans le profil restent intactes et peuvent être modifiés dans l’interface du Gestionnaire de trafic.  Les redirections de reprendre lorsque vous réactivez le profil. Lorsque vous créez un profil de gestionnaire de trafic dans le portail classique Azure, il est automatiquement activé. Si vous décidez de qu'un profil n’est plus nécessaire, vous pouvez le supprimer.

### <a name="to-disable-a-profile"></a>Pour désactiver un profil

1. Si vous utilisez un nom de domaine personnalisé, modifiez l’enregistrement CNAME sur votre serveur DNS Internet afin qu’il ne pointe plus vers votre profil Traffic Manager.
2. Le trafic cesse est dirigée vers les points de terminaison via les paramètres de profil du Gestionnaire de trafic.
3. Sélectionnez le profil que vous souhaitez désactiver. Dans la page Gestionnaire de trafic, sélectionnez le profil en cliquant sur la colonne en regard du nom de profil. Remarque, en cliquant sur le nom du profil ou sur la flèche en regard du nom ouvre la page de paramètres pour le profil.
4. Après avoir sélectionné le profil, cliquez sur **désactiver** au bas de la page.

### <a name="to-enable-a-profile"></a>Pour activer un profil

1. Sélectionnez le profil que vous souhaitez désactiver. Dans la page Gestionnaire de trafic, sélectionnez le profil en cliquant sur la colonne en regard du nom de profil. Remarque, en cliquant sur le nom du profil ou sur la flèche en regard du nom ouvre la page de paramètres pour le profil.
2. Après avoir sélectionné le profil, cliquez sur **Activer** en bas de la page.
3. Si vous utilisez un nom de domaine personnalisé, créez un enregistrement de ressource CNAME sur votre serveur Internet DNS pour pointer vers le nom de domaine de votre profil de gestionnaire de trafic.
4. Le trafic est dirigé vers les points de terminaison à nouveau.

### <a name="to-delete-a-profile"></a>Pour supprimer un profil

1. Assurez-vous que l’enregistrement de ressource DNS sur votre serveur DNS Internet n’utilise plus un enregistrement de ressource CNAME qui pointe vers le nom de domaine de votre profil de gestionnaire de trafic.
2. Sélectionnez le profil que vous souhaitez désactiver. Dans la page Gestionnaire de trafic, sélectionnez le profil en cliquant sur la colonne en regard du nom de profil. Remarque, en cliquant sur le nom du profil ou sur la flèche en regard du nom ouvre la page de paramètres pour le profil.
3. Après avoir sélectionné le profil, cliquez sur **Supprimer** au bas de la page.

## <a name="view-traffic-manager-profile-change-history"></a>Historique des modifications de profil de gestionnaire du trafic

Vous pouvez afficher l’historique des modifications de votre profil de Traffic Manager dans le portail Azure classique dans les Services de gestion.

### <a name="to-view-your-traffic-manager-change-history"></a>Pour afficher votre historique de modification Traffic Manager

1. Dans le volet gauche du portail Azure classique, cliquez sur **Gestion des Services**.
2. Dans la page Gestion des Services, cliquez sur **Journaux d’opération**.
3. Dans la page journaux d’opération, vous pouvez filtrer pour afficher l’historique de modification de votre profil de Traffic Manager. Après avoir sélectionné vos options de filtrage, cliquez sur la case à cocher pour afficher les résultats.

   - Pour afficher les modifications pour tous les profils, sélectionnez votre abonnement et la plage de temps, puis **Traffic Manager** dans le menu contextuel **Type** .
   - Pour filtrer par un nom de profil, tapez le nom du profil dans le champ **Nom du Service** , ou sélectionnez-le dans le menu contextuel.
   - Pour afficher les détails de chaque modification individuelle, sélectionnez la ligne contenant la modification que vous souhaitez afficher, puis cliquez sur **Détails** en bas de la page. Dans la fenêtre **Détails de l’opération** , vous pouvez afficher la représentation XML de l’objet de l’API qui a été créé ou mis à jour dans le cadre de l’opération.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter un point de terminaison](traffic-manager-endpoints.md)
- [Configurer la méthode d’acheminement de basculement](traffic-manager-configure-failover-routing-method.md)
- [Configurer la méthode de routage alternée](traffic-manager-configure-round-robin-routing-method.md)
- [Configurer la méthode de routage de performances](traffic-manager-configure-performance-routing-method.md)
- [Pointez un domaine Internet de l’entreprise à un nom de domaine Traffic Manager](traffic-manager-point-internet-domain.md)
- [Résolution des problèmes de Traffic Manager dégradées état](traffic-manager-troubleshooting-degraded.md)