<properties
    pageTitle="Comment configurer des connexions VPN dans Gestion de l’API Azure"
    description="Apprenez à configurer une connexion VPN dans Azure API de services de gestion et accès web par son intermédiaire."
    services="api-management"
    documentationCenter=""
    authors="antonba"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="antonba"/>

# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Comment configurer des connexions VPN dans Gestion de l’API Azure

Prise en charge VPN de la direction de l’API vous permet de vous connecter à la passerelle API gestion à un réseau virtuel d’Azure (classique). Gestion des API les clients peuvent ainsi se connecter de manière sécurisée à leurs services web d’arrière-guichet qui sont locaux ou inaccessibles à l’internet public.

>[AZURE.NOTE] Gestion des API Azure fonctionne avec VNETs classiques. Pour plus d’informations sur la création d’un VNET classique, voir [Création d’un réseau virtuel (classique) via le portail d’Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Pour plus d’informations sur la connexion classiques VNETs à VNETS de ARM, consultez [VNets classique de connexion à VNets de nouveau](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="enable-vpn"> </a>Les connexions VPN d’activer

>Connectivité VPN est disponible uniquement dans les couches **Premium** et **développeur** . Pour basculer vers elle, ouvrez votre service de gestion de l’API dans le [Portail classique d’Azure][] , puis l’onglet **échelle** . Sous la section **Général** , sélectionnez le niveau de prime, puis cliquez sur Enregistrer.

Pour activer la connectivité VPN, ouvrez votre service de gestion de l’API dans le [Portail classique d’Azure][] et basculez vers l’onglet **configurer** . 

Sous la section VPN, utiliser **connexion VPN** **sur**.

![Onglet de l’instance de gestion de l’API de configuration][api-management-setup-vpn-configure]

Vous verrez maintenant une liste de toutes les régions où votre service de gestion de l’API est mis en service.

Sélectionnez un VPN et un sous-réseau pour chaque région. La liste des réseaux privés virtuels est remplie en fonction des réseaux virtuels disponibles dans votre abonnement Azure qui sont configurés dans la zone que vous configurez.

![Sélectionnez VPN][api-management-setup-vpn-select]

Cliquez sur **Enregistrer** en bas de l’écran. Vous ne serez pas en mesure d’effectuer d’autres opérations sur le service de gestion de l’API à partir du portail classique Azure pendant qu’il est mis à jour. Le composant service gateway restent disponible et des appels de runtime ne devraient pas être affectées.

Notez que l’adresse de la passerelle changent chaque fois que VPN est activé ou désactivé.

## <a name="connect-vpn"> </a>Se connecter à un service web derrière un VPN

Une fois que votre service de gestion de l’API est connecté au VPN, l’accès à des services web dans le réseau virtuel est pas différente de celle de l’accès à des services publics. Tapez simplement l’adresse locale ou le nom d’hôte (si un serveur DNS a été configuré pour le réseau virtuel Azure) de votre service web dans le champ **URL du service Web** lors de la création d’une nouvelle API ou en modifier une existante.

![Ajouter des API à partir de VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Ports requis pour la prise en charge API gestion VPN

Lorsqu’une instance de service de gestion de l’API est hébergée dans un VNET, les ports dans le tableau suivant sont utilisées. Si ces ports sont bloqués, le service peut ne pas fonctionne correctement. Un ou plusieurs de ces ports bloqués est le problème le plus courant une configuration incorrecte lors de l’utilisation des API de gestion avec un VNET.

| Port (s)                      | Direction        | Protocole de transport | Objectif                                                          | Source / Destination              |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443                      | Trafic entrant          | TCP                | Communication client à la gestion de l’API                           | INTERNET / VIRTUAL_NETWORK        |
| 80,443                       | Sortant         | TCP                | Dépendance de gestion API sur le stockage Azure et Bus des services Azure | VIRTUAL_NETWORK / INTERNET        |
| 1433                         | Sortant         | TCP                | Dépendances de gestion de l’API sur SQL                               | VIRTUAL_NETWORK / INTERNET        |
| 9350, 9351, 9352, 9353, 9354 | Sortant         | TCP                | Dépendances de gestion de l’API sur le Bus de Service                       | VIRTUAL_NETWORK / INTERNET        |
| 5671                         | Sortant         | AMQP               | Dépendance de gestion d’API pour le journal d’événements stratégie de concentrateur            | VIRTUAL_NETWORK / INTERNET        |
| 6381, 6382, 6383             | Entrant et sortant | UDP                | Dépendances de gestion de l’API Redis de cache                       | VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445                          | Sortant         | TCP                | Dépendance de gestion API sur le partage de fichiers Azure pour GIT            | VIRTUAL_NETWORK / INTERNET        |

## <a name="custom-dns"> </a>La configuration de serveur DNS de personnalisé

API de gestion dépend d’un certain nombre de services Azure. Lorsqu’une instance de service de gestion de l’API est hébergée dans un VNET, où un serveur DNS personnalisé est utilisé, il doit être en mesure de résoudre les noms d’hôtes de ces services Azure. Veuillez suivre [ces](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) instructions sur la configuration de DNS personnalisée.  

## <a name="related-content"> </a>Contenu associé


* [Créer un réseau virtuel avec une connexion VPN de site à l’aide du portail classique Azure][]
* [Comment utiliser l’inspecteur de l’API de suivi des appels dans Azure API de gestion][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure Portal classique]: https://manage.windowsazure.com/

[Créer un réseau virtuel avec une connexion VPN de site à l’aide du portail classique Azure]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Comment utiliser l’inspecteur de l’API de suivi des appels dans Azure API de gestion]: api-management-howto-api-inspector.md
