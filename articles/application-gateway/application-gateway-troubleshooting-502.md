<properties
   pageTitle="Résoudre les erreurs d’Application passerelle passerelle incorrecte (502) | Microsoft Azure"
   description="Découvrez comment résoudre les erreurs d’Application passerelle 502"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Dépannage des erreurs de passerelle incorrecte dans Application Gateway

## <a name="overview"></a>Vue d’ensemble

Après avoir configuré une passerelle d’Application Azure, l’une des erreurs que les utilisateurs peuvent rencontrer est « Erreur serveur : 502 - serveur Web a reçu une réponse non valide en tant que passerelle ou proxy ». Cela peut se produire en raison des raisons suivantes :

- Pool de back-end de passerelle d’Application Azure n’est pas configuré ou est vide.
- Les ordinateurs virtuels ou les instances dans l’échelle de la machine virtuelle définie sont en bon état.
- Ordinateurs virtuels ou les instances de l’échelle de la machine virtuelle définie back-end ne répondent pas à la sonde de santé par défaut.
- Configuration non valide ou incorrecte de sondes d’état personnalisé.
- Demande de délai d’attente ou de problèmes de connectivité avec les demandes de l’utilisateur.

## <a name="empty-backendaddresspool"></a>BackendAddressPool vide

### <a name="cause"></a>Cause

Si la passerelle d’Application a ordinateurs virtuels ni VM échelle définie est configuré dans le pool d’adresses de back-end, elle ne peut pas acheminer toutes les demandes client et lève une erreur de passerelle incorrecte.

### <a name="solution"></a>Solution

Vérifiez que le pool d’adresses de back-end n’est pas vide. Ceci est possible via PowerShell, CLI ou portail.

    Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

La sortie de l’applet de commande précédent doit contenir le pool d’adresses de back-end non vide. Voici un exemple où deux pools sont renvoyées qui sont configurés avec des adresses IP ou le nom de domaine complet pour le back-end de machines virtuelles. L’état de mise en service de la BackendAddressPool doit être « a réussi ».
    
    BackendAddressPoolsText: 
            [{
                "BackendAddresses": [{
                    "ipAddress": "10.0.0.10",
                    "ipAddress": "10.0.0.11"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool01",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
            }, {
                "BackendAddresses": [{
                    "Fqdn": "xyx.cloudapp.net",
                    "Fqdn": "abc.cloudapp.net"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool02",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
            }]


## <a name="unhealthy-instances-in-backendaddresspool"></a>Instances défectueux dans les BackendAddressPool

### <a name="cause"></a>Cause

Si toutes les instances de BackendAddressPool sont défectueux, puis Application passerelle n’auraient pas les principaux à la demande d’utilisateur itinéraire à. Cela pourrait également être le cas lorsque les instances de back-end sont en bon état mais ne disposez pas de l’application requise est déployée.

### <a name="solution"></a>Solution

Assurez-vous que les instances sont en bonne santé et que l’application est correctement configurée. Vérifiez si les instances de back-end sont en mesure de répondre à une commande ping à partir de l’autre dans le même VNet. Si configuré avec un point de terminaison public, assurez-vous qu’une demande de navigateur à l’application web est accessible.

## <a name="problems-with-default-health-probe"></a>Problèmes avec sonde de santé par défaut

### <a name="cause"></a>Cause

502 erreurs peuvent également être des indicateurs fréquentes que la sonde d’intégrité par défaut n’est pas en mesure d’atteindre les ordinateurs virtuels du back-end. Lorsqu’une instance de passerelle d’Application est configurée, il configure automatiquement une sonde d’intégrité par défaut pour chaque BackendAddressPool à l’aide des propriétés de la BackendHttpSetting. Aucune entrée d’utilisateur n’est requis pour définir cette sonde. Plus précisément, lorsqu’une règle d’équilibrage de charge est configurée, une association est faite entre BackendHttpSetting et BackendAddressPool. Une sonde par défaut est configurée pour chacune de ces associations et Application passerelle initie une connexion à cocher santé périodique chaque instance dans le BackendAddressPool sur le port spécifié dans l’élément BackendHttpSetting. Tableau suivant répertorie les valeurs associées à la sonde de santé par défaut.


|Propriété de sonde | Valeur | Description|
|---|---|---|
| URL de la sonde| http://127.0.0.1/ | Chemin d’accès de l’URL |
| Intervalle | 30 | Sonde d’intervalle en secondes |
| Délai d’attente  | 30 | Sonde de délai d’expiration en secondes |
| Seuil de mauvais fonctionnement | 3 | Nombre de tentatives de la sonde. Le serveur principal est marqué vers le bas après que le nombre d’échecs consécutifs de sonde atteint le seuil de fonctionnement anormal. |

### <a name="solution"></a>Solution

- Vérifiez qu’un site par défaut est configuré et qu’il est à l’écoute sur 127.0.0.1.
- Si BackendHttpSetting indique un port autre que 80, le site par défaut doit être configuré pour écouter sur ce port.
- L’appel à http://127.0.0.1:port doit renvoyer un code de résultat HTTP 200. Cette valeur doit être retournée dans le délai de 30 secondes.
- Assurez-vous que le port configuré est ouvert et qu’il n’y a aucune des règles de pare-feu ou des groupes de sécurité réseau Azure, qui bloquent le trafic entrant ou sortant sur le port configuré.
- Si Azure VM classique ou le Service en nuage est utilisé avec le nom de domaine complet ou l’adresse IP publique, vérifiez que le [point de terminaison](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) correspondant est ouvert.
- Si la machine virtuelle est configurée via le Gestionnaire de ressources Azure et se trouve en dehors de le VNet où la passerelle d’Application est déployé, le [Groupe de sécurité de réseau](../virtual-network/virtual-networks-nsg.md) doit être configuré pour autoriser l’accès à la voie souhaitée.


## <a name="problems-with-custom-health-probe"></a>Problèmes avec une sonde d’état personnalisé

### <a name="cause"></a>Cause

Sondes de santé personnalisées permettent une flexibilité supplémentaire pour tester le comportement par défaut. Lors de l’utilisation des sondes personnalisés, les utilisateurs peuvent configurer l’intervalle de la sonde, l’URL et chemin d’accès pour tester et nombre de réponses ayant échoué pour accepter avant de marquer l’instance principaux pool comme étant non fonctionnelle. Les propriétés suivantes sont ajoutées.

|Propriété de sonde| Description|
|---|---|
| Nom | Nom de la sonde. Ce nom est utilisé pour faire référence à la sonde dans Paramètres HTTP de back-end. |
| Protocole | Protocole utilisé pour envoyer la sonde. La sonde utilise le protocole défini dans les paramètres HTTP de back-end |
| Hôte |  Nom d’hôte pour envoyer la sonde. Ne s’applique que lorsque plusieurs sites est configuré sur la passerelle de l’Application. Cela est différent du nom d’hôte de machine virtuelle.  |
| Chemin d’accès | Chemin d’accès relatif de la sonde. Le chemin d’accès valide commence à partir de '/'. La sonde est envoyée à \<protocole de\>://\<hôte\>:\<port\>\<chemin d’accès\> |
| Intervalle | Sonde d’intervalle en secondes. Il s’agit de l’intervalle de temps entre deux sondes consécutives.|
| Délai d’attente | Sonde de délai d’attente en secondes. La sonde est marquée comme ayant échoué si une réponse valide n’est pas reçue dans ce délai. |
| Seuil de mauvais fonctionnement | Nombre de tentatives de la sonde. Le serveur principal est marqué vers le bas après que le nombre d’échecs consécutifs de sonde atteint le seuil de fonctionnement anormal. |


### <a name="solution"></a>Solution

Valider que la sonde de santé personnalisé est correctement configuré en tant que le tableau précédent. Outre les étapes de dépannage précédents, également vérifier les éléments suivants :

- Assurez-vous que la sonde est correctement spécifiée selon le [guide](application-gateway-create-probe-ps.md).
- Si la passerelle d’Application est configuré pour un seul site, par défaut, l’hôte nom doit être spécifié comme '127.0.0.1', à moins que dans le cas contraire configuré dans la sonde personnalisé.
- Assurer un appel à http://\<hôte\>:\<port\>\<chemin d’accès\> retourne un code de résultat HTTP 200.
- Assurez-vous que l’intervalle de délai d’attente et de UnhealtyThreshold sont dans des limites acceptables.

## <a name="request-time-out"></a>Délai de demande

### <a name="cause"></a>Cause

Lors de la réception de la demande d’un utilisateur, Application passerelle applique les règles configurées à la demande et l’achemine vers une instance de pool du back-end. Il attend un intervalle configurable de temps d’une réponse à partir de l’instance de back-end. Par défaut, cet intervalle est de **30 secondes**. Si la passerelle d’Application ne reçoit pas une réponse à partir de l’application back-end dans cet intervalle, demande de l’utilisateur peut voir une erreur 502.

### <a name="solution"></a>Solution

Passerelle d’application permet aux utilisateurs de configurer ce paramètre via BackendHttpSetting, qui peut être appliqué ensuite aux pools différents. Différents pools de back-end peuvent avoir différent BackendHttpSetting et demande donc autre délai d’attente configuré.

    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne résolvent pas le problème, ouvrez une [prise en charge de ticket](https://azure.microsoft.com/support/options/).
