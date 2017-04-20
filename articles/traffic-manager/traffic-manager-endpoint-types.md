<properties
    pageTitle="Types de point de terminaison de Traffic Manager | Microsoft Azure"
    description="Cet article présente les différents types de points de terminaison qui peuvent être utilisés avec le Gestionnaire de trafic Azure"
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

# <a name="traffic-manager-endpoints"></a>Les points de terminaison Traffic Manager

Microsoft Azure Traffic Manager vous permet de contrôler comment le trafic réseau est distribué à des déploiements d’applications en cours d’exécution dans différents centres de données. Vous configurez chaque déploiement de l’application sous la forme d’un « point de terminaison' dans le Gestionnaire de trafic. Traffic Manager reçoit une demande DNS, il choisit un point de terminaison disponible à renvoyer dans la réponse DNS. Gestionnaire de trafic base le choix de l’état du point de terminaison actuel et la méthode de routage du trafic. Pour plus d’informations, consultez [Fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).

Il existe trois types de point de terminaison pris en charge par le Gestionnaire de trafic :

- **Les points de terminaison Azure** sont utilisés pour les services hébergés dans Azure.
- **Les points de terminaison externes** sont utilisés pour les services hébergés en dehors d’Azure, sur site ou avec un autre fournisseur d’hébergement.
- **Les points de terminaison Nested** utilisés pour combiner des profils de Traffic Manager pour créer des schémas de routage de trafic plus flexibles pour prendre en charge les besoins de déploiements plus grands et plus complexes.

Il n’y a pas de restriction sur la combinaison des points de terminaison de types différents dans un seul profil de Traffic Manager. Chaque profil peut contenir toute combinaison des types de point de terminaison.

Les sections suivantes décrivent chaque type de point de terminaison de manière plus approfondie.

## <a name="azure-endpoints"></a>Les points de terminaison Azure

Les points de terminaison Azure sont utilisés pour des services Azure dans le Gestionnaire de trafic. Types de ressources Azure suivants sont pris en charge :

- « Classiques » IaaS VMs et PaaS services en nuage.
- Applications Web
- Ressources de PublicIPAddress (qui peut être connecté aux ordinateurs virtuels soit directement, soit par un équilibreur de charge Azure). Le publicIpAddress doit avoir un nom DNS attribué à être utilisé dans un profil de gestionnaire de trafic.

PublicIPAddress les ressources du Gestionnaire de ressources Azure. Ils n’existent pas dans le modèle de déploiement classique. Par conséquent, ils sont uniquement pris en charge dans le trafic du gestionnaire Gestionnaire de ressources Azure expériences. Les autres types de point de terminaison sont pris en charge via le Gestionnaire de ressources et le modèle de déploiement classique.

Lors de l’utilisation des points de terminaison Azure, Traffic Manager détecte un 'Standard' IaaS VM, cloud service ou une application Web est arrêtée et démarrée. Cet état est reflété dans l’état du point de terminaison. Pour plus d’informations, consultez [contrôle de point de terminaison de Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status) . Lorsque le service sous-jacent est arrêté, Traffic Manager n’effectue pas les vérifications de fonctionnement de point de terminaison ou de diriger le trafic vers le point de terminaison. Aucun gestionnaire de trafic facturation se produisent pour l’instance arrêté. Lorsque le service est redémarré, reprend la facturation et le point de terminaison n’est éligible pour recevoir le trafic. Cette détection ne s’applique pas aux points de terminaison PublicIpAddress.

## <a name="external-endpoints"></a>Les points de terminaison externes

Les points de terminaison externes sont utilisés pour les services en dehors d’Azure. Par exemple, un service hébergé sur site ou avec un autre fournisseur. Les points de terminaison externes peuvent être utilisés individuellement ou combinées avec des points de terminaison Azure dans le même profil de Traffic Manager. Combinaison de points de terminaison Azure avec des points de terminaison externes permet de différents scénarios :

- Dans un modèle de basculement actif-actif ou actif-passif, utilisez Azure permettant d’augmenter la redondance d’une application sur site.
- Pour réduire la latence des applications pour les utilisateurs dans le monde entier, étendre une application sur site pour les autres emplacements géographiques dans Azure. Pour plus d’informations, consultez [Traffic Manager 'Performances' le routage du trafic](traffic-manager-routing-methods.md#performance-traffic-routing-method).
- Azure utilisation pour fournir des capacités supplémentaires pour un existant sur site application, soit en continu, soit sous la forme d’une solution de « rafales-nuage » pour répondre à un pic de la demande.

Dans certains cas, il est utile d’utiliser des points de terminaison externes pour référencer des services Azure (pour obtenir des exemples, consultez le [Forum aux questions](#faq)). Dans ce cas, les vérifications de la santé sont facturées au taux de points de terminaison Azure, pas la fréquence des points de terminaison externe. Toutefois, contrairement aux points de terminaison Azure, si vous arrêtez ou supprimez le service sous-jacent, bilan de santé facturation continue jusqu'à ce que vous désactivez ou supprimez le point de terminaison dans le Gestionnaire de trafic.

## <a name="nested-endpoints"></a>Les points de terminaison imbriqués

Des points de terminaison imbriqués combinent plusieurs profils de Traffic Manager pour créer des schémas de routage de trafic flexibles et prend en charge les besoins de déploiements plus grands et complexes. Avec les points de terminaison Nested, un profil 'child' est ajouté sous la forme d’un point de terminaison à un profil « parent ». Profils de l’enfant et le parent peuvent contenir d’autres points de terminaison de tout type, y compris les autres profils imbriqués. Pour plus d’informations, voir [les profils de Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps comme points de terminaison

Certaines considérations supplémentaires s’appliquent lors de la configuration des applications Web en tant que points de terminaison dans le Gestionnaire de trafic :

1. Des applications Web uniquement à la référence SKU de « Standard » ou ci-dessus sont éligibles pour une utilisation avec le Gestionnaire de trafic. Échouent de tentatives d’ajout d’une application Web d’un SKU inférieur. Déclassement de la référence SKU d’une application Web existante de résultats dans le Gestionnaire de trafic n’est plus en envoyant le trafic pour cette application Web.

2. Lorsqu’un point de terminaison reçoit une requête HTTP, il utilise l’en-tête « hôte » dans la requête pour déterminer quelle application Web doit traiter la demande. L’en-tête de l’hôte contient le nom DNS utilisé pour lancer la requête, par exemple « contosoapp.azurewebsites.net ». Pour utiliser un nom DNS différent avec votre application Web, le nom DNS doit être enregistré sous la forme d’un nom de domaine personnalisé pour l’application. Lorsque vous ajoutez un point de terminaison d’application Web sous la forme d’un point de terminaison Azure, le nom DNS du profil Traffic Manager est automatiquement inscrit pour l’application. Cet enregistrement est automatiquement supprimé lorsque le point de terminaison est supprimé.

3. Chaque profil de Traffic Manager peut avoir au plus une extrémité Web App à partir de chaque région Azure. Pour contourner cette contrainte, vous pouvez configurer une application Web sous la forme d’un point de terminaison externe. Pour plus d’informations, consultez le [Forum aux questions](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Activation et désactivation des points de terminaison

La désactivation d’un point de terminaison dans le Gestionnaire de trafic peut être utile pour supprimer temporairement le trafic à partir d’un point de terminaison est en mode maintenance ou en cours de redéploiement. Une fois que le point de terminaison fonctionne de nouveau, il peut être réactivé.

Points de terminaison qui peuvent être activés et désactivés via le portail de Traffic Manager, PowerShell, CLI ou API REST, qui sont pris en charge dans le Gestionnaire de ressources et le modèle de déploiement classique.

>[AZURE.NOTE] Désactivation d’un point de terminaison Azure n’a rien à voir avec l’état de déploiement dans Azure. Un service Azure (comme un ordinateur virtuel ou une application Web reste en cours d’exécution et en mesure de recevoir le trafic même si désactivée dans le Gestionnaire de trafic. Le trafic peut être résolu directement à l’instance de service et non via le nom DNS de profil Traffic Manager. Pour plus d’informations, consultez [fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).

L’éligibilité en cours de chaque point de terminaison pour recevoir le trafic dépend des facteurs suivants :

- L’état du profil (activé/désactivé)
- L’état du point de terminaison (activé/désactivé)
- Les résultats de l’état de santé vérifie ce point de terminaison

Pour plus d’informations, reportez-vous à la section [Traffic Manager contrôle de point de terminaison](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Traffic Manager fonctionnant au niveau du DNS, il ne peut pas influencer les connexions existantes à un point de terminaison. Lorsqu’un point de terminaison n’est pas disponible, Traffic Manager indique les nouvelles connexions à un autre point de terminaison disponible. Toutefois, l’hôte derrière le point de terminaison désactivé ou défectueux peut continuer à recevoir le trafic via des connexions existantes, jusqu'à ce que les sessions sont terminées. Applications doivent limiter la durée de la session pour autoriser le trafic décharger des connexions existantes.

Si tous les points de terminaison dans un profil sont désactivés ou si le profil lui-même est désactivé, Traffic Manager envoie une réponse « NXDOMAIN » à une requête DNS.

## <a name="faq"></a>FAQ

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Puis-je utiliser le Gestionnaire de trafic avec des points de terminaison à partir de plusieurs abonnements ?

À l’aide de points de terminaison à partir de plusieurs abonnements n’est pas possible avec les applications Web Azure. Les applications Web Azure nécessite que n’importe quel nom de domaine personnalisé utilisé avec des applications Web est uniquement utilisé au sein d’un seul abonnement. Il n’est pas possible d’utiliser des applications Web à partir de plusieurs abonnements portant le même nom de domaine.

Pour les autres types de point de terminaison, il est possible d’utiliser le Gestionnaire de trafic avec des points de terminaison à partir de plus d’un abonnement. Comment vous configurez Traffic Manager dépend de si vous utilisez le modèle de déploiement classique ou l’expérience du Gestionnaire de ressources.

- Dans le Gestionnaire de ressources, les points de terminaison à partir de n’importe quel abonnement peuvent être ajoutées au Gestionnaire de trafic, tant que la configuration du profil de Traffic Manager de personne a un accès en lecture au point de terminaison. Ces autorisations peuvent être accordées à l’aide du [contrôle d’accès basé sur le rôle de gestionnaire de ressources de Azure (RBAC)](../active-directory/role-based-access-control-configure.md).
- Dans l’interface de modèle de déploiement classique, Traffic Manager nécessite que les Services en nuage ou applications Web configurés en tant que points de terminaison Azure résident dans l’abonnement de même que le profil du Gestionnaire de trafic. Les points de terminaison de Service cloud dans les autres abonnements peuvent être ajoutées au Gestionnaire de trafic comme points de terminaison « externes ». Ces points de terminaison externes sont facturés en tant que points de terminaison Azure, plutôt que de la vitesse externe.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Puis-je utiliser le Gestionnaire de trafic avec des emplacements de Service Cloud 'Test' ?

Oui. Service de cloud 'test' emplacements peut être configuré dans le Gestionnaire de trafic comme points de terminaison externes. Vérifications de la santé sont toujours être facturés à la fréquence des points de terminaison Azure. Étant donné que le type de point de terminaison externe est en cours d’utilisation, les modifications apportées au service sous-jacent ne sont pas prises automatiquement. Avec les points de terminaison externes, Traffic Manager ne peut pas détecter lorsque le Service Cloud est arrêté ou supprimé. Par conséquent, le Gestionnaire de trafic continue facturation pour les vérifications de fonctionnement jusqu'à ce que le point de terminaison est désactivé ou supprimé.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager prend en charge les points de terminaison IPv6 ?

Traffic Manager ne fournit pas actuellement des serveurs de noms IPv6-addressible. Toutefois, Traffic Manager peut toujours être utilisé par clients IPv6 se connecter aux points de terminaison IPv6. Un client ne rend pas les requêtes DNS directement au Gestionnaire de trafic. Au lieu de cela, le client utilise un service DNS récursive. Un client IPv6 uniquement envoie des requêtes au service récursif DNS via IPv6. Puis le service récursive doit être en mesure de contacter les serveurs de noms Traffic Manager à l’aide d’IPv4.

Traffic Manager répond avec le nom DNS du point de terminaison. Pour prendre en charge d’un point de terminaison IPv6, un enregistrement AAAA DNS pointant vers le nom DNS du point de terminaison à l’adresse IPv6 doit exister. Vérifications de la santé Traffic Manager prennent en charge uniquement les adresses IPv4. Le service doit exposer un point de terminaison IPv4 sur le même nom DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Puis-je utiliser le Gestionnaire de trafic avec plus d’une application Web dans la même région ?

En règle générale, Traffic Manager permet de diriger le trafic vers les applications déployées dans des régions différentes. Toutefois, il peut également être utilisé où une application a plusieurs déploiements dans la même région. Les points de terminaison de Traffic Manager Azure n’autorisent pas plus d’un point de terminaison Web App à partir de la même région Azure pour être ajoutés au même profil Traffic Manager.

Les étapes suivantes fournissent une solution de contournement pour cette contrainte :

1. Vérifiez que vos points de terminaison sont dans un autre site web app « unités de l’échelle ». Un nom de domaine doit correspondre à un seul site dans une unité d’échelle donnée. Par conséquent, les deux applications Web dans la même unité d’échelle ne peuvent pas partager un profil de gestionnaire de trafic.
2. Ajoutez votre nom de domaine vanity sous la forme d’un nom d’hôte personnalisé pour chaque application Web. Chaque application Web doit être dans une unité d’échelle différents. Toutes les applications Web doivent appartenir à la même abonnement.
3. Ajouter le point de terminaison Web App une (et une seule) à votre profil de Traffic Manager, sous la forme d’un point de terminaison Azure.
4. Ajouter chaque point de terminaison d’application Web supplémentaire à votre profil de Traffic Manager sous la forme d’un point de terminaison externe. Les points de terminaison externes ne peuvent être ajoutées en utilisant le modèle de déploiement du Gestionnaire de ressources.
5. Créer un enregistrement DNS CNAME dans votre domaine de personnel qui pointe vers le nom de DNS de votre profil de Traffic Manager (<>.... trafficmanager.net).
6. Accéder à votre site via le nom de domaine vanity, pas le nom DNS de profil Traffic Manager.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez [comment fonctionne la Traffic Manager](traffic-manager-how-traffic-manager-works.md).
- Obtenir des informations sur le Traffic Manager de [point de terminaison de surveillance et de basculement automatique](traffic-manager-monitoring.md).
- Obtenir des informations sur le Traffic Manager [des méthodes de routage du trafic](traffic-manager-routing-methods.md).
