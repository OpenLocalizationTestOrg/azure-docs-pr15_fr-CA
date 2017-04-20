<properties
   pageTitle="L’équilibrage de charge de plusieurs VIP pour Azure | Microsoft Azure"
   description="Vue d’ensemble de plusieurs VIP de l’équilibreur de charge Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="chkuhtz"
   manager="narayan"
   editor=""
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="chkuhtz"
/>

# <a name="multiple-vips-for-azure-load-balancer"></a>L’équilibrage de charge de plusieurs VIP pour Azure

Azure équilibreur de charge vous permet de charger des services de solde sur plusieurs ports, plusieurs adresses IP ou les deux. Vous pouvez utiliser les définitions d’équilibreur de charge publics et internes à charger le flux de solde sur un ensemble de machines virtuelles.

Cet article décrit les principes fondamentaux de cette capacité, des concepts importants et des contraintes. Si vous souhaitez uniquement exposer les services d’une adresse IP, vous trouverez des instructions simplifiées pour [public](load-balancer-get-started-internet-portal.md) ou [internal](load-balancer-get-started-ilb-arm-portal.md) chargement les configurations d’équilibrage. Ajout de plusieurs VIP est incrémentielle pour une configuration d’adresse IP virtuelle unique. Utilisant les concepts de cet article, vous pouvez développer une configuration simplifiée, à tout moment.

Lorsque vous définissez un équilibreur de charge Azure, un site Web frontal et une serveur principal configuration sont connectés avec des règles. La sonde de santé référencée par la règle est utilisée pour déterminer comment les nouveaux flux sont envoyés à un nœud dans le pool de back-end. La partie frontale est défini par un IP virtuelle, qui est un 3-tuple constitué d’une adresse IP (public ou interne), un protocole de transport (UDP ou TCP) et un numéro de port. Un DIP est une adresse IP sur une carte de réseau virtuel Azure attaché à une machine virtuelle dans le pool de back-end.

Le tableau suivant contient quelques exemples de configurations frontal :

| VIP | Adresse IP | protocole | port |
|-----|------------|----------|------|
|1|65.52.0.1|TCP|80|
|2|65.52.0.1|TCP|_8080_|
|3|65.52.0.1|_UDP_|80|
|4|_65.52.0.2_|TCP|80|

Le tableau présente les quatre frontends différents. Frontends #1, #2 et #3 sont un VIP unique avec plusieurs règles. La même adresse IP est utilisée, mais le port ou le protocole est différent pour chaque site Web frontal. Frontends #1 et #4 sont un exemple de plusieurs VIP, où le même protocole frontal et le port sont réutilisées plusieurs VIP.

Azure équilibreur de charge permet de définir la règles d’équilibrage de la charge. Une règle déclare la façon dont une adresse et un port sur le site Web frontal est mappé à l’adresse de destination et le port sur le serveur principal. Ou non les ports back-end sont réutilisées entre les règles dépendant du type de la règle. Chaque type de règle a des exigences spécifiques qui peuvent affecter la conception de sonde et de configuration hôte. Il existe deux types de règles :

1. La règle par défaut avec aucune réutilisation de port back-end
2. La règle flottante de IP où les ports back-end sont réutilisés

Azure équilibreur de charge vous permet de combiner les deux types de règle sur la même configuration d’équilibrage de la charge. L’équilibreur de charge peut utiliser simultanément pour une machine virtuelle donnée, ou toute combinaison, tant que vous respectez les contraintes de la règle. Quel type de règle que vous choisissez dépend des exigences de votre application et de la complexité de la prise en charge de cette configuration. Vous devez évaluer les types de règles sont mieux adaptées à votre scénario.

Nous explorons davantage de ces scénarios en commençant par le comportement par défaut.

## <a name="rule-type-1-no-backend-port-reuse"></a>Type #1 de règle : aucun réutilisation de port back-end

![Illustration de la MultiVIP](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Dans ce scénario, les système frontal VIPs sont configurés comme suit :

| VIP | Adresse IP | protocole | port |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

Le DIP est la destination du flux entrant. Dans le pool back-end, chaque machine virtuelle expose le service désiré sur un port unique sur un DIP. Ce service est associé à la partie frontale une définition de règle.

Nous définissons deux règles :

| Règle | Site Web frontal de la carte | Pool de back-end |
|------|--------------|-----------------|
| 1 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Le mappage complet de l’équilibreur de charge Azure est désormais la suivante :

| Règle | Adresse IP du protocole VIP | protocole | port | Destination | port |
|------|----------------|----------|------|-----|------|
|![règle](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|Adresse IP de DIP|80|
|![règle](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|Adresse IP de DIP|81|

Chaque règle doit produire un flux avec une combinaison unique d’adresse IP de destination et le port de destination. En modifiant le port de destination du flux, plusieurs règles peuvent fournir des flux à la même DIP sur des ports différents.

Sondes de santé sont toujours dirigées vers le fondu d’un ordinateur virtuel. Vous devez aussi vous assurer vous que votre sonde reflète l’état de santé de la machine virtuelle.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Règle le type #2 : réutilisation de port back-end à l’aide d’IP de flottant

Équilibreur de charge Azure fournit la flexibilité nécessaire pour le port frontal de réutiliser plusieurs VIP, quel que soit le type de règle utilisée. En outre, certains scénarios d’application préfèrent ou requièrent le même port pour être utilisé par plusieurs instances de l’application sur un seul ordinateur virtuel du pool de back-end. Des exemples courants de réutilisation de port y inclure le clustering haute disponibilité, réseau appliances virtuelles et exposer plusieurs points de terminaison TLS sans nouveau cryptage.

Si vous souhaitez réutiliser le port back-end sur plusieurs règles, vous devez activer IP de flottant dans la définition de règle.

Flottante IP est une partie de ce que l'on appelle les renvoyer en tant que serveur Direct (DSR). DSR se compose de deux parties : une topologie de flux et une adresses IP schéma de configuration. Un niveau de la plate-forme, équilibrage de la charge Azure fonctionne toujours dans une topologie de flux DSR que si flottante de IP soit activé ou non. Cela signifie que la partie sortante d’un flux est toujours correctement réécrites pour flux directement à l’origine.

Le type de règle par défaut, Azure expose une schéma de configuration d’adresse IP pour une utilisation aisée d’équilibrage de charge traditionnelle. Activation IP de flottante modifie le schéma de configuration d’adresse IP à autoriser pour plus de souplesse, comme expliqué ci-dessous.

Le diagramme suivant illustre cette configuration :

![Illustration de la MultiVIP](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Dans ce scénario, chaque machine virtuelle dans le pool principal a trois interfaces réseau :

* DIP : une carte réseau virtuelle associé à la machine virtuelle (ressource de carte d’interface réseau d’Azure)
* VIP1 : une interface de bouclage au sein de l’invité du système d’exploitation qui est configuré avec l’adresse IP de VIP1
* VIP2 : une interface de bouclage au sein de l’invité du système d’exploitation qui est configuré avec l’adresse IP de VIP2

>[AZURE.IMPORTANT] La configuration des interfaces logiques est effectuée dans la système d’exploitation invité. Cette configuration n’est pas exécutée ou gérée par Azure. Sans cette configuration, les règles ne fonctionnent pas. Définitions de sonde de santé utilisent DIP de la machine virtuelle plutôt que l’adresse IP virtuelle logique. Par conséquent, votre service doit fournir des réponses de sonde sur un port DIP qui reflètent l’état du service offert sur l’adresse VIP logique.

Supposons que la même configuration de site Web frontal comme dans le scénario précédent :

| VIP | Adresse IP | protocole | port |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

Nous définissons deux règles :

| Règle | Site Web frontal de la carte | Pool de back-end |
|------|--------------|-----------------|
| 1 | ![règle](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 (dans VM1 et l’ordinateur virtuel 2) |
| 2 | ![règle](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 (dans VM1 et l’ordinateur virtuel 2) |

Le tableau suivant montre le mappage complet de l’équilibreur de charge :

| Règle | Adresse IP du protocole VIP | protocole | port | Destination | port |
|------|----------------|----------|------|-------------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|identique à l’adresse IP virtuelle (65.52.0.1)|identique à l’adresse IP virtuelle (80)|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|identique à l’adresse IP virtuelle (65.52.0.2)|identique à l’adresse IP virtuelle (80)|

La destination du flux entrant est l’adresse VIP sur l’interface de bouclage de l’ordinateur virtuel. Chaque règle doit produire un flux avec une combinaison unique d’adresse IP de destination et le port de destination. En modifiant l’adresse IP de destination du flux, la réutilisation de port est possible sur l’ordinateur virtuel même. Votre service est exposé à l’équilibreur de charge en le liant à l’adresse IP et le port de l’interface de bouclage respectifs de l’adresse IP virtuelle.

Notez que cet exemple ne modifie pas le port de destination. Même s’il s’agit d’un scénario d’IP de flottante, équilibrage de la charge Azure prend également en charge la définition d’une règle de réécrire le port de destination du back-end et le rendre différent du port de destination frontal.

Le type de règle flottante de IP constitue le fondement de plusieurs modèles de configuration d’équilibrage de la charge. Un exemple qui est actuellement disponible est la configuration [AlwaysOn de SQL avec plusieurs écouteurs](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) . Au fil du temps, nous allez documenter plusieurs de ces scénarios.

## <a name="limitations"></a>Limitations

* Plusieurs configurations VIP sont pris en charge uniquement avec les ordinateurs virtuels de IaaS.
* Avec la règle flottante de IP, votre application doit utiliser le DIP pour les flux sortants. Si votre application se lie à l’adresse VIP configurée sur l’interface de bouclage dans la système d’exploitation invité, puis SNAT n’est pas disponible pour réécrire le flux sortant et le flux échoue.
* Les adresses IP publiques ont un effet sur la facturation. Pour plus d’informations, consultez [tarification d’adresse IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Limites d’abonnement s’appliquent. Pour plus d’informations, reportez-vous à la section [limites de Service](../azure-subscription-service-limits.md#networking-limits) pour plus de détails.
