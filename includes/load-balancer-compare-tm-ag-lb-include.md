## <a name="load-balancer-differences"></a>Différences d’équilibrage de la charge

Il existe différentes options pour distribuer le trafic réseau à l’aide de Microsoft Azure. Ces options de travail différemment des autres ayant une autre fonction définie et prendre en charge différents scénarios. Ils peuvent chacun être utilisés de manière isolée ou en les combinant.

- **Équilibrage de la charge Azure** fonctionne sur la couche de transport (couche 4 dans la pile de référence du réseau OSI). Il assure la distribution au niveau du réseau du trafic entre les instances d’une application en cours d’exécution dans le même centre de données Azure.

- **Passerelle d’application** fonctionne sur la couche application (couche 7 dans la pile de référence du réseau OSI). Il fonctionne comme un service de proxy inversé, mettre fin à la connexion client et transfert des demandes aux principaux points de terminaison.

- **Traffic Manager** fonctionne au niveau du DNS.  Il utilise les réponses DNS pour diriger le trafic des utilisateurs finaux aux points de terminaison répartis à échelle mondiale. Clients puis se connecter à ces points de terminaison directement.

Le tableau suivant récapitule les fonctionnalités offertes par chaque service :

| Service | Équilibreur de charge Azure | Passerelle d’application | Traffic Manager |
|---|---|---|---|
|Technologie| Au niveau du transport (couche 4) | Niveau de l’application (couche 7) | Niveau de DNS |
| Protocoles d’application pris en charge | Tout | HTTP et HTTPS |  Tout (un point de terminaison HTTP est requis pour l’analyse du point de terminaison) |
| Points de terminaison | Instances de rôle Azure VM et les Services en nuage | Toute adresse IP interne d’Azure ou l’internet public adresse IP | Azure ordinateurs virtuels, les Services en nuage, Azure Web Apps et les points de terminaison externes |
| Prise en charge de Vnet | Peut être utilisé pour des applications Internet internes et en vis-à-vis (Vnet) | Peut être utilisé pour des applications Internet internes et en vis-à-vis (Vnet) |    Prend uniquement en charge les applications ouvertes sur Internet |
Contrôle de point de terminaison | Prise en charge via des sondes | Prise en charge via des sondes | Prise en charge via HTTP/HTTPS GET | 

Azure équilibreur de charge et le réseau de gammes de passerelle d’Application aux points de terminaison de trafic, mais ils ont différents scénarios d’utilisation pour gérer le trafic. Le tableau suivant permet de comprendre la différence entre les équilibreurs de deux charge :

| Type de | Équilibreur de charge Azure | Passerelle d’application |
|---|---|---|
| Protocoles | UDP/TCP. | HTTP / HTTPS |
| Réservation d’IP | Prise en charge | Non pris en charge | 
| Mode d’équilibrage de la charge | 5-tuple(source IP, source port, destination IP, destination port, protocol type) | Répétition alternée<br>Routage basé sur l’URL | 
| Mode d’équilibrage de la charge (adresse IP source / sticky sessions) |  2 tuples (adresse IP source et destination IP), 3-tuple (adresse IP source, adresse IP de destination et le port). Peut évoluer vers le haut ou vers le bas selon le nombre de machines virtuelles | Affinité de basée sur les cookies<br>Routage basé sur l’URL |
| Sondes de santé | Par défaut : intervalle de sonde - 15 secondes. Extraits de rotation : 2 défaillances continues. Prend en charge les sondes définies par l’utilisateur | Intervalle d’inactivité sonde 30 secondes. Prise d’après 5 échecs de trafic consécutifs ou une défaillance unique de sonde en mode inactif. Prend en charge les sondes définies par l’utilisateur | 
| Le déchargement SSL | Non pris en charge | Prise en charge | 
  