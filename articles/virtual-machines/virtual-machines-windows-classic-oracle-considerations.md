<properties
pageTitle="Considérations sur l’utilisation des images de machine virtuelle d’Oracle | Microsoft Azure"
description="Obtenir des informations sur les configurations prises en charge et les limitations propres à une machine virtuelle d’Oracle sur serveur Windows Azure avant de le déployer."
services="virtual-machines-windows"
documentationCenter=""
manager="timlt"
authors="rickstercdn"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="09/06/2016"
ms.author="rclaus" />

#<a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>Considérations relatives à divers pour les images de machine virtuelle d’Oracle



Cet article traite des considérations pour les machines virtuelles Oracle dans Azure, qui sont basées sur des images de logiciels Oracle fournis par Oracle.  

-  Images de machine virtuelle de base de données Oracle
-  Images de machine virtuelle WebLogic Server Oracle
-  Images de machine virtuelle JDK Oracle

##<a name="oracle-database-virtual-machine-images"></a>Images de machine virtuelle de base de données Oracle
### <a name="clustering-rac-is-not-supported"></a>Gestion de clusters (RAC) n’est pas pris en charge.

Azure ne prend pas en charge Oracle Real Application Clusters (RAC) de la base de données Oracle. Autonome uniquement des instances de base de données Oracle sont possibles. Il s’agit, car Azure n’autorise pas actuellement partage de disque virtuel d’une manière en lecture/écriture entre plusieurs instances de l’ordinateur virtuel. Multidiffusion UDP n’est également pas prise en charge.

### <a name="no-static-internal-ip"></a>Aucun IP interne statique

Azure affecte à chaque machine virtuelle une adresse IP interne. À moins que l’ordinateur virtuel fait partie d’un réseau virtuel, l’adresse IP de l’ordinateur virtuel est dynamique et peut changer après le redémarrage de l’ordinateur virtuel. Cela peut entraîner des problèmes car la base de données Oracle attend l’adresse IP statique. Pour éviter ce problème, pensez à utiliser l’ordinateur virtuel à un réseau virtuel d’Azure. Pour plus d’informations, reportez-vous à la section [Réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) et [créer un réseau virtuel dans Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="attached-disk-configuration-options"></a>Options de configuration de disque connecté

Vous pouvez placer des fichiers de données sur les disques attachés, également connu sous le nom des disques de données ou soit le disque système d’exploitation de l’ordinateur virtuel. Les disques liés peuvent offrir une meilleure flexibilité de performances et la taille que le disque du système d’exploitation. Le disque du système d’exploitation peut être préférable que pour les bases de données sous 10 gigaoctets (Go).

Les disques liés s’appuient sur le service de stockage Azure Blob. Chaque disque est capable d’un maximum théorique d’environ 500 entrées/sorties par seconde (IOPS). Les performances des disques connectés ne soient pas optimaux initialement et performances d’e/s peuvent améliorer considérablement après une période de « burn-in » d’environ 60 à 90 minutes de fonctionnement continu. Si par la suite, un disque reste inactif, ses performances peuvent s’en ressentir jusqu'à ce qu’une autre période de rodage de fonctionnement continu. En bref, la plus active un disque, plus il est pour optimiser les performances e/s approche.

Bien que l’approche la plus simple consiste à associer un seul disque à la machine virtuelle et les fichiers de base de données sur ce disque, cette approche est également le plus limitatif en termes de performances. Au lieu de cela, vous pouvez souvent améliorer les performances d’e/s efficace de si vous utilisez plusieurs disques connectés, répartir les données de la base de données et ensuite utilisez Oracle Automatic Storage Management (ASM). Pour plus d’informations, consultez [vue d’ensemble du stockage automatique d’Oracle](http://www.oracle.com/technetwork/database/index-100339.html) . Bien qu’il soit possible d’utiliser l’entrelacement de disques multiples au niveau du système d’exploitation, cette approche est déconseillée, car elle n’est pas connu pour améliorer les performances d’e/s.

Tenez compte des deux approches différentes pour la connexion de plusieurs disques en fonction de si vous souhaitez hiérarchiser les performances des opérations de lecture ou d’écriture des opérations pour votre base de données :

- **Oracle ASM sur son propre** est susceptible d’entraîner des meilleures performances des opérations d’écriture, mais les pires Ops ES/s pour les opérations de lecture par rapport à l’approche à l’aide de baies de disques. L’illustration suivante représente logiquement cette disposition.  
    ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

>[AZURE.IMPORTANT] Évaluer le compromis entre les performances en écriture et les performances de lecture d’une base au cas par cas. Les résultats réels peuvent varier lorsque vous utilisez cette option.

### <a name="high-availability-and-disaster-recovery-considerations"></a>Considérations de récupération hautes disponibilité et reprise après sinistre

Lors de l’utilisation de la base de données Oracle sur des machines virtuelles Azure, vous êtes responsable de la mise en œuvre d’une solution de récupération après sinistre et de disponibilité élevée pour éviter les temps d’arrêt. Vous êtes également chargé de sauvegarder vos données et des applications.

Haute disponibilité et reprise après sinistre pour Oracle Database Enterprise Edition (sans RAC) sur Azure peut être obtenue à l’aide de [Data Guard, de protection de données Active](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)ou [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), avec deux bases de données dans les deux machines virtuelles séparées. Les deux ordinateurs virtuels doivent être dans le même [service de cloud](virtual-machines-linux-classic-connect-vms.md) et le même [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) pour garantir qu’ils peuvent accéder à eux sur l’adresse IP permanente privé.  En outre, nous vous recommandons de placer les ordinateurs virtuels dans le même [jeu de disponibilité](virtual-machines-windows-manage-availability.md) pour permettre d’Azure pour les placer dans des domaines d’erreur distinct et la mise à niveau des domaines. Uniquement des ordinateurs virtuels dans le même service de cloud peut faire partie du même jeu de disponibilité. Chaque machine virtuelle doit avoir au moins 2 Go de mémoire et 5 Go d’espace disque.

Avec Oracle Data Guard, la haute disponibilité peut être obtenue avec une base de données primaire dans une machine virtuelle, une base de données secondaire (de secours) sur un autre ordinateur virtuel et la réplication à sens unique entre eux. Le résultat est un accès en lecture à la copie de la base de données. Avec Oracle GoldenGate, vous pouvez configurer une réplication bidirectionnelle entre deux bases de données. Pour savoir comment configurer une solution de haute disponibilité pour vos bases de données à l’aide de ces outils, consultez la documentation [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) et [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) sur le site Web d’Oracle. Si vous devez accès en lecture-écriture à la copie de la base de données, vous pouvez utiliser [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##<a name="oracle-weblogic-server-virtual-machine-images"></a>Images de machine virtuelle WebLogic Server Oracle

-  **Le clustering est pris en charge sur Enterprise Edition uniquement.** Vous êtes autorisé à utiliser le clustering WebLogic uniquement lorsque vous utilisez l’édition entreprise de WebLogic Server. N’utilisez pas la gestion de clusters avec WebLogic Server Standard Edition.

-  **Des délais d’expiration de connexion :** Si votre application repose sur les connexions aux points de terminaison publics d’un autre service de cloud Azure (par exemple, un service de niveau de base de données), Azure peut fermer ces connexions ouvertes après quatre minutes d’inactivité. Cela peut affecter les fonctionnalités et les applications basées sur les pools de connexion, car les connexions sont inactifs au-delà de cette limite peuvent rester n’est plus valides. Si cela affecte votre application, envisagez d’activer la logique « keep-alive » sur vos pools de connexion.

    Si un point de terminaison est *interne* à votre déploiement de service cloud Azure (par exemple, un ordinateur autonome de virtuel de base de données dans le *même* service de cloud que vos machines virtuelles de WebLogic), puis la connexion est directe et ne dépend pas de l’équilibreur de charge Azure et n’est donc pas soumis à un délai d’expiration de la connexion.

-  **Multidiffusion UDP n’est pas pris en charge.** Azure prend en charge les monodiffusion UDP, mais pas la multidiffusion ou diffusion. WebLogic Server est en mesure de s’appuient sur les fonctionnalités de monodiffusion UDP d’Azure. Pour mieux résultats fier de monodiffusion UDP, il est recommandé que la taille de cluster WebLogic restent statiques, ou conservés avec pas plus de 10 serveurs gérés inclus dans le cluster.

-  **WebLogic Server attend des ports publics et privés soient les mêmes pour l’accès de T3 (par exemple, lorsque vous utilisez Enterprise JavaBeans).** Imaginez un scénario à plusieurs niveaux où une application de service de couche (EJB) est en cours d’exécution sur un cluster WebLogic Server consistant en deux ou plusieurs serveurs gérés, dans un service cloud nommé **SLWLS**. Le niveau client est dans un service de nuage différent, en exécutant un programme Java simple, essayez d’appeler EJB dans la couche de service. Dans la mesure où elle est nécessaire équilibrer la couche de service, un point de terminaison public avec équilibrage de charge doit être créé pour les ordinateurs virtuels du cluster WebLogic Server. Si le port privé que vous spécifiez pour ce point de terminaison est différent du port public (par exemple, 7006:7008), une erreur semblable à la suivante se produit :

        [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

        Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

    C’est parce que pour un accès à distance de T3, WebLogic Server attend le port d’équilibrage de la charge et le port du serveur WebLogic géré soient les mêmes. Dans le cas ci-dessus, le client accède au port 7006 (le port d’équilibrage de la charge) et le serveur géré est à l’écoute sur 7008 (port privé). Cette restriction s’applique uniquement aux accès T3, non HTTP.

    Pour éviter ce problème, appliquez l’une des solutions suivantes :

    -  Utiliser le même numéro de port publics et privés pour équilibrage de la charge de points de terminaison dédiée à l’accès de T3.

    -  Incluez le paramètre suivant de la machine virtuelle Java lors du démarrage de WebLogic Server :

            -Dweblogic.rjvm.enableprotocolswitch=true

Pour plus d’informations, voir la base de connaissances l’article **860340.1** à <http://support.oracle.com>.

-  **Dynamique de clustering et limitations d’équilibrage de la charge.** Supposons que vous vouliez utiliser un cluster dynamique dans WebLogic Server et exposer via un unique, public équilibrage point de terminaison dans Azure. Pour ce faire tant que vous utilisez un numéro de port fixe pour chacun des serveurs gérés (ne sont pas automatiquement affectées à partir d’une plage) et ne démarrez pas plus de serveurs gérés à des ordinateurs, l’administrateur effectue le suivi (c'est-à-dire, pas plus d’un serveur géré par machine virtuelle). Si votre configuration se traduit par davantage de serveurs WebLogic démarrage que des ordinateurs virtuels (c'est-à-dire, où plusieurs instances WebLogic Server partagent la même machine virtuelle), puis il n’est pas possible pour plusieurs de ces instances de serveurs WebLogic Server à lier à un numéro de port donné – autres sur cet ordinateur virtuel échoue.

    En revanche, si vous configurez le serveur d’administration pour affecter automatiquement des numéros de port uniques à ses serveurs gérés, puis l’équilibrage de charge n’est pas possible car Azure ne gère pas le mappage d’un seul port public à plusieurs ports privés, qui serait nécessaire pour cette configuration.

-  **Plusieurs instances de Weblogic Server sur une machine virtuelle.** Selon les besoins de votre déploiement, vous pouvez envisager la possibilité d’exécuter plusieurs instances de WebLogic Server sur la même machine virtuelle, si la machine virtuelle est insuffisante. Par exemple, sur une machine virtuelle de taille moyenne, qui contient deux cœurs, vous pouvez choisir d’exécuter deux instances de WebLogic Server. Notez cependant que nous recommandons quand même d’éviter d’introduire des points de défaillance uniques dans votre architecture, ce qui serait le cas si vous avez utilisé un ordinateur virtuel qui exécute plusieurs instances de WebLogic Server. À l’aide d’au moins deux machines virtuelles peut être une meilleure approche, et chacun de ces ordinateurs virtuels pourrait alors exécuter plusieurs instances de WebLogic Server. Chacune de ces instances de WebLogic Server serait encore partie du même cluster. Notez, cependant, il est actuellement pas possible d’utiliser Azure aux points de terminaison de l’équilibrage de charge qui sont exposés par ces déploiements WebLogic Server au sein de la même machine virtuelle, comme équilibreur de charge Azure nécessite les serveurs à charge équilibrée à répartir entre les machines virtuelles uniques.

##<a name="oracle-jdk-virtual-machine-images"></a>Images de machine virtuelle JDK Oracle

-  **JDK 6 et 7 dernières mises à jour.** Alors que nous vous recommandons d’utiliser la version publique plus récents et la prise en charge de Java (Java actuellement 8), Azure, JDK 6 et 7 images disponibles. Ceci est conçu pour les applications héritées qui ne sont pas encore prêtes à être mis à niveau vers JDK 8. Tandis que les mises à jour des images JDK précédentes ne soit plus disponibles pour le grand public, compte tenu du partenariat Microsoft pour Oracle, le JDK 6 et 7 images fournies par Azure doivent contenir une plus récente mise à jour non public qui est normalement proposé par Oracle pour seulement un groupe de clients de prise en charge d’Oracle. Nouvelles versions des images JDK seront disponibles au fil du temps avec les versions mises à jour de JDK 6 et 7.

    Le JDK disponible dans ce JDK de 6 et 7 images et les ordinateurs virtuels et les images dérivés de celles-ci, ne peut être utilisé dans Azure.

-  **JDK de 64 bits.** Les images de machine virtuelle Oracle WebLogic Server et les images de machine virtuelle JDK d’Oracle fournis par Azure contiennent les versions 64 bits de Windows Server et le JDK.

##<a name="additional-resources"></a>Ressources supplémentaires
[Images de machine virtuelle d’Oracle pour Azure](virtual-machines-linux-classic-oracle-images.md)
