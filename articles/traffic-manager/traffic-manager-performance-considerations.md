<properties
    pageTitle="Considérations de performances pour le Gestionnaire de trafic Azure | Microsoft Azure"
    description="Comprendre les performances Traffic Manager et comment tester les performances de votre site Web lors de l’utilisation du Gestionnaire de trafic"
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
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="performance-considerations-for-traffic-manager"></a>Considérations de performances pour le Gestionnaire de trafic

Cette page explique les considérations relatives aux performances à l’aide du Gestionnaire de trafic. Envisagez le scénario suivant :

Vous avez des instances de votre site Web dans les régions WestUS et EastAsia. Une des instances ne réussit pas la vérification d’état de la sonde de gestionnaire de trafic. Trafic d’application est dirigé vers la région en bon état. Ce basculement est attendu, mais les performances peuvent être un problème en fonction de la latence du trafic maintenant à se déplacer une zone distante.

## <a name="how-traffic-manager-works"></a>Fonctionnement du Gestionnaire de trafic

Le seul impact sur les performances que Traffic Manager peuvent avoir sur votre site Web est la recherche DNS initiale. Une requête DNS pour le nom de votre profil de gestionnaire de trafic est gérée par le serveur racine DNS Microsoft qui héberge la zone trafficmanager.net. Traffic Manager remplit et met à jour régulièrement, serveurs de racine DNS de Microsoft basées sur la stratégie du Gestionnaire de trafic et les résultats de la sonde. Donc, même au cours de la recherche DNS initiale, les requêtes DNS sont envoyées au Gestionnaire de trafic.

Traffic Manager est constitué de plusieurs composants : serveurs, un service d’API, la couche de stockage et un service de surveillance du point de terminaison de nom DNS. En cas de défaillance d’un composant de service du Gestionnaire de trafic, il n’a aucun effet sur le nom DNS associé à votre profil de Traffic Manager. Les enregistrements dans les serveurs DNS Microsoft restent inchangés. Toutefois, analyse du point de terminaison et la mise à jour de DNS ne sont pas effectuées. Par conséquent, Traffic Manager n’est pas en mesure de mettre à jour DNS pour pointer vers votre site de basculement lorsque votre site principal tombe en panne.

Résolution de noms DNS est rapide et les résultats sont mis en cache. Les serveurs DNS que le client utilise pour la résolution de nom dépend de la vitesse de la recherche DNS initiale. En général, un client peut effectuer une recherche DNS dans environ 50 ms. Les résultats de la recherche sont mis en cache pour la durée de la DNS Time-to-live (TTL). La durée de vie pour le Gestionnaire de trafic par défaut est 300 secondes.

Le trafic n’est pas transmis par le biais du Gestionnaire de trafic. Une fois la recherche DNS est terminée, le client a une adresse IP d’une instance de votre site web. Le client se connecte directement à cette adresse et ne passe pas par le biais du Gestionnaire de trafic. La stratégie du Gestionnaire de trafic que vous choisissez a aucune influence sur les performances de DNS. Toutefois, une méthode routage performances peut impact négatif sur l’expérience de l’application. Par exemple, si votre stratégie redirige le trafic en provenance d’Amérique du Nord dans une instance hébergée en Asie, la latence du réseau pour ces sessions peut être un problème de performances.

## <a name="measuring-traffic-manager-performance"></a>Mesurer les performances du Gestionnaire de trafic

Il existe plusieurs sites Web, que vous pouvez utiliser pour comprendre les performances et le comportement d’un profil de gestionnaire de trafic. La plupart de ces sites sont libres mais peuvent comporter des limites. Certains sites proposent améliorée de surveillance et de reporting pour les frais.

Les outils sur ces sites les latences DNS de mesure et d’affichage la résolu adresses IP pour les adresses de clients dans le monde entier. La plupart de ces outils ne pas mettre en cache les résultats DNS. Par conséquent, les outils affichent la recherche DNS complet chaque fois qu’un test est exécuté. Lorsque vous testez votre propre client, vous rencontrez uniquement les performances de recherche DNS complète une fois pendant la durée de vie.

## <a name="sample-tools-to-measure-dns-performance"></a>Outils pour mesurer les performances de DNS

- [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS propose de nombreux outils de performance. L’outil de comparaison de DNS peut vous indiquer le temps nécessaire pour résoudre le nom DNS et comment qui est comparé à d’autres fournisseurs de service DNS.

- [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Un des outils plus simples est WebSitePulse. Entrez l’URL pour afficher le temps de résolution DNS, le premier octet, dernier octet et autres statistiques sur les performances. Vous pouvez choisir parmi trois emplacements de test différents. Dans cet exemple, vous voyez que la première exécution indique que la recherche DNS prend 0.204 s.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Les résultats sont mis en cache, le deuxième test pour le point de terminaison Traffic Manager même la recherche DNS prend 0,002 s.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

- [Moniteur de App synthétique d’autorité de certification](https://asm.ca.com/en/checkit.php)

    Anciennement appelé l’outil du site Web de Watchmouse à cocher, ce site vous indiquent le temps de résolution DNS valide dans plusieurs régions géographiques simultanément. Entrez l’URL pour afficher le temps de résolution DNS, heure de la connexion et la vitesse à partir de plusieurs emplacements géographiques. Ce test permet de voir le service hébergé qui est renvoyé pour différents emplacements dans le monde entier.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

- [Pingdom](http://tools.pingdom.com/)

    Cet outil fournit des statistiques de performance pour chaque élément d’une page web. L’onglet analyse de la Page affiche le pourcentage de temps consacré à la recherche DNS.

- [Quel est mon DNS ?](http://www.whatsmydns.net/)

    Ce site effectue une recherche DNS à partir de 20 emplacements différents et affiche les résultats sur une carte.

- [Interface Web de creuser](http://www.digwebinterface.com)

    Ce site présente que des informations DNS, y compris les enregistrements CNAME et les enregistrements de plus détaillées. Assurez-vous que vous vérifiez le « Sortie Coloriser » et « Statistiques » sous options et sélectionnez « Tout » sous serveurs de noms.

## <a name="next-steps"></a>Étapes suivantes

[À propos des méthodes de routage de trafic Traffic Manager](traffic-manager-routing-methods.md)

[Tester vos paramètres de gestionnaire de trafic](traffic-manager-testing-settings.md)

[Opérations sur le Gestionnaire de trafic (référence des API reste)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Applets de commande Gestionnaire de trafic Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
