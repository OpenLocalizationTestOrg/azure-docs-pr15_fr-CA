<properties 
    pageTitle="Geo distribués à l’échelle avec les environnements de Service d’application" 
    description="Obtenir des informations à l’échelle horizontalement les applications à l’aide de la distribution géographique avec le Gestionnaire de trafic et les environnements de Service d’application." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="stefsch"/>   

# <a name="geo-distributed-scale-with-app-service-environments"></a>Geo distribués à l’échelle avec les environnements de Service d’application

## <a name="overview"></a>Vue d’ensemble ##
Scénarios d’applications qui nécessitent une échelle très élevée peuvent dépasser la capacité de ressources calcul disponible pour un seul déploiement d’une application.  Vote des applications, des événements sportifs et les événements de divertissement télévisée sont des exemples de scénarios qui requièrent une échelle extrêmement élevée. Échelle élevées peut être requise en faisant évoluer horizontalement les applications, avec plusieurs déploiements d’application effectuées au sein d’une seule région, ainsi qu’entre les régions, pour gérer les exigences de charge extrême.

Les environnements de Service d’application sont une plate-forme idéale pour l’évolution horizontale.  Une fois une application Service environnement configuration a été sélectionnée qui prennent en charge une vitesse connue, les développeurs peuvent déployer environnements de Service d’application supplémentaires selon le principe « découpage » pour atteindre une capacité de charge maximale de votre choix.

Par exemple, supposons qu’une application s’exécutant sur une configuration d’environnement de Service d’application a été testée pour gérer les demandes de 20 Ko par seconde (RPS).  Si la capacité de charge maximale souhaitée est 100K RPS, environnements de Service d’application cinq (5) peuvent être créés et configurés pour s’assurer de que l’application peut gérer la charge prévue maximale.

Dans la mesure où les clients accéder généralement les applications à l’aide d’un domaine personnalisé (ou vanity), les développeurs ont besoin d’un moyen de répartir les demandes d’application entre toutes les instances d’environnement de Service d’application.  Un excellent moyen d’y parvenir est de résoudre le domaine personnalisé à l’aide d’un [profil de gestionnaire de trafic Azure][AzureTrafficManagerProfile].  Le profil de Traffic Manager peut être configuré pour pointer sur tous les environnements de Service d’application individuels.  Traffic Manager gère automatiquement distribution des clients sur tous les environnements de Service d’application basée sur la paramètres du profil de gestionnaire de trafic d’équilibrage de la charge.  Cette approche est que tous les environnements de Service d’application sont déployés dans une seule région Azure, ou déployés dans le monde dans plusieurs régions Azure.

En outre, dans la mesure où les clients accès aux applications via le domaine personnel, les clients ne savent pas le nombre d’environnements de Service d’application exécutant une application.  Ainsi les développeurs peuvent rapidement et facilement ajouter et supprimer, environnements de Service d’application en fonction de la charge du trafic observé.

Le schéma conceptuel ci-dessous illustre une application répartie horizontalement dans trois environnements de Service d’application au sein d’une seule région.

![Architecture conceptuelle][ConceptualArchitecture] 

Le reste de cette rubrique vous guide à travers les différentes étapes de configuration d’une topologie distribuée pour l’exemple d’application à l’aide de plusieurs environnements de Service d’application.

## <a name="planning-the-topology"></a>Planification de la topologie ##
Avant de créer une empreinte de l’application distribuée, il est utile de quelques éléments d’informations à l’avance.

- **Domaine personnalisé pour l’application :**  Quel est le nom de domaine personnalisé que les utilisateurs utiliseront pour accéder à l’application ?  Pour l’exemple d’application, le nom de domaine personnalisé est *www.scalableasedemo.com*
- **Domaine de traffic Manager :**  Un nom de domaine doit être choisi lors de la création d’un [profil de gestionnaire de trafic Azure][AzureTrafficManagerProfile].  Ce nom à combiner avec le suffixe *trafficmanager.net* pour enregistrer une entrée de domaine qui est gérée par le Gestionnaire de trafic.  Pour l’exemple d’application, le nom choisi est *évolutive-ase-démonstration*.  Par conséquent, le nom de domaine complet qui est géré par le Gestionnaire de trafic est *évolutive-ase-demo.trafficmanager.net*.
- **Stratégie de mise à l’échelle de l’empreinte de l’application :**  L’empreinte de l’application est réparti sur plusieurs environnements de Service d’application dans une seule région ?  Plusieurs régions ?  Un mix and match des deux approches ?  La décision doit être basée sur les attentes d’origine le trafic du client, ainsi que comment le reste de l’application prenant en charge l’infrastructure back-end peut évoluer.  Par exemple, avec une application sans état de 100 %, une application peut être massivement mis à l’échelle à l’aide d’une combinaison de plusieurs environnements de Service application par région Azure, multipliée par les environnements de Service d’application déployée sur plusieurs régions Azure.  Avec 15 + publiques Azure régions disponibles, les clients peuvent créer réellement une empreinte de l’application d’une évolutivité à l’échelle mondiale.  Pour l’exemple d’application utilisé pour cet article, les trois environnements de Service d’application ont été créés dans une seule région Azure (Sud États-Unis).
- **Convention de dénomination pour les environnements de Service d’application :**  Chaque environnement de Service d’application requiert un nom unique.  Au-delà d’un ou de deux environnements de Service d’application, il est utile d’avoir une convention d’affectation de noms pour identifier chaque environnement de Service d’application.  Pour l’exemple d’application, une convention d’affectation de noms simple a été utilisée.  Les noms des trois environnements de Service d’application sont *fe1ase*, *fe2ase*et *fe3ase*.
- **Convention de dénomination pour les applications :**  Étant donné que plusieurs instances de l’application seront déployées, un nom est requis pour chaque instance de l’application déployée.  Une fonctionnalité peu connue, mais très pratique des environnements de Service d’application est que le même nom d’application peut être utilisé dans différents environnements de Service d’application.  Dans la mesure où chaque environnement de Service d’application possède un suffixe de domaine unique, les développeurs peuvent choisir à nouveau utiliser exactement le même nom d’application dans chaque environnement.  Par exemple un développeur pourrait avoir des applications comme suites : *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc..  Pour l’exemple d’application bien que chaque instance de l’application a également un nom unique.  Les noms d’instance application utilisés sont *webfrontend1*, *webfrontend2*et *webfrontend3*.


## <a name="setting-up-the-traffic-manager-profile"></a>Configuration du profil de Traffic Manager ##
Une fois que plusieurs instances d’une application sont déployés sur plusieurs environnements de Service d’application, les instances d’application individuels peuvent être inscrit avec le Gestionnaire de trafic.  Pour l’exemple d’application Gestionnaire de trafic profil est nécessaire pour la *demo.trafficmanager.net-ase évolutive* qui permet d’acheminer des clients pour toutes les instances d’une application déployée suivant :

- **webfrontend1.fe1ase.p.azurewebsites.net :**  Une instance de l’application déployée sur le premier environnement de Service d’application.
- **webfrontend2.fe2ase.p.azurewebsites.net :**  Une instance de l’application déployée sur le deuxième environnement de Service d’application.
- **webfrontend3.fe3ase.p.azurewebsites.net :**  Une instance de l’application déployée sur le troisième environnement de Service d’application.

Pour inscrire plusieurs points de terminaison des services d’application Azure, tous en cours d’exécution dans la **même** région Azure, le plus simple est le Powershell [prend en charge de trafic le Gestionnaire de ressources Azure][ARMTrafficManager].  

La première étape consiste à créer un profil de gestionnaire de trafic Azure.  Le code ci-dessous montre comment le profil a été créé pour l’exemple d’application :

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Notez comment le paramètre *RelativeDnsName* a été défini à *demo-ase évolutive*.  Voici comment le nom de domaine *demo.trafficmanager.net-ase évolutive* est créé et associé à un profil de gestionnaire de trafic.

Le paramètre *TrafficRoutingMethod* définit la stratégie de Traffic Manager utilisera pour déterminer comment répartir la charge du client sur tous les points de terminaison disponibles d’équilibrage de la charge.  Dans cet exemple la *Weighted* procédé a été choisi.  Ainsi, les demandes de clients sont réparties entre tous les points de terminaison des applications basées sur la pondération relative associée à chaque point de terminaison. 

Avec le profil créé, chaque instance d’application est ajouté au profil sous la forme d’un point de terminaison Azure natif.  Le code ci-dessous extrait une référence à chaque application de web front-end, puis ajoute chaque application sous la forme d’un point de terminaison de Traffic Manager par le paramètre *TargetResourceId* .


    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
    
Remarquez comment un appel à *Add-AzureTrafficManagerEndpointConfig* pour chaque instance d’application individuels.  Le paramètre *TargetResourceId* dans chaque commande Powershell fait référence à une des trois instances de l’application déployée.  Le profil de Traffic Manager va répartir la charge sur tous les systèmes de trois extrémité enregistrés dans le profil.

Toutes les trois points de terminaison utilisent la même valeur (10) pour le paramètre de *poids* .  Ainsi, les demandes client étalement Traffic Manager dans toutes les instances de l’application de trois relativement uniforme. 


## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Pointant vers le domaine personnalisé de l’application au niveau du domaine gestionnaire de trafic ##
La dernière étape nécessaire consiste à pointer le domaine personnalisé de l’application au niveau du domaine gestionnaire de trafic.  Pour l’exemple d’application, cela signifie pointant vers *www.scalableasedemo.com* *demo.trafficmanager.net-ase évolutive*.  Cette étape doit être effectuée dans le Registre de domaine qui gère le domaine personnalisé.  

À l’aide des outils de gestion de votre registraire domaine, un CNAME enregistre doit être créée qui pointe du domaine personnalisé au niveau du domaine gestionnaire de trafic.  L’illustration ci-dessous montre un exemple de l’aspect de cette configuration CNAME :

![CNAME pour domaine personnalisé][CNAMEforCustomDomain] 

Bien que non couverts dans cette rubrique, gardez à l’esprit que chaque instance d’une application individuelle doit posséder le domaine personnalisé inscrit, ainsi.  Dans le cas contraire, si une requête rend à une instance de l’application et l’application ne dispose pas du domaine personnalisé est inscrit avec l’application, la demande échoue.  

Dans cet exemple, le domaine personnalisé est *www.scalableasedemo.com*, et chaque instance d’application est un domaine personnalisé lui est associé.

![Domaine personnalisé][CustomDomain] 

Pour un récapitulatif de d’enregistrement d’un domaine personnalisé avec les applications de Service d’application Azure, consultez l’article suivant sur [l’inscription des domaines personnalisés][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Essai de la topologie distribuée ##
Le résultat final de la configuration de DNS et de Traffic Manager est que les demandes de *www.scalableasedemo.com* flux au sein de la séquence suivante :

1. Un périphérique ou un navigateur effectue une recherche DNS pour *www.scalableasedemo.com*
2. L’entrée CNAME au registraire de domaine entraîne la recherche DNS être redirigé vers le Gestionnaire de trafic Azure.
3. Une recherche DNS est effectuée pour *demo.trafficmanager.net-ase évolutive* par rapport à un des serveurs DNS de Traffic Manager Azure.
4. En fonction de la stratégie (le paramètre *TrafficRoutingMethod* utilisé précédemment lors de la création du profil de Traffic Manager) d’équilibrage de la charge, Traffic Manager sélectionne un points de terminaison configurés et renvoyer le nom de domaine complet de ce point de terminaison vers le navigateur ou le périphérique.
5.  Dans la mesure où le nom de domaine complet du point de terminaison est l’Url d’une instance de l’application en cours d’exécution dans un environnement de Service d’application, le navigateur ou le périphérique vous demande à un serveur DNS de Microsoft Azure pour résoudre le nom de domaine complet en une adresse IP. 
6. Le navigateur ou le périphérique envoie la requête HTTP/S à l’adresse IP.  
7. La demande se produisent à une des instances d’application en cours d’exécution sur l’un des environnements de Service d’application.

L’image de la console ci-dessous montre une recherche DNS pour le domaine personnalisé de l’exemple d’application résoudre avec succès à une instance de l’application en cours d’exécution sur l’un des trois environnements de Service exemple App (dans ce cas, le deuxième des trois environnements de Service d’application) :

![Recherche DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Informations et liens supplémentaires ##
Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Documentation sur le Powershell [prend en charge de trafic le Gestionnaire de ressources Azure][ARMTrafficManager].  

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
