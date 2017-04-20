<properties
   pageTitle="Ce qui est une liste de contrôle d’accès réseau (ACL) ?"
   description="En savoir plus sur les listes ACL"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-is-an-endpoint-access-control-list-acls"></a>Ce qui est un point de terminaison de liste de contrôle d’accès (ACL) ?

Un point de terminaison de liste de contrôle d’accès (ACL) est une amélioration de la sécurité disponible pour votre déploiement d’Azure. Une liste ACL offre la possibilité d’autoriser ou d’interdire le trafic pour l’ordinateur virtuel d’un point de terminaison sélective. Cette fonctionnalité de filtrage de paquets fournit une couche supplémentaire de sécurité. Vous pouvez spécifier les ACL du réseau pour seulement les points de terminaison. Vous ne pouvez pas spécifier une ACL pour un réseau virtuel ou d’un sous-réseau spécifique contenu dans un réseau virtuel.

> [AZURE.IMPORTANT] Il est recommandé d’utiliser des groupes de sécurité réseau (NSGs) au lieu de l’ACL dès que possible. Pour en savoir plus sur NSGs, reportez-vous à la section [ce qu’est un groupe de sécurité réseau ?](virtual-networks-nsg.md).

Listes ACL peut être configurés à l’aide de PowerShell ou sur le portail de gestion. Pour configurer un réseau ACL à l’aide de PowerShell, consultez [Gestion des Access Control Lists (ACL) pour les points de terminaison à l’aide de PowerShell](virtual-networks-acl-powershell.md). Pour configurer un réseau ACL à l’aide du portail de gestion, consultez [comment définir les points de terminaison à une Machine virtuelle](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

À l’aide de listes ACL de réseau, vous pouvez effectuer les opérations suivantes :

- Autoriser ou refuser le trafic entrant, basé sur un sous-réseau distant plage d’adresse IPv4 pour un point de terminaison d’entrée d’ordinateur virtuel de manière sélective.

- Liste de blocage des adresses IP

- Créer plusieurs règles par point de terminaison de machine virtuelle

- Spécifier les règles ACL jusqu'à 50 par point de terminaison de machine virtuelle

- Classement des règles d’utilisation pour assurer l’ensemble approprié de règles sont appliquées à un point de terminaison une machine virtuelle spécifique (le plus bas au plus élevé)

- Spécifiez une liste ACL pour une adresse IPv4 du sous-réseau distant spécifique.

## <a name="how-acls-work"></a>Fonctionne des listes ACL

Une ACL est un objet qui contient une liste de règles. Lorsque vous créez une liste ACL et l’appliquez à un point de terminaison de machine virtuelle, le filtrage de paquets intervient sur le nœud hôte de votre machine virtuelle. Cela signifie que le trafic provenant d’adresses IP distantes est filtré par le nœud de l’hôte pour les règles ACL correspondants au lieu de sur votre ordinateur virtuel. Ceci empêche votre ordinateur virtuel dépenses les cycles de processeur précieux sur le filtrage de paquets.

Lorsqu’une machine virtuelle est créée, les ACL par défaut est mis en place pour bloquer tout le trafic entrant. Toutefois, si un point de terminaison est créé (port 3389), puis les ACL est modifiée pour autoriser tout le trafic entrant pour ce point de terminaison par défaut. Le trafic entrant à partir de n’importe quel sous-réseau distant est alors autorisé à ce point de terminaison et aucune configuration de pare-feu n’est requis. Tous les autres ports sont bloqués pour le trafic entrant, sauf si les points de terminaison sont créées pour ces ports. Par défaut, le trafic sortant est autorisé.

**Exemple de table ACL par défaut**

| **Règle #** | **Sous-réseau à distance** | **Point de terminaison** | **Autoriser/Refuser** |
|--------|---------------|----------|-------------|
| 100    | 0.0.0.0/0     | 3389     | Autoriser      |

## <a name="permit-and-deny"></a>Autoriser et refuser

Vous pouvez sélectivement autoriser ou refuser le trafic réseau pour un point de terminaison d’entrée d’ordinateur virtuel en créant des règles qui spécifient « permit » ou « refuser ». Il est important de noter que par défaut, lors de la création d’un point de terminaison, tout le trafic est autorisé pour le point de terminaison. Pour cette raison, il est important de comprendre comment créer des règles d’autorisation/de blocage et de les placer dans le bon ordre de priorité, si vous souhaitez un contrôle granulaire sur le trafic réseau que vous choisissez pour permettre d’atteindre le point de terminaison de machine virtuelle.

Points à prendre en compte :

1. **Aucune ACL :** Par défaut lors de la création d’un point de terminaison, nous permettre toutes les pour le point de terminaison.

1. **Autoriser :** Lorsque vous ajoutez un ou plusieurs « permettant » plages, vous refusez toutes les autres tranches par défaut. Seuls les paquets à partir de la plage IP autorisée seront en mesure de communiquer avec le point de terminaison de machine virtuelle.

1. **Refus-** Lorsque vous ajoutez un ou plusieurs « refuser » plages, vous autorisez toutes les autres tranches de trafic par défaut.

1. **Combinaison d’autorisation et de refus :** Vous pouvez utiliser une combinaison de « autoriser » et « refuser » lorsque vous voulez imaginer une plage IP spécifique pour être autorisé ou refusé.

## <a name="rules-and-rule-precedence"></a>Les règles et la priorité des règles

Les ACL de réseau permet de configurer sur un ordinateur virtuel spécifique de points de terminaison. Par exemple, vous pouvez spécifier un réseau ACL pour un point de terminaison RDP créé sur une machine virtuelle qui verrouille les accès pour certaines adresses IP. Le tableau ci-dessous indique un moyen pour accorder l’accès au public IPs virtuel (VIPs) d’une certaine plage d’autoriser l’accès pour RDP. Toutes les autre adresses IP distante sont refusées. Nous suivons un ordre de règle *le plus bas est prioritaire* .

### <a name="multiple-rules"></a>Plusieurs règles

Dans l’exemple ci-dessous, si vous souhaitez autoriser l’accès au point de terminaison RDP uniquement à partir de deux publics plages d’adresses IPv4 (65.0.0.0/8 et 159.0.0.0/8), vous pouvez obtenir cela en spécifiant les deux règles *Autoriser* . Dans ce cas, étant donné que le protocole RDP est créé par défaut pour un ordinateur virtuel, vous souhaiterez verrouiller l’accès au port RDP basé sur un sous-réseau distant. L’exemple ci-dessous illustre une méthode pour accorder l’accès au public IPs virtuel (VIPs) d’une certaine plage d’autoriser l’accès pour RDP. Toutes les autre adresses IP distante sont refusées. Cela fonctionne parce que les ACL de réseau permet de configurer un point de terminaison de machine virtuelle spécifique et l’accès est refusé par défaut.

**Exemple – plusieurs règles**

| **Règle #** | **Sous-réseau à distance** | **Point de terminaison** | **Autoriser/Refuser** |
|--------|---------------|----------|-------------|
| 100    | 65.0.0.0/8    | 3389     | Autoriser      |
| 200    | 159.0.0.0/8   | 3389     | Autoriser      |

### <a name="rule-order"></a>Ordre de la règle

Étant donné que plusieurs règles peuvent être spécifiées pour un point de terminaison, il doit y avoir une façon d’organiser les règles pour déterminer quelle règle est prioritaire. L’ordre des règles spécifie la priorité. ACL du réseau suivre un ordre de règle *le plus bas est prioritaire* . Dans l’exemple ci-dessous, le point de terminaison sur le port 80 sélective a accès aux seules certaines plages d’adresses IP. Pour ce faire, nous avons une règle de refus (règle \# 100) pour les adresses dans l’espace 175.1.0.1/24. Une deuxième règle est ensuite spécifiée avec priorité 200 qui permet l’accès à toutes les autres adresses sous 175.0.0.0/8.

**Exemple – la priorité des règles**

| **Règle #** | **Sous-réseau à distance** | **Point de terminaison** | **Autoriser/Refuser** |
|--------|---------------|----------|-------------|
| 100    | 175.1.0.1/24  | 80       | Refuser        |
| 200    | 175.0.0.0/8   | 80       | Autoriser      |

## <a name="network-acls-and-load-balanced-sets"></a>Réseau des ACL et équilibrée des jeux de charges

Les ACL de réseau peuvent être spécifiés sur un point de terminaison du jeu (Set LB) équilibrée de la charge. Si une liste ACL est spécifiée pour un ensemble de livres, l’ACL du réseau est appliquée à tous les ordinateurs virtuels dans ce jeu de livres. Par exemple, si un ensemble de livres est créé avec le » Port « 80 et l’ensemble de livres contient 3 VMs, l’ACL de réseau créé sur le point de terminaison « Port 80", d’une que machine virtuelle s’appliquera automatiquement aux autres ordinateurs virtuels.

![Réseau des ACL et équilibrée des jeux de charges](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Étapes suivantes

[Comment faire pour gérer les listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](virtual-networks-acl-powershell.md)
