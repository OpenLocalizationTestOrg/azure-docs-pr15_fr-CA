<properties
  pageTitle="Sondes personnalisé d’équilibrage de charge et de surveillance de l’état de santé | Microsoft Azure"
  description="Apprenez à utiliser les sondes personnalisé pour Azure équilibreur de charge pour surveiller les instances derrière un équilibreur de charge"
  services="load-balancer"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="understand-load-balancer-probes"></a>Comprendre les sondes d’équilibrage de la charge

Équilibreur de charge Azure offre la possibilité de surveiller l’état des instances de serveur à l’aide de sondes. Lorsqu’une sonde ne répond pas, équilibreur de charge cesse d’envoyer de nouvelles connexions à l’instance incorrecte. Les connexions existantes ne sont pas affectées, et de nouvelles connexions sont envoyées aux instances en bon état.

Rôles de service cloud (rôles worker et rôles web) utilisent un agent de l’invité pour la surveillance de la sonde. Les sondes personnalisé TCP ou HTTP doivent être configurés lorsque vous utilisez des machines virtuelles derrière un équilibreur de charge.

## <a name="understand-probe-count-and-timeout"></a>Comprendre le nombre de sondes et de délai d’attente

Comportement de la sonde dépend :

- Le nombre de sondes réussies qui permettent une instance doivent être étiquetées comme étant en service.
- Le nombre de sondes ayant échouées qui provoquent une instance doivent être étiquetées comme étant hors service.

La valeur de délai d’expiration et la fréquence définie dans SuccessFailCount déterminer si une instance est confirmée être en cours d’exécution ou ne pas en cours d’exécution. Dans le portail d’Azure, le délai est défini à deux fois la valeur de la fréquence.

La configuration de la sonde de toutes les instances d’équilibrage de charge pour un point de terminaison (autrement dit, un jeu avec équilibrage de charge) doit être identiques. Cela signifie que vous ne peut pas avoir une configuration de sondage différentes pour chaque instance de rôle ou d’un ordinateur virtuel dans le même service hébergé pour une combinaison de point de terminaison particulier. Par exemple, chaque instance doit avoir des délais d’attente et les ports locaux identiques.

>[AZURE.IMPORTANT] Une sonde de l’équilibreur de charge utilise l’adresse IP 168.63.129.16. Cette adresse IP publique facilite la communication aux ressources internes de plate-forme pour la mettre votre-propriétaire-IP scénario Azure virtuelle réseau. L’adresse IP publique virtuelle 168.63.129.16 est utilisé dans toutes les régions et ne changera pas. Nous vous conseillons d’autoriser cette adresse IP dans les stratégies de pare-feu local. Elle ne doit pas être considérée comme un risque de sécurité car uniquement la plateforme Azure interne puisse fournir la source un message provenant de cette adresse. Si vous ne le faites pas, il y aura un comportement inattendu dans une variété de scénarios tels que la configuration de la même plage d’adresses IP de 168.63.129.16 et ayant des adresses IP en double.

## <a name="learn-about-the-types-of-probes"></a>En savoir plus sur les types de sondes

### <a name="guest-agent-probe"></a>Sonde d’agent invité

Cette sonde est uniquement disponible pour les Services en nuage Azure. Équilibreur de charge utilise l’agent de l’invité à l’intérieur de l’ordinateur virtuel, puis écoute et répond avec une réponse HTTP 200 OK uniquement lorsque l’instance est à l’état prêt (c'est-à-dire, pas dans un autre état comme occupé, de recyclage ou d’arrêter).

Pour plus d’informations, reportez-vous à la section [configuration du fichier de définition de service (csdef) pour les sondes de santé](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [commencer à créer un équilibreur de charge pour les services en nuage avec accès via Internet](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>En quoi une sonde d’agent invité marquer une instance comme étant non fonctionnelle ?

Si l’agent de l’invité ne répond pas avec HTTP 200 OK, l’équilibreur de charge marque l’instance comme ne répond pas et interrompt l’envoi de trafic à cette instance. L’équilibreur de charge se poursuit avec la commande ping de l’instance. Si l’agent invité répond avec un HTTP 200, l’équilibreur de charge envoie à nouveau le trafic à cette instance.

Lorsque vous utilisez un rôle web, le code de site Web s’exécute généralement dans w3wp.exe, qui n’est pas surveillé par le Azure fabric ou invité de l’agent. Cela signifie que les défaillances dans w3wp.exe (par exemple, les réponses HTTP 500) ne sont pas signalés à l’agent d’invité et l’équilibreur de charge ne prendra pas cette instance de rotation.

### <a name="http-custom-probe"></a>Sonde personnalisé de HTTP

La sonde d’équilibrage de la charge HTTP personnalisé substitue la sonde de l’agent par défaut invité, qui signifie que vous pouvez créer votre propre logique personnalisée pour déterminer l’état de l’instance de rôle. L’équilibreur de charge sondes de toutes les 15 secondes, votre point de terminaison par défaut. L’instance est considérée comme de la rotation d’équilibrage de la charge si il répond avec un HTTP 200 dans le délai d’expiration (31 secondes par défaut).

Cela peut être utile si vous souhaitez implémenter votre propre logique pour supprimer des instances de la rotation d’équilibrage de la charge. Par exemple, vous pouvez décider de supprimer une instance, si elle est supérieure à 90 % du temps processeur et renvoie un état autre que 200. Si vous avez des rôles web qui utilisent w3wp.exe, cela signifie également vous obtenez automatique de surveillance de votre site Web, car les échecs dans le code de votre site Web renvoie un état autre que 200 à la sonde d’équilibrage de la charge.

>[AZURE.NOTE] La sonde personnalisée HTTP prend en charge les chemins d’accès relatifs et le protocole HTTP uniquement. HTTPS n’est pas pris en charge.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>En quoi une sonde personnalisée HTTP marquer une instance comme étant non fonctionnelle ?

- L’application HTTP renvoie un code de réponse HTTP autre que 200 (par exemple, 403, 404 ou 500). Il s’agit d’un accusé de réception positif que l’instance d’application doit être immédiatement mises hors service.
- Le serveur HTTP ne répond pas du tout après la période de délai d’attente. En fonction de la valeur de délai d’expiration est définie, cela peut signifier que sonde plusieurs demandes go sans réponse avant que la sonde est marquée comme ne fonctionnant ne pas (c'est-à-dire, avant de SuccessFailCount sondes sont envoyés).
- Le serveur ferme la connexion via une réinitialisation TCP.

### <a name="tcp-custom-probe"></a>Sonde personnalisé de TCP

Sondes de TCP établir une connexion en effectuant une négociation à trois voies avec le port défini.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>En quoi une sonde personnalisée TCP marquer une instance comme étant non fonctionnelle ?

- Le serveur TCP ne répond pas du tout après la période de délai d’attente. Lorsque la sonde est marquée comme ne fonctionnant ne pas dépend du nombre de demandes ayant échoué de sonde qui ont été configurés pour accéder sans réponse avant de marquer la sonde comme ne fonctionnant ne pas.
- La sonde reçoit un réinitialisation à partir de l’instance de rôle TCP.

Pour plus d’informations sur la configuration d’une sonde de santé HTTP ou une sonde TCP, consultez [créer un équilibreur de charge faisant face à Internet dans le Gestionnaire de ressources à l’aide de PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Ajouter des instances sains en rotation d’équilibrage de la charge

Sondes TCP et HTTP sont considérés comme intègres et marquer comme sain lorsque l’instance de rôle :

- L’équilibreur de charge Obtient une sonde positive lors du premier démarrage de la machine virtuelle.
- Le numéro SuccessFailCount (décrit précédemment) définit la valeur de sondes réussies qui sont nécessaires pour marquer l’instance de rôle comme sain. Si une instance du rôle a été supprimée, le nombre de sondes réussies, successives égale ou supérieure à la valeur de SuccessFailCount pour marquer l’instance du rôle en cours d’exécution.

>[AZURE.NOTE] Si l’état d’une instance de rôle fluctue, l’équilibreur de charge attend plus avant de mettre l’instance de rôle dans l’état sain. Ceci est possible grâce à protéger l’utilisateur et l’infrastructure.

## <a name="use-log-analytics-for-load-balancer"></a>Analytique de journal d’utilisation pour l’équilibrage de la charge

Vous pouvez utiliser [analytique de journal pour l’équilibrage de la charge](load-balancer-monitor-log.md) pour vérifier l’état de santé de sonde et le nombre de la sonde. La journalisation peut servir avec alimentation BI ou Azure des perspectives opérationnelles de fournir des statistiques sur l’état de santé d’équilibrage de la charge.
