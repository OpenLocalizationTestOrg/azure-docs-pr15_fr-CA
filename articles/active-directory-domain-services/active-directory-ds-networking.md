<properties
    pageTitle="Des Services de domaine Active Directory Azure : Les instructions de mise en réseau | Microsoft Azure"
    description="Considérations relatives à la mise en réseau pour Azure des Services de domaine Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="maheshu"/>

# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considérations relatives à la mise en réseau pour les Services de domaine Active Directory de Azure

## <a name="how-to-select-an-azure-virtual-network"></a>Comment sélectionner un réseau virtuel Azure
Les instructions suivantes vous aident à sélectionner un réseau virtuel à utiliser avec les Services de domaine Active Directory de Azure.

### <a name="type-of-azure-virtual-network"></a>Type d’Azure réseau virtuel

- Vous pouvez activer les Services de domaine Active Directory de Azure dans un réseau virtuel Azure classique.

- Azure des Services de domaine Active Directory **ne peut pas être activée dans les réseaux virtuels créés à l’aide du Gestionnaire de ressources Azure**.

- Vous pouvez vous connecter à un réseau virtuel basé sur le Gestionnaire de ressources à un réseau virtuel classique, dans lequel les Services de domaine Active Directory Azure est activée. Par la suite, vous pouvez utiliser les Services de domaine Active Directory de Azure dans le réseau virtuel basé sur le Gestionnaire de ressources. Pour plus d’informations, reportez-vous à la section de [connectivité réseau](active-directory-ds-networking.md#network-connectivity) .

- **Les réseaux virtuels régionaux**: Si vous envisagez d’utiliser un réseau virtuel, assurez-vous qu’il s’agit d’un réseau virtuel.

    - Réseaux virtuels qui utilisent le mécanisme d’affinités hérités ne peut pas être utilisés avec les Services de domaine Active Directory de Azure.

    - Pour utiliser les Services de domaine Active Directory de Azure, [migrer d’anciens réseaux virtuels à des réseaux virtuels régionaux](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### <a name="azure-region-for-the-virtual-network"></a>Région Azure pour le réseau virtuel

- Vos Services de domaine Active Directory de Azure domaine géré est déployé dans la même région Azure comme le réseau virtuel que vous choisissez d’activer le service.

- Sélectionnez un réseau virtuel dans une région Azure pris en charge par les Services de domaine Active Directory de Azure.

- Consultez la page [des services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans lequel les Services de domaine Active Directory Azure est disponible.


### <a name="requirements-for-the-virtual-network"></a>Configuration requise pour le réseau virtuel

- **Proximité à vos charges de travail Azure**: sélectionnez le réseau virtuel qui héberge actuellement / d’héberge des ordinateurs virtuels qui ont besoin d’accéder aux Services de domaine Active Directory de Azure.

- **Personnalisé/mettre vos propres serveurs**: s’assurer qu’il n’y a aucun serveur DNS personnalisé configuré pour le réseau virtuel.

- **Les domaines existants portant le même nom de domaine**: s’assurer que vous n’avez pas d’un domaine existant portant le même nom de domaine disponible sur ce réseau virtuel. Par exemple, supposons que vous avez un domaine appelé « contoso.com » déjà disponibles sur le réseau virtuel sélectionné. Par la suite, vous essayez d’activer un domaine géré de Services de domaine Active Directory de Azure avec le même nom de domaine (qui est « contoso.com ») sur ce réseau virtuel. Vous rencontrez une erreur lorsque vous tentez d’activer les Services de domaine Active Directory de Azure. Cet échec est dû à des conflits de noms pour le nom de domaine du réseau virtuel. Dans ce cas, vous devez utiliser un nom différent pour définir votre domaine géré les Services de domaine Active Directory de Azure. Alternativement, vous pouvez mettre hors service le domaine existant et continuez à activer les Services de domaine Active Directory de Azure.

> [AZURE.WARNING] Impossible de déplacer les Services de domaine à un autre réseau virtuel une fois que vous avez activé le service.


## <a name="network-security-groups-and-subnet-design"></a>Création de groupes de sécurité et de sous-réseau du réseau
Un [Groupe de sécurité réseau (NSG)](../virtual-network/virtual-networks-nsg.md) contient une liste des règles de liste de contrôle d’accès (ACL) qui autorisent ou refusent le trafic réseau vers les instances de la machine virtuelle dans un réseau virtuel. NSGs peuvent être associés à des sous-réseaux ou des instances VM individuelles au sein de ce sous-réseau. Lorsqu’un NSG est associé à un sous-réseau, les règles de la liste ACL s’appliquent à toutes les instances de la machine virtuelle de ce sous-réseau. En outre, le trafic vers un ordinateur virtuel individuel peut être limité en associant un NSG directement à cette machine virtuelle.

![Conception de sous-réseau recommandée](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)


### <a name="best-practices-for-choosing-a-subnet"></a>Meilleures pratiques pour le choix d’un sous-réseau
- Déployer des Services de domaine Active Directory de Azure à un **sous-réseau dédié distinct** au sein de votre réseau virtuel Azure.

- N’appliquez pas de NSGs au sous-réseau dédié pour votre domaine géré. Si vous devez appliquer le NSGs au sous-réseau dédié, assurez-vous de **ne pas bloquer les ports requis pour le service et la gestion d’un domaine**.

- Ne limitent pas trop le nombre d’adresses IP disponibles au sein du sous-réseau dédié pour votre domaine géré. Cette restriction empêche le service de mise à disposition deux contrôleurs de domaine pour votre domaine géré.

- **Ne pas activer les Services de domaine Active Directory de Azure dans le sous-réseau de passerelle** de votre réseau virtuel.


> [AZURE.WARNING] Lorsque vous associez un NSG avec un sous-réseau dans lequel des Services de domaine Active Directory de Azure est activé, vous pouvez perturber la capacité de Microsoft de service et de gérer le domaine. En outre, la synchronisation entre vos clients AD Azure et votre domaine géré est interrompue. **Le contrat SLA ne s’applique pas aux déploiements où un NSG a été appliquée qui bloque les Services de domaine Active Directory de Azure dans la mise à jour et la gestion de votre domaine.**


### <a name="ports-required-for-azure-ad-domain-services"></a>Ports requis pour les Services de domaine Active Directory de Azure
Les ports suivants sont requis pour les Services de domaine Active Directory de Azure au service et maintenir votre domaine géré. Assurez-vous que ces ports ne sont pas bloqués pour le sous-réseau dans lequel vous avez activé votre domaine géré.

| Numéro de port | Objectif |
|---|---|
| 443 | Synchronisation avec vos clients AD Azure |
| 3389 | Gestion de votre domaine |
| 5986 | Gestion de votre domaine |
| 636 | Sécurisation de l’accès LDAP (LDAPS) pour votre domaine géré |



## <a name="network-connectivity"></a>Connectivité réseau
Un domaine géré de Services de domaine Active Directory de Azure peut être activé uniquement dans un seul réseau virtuel classique dans Azure. Les réseaux virtuels créés à l’aide du Gestionnaire de ressources Azure ne sont pas pris en charge.


### <a name="scenarios-for-connecting-azure-networks"></a>Scénarios pour connecter les réseaux Azure
Connexion à des réseaux virtuels Azure pour utiliser le domaine géré dans un des scénarios de déploiement suivants :

#### <a name="use-the-managed-domain-in-more-than-one-azure-classic-virtual-network"></a>Utilisez le domaine géré dans plus d’un réseau virtuel Azure classique
Vous pouvez vous connecter à des réseaux virtuels autres Azure classiques au réseau virtuel classique Azure dans lequel vous avez activé les Services de domaine Active Directory de Azure. Cette connexion VPN vous permet d’utiliser le domaine géré avec vos charges de travail déployés dans d’autres réseaux virtuels.

![Connectivité classique de réseau virtuel](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Utilisez le domaine géré dans un réseau virtuel basé sur le Gestionnaire de ressources
Vous pouvez vous connecter à un réseau virtuel basé sur le Gestionnaire de ressources sur le réseau virtuel classique Azure dans lequel vous avez activé les Services de domaine Active Directory de Azure. Cette connexion vous permet d’utiliser le domaine géré avec vos charges de travail déployés dans le réseau virtuel basé sur le Gestionnaire de ressources.

![Gestionnaire de ressources de réseau virtuel classique](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### <a name="network-connection-options"></a>Options de connexion réseau

- **Connexions de VNet-à-VNet à l’aide de connexions VPN de site à site**: connexion d’un réseau virtuel à un autre réseau virtuel (VNet à VNet) est similaire à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité permet de fournir un tunnel sécurisé à l’aide de IPsec/IKE une passerelle VPN.

    ![Connectivité de réseau virtuel à l’aide de la passerelle VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Plus d’informations - se connecter à des réseaux virtuels à l’aide de la passerelle VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **Connexions VNet à VNet à l’aide de l’homologation de réseau virtuel**: l’homologation de réseau virtuel est un mécanisme qui connecte deux réseaux virtuels dans la même région à travers le réseau principal d’Azure. Une fois que ressources les deux réseaux virtuels apparaissent sous forme d’une à toutes fins de connectivité. Ils sont toujours gérés comme des ressources distinctes, mais les ordinateurs virtuels de ces réseaux virtuels peuvent communiquer entre eux directement à l’aide des adresses IP privées.

    ![Connectivité de réseau virtuel à l’aide d’homologation](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Plus d’informations - virtuels réseau homologation](../virtual-network/virtual-network-peering-overview.md)



<br>

## <a name="related-content"></a>Contenu associé

- [Homologation d’Azure réseau virtuel](../virtual-network/virtual-network-peering-overview.md)

- [Configurer une connexion de VNet-à-VNet pour le modèle de déploiement classique](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Groupes de sécurité de réseau Azure](../virtual-network/virtual-networks-nsg.md)
