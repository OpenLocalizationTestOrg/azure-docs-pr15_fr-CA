<properties
   pageTitle="Planification de la capacité de cluster Service Fabric | Microsoft Azure"
   description="Considérations relatives à la planification de la capacité de cluster de service Fabric. Niveaux de fiabilité, de durabilité et de NodeTypes"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considérations relatives à la planification de la capacité de cluster service Fabric

Pour tout déploiement de production, la planification de la capacité est une étape importante. Voici certains des éléments que vous devez prendre en compte dans le cadre de ce processus.

- Le numéro de votre cluster doit commencer avec des types de nœuds
- Les propriétés de chaque type de nœud (taille, principal, internet face, le nombre d’ordinateurs virtuels, etc.).
- Les caractéristiques de fiabilité et de durabilité du cluster

Nous allons vous présenter brièvement chacun de ces éléments.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Le numéro de votre cluster doit commencer avec des types de nœuds

Vous devez d’abord déterminer celui à utiliser pour le cluster que vous créez et quels types d’applications que vous envisagez de déployer dans ce cluster. Si vous n’êtes pas clairement l’objectif du cluster, vous ne serez probablement pas encore prêt à entrer la processus de planification de la capacité.

Établir le nombre de votre cluster doit commencer avec des types de nœuds.  Chaque type de nœud est mappé à un ensemble d’échelle de Machine virtuelle. Chaque type de nœud peut ensuite être mis à l’échelle ou vers le bas de manière indépendante, ont différents ensembles de ports ouverts et peut comporter des mesures de capacité différente. Par conséquent, la décision du nombre de types de nœud essentiellement revient aux considérations suivantes :

- Votre application possède plusieurs services, et un d’eux doivent-ils être public ou accès internet ? Les applications contiennent un service passerelle frontale qui reçoit l’entrée d’un client et un ou plusieurs services back-end qui communiquent avec les services frontaux. Ainsi, dans ce cas, vous vous retrouver ayant au moins deux types de nœuds.

- Vos services (qui composent votre application) ont des besoins d’infrastructure différentes de RAM au minimum des cycles microprocesseurs plus élevés ? Par exemple, supposons que l’application que vous souhaitez déployer contient un service frontal et un service back-end. Le service frontal peut exécuter sur les ordinateurs virtuels plus petits (tailles de machine virtuelle comme D2) que les ports sont ouverts à internet.  Le service principal, cependant, est beaucoup de calcul et doit être exécuté sur des machines virtuelles plus grands (dont la taille de la machine virtuelle comme D4, D6, D15) qui ne sont pas internet face.

 Dans cet exemple, bien que vous pouvez décider de placer tous les services sur le type d’un nœud, il est recommandé que vous les placez dans un cluster avec deux types de nœuds.  Ainsi, pour chaque type de nœud d’avoir des propriétés distinctes, telles que la connectivité internet ou la taille de mémoire virtuelle. Le nombre d’ordinateurs virtuels peut être mis à l’échelle indépendamment, ainsi.  

- Dans la mesure où vous ne pouvez pas prédire l’avenir, accédez avec les faits que vous connaissez et décider du nombre de types de nœuds que vos applications ont besoin de commencer. Vous pouvez toujours ajouter ou supprimer ultérieurement des types de nœuds. Un cluster à structure de Service doit avoir le type d’au moins un nœud.

## <a name="the-properties-of-each-node-type"></a>Les propriétés de chaque type de nœud

Le **type de nœud** peuvent être considérées comme équivalentes à des rôles dans les Services en nuage. Les types de nœud définissent les tailles de la machine virtuelle, le nombre d’ordinateurs virtuels et leurs propriétés. Chaque type de nœud qui est défini dans un cluster de Service Fabric est configuré comme un ordinateur virtuel distinct échelle définie. Jeux d’échelle de VM sont un Azure compute vous pouvez utiliser pour déployer et gérer une collection d’ordinateurs virtuels sous la forme d’un ensemble de ressources. Défini comme distinct VM échelle définit, chaque type de nœud peut ensuite être mis à l’échelle ou vers le bas de manière indépendante, ont différents ensembles de ports ouverts et peut comporter des mesures de capacité différente.

Votre cluster peut posséder plus d’un type de nœud, mais le type de nœud principal (celle que vous définissez sur le portail) doit avoir au moins cinq des ordinateurs virtuels pour les clusters utilisés pour les charges de travail (ou au moins trois ordinateurs virtuels pour les clusters de test). Si vous créez le cluster à l’aide d’un modèle de gestionnaire de ressources, vous trouverez un attribut **est principal** à la définition de type de nœud. Le type de nœud principal est le type de nœud où sont trouvent les services de Fabric de Service système.  

### <a name="primary-node-type"></a>Type de nœud principal
Pour un cluster avec plusieurs types de nœud, vous devez choisir un d’eux comme principal. Voici les caractéristiques d’un type de nœud principal :

- La taille minimale des ordinateurs virtuels pour le type de nœud principal est déterminée par le niveau de durabilité que vous choisissez. La valeur par défaut pour le niveau de durabilité est Bronze. Faites défiler la liste pour plus d’informations sur le niveau de durabilité et les valeurs qu’il peut prendre.  

- Le nombre minimal d’ordinateurs virtuels pour le type de nœud principal est déterminé par le niveau de fiabilité que vous choisissez. La valeur par défaut pour le niveau de fiabilité est argent. Faites défiler la liste pour plus d’informations sur le niveau de fiabilité et sur les valeurs qu’il peut prendre.

- Les services de système de tissu de Service (par exemple, le service Gestionnaire de Cluster ou le service de banque d’Image) sont placées sur le type de nœud principal et ainsi la fiabilité et la durabilité du cluster est déterminée par la valeur de couche de la fiabilité et la valeur de couche de durabilité vous sélectionnez pour le type de nœud principal.

![Capture d’écran d’un cluster qui possède deux Types de nœuds ][SystemServices]


### <a name="non-primary-node-type"></a>Type de nœud principal non
Pour un cluster avec plusieurs types de nœud, il existe un type de nœud principal et les autres leur sont secondaires. Voici les caractéristiques d’un type de nœud principal :

- La taille minimale des ordinateurs virtuels pour ce type de nœud est déterminée par le niveau de durabilité que vous choisissez. La valeur par défaut pour le niveau de durabilité est Bronze. Faites défiler la liste pour plus d’informations sur le niveau de durabilité et les valeurs qu’il peut prendre.  

- Le nombre minimal d’ordinateurs virtuels pour ce type de nœud peut être une. Toutefois, vous devez choisir ce nombre en fonction du nombre de réplicas de/services d’application que vous souhaitez exécuter dans ce type de nœud. Le nombre d’ordinateurs virtuels dans un type de nœud peut être augmenté après avoir déployé le cluster.


## <a name="the-durability-characteristics-of-the-cluster"></a>Les caractéristiques de durabilité du cluster

Le niveau de durabilité est utilisé pour indiquer au système les privilèges dont vos ordinateurs virtuels avec l’infrastructure sous-jacente de Azure. Dans le type de nœud principal, ce privilège permet de Fabric du Service à suspendre toute demande de niveau infrastructure de machine virtuelle (par exemple, un redémarrage de la machine virtuelle, créer une nouvelle image de machine virtuelle ou de migration de VM) ayant un impact sur les conditions de quorum pour les services système et votre état. Dans les types de nœud principal, ce privilège permet de Fabric suspendre toute demande de niveau infrastructure de machine virtuelle telles que le redémarrage de l’ordinateur virtuel, de créer une nouvelle image de machine virtuelle, de migration de VM etc., ayant un impact sur les conditions de quorum pour vos services avec état, il est en Service.

Ce privilège est exprimé dans les valeurs suivantes :

- Gold - les travaux d’infrastructure peut être suspendue pour une durée de 2 heures par UD

- Argent - les travaux d’infrastructure peut être suspendue pour une durée de 30 minutes par UD (Ceci est actuellement pas activé pour l’utilisation. Une fois activée ce sera disponible sur tous les ordinateurs virtuels standard de cœur unique et plus).

- Bronze - aucun privilège. Il s’agit de la valeur par défaut.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Les caractéristiques de fiabilité du cluster

Le niveau de fiabilité est utilisé pour définir le nombre de réplicas des services système que vous souhaitez exécuter dans ce cluster, sur le type de nœud principal. Plus le nombre de répliques, la plus fiable les services système sont dans votre cluster.  

Le niveau de fiabilité peut prendre les valeurs suivantes.

- PLATINE - exécuter les services système avec une cible de nombre de 9 du jeu de réplicas

- Gold - exécuter les services système avec une cible du jeu de réplicas nombre de 7

- Argent - exécuter les services système avec une cible du jeu de réplicas nombre de 5

- Bronze - exécuter les services système avec une cible de compteur de 3 du jeu de réplicas

>[AZURE.NOTE] Le niveau de fiabilité que vous choisissez détermine le nombre minimum de noeuds que le type de nœud principal doit avoir. Le niveau de fiabilité n’a aucune incidence sur la taille maximale du cluster. Afin de disposer d’un cluster à 20 nœuds, qui est en cours d’exécution à la fiabilité de Bronze.

 Vous pouvez mettre à jour de la fiabilité de votre cluster d’un niveau à l’autre. Cette opération déclenche les mises à niveau de cluster si nécessaire de modifier le réplica de services système définir le nombre. Attendez que la mise à niveau en cours soit terminée avant d’apporter d’autres modifications au cluster, telles que l’ajout de nœuds, etc..  Vous pouvez surveiller la progression de la mise à niveau sur le Service Fabric Explorer ou en exécutant [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez terminé votre capacité de planification et de configurer un cluster, lisez le texte suivant :
- [Sécurité du Fabric du service cluster](service-fabric-cluster-security.md)
- [Introduction de modèle health service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
