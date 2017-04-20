<properties
    pageTitle="Test des paramètres du Gestionnaire de trafic | Microsoft Azure"
    description="Cet article vous permettra de tester les paramètres du Gestionnaire de trafic"
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

# <a name="test-your-traffic-manager-settings"></a>Tester vos paramètres de gestionnaire de trafic

Pour tester vos paramètres de Traffic Manager, vous devez disposer de plusieurs clients, dans divers emplacements, à partir de laquelle vous pouvez exécuter vos tests. Ensuite, mettez les points de terminaison dans votre profil de gestionnaire de trafic vers le bas un à la fois.

* La valeur DNS TTL faible afin que les modifications se propagent rapidement (par exemple, 30 secondes).
* Connaître les adresses IP de vos services en nuage Azure et les sites Web dans le profil que vous testez.
* Utiliser des outils qui vous permettent de résoudre un nom DNS à une adresse IP et afficher cette adresse.

Vous vérifiez que les noms DNS de résoudre les adresses IP des points de terminaison dans votre profil. Les noms doivent résoudre de manière cohérente avec la méthode de routage de trafic définie dans le profil du Gestionnaire de trafic. Vous pouvez utiliser les outils tels que **nslookup** ou **Aller** à résoudre les noms DNS.

Les exemples ci-dessous vous permettent de tester votre profil de gestionnaire de trafic.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Vérifiez le profil de Traffic Manager à l’aide de nslookup et ipconfig dans Windows

1. En tant qu’administrateur, ouvrez une invite de commandes Windows PowerShell ou une commande.
2. Type de `ipconfig /flushdns` pour vider le cache de résolution DNS.
3. Type de `nslookup <your Traffic Manager domain name>`. Par exemple, la commande suivante vérifie le nom de domaine avec le préfixe *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Un résultat par défaut affiche les informations suivantes :

    * Le nom DNS et l’adresse IP du serveur DNS pour résoudre ce nom de domaine Traffic Manager en cours d’accès.
    * Le nom de domaine Traffic Manager vous avez tapé sur la ligne de commande après « nslookup » et l’adresse IP à laquelle le domaine Traffic Manager résout. La deuxième adresse IP est un important pour vérifier. Il doit correspondre à une public adresse IP virtuelle (VIP) pour un des services en nuage ou de sites Web dans le profil de Traffic Manager que vous testez.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Comment tester la méthode de routage de trafic de basculement

1. Laissez tous les points de terminaison des.
2. À l’aide d’un seul client, demande la résolution DNS pour le nom de domaine de votre société à l’aide de nslookup ou un utilitaire similaire.
3. Assurez-vous que l’adresse IP résolue correspond le principal point de terminaison.
4. Réduire votre principal point de terminaison ou supprimer le fichier de contrôle afin que le Gestionnaire de trafic pense que l’application est en panne.
5. Attendez que la DNS Time-to-Live (TTL) le profil de Traffic Manager plus un deux minutes supplémentaires. Par exemple, si votre DNS TTL est de 300 secondes (5 minutes), vous devez attendre sept minutes.
6. Vidage de votre DNS client du cache et la demande de résolution de DNS à l’aide de nslookup. Dans Windows, vous pouvez vider votre cache DNS avec la commande ipconfig /flushdns.
7. Assurez-vous que l’adresse IP résolue correspond à votre point de terminaison secondaire.
8. Répétez le processus de mise hors service de chaque point de terminaison à son tour. Vérifiez que le serveur DNS renvoie l’adresse IP du point de terminaison suivant dans la liste. Lorsque tous les points de terminaison sont arrêtés, vous devez obtenir l’adresse IP du point d’entrée principal à nouveau.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Comment tester la méthode de routage de trafic pondérée

1. Laissez tous les points de terminaison des.
2. À l’aide d’un seul client, demande la résolution DNS pour le nom de domaine de votre société à l’aide de nslookup ou un utilitaire similaire.
3. Assurez-vous que l’adresse IP résolue correspond à l’un de vos points de terminaison.
4. Vider le cache du client DNS et répétez les étapes 2 et 3 pour chaque point de terminaison. Vous devriez voir les différentes adresses IP retournées pour chacun de vos points de terminaison.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Comment tester la méthode de routage de trafic de performances

Pour tester efficacement une méthode de routage de trafic de performances, vous devez avoir des clients situés dans différentes parties du monde. Vous pouvez créer des clients dans différentes régions Azure qui peuvent être utilisées pour tester les services. Si vous avez un réseau global, vous pouvez à distance à se connecter à des clients dans d’autres parties du monde et exécuter vos tests à partir de là.

Il existe également disponible sur le web de recherche DNS et approfondir les services disponibles. Certains de ces outils vous donnent la possibilité de vérifier la résolution de nom DNS à partir de différents emplacements dans le monde entier. Effectuez une recherche sur « Recherche DNS » pour obtenir des exemples. Les services tiers tels que les systèmes Gomez ou discours peuvent servir pour confirmer que vos profils distribuez le trafic comme prévu.

## <a name="next-steps"></a>Étapes suivantes

* [À propos des méthodes de routage de trafic Traffic Manager](traffic-manager-routing-methods.md)
* [Considérations relatives aux performances de Traffic Manager](traffic-manager-performance-considerations.md)
* [Résolution des problèmes de Traffic Manager dégradées état](traffic-manager-troubleshooting-degraded.md)




