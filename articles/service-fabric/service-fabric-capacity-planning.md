<properties
   pageTitle="Planification de capacité pour les applications de Service Fabric | Microsoft Azure"
   description="Décrit comment identifier le nombre de nœuds de calcul requise pour une application de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="markfuss"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="capacity-planning-for-service-fabric-applications"></a>Planification de capacité pour les applications de Service Fabric


Ce document vous apprend à estimer le montant des ressources (UC, RAM, disque), que vous avez besoin pour exécuter vos applications Azure Fabric de Service. Il est courant que les ressources requises changer avec le temps. En général, peu de ressources est nécessaire comme votre service de développement/test, puis nécessitent plus de ressources que vous allez en production et votre application grandit en popularité. Lorsque vous concevez votre application, pensez selon les besoins à long terme et faire des choix qui permettent de votre service à l’échelle pour répondre à la forte demande des clients.

 Lorsque vous créez un cluster Service Fabric, vous choisissez les types de machines virtuelles (VM) qui composent le cluster. Chaque machine virtuelle est fourni avec une quantité limitée de ressources sous la forme de CPU (cœurs et vitesse), la bande passante réseau, mémoire vive et de stockage sur disque. Que votre service augmente au fil du temps, vous pouvez mettre à niveau pour les ordinateurs virtuels qui offrent davantage de ressources et/ou ajouter davantage d’ordinateurs virtuels à votre cluster. Pour faire de ce dernier, vous devez l’architecture votre service initialement afin qu’il peut tirer parti de nouvelles machines virtuelles ajoutées dynamiquement au cluster.

Certains services de gérer peu à aucune donnée sur les ordinateurs virtuels eux-mêmes. Par conséquent, la planification de capacité pour ces services devrait se concentrer principalement sur les performances, ce qui signifie que la sélection appropriées CPU (cœurs et vitesse) des ordinateurs virtuels. En outre, vous devez envisager la bande passante du réseau, y compris la fréquence des transferts réseau sont produisent et la quantité de données est en cours de transfert. Si votre service doit effectuer ainsi que l’utilisation de service augmente, vous pouvez ajouter davantage d’ordinateurs virtuels à l’équilibre de charge et de cluster les demandes réseau sur tous les ordinateurs virtuels.

Pour les services qui gèrent de grandes quantités de données sur les ordinateurs virtuels, la planification de la capacité doit se concentrer principalement sur la taille. Par conséquent, vous devez réfléchir soigneusement la capacité de RAM de l’ordinateur et le stockage sur disque. Le système de gestion de mémoire virtuelle dans Windows rend espace disque RAM l’aspect au code d’application. En outre, le runtime Service Fabric fournit pagination active, conserver les données uniquement à chaud en mémoire et le déplacement des données à froid sur le disque. Les applications peuvent ainsi utiliser plus de mémoire physique disponible sur l’ordinateur virtuel. Ayant plus de RAM simplement augmente les performances, étant donné que la machine virtuelle peut conserver plus de stockage de disque dans la mémoire vive. La machine virtuelle que vous sélectionnez doit avoir un disque suffisamment grand pour stocker les données que vous souhaitez sur la machine virtuelle. De la même façon, l’ordinateur virtuel doit avoir suffisamment de mémoire vive afin d’améliorer les performances de que votre choix. Si les données de votre service augmentent au fil du temps, vous pouvez ajouter davantage d’ordinateurs virtuels pour le cluster et les données de partition sur tous les ordinateurs virtuels.

## <a name="determine-how-many-nodes-you-need"></a>Déterminer le nombre de nœuds dont vous avez besoin

Votre service de partitionnement vous permet de faire évoluer les données de votre service. Pour plus d’informations sur le partitionnement, consultez [Partitionnement de tissu de Service](service-fabric-concepts-partitioning.md). Chaque partition doit tenir dans une machine virtuelle unique, mais plusieurs partitions (petites) peuvent être placées sur une machine virtuelle unique. Par conséquent, avoir des partitions plus petites offre davantage de souplesse que d’avoir quelques partitions plus grandes. Le compromis est que beaucoup de partitions augmente la charge de la Fabric du Service et vous ne pouvez effectuer des opérations traitées entre les partitions. Il est également plus de trafic réseau potentielles si votre code de service doit fréquemment accéder aux éléments de données qui résident dans des partitions différentes. Lorsque vous concevez votre service, vous devez réfléchir soigneusement ces avantages et les inconvénients d’arriver à une stratégie de partitionnement efficace.

Supposons que votre application possède un seul service avec état qui a une taille de la banque que vous vouliez atteindre DB_Size Go par an. Vous êtes prêt à ajouter des applications (et des partitions) que vous rencontrez de croissance au-delà de cette année.  Le facteur de réplication (RF), qui détermine le nombre de réplicas de votre service a une incidence sur le total DB_Size. Le total DB_Size entre tous les réplicas est le facteur de réplication multiplié par DB_Size.  Node_Size représente l’espace disque/de RAM par nœud que vous souhaitez utiliser pour votre service. Pour des performances optimales, le DB_Size devraient s’inscrire dans la mémoire au sein du cluster et un Node_Size autour de la RAM de la machine virtuelle doit être choisi. En allouant un Node_Size qui est supérieure à la capacité de mémoire vive, vous comptez sur la pagination fournie par le runtime du Service Fabric. Par conséquent, les performances ne soient pas optimales si vos données tout entier sont considéré comme actif (depuis, les données sont paginées d’entrée/sortie). Toutefois, pour de nombreux services qu’une fraction des données étant active, il est plus rentable.

Le nombre de nœuds requis pour une performance maximale peut être calculé comme suit :

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Compte de la croissance

Vous souhaiterez peut-être calculer le nombre de nœuds en fonction de la DB_Size que vous attendez de votre service pour atteindre, en plus de la DB_Size sur laquelle vous avez commencé avec. Puis, augmenter le nombre de nœuds que votre service augmente afin que le nombre de nœuds n’est pas-provisionnement. Mais le nombre de partitions doit être basé sur le nombre de nœuds qui sont nécessaires lorsque vous exécutez votre service à croissance maximale.

Il est utile de sorte que vous pouvez gérer les pics inattendues ou l’échec (par exemple, si vous descendent d’ordinateurs virtuels quelques) certaines machines supplémentaires disponibles à tout moment.  Alors que la capacité supplémentaire doit être déterminée à l’aide de vos pics attendus, un point de départ est à réserver quelques VM supplémentaires (5 à 10 % supplémentaires).

Le précédent suppose un service dynamique. Si vous avez plus d’un service dynamique, vous devez ajouter le DB_Size associé avec les autres services dans l’équation. Sinon, vous pouvez calculer le nombre de nœuds séparément pour chaque service avec état.  Votre service peut avoir des duplications ou des partitions qui ne sont pas équilibrées. Gardez à l’esprit que les partitions peuvent contenir des données plus que d’autres. Pour plus d’informations sur le partitionnement, consultez [l’article sur les meilleures pratiques de partitionnement](service-fabric-concepts-partitioning.md). Toutefois, l’équation précédente est la partition et les réplicas agnostique, parce que le Service Fabric permet de s’assurer que les réplicas sont répartis entre les nœuds de manière optimisée.


## <a name="use-a-spreadsheet-for-cost-calculation"></a>Utiliser une feuille de calcul pour le calcul des coûts

Maintenant nous allons placer des chiffres réels dans la formule. Une [feuille de calcul exemple](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) illustre la planification de la capacité d’une application qui contient trois types d’objets de données. Pour chaque objet, nous rapprocher de sa taille et le nombre des objets que nous s’attendent à avoir. Nous allons sélectionner également les réplicas combien nous voulons de chaque type d’objet. La feuille de calcul calcule le montant total de la mémoire sont stockées dans le cluster.

Puis nous permet d’entrer une taille de mémoire virtuelle et d’un coût mensuel. En fonction de la taille de la mémoire virtuelle, la feuille de calcul vous indique le nombre de partitions que vous permet de diviser vos données en fonction de physiquement sur les nœuds. Vous pouvez permettre à un plus grand nombre de partitions pour prendre en charge de calcul spécifique de votre application et le trafic réseau a besoin. La feuille de calcul affiche le nombre de partitions que vous gérez les objets de profil utilisateur a augmenté d’un à six.

Maintenant, la feuille de calcul en fonction de toutes ces informations, montre que vous pouvez physiquement obtenir toutes les données avec les partitions de votre choix et les réplicas sur un cluster de nœuds 26. Toutefois, ce cluster doit être compactes, vous pouvez donc certains nœuds supplémentaires pour prendre en charge les mises à niveau et les défaillances de nœud. La feuille de calcul indique également qu’ayant plus de 57 nœuds ne fournit aucune valeur supplémentaire, car vous auriez de nœuds vides. À nouveau, vous souhaiterez atteindre au-dessus de 57 noeuds malgré tout prendre en charge les mises à niveau et les défaillances de nœud. Vous pouvez modifier la feuille de calcul pour faire correspondre les besoins spécifiques de votre application.   

![Feuille de calcul pour le calcul des coûts][Image1]



## <a name="next-steps"></a>Étapes suivantes

Consultez [les services de Fabric de Service partitionnement] [ 10] pour en savoir plus sur le partitionnement de votre service.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
