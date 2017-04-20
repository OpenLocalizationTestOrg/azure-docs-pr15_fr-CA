<properties
   pageTitle="Activation de la stratégie de SSL et SSL de bout en bout sur la passerelle d’Application | Microsoft Azure"
   description="Cette page fournit une vue d’ensemble de la passerelle d’Application prise en charge de SSL de bout en bout."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Activation de la stratégie de SSL et SSL de bout en bout sur la passerelle d’Application

## <a name="overview"></a>Vue d’ensemble

Passerelle d’application prend en charge l’arrêt de SSL au niveau de la passerelle, une fois que le trafic qui circule généralement non chiffré aux serveurs principaux. Ainsi, les serveurs web pour être unburdened à partir de chiffrement/déchiffrement coûteux. Cependant pour certains clients communication non cryptée aux serveurs principaux n’est pas une option acceptable. Il peut s’agir des exigences de sécurité et de conformité ou de l’application n’accepte que les connexion sécurisée. Pour des applications de la passerelle d’application prend désormais en charge le cryptage SSL de bout en bout.

Fin à fin SSL permet de transmettre de façon sécurisée des données sensibles sur le back-end crypté toujours profiter des avantages des fonctionnalités d’équilibrage de charge de couche 7 quelle passerelle d’application offre, par exemple l’affinité de cookie, basée sur l’URL de routage, la prise en charge pour le routage basé sur des sites ou possibilité d’injecter X-transférés-* les en-têtes.

Lorsqu’il est configuré avec le mode de communication SSL de bout en bout, passerelle d’application met fin aux sessions SSL utilisateur au niveau de la passerelle et le décrypte le trafic des utilisateurs. Il applique ensuite les règles configurées pour sélectionner une instance de pool de serveur principal approprié pour acheminer le trafic vers. Passerelle d’application démarre une nouvelle connexion SSL au serveur principal, puis crypte de nouveau les données à l’aide du certificat de clé publique du serveur principal avant de transmettre la demande sur le serveur principal. SSL de bout en bout est activé en définissant le paramètre de protocole dans BackendHTTPSetting pour Https, qui est ensuite appliquée à un pool de back-end. Chaque serveur principal du pool back-end avec SSL de bout en bout activé doit être configuré avec un certificat pour sécuriser les communications.

![scénario de bout en bout ssl][1]

Dans cet exemple, les requêtes à l’aide de TLS1.2 sont routées vers les serveurs principaux dans Pool1 à l’aide de SSL de bout en bout.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Bout en bout de SSL et création de listes autorisées de certificats

Passerelle d’application communique uniquement avec les instances de back-end connus qui ont autorisés à leur certificat avec la passerelle d’application. Pour activer la création de listes autorisées de certificats, vous devez télécharger la clé publique de certificats de serveur backend pour la passerelle d’application (et non le certificat racine). Seules les connexions vers les serveurs principaux connus et la liste d’autorisation sont ensuite autorisées. Les résultats restants et les serveurs principaux dans une erreur de passerelle. Les certificats auto-signés sont à des fins de test uniquement et pas recommandés pour les charges de travail. Ces certificats doivent également être autorisés avec la passerelle d’application comme décrit dans les étapes précédentes avant de pouvoir être utilisés.

## <a name="application-gateway-ssl-policy"></a>Stratégie d’application passerelle SSL

Passerelle d’application prend en charge l’utilisateur configurable SSL stratégies de négociation, qui permettent aux clients de mieux contrôler les connexions SSL au niveau de la passerelle d’application.

1. SSL 2.0 et 3.0, désactivée par défaut pour toutes les passerelles d’Application. Ils ne sont pas configurables du tout.
2. Donne de définition de stratégie SSL vous option pour désactiver un des protocoles suivants 3 - TLSv1\_0, TLSv1\_1, TLSv1\_2.
3. Si aucune stratégie SSL n’est défini par les trois (TLSv1\_0, TLSv1\_1, TLSv1_2) sont activés.

## <a name="next-steps"></a>Étapes suivantes

Après formation sur fin à fin SSL et de stratégie SSL, accédez à [Activer SSL de bout en bout sur la passerelle d’application](application-gateway-end-to-end-ssl-powershell.md) créer une passerelle d’application avec la possibilité d’envoyer le trafic vers les serveurs principaux sous forme cryptée.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png