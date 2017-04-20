<properties
    pageTitle="Configurer des méthodes de routage de Traffic Manager | Microsoft Azure"
    description="Cet article explique comment configure les différentes méthodes de routage dans le Gestionnaire de trafic"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-traffic-manager-routing-methods"></a>Configurer des méthodes de routage de Traffic Manager

Gestionnaire de trafic Azure propose trois méthodes de routage qui contrôlent la manière dont le trafic est acheminé aux points de terminaison de service disponibles. La méthode de routage de trafic est appliquée à chaque requête DNS reçu afin de déterminer quel point de terminaison doit être retourné dans la réponse DNS.

Il existe trois méthodes de routage de trafic dans le Gestionnaire de trafic :

- **Priorité :** Sélectionnez 'Priority' lorsque vous souhaitez utiliser un point de terminaison de service principal et fournir des sauvegardes dans le cas où le serveur principal n’est pas disponible.
- **Pondérée :** Sélectionnez « pondérée » lorsque vous souhaitez répartir le trafic entre un ensemble de points de terminaison, régulièrement ou en fonction du poids, que vous définissez.
- **Performances :** Sélectionnez 'Performances' lorsque vous avez des points de terminaison dans des lieux géographiques différents et vous souhaitez que les utilisateurs finaux utilisent le point de terminaison « plus proche » en fonction de la latence du réseau le plus bas.

## <a name="configure-priority-routing-method"></a>Configurer la méthode d’acheminement de priorité

Quel que soit le mode site Web, les sites Web de Azure fournissent déjà des fonctionnalités de basculement pour les sites Web au sein d’un centre de données (également connu sous le nom d’une région). Traffic Manager fournit le basculement pour les sites Web dans différents centres de données.

Un modèle commun pour le basculement du service est à acheminer le trafic vers le service primaire et de fournir un ensemble de services de sauvegarde identiques pour le basculement. Les étapes suivantes expliquent comment configurer ce basculement par ordre de priorité avec les sites Web et des services en nuage Azure :

1. Dans le portail Azure classique, dans le volet gauche, cliquez sur l’icône du **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic.
2. Dans le volet Gestionnaire de trafic dans le portail classique Azure, recherchez le profil de Traffic Manager qui contient les paramètres que vous souhaitez modifier. Pour ouvrir la page de paramètres de profil, cliquez sur la flèche à droite du nom du profil.
3. Sur votre page de profil, cliquez sur les **points de terminaison** en haut de la page. Vérifiez que les services en nuage et les sites Web que vous souhaitez inclure dans votre configuration.
4. Cliquez sur **configurer** pour ouvrir la page de configuration en haut.
5. Pour les **paramètres de méthode de routage de trafic**, vérifiez que la méthode de routage de trafic est le **basculement**. S’il n’est pas le cas, cliquez sur **reprise** dans la liste déroulante.
6. Pour la **Liste de priorité de basculement sur incident**, ajustez l’ordre de basculement pour vos points de terminaison. Lorsque vous sélectionnez la méthode de routage de trafic de **basculement** , l’ordre des points de terminaison sélectionnés est important. Le point de terminaison principal se trouve au-dessus. Utilisez les flèches et bas pour modifier l’ordre en fonction des besoins. Pour plus d’informations sur la façon de définir la priorité de basculement à l’aide de Windows PowerShell, voir [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Vérifiez que les **Paramètres de contrôle** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés du trafic. Pour analyser les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et implique que le fichier est dans le répertoire racine (par défaut).
8. Après avoir terminé vos modifications de configuration, cliquez sur **Enregistrer** en bas de la page.
9. Tester les modifications de votre configuration.
10. Une fois votre profil de Traffic Manager fonctionne, modifiez l’enregistrement DNS sur le serveur DNS faisant autorité pour le nom de domaine de votre entreprise au nom de domaine Traffic Manager.

## <a name="configure-weighted-routing-method"></a>Configurer une méthode de routage pondérée

Un modèle commun méthode de routage de trafic consiste à fournir un ensemble de points de terminaison identiques, ce qui inclut les services en nuage et sites Web, et à acheminer le trafic vers chacun de façon alternée. Les étapes suivantes décrivent comment configurer ce type de méthode de routage du trafic.

>[AZURE.NOTE] Sites Web Azure fournissent déjà des fonctionnalités pour les sites Web au sein d’un centre de données (également connu sous le nom d’une région) d’équilibrage de charge round robin. Traffic Manager vous permet de spécifier la méthode de routage de trafic de la répétition alternée pour les sites Web dans différents centres de données.

1. Dans le portail Azure classique, dans le volet gauche, cliquez sur l’icône du **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic.
2. Dans le volet Gestionnaire de trafic, recherchez le profil de Traffic Manager qui contient les paramètres que vous souhaitez modifier. Pour ouvrir la page de paramètres de profil, cliquez sur la flèche à droite du nom du profil.
3. Sur la page de votre profil, cliquez sur les **points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents.
4. Sur votre page de profil, cliquez sur **configurer** pour ouvrir la page de configuration en haut.
5. Pour les **paramètres de la méthode de routage du trafic**, vérifiez que la méthode d’acheminement du trafic est **alternée**. S’il n’est pas le cas, cliquez sur **répétition alternée** , dans la liste déroulante.
6. Vérifiez que les **Paramètres de contrôle** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés du trafic. Pour analyser les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et implique que le fichier est dans le répertoire racine (par défaut).
7. Après avoir terminé vos modifications de configuration, cliquez sur **Enregistrer** en bas de la page.
8. Tester les modifications de votre configuration.
9. Une fois votre profil de Traffic Manager fonctionne, modifiez l’enregistrement DNS sur le serveur DNS faisant autorité pour le nom de domaine de votre entreprise au nom de domaine Traffic Manager.

## <a name="configure-performance-traffic-routing-method"></a>Configurer la méthode de routage de trafic de performances

La méthode de routage de trafic de performances vous permet de diriger le trafic vers le point de terminaison avec la latence la plus faible dans le réseau du client. En règle générale, le centre de données avec la latence la plus faible est le plus proche dans la distance géographique. Cette méthode de routage de trafic ne peut pas tenir compte des modifications en temps réel dans la configuration du réseau ou de charger.

1. Dans le portail Azure classique, dans le volet gauche, cliquez sur l’icône du **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic.
2. Dans le volet Gestionnaire de trafic, recherchez le profil de Traffic Manager qui contient les paramètres que vous souhaitez modifier. Pour ouvrir la page de paramètres de profil, cliquez sur la flèche à droite du nom du profil.
3. Sur la page de votre profil, cliquez sur les **points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents.
4. Sur la page de votre profil, cliquez sur **configurer** pour ouvrir la page de configuration en haut.
5. Pour les **paramètres de méthode de routage de trafic**, vérifiez que la méthode de routage de trafic est * *performances*. S’il n’est pas le cas, cliquez sur * *performances** dans la liste déroulante.
6. Vérifiez que les **Paramètres de contrôle** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés du trafic. Pour analyser les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et implique que le fichier est dans le répertoire racine (par défaut).
7. Après avoir terminé vos modifications de configuration, cliquez sur **Enregistrer** en bas de la page.
8. Tester les modifications de votre configuration.
9. Une fois votre profil de Traffic Manager fonctionne, modifiez l’enregistrement DNS sur le serveur DNS faisant autorité pour le nom de domaine de votre entreprise au nom de domaine Traffic Manager.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les profils de Traffic Manager](traffic-manager-manage-profiles.md)
* [Méthodes de routage du Gestionnaire de trafic](traffic-manager-routing-methods.md)
* [Test des paramètres du Gestionnaire de trafic](traffic-manager-testing-settings.md)
* [Pointez un domaine Internet de l’entreprise à un domaine de Traffic Manager](traffic-manager-point-internet-domain.md)
* [Gérer les points de terminaison de Traffic Manager](traffic-manager-manage-endpoints.md)
* [Résolution des problèmes de Traffic Manager dégradées état](traffic-manager-troubleshooting-degraded.md)
