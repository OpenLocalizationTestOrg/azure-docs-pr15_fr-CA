

<properties
   pageTitle="Contrôle de vue d’ensemble pour la passerelle d’Application Azure d’état | Microsoft Azure"
   description="En savoir plus sur les fonctionnalités de contrôle de passerelle d’Application Azure"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="application-gateway-health-monitoring-overview"></a>Vue d’ensemble du contrôle de la santé application passerelle

Azure Application passerelle par défaut surveille l’état de santé de toutes les ressources dans son pool de back-end et supprime automatiquement toute ressource considérée comme incorrecte du pool. Passerelle d’application continue à surveiller les instances défectueux et les ajoute dans le pool principal sain, une fois qu’ils deviennent disponibles et répondent aux sondes de santé. Passerelle d’application envoie que l’état de santé des sondes avec le même port que celui qui est défini dans les paramètres HTTP de back-end. Cela garantit que la sonde est le test le même port que les clients vous pourriez utiliser pour se connecter sur le serveur principal.

![exemple d’application passerelle sonde][1]

Outre l’utilisation de surveillance de sonde l’état par défaut, vous pouvez également personnaliser la sonde de santé pour répondre aux exigences de votre application. Dans cet article, à la fois par défaut et les sondes de santé personnalisés sont couverts.

## <a name="default-health-probe"></a>Sonde d’intégrité par défaut

Une passerelle d’application configure automatiquement une sonde de santé par défaut lorsque vous ne définissez pas n’importe quelle configuration de sonde personnalisé. Le comportement d’analyse fonctionne en créant une requête HTTP vers les adresses IP configurées pour le pool principal. Pour les sondes par défaut si les paramètres http de back-end sont configurés pour le protocole HTTPS, la sonde utilise https ainsi pour tester l’état de santé des serveurs principaux.

Par exemple : vous configurez votre passerelle d’application pour utiliser des serveurs principaux A, B et C pour recevoir le trafic de réseau HTTP sur le port 80. La surveillance de l’état par défaut teste les trois serveurs toutes les 30 secondes pour une réponse HTTP en bon état. Une réponse HTTP en bon état a un [code d’état](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 et 399.

En cas d’échec de la vérification de la sonde par défaut pour le serveur A, la passerelle d’application retire son pool de back-end et l’arrêt du trafic de réseau pour ce serveur. La sonde par défaut reste à vérifier pour serveur un toutes les 30 secondes. Lorsque le serveur A répond correctement à une demande à partir d’une sonde d’intégrité par défaut, il est ajouté à la précédente comme sain dans le pool principal, et démarre de trafic circulant à nouveau au serveur.

### <a name="default-health-probe-settings"></a>Paramètres de sonde d’intégrité par défaut

|Propriété de sonde | Valeur | Description|
|---|---|---|
| URL de la sonde| http://127.0.0.1 :\<port\>/ | Chemin d’accès de l’URL |
| Intervalle | 30 | Sonde d’intervalle en secondes |
| Délai d’attente  | 30 | Sonde de délai d’expiration en secondes |
| Seuil de mauvais fonctionnement | 3 | Nombre de tentatives de la sonde. Le serveur principal est marqué vers le bas après que le nombre d’échecs consécutifs de sonde atteint le seuil de fonctionnement anormal. |

> [AZURE.NOTE] Le port sera toujours le même port que les paramètres HTTP de back-end.

La sonde par défaut se contente de regarder http://127.0.0.1 :\<port\> pour déterminer l’état de santé. Si vous devez configurer la sonde de santé pour atteindre une URL personnalisée ou modifier d’autres paramètres, vous devez utiliser les sondes personnalisé comme décrit dans les étapes suivantes.

## <a name="custom-health-probe"></a>Sonde d’état personnalisé

Sondes personnalisés vous permettent d’avoir un contrôle plus granulaire sur la surveillance de la santé. Lors de l’utilisation des sondes personnalisées, vous pouvez configurer l’intervalle de la sonde, l’URL et chemin d’accès pour tester et nombre de réponses ayant échoué pour accepter avant de marquer l’instance principaux pool comme étant non fonctionnelle.

### <a name="custom-health-probe-settings"></a>Paramètres de sonde d’état personnalisé

Le tableau suivant fournit les définitions des propriétés d’une sonde d’état personnalisé.

|Propriété de sonde| Description|
|---|---|
| Nom | Nom de la sonde. Ce nom est utilisé pour faire référence à la sonde dans Paramètres HTTP de back-end. |
| Protocole | Protocole utilisé pour envoyer la sonde. La sonde utilise le protocole défini dans les paramètres HTTP de back-end |
| Hôte |  Nom d’hôte pour envoyer la sonde. Applicable uniquement lorsque plusieurs sites est configuré sur Application Gateway, sinon utilisez '127.0.0.1'. Cela est différent du nom d’hôte de machine virtuelle. |
| Chemin d’accès | Chemin d’accès relatif de la sonde. Le chemin d’accès valide commence à partir de '/'. |
| Intervalle | Sonde d’intervalle en secondes. Il s’agit de l’intervalle de temps entre deux sondes consécutives.|
| Délai d’attente | Sonde de délai d’attente en secondes. La sonde est marquée comme ayant échoué si une réponse valide n’est pas reçue dans ce délai. |
| Seuil de mauvais fonctionnement | Nombre de tentatives de la sonde. Le serveur principal est marqué vers le bas après que le nombre d’échecs consécutifs de sonde atteint le seuil de fonctionnement anormal. |

> [AZURE.IMPORTANT] Si la passerelle d’Application est configuré pour un seul site, par défaut, l’hôte nom doit être spécifié comme '127.0.0.1', à moins que dans le cas contraire configuré dans la sonde personnalisé.
Pour référence, une sonde personnalisé est envoyée à \<protocole de\>://\<hôte\>:\<port\>\<chemin d’accès\>. Le port utilisé sera le même port, tel que défini dans les paramètres HTTP de back-end.

## <a name="next-steps"></a>Étapes suivantes

Après étude de surveillance de l’état Application Gateway, vous pouvez configurer une [sonde d’état personnalisé](application-gateway-create-probe-portal.md) dans le portail Azure ou une [sonde d’état personnalisé](application-gateway-create-probe-ps.md) à l’aide de PowerShell et le modèle de déploiement du Gestionnaire de ressources Azure.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png