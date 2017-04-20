<properties
   pageTitle="Les types de nœud de service Fabric et jeux d’échelle de machine virtuelle | Microsoft Azure"
   description="Explique comment les types de nœud de Service Fabric se rapportent à des jeux d’échelle de machine virtuelle et comment connectent à distance à une instance de machine virtuelle échelle définie ou un nœud de cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>La relation entre les types de nœud de Service Fabric et des ensembles d’échelle de Machine virtuelle

Jeux d’échelle de Machine virtuelle sont une ressource Azure Compute, que vous pouvez utiliser pour déployer et gérer une collection d’ordinateurs virtuels sous la forme d’un ensemble. Chaque type de nœud qui est défini dans un cluster de Service Fabric est configuré comme un ensemble d’échelle VM distinct. Chaque type de nœud peut ensuite être mis à l’échelle ou vers le bas de manière indépendante, ont différents ensembles de ports ouverts et peut comporter des mesures de capacité différente.

La capture d’écran suivante montre un cluster qui possède deux types de nœuds : frontal et principal.  Chaque type de nœud a cinq nœuds.

![Capture d’écran d’un cluster qui possède deux Types de nœuds][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Mappage des instances de machine virtuelle échelle définie à des nœuds

Comme vous pouvez le voir ci-dessus, VM échelle définie instances démarrer à partir de l’instance 0, puis passe les. La numérotation est répercutée sur les noms. Par exemple, BackEnd_0 est l’instance 0 de l’ensemble d’échelle de machine virtuelle de back-end. Cet ensemble d’échelle machine virtuelle donnée dispose de cinq instances, nommés BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 et BackEnd_4.

Lorsque vous mettre à l’échelle d’une échelle de machine virtuelle définie, une nouvelle instance est créée. Le nouveau nom d’instance VM échelle définie sera en général le nom de la machine virtuelle échelle définie + le numéro d’instance suivant. Dans notre exemple, il est BackEnd_5.


## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Mappage d’échelle de l’ordinateur virtuel définie les équilibreurs de charge pour chaque type de nœud/VM ensemble d’échelle

Si vous avez déployé votre cluster à partir du portail ou que vous avez utilisé l’exemple de modèle de gestionnaire de ressources donné, puis lorsque vous obtenez une liste de toutes les ressources dans un groupe de ressources vous verrez les équilibreurs de charge pour chaque type de nœud ou de la machine virtuelle échelle définie.

Le nom devrait ressembler à : **livres -&lt;nom de NodeType&gt;**. Par exemple, LB-sfcluster4doc-0, comme le montre cette capture d’écran :


![Ressources][Resources]


## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Connexion à distance à une instance de machine virtuelle échelle définie ou un nœud de cluster
Chaque type de nœud qui est défini dans un cluster est configuré comme un ensemble d’échelle VM distinct.  Cela signifie que les types de nœud peuvent être mis à l’échelle vers le haut ou vers le bas de manière indépendante et peut en être faite différentes références SKU de machine virtuelle. Contrairement aux ordinateurs virtuels d’instance unique, les instances de l’échelle de la machine virtuelle définie n’obtiennent pas une adresse IP virtuelle qui leur sont propres. Ainsi, il peut être un peu délicat lorsque vous recherchez une adresse IP et le port que vous pouvez utiliser à distance de se connecter à une instance spécifique.

Voici la procédure à suivre pour les découvrir.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Étape 1 : Rechercher l’adresse IP virtuelle pour le type de nœud et les règles de trafic entrant de NAT pour RDP

Pour qui obtenir, vous devez obtenir les valeurs de règles NAT entrants qui ont été définis dans le cadre de la définition de ressource pour **Microsoft.Network/loadBalancers**.

Dans le portail, accédez à la lame d’équilibrage de la charge, puis sur **paramètres**.

![LBBlade][LBBlade]


Dans **paramètres**, cliquez sur **règles de trafic entrant de NAT**. Désormais vous donne l’adresse IP et le port que vous pouvez utiliser à distance de se connecter à la première instance de machine virtuelle échelle définie. Dans la capture d’écran ci-dessous, il est **104.42.106.156** et **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Étape 2 : Déterminer le port que vous pouvez utiliser à distance se connecter à l’instance d’échelle de machine virtuelle définie spécifique/nœud

Plus haut dans ce document, j’ai parlé de correspondent entre les instances de l’échelle de la machine virtuelle définie pour les nœuds. Nous les utiliserons pour déterminer le port exact.

Les ports sont affectés dans l’ordre croissant de l’instance de machine virtuelle échelle définie. Ainsi, dans mon exemple pour le type de nœud frontal, les ports pour chacune des cinq instances sont les suivantes : Vous devez maintenant effectuer le même mappage pour votre instance de machine virtuelle échelle définie.

|**Instance de jeu d’échelle VM**|**Port**|
|-----------------------|--------------------------|
|FrontEnd_0|3389|
|FrontEnd_1|3390|
|FrontEnd_2|3391|
|FrontEnd_3|3392|
|FrontEnd_4|3393|
|FrontEnd_5|3394|


### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Étape 3 : À distance de se connecter à l’instance spécifique de machine virtuelle échelle définie

Dans la capture d’écran ci-dessous utiliser Connexion Bureau à distance pour vous connecter à la FrontEnd_1 :

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Comment faire pour modifier les valeurs de plage de port RDP

### <a name="before-cluster-deployment"></a>Avant le déploiement du cluster

Lorsque vous configurez le cluster à l’aide d’un modèle de gestionnaire de ressources, vous pouvez spécifier la plage dans **inboundNatPools**.

Atteindre la définition de ressource pour **Microsoft.Network/loadBalancers**. Sous que vous trouverez la description de **inboundNatPools**.  Remplacez les valeurs *frontendPortRangeStart* et *frontendPortRangeEnd* .

![InboundNatPools][InboundNatPools]


### <a name="after-cluster-deployment"></a>Après le déploiement du cluster
C’est un peu plus complexe et les ordinateurs virtuels lors de l’obtention de recyclage peut entraîner. Vous devez maintenant définir de nouvelles valeurs à l’aide de PowerShell d’Azure. Assurez-vous que Azure PowerShell 1.0 ou version ultérieure est installé sur votre ordinateur. Si vous n’avez pas fait auparavant, je vous suggère fortement que vous suivez la procédure décrite dans [Comment faire pour installer et configurer Azure PowerShell.](../powershell-install-configure.md)

Connectez-vous à votre compte Azure. Si cette commande PowerShell échoue pour une raison quelconque, vous devez vérifier si vous disposez de PowerShell Azure installé correctement.

```
Login-AzureRmAccount
```

Exécutez la commande suivante pour obtenir des détails sur votre équilibreur de charge, et que les valeurs de la description de **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Maintenant la valeur *frontendPortRangeEnd* et *frontendPortRangeStart* les valeurs souhaitées.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la fonctionnalité « Déploiement anywhere » et une comparaison avec Azure-Gestion de clusters](service-fabric-deploy-anywhere.md)
- [Sécurité des clusters](service-fabric-cluster-security.md)
- [Kit de développement logiciel de service de Fabric et de mise en route](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
