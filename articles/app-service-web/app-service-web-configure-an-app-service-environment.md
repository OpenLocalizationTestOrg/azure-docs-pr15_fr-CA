<properties
    pageTitle="Comment faire pour configurer un environnement de Service d’application | Microsoft Azure"
    description="Configuration, gestion et surveillance des environnements de Service d’application"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>


# <a name="configuring-an-app-service-environment"></a>Configuration d’un environnement de Service d’application #

## <a name="overview"></a>Vue d’ensemble ##

À un niveau élevé, un environnement de Service d’application Azure se compose de plusieurs composants principaux :

- Les ressources de calcul qui sont exécutent dans l’environnement de Service d’application de service hébergé
- Stockage
- Une base de données
- Un réseau virtuel de Classic(V1) ou de la ressource Manager(V2) Azure (VNet) 
- Un sous-réseau avec le service environnement de Service d’application hébergé en cours d’exécution dans celui-ci

### <a name="compute-resources"></a>Ressources de calcul

Vous utilisez les ressources de calcul pour les pools de quatre ressources.  Chaque environnement de Service d’application (ASE) possède un ensemble de ports front-end et trois pools de travail possible. Vous n’avez pas besoin d’utiliser tous les pools du travailleur trois--si vous le souhaitez, vous pouvez simplement utiliser un ou deux.

Les hôtes dans les pools de ressources (ports front-end et travailleurs) ne sont pas directement accessibles aux locataires. Vous ne peut pas utiliser le protocole RDP (Remote Desktop) pour s’y connecter, modifier leur mise en service ou agir en tant qu’administrateur sur les.

Vous pouvez définir la taille et la quantité de pool de ressources. Dans un équipement de direction auxiliaire, vous disposez de quatre options de taille, étiquetés P1 à P4. Pour plus d’informations sur les tailles et leur prix, consultez [tarification Service d’application](../app-service/app-service-value-prop-what-is.md).
Modification de la quantité ou la taille, la mise à une échelle est appelée.  Une seule échelle peut être en cours à la fois.

**Frontaux**: les serveurs frontaux est les points de terminaison HTTP/HTTPS pour vos applications qui sont trouvent dans votre ASE. Vous n’exécutez pas les charges de travail dans les ports front-end.

- ASE démarre avec deux P2s, ce qui est suffisant pour les charges de travail de développement/test et charges de travail de bas niveau. Nous recommandons fortement P3s modérée pour les charges de travail lourdes.
- Modéré pour les charges de travail lourdes, nous recommandons que vous disposez d’au moins quatre P3s pour vous assurer que frontaux suffisante en cas de maintenance planifiée en cours d’exécution. Les activités de maintenance programmée permet de récupérer un front-end à la fois. Cela réduit l’ensemble des capacités front-end disponibles au cours des opérations de maintenance.
- Impossible d’ajouter instantanément une nouvelle instance de front-end. Elles peuvent prendre entre 2 à 3 heures à disposition.
- Pour la nouvelle échelle de réglage fin, vous devez contrôler le pourcentage de l’UC, de pourcentage de la mémoire et de mesures de demandes actives pour le pool frontal. Si les pourcentages de processeur ou de mémoire supérieure à 70 % lors de l’exécution de P3s, ajoutez plus de ports front-end. Si la valeur de demandes actives en moyenne à 15 000 à 20 000 demandes par front-end, vous devez également ajouter plus de ports front-end. L’objectif est de conserver les pourcentages de processeur et de mémoire ci-dessous 70 %, et de demandes actives moyennes à au-dessous de 15 000 demandes par avant fin lorsque vous exécutez P3s.  

**Travailleurs**: les travailleurs sont où exécutent vos applications. Lorsque vous faites évoluer vos plans de Service de l’application, qui utilise des travailleurs dans le pool de travail associé.

- Vous ne pouvez pas ajouter instantanément des travailleurs. Elles peut durer de 2 à 3 heures à disposition, quel que soit le nombre sont ajoutés.
- Mise à l’échelle de la taille d’une ressource de calcul pour n’importe quel pool prendront 2 à 3 heures par domaine de mise à jour. Il existe des 20 domaines de mise à jour dans un équipement de direction auxiliaire. Si l’échelle de la taille du calcul d’un pool de travail avec 10 instances et il pourrait prendre entre 20 et 30 heures de travail.
- Si vous modifiez la taille, les ressources de calcul qui sont utilisées dans un pool de travail, vous provoquerez démarre à froid des applications qui sont exécutent dans ce pool de travail.

Pour modifier la taille de ressources de calcul d’un pool de travail qui n’exécute pas les applications, la plus rapide consiste à :

- Mettre à l’échelle vers le bas à 0, le nombre d’instance. Il prendra environ 30 minutes pour libérer vos instances.
- Sélectionnez la nouvelle taille de compute et le nombre d’instances. À partir de là, il prendra entre 2 à 3 heures.

Si vos applications nécessitent une plus grande taille de ressources de calcul, vous ne pouvez pas tirer parti de recommandations précédentes. Au lieu de modifier la taille du pool de travail qui héberge ces applications, vous pouvez remplir un autre pool de travail avec les travailleurs de la taille souhaitée et déplacer vos applications vers ce pool.

- Créer des instances supplémentaires de la taille de calcul nécessaires dans un autre pool de travail. Cela va prendre de 2 à 3 heures.
- Réaffecter vos plans de Service d’application qui hébergent les applications qui ont besoin d’une plus grande taille pour le pool de travail nouvellement configuré. Il s’agit d’une opération rapide qui devrait prendre moins d’une minute pour terminer.  
- Mettre à l’échelle vers le bas le premier pool de travail si vous n’avez plus besoin des instances non utilisés. Cette opération prend environ 30 minutes.

**AutoScaling**: un des outils qui peuvent vous aider à gérer votre consommation de ressources de calcul est autoscaling. Vous pouvez utiliser autoscaling de front-end ou des pools de travailleur. Vous pouvez faire comme l’augmentation de vos instances de n’importe quel type de pool le matin et réduire le soir. Ou, par exemple, vous pouvez ajouter des instances lorsque le nombre des travailleurs qui sont disponibles dans un pool de travail est inférieur à un certain seuil.

Si vous souhaitez définir des règles d’échelle autour des métriques de pool de ressources de calcul, puis Gardez à l’esprit le temps nécessitant une mise en service. Pour plus d’informations sur autoscaling, environnements de Service d’application, voir [Comment faire pour configurer l’échelle dans un environnement de Service d’application][ASEAutoscale].

### <a name="storage"></a>Stockage

Chaque équipement de direction auxiliaire est configuré avec une capacité de stockage de 500 Go. Cet espace est utilisé sur toutes les applications dans l’équipement de direction auxiliaire. Cet espace de stockage est une partie de l’équipement de direction auxiliaire et actuellement, ne peut être basculé pour utiliser votre espace de stockage. Si vous effectuez des ajustements à votre sécurité ou de routage réseau virtuel, vous devez toujours autoriser l’accès au stockage Azure ou l’équipement de direction auxiliaire ne peut pas fonctionner.

### <a name="database"></a>Base de données

La base de données conserve les informations qui définissent l’environnement, ainsi que les détails sur les applications qui sont exécutent au sein de celui-ci. Il s’agit une partie de l’abonnement détenue par Azure trop. Il n’est pas quelque chose que vous avez une possibilité de manipuler directement. Si vous effectuez des ajustements à votre sécurité ou de routage réseau virtuel, vous devez toujours autoriser l’accès à SQL Azure,--ou l’équipement de direction auxiliaire ne peut pas fonctionner.

### <a name="network"></a>Réseau

Le VNet est utilisé avec votre équipement de direction auxiliaire peut être effectuées lorsque vous avez créé l’équipement de direction auxiliaire ou que vous avez apportées à l’avance. Lorsque vous créez le sous-réseau lors de la création de l’équipement de direction auxiliaire, il force l’équipement de direction auxiliaire dans le même groupe de ressources que le réseau virtuel. Si vous devez le groupe de ressources utilisé par votre équipement de direction auxiliaire à être différent de celui de votre VNet, vous devez créer votre équipement de direction auxiliaire à l’aide d’un modèle de gestionnaire de ressources.

Il existe quelques restrictions sur le réseau virtuel qui est utilisé pour un équipement de direction auxiliaire :
 
- Le réseau virtuel doit être un réseau virtuel.
- Il faut un sous-réseau avec 8 ou plusieurs adresses où l’équipement de direction auxiliaire est déployé.
- Après qu’un sous-réseau est utilisé pour héberger un équipement de direction auxiliaire, la plage d’adresses du sous-réseau ne peut pas être modifiée. Pour cette raison, nous recommandons que le sous-réseau contienne au moins 64 adresses pour s’adapter à la croissance future de tout équipement auxiliaire de direction.
- Il peut être rien d’autre dans le sous-réseau mais l’équipement de direction auxiliaire.

Contrairement au service hébergé qui contient de l’équipement de direction auxiliaire, le [réseau virtuel] [ virtualnetwork] et le sous-réseau sont sous le contrôle de l’utilisateur.  Vous pouvez administrer votre réseau virtuel par l’intermédiaire de l’interface utilisateur de réseau virtuel ou PowerShell.  Un équipement de direction auxiliaire peut être déployé dans un classique ou VNet le Gestionnaire de ressources.  Le portail et les expériences d’API sont légèrement différentes entre classique et Gestionnaire de ressources VNets, mais l’expérience de l’équipement auxiliaire de direction est la même.

Le VNet est utilisé pour héberger un équipement auxiliaire de direction pouvez utiliser soit des adresses privées RFC1918 IP ou il peut utiliser des adresses IP publiques.  Si vous souhaitez utiliser une plage IP qui n’est pas couvert par RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), vous devez créer vos VNet et le sous-réseau devant être utilisé par votre équipement auxiliaire de direction avant la création de l’équipement de direction auxiliaire.

Dans la mesure où cette fonction place le Service d’application Azure dans votre réseau virtuel, cela signifie que vos applications sont hébergées dans votre ASE peuvent accéder maintenant de ressources qui sont accessibles par le biais de ExpressRoute ou de réseaux privés virtuels (VPN) site à site directement. Les applications qui se trouvent dans votre environnement de Service d’application ne nécessitent pas les fonctionnalités de mise en réseau supplémentaires pour accéder aux ressources disponibles sur le réseau virtuel qui héberge votre environnement de Service d’application. Cela signifie que vous n’avez pas besoin d’utiliser VNET intégration ou les connexions hybride à accéder aux ressources dans ou connecté à votre réseau virtuel. Vous pouvez toujours utiliser les deux de ces fonctionnalités pour accéder aux ressources dans les réseaux qui ne sont pas connectés au réseau virtuel.

Par exemple, vous pouvez utiliser VNET intégration pour s’intégrer à un réseau virtuel qui se trouve dans votre abonnement, mais n’est pas connecté au réseau virtuel qui se trouve dans votre ASE. Vous pouvez toujours également utiliser des connexions de hybride pour accéder aux ressources qui se trouvent dans d’autres réseaux, comme vous pouvez normalement.  

Si vous n’avez pas votre réseau virtuel est configuré avec un ExpressRoute de VPN, vous devez être conscient de besoins de routage qui dispose d’un équipement de direction auxiliaire. Il existe certaines configurations d’itinéraire de défini par l’utilisateur (UDR) qui ne sont pas compatibles avec un équipement de direction auxiliaire. Pour plus d’informations sur l’exécution d’un équipement de direction auxiliaire dans un réseau virtuel avec ExpressRoute, [un environnement de Service d’application d’un réseau virtuel avec ExpressRoute en cours d’exécution], consultez[ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Sécurisation du trafic entrant

Il existe deux méthodes principales pour contrôler le trafic entrant à votre équipement de direction auxiliaire.  Vous pouvez utiliser Groups(NSGs) de sécurité réseau pour contrôler le IP adresses peuvent accéder à votre équipement de direction auxiliaire comme décrit ici [comment contrôler le trafic entrant dans un environnement de Service d’application](app-service-app-service-environment-control-inbound-traffic.md) , et vous pouvez également configurer votre équipement auxiliaire de direction avec un Balancer(ILB) de charge interne.  Ces fonctionnalités peuvent également utilisées ensemble si vous voulez restreindre l’accès à l’aide de NSGs pour ASE de votre ILB.

Lorsque vous créez un équipement de direction auxiliaire, il créera un VIP dans votre VNet.  Il existe deux types de VIP, externes et internes.  Lorsque vous créez un équipement auxiliaire de direction avec une adresse IP virtuelle externe vos applications dans votre ASE seront accessibles via une adresse IP routable d’internet. Lorsque vous sélectionnez interne votre ASE va être configuré avec un ILB et ne sera pas directement accessible d’internet.  ILB ASE nécessite toujours une adresse IP virtuelle externe, mais il est utilisé uniquement pour l’accès de gestion et de maintenance Azure.  

Lors de la création de la ILB ASE vous fournissez le sous-domaine utilisé par l’équipement de direction auxiliaire ILB et avez à gérer votre propre serveur DNS pour le sous-domaine que vous spécifiez.  Étant donné que vous définissez le nom de sous-domaine, vous devez également gérer le certificat utilisé pour l’accès HTTPS.  Après la création de l’équipement de direction auxiliaire, vous êtes invité à fournir le certificat.  Pour en savoir plus sur la création et l’utilisation d’un équipement de direction auxiliaire ILB lire [à l’aide d’un équilibreur de charge interne avec un environnement de Service d’application][ILBASE]. 


## <a name="portal"></a>Portail

Vous pouvez gérer et surveiller votre environnement de Service d’application à l’aide de l’interface utilisateur dans le portail Azure. Si vous disposez d’un équipement de direction auxiliaire, vous êtes susceptible de voir le symbole de Service de l’application sur la barre latérale. Ce symbole est utilisé pour représenter les environnements de Service d’application dans Azure portal :

![Symbole du Service environnement App][1]

Pour ouvrir l’interface utilisateur qui répertorie l’ensemble de vos environnements de Service d’application, vous pouvez utiliser l’icône ou sélectionnez le chevron ("> » symbole) au bas de la barre latérale pour sélectionner les environnements de Service d’application. En sélectionnant l’une de l’ASEs répertoriés, vous ouvrez l’interface utilisateur qui permet de surveiller et de le gérer.

![Interface utilisateur pour la surveillance et la gestion de votre environnement de Service d’application][2]

Cette première lame indique certaines propriétés de votre équipement de direction auxiliaire, accompagnés d’un graphique de métriques par pool de ressources. Certaines des propriétés qui sont affichées dans le bloc **d’éducation** sont également des liens hypertexte qui ouvriront la lame qui lui est associée. Par exemple, vous pouvez sélectionner le nom de **Réseau virtuel** pour ouvrir l’interface utilisateur associée à votre équipement de direction auxiliaire est en cours d’exécution dans le réseau virtuel. **Plans de Service de l’application** et les **applications** d’ouvrir des lames qui répertorient ces éléments dans votre ASE.  

### <a name="monitoring"></a>Surveillance

Les graphiques permettent d’afficher un choix de mesures de performances dans chaque pool de ressources. Pour le pool frontal, vous pouvez surveiller le processeur et la mémoire moyenne. Pour les groupes de travail, vous pouvez contrôler la quantité utilisée et la quantité disponible.

Le Service application plusieurs plans peuvent faire utiliser des travailleurs dans un pool de travail. La charge de travail n’est pas distribué de la même manière qu’avec les serveurs frontaux, par conséquent, l’utilisation de la mémoire et du processeur ne fournissent pas article des informations utiles. Il est plus important suivre le nombre de travailleurs que vous avez utilisés et sont disponibles, notamment si vous gérez ce système à d’autres personnes à utiliser.  

Vous pouvez également utiliser toutes les mesures qui peuvent être suivis dans les graphiques pour configurer des alertes. Configuration d’alertes ici fonctionne de la même comme ailleurs dans le Service d’application. Vous pouvez définir une alerte à partir de la partie de l’interface utilisateur de **alertes** ou forage dans les mesures de l’interface utilisateur et en sélectionnant **Ajouter une alerte**.

![Métrique de l’interface utilisateur][3]

Les mesures qui ont été simplement présentés sont l’environnement de Service d’application des mesures. Il existe également des mesures qui sont disponibles au niveau du plan de Service de l’application. C’est où la surveillance du processeur et mémoire fait beaucoup de sens.

Dans un équipement de direction auxiliaire, tous les plans de Service de l’application sont des plans de Service d’application dédiés. Cela signifie que seules les applications qui sont exécutent sur les hôtes alloués pour que le service d’application sont les applications dans ce plan de Service d’application. Pour plus de détails sur votre plan de Service de l’application, afficher votre plan de Service de l’application à partir d’une des listes de l’interface utilisateur de ASE ou à partir de **plans de parcourir le Service application** (qui répertorie tous les).   

### <a name="settings"></a>Paramètres

Au sein de la lame ASE, il existe une section de **paramètres** qui contient plusieurs fonctions importantes :

**Paramètres** > **Propriétés**: la lame de **paramètres** s’ouvre automatiquement lorsque vous affichez les lames de votre équipement de direction auxiliaire. Dans la partie supérieure est **Propriétés**. Il existe un certain nombre d’éléments redondants à ce que vous voyez dans **Essentials**ici, mais ce qui est très utile est **Adresse IP virtuelle**, ainsi que **Les adresses IP sortants**.

![Lame de paramètres et propriétés][4]

**Paramètres** > **Les adresses IP**: lorsque vous créez une application IP Secure Sockets Layer (SSL) dans votre équipement de direction auxiliaire, vous avez besoin d’une adresse IP SSL. Pour obtenir une, votre ASE doit adresses IP SSL qu’il possède et qui peuvent être ventilés. Lors de la création d’un équipement de direction auxiliaire, il a une adresse IP SSL à cet effet, mais vous pouvez ajouter d’autres. Est une charge pour les adresses supplémentaires IP SSL, comme indiqué dans le [Service d’application de tarification] [ AppServicePricing] (dans la section sur les connexions SSL). Le supplément de prix est le prix de l’IP SSL.

**Paramètres** > **Avant fin Pool** / **Pools de travailleur**: chacune de ces lames de pool de ressources offre la possibilité d’afficher des informations uniquement sur ce pool de ressources, en plus des contrôles pour mettre entièrement ce pool de ressources à l’échelle.  

La lame de base pour chaque pool de ressources fournit un graphique avec des mesures de ce pool de ressources. Tout comme avec les graphiques de la lame ASE, vous pouvez aller dans le graphique et définir des alertes comme vous le souhaitez. Définition d’une alerte à partir de la blade ASE pour un pool de ressources spécifique fait la même chose que de la liste des ressources. À partir de la blade de **paramètres** de pool de travail, vous avez accès à toutes les applications ou les programmes de Service d’application qui s’exécutent dans ce pool de travail.

![Paramètres de pool de travail l’interface utilisateur][5]

### <a name="portal-scale-capabilities"></a>Fonctionnalités de portail d’échelle  

Il existe trois opérations d’échelle :

- Modification du nombre d’adresses IP de l’équipement auxiliaire de direction qui sont disponibles pour l’utilisation de IP SSL.
- Modification de la taille de la ressource de calcul qui est utilisée dans un pool de ressources.
- Modification du nombre de ressources de calcul qui sont utilisés dans une liste de ressources, soit manuellement, soit via autoscaling.

Dans le portail, il existe trois façons de contrôler le nombre de serveurs que vous avez dans vos pools de ressources :

- Une opération de l’échelle de la lame ASE principale du haut. Vous pouvez modifier plusieurs échelle configuration aux pools de front-end et le travailleur. Elles sont toutes appliquées en une seule opération.
- Une opération manuelle d’échelle de la lame ressource pool **échelle** , qui se trouve sous **paramètres**.
- AutoScaling, que vous pouvez configurer à partir de la lame **d’échelle** de pool des ressources individuelles.

Pour utiliser la mise à l’échelle sur la lame ASE, faites glisser le curseur à la quantité que vous souhaitez et l’enregistrez. Cette interface utilisateur prend également en charge la modification de la taille.  

![Échelle de l’interface utilisateur][6]

Pour utiliser les fonctionnalités manuel ou échelle automatique dans un pool de ressources spécifique, accédez à **paramètres** > **Avant fin Pool** / **Pools de travail** selon le cas. Ouvrez ensuite le pool que vous souhaitez modifier. Accédez à **paramètres** > **mise à l’échelle** ou **paramètres** > **évoluer**. La lame **Évolution** vous permet de contrôler la quantité de l’instance. **Évolution** vous permet de contrôler la taille de la ressource.  

![Paramètres de l’interface utilisateur mise à l’échelle][7]

## <a name="fault-tolerance-considerations"></a>Considérations relatives à la tolérance de pannes

Vous pouvez configurer un environnement de Service d’application pour utiliser les ressources de calcul total jusqu'à 55. Ces ressources de calcul 55, 50 uniquement peuvent être utilisés pour les charges de travail hôte. La raison à cela est double. Il existe un minimum de ressources de calcul frontal 2.  Cela laisse jusqu'à 53 pour prendre en charge de l’allocation de pool de travail. Pour fournir la tolérance de pannes, vous devez disposer d’une ressource de calcul supplémentaire est allouée selon les règles suivantes :

- Chaque pool de travail doit être d’au moins 1 ressource de calcul supplémentaire qui n’est pas disponible pour être affecté à une charge de travail.
- Lorsque la quantité de ressources informatiques dans un pool de travail dépasse une certaine valeur, puis une autre ressource de calcul est requise pour la tolérance de pannes. Ce n’est pas le cas dans le pool frontal.

Au sein d’un pool de travail unique, les exigences de tolérance de pannes qui sont pour une valeur donnée de X ressources affectés à un pool de travail :

- Si X est compris entre 2 et 20, la quantité de ressources de calcul utilisable que vous pouvez utiliser pour les charges de travail est X-1.
- Si X est compris entre 21 et 40, la quantité de ressources de calcul utilisable que vous pouvez utiliser pour les charges de travail est X-2.
- Si X est entre 41 et 53, la quantité de ressources de calcul utilisable que vous pouvez utiliser pour les charges de travail est X-3.

L’encombrement minimal a 2 serveurs frontaux et 2 travailleurs.  Avec les instructions ci-dessus puis, voici quelques exemples pour clarifier :  

- Si vous avez 30 travailleurs dans un pool unique, les 28 d'entre eux peut servir aux charges de travail hôte.
- Si vous avez 2 travailleurs dans un pool unique, 1 peut être utilisé pour les charges de travail hôte.
- Si vous disposez de 20 travailleurs dans un pool unique, les 19 peut servir aux charges de travail hôte.  
- Si vous avez 21 travailleurs dans un pool unique, puis encore 19 seule peut servir aux charges de travail hôte.  

L’aspect de la tolérance de panne est important, mais vous devez garder à l’esprit de mise à l’échelle dépasse un certain seuil. Si vous souhaitez augmenter la capacité allant de 20 instances, puis passez à 22 ou supérieur car 21 n’ajoute pas une plus grande capacité. Cela vaut passer au-dessus de 40, où le numéro suivant qui ajoute la capacité est de 42.  

## <a name="deleting-an-app-service-environment"></a>Suppression d’un environnement de Service d’application ##

Si vous souhaitez supprimer un environnement de Service d’application, utilisez simplement l’action **Supprimer** dans la partie supérieure de la lame d’environnement de Service d’application. Lorsque vous effectuez cette opération, vous devrez entrer le nom de votre environnement de Service d’application pour confirmer que vous souhaitez vraiment effectuer cette opération. Notez que lorsque vous supprimez un environnement de Service d’application, vous supprimez tout le contenu qu’il contient.  

![Supprimer un environnement de Service d’application interface utilisateur][9]  

## <a name="getting-started"></a>Mise en route

Pour vous familiariser avec les environnements de Service d’application, reportez-vous à la section [comment créer un environnement de Service d’application](app-service-web-how-to-create-an-app-service-environment.md).

Pour plus d’informations sur la plate-forme de services d’application Azure, voir [Service d’application Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
