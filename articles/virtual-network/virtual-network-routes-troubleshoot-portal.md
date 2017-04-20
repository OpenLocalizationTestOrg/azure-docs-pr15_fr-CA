<properties 
   pageTitle="Résoudre les problèmes d’itinéraires - portail | Microsoft Azure"
   description="Découvrez comment résoudre les problèmes des itinéraires dans le modèle de déploiement d’Azure le Gestionnaire de ressources en utilisant le portail Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-routes-using-the-azure-portal"></a>Résoudre les problèmes des itinéraires en utilisant le portail Azure

> [AZURE.SELECTOR]
- [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Si vous rencontrez des problèmes de connectivité réseau vers ou à partir de votre Machine virtuelle (VM) Azure, itinéraires peuvent être ayant un impact sur les flux de trafic de votre machine virtuelle. Cet article fournit une vue d’ensemble des fonctionnalités de diagnostic pour les itinéraires afin de résoudre les autres.

Tables de routage sont associés à des sous-réseaux et sont efficaces sur toutes les interfaces réseau (NIC) de ce sous-réseau. Les types d’itinéraires suivants peuvent être appliquées à chaque interface réseau :

- **Des itinéraires de système :** Par défaut, chaque sous-réseau créé dans un réseau virtuel d’Azure (VNet) a des tables de routage de système qui autorisent le trafic local de VNet, le trafic local via des passerelles VPN et du trafic Internet. Itinéraires de système existent également pour les ressources VNets.
- **Itinéraires BGP :** Propagé aux interfaces réseau via ExpressRoute ou des connexions VPN de site à site. Plus d’informations sur le routage BGP en lisant les articles [BGP avec les passerelles VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) et [vue d’ensemble de ExpressRoute](../expressroute/expressroute-introduction.md) .
- **Les itinéraires définis par l’utilisateur (UDR) :** Si vous utilisez des équipements de réseau virtuel ou sont forcés de tunneling du trafic d’un réseau local via une connexion VPN de site à site, vous pouvez avoir des itinéraires définis par l’utilisateur (UDRs) associés à votre table de routage de sous-réseau. Si vous n’êtes pas familier avec UDRs, lisez l’article [les itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined-routes) .

Avec les itinéraires différents qui peuvent être appliqués à une interface réseau, il peut être difficile de déterminer les itinéraires d’agrégation sont efficaces. Pour vous aider à résoudre les problèmes de connectivité de réseau de machine virtuelle, vous pouvez afficher tous les itinéraires pour une interface réseau efficaces dans le modèle de déploiement du Gestionnaire de ressources Azure.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>À l’aide d’itinéraires efficace pour résoudre les problèmes de flux de trafic de machine virtuelle

Cet article utilise le scénario suivant à titre d’exemple pour illustrer comment résoudre les problèmes des itinéraires pour une interface réseau efficaces :

Une machine virtuelle (*VM1*) connecté à la VNet (*VNet1*, préfixe : 10.9.0.0/16) ne parvient pas à se connecter à un VM(VM3) dans une VNet de ressources récemment (*VNet3*, préfixe 10.10.0.0/16). Il n’y a aucun UDRs ou BGP n’achemine appliqué à l’interface de réseau VM1-NIC1 connecté à la machine virtuelle, seuls les itinéraires de système sont appliquées.

Cet article explique comment faire pour déterminer la cause de l’échec de la connexion, à l’aide de la capacité de parcours efficace dans un modèle de déploiement de la gestion des ressources Azure.
Tandis que l’exemple utilise uniquement les itinéraires de système, les mêmes étapes peuvent être utilisés pour déterminer les échecs de connexion entrante et sortante sur n’importe quel type d’itinéraire.

>[AZURE.NOTE] Si votre machine virtuelle a plus d’une carte d’interface réseau attachée, vérifiez les itinéraires efficaces pour chacune des cartes réseau pour diagnostiquer des problèmes de connectivité réseau vers et à partir d’un ordinateur virtuel.

### <a name="view-effective-routes-for-a-virtual-machine"></a>Afficher les gammes efficaces pour une machine virtuelle

Pour afficher les itinéraires d’agrégation qui sont appliqués à un ordinateur virtuel, procédez comme suit :

1. Connexion au portail Azure à https://portal.azure.com.
2. Cliquez sur **plus de services**, puis cliquez sur des **ordinateurs virtuels** dans la liste qui s’affiche.
3. Sélectionnez un ordinateur virtuel pour résoudre les problèmes à partir de la liste qui s’affiche et une lame de machine virtuelle avec options s’affiche.
4. Cliquez sur **diagnostiquer & résoudre les problèmes de** , puis sélectionnez un problème courant. Pour cet exemple, **impossible de me connecter à mon ordinateur virtuel de Windows** est activée. 

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)

5. Étapes apparaissent sous le problème, comme illustré dans l’image suivante : 

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Cliquez sur *itinéraires efficaces* dans la liste des étapes recommandées.

6. La blade **d’itinéraires efficaces** s’affiche, comme illustré dans l’image suivante :

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Si votre ordinateur virtuel n’a qu’une seule carte d’interface réseau, il est sélectionné par défaut. Si vous avez plusieurs cartes réseau, sélectionnez la carte réseau pour laquelle vous souhaitez afficher les itinéraires efficaces.

    >[AZURE.NOTE] Si la machine virtuelle associée à la carte réseau n’est pas en cours d’exécution, les itinéraires efficaces ne s’affichera pas. Uniquement les 200 premiers itinéraires efficaces sont affichés dans le portail. Pour obtenir la liste complète, cliquez sur **Télécharger**. Vous pouvez filtrer davantage les résultats à partir du fichier .csv téléchargé.

    Notez ce qui suit dans la sortie :
    - **Source**: indique le type d’itinéraire. Itinéraires de système sont affichés comme *par défaut*, UDRs sont affichés sous forme d’itinéraires *d’utilisateur* et de la passerelle (statique ou BGP) sont affichées sous forme de *VPNGateway*.
    - **État**: indique l’état de la gamme efficace. Les valeurs possibles sont *actives* ou *non valide*.
    - **AddressPrefixes**: Spécifie le préfixe de l’adresse de l’itinéraire efficace dans la notation CIDR. 
    - **nextHopType**: indique le saut suivant pour l’itinéraire. Les valeurs possibles sont *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*ou *Null*. Une valeur *Null* pour **nextHopType** dans un UDR peut indiquer un itinéraire non valide. Par exemple, si **nextHopType** est *VirtualAppliance* et le réseau virtuel appliance virtuelle n’est pas dans un état de mise en service ou l’exécution. Si **nextHopType** est *VPNGateway* et qu’il n’y a pas de passerelle mis en service ou l’exécution dans le VNet donné, l’itinéraire peut devenir non valide.
    
7. Il n’y a aucun itinéraire répertorié à la VNet *WestUS-VNET3* (préfixe 10.10.0.0/16) à partir de la *WestUS-VNet1* (préfixe 10.9.0.0/16) dans l’image à l’étape précédente. Dans l’image suivante, le lien d’homologation est dans l’état *déconnecté* :
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Le lien bidirectionnel pour l’homologation est rompu, qui explique pourquoi VM1 pas pu se connecter à VM3 dans le VNet *WestUS-VNet3* .

8. L’illustration suivante montre les itinéraires après avoir établi le lien d’homologation bidirectionnelle :

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Pour des scénarios de dépannage plus de tunneling forcé et à l’évaluation de l’itinéraire, lisez la [Considérations relatives à la](virtual-network-routes-troubleshoot-portal.md#Considerations) section de cet article.

### <a name="view-effective-routes-for-a-network-interface"></a>Afficher les gammes efficaces pour une interface réseau

Si le flux de trafic réseau est affecté pour une interface réseau (NIC), vous pouvez afficher une liste complète des itinéraires efficaces sur une carte d’interface réseau directement. Pour afficher les itinéraires d’agrégation qui sont appliquées à une carte réseau, procédez comme suit :

1. Connexion au portail Azure à https://portal.azure.com.
2. Cliquez sur **plus de services**, puis cliquez sur **interfaces réseau**
3. Recherche dans la liste pour le nom d’une carte réseau, ou le sélectionner dans la liste qui s’affiche. Dans cet exemple, **VM1-NIC1** est sélectionné.
4. Sélectionnez **les itinéraires efficaces** dans la blade **d’interface réseau** , comme illustré dans l’image suivante :
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    **L’étendue** par défaut, l’interface réseau sélectionnée.

    ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)


### <a name="view-effective-routes-for-a-route-table"></a>Afficher les gammes efficaces pour une table de routage

Lors de la modification des itinéraires définis par l’utilisateur (UDRs) dans une table de routage, vous souhaiterez examiner l’impact des itinéraires ajoutés sur un ordinateur virtuel spécifique. Une table de routage peut être associée à n’importe quel nombre de sous-réseaux. Vous pouvez désormais afficher tous les itinéraires efficaces pour toutes les cartes réseau qui est appliquée une table de routage donné, sans avoir à basculer le contexte de la lame de la table gamme donné.

Pour cet exemple, un UDR (*UDRoute*) est spécifié dans une table de routage (*UDRouteTable*). Cet itinéraire envoie tout le trafic Internet à partir de *Subnet1* dans la *WestUS-VNet1* VNet, via un réseau virtuel à (couloirs), dans le *Subnet2* de la même VNet. L’itinéraire est illustré dans l’image suivante :

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Pour afficher les itinéraires d’agrégation pour une table de routage, procédez comme suit :

1. Connexion au portail Azure à https://portal.azure.com.
2. **Plus de services**, puis cliquez sur les **tables de routage**
3. Recherche dans la liste pour la table de routage que vous souhaitez voir les itinéraires agrégation pour et sélectionnez-le. Dans cet exemple, **UDRouteTable** est sélectionnée. Une lame pour la table de la gamme sélectionnée s’affiche, comme illustré dans l’image suivante :

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)

4. Sélectionnez **Efficace d’itinéraires** dans la **table de routage** de lame. La **portée** est définie pour la table de routage que vous avez sélectionné.
5. Une table de routage peut être appliquée à plusieurs sous-réseaux. Sélectionnez le **sous-réseau** que vous souhaitez examiner dans la liste. Dans cet exemple, **Subnet1** est sélectionnée.
6. Sélectionnez une **Interface réseau**. Toutes les cartes réseau connectés au sous-réseau sélectionné sont répertoriés. Dans cet exemple, **VM1-NIC1** est sélectionné.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

    >[AZURE.NOTE] Si la carte réseau n’est pas associée à une machine virtuelle en cours d’exécution, aucun itinéraire efficaces n’est affichés.

## <a name="considerations"></a>Considérations relatives à la

Quelques éléments à garder à l’esprit lors de la révision de la liste des itinéraires retournés :

- Le routage est basé sur la plus longue du préfixe correspondance (LPM) entre les UDRs, les itinéraires BGP et système. S’il existe plusieurs itinéraires avec la même correspondance de stratégies locales, puis un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :
    - Itinéraire de défini par l’utilisateur
    - Itinéraires BGP
    - Itinéraire du système (par défaut)

    Avec des itinéraires efficaces, vous ne pouvez voir parcours efficaces de correspondance de stratégies locales basée sur toutes les voies disponibles. En montrant comment les itinéraires sont en réalité évalués pour une carte de réseau donné, cela rend beaucoup plus facile résoudre les problèmes des itinéraires spécifiques qui peuvent être ayant un impact sur la connectivité vers/à partir de votre machine virtuelle.

- Si vous avez UDRs et que vous envoyez du trafic à un réseau virtuel solution matérielle-logicielle (à couloirs), avec *VirtualAppliance* comme **nextHopType**, assurez-vous que le transfert IP est activé sur l’à couloirs reçoit le trafic ou les paquets sont supprimés. 
- Si Forced tunneling est activée, tout le trafic Internet sortant est routé vers sur site. RDP/SSH à partir d’Internet à votre machine virtuelle peuvent ne pas fonctionner avec ce paramètre, en fonction de comment sur site gère ce trafic. 
  Forcé de tunneling peut être activé :
    - Si vous utilisez un réseau VPN de site à site, en définissant un itinéraire défini par l’utilisateur (UDR) avec nextHopType comme passerelle VPN
    - Si un itinéraire par défaut est annoncé sur BGP
- Pour VNet le trafic homologation fonctionne correctement, un itinéraire de système avec **nextHopType** *VNetPeering* doit exister pour la plage de préfixes de le VNet de ressources. Si un itinéraire de ce type n’existe pas, et le lien d’homologation VNet semble OK :
    - Attendez quelques secondes et réessayez s’il s’agit d’un lien d’homologation récemment établi. Il est parfois plus longue pour propager les itinéraires à toutes les interfaces réseau dans un sous-réseau.
    - Règles du groupe de sécurité réseau (NSG) peuvent être un impact sur les flux de trafic. Pour plus d’informations, consultez l’article de [Résoudre les groupes de sécurité réseau](virtual-network-nsg-troubleshoot-portal.md) .
