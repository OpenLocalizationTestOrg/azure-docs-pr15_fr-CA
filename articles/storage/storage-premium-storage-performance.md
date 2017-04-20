<properties
    pageTitle="Le stockage Azure Premium : Conception de performances | Microsoft Azure"
    description="Concevoir des applications hautes performances à l’aide de prime le stockage Azure. Stockage de prime offre la prise en charge de disque hautes performances et à faible latence pour les charges d’e/S intensives sur Azure Virtual Machines en cours d’exécution."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>

# <a name="azure-premium-storage-design-for-high-performance"></a>Stockage de prime Azure : Conception de hautes performances

## <a name="overview"></a>Vue d’ensemble  
Cet article fournit des instructions pour la création d’applications hautes performances à l’aide de prime le stockage Azure. Vous pouvez utiliser les instructions fournies dans ce document combiné avec les procédures recommandées applicables aux technologies utilisées par votre application. Pour illustrer les instructions, nous avons utilisé SQL Server exécuté sur un stockage de prime à titre d’exemple dans ce document.

Tandis que nous traitent des scénarios de performances pour la couche de stockage de cet article, vous devez optimiser la couche d’application. Par exemple, si vous hébergez une batterie de serveurs SharePoint sur le stockage Azure Premium, vous pouvez utiliser les exemples de SQL Server à partir de cet article afin d’optimiser le serveur de base de données. En outre, optimiser le serveur Web et serveur d’Application pour obtenir les meilleures performances de la batterie de serveurs SharePoint.

Cet article vous aidera à réponse suite à des questions courantes sur l’optimisation des performances des applications sur le stockage Azure Premium,

-   Comment mesurer les performances de votre application ?  
-   Pourquoi ne vous voyez pas attendu hautes performances ?  
-   Les facteurs qui influencent les performances de votre application sur le stockage de la prime ?  
-   Comment ces facteurs n’influencent pas les performances de votre application sur le stockage de la prime ?  
-   Comment pouvez-vous optimiser pour les e/s, bande passante et la latence ?  

Nous avons fourni des présentes lignes directrices spécifiquement pour le stockage de la prime dans la mesure où les charges de travail en cours d’exécution sur le stockage de prime sont extrêmement sensibles aux performances. Nous avons fourni des exemples le cas échéant. Vous pouvez également appliquer certaines de ces lignes directrices aux applications s’exécutant sur des machines virtuelles de IaaS avec des disques de stockage Standard.

Avant de commencer, si vous ne connaissez pas le stockage de la prime, lisez tout d’abord la [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](storage-premium-storage.md) l’article et l' [évolutivité de stockage Azure Premium et des objectifs de Performance](storage-scalability-targets.md#premium-storage-accounts).

## <a name="application-performance-indicators"></a>Indicateurs de performances des applications  
Nous évaluer si une application s’exécute correctement ou non à l’aide de tels que des indicateurs de performance, la vitesse à laquelle une application traite une demande de l’utilisateur, la quantité de données est une application de traitement par la demande, le nombre de requêtes est un traitement de l’application dans un certain délai, combien de temps un utilisateur doit attendre d’obtenir une réponse après avoir envoyé la demande. Les modalités techniques de ces indicateurs de performance sont, e/s, le débit ou la bande passante et la latence.

Dans cette section, nous allons aborder les indicateurs de performance courants dans le contexte de stockage de la prime. Dans la section suivante, collecte des exigences de l’Application, vous apprendrez comment mesurer ces indicateurs de performance pour votre application. Plus loin dans l’optimisation des performances de l’Application, vous allez découvrir les facteurs qui affectent ces indicateurs de performance et des recommandations pour optimiser les.

## <a name="iops"></a>OPS ES/S  
Ops ES/s est le nombre de demandes qui envoie votre application sur les disques de stockage en une seconde. Une opération d’entrée/sortie peut lire ou écrire, séquentielle ou aléatoire. Comme un site Web de vente au détail en ligne, les applications OLTP doivent traiter immédiatement les nombreuses demandes utilisateur simultanées. Les demandes de l’utilisateur sont Insérer et mettre à jour les transactions de la base de données intensives, dont l’application doit traiter rapidement. Par conséquent, les applications OLTP requièrent IOPS très élevé. Ces applications gèrent des millions de requêtes d’e/s petites et aléatoires. Si vous avez une telle application, vous devez concevoir l’infrastructure d’application pour optimiser les e/s. Dans la section ultérieure, *Optimisation des performances de l’Application*, nous décrivent en détail tous les facteurs que vous devez prendre en compte pour obtenir des e/s élevé.

Lorsque vous attachez un disque de stockage de prime à votre échelle élevée VM, dispositions Azure pour vous un garantie nombre des Ops ES/s conformément à la spécification de disque. Par exemple, un disque P30 dispositions 5000 Ops ES/s. Chaque échelle de grande taille de mémoire virtuelle a également une limite d’e/s spécifique qu’il peut supporter. Par exemple, un ordinateur virtuel Standard de GS5 a 80 000 IOPS limiter.

## <a name="throughput"></a>Débit  
Le débit ou la bande passante est la quantité de données que votre application envoie pour les disques de stockage dans un intervalle spécifié. Si votre application effectue les opérations d’entrée/sortie avec grandes tailles d’e/s, il nécessite un débit élevé. Applications d’entrepôt de données ont tendance à émettre des opérations d’analyse intensives qui accèdent à grandes portions de données à la fois et généralement les opérations en bloc. En d’autres termes, ces applications nécessitent un débit plus élevé. Si vous avez une telle application, vous devez concevoir l’infrastructure pour optimiser le débit. Dans la section suivante, nous décrivent en détail les facteurs que vous devez régler pour atteindre cet objectif.

Lorsque vous l’attachez un disque de stockage de prime à une échelle élevée VM, dispositions Azure débit en fonction de cette spécification de disque. Par exemple, un disque P30 dispositions 200 Mo par seconde disquette de débit. Chaque échelle de grande taille de mémoire virtuelle a également en tant que limite de débit spécifique qu’il peut supporter. Par exemple, ordinateur virtuel Standard de GS5 a un débit maximal de 2 000 Mo par seconde.

Il existe une relation entre le débit et les e/s, comme le montre la formule ci-dessous.

![](media/storage-premium-storage-performance/image1.png)

Par conséquent, il est important de déterminer les valeurs optimales de débit et des Ops ES/s requis par votre application. Lorsque vous essayez d’optimiser une, l’autre également obtient affecté. Dans une section ultérieure, *Optimisation des performances de l’Application*, nous aborderons dans plus de détails sur l’optimisation des e/s et le débit.

## <a name="latency"></a>Temps de latence  
La latence est le temps nécessaire à une application de recevoir une demande unique, l’envoyer sur les disques de stockage et envoie la réponse au client. Il s’agit d’une mesure critique des performances d’une application en plus des e/s et le débit. La latence d’un disque de stockage de prime est le temps que nécessaire pour récupérer les informations d’une demande et la communiquent à votre application. Stockage de prime fournit des latences de faibles cohérence. Si vous activez hôte ReadOnly la mise en cache sur les disques de stockage en prime, vous pouvez obtenir beaucoup plus faible latence de lecture. Nous aborderons la mise en cache du disque plus en détail dans la section ultérieure sur *l’Optimisation des performances de l’Application*.

Lorsque vous optimisez votre application pour obtenir des Ops ES/s et débit plus élevés, elle affectera le temps de latence de votre application. Après le réglage des performances de l’application, toujours évaluer la latence de l’application pour éviter un comportement inattendu de latence élevée.

## <a name="gather-application-performance-requirements"></a>Recueillir les exigences de performances d’Application  
La première étape de la conception d’applications hautes performances en cours d’exécution sur le stockage Azure Premium est, de bien comprendre les exigences de performances de votre application. Après avoir rassemblé des exigences de performances, vous pouvez optimiser votre application pour obtenir les performances optimal.

Dans la section précédente, nous avons expliqué les indicateurs de performance courants, Ops ES/s, le débit et la latence. Vous devez identifier ces indicateurs de performances sont critiques à votre application pour fournir l’expérience utilisateur souhaitée. Par exemple, e/s haute est indispensable pour les applications OLTP traitement des millions de transactions par seconde. Considérant que, dans le haut débit est essentiel pour les applications d’entrepôt de données de traitement de grandes quantités de données dans une seconde. Extrêmement faible latence est cruciale pour les applications en temps réel telles que la vidéo live en continu des sites Web.

Ensuite, mesurer les exigences d’optimiser les performances de votre application tout au long de sa durée de vie. Utilisez la liste de contrôle d’exemple ci-dessous sous la forme d’un départ. Enregistrer les exigences de performances normale, les périodes de charge de travail de pointe et heures creuses. En identifiant les conditions requises pour tous les niveaux de charges de travail, vous serez en mesure de déterminer l’exigence de performance globale de votre application. Par exemple, la charge de travail normale d’un site Web de commerce électronique seront les transactions qu’il est utilisé au cours de la plupart des jours dans une année. Les pics de charge du site Web seront les transactions qu’il est utilisé au cours des vacances ou des événements de vente spécial. Les pics de charge est généralement expérimentés pour une période limitée, mais peut nécessiter que votre application à l’échelle de deux ou plusieurs fois son fonctionnement normal. Découvrez le centile 50, centile 90 en matière de 99 centile. Cela permet d’éliminer les valeurs aberrantes dans les exigences de performances et vous pouvez concentrer vos efforts sur l’optimisation pour les valeurs.

**Aide-mémoire des conditions requises application Performance**

| **Exigences de performances** | **50 centile** | **Centile 90** | **99 centile** |
|---|---|---|---|
| Max. Transactions par seconde | | | |
| Opérations de lecture de %            | | | |
| Opérations d’écriture %           | | | |
| % Des opérations aléatoires          | | | |
| Opérations séquentielles %      | | | |
| Taille de la demande d’e/s              | | | |
| Débit moyen           | | | |
| Max. Débit              | | | |
| Min. Temps de latence                 | | | |
| Latence moyenne              | | | |
| Max. UNITÉ CENTRALE                     | | | |
| Charge moyenne du processeur                  | | | |
| Max. Mémoire                  | | | |
| Mémoire moyenne               | | | |
| Profondeur de la file d’attente                  | | | |

>**Remarque importante :**  
>Vous devez envisager la mise à l’échelle de ces chiffres en fonction de la croissance future de votre application. Il est conseillé de planifier la croissance à l’avance, car il pourrait être plus difficile à modifier l’infrastructure pour améliorer les performances ultérieures.

Si vous possédez une application existante et que vous souhaitez déplacer vers le stockage de la prime, tout d’abord créer la liste de contrôle ci-dessus pour l’application existante. Ensuite, créez un prototype de votre application sur le stockage de la prime et concevoir l’application en fonction des indications décrites dans l' *Optimisation des performances de l’Application* dans une section ultérieure de ce document. La section suivante décrit les outils que vous pouvez utiliser pour collecter les mesures de performances.

Créer une liste de contrôle similaire à votre application existante pour le prototype. À l’aide des outils de Benchmarking, vous pouvez simuler les charges de travail et mesurer les performances de l’application de prototype. De [Benchmarking](#benchmarking) pour en savoir plus, consultez la section. Ainsi, vous pouvez déterminer si Premium stockage peut correspondre ou dépasser les exigences de performances de votre application. Vous pouvez implémenter les mêmes instructions pour votre application de production.

### <a name="counters-to-measure-application-performance-requirements"></a>Compteurs pour mesurer les performances d’application requises  
La meilleure façon de mesurer des exigences de performances de votre application, consiste à utiliser des outils d’analyse des performances fournies par le système d’exploitation du serveur. Vous pouvez utiliser l’Analyseur de performances pour Windows et iostat pour Linux. Ces outils de capture les compteurs correspondant à chaque mesure indiqué dans la section ci-dessus. Vous devez capturer les valeurs de ces compteurs lorsque votre application s’exécute ses normal, les charges de travail de pointe et heures creuses.

Les compteurs de performance sont disponibles pour le processeur, la mémoire et, de chaque disque logique et disque physique de votre serveur. Lorsque vous utilisez des disques de stockage premium avec un ordinateur virtuel, les compteurs de disque physique sont pour chaque disque de stockage de prime, et sont des compteurs de disque logique pour chaque volume créé sur les disques de stockage premium. Vous devez capturer les valeurs pour les disques qui hébergent votre charge de travail d’application. S’il existe un mappage un à un entre les disques physiques et logiques, vous pouvez consulter les compteurs de disque physique ; Sinon, consultez les compteurs de disque logique. Sous Linux, la commande iostat génère un rapport d’utilisation du processeur et du disque. Ce rapport fournit des statistiques par le périphérique physique ou la partition. Si vous avez un serveur de base de données avec ses données et les journaux sur des disques distincts, collecter ces données pour les deux disques. Tableau ci-dessous décrit les compteurs de disque, de processeur et de mémoire :

| Compteur | Description | Analyseur de performances | Iostat |
|---|---|---|---|
| **Ops ES/s ou Transactions par seconde** | Nombre de demandes d’e/s envoyées au disque de stockage par seconde. | Lectures disque/s <br> Écritures disque/s | TPS <br> r/s <br> w/s |
| **Lectures et écritures** | % de lectures et d’écritures effectuées sur le disque. | % Temps lecture du disque <br> % Temps écriture du disque | r/s <br> w/s |
| **Débit** | Quantité de données lues ou écrites sur le disque par seconde. | Octets lus/s de disque <br> Octets d’écriture disque/s | kB_read/s <br> kB_wrtn/s |
| **Temps de latence** | Durée totale d’une demande d’e/s de disque. | Moyen disque s/lecture <br> Moyenne disque s/écriture | attendre <br> svctm |
| **Taille des e/s** | La taille des e/s des problèmes sur les disques de stockage de demandes. | Moyenne disque, octets/lecture <br> Moyenne disque, octets/écriture | avgrq-sz |
| **Profondeur de la file d’attente** | Nombre d’e/s en attente les demandes en attente de lecture d’écran ou écrites sur le disque de stockage. | Longueur de file d’attente du disque actuel | avgqu-sz |
| **Max. Mémoire** | Quantité de mémoire requise pour le fonctionnement des applications | % Octets dédiés utilisés | Utilisez vmstat |
| **Max. UNITÉ CENTRALE** | Montant du processeur requis pour le fonctionnement des applications | % Temps processeur | % util |

En savoir plus sur [iostat](http://linuxcommand.org/man_pages/iostat1.html) et [Analyseur de performances](https://msdn.microsoft.com/library/aa645516.aspx).


## <a name="optimizing-application-performance"></a>Optimisation des performances de l’Application  
Les principaux facteurs qui influencent les performances d’une application en cours d’exécution sur le stockage de prime sont de Nature de requêtes e/s, taille de la mémoire virtuelle, la taille du disque, nombre de disques, la mise en cache du disque, le Multithreading et profondeur de file d’attente. Vous pouvez contrôler certains de ces facteurs avec boutons fournis par le système. La plupart des applications peuvent vous fournir pas une option pour modifier la taille d’e/s et de la profondeur de file d’attente de directement. Par exemple, si vous utilisez SQL Server, vous ne pouvez pas choisir la profondeur de file d’attente et de taille d’e/s. SQL Server choisit les e/s taille et la file d’attente de profondeur valeurs optimales pour obtenir les meilleures performances. Il est important de comprendre les effets de ces deux types de facteurs sur les performances de votre application, afin que vous pouvez configurer les ressources appropriées pour répondre aux besoins de performances.

Dans cette section, reportez-vous à l’aide-mémoire des conditions requises application que vous avez créée pour identifier combien vous avez besoin optimiser les performances de votre application. Sur cette base, vous serez en mesure de déterminer quels facteurs à partir de cette section vous devez régler. Pour assister les effets de chaque facteur sur les performances de votre application, exécuter des outils d’évaluation des performances sur l’installation de votre application. Reportez-vous à la section de [Benchmarking](#Benchmarking) à la fin de cet article pour obtenir la procédure exécuter les outils d’évaluation courants sous Windows et Linux VMs.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Optimisation des e/s, le débit et la latence en un clin de œil  
Le tableau ci-dessous résume les étapes pour optimiser les e/s, le débit et la latence et tous les facteurs de performances. Les sections suivantes de ce résumé décrit chaque facteur est beaucoup plus en détail.

| | **OPS ES/S** | **Débit** | **Temps de latence** |
|---|---|---|---|
| **Exemple de scénario** | Application OLTP de l’entreprise nécessitant des transactions très élevées taux par seconde.                                                                                                                                 | Données de l’entreprise application traitement de grandes quantités de données de l’entrepôt. | Dans les applications nécessitant des réponses instantanées aux demandes des utilisateurs, comme les jeux en ligne en temps réel. |
| Facteurs de performances  | | | |
| **Taille des e/s** | Taille d’e/s donne plus d’IOPS.                                                                                                                                                                           | Taille d’e/s à permet d’obtenir un débit plus élevé. | |
| **Taille de mémoire virtuelle** | Utilisez une taille de mémoire virtuelle qui offre des Ops ES/s supérieur à vos besoins d’application. Voir les tailles de la machine virtuelle et leurs limites d’IOPS ici. | Utilisez une taille de mémoire virtuelle avec la limite de débit supérieur à vos besoins d’application. Voir les tailles de la machine virtuelle et les limites du débit. | Utilisez une taille de mémoire virtuelle qu’offres échelle limites supérieures à vos besoins d’application. Voir les tailles de la machine virtuelle et leurs limites ici. |
| **Taille du disque** | Utilisez une taille de disque offre des Ops ES/s supérieur à vos besoins d’application. Voir les tailles de disque et de leurs limites IOPS ici. | Utilisez une taille de disque avec une limite de débit supérieur à vos besoins d’application. Voir les disques dont la taille et les limites du débit. | Utilisez une taille de disque qu’offres échelle limites supérieures à vos besoins d’application. Voir les tailles de disque et leurs limites ici. |
| **Machine virtuelle et les limites d’échelle de disque** | Limite d’e/s de la taille de la mémoire virtuelle choisie doit être supérieure à IOPS total, pilotée par des disques de stockage premium attachés à. | Limite de débit de la taille de la mémoire virtuelle choisie doit être supérieur au débit total, piloté par prime les disques de stockage attachés. | Limites d’échelle de la taille de la mémoire virtuelle choisi doivent être supérieurs aux limites d’échelle total premium joint des disques de stockage. |
| **La mise en cache du disque** | Activer le Cache de ReadOnly sur des disques de stockage premium avec lourdes opérations de lecture pour obtenir des e/s de lecture supérieur. | | Activer le Cache de ReadOnly sur des disques de stockage premium avec des opérations lourdes prêtes pour obtenir des latences de lecture très faible. |
| **Entrelacement de disques** | Utilisez plusieurs disques et répartir les pour obtenir une limite Ops ES/s et un débit plus élevée combinée. Notez que la limite combinée par machine virtuelle doit être supérieure aux limites combinées de disques de prime attaché. | |
| **Taille de répartition** | Plus petite taille de répartition pour aléatoire petit modèle d’e/s dans des applications OLTP. Par exemple, utiliser la taille de répartition de 64 Ko pour application OLTP de SQL Server. | Plus grande taille de répartition pour le modèle d’e/s grande séquentiel dans les applications de Data Warehouse. Par exemple, utiliser le taille de bande de 256 Ko pour les applications d’entrepôt de données de SQL Server. | |
| **Le multithreading** | Utilisez le multithreading pour pousser un plus grand nombre de demandes au stockage de prime qui entraîne des Ops ES/s et débit plus élevés. Par exemple, dans SQL Server définir une valeur MAXDOP élevée pour allouer plus de processeurs pour SQL Server. | |
| **Profondeur de la file d’attente** | Plus grande profondeur de file d’attente génère des e/s plus élevée. | Plus grande profondeur de file d’attente permet d’obtenir un débit plus élevé. | Plus petite longueur de file d’attente génère une latence inférieure. |

## <a name="nature-of-io-requests"></a>Nature des demandes d’e/s  
Une demande d’e/s est une unité d’opération d’entrée/sortie à l’exécution de votre application. Identifiant la nature des demandes d’e/s, aléatoires ou séquentielles, de lecture ou écriture, petite ou grande, vous permettra de déterminer les exigences de performances de votre application. Il est très important de comprendre la nature des demandes d’e/s, à prendre les bonnes décisions lors de la conception de votre infrastructure de l’application.

Taille des e/s est un des facteurs plus importants. La taille d’e/s est la taille de la demande d’entrée/sortie générée par votre application. La taille d’e/s a un impact significatif sur les performances, en particulier sur les IOPS et la bande passante que l’application est en mesure de réaliser. La formule suivante montre la relation entre les e/s, taille d’e/s et de bande passante/débit.  
    ![](media/storage-premium-storage-performance/image1.png)

Certaines applications vous permettent de vous permettent de modifier leur taille d’e/s, tandis que certaines applications ne le font pas. Par exemple, SQL Server détermine la taille d’e/s optimale lui-même et ne fournit pas d’utilisateurs avec des boutons pour le modifier. D’autre part, Oracle fournit un paramètre appelé [DB\_bloquer\_taille](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) à l’aide de laquelle vous pouvez configurer la taille de la demande d’e/s de la base de données.

Si vous utilisez une application qui ne vous permet pas de modifier la taille d’e/s, vous pouvez utiliser les instructions de cet article pour optimiser les performances, indicateurs de performance clés qui correspond le mieux à votre application. Par exemple,

-   Une application OLTP génère des millions de requêtes d’e/s petites et aléatoires. Pour gérer ces types de demandes d’e/s, vous devez concevoir votre infrastructure de l’application afin d’obtenir des Ops ES/s supérieur.  
-   Un data warehouse application génère les demandes d’e/s séquentielles et les grands. Pour gérer ces types de demandes d’e/s, vous devez concevoir votre infrastructure de l’application pour obtenir plus de bande passante ou de débit.

Si vous utilisez une application qui vous permet de modifier la taille d’e/s, utilisez cette règle générale pour la taille d’e/s en plus des autres indications de performances,

-   Taille d’e/s pour obtenir plus d’IOPS. Par exemple, 8 Ko pour une application OLTP.  
-   Taille d’e/s pour obtenir de la bande passante débit. Par exemple, 1 024 Ko pour une application de data warehouse.

Voici un exemple de comment vous pouvez calculer les e/s et un débit/bande passante de votre application. Considérez une application à l’aide d’une disquette de P30. Le maximum d’e/s et débit/bande passante un disque P30 peuvent atteindre est 5000 IOPS et 200 Mo par seconde respectivement. Si votre application requiert l’IOPS maximal à partir du disque de P30 et si vous utilisez une taille d’e/s inférieure à 8 Ko, la bande passante qui en résulte, vous ne pourrez pas obtenir est désormais de 40 Mo par seconde. Toutefois, si votre application nécessite la débit/bande passante maximale à partir du disque de P30, et que vous utilisez une taille d’e/s supérieure à 1024 Ko, les e/s qui en résulte sera moins, 200 Ops ES/s. Par conséquent, régler la taille d’e/s telles qu’il respecte à la fois vos Ops ES/s et débit/bande passante nécessaires à une application. Tableau ci-dessous résume les différentes tailles d’e/s et leur e/s et le débit correspondant pour un disque P30.

| **Requis par l’application** | **Taille des e/s** | **OPS ES/S** | **Débit/bande passante** |
|-----------------------------|--------------|----------|--------------------------|
| Ops ES/s max                    | 8 KO         | 5 000    | 40 Mo par seconde         |
| Débit maximal              | 1024 KO      | 200      | 200 Mo par seconde        |
| Max Throughput + e/s élevées  | 64 KO        | 3 200    | 200 Mo par seconde        |
| Max IOPS + haut débit  | 32 KO        | 5 000    | 160 Mo par seconde        |

Pour obtenir le nombre d’IOPS et de bande passante supérieure à la valeur maximale d’un disque de stockage de prime unique, utilisez plusieurs disques premium réparties ensemble. Par exemple, répartir les deux disques de P30 pour obtenir une e/s combinée de 10 000 IOPS ou un débit combiné de 400 Mo par seconde. Comme expliqué dans la section suivante, vous devez utiliser une taille de mémoire virtuelle qui prend en charge la combinaison Ops ES/s et un débit de disque.

>**Remarque :**  
>Si vous augmentez les e/s, soit l’autre augmente aussi le débit, assurez-vous que vous ne cliquez pas sur le débit ou limites d’e/s du disque ou de mémoire virtuelle lorsque vous augmentez le d’eux.

Pour assister les effets de taille d’e/s sur les performances de l’application, vous pouvez exécuter des outils d’évaluation des performances sur votre machine virtuelle et les disques. Création de plusieurs séries de tests et permet de voir l’impact des tailles d’e/s différentes pour chaque exécution. Reportez-vous à la section de [Benchmarking](#Benchmarking) à la fin de cet article pour plus de détails.

## <a name="high-scale-vm-sizes"></a>Tailles de machine virtuelle de grande échelle  
Lorsque vous démarrez la conception d’une application, une des premières choses à faire est choisir une machine virtuelle pour héberger votre application. Stockage de prime est fourni avec des tailles de VM d’échelle élevées qui peuvent exécuter des applications nécessitant une puissance de calcul supérieure et un haut performances e/s de disque local. Ces ordinateurs virtuels offrent des processeurs plus rapides, un taux plus élevé de la mémoire vers le centre et un Solid-State Drive (SSD) pour le disque local. La série DS, DSv2 et GS VMs sont des exemples de grande échelle VMs prenant en charge le stockage de la prime.

Grande échelle VMs sont disponibles dans des tailles différentes avec un nombre différent de cœurs du processeur, mémoire, du système d’exploitation et taille du disque temporaire. La taille de chaque machine virtuelle possède également un nombre maximal de disques de données que vous pouvez attacher à la machine virtuelle. Par conséquent, la taille de mémoire virtuelle choisie affecte la quantité de traitement, mémoire, et la capacité de stockage est disponible pour votre application. Il affecte également le calcul et les coûts du stockage. Par exemple, voici les spécifications de la taille maximale de la machine virtuelle en une série DS, DSv2 série et une série de GS :

| Taille de mémoire virtuelle | Coeurs de processeur | Mémoire | Tailles de disque de machine virtuelle | Max. disques de données | Taille du cache | OPS ES/S | Limites de bande passante d’e/s de Cache |
|---|---|---|---|---|---|---|---|
| Standard_DS14 | 16 | 112 GO | SYSTÈME D’EXPLOITATION = 1 023 GO <br> SSD local = 224 Go | 32 | 576 GO | 50 000 IOPS <br> 512 Mo par seconde | 4 000 IOPS et 33 Mo par seconde |
| Standard_GS5 | 32 | 448 GO | SYSTÈME D’EXPLOITATION = 1 023 GO <br> SSD local = 896 Go | 64 | 4224 GO | 80 000 IOPS <br> 2 000 Mo par seconde | 5 000 IOPS et 50 Mo par seconde |

Pour afficher une liste complète de toutes les tailles d’Azure VM disponibles, reportez-vous à la [taille de la mémoire virtuelle de Windows](../virtual-machines/virtual-machines-windows-sizes.md) ou [Linux VM tailles](../virtual-machines/virtual-machines-linux-sizes.md). Choisissez une taille de mémoire virtuelle qui peut respecter et adapter à vos besoins de performances d’application souhaitée. En outre, prendre en compte suivant des considérations importantes lors de la sélection de tailles de machine virtuelle.


*Limites d’échelle*  
Les limites maximales d’Ops ES/s par la machine virtuelle et par disque sont différents et indépendants des uns des autres. Assurez-vous que l’application est conduite Ops ES/s dans les limites de la machine virtuelle, ainsi que les disques de prime attachés. Dans le cas contraire, les performances des applications bénéficieront de la limitation.

Par exemple, supposons qu’une application est un maximum de 4 000 IOPS. Pour ce faire, vous devez configurer un disque P30 sur un ordinateur virtuel de DS1. Le disque P30 peut fournir jusqu'à 5 000 IOPS. Toutefois, la machine virtuelle DS1 est limitée à 3 200 Ops ES/s. Par conséquent, les performances de l’application seront contraint par la limite de mémoire virtuelle à 3 200 Ops ES/s, et il y aura de dégradation des performances. Pour éviter cette situation, choisissez une taille de machine virtuelle et le disque sont les deux répondent aux exigences de l’application.

*Coût de fonctionnement*  
Dans de nombreux cas, il est possible que le coût global de l’opération à l’aide du stockage de prime est inférieur à l’aide de stockage Standard.

Par exemple, considérez une application nécessitant 16 000 IOPS. Pour atteindre ces performances, vous aurez besoin d’une norme\_D14 Azure IaaS VM, ce qui peut donner un IOPS maximum de 16 000 à l’aide de 32 disques de 1 To de stockage standard. Chaque disque de stockage standard de 1 to peut atteindre un maximum de 500 Ops ES/s. Le coût estimé de cet ordinateur virtuel par mois sera $1,570. Le coût mensuel de 32 disques de stockage standard sera $1,638. Le coût mensuel total estimé est de $3,208.

Toutefois, si vous hébergés de la même application sur le stockage de la prime, vous devrez une taille plus petite de la machine virtuelle et les disques de stockage moins premium, réduisant ainsi le coût global. Une norme\_DS13 VM peut disposer de la 16 000 e/s à l’aide des quatre disquettes de P30. La machine virtuelle de DS13 a un IOPS maximal de 25,600 et chaque disque P30 a un IOPS maximal de 5 000. En général, cette configuration peut atteindre 5 000 x 4 = 20 000 e/s. Le coût estimé de cet ordinateur virtuel par mois sera $1,003. Le coût mensuel de quatre disques de stockage P30 prime sera $544.34. Le coût mensuel total estimé sera 1,544 $.

Tableau ci-dessous résume la répartition des coûts de ce scénario pour le stockage de prime et de la norme.

| | **Standard** | **Premium** |
|---|---|---|
| **Coût de la machine virtuelle par mois** | 1,570.58 $ (standard\_D14)   | 1,003.66 $ (standard\_DS13) |
| **Coût des disques par mois** | 1,638.40 $ (disques 32 x 1 To) | 544.34 $ (4 disques de x P30) |
| **Coût total par mois**  | $3,208.98 | $1,544.34 |

*Linux Distros*  

Avec le stockage Azure Premium, vous obtenez le même niveau de performances pour les machines virtuelles exécutant Windows et Linux. Nous prenons en charge les diverses versions de Linux distros, et vous pouvez voir la liste complète [ici](../virtual-machines/virtual-machines-linux-endorsed-distros.md). Il est important de noter que différentes distros conviennent mieux aux différents types de charges de travail. Vous verrez différents niveaux de performances selon le distro que votre charge de travail est en cours d’exécution. Tester la distros de Linux avec votre application et choisissez celle qui vous convient le mieux.


Lors de l’exécution de Linux avec un stockage de prime, vérifiez les mises à jour les plus récentes sur les pilotes requis pour garantir des performances élevées.

## <a name="premium-storage-disk-sizes"></a>Tailles de disque de stockage de prime  
Stockage de prime Azure propose trois tailles de disque actuellement. La taille de chaque disque a une limite de l’échelle différente pour les e/s, de bande passante et de stockage. Choisissez la taille du disque de stockage de prime selon les exigences de l’application et de l’échelle de grande taille de mémoire virtuelle de droite. Le tableau ci-dessous indique les tailles de trois disques et de leurs fonctions.

| **Type de disque**       | **P10**           | **P20**           | **P30**           |
|---------------------|-------------------|-------------------|-------------------|
| Taille du disque           | Talon 128           | Talon 512           | Talon de 1024 (1 To)   |
| Ops ES/s par disque       | 500               | 2300              | 5000              |
| Débit par disque | 100 Mo par seconde | 150 Mo par seconde | 200 Mo par seconde |

Nombre de disques que vous choisissez dépend du disque la taille choisie. Vous pouvez utiliser un seul disque de P30 ou de plusieurs disques P10 pour répondre aux besoins de votre application. Tenir les considérations relatives au compte indiqué ci-après pour faire le choix.

*Limites d’échelle (Ops ES/s et le débit)*  
Les limites des Ops ES/s et le débit de chaque taille de disque Premium est différent et indépendantes dans les limites d’échelle de machine virtuelle. Assurez-vous que le total Ops ES/s et le débit à partir des disques sont dans les limites d’échelle de la taille de mémoire virtuelle choisie.

Par exemple, si une demande d’application est un maximum de 250 Mo/s de débit et si vous utilisez un ordinateur virtuel de DS4 avec un seul disque de P30. La machine virtuelle de DS4 permettent un débit jusqu'à 256 Mo/s. Toutefois, un seul disque de P30 a la limite de débit de 200 Mo/s. Par conséquent, l’application sera restreinte à 200 Mo/s en raison de la limite du disque. Pour contourner cette limite, la constitution plusieurs disques de données de la machine virtuelle.

>**Remarque :**  
>Pris en charge par le cache de lecture n’est pas inclus dans les e/s de disque et le débit, et donc pas soumis à des limites de disque. Cache a ses limites distinctes d’Ops ES/s et un débit par machine virtuelle.
>
>Par exemple, initialement vos lectures et écritures sont 60 Mo/s et 40 Mo/s respectivement. Au fil du temps, le cache de préchauffage et sert de plus en plus de la lecture à partir du cache. Ensuite, vous pouvez obtenir plu écriture débit à partir du disque.

*Nombre de disques*  
Déterminer le nombre de disques, que vous devez en évaluant les besoins de l’application. La taille de chaque machine virtuelle possède également une limite sur le nombre de disques que vous pouvez attacher à la machine virtuelle. En général, il s’agit de deux fois le nombre de cœurs. Assurez-vous que la taille de mémoire virtuelle que vous choisissez peut prendre en charge le nombre de disques requis.

N’oubliez pas que les disques de stockage de prime ont des capacités supérieures par rapport aux disques de stockage Standard. Par conséquent, si vous faites migrer votre application Azure IaaS VM à l’aide de stockage Standard pour le stockage de la prime, vous devrez probablement moins de disques de prime pour obtenir les performances identique ou supérieur pour votre application.

## <a name="disk-caching"></a>La mise en cache du disque  
Ordinateurs virtuels échelle élevées qui exploitent le stockage Azure Premium ont une technologie de mise en cache de n-tier appelée BlobCache. BlobCache utilise une combinaison de la RAM de la Machine virtuelle et SSD local pour la mise en cache. Ce cache est disponible pour les disques de permanente de stockage de la prime et les disques locaux de machine virtuelle. Par défaut, ce cache est défini en lecture/écriture pour les disques du système d’exploitation et en lecture seule pour les disques de données hébergées sur le stockage de la prime. Avec mise en cache disque activé sur les disques de stockage de la prime, l’échelle élevée VMs peut atteindre des niveaux de performances extrêmement élevés qui dépassent les performances du disque sous-jacent.

>[AZURE.WARNING] Modifier le paramètre de cache d’un disque Azure se détache et rattache le disque cible. S’il s’agit du disque du système d’exploitation, la machine virtuelle est redémarrée. Arrêtez toutes les applications et services qui peuvent être affectés par cette perturbation avant de modifier le paramètre de cache disque.

Pour en savoir plus sur le fonctionne de BlobCache, reportez-vous à l’intérieur de blog [Le stockage Azure Premium](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

Il est important d’activer le cache sur le jeu de disques. Si vous devez permettre la mise en cache du disque sur un disque premium ou pas varient selon le modèle de charge de travail que ce disque doit gérer. Tableau ci-dessous indique la valeur par défaut des paramètres de cache pour les disques du système d’exploitation et des données.

| **Type de disque** | **Le paramètre de Cache par défaut** |
|---|---|
| Disque du système d’exploitation | ReadWrite |
| Disque de données | Aucun |

Voici les paramètres de cache de disque recommandé pour les disques de données,

| **Paramètre de cache de disque** | **Recommandations sur l’utilisation de ce paramètre** |
|---|---|
| Aucun | Configurer du cache de l’hôte en tant qu’aucun des disques d’écriture seule et lourds à l’écriture. |
| En lecture seule | Configurer le cache de l’hôte comme en lecture seule pour les disques en lecture seule et en lecture-écriture. |
| ReadWrite | Configurer le cache de l’hôte comme ReadWrite uniquement si votre application gère correctement l’écriture des données mises en cache permanent disques lorsque cela est nécessaire. |

*En lecture seule*  
En configurant en lecture seule de la mise en cache de données du stockage de prime des disques, vous pouvez parvenir à faible temps de latence en lecture et obtenir des Ops ES/s en lecture et le débit très élevé pour votre application. Il s’agit de deux raisons, d’échéance

1.  Lectures effectuées à partir du cache, qui est de la mémoire de la machine virtuelle et les SSD local, sont beaucoup plus rapides que les lectures à partir du disque de données, qui se trouve sur le stockage blob Azure.  
2.  Stockage de prime ne compte pas les lectures servis à partir du cache, vers l’e/s de disque et le débit. Par conséquent, votre application est en mesure de réaliser les e/s total plus élevé et le débit.

*ReadWrite*  
Par défaut, les disques du système d’exploitation ReadWrite cache sont activé. Nous avons récemment ajouté la prise en charge de ReadWrite la mise en cache des données ainsi que les disques. Si vous utilisez ReadWrite la mise en cache, vous devez disposer d’un moyen approprié pour écrire les données du cache permanent disques. Par exemple, SQL Server gère l’écriture des données mises en cache sur les disques de stockage persistant sur son propre. À l’aide de ReadWrite cache avec une application qui ne gère pas la persistance des données requises peut entraîner des pertes de données, en cas de panne de la machine virtuelle.

Par exemple, vous pouvez appliquer ces instructions à SQL Server en cours d’exécution sur le stockage de prime de la manière suivante,

1.  Configurer les « ReadOnly » cache sur les disques de stockage premium hébergeant les fichiers de données.  
    une barre d’outils.  Le rapide lit cache inférieur de la durée de requête de SQL Server, dans la mesure où les pages de données sont récupérées plus rapidement à partir du cache par rapport à directement à partir des données des disques.  
    b.  Faisant office de lectures du cache, signifie augmenter le débit à partir des disques de données premium. SQL Server peut utiliser ce débit supplémentaire vers la récupération de plusieurs pages de données et d’autres opérations de sauvegarde et de restauration, charges de traitement par lots et reconstructions d’index.  
2.  Configurer « None » mettre en cache sur les disques de stockage premium hébergeant les fichiers journaux.  
    une barre d’outils.  Les fichiers journaux ont principalement les opérations d’écriture-lourds. Par conséquent, ils ne bénéficient pas du cache en lecture seule.

## <a name="disk-striping"></a>Entrelacement de disques  
Lorsqu’une échelle élevée que VM est attachée avec plusieurs premium stockage permanent disques, les disques peuvent être réparties entre eux pour agréger leurs Ops ES/s, la bande passante et la capacité de stockage.

Sous Windows, vous permet des espaces de stockage des disques de répartition ensemble. Vous devez configurer une colonne pour chaque disque dans un pool. Dans le cas contraire, les performances globales d’un volume agrégé par bandes peuvent être plus basse que prévu, en raison d’une distribution irrégulière du trafic entre les disques.

Important : L’utilisation de Server Manager UI, vous pouvez définir le nombre total de colonnes jusqu'à 8 pour un volume agrégé par bandes. Lorsque vous joignez plus de 8 disques, utiliser PowerShell pour créer le volume. À l’aide de PowerShell, vous pouvez définir le nombre de colonnes égal au nombre de disques. Par exemple, s’il y a 16 disques dans un agrégat unique ; spécifier des colonnes de 16 dans le paramètre *NumberOfColumns* de l’applet de commande PowerShell *New-VirtualDisk* .


Sous Linux, utilisez l’utilitaire MDADM pour les disques de l’agrégat par bandes ensemble. Pour plus de détails sur les disques de la répartition sur Linux, reportez-vous à [Configurer le RAID logiciel sous Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).


*Taille de répartition*  
Une configuration importante dans l’entrelacement est la taille de répartition. La taille de répartition ou d’une taille de bloc est le plus petit segment de données application pouvant traiter sur un volume agrégé par bandes. Le type d’application et de son modèle de demande dépend de la taille de répartition que vous configurez. Si vous choisissez la taille de répartition incorrect, il peut entraîner un défaut d’alignement d’e/s, qui conduit à une dégradation des performances de votre application.

Par exemple, si une demande d’e/s générée par votre application est plus grande que la taille de répartition de disque, le système de stockage écrit au-delà des limites d’unité agrégats par bandes sur plusieurs disques. Lorsqu’il est temps pour accéder à ces données, il faudra effectuer de recherches sur plusieurs unités de bande pour terminer la demande. L’effet cumulé d’un tel comportement peut conduire à une dégradation des performances. En revanche, si la taille de la demande d’e/s est plus petite que la taille de répartition, et si c’est aléatoire, par nature, les demandes d’e/s peuvent ajouter sur le même disque, à l’origine d’un goulet d’étranglement et finalement dégrader les performances d’e/s.


Selon le type de charge de travail de votre application est en cours d’exécution, choisissez une taille de répartition appropriée. Pour les demandes d’e/s peu aléatoires, utilisez une plus petite taille de répartition. Considérant que, pour les e/s séquentielles grand demandes utilisent une plus grande taille de répartition. Découvrez les recommandations de taille de répartition pour l’application exécutée sur le stockage de la prime. Pour SQL Server, configurez la taille de répartition de 64 Ko pour les charges de travail OLTP et 256 Ko pour les charges de gestion de magasins de données. Consultez les [méthodes conseillées en matière de performances de SQL Server sur Azure VM](../virtual-machines/virtual-machines-windows-sql-performance.md#disks-and-performance-considerations) pour en savoir plus.


>**Remarque :**  
>Vous pouvez répartir un maximum de 32 disques de stockage de prime sur une machine virtuelle de la série DS et des disques de stockage premium 64 sur une machine virtuelle de la série GS.

## <a name="multi-threading"></a>Multi-threading  
Azure a conçu la plate-forme de stockage de la prime doit être massivement parallèle. Par conséquent, une application multi-thread permet d’atteindre des performances largement supérieures à une application à thread unique. Une application multithread répartit ses tâches sur plusieurs threads et augmente l’efficacité de son exécution en utilisant les ressources de la machine virtuelle et le disque à la valeur maximale.

Par exemple, si votre application s’exécute sur un seul ordinateur virtuel à l’aide de deux threads de cœur, le processeur peut basculer entre les deux threads pour atteindre l’efficacité. Si un thread est en attente sur un disque e/s pour terminer, le processeur peut basculer vers l’autre thread. De cette manière, deux threads peuvent effectuer plus qu’un seul thread. Si la machine virtuelle n’a plus d’un seul cœur, il diminue plus temps d’exécution dans la mesure où chaque cœur peut exécuter des tâches en parallèle.

Vous ne serez peut-être pas en mesure de modifier la façon dont une application prête à l’emploi implémente une seule thread ou multi-thread. Par exemple, SQL Server est capable de gérer multiprocesseur et multicœur. Toutefois, SQL Server décide dans quelles conditions elle exploitera un ou plusieurs threads pour traiter une requête. Elle peut exécuter des requêtes et créer des index à l’aide de multi-threading. Pour les requêtes impliquant la jointure de tables volumineuses et trier les données avant de retourner à l’utilisateur, SQL Server utilise probablement plusieurs threads. Toutefois, un utilisateur ne peut pas contrôler si SQL Server exécute une requête à l’aide d’un thread unique ou plusieurs threads.

Il existe des paramètres de configuration que vous pouvez modifier pour influencer ce multi-thread ou en parallèle d’une application de traitement. Par exemple, dans le cas de SQL Server, il est la configuration de degré de parallélisme maximale. Ce paramètre MAXDOP, vous pouvez configurer le nombre maximal de processeurs que SQL Server peut utiliser lors de la parallèle de traitement. Vous pouvez configurer MAXDOP pour des requêtes individuelles ou d’opérations d’index. Cela est utile lorsque vous voulez équilibrer les ressources de votre système pour une application stratégique de performances.

Par exemple, supposons que votre application à l’aide de SQL Server exécute une requête de grande taille et une opération d’index en même temps. Supposons que vous souhaitiez que l’opération d’index d’être plus performant par rapport à la requête de grande taille. Dans ce cas, vous pouvez définir la valeur MAXDOP de l’opération d’index supérieure à la valeur MAXDOP pour la requête. De cette manière, SQL Server a plus nombre de processeurs qu’il peut exploiter pour l’opération d’index par rapport au nombre de processeurs, qu'il peut consacrer à la requête de grande taille. N’oubliez pas que vous ne contrôlez pas le nombre de threads SQL Server utilisera pour chaque opération. Vous pouvez contrôler le nombre maximal de processeurs étant dédiée de multi-threading.

Pour en savoir plus sur les [Degrés de parallélisme](https://technet.microsoft.com/library/ms188611.aspx) dans SQL Server. Découvrez ces paramètres qui influencent multi-threading dans votre application et de leurs configurations pour optimiser les performances.

## <a name="queue-depth"></a>Profondeur de la file d’attente  
La profondeur de la file d’attente ou la longueur de la file d’attente ou la taille de la file d’attente est le nombre de demandes d’e/s en attente dans le système. La valeur de profondeur de file d’attente détermine le nombre d’opérations d’e/s votre application peut d’aligner, qui va traiter les disques de stockage. Elle affecte tous les indicateurs de performance trois application dont nous avons parlé dans cet article savoir, e/s, le débit et la latence.

File d’attente de profondeur et multi-threading sont étroitement liées. La valeur de profondeur de file d’attente indique la quantité multi-threading qui peut être obtenue par l’application. Si la profondeur de la file d’attente est grande, application peut exécuter plusieurs opérations simultanément, en d’autres termes, plus de multi-threading. Si la profondeur de la file d’attente est petite, même si l’application est multithread, il n’aura pas suffisamment demandes alignés pour une exécution simultanée.

En règle générale, l’emploi applications ne vous permettent pas de modifier la profondeur de la file d’attente, car si défini correctement, il va faire plus de mal que de bien. Applications définit la valeur de droite de la profondeur de file d’attente pour obtenir des performances optimales. Toutefois, il est important de comprendre ce concept de sorte que vous pouvez résoudre des problèmes de performances avec votre application. Vous pouvez également observer les effets de profondeur de file d’attente en exécutant les outils d’évaluation des performances de votre système.

Certaines applications fournissent des paramètres pour influencer la profondeur de la file d’attente. Par exemple, le paramètre MAXDOP (degré maximal de parallélisme) de SQL Server est expliqué dans la section précédente. MAXDOP est un moyen d’influencer la profondeur de la file d’attente et multi-threading, bien qu’il ne modifie pas directement la valeur de profondeur de file d’attente de SQL Server.

*Profondeur de la file d’attente élevé*  
Une profondeur de file d’attente élevé les lignes les plus d’opérations sur le disque. Le disque connaît la prochaine demande dans sa file d’attente à l’avance. Par conséquent, le disque peut planifier des opérations d’avance et de les traiter dans une séquence optimale. Étant donné que l’application envoie plus de requêtes sur le disque, le disque peut traiter les e/s parallèles plus. Au final, l’application sera en mesure de réaliser les e/s plus élevée. Étant donné que l’application est en train de traiter davantage de demandes, le débit total de l’application augmente également.

En général, une application peut obtenir un débit maximal avec 8-16 + en attente e/s par disque connecté. Si une profondeur de file d’attente, application repousse pas suffisamment e/s vers le système, et il va traiter moins le montant de sur une période donnée. En d’autres termes, un débit inférieur.

Par exemple, dans SQL Server, la valeur MAXDOP pour une requête à « 4 » indique à SQL Server qu’il peut utiliser jusqu'à quatre cœurs pour exécuter la requête. SQL Server détermine quelle est la meilleure valeur de profondeur de file d’attente et le nombre de cœurs pour l’exécution de la requête.

*Profondeur de la file d’attente optimale*  
Valeur de profondeur de file d’attente très élevé a aussi ses inconvénients. Si la valeur de profondeur de file d’attente est trop élevé, l’application tente de lecteur d’e/s très élevé. À moins que l’application a permanents disques avec suffisamment d’IOPS mis en service, cela peut nuire aux latences d’application. Suivant la formule indique la relation entre les e/s, de latence et de profondeur de file d’attente.  
    ![](media/storage-premium-storage-performance/image6.png)

Vous ne devez pas configurer profondeur de file d’attente une valeur élevée, mais une valeur optimale, capable de fournir assez Ops ES/s pour l’application sans affecter les latences. Par exemple, si la latence de l’application doit être de 1 milliseconde, la profondeur de file d’attente requis pour atteindre 5 000 IOPS est, QD = 5000 x 0,001 = 5.

*Profondeur de file d’attente pour le Volume agrégé par bandes*  
Pour un volume agrégé par bandes, maintenir une profondeur de file d’attente suffisamment élevée telle que, chaque disque a une profondeur de file d’attente de pic individuellement. Par exemple, considérez une application qui exécute un push d’une profondeur de file d’attente de 2 et il y a 4 disques de la répartition. Les deux demandes d’e/s passe à deux disques et restant deux disques seront inactif. Par conséquent, configurer la profondeur de la file d’attente telles que tous les disques peuvent être occupés. Formule ci-dessous montre comment déterminer la profondeur de file d’attente des volumes agrégés par bandes.  
    ![](media/storage-premium-storage-performance/image7.png)

## <a name="throttling"></a>La limitation de  
Les dispositions de prime stockage Azure spécifié nombre des Ops ES/s et le débit en fonction de la taille de la mémoire virtuelle et tailles de disque que vous choisissez. Chaque fois que votre application tente de lecteur d’e/s ou débit dépasse ces limites de ce que la machine virtuelle ou un disque peut gérer, stockage de prime accélèrera il. Cela se manifeste sous la forme de dégradation des performances dans votre application. Cela peut signifier la latence plus élevée, réduire le débit inférieur ou Ops ES/s. Si le stockage de prime ne se limite pas, votre application risque d’échouer complètement par supérieure à ce que ses ressources sont capables d’atteindre. Par conséquent, pour éviter les problèmes de performances en raison de la limitation, toujours fournir suffisamment de ressources pour votre application. Tenir compte de ce que nous avons parlé dans les sections de tailles de disque ci-dessus et la taille de la mémoire virtuelle. Bancs d’essai est le meilleur moyen de savoir à quelles ressources vous devez héberger votre application.

## <a name="benchmarking"></a>Bancs d’essai  
Bancs d’essai consiste à simuler différentes charges de travail sur votre application et de mesurer les performances de l’application pour chaque charge de travail. À l’aide de la procédure décrite dans une section précédente, vous avez rassemblé les exigences de performances d’application. En exécutant les outils d’évaluation des performances sur les ordinateurs virtuels qui héberge l’application, vous pouvez déterminer les niveaux de performances que votre application peut obtenir avec stockage de la prime. Dans cette section, nous vous fournir exemples d’étalonnage d’un ordinateur virtuel DS14 Standard doté de disques de stockage de prime Azure.

Nous avons utilisé des outils de test courants Iometer et FIO, respectivement pour Windows et Linux. Ces outils de générer plusieurs threads simulant une production comme charge de travail et de mesurent les performances du système. L’utilisation des outils vous pouvez également configurer des paramètres tels que la profondeur bloc de taille et de la file d’attente, que vous ne pouvez pas normalement modifier pour une application. Cela vous donne plus de flexibilité pour lecteur de performances maximales sur une échelle élevée VM mis en service avec des disques de prime pour les différents types de charges de travail applicatives. Pour en savoir que plus sur chaque outil de test, visitez [Iometer](http://www.iometer.org/) et [FIO](http://freecode.com/projects/fio).

Pour suivre les exemples ci-dessous, créez un ordinateur virtuel Standard de DS14 et attacher de 11 disques de stockage de la prime à la machine virtuelle. De 11 disques, configurer 10 disques avec l’hôte, la mise en cache en tant que « None » et de leur répartition dans un volume nommé NoCacheWrites. Configurer l’hôte de la mise en cache en tant que « ReadOnly » sur le disque restant et créer un volume appelé CacheReads avec ce disque. À l’aide de ce programme d’installation, vous serez en mesure de voir les performances de lecture et d’écriture maximale à partir d’un ordinateur virtuel DS14 Standard. Pour obtenir la procédure détaillée sur la création d’une machine virtuelle de DS14 avec des disques de la prime, accédez à [créer et utiliser le compte de stockage de la prime pour un disque de données de machine virtuelle](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Préchauffage du Cache*  
Le disque avec hôte ReadOnly la mise en cache sera en mesure de donner des Ops ES/s supérieur à la limite du disque. Pour obtenir ces performances de lecture maximale dans le cache de l’hôte, tout d’abord vous devez préchauffage du cache de ce disque. Cela garantit que les e/s en lecture quel outil évaluation déterminera CacheReads volume effectivement atteint le cache et n’est pas le disque directement. Les correspondances de cache du résultat en Ops ES/s supplémentaires à partir du cache unique activée disque.

>**Important :**  
>Vous devez préchauffage du cache avant d’exécuter l’évaluation, chaque redémarrage d’ordinateur virtuel.

#### <a name="iometer"></a>Iometer   
[Télécharger l’outil Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) sur l’ordinateur virtuel.

*Fichier de test*  
Iometer utilise un fichier de test qui est stocké sur le volume sur lequel vous allez exécuter le test d’évaluation des performances. Il lit les lecteurs et écrit dans ce fichier de test pour mesurer le disque e/s et le débit. Iometer crée ce fichier de test si vous n’avez pas fourni un. Créer un fichier de test de 200 Go appelé iobw.tst sur les volumes CacheReads et NoCacheWrites.

*Caractéristiques de Microsoft Access*  
Le cahier des charges demande de taille d’e/s, % en lecture-écriture, % aléatoire/séquentielle configurés à l’aide de l’onglet « Caractéristiques de Microsoft Access » dans Iometer. Créer une spécification d’accès pour chacun des scénarios décrits ci-dessous. Créez les spécifications de l’accès et « Enregistrer » avec un nom similaire – RandomWrites\_8K, RandomReads\_de 8 Ko. Sélectionnez la spécification correspondante lors de l’exécution du scénario de test.

Ci-dessous un exemple de spécifications d’accès pour le scénario d’écrire des Ops ES/s maximum,  
    ![](media/storage-premium-storage-performance/image8.png)

*Spécifications de Test Ops ES/s maximum*  
Pour illustrer des Ops ES/s maximum, utiliser la plus petite taille de la demande. Utiliser la taille de demande de 8 Ko et de créer des spécifications pour des lectures et des écritures aléatoires.

| Spécification de l’accès | Taille de la demande | % Aléatoire | % De lecture |
|----------------------|--------------|----------|--------|
| RandomWrites\_8K     | 8 KO           | 100      | 0      |
| RandomReads\_8K      | 8 KO           | 100      | 100    |

*Spécifications de Test de débit maximal*  
Pour illustrer le débit maximal, utilisez la plus grande taille de la demande. Utiliser la taille de 64 Ko la demande et de créer des spécifications pour des lectures et des écritures aléatoires.

| Spécification de l’accès | Taille de la demande | % Aléatoire | % De lecture |
|----------------------|--------------|----------|--------|
| RandomWrites\_64 Ko    | 64 KO          | 100      | 0      |
| RandomReads\_64 Ko     | 64 KO          | 100      | 100    |

*Exécution du Test de Iometer*  
Effectuez les étapes ci-dessous pour le préchauffage du cache

1.  Créer deux spécifications d’accès avec les valeurs indiquées ci-dessous,

  	| Nom              | Taille de la demande | % Aléatoire | % De lecture |
  	|-------------------|--------------|----------|--------|
  	| RandomWrites\_1 Mo | 1 MO          | 100      | 0      |
  	| RandomReads\_1 Mo  | 1 MO          | 100      | 100    |

2.  Exécutez le test de Iometer pour l’initialisation du cache disque avec les paramètres suivants. Utilisez les trois threads de travail pour le volume cible et d’une profondeur de file d’attente de 128. Définir la durée de « Exécution » de l’essai à 2hrs sur l’onglet « Tester le programme d’installation ».

  	| Scénario              | Volume cible | Nom              | Durée |
  	|-----------------------|---------------|-------------------|----------|
  	| Initialisation du Cache disque | CacheReads    | RandomWrites\_1 Mo | 2hrs     |

3.  Exécutez le test de Iometer pour réchauffer le disque cache avec les paramètres suivants. Utilisez les trois threads de travail pour le volume cible et d’une profondeur de file d’attente de 128. Définir la durée de « Exécution » de l’essai à 2hrs sur l’onglet « Tester le programme d’installation ».

  	| Scénario           | Volume cible | Nom             | Durée |
  	|--------------------|---------------|------------------|----------|
  	| Préchauffage du Cache disque | CacheReads    | RandomReads\_1 Mo | 2hrs     |

Une fois le cache disque est échauffé, poursuivre les scénarios de test répertoriés ci-dessous. Pour exécuter le test de Iometer, utilisez au moins trois des threads de travail pour **chaque** volume cible. Pour chaque thread de travail, sélectionnez le volume cible, définir la profondeur de la file d’attente et sélectionnez une des spécifications de test enregistré, comme illustré dans le tableau ci-dessous, pour exécuter le scénario de test correspondant. Le tableau indique également les résultats attendus pour les e/s et de débit lors de l’exécution de ces tests. Pour tous les scénarios, une petite taille d’e/s de 8 Ko et d’une profondeur de file d’attente élevé de 128 sont utilisés.

| Scénario de test      | Volume cible | Nom              | Résultat       |
|--------------------|---------------|-------------------|--------------|
| Max. Ops ES/s en lecture     | CacheReads    | RandomWrites\_8K  | 50 000 IOPS  |
| Max. Écrire des Ops ES/s    | NoCacheWrites | RandomReads\_8K   | 64 000 IOPS  |
| Max. Ops ES/s combinée | CacheReads    | RandomWrites\_8K  | 100 000 IOPS |
|                    | NoCacheWrites | RandomReads\_8K   |              |
| Max. Lecture Mo/s   | CacheReads    | RandomWrites\_64 Ko | 524 Mo/s   |
| Max. Écriture Mo/s  | NoCacheWrites | RandomReads\_64 Ko  | 524 Mo/s   |
| Combiné Mo/s    | CacheReads    | RandomWrites\_64 Ko | 1000 Mo/s  |
|                    | NoCacheWrites | RandomReads\_64 Ko  |              |

Vous trouverez ci-dessous des captures d’écran de la Iometer résultats des tests pour les scénarios Ops ES/s et le débit combinés.

*Ops ES/s maximal combiné de lectures et d’écritures*  
![](media/storage-premium-storage-performance/image9.png)

*Combinaison de lectures et d’écritures débit maximal*  
![](media/storage-premium-storage-performance/image10.png)

### <a name="fio"></a>FIO  
FIO est un outil populaire pour le stockage de banc d’essai sur les ordinateurs virtuels de Linux. Il a la possibilité de sélectionner les différentes tailles d’e/s séquentielles ou lectures aléatoires et écrit. Il génère des threads de travail ou les processus pour effectuer les opérations d’e/s spécifiées. Vous pouvez spécifier le type d’opérations d’e/s que chaque thread de travail doit effectuer à l’aide de fichiers de travail. Nous avons créé un fichier de projet par le scénario illustré dans les exemples ci-dessous. Vous pouvez modifier les spécifications de ces fichiers de projet à différentes charges de travail en cours d’exécution sur le stockage de la prime de référence. Dans les exemples, nous utilisons un DS 14 ordinateur virtuel Standard exécutant **Ubuntu**. Utiliser la même configuration décrite au début de la [section de Benchmarking](#Benchmarking) et à chaud le cache avant d’exécuter les tests d’évaluation.

Avant de commencer, [FIO de télécharger](https://github.com/axboe/fio) et l’installer sur votre ordinateur virtuel.

Exécutez la commande suivante pour Ubuntu,

        apt-get install fio

Nous allons utiliser quatre threads de travail pour la conduite des opérations d’écriture et de quatre threads de travail pour la conduite des opérations de lecture sur les disques. Les travailleurs de l’écriture va être conduite le trafic sur le volume « nocache », qui a 10 disques avec cache la valeur « None ». Les travailleurs en lecture va être conduite le trafic sur le volume « readcache », qui a 1 disque avec cache a la valeur « ReadOnly ».

*Écriture maximale Ops ES/s*  
Créez le fichier de travail avec les spécifications suivantes pour obtenir le maximum écrire des Ops ES/s. Le nom « fiowrite.ini ».

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Notez les points clés suivantes qui sont en conformité avec les règles de conception présentées dans les sections précédentes. Ces spécifications sont essentielles pour les IOPS maximales,  
-   Une profondeur de file d’attente élevé de 256.  
-   Une taille de petit bloc de 8 Ko.  
-   Plusieurs threads d’exécution des écritures aléatoires.

Exécutez la commande suivante pour lancer le test FIO pendant 30 secondes,  

    sudo fio --runtime 30 fiowrite.ini

Pendant que le test s’exécute, vous serez en mesure de voir le nombre d’écriture pour la machine virtuelle et livrez des disques de la prime. Comme indiqué dans l’exemple ci-dessous, la machine virtuelle DS14 propose son écriture maximale limite d’e/s de 50 000 IOPS.  
    ![](media/storage-premium-storage-performance/image11.png)

*Nombre maximal de lectures e/s*  
Créez le fichier de travail avec les spécifications suivantes pour obtenir des e/s de lecture maximale. Le nom « fioread.ini ».

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Notez les points clés suivantes qui sont en conformité avec les règles de conception présentées dans les sections précédentes. Ces spécifications sont essentielles pour les IOPS maximales,

-   Une profondeur de file d’attente élevé de 256.  
-   Une taille de petit bloc de 8 Ko.  
-   Plusieurs threads d’exécution des écritures aléatoires.

Exécutez la commande suivante pour lancer le test FIO pendant 30 secondes,

    sudo fio --runtime 30 fioread.ini

Pendant que le test s’exécute, vous serez en mesure de voir le nombre de lecture Ops ES/s de la machine virtuelle et livrez des disques de la prime. Comme indiqué dans l’exemple ci-dessous, la machine virtuelle DS14 propose plus de 64 000 e/s de lecture. Il s’agit d’une combinaison du disque et les performances du cache.  
    ![](media/storage-premium-storage-performance/image12.png)

*Ops ES/s maximales de lecture et d’écriture*  
Créer le fichier de travail avec suivant les spécifications maximale combinée en lecture et écriture des e/s. Le nom « fioreadwrite.ini ».

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Notez les points clés suivantes qui sont en conformité avec les règles de conception présentées dans les sections précédentes. Ces spécifications sont essentielles pour les IOPS maximales,

-   Une profondeur de file d’attente élevé de 128.  
-   Une taille de petit bloc de 4 Ko.  
-   Exécution aléatoire de plusieurs threads, lit et écrit.

Exécutez la commande suivante pour lancer le test FIO pendant 30 secondes,

    sudo fio --runtime 30 fioreadwrite.ini

Pendant que le test s’exécute, vous ne pourrez pas voir le nombre de lectures combinée et en écriture pour la machine virtuelle et livrez des disques de la prime. Comme indiqué dans l’exemple ci-dessous, la machine virtuelle DS14 propose plus de 100 000 des combinés en lecture et en écriture des e/s. Il s’agit d’une combinaison du disque et les performances du cache.  
    ![](media/storage-premium-storage-performance/image13.png)

*Nombre maximal combiné de débit*  
Pour obtenir la valeur maximale combinée en lecture et écriture de débit, utilisez une taille de bloc supérieure et une profondeur de file d’attente de grande taille avec plusieurs threads effectuant des lectures et écritures. Vous pouvez utiliser une taille de bloc de 64 Ko et à la profondeur de file d’attente de 128.

## <a name="next-steps"></a>Étapes suivantes  

Pour en savoir plus sur le stockage Azure Premium :

- [: Premium hautes performances de stockage pour les charges de travail des machines virtuelles Azure](storage-premium-storage.md)  

Pour les utilisateurs de SQL Server, lisez les articles sur les procédures recommandées pour SQL Server :

- [Méthodes conseillées en matière de performances de SQL Server sur des Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-performance.md)
- [Stockage de prime Azure fournit les meilleures performances pour SQL Server dans Azure VM](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx) 
