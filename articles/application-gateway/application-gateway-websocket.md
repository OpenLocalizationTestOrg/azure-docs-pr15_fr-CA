<properties
   pageTitle="Prise en charge de la passerelle WebSocket application | Microsoft Azure"
   description="Cette page fournit une vue d’ensemble de la prise en charge de WebSocket de passerelle d’Application."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-websocket-support"></a>Prise en charge de la passerelle WebSocket application

Passerelle d’application fournit la prise en charge native pour WebSocket dans toutes les tailles de passerelle. Il n’existe aucun paramètre configurable par l’utilisateur d’activer ou de désactiver la prise en charge de WebSocket. Vous pouvez continuer à l’aide d’un HTTPListener standard sur port 80 ou 443 pour recevoir le trafic WebSocket. Le trafic WebSocket est alors dirigé vers le serveur de back-end activé WebSocket à l’aide du pool principal approprié tel que spécifié dans les règles d’application passerelle. Protocole WebSocket normalisée en [RFC6455](https://tools.ietf.org/html/rfc6455) permet une communication bidirectionnelle entre le serveur et le client sur une connexion TCP longue. Cette fonctionnalité permet une communication plus interactive entre le serveur web et le client, qui peut ainsi être bidirectionnel sans nécessiter d’interrogation comme requis dans mises en œuvre basé sur HTTP.  WebSocket ont faible surcharge Contrairement à HTTP et pouvez réutiliser la même connexion TCP pour plusieurs demande/réponses, ce qui entraîne une utilisation plus efficace des ressources. Protocoles de WebSocket sont conçus pour fonctionner sur les ports HTTP traditionnels de 80 et 443.

Le serveur principal doit répondre aux sondes passerelle d’application, qui sont décrites dans la section [vue d’ensemble de sonde de la santé](application-gateway-probe-overview.md) . Sondes de santé application passerelle HTTP/HTTPS uniquement, cela implique que chaque serveur principal doit répondre à des sondes HTTP pour une application passerelle à acheminer le trafic WebSocket vers le serveur.

## <a name="listener-configuration-element"></a>Élément de configuration de port d’écoute

HTTPListener existant peut servir à la prise en charge de WebSocket. Voici un extrait de l’élément de HttpListeners à partir d’un exemple de fichier de modèle. Vous devez à la fois HTTP et HTTPS des écouteurs pour la prise en charge de WebSocket et sécuriser le trafic WebSocket. De la même façon que vous pouvez utiliser le [portail](application-gateway-create-gateway-portal.md) ou [PowerShell](application-gateway-create-gateway-arm.md) pour créer une passerelle d’application avec les ports d’écoute sur le port 80/443, prendre en charge le trafic WebSocket.


    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                    }
                },
                {
                    "name": "appGatewayHttpListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                    }
                }
            ],

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuration des règles de routage, BackendHttpSetting et BackendAddressPool

BackendAddressPool doit être utilisé pour définir un pool back-end avec des serveurs WebSocket activé. BackendHttpSetting doit être défini avec back-end port 80 ou 443. Propriétés pour l’affinité basée sur les cookies et requestTimeouts ne sont pas pertinentes pour le trafic WebSocket. Aucune modification n’est requise dans la règle de routage. Règle de routage 'Base' doit continuer à être utilisés pour lier l’écouteur approprié pour le pool d’adresses back-end correspondants. 

    "requestRoutingRules": [{
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }, {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }

        }
    }]

## <a name="websocket-enabled-backend"></a>Back-end de WebSocket activé

La principale doit être un serveur web HTTP/HTTPS sur la configuration du port (généralement 80/443) pour WebSocket fonctionne. Cette exigence est parce que le protocole WebSocket nécessite la négociation initiale à protocole WebSocket sous la forme d’un champ d’en-tête HTTP avec la mise à niveau.

    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

Une autre raison pour Ceci est que sonde de santé application passerelle back-end prend en charge uniquement les protocoles HTTP/HTTPS. Si le serveur principal ne répond pas à HTTP/HTTPS sondes, il devrait être retiré de pool du back-end et aucune demande, y compris les demandes de WebSocket, susceptibles d’atteindre cette back-end.

## <a name="next-steps"></a>Étapes suivantes

Après étude de prise en charge de WebSocket, consultez [créer une passerelle d’application](application-gateway-create-gateway.md) mise en route avec une application web de WebSocket activé.
