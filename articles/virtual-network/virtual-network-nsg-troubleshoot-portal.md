<properties 
   pageTitle="Résoudre les problèmes de réseau des groupes de sécurité - portail | Microsoft Azure"
   description="Découvrez comment résoudre les groupes de sécurité de réseau dans le modèle de déploiement d’Azure le Gestionnaire de ressources en utilisant le portail Azure."
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

# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Résoudre les problèmes liés à des groupes de sécurité réseau à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Si vous configuré des groupes de sécurité réseau (NSGs) sur votre machine virtuelle (VM) et que vous rencontrez des problèmes de connectivité de machine virtuelle, cet article fournit une vue d’ensemble des fonctionnalités de diagnostic pour NSGs afin de résoudre les autres.

NSGs vous permet de contrôler les types de trafic activer ce flux et vos machines virtuelles (VM). NSGs peuvent être appliqués à des sous-réseaux dans un réseau virtuel d’Azure (VNet), les interfaces de réseau (NIC) ou les deux. Règles efficaces sur une carte d’interface réseau sont le regroupement de règles qui existent dans les NSGs appliqués à une carte réseau et le sous-réseau qu’il est connecté à. Les règles au sein de ces NSGs peuvent parfois entrent en conflit et avoir un impact sur la connectivité de réseau d’un ordinateur virtuel.  

Vous pouvez afficher toutes les règles de sécurité efficace dans votre NSGs, appliqués sur les cartes d’interface réseau de votre ordinateur virtuel. Cet article explique comment résoudre les problèmes de connectivité de machine virtuelle à l’aide de ces règles dans le modèle de déploiement du Gestionnaire de ressources Azure. Si vous n’êtes pas familiarisé avec les concepts de VNet et NSG, lisez les articles de vue d’ensemble [des réseaux virtuels](virtual-networks-overview.md) et des [groupes de sécurité réseau](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>À l’aide des règles de sécurité efficaces pour résoudre les problèmes de flux de trafic de machine virtuelle

Le scénario qui suit est un exemple d’un problème de connexion courant :

Un ordinateur virtuel nommé *VM1* fait partie d’un sous-réseau nommé *Subnet1* dans un VNet nommé *WestUS-VNet1*. Échec de la tentative de connexion à la machine virtuelle à l’aide de RDP sur le port TCP 3389. NSGs sont appliquées simultanément à la carte d’interface réseau *- NIC1 VM1* et le sous-réseau *Subnet1*. Le trafic sur le port TCP 3389 est autorisé dans le NSG associé à l’interface réseau *VM1-NIC1*, cependant, TCP ping à Échec du port 3389 de VM1.

Bien que cet exemple utilise le port TCP 3389, les étapes suivantes peuvent servir à déterminer les échecs de connexion entrante et sortante sur n’importe quel port.

### <a name="view-effective-security-rules-for-a-virtual-machine"></a>Afficher les règles de sécurité efficace pour une machine virtuelle

Effectuez les étapes suivantes pour résoudre les problèmes de NSGs pour une machine virtuelle :

Vous pouvez afficher la liste complète des règles de sécurité efficace sur une carte d’interface réseau, à partir de la machine virtuelle elle-même. Vous pouvez également ajouter, modifier et supprimer des règles NSG de carte réseau et de sous-réseau de la lame de règles efficaces, si vous disposez des autorisations nécessaires pour effectuer ces opérations.

1. Connexion au portail Azure à https://portal.azure.com.
2. Cliquez sur **plus de services**, puis cliquez sur des **ordinateurs virtuels** dans la liste qui s’affiche.
3. Sélectionnez un ordinateur virtuel pour résoudre les problèmes à partir de la liste qui s’affiche et une lame de machine virtuelle avec options s’affiche.
4. Cliquez sur **diagnostiquer & résoudre les problèmes de** , puis sélectionnez un problème courant. Pour cet exemple, **impossible de me connecter à mon ordinateur virtuel de Windows** est activée. 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)

5. Étapes apparaissent sous le problème, comme illustré dans l’image suivante : 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)

    Cliquez sur *règles de groupe de sécurité en cours* dans la liste des étapes recommandées.

6. La blade **d’obtenir les règles de sécurité** s’affiche, comme illustré dans l’image suivante :

    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)

    Notez les sections suivantes de l’image :

    - **Étendue :** La valeur *VM1*, l’ordinateur virtuel sélectionné à l’étape 3.
    - **Interface réseau :** *VM1-NIC1* est sélectionné. Une machine virtuelle peut avoir plusieurs interfaces réseau (NIC). Chaque carte réseau peut avoir des règles de sécurité unique. Lors du dépannage, vous devrez peut-être afficher les règles de sécurité efficace pour chaque carte réseau.
    - **Associés NSGs :** NSGs peuvent être appliqués à la fois la carte réseau et le sous-réseau d’à que la carte réseau est connectée. Dans l’image, un NSG a été appliqué à la fois la carte réseau et qu’il est connecté au sous-réseau. Vous pouvez cliquer sur les noms NSG de modifier directement les règles dans les NSGs.
    - **-Nsg VM1 onglet :** La liste de règles affichées dans l’image est pour le NSG appliquée à la carte réseau. Plusieurs règles par défaut sont créés par Azure chaque fois qu’un NSG est créée. Vous ne pouvez pas supprimer les règles par défaut, mais vous pouvez substituer les règles de priorité plus élevée. Pour en savoir plus sur les règles par défaut, lisez l’article de [présentation NSG](virtual-networks-nsg.md#default-rules) .
    - **La colonne de DESTINATION :** Certaines des règles que le texte dans la colonne, tandis que d’autres préfixes d’adresse. Le texte est le nom de balises par défaut appliqué à la règle de sécurité lorsqu’il a été créé. Les balises sont fournis par le système des identificateurs qui représentent plusieurs préfixes. Une règle avec une balise, comme *AllowInternetOutBound*, répertorie les préfixes de la lame de **préfixes d’adresses** .
    - **Télécharger :** La liste de règles peut être longue. Vous pouvez télécharger un fichier .csv, des règles pour l’analyse hors connexion en cliquant sur **Télécharger** et enregistrez le fichier.
    - **AllowRDP** Règle de trafic entrant : cette règle permet des connexions RDP à l’ordinateur virtuel.
7. Cliquez sur l’onglet **Subnet1-NSG** pour afficher les règles efficaces à partir du NSG appliquée au sous-réseau, comme illustré dans l’image suivante : 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)

    Notez la règle **entrante** de *denyRDP* . Les règles de trafic entrant appliqués sur le sous-réseau sont évaluées avant les règles appliquées au niveau de l’interface réseau. Dans la mesure où la règle de refus est appliquée sur le sous-réseau, la demande de connexion TCP 3389 tombe en panne, car la règle d’autorisation à la carte réseau n’est jamais évaluée. 

    La règle de *denyRDP* est la raison pour laquelle pourquoi ne réussit pas la connexion RDP. Suppression devrait résoudre le problème.

    >[AZURE.NOTE]Si la machine virtuelle associée à la carte réseau n’est pas en cours d’exécution ou NSGs n’ont pas été appliquées à la carte réseau ou un sous-réseau, aucune règle n’est affichés.

8. Pour modifier les règles NSG, cliquez sur *Subnet1-NSG* dans la section **Associée de NSGs** .
   La lame de **Subnet1-NSG** s’ouvre. Vous pouvez modifier directement les règles en cliquant sur **les règles de sécurité entrante**.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)

9. Après la suppression de la règle de trafic entrant *denyRDP* dans la **Subnet1-NSG** et ajout d’une règle *allowRDP* , la liste des règles efficaces ressemble à l’illustration suivante :

    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)

    Vérifiez que le port TCP 3389 est ouvert par l’ouverture d’une connexion RDP de la machine virtuelle ou à l’aide de l’outil PsPing. Vous pouvez apprendre PsPing en lisant la [page de téléchargement de PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="view-effective-security-rules-for-a-network-interface"></a>Afficher les règles de sécurité efficace pour une interface réseau

Si le flux de trafic de votre machine virtuelle est affecté pour une carte réseau spécifique, vous pouvez afficher une liste complète des règles efficaces pour la carte réseau à partir du contexte d’interfaces réseau en effectuant les étapes suivantes :

1. Connexion au portail Azure à https://portal.azure.com.
2. Cliquez sur **plus de services**, puis cliquez sur **interfaces réseau** dans la liste qui s’affiche.
3. Sélectionnez une interface réseau. Dans l’image suivante, une carte réseau nommée *VM1-NIC1* est sélectionnée.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)

    Notez que la **portée** est définie pour l’interface réseau sélectionnée. Pour en savoir plus sur les informations complémentaires affichées, lisez l’étape 6 de la section **NSGs de résoudre les problèmes d’un ordinateur virtuel** de cet article.

    >[AZURE.NOTE] Si un NSG est supprimé à partir d’une interface réseau, le sous-réseau NSG est toujours en vigueur sur la carte de donnée. Dans ce cas, la sortie n’affichait les règles du NSG du sous-réseau. Les règles s’affichent uniquement si la carte réseau est connectée à un ordinateur virtuel.

4. Vous pouvez modifier directement les règles pour NSGs associées à une carte réseau et un sous-réseau. Pour en savoir plus, voir l’étape 8 de la section **Afficher les règles de sécurité efficace pour un ordinateur virtuel** de cet article.

## <a name="view-effective-security-rules-for-a-network-security-group-nsg"></a>Afficher les règles de sécurité efficace pour un groupe de sécurité réseau (NSG)

Lorsque vous modifiez les règles NSG, vous souhaiterez examiner l’impact des règles ajoutées sur un ordinateur virtuel spécifique. Vous pouvez afficher une liste complète des règles de sécurité efficace pour toutes les cartes réseau qui est appliquée une NSG donné, sans avoir à basculer le contexte de la lame NSG donnée. Pour résoudre les problèmes des règles efficaces dans un NSG, procédez comme suit :

1. Connexion au portail Azure à https://portal.azure.com.
2. Cliquez sur **plus de services**, puis cliquez sur **groupes de sécurité de réseau** dans la liste qui s’affiche.
3. Sélectionnez un NSG. Dans l’image suivante, un NSG nommé VM1-nsg a été sélectionné.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)

    Notez les sections suivantes de l’image précédente :

    - **Étendue :** La valeur du NSG sélectionné.
    - **Machine virtuelle :** Lorsqu’un NSG est appliqué à un sous-réseau, il est appliqué à toutes les interfaces réseau attachés à tous les ordinateurs virtuels connectés au sous-réseau. Cette liste affiche tous les ordinateurs virtuels ce NSG s’applique à. Vous pouvez sélectionner toute machine virtuelle à partir de la liste.

    >[AZURE.NOTE] Si un NSG est appliqué uniquement un sous-réseau vide, ordinateurs virtuels ne seront pas répertoriés. Si un NSG est appliqué à une carte réseau qui n’est pas associée à une machine virtuelle, ces cartes réseau également pas apparaît. 
    - **Interface réseau :** Une machine virtuelle peut avoir plusieurs interfaces réseau. Vous pouvez sélectionner une interface réseau attachée à la machine virtuelle sélectionnée.
    - **AssociatedNSGs :** À tout moment, une carte réseau peut avoir jusqu'à deux NSGs efficaces, appliqué à la carte réseau et l’autre pour le sous-réseau. Bien que la portée est sélectionnée comme VM1-nsg, si la carte réseau a un sous-réseau efficace NSG, la sortie affiche les deux NSGs.
4. Vous pouvez modifier directement les règles pour NSGs associées à une carte réseau ou un sous-réseau. Pour en savoir plus, voir l’étape 8 de la section **Afficher les règles de sécurité efficace pour un ordinateur virtuel** de cet article.

Pour en savoir plus sur les informations complémentaires affichées, lisez l’étape 6 de la section **Afficher les règles de sécurité efficace pour un ordinateur virtuel** de cet article.

>[AZURE.NOTE] Bien qu’un sous-réseau et une carte d’interface réseau peuvent contenir uniquement une que NSG appliquée, un NSG peut être associé à plusieurs cartes réseau et plusieurs sous-réseaux.

## <a name="considerations"></a>Considérations relatives à la

Lors de la résolution des problèmes de connectivité, tenez compte des points suivants :

- Les règles par défaut NSG va bloquer l’accès entrant à partir d’internet et ne permettre que VNet le trafic entrant. Règles doivent être explicitement ajoutés pour permettre l’accès entrant à partir d’Internet, comme requis.
- S’il n’y a aucune règle de sécurité NSG à l’origine de la connectivité de réseau d’un ordinateur virtuel échoue, le problème peut être dû à :
    - Logiciel de pare-feu s’exécutant dans le système d’exploitation de l’ordinateur
    - Itinéraires configurés pour appliances virtuelles ou sur site. Le trafic Internet peut être redirigé vers les locaux via forcé de tunnel. Une connexion RDP/SSH à partir d’Internet à votre machine virtuelle peut ne pas fonctionne avec ce paramètre, en fonction de la manière dont le matériel de réseau local gère ce trafic. Lisez l’article de [Résolution des problèmes des itinéraires](virtual-network-routes-troubleshoot-powershell.md) pour savoir comment diagnostiquer les problèmes de routage peuvent être entraver le flux du trafic entrant et sortant de la machine virtuelle. 
- Si vous avez ressources VNets, par défaut, la balise VIRTUAL_NETWORK se développe automatiquement pour inclure les préfixes de ressources VNets. Vous pouvez afficher ces préfixes dans la liste **ExpandedAddressPrefix** , pour résoudre les problèmes liés à la connectivité d’homologation VNet. 
- Les règles de sécurité efficace ne sont affichées que s’il existe un NSG associé à de la machine virtuelle carte d’interface réseau et ou sous-réseau. 
- Si il n’y a aucun NSGs associés à la carte réseau ou sous-réseau et que vous avez une adresse IP publique affectée à votre machine virtuelle, tous les ports seront ouverts pour un accès entrant et sortant. Si la machine virtuelle a une adresse IP publique, l’application NSGs à la carte réseau ou un sous-réseau est fortement recommandé.