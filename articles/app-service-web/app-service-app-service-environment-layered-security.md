<properties 
    pageTitle="Architecture de sécurité multiniveau avec les environnements de Service d’application" 
    description="Implémentation d’une architecture de sécurité multiniveau avec les environnements de Service d’application." 
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
    ms.date="08/30/2016" 
    ms.author="stefsch"/>   

# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Mise en œuvre d’une Architecture de sécurité multiniveau avec les environnements de Service d’application

## <a name="overview"></a>Vue d’ensemble ##
 
Dans la mesure où les environnements de Service d’application de fournir un environnement isolé runtime déployé dans un réseau virtuel, les développeurs peuvent créer une architecture de sécurité multiniveau fournissant différents niveaux d’accès réseau pour chaque couche application physique.

Une volonté commune consiste à masquer les principaux API d’accès général à Internet et n’autoriser que les API doit être appelée par les applications web en amont.  [Réseau de groupes de sécurité (NSGs)] [ NetworkSecurityGroups] peut servir de sous-réseaux contenant des environnements de Service d’application pour restreindre l’accès du public aux applications d’API.

Le diagramme ci-dessous présente une architecture d’exemple avec une application WebAPI basé déployée dans un environnement de Service d’application.  Trois instances d’app web distinctes, déployés dans les trois environnements de Service application distincte, effectuer des appels de back-end avec la même application WebAPI.

![Architecture conceptuelle][ConceptualArchitecture] 

Les signes plus verts indiquent que le groupe de sécurité réseau sur le sous-réseau contenant « apiase » permet les appels entrants à partir d’applications web en amont, en tant qu’appels bien à partir de lui-même.  Toutefois le même groupe de sécurité réseau refuse explicitement l’accès à grand trafic entrant à partir d’Internet. 

Le reste de cette rubrique vous guide à travers les étapes nécessaires à la configuration du groupe de sécurité réseau sur le sous-réseau contenant « apiase ».

## <a name="determining-the-network-behavior"></a>Déterminer le comportement du réseau ##
Pour connaître les règles de sécurité réseau sont nécessaires, vous devez déterminer quels clients réseau puisse accéder à l’environnement de Service d’application contenant l’application API, et les clients seront bloqués.

Depuis les [groupes de sécurité réseau (NSGs)] [ NetworkSecurityGroups] sont appliquées aux sous-réseaux et les environnements de Service d’application sont déployés dans les sous-réseaux, les règles contenues dans un NSG s’appliquent à **toutes les** applications s’exécutant sur un environnement de Service d’application.  À l’aide de l’architecture de l’exemple de cet article, une fois un groupe de sécurité du réseau est appliqué au sous-réseau contenant « apiase », toutes les applications s’exécutant sur un environnement de Service d’application « apiase » seront protégées par le même ensemble de règles de sécurité. 

- **Déterminer l’adresse IP sortante des appelants en amont :**  Quelle est l’adresse IP ou les adresses des appelants en amont ?  Ces adresses devront être explicitement autorisé à accéder du NSG.  Étant donné que les appels entre les environnements de Service d’application sont considérés comme des appels de « Internet », cela signifie que l’adresse IP sortante assignés à chacun des trois environnements amont application de Service doit être autorisé à accéder du NSG pour le sous-réseau « apiase ».   Pour plus d’informations sur la détermination de l’adresse IP sortante pour les applications en cours d’exécution dans un environnement de Service d’application, consultez l' [Architecture de réseau] [ NetworkArchitecture] article de présentation.
- **L’application d’API back-end devront s’appeler lui-même ?**  Un point parfois négligé et plus subtil est le scénario dans lequel l’application back-end doit s’appeler lui-même.  Si une application API de back-end sur un environnement de Service d’application doit s’appeler lui-même, cette opération est également considérée comme un appel « Internet ».  Dans l’architecture de l’exemple, cela nécessite autorisant l’accès à partir de l’adresse IP sortante de la « apiase » environnement de Service d’application ainsi.

## <a name="setting-up-the-network-security-group"></a>Configuration du groupe de sécurité du réseau ##
Une fois l’ensemble des adresses IP sortants sont connues, l’étape suivante consiste à créer un groupe de sécurité du réseau.  Les groupes de sécurité de réseau peuvent être créés pour le Gestionnaire de ressources à la fois en fonction des réseaux virtuels, ainsi que les réseaux virtuels classiques.  Les exemples ci-dessous illustrent la création et la configuration d’un NSG sur un réseau virtuel classique à l’aide de Powershell.

Pour l’architecture de l’exemple, les environnements sont trouvent dans Sud États-Unis, donc un NSG vide est créé dans cette région :

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

D’abord explicite Autoriser la règle est ajoutée pour l’infrastructure de gestion d’Azure, comme indiqué dans l’article sur [le trafic entrant] [ InboundTraffic] pour les environnements de Service d’application.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
Ensuite, deux règles sont ajoutées pour permettre les appels HTTP et HTTPS à partir de l’environnement de Service d’application en amont première (« fe1ase »).

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Effacez et recommencez pour la deuxième et la troisième en amont App environnements de Service (« fe2ase » et « fe3ase »).

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Enfin, accordez l’accès à l’adresse IP sortante de l’environnement de Service d’application de l’API principal afin qu’il puisse rappeler dans lui-même.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Aucune autre règle de sécurité de réseau ne doit être configurés dans la mesure où chaque NSG possède un ensemble de règles par défaut qui bloquent l’accès entrant à partir d’Internet par défaut.

Voici la liste complète des règles dans le groupe de sécurité du réseau.  Notez la façon dont la dernière règle, qui est mis en surbrillance, bloque l’accès entrant à partir de tous les appelants autres que celles qui ont explicitement reçu l’accès.

![Configuration du NSG][NSGConfiguration] 

La dernière étape consiste à appliquer le NSG au sous-réseau qui contient l’environnement de Service d’application « apiase ».  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Avec le NSG appliquée au sous-réseau, uniquement les trois en amont App Service environnements et que l’environnement de Service d’application contenant des API back-end, sont autorisés à appeler dans l’environnement de « apiase ».


## <a name="additional-links-and-information"></a>Informations et liens supplémentaires ##
Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Informations sur les [groupes de sécurité de réseau](../virtual-network/virtual-networks-nsg.md). 

Présentation [des adresses IP sortants] [ NetworkArchitecture] et environnements de Service d’application.

[Ports réseau] [ InboundTraffic] utilisé par les environnements de Service d’application.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[InboundTraffic]:  https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
