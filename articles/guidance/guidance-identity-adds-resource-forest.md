<properties
   pageTitle="Référence d’Architecture Azure - IaaS : Création d’une forêt de ressources Active Directory dans Azure | Microsoft Azure"
   description="Procédure de création d’un domaine Active Directory approuvé dans Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="creating-a-active-directory-directory-services-adds-resource-forest-in-azure"></a>Création d’une forêt de ressources des Services de répertoire Active Directory (ADDS) dans Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit comment créer un domaine Active Directory dans Azure qui est distinct de, mais il est approuvé par les domaines de votre forêt sur site.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Une organisation qui exécute Active Directory (AD) sur site peut être une forêt comprenant de nombreux domaines différents. Par exemple, vous pouvez créer des domaines individuels pour divers départements ou suborganizations, ou les nouveaux domaines ont été ajoutés à la suite d’acquisition ou d’une fusion d’autres organisations. Vous pouvez utiliser des domaines pour assurer l’isolation entre les domaines fonctionnels qui doivent être maintenus séparés, peut-être pour des raisons de sécurité, mais vous pouvez partager des informations entre des domaines en établissant des relations d’approbation.

Une organisation qui utilise des domaines séparés peut tirer parti d’Azure en déplaçant un ou plusieurs de ces domaines vers le nuage. Vous pouvez également, une organisation peut souhaiter garder toutes les ressources de cloud logiquement distinctes des ces détenues sur place et stocker des informations sur les ressources du cloud dans leur propre répertoire, dans un domaine aussi détenu dans le nuage.

Vous pouvez implémenter Active Directory dans Azure de différentes façons, comme décrit dans les articles [Extension de Active Directory vers Azure] [ extending-ad-to-azure] et [Mise en œuvre d’Azure Active Directory][implementing-aad]. Ce document se concentre sur un scénario spécifique : création d’un domaine dans le cloud qui est distincte de tous les domaines détenues sur site, mais qui peuvent avoir une relation d’approbation avec les domaines locaux. 

Scénarios d’utilisation classiques de cette architecture sont les suivantes :

- Maintien de la séparation de sécurité pour les objets et les identités détenues dans le nuage.

- Migration des domaines individuels en local dans le nuage.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le schéma suivant met en évidence les éléments importants de cette architecture (*Cliquez pour agrandir*). Pour plus d’informations sur les éléments grisé, lire la [mise en œuvre d’une architecture de réseau sécurisée hybride dans Azure] [ implementing-a-secure-hybrid-network-architecture] et [mise en œuvre d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Réseau de locaux.** Le réseau local contient sa propre forêt Active Directory et des domaines.

- **Serveurs Active Directory.** Il s’agit de contrôleurs de domaine mise en œuvre des services d’annuaire (AD DS) en cours d’exécution en tant qu’ordinateurs virtuels dans le nuage. Ces serveurs hébergent une forêt contenant un ou plusieurs domaines, distincts de ces situé sur site.

- **Relation d’approbation unidirectionnelle.** Dans le diagramme montre une approbation unidirectionnelle du domaine dans le nuage pour le domaine local. Cette relation permet aux utilisateurs de locaux d’accéder aux ressources du domaine dans le nuage, mais pas l’inverse. Il s’agit d’un cas courant. Toutefois, vous pouvez créer une approbation bidirectionnelle si les utilisateurs de nuage nécessitent également accès à des ressources sur site.

- **Sous-réseau de Active Directory.** Les serveurs de service d’annuaire Active Directory sont hébergées dans un sous-réseau distinct. Règles NSG protéger les serveurs de service d’annuaire Active Directory et peuvent fournir un pare-feu contre le trafic de sources inconnues.

- **Sous-réseau de niveau Web**, **sous-réseau de niveau entreprise**et **sous-réseau de couche de données**. Ces sous-réseaux héberge les serveurs et les composants qui s’exécutent les applications dans le nuage. Pour plus d’informations, consultez [Exécution de machines virtuelles d’une architecture à N niveaux sur Azure][running-VMs-for-an-N-tier-architecture-on-Azure]. Les ressources et les ordinateurs virtuels de ce sous-réseau sont contenus dans le domaine du nuage.

- **Passerelle azure**. La passerelle Azure fournit une connexion entre le réseau local et de le VNet d’Azure. Cela peut être une [connexion VPN] [ azure-vpn-gateway] ou [ExpressRoute d’Azure][azure-expressroute]. Pour plus d’informations, consultez [implémentation d’une architecture de réseau sécurisée hybride dans Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recommandations

Cette section fournit une liste de recommandations basées sur les composants essentiels nécessaires à l’implémentation de l’architecture de base. Ces recommandations couvrent :

- Ajoute les paramètres, et

- Configuration de la relation d’approbation.

Vous pouvez avoir des exigences supplémentaires ou différentes de celles décrites ici. Vous pouvez utiliser les éléments de cette section comme point de départ de considérer comment personnaliser l’architecture de votre système.

### <a name="adds-recommendations"></a>Recommandations ajoute

Pour obtenir des recommandations spécifiques sur l’implémentation d’Active Directory dans le nuage, reportez-vous au document [d’Extension d’Active Directory vers Azure][extending-ad-to-azure]. L’article [instructions de déploiement de Windows Server Active Directory Azure machines virtuelles en fonctionnement] [ ad-azure-guidelines] contient des informations détaillées supplémentaires.

### <a name="trust-recommendations"></a>Approbation des recommandations

Les domaines locaux sont contenues dans une forêt différente de domaines dans le nuage. Pour activer l’authentification des utilisateurs locaux dans le nuage, les domaines dans le nuage doivent approuver le domaine d’ouverture de session dans la forêt locale. De même, si le nuage offre un domaine d’ouverture de session pour les utilisateurs externes, il peut être nécessaire pour la forêt locale approuver le domaine du nuage.

Vous pouvez établir des approbations au niveau de la forêt en [créant des approbations de forêt][creating-forest-trusts], ou au niveau du domaine en [créant des approbations externes][creating-external-trusts]. Une niveau d’approbation de forêt crée une relation entre tous les domaines de deux forêts. Une approbation de niveau domaine externe crée uniquement une relation entre deux domaines spécifiés. Vous devez uniquement créer des approbations de niveau de domaine externe entre les domaines dans des forêts différentes.

Relations d’approbation peuvent être unidirectionnelles (unidirectionnelle) ou bidirectionnelle (bidirectionnelle) :

- Une approbation à sens unique permet aux utilisateurs d’un domaine ou une forêt (appelé de la forêt ou le domaine *entrant* ) pour accéder aux ressources détenues dans un autre (le domaine *sortants* ou la forêt). 

- Une approbation bidirectionnelle permet aux utilisateurs de la forêt ou de domaine d’accéder aux ressources détenues dans l’autre.

Le tableau suivant résume les configurations de confiance pour des scénarios simples :

| Scénario | Sur les sites de confiance | Approbation de nuage |
|----------|-------------------|-------------|
| Les utilisateurs doivent accéder aux ressources dans le nuage, sur site, mais pas inversement | Entrante | Unidirectionnelle, sortant |
| Les utilisateurs dans le nuage ont besoin d’accéder à des ressources situées sur les sites, mais pas inversement | Unidirectionnelle, sortant | Entrante |
| Les utilisateurs dans le nuage et sur site doit accéder aux ressources détenues dans le nuage et sur site | Bidirectionnelle, entrants et sortants | Bidirectionnelle, entrants et sortants |

## <a name="security-considerations"></a>Considérations sur la sécurité

Approbations de niveau de forêt sont transitives. Si vous établissez une approbation de niveau de forêt entre une forêt sur site et un dans le nuage, cette approbation est étendue à d’autres domaines de nouveau créés dans une forêt. Si vous utilisez des domaines pour fournir une séparation pour des raisons de sécurité, envisagez de créer des approbations au niveau du domaine uniquement. Approbations de niveau domaine sont non transitives.

Annonces spécifiques en matière de sécurité, reportez-vous à la section *Considérations sur la sécurité* dans [l’Extension de Active Directory vers Azure][extending-ad-to-azure].

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Implémenter au moins deux contrôleurs de domaine pour chaque domaine. Cela permet une réplication automatique entre les serveurs. Créer une disponibilité pour agissant en tant que serveurs de publicités chaque domaine de la gestion des ordinateurs virtuels. Vérifiez qu’il existe au moins deux serveurs dans le jeu. 

Envisagez également de désigner un ou plusieurs serveurs dans chaque domaine en tant que [maître d’opérations en attente] [ standby-operations-masters] en cas d’échec de la connexion à un serveur ayant un rôle FSMO.

## <a name="scalability-considerations"></a>Évolutivité

Active Directory est automatiquement évolutive pour les contrôleurs de domaine qui font partie du même domaine. Les demandes sont distribuées sur tous les contrôleurs d’un domaine. Vous pouvez ajouter un autre contrôleur de domaine, et il se synchronise automatiquement avec le domaine. Ne configurez pas un équilibreur de charge séparé pour diriger le trafic vers les contrôleurs dans le domaine. Assurez-vous que tous les contrôleurs de domaine disposent de suffisamment de ressources mémoire et le stockage pour gérer la base de données du domaine. Que le contrôleur de domaine tous les ordinateurs virtuels de la même taille.

## <a name="management-and-monitoring-considerations"></a>Gestion et surveillance des considérations relatives à la

Pour plus d’informations sur la gestion et la surveillance des considérations, consultez les sections équivalentes dans [Extension de Active Directory vers Azure][extending-ad-to-azure]. 

Pour plus d’informations, consultez [Contrôle de Active Directory][monitoring_ad]. Vous pouvez installer des outils tels que [Microsoft Systems Center] [ microsoft_systems_center] sur un serveur d’analyse dans le sous-réseau de gestion pour aider à effectuer ces tâches. 

## <a name="solution-components"></a>Composants de la solution

Un exemple de script de solution, [Déployer-ReferenceArchitecture.ps1][solution-script], n’est disponible que vous pouvez utiliser pour mettre en œuvre de l’architecture qui suit les recommandations décrites dans cet article. Ce script utilise le Gestionnaire de ressources Azure modèles. Les modèles sont disponibles sous la forme d’un ensemble de blocs de construction fondamentaux, dont chacun exécute une action spécifique comme la création d’un VNet ou configurer un NSG. L’objectif du script est d’orchestrer le déploiement du modèle.

Les modèles sont paramétrées, avec les paramètres conservés dans des fichiers distincts de JSON. Vous pouvez modifier les paramètres dans ces fichiers pour configurer le déploiement pour répondre à vos besoins. Vous n’avez pas besoin de modifier les modèles eux-mêmes. Vous ne devez pas modifier les schémas des objets dans les fichiers de paramètres.

Lorsque vous modifiez les modèles, créer des objets qui suivent les conventions d’attribution de noms décrites dans [Recommandé des Conventions d’affectation de noms pour les ressources d’Azure][naming-conventions].

L’exemple de solution crée et configure un environnement dans le cloud qui implémente un domaine nommé *treyresearch.com*. Cet environnement comprend la passerelle VPN de sous-réseau et les serveurs, DMZ, couche web, couche d’entreprise et composants de couche d’accès aux données, ajoute et la couche de gestion. L’exemple de solution inclut également une configuration facultative pour la création d’un environnement simulé sur site avec son propre domaine, *contoso.com*. La solution inclut des scripts qui permettent d’établir une relation d’approbation entre ces domaines qui permet aux utilisateurs de locaux accéder aux objets dans le domaine de la *treyresearch.com* dans le nuage.

Les sections suivantes décrivent les éléments de la locale et configurations en nuage.

### <a name="on-premises-components"></a>Composants locaux

>[AZURE.NOTE] Ces composants ne sont pas le principal objectif de l’architecture décrite dans ce document et sont fournis pour vous permettre de tester en toute sécurité, de l’environnement en nuage au lieu d’utiliser un environnement de production réel. Pour cette raison, cette section résume uniquement les fichiers de paramètres de clé. Vous pouvez modifier des paramètres tels que les adresses IP ou les tailles des ordinateurs virtuels, mais il est recommandé de laisser la plupart des autres paramètres inchangés.

Cet environnement compose d’une forêt Active Directory du domaine *contoso.com* . Le domaine contient deux serveurs ajoute les adresses IP 192.168.0.4 et 192.168.0.5. Ces deux serveurs exécutent également le service DNS. Le compte d’administrateur local sur les deux ordinateurs virtuels est appelé `testuser` avec le mot de passe `AweS0me@PW`. En outre, la configuration définit une passerelle VPN pour se connecter à la VNet dans le nuage. Vous pouvez modifier la configuration en modifiant les fichiers JSON suivants qui se trouve dans les [**paramètres/locales**] [ on-premises-folder] dossier :

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Ce fichier définit l’espace d’adressage de réseau pour l’environnement local.

- ** [desmachines virtuelles-adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Ce fichier contient la configuration des VM sur site ajoute les services d’hébergement. Par défaut, deux VM *Standard-DS3-v2* est créés.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** et ** [connection.parameters.json][on-premises-connection-parameters]**. Ces fichiers contiennent les paramètres pour la connexion VPN à la passerelle VPN d’Azure dans le nuage, y compris la clé partagée à utiliser pour protéger le trafic de traverser la passerelle.

Les fichiers restants dans le dossier contiennent les informations de configuration utilisées pour créer le domaine local (*contoso.com*) à l’aide de cette infrastructure. Vous permet de les pour installer ajouter, configurer le système DNS et créez la forêt locale.

La solution utilise également le script suivant, nommé [entrant-trust.ps1][incoming-trust], qui s’exécute sur un ordinateur dans le domaine local :

```Powershell
# Run the following powershell script in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)

$TrustedDomainName = "contoso.com"
#$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

$TrustingDomainName = "treyresearch.com"
$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustingDomainDnsIpAddresses
$ForwardDomainName = $TrustingDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Ce script ajoute les adresses IP des serveurs AD DS dans le nuage (voir la section suivante) pour le service DNS local, puis utilise le [netdom] [ netdom] commande pour créer une approbation unidirectionnelle entrante à partir du domaine dans le nuage (*treyresearch.com*).

### <a name="cloud-components"></a>Composants du cloud

Ces composants constituent le cœur de cette architecture. Ils le programme d’installation de l’infrastructure pour le domaine *treyresearch.com* et créer les relations d’approbation avec le domaine *contoso.com* sur site. Les [**paramètres/azure**] [ azure-folder] dossier contient les fichiers de paramètres suivants pour la configuration de ces composants :

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Ce fichier définit la structure de le VNet pour les ordinateurs virtuels et d’autres composants dans le nuage. Il inclut des paramètres, tels que le nom, espace d’adressage, des sous-réseaux et les adresses des serveurs DNS requis. Les adresses DNS indiqués dans cette référence d’exemple, les adresses IP des serveurs DNS locaux, ainsi que le serveur DNS d’Azure par défaut. Modifier ces adresses pour faire référence à la configuration de votre propre DNS si vous n’utilisez pas l’exemple d’environnement local :
    
    ```json
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg",
        "addressPrefixes": [
          "10.0.0.0/16"
        ],
        "subnets": [
          {
            "name": "dmz-private-in",
            "addressPrefix": "10.0.0.0/27"
          },
          {
            "name": "dmz-private-out",
            "addressPrefix": "10.0.0.32/27"
          },
          {
            "name": "dmz-public-in",
            "addressPrefix": "10.0.0.64/27"
          },
          {
            "name": "dmz-public-out",
            "addressPrefix": "10.0.0.96/27"
          },
          {
            "name": "mgmt",
            "addressPrefix": "10.0.0.128/25"
          },
          {
            "name": "GatewaySubnet",
            "addressPrefix": "10.0.255.224/27"
          },
          {
            "name": "web",
            "addressPrefix": "10.0.1.0/24"
          },
          {
            "name": "biz",
            "addressPrefix": "10.0.2.0/24"
          },
          {
            "name": "data",
            "addressPrefix": "10.0.3.0/24"
          },
          {
            "name": "adds",
            "addressPrefix": "10.0.4.0/27"
          }
        ],
        "dnsServers": [
          "10.0.4.4",
          "10.0.4.5",
          "168.63.129.16"
        ]
      }
    }
    ```

- ** [desmachines virtuelles-adds.parameters.json ] [ virtualmachines-adds-parameters] **. Ce fichier configure les ordinateurs virtuels en cours d’exécution ajoute dans le nuage. La configuration se compose de deux VM. Modifier le nom d’utilisateur admin et le mot de passe dans le `virtualMachineSettings` section et vous pouvez éventuellement modifier la taille de la mémoire virtuelle pour répondre aux besoins du domaine :

    Pour plus d’informations, consultez [Extension d’Active Directory vers Azure][extending-ad-to-azure].
    
    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-ad",
        "computerNamePrefix": "aad",
        "size": "Standard_DS3_v2",
        "osType": "Windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "adds",
            "privateIPAllocationMethod": "Static",
            "startingIPAddress": "10.0.4.4",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 127,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [
        ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-as"
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ```

- ** [Ajouter-ajoute-domaine-controller.parameters.json][add-adds-domain-controller-parameters]**. Ce fichier contient les paramètres pour la création du domaine *treyresearch.com* couvrant les serveurs ajoute. Il utilise des extensions personnalisées qui établissent le domaine et lui ajouter les serveurs à ajouter. Sauf si vous créez des serveurs supplémentaires ajoute (auquel cas vous devez les ajouter à la `vms` tableau), modifier leur nom par défaut ou pour créer un domaine sous un nom différent, vous n’avez pas besoin de modifier ce fichier.

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Ce fichier contient les paramètres utilisés pour créer la passerelle VPN d’Azure dans le nuage, utilisé pour la connexion au réseau local. Vous devez modifier le `sharedKey` valeur dans le `connectionsSettings` section correspond à celle du périphérique VPN sur site. Pour plus d’informations, consultez [implémentation d’une Architecture de réseau hybride avec Azure et sur site VPN][hybrid-azure-on-prem-vpn].

- ** [zone dmz-private.parameters.json] [ dmz-private-parameters] ** et ** [dmz-public.parameters.json ] [ dmz-public-parameters] **. Ces fichiers configurent (public) entrant et sortant (privé) des ordinateurs virtuels qui composent la zone DMZ, protégeant les serveurs dans le nuage. Pour plus d’informations sur ces éléments et leur configuration, consultez [implémentation d’un réseau de périmètre entre Internet et les Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer-web.parameters.json][loadBalancer-web-parameters]**, ** [loadBalancer-biz.parameters.json][loadBalancer-biz-parameters]**, et ** [loadBalancer-data.parameters.json][loadBalancer-data-parameters]**. Ces fichiers de paramètres contiennent les spécifications de la machine virtuelle pour les niveaux d’accès web, métier et données et de configurer les équilibreurs de charge pour chaque niveau. Ce sont les ordinateurs virtuels qui hébergent les applications web et les bases de données et exécuter les charges de travail des professionnels de l’organisation. Les ordinateurs virtuels dans la couche web sont ajoutés au domaine dans le nuage en utilisant les paramètres spécifiés dans le ** [web-vm-domaine-join.parameters.json] [ web-vm-domain-join-parameters] ** fichier.

    Chaque fichier contient deux ensembles de paramètres de configuration. Le `virtualMachineSettings` section définit les ordinateurs virtuels qui hébergent le service ADFS dans le nuage. Par défaut, le script crée deux de ces ordinateurs virtuels dans le même jeu de disponibilité. Les fragments suivants illustrent les parties pertinentes du fichier *loadBalancer-web.parameters.json* :

    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-web",
        "computerNamePrefix": "web",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "web",
            "privateIPAllocationMethod": "Dynamic",
            "isPrimary": "true",
            "enableIPForwarding": false,
            "dnsServers": [ ]
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-web-vm-as"
        }
      }
    },
    ...
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ````
    Vous pouvez modifier la taille et le nombre d’ordinateurs virtuels dans chaque niveau en fonction de vos besoins.

    Le `loadBalancerSettings` section fournit une description de l’équilibreur de charge pour ces ordinateurs virtuels. L’équilibreur de charge transmet le trafic qui s’affiche sur le port 80 (HTTP) et le port 443 (HTTPS) pour un ou l’autre des ordinateurs virtuels. 

    >[AZURE.NOTE] La règle pour le port 80 utilise une connexion TCP et non HTTP. C’est parce que l’installation d’IIS sur la couche web est configurée pour prendre en charge de l’authentification Windows uniquement. L’authentification anonyme est désactivée. Tentative de *commande ping* le port 80 sur une connexion HTTP échoue avec une erreur 401 (non autorisé), considérant que simplement à l’aide d’une connexion TCP détecte si le port est actif :

    ```json
    "loadBalancerSettings": {
      "value": {
        "name": "ra-adtrust-web-lb",
        "frontendIPConfigurations": [
          {
            "name": "ra-adtrust-web-lb-fe",
            "loadBalancerType": "internal",
            "internalLoadBalancerSettings": {
              "privateIPAddress": "10.0.1.254",
              "subnetName": "web"
            }
          }
        ],
        "backendPools": [
          {
            "name": "ra-adtrust-web-lb-bep",
            "nicIndex": 0
          }
        ],
        "loadBalancingRules": [
          {
            "name": "http-rule",
            "frontendPort": 80,
            "backendPort": 80,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "http-probe",
            "enableFloatingIP": false
          },
          {
            "name": "https-rule",
            "frontendPort": 443,
            "backendPort": 443,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "https-probe",
            "enableFloatingIP": false
          }
        ],
        "probes": [
          {
            "name": "http-probe",
            "port": 80,
            "protocol": "Tcp",
            "requestPath": null
          },
          {
            "name": "https-probe",
            "port": 443,
            "protocol": "Tcp",
            "requestPath": null
          }
        ],
        "inboundNatRules": [ ]
      }
    }
    ```

- ** [desmachines virtuelles-mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Ce fichier contient la configuration de la gestion/boîte de déviation de machines virtuelles. Il n’est possible d’obtenir d’ouverture et de l’accès administratif aux ordinateurs virtuels dans le web, business et les niveaux de données à partir de la zone passer. Par défaut, le script crée un seul *Standard_DS1_v2* VM, mais vous pouvez modifier ce fichier pour créer les ordinateurs virtuels de plus grandes ou plus si la charge de travail de gestion est susceptible d’être important.

La configuration utilise également le [sortant-trust.ps1] [ outgoing-trust] script ci-dessous pour créer une approbation sortante avec le domaine *contoso.com* :

```powershell
# prerequiste: 
# You need to first run incoming-trust.ps1 in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)
# Then,
# Run the following powershell script in ra-adtrust-ad-vm1 (ip 10.0.4.4)

$TrustedDomainName = "contoso.com"
$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

#$TrustingDomainName = "treyresearch.com"
#$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustedDomainDnsIpAddresses
$ForwardDomainName = $TrustedDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

#netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Ce script est similaire au script *entrant-trust.ps1* décrit plus haut. Il ajoute les adresses IP des locaux sur serveurs AD DS au service DNS local, puis utilise [netdom] [ netdom] commande pour créer la relation d’approbation sortante.

## <a name="solution-deployment"></a>Déploiement de la solution

La solution suppose que les conditions préalables suivantes :

- Vous avez un abonnement Azure existant dans lequel vous pouvez créer des groupes de ressources.

- Avoir téléchargé et installé la version la plus récente de Powershell d’Azure. Consultez [ici] [ azure-powershell-download] pour obtenir des instructions.

Pour exécuter le script qui déploie la solution :

1. Déplacer vers un dossier sur votre ordinateur local et créez les sous-dossiers suivants :

    - Scripts

    - Scripts/paramètres

    - Paramètres/scripts/locales

    - Paramètres/scripts/Azure

2. Télécharger le [Déploiement-ReferenceArchitecture.ps1] [ solution-script] le fichier dans le dossier Scripts

3. Télécharger le contenu de la [paramètres/locales] [ on-premises-folder] dossier dans le dossier Scripts/paramètres/locales :

4. Télécharger le contenu de [paramètres/azure] [ azure-folder] dossier dans le dossier Scripts/paramètres/Azure.

5. Modifiez le fichier de déploiement-ReferenceArchitecture.ps1 dans le dossier Scripts et modifiez les lignes suivantes pour spécifier les groupes de ressources qui doivent être créés ou utilisés pour contenir les ressources créées par le script :

    ```powershell
    # Azure Onpremise Deployments
    $onpremiseNetworkResourceGroupName = "ra-adtrust-onpremise-rg"

    # Azure ADDS Deployments
    $azureNetworkResourceGroupName = "ra-adtrust-network-rg"
    $workloadResourceGroupName = "ra-adtrust-workload-rg"
    $securityResourceGroupName = "ra-adtrust-security-rg"
    $addsResourceGroupName = "ra-adtrust-adds-rg"
    ```

6. Modifier les fichiers de paramètres dans les Scripts/paramètres/locales et les dossiers de Scripts/les paramètres/Azure. Mettre à jour les références de groupe de ressources dans ces fichiers pour faire correspondre les noms des groupes de ressources affectées aux variables dans le fichier ReferenceArchitecture.ps1 à la déployer. Le tableau suivant répertorie les fichiers de paramètres de référence quel groupe de ressources. Les groupes de ressources *ra-adfs-charge de travail-rg*, *ra-adfs-sécurité-rg*, *ra-adfs-ajoute-rg*, *ra-adfs-adfs-rg*et *ra-adfs-proxy-rg* sont uniquement utilisées dans le script PowerShell et ne se produisent pas dans les fichiers de paramètres.

  	|Groupe de ressources|Fichiers de paramètres|
  	|--------------|--------------|
  	|RA-adtrust-locales-rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RA-adtrust-réseau-rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*deux occurrences*)

    En outre, définir la configuration pour les locaux et cloud composants, comme décrit dans les [Composants de la Solution] [ solution-components] section.

7. Ouvrez une fenêtre PowerShell d’Azure, déplacer vers le dossier de Scripts et exécutez la commande suivante :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Remplacer `<subscription id>` avec votre ID d’abonnement Azure.

    Pour `<location>`, spécifier une région Azure, tel que `eastus` ou `westus`.

    Le `<mode>` paramètre peut avoir une des valeurs suivantes :

    - `Onpremise`, pour créer l’environnement simulé sur site.

    - `Infrastructure`, pour créer l’infrastructure de VNet et de sauter la boîte dans le nuage.

    - `CreateVpn`, pour générer la passerelle Azure réseau virtuel et le connecter au réseau local.

    - `AzureADDS`, pour créer les ordinateurs virtuels agissant en tant que serveurs d’ajoute, déployer Active Directory à ces ordinateurs virtuels et créer le domaine dans le nuage.

    - `WebTier`, qui crée le site web de hiérarchiser les machines virtuelles et d’équilibrage de la charge.

    - `Prepare`, qui effectue toutes les tâches précédentes. **C’est l’option recommandée si vous créez un nouveau déploiement et vous n’avez pas une infrastructure sur site.**

    - `Workload`Pour créer la couche métier et données VMs et équilibreurs de charge. Ces ordinateurs virtuels ne sont pas inclus dans le cadre de la `Prepare` option.

    >[AZURE.NOTE] Si vous utilisez la `Prepare` option, le script prend plusieurs heures.

8.  Si vous utilisez l’exemple de configuration sur site :

    1. Se connecter à la zone passer (*ra-adtrust-gestion-vm1* dans le groupe de ressources de *ra-adtrust-sécurité-rg* ). Connectez-vous en tant qu' *UtilisateurTest* avec mot de passe *AweS0me@PW*.

    2.  Dans la zone passer d’ouvrir une session RDP sur l’ordinateur virtuel première dans le domaine *contoso.com* (domaine local). Cet ordinateur virtuel est l’adresse IP 192.168.0.4. Le nom d’utilisateur est *contoso\testuser* avec le mot de passe *AweS0me@PW*.

    3. Télécharger le [entrant-trust.ps1] [ incoming-trust] de script et exécutez-le pour créer l’approbation entrante à partir du domaine *treyresearch.com* .

9. Si vous utilisez votre propre infrastructure sur site :

    1. Télécharger le [entrant-trust.ps1] [ incoming-trust] script.

    2. Modifiez le script, puis remplacez la valeur de la `$TrustedDomainName` variable avec le nom de votre domaine.

    3. Exécutez le script.

10. Dans la zone saut de se connecter à la première virtuelle dans le domaine *treyresearch.com* (domaine dans le nuage). Cet ordinateur virtuel est l’adresse IP 10.0.4.4. Le nom d’utilisateur est *treyresearch\testuser* avec le mot de passe *AweS0me@PW*.

11. Télécharger le [sortant-trust.ps1] [ outgoing-trust] de script et exécutez-le pour créer l’approbation entrante à partir du domaine *treyresearch.com* . Si vous utilisez vos propres ordinateurs locaux, puis modifiez le script premier. Définir le `$TrustedDomainName` variable pour le nom de votre domaine local et de spécifier les adresses IP des serveurs de ce domaine dans les services AD DS la `$TrustedDomainDnsIpAddresses` variable.

12. Sur un ordinateur local, exécutez les étapes décrites dans l’article [vérifier une approbation] [ verify-a-trust] pour déterminer si la relation d’approbation a été correctement configurée entre les domaines *contoso.com* et *treyresearch.com* . Vous devrez peut-être attendre quelques minutes après les étapes précédentes avant l’approbation peut être validée.

Les étapes facultatives suivantes indiquent comment déterminer si l’approbation du domaine fonctionne comme prévu. Ces étapes requièrent que vous avez accès à un ordinateur de développement avec Visual Studio installé.

1.  Dans la fenêtre PowerShell d’Azure, exécutez la commande suivante pour créer la couche web, si vous le n'avez pas configuré précédemment (à l’aide de la `Prepare` option) :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> WebTier
    ```

    Cette commande crée le niveau web et ajoute les ordinateurs virtuels au domaine *treyresearch.com* .

2. À l’aide de Visual Studio sur l’ordinateur de développement, de créer une application Web d’ASP.NET nommée *TreyResearchWebApp*. Utilisez le point 4.5.2 de.NET Framework.

3. Sélectionnez le modèle *MVC* et modifiez l’authentification *Windows*. Ne créez pas une application de Service dans le nuage.

3. Générez et exécutez l’application pour tester l’authentification. Vérifiez que votre nom d’utilisateur Windows actuel s’affiche dans la barre de menus en haut de la page, vers la droite.

4. Fermez Internet Explorer.

5. Dans la fenêtre de *L’Explorateur de solutions* , cliquez sur le projet TreyResearchWebApp et cliquez sur *Publier*.

6. Dans la fenêtre *Publier le site Web* , cliquez sur *personnalisé*. Créer un profil personnalisé nommé *TreyResearchWebApp*.

7. Sur la page de *connexion* , définissez la *méthode de publication* pour le *Système de fichiers* et spécifier un dossier nommé *TreyResearchWebApp*, situé dans un emplacement approprié sur votre ordinateur de développement.

8. Dans la page *paramètres* , affectez la *Configuration* *Release*.

9. Dans la page *d’Aperçu* , cliquez sur *Publier*.

10. Connectez-vous à chaque ordinateur virtuel dans la couche web à son tour (via la zone passer) et effectuer les tâches suivantes. Les adresses IP du site web niveau VMs sont 10.0.1.4 et 10.0.1.5. Le nom d’utilisateur pour les deux ordinateurs virtuels est *treyresearch\testuser* avec le mot de passe *AweS0me@PW*:

    1. Copiez le dossier *TreyResearchWebApp* et son contenu à partir de l’ordinateur de développement vers le dossier *C:\inetpub* .

    2. La console Gestionnaire des Services Internet (IIS), accédez au *Site Web de par* sur l’ordinateur.

    3. Dans le volet *Actions* , cliquez sur *Paramètres de base*et modifier le chemin d’accès physique du site web de *%SystemDrive%\inetpub\TreyResearchWebApp*.

    4. Dans le volet *d’Affichage des fonctionnalités* , double-cliquez sur *l’authentification*. Vérifiez que *L’authentification Windows* est activée et que *L’authentification anonyme* est désactivée.

11. à partir d’un ordinateur local, ouvrez Internet Explorer et accédez au site web à http://10.0.1.254 (il s’agit de l’adresse de l’équilibreur de charge de couche web).

12. Dans la boîte de dialogue *Sécurité de Windows* , entrez les informations d’identification d’un utilisateur dans le domaine local. Spécifiez un nom d’utilisateur qui n’existe pas dans le domaine *treyresearch* . Si vous utilisez l’environnement simulé sur site créez d’abord un utilisateur dans le domaine *contoso* et spécifier les informations d’identification de l’utilisateur.

13. Lorsque la page d’accueil s’affiche, vérifiez que le nom d’utilisateur et le domaine approprié apparaissent dans la barre de menus en haut de la page, vers la droite.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les meilleures pratiques pour [l’extension de votre domaine local ajoute Azure][adds-extend-domain]

- Découvrez les meilleures pratiques pour la [Création d’une infrastructure ADFS] [ adfs] dans Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[implementing-aad]: ./guidance-identity-aad.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[running-VMs-for-an-N-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[naming-conventions]: ./guidance-naming-conventions.md
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[netdom]: https://technet.microsoft.com/library/cc835085.aspx
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adds-trust/parameters/onpremise/connection.parameters.json
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/incoming-trust.ps1
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/outgoing-trust.ps1
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-adds.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/add-adds-domain-controller.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-public.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-mgmt.parameters.json
[web-vm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/web-vm-domain-join.parameters.json
[solution-components]: [#solution_components]
[0]: ./media/guidance-identity-aad-resource-forest/figure1.png "Architecture de réseau hybride avec des domaines Active Directory différents sécurisée"