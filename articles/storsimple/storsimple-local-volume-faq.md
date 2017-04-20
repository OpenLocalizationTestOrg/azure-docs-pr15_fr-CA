<properties 
   pageTitle="Forum aux questions sur les volumes mis en attente StorSimple localement | Microsoft Azure"
   description="Fournit des réponses aux questions fréquemment posées sur les volumes StorSimple épinglé localement."
   services="storsimple"
   documentationCenter="NA"
   authors="manuaery"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="manuaery" />

# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple localement épinglé volumes : Forum aux questions (FAQ)

## <a name="overview"></a>Vue d’ensemble

Voici les questions et les réponses que vous pourriez rencontrer lorsque vous créez un volume StorSimple localement épinglé, convertissez un volume monté en cascade sur un volume local épinglé (et inversement), ou sauvegardez et restaurez un volume localement épinglé.

Questions et réponses sont organisées dans les catégories suivantes

- Création d’un volume local épinglé
- Sauvegarde locale épinglée
- Conversion d’un volume monté en cascade sur un volume local épinglé
- Restauration d’un volume localement épinglé
- Basculer sur un volume local épinglé

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Questions à propos de la création d’un volume local épinglé

**Q.** Quelle est la taille maximale d’un volume local épinglé que je peux créer sur les périphériques 8000 série ?

**A** vous pouvez provisionner les volumes ajoutés localement jusqu'à 8,5 To ou volumes hiérarchisés jusqu'à 200 To de données sur le périphérique 8100. Sur le périphérique 8600 plus grand, vous pouvez configurer des volumes ajoutés localement jusqu'à 22,5 To ou volumes hiérarchisés jusqu'à 500 To.

**Q.** J’ai récemment mis à niveau mon périphérique 8100 à la mise à jour 2 et lorsque j’essaie de créer un volume localement épinglé, la taille maximale disponible est uniquement 6 et pas 8,5 To. Pourquoi ne puis-je pas pour créer un volume de 8,5 to ?

**A** vous pouvez provisionner les volumes ajoutés localement jusqu'à 8,5 To ou niveaux des volumes jusqu'à 200 To de données sur le périphérique 8100. Si votre périphérique a déjà hiérarchisé des volumes, l’espace disponible pour la création d’un volume local épinglé sera proportionnellement inférieur à cette limite maximale. Par exemple, si 100 To de volumes hiérarchisés ont déjà été mis en service sur votre périphérique 8100 (c'est-à-dire la moitié de la capacité hiérarchisée), alors la taille maximale d’un volume local que vous pouvez créer sur le périphérique 8100 est relativement réduite jusqu'à 4 To (environ la moitié de la valeur maximale localement épinglé à capacité de volume).

Étant donné que l’espace sur le périphérique local est utilisé pour héberger le jeu de travail de volumes de plusieurs niveaux, l’espace disponible pour la création d’un volume local épinglé est réduite si le périphérique a hiérarchisé des volumes. À l’inverse, la création d’un volume local épinglé proportionnellement réduit l’espace disponible pour les volumes hiérarchisés. Le tableau suivant résume la capacité hiérarchisée disponible sur les périphériques 8100 et 8600 lors de la création de volumes ajoutés localement.

|Capacité de mise en service de volumes ajoutés localement|Capacité disponible pour être mis en service pour les volumes hiérarchisés - 8100|Capacité disponible pour être mis en service pour les volumes hiérarchisés - 8600|
|-----|------|------|
|0 | 200 TO | 500 TO |
|1 TO | 176.5 TO | 477.8 TO|
|4 TO | 105.9 TO | 411.1 TO |
|8.5 TO | 0 TO | 311.1 TO|
|10 TO | NA | 277.8 TO |
|15 TO | NA | 166.7 TO |
|22.5 TO | NA | 0 TO |


**Q.** Pourquoi est-création d’un volume local épinglé une longue opération ? 

**A.** Les volumes ajoutés localement sont thickly mis en service. Pour créer de l’espace sur les couches locales de l’équipement, des données à partir des volumes existants hiérarchisés peuvent être poussées vers le nuage pendant le processus de déploiement. Et, dans la mesure où cela dépend de la taille du volume en cours de provision, les données existantes sur votre périphérique et de la bande passante disponible vers le nuage, le temps nécessaire pour créer un volume local peut prendre plusieurs heures.

**Q.** Combien de temps faut-il pour créer un volume localement épinglé ?

**A.** Étant donné que les volumes ajoutés localement sont thickly mis en service, des données existantes à partir de volumes de plusieurs niveaux peuvent être poussées vers le nuage pendant le processus de déploiement. Par conséquent, le temps nécessaire pour créer un volume localement ajouté dépend de plusieurs facteurs, notamment la taille du volume, les données sur votre périphérique et de la bande passante disponible. Sur un périphérique récemment installé n’a aucun volume, le temps de créer un volume localement épinglé est environ 10 minutes par téraoctet de données. Toutefois, la création de volumes locaux peut prendre plusieurs heures selon les facteurs expliqués ci-dessus sur un périphérique qui est en cours d’utilisation.

**Q.** Je souhaite créer un volume localement épinglé. Existe-t-il des pratiques recommandées que je dois connaître ?

**A.** Les volumes ajoutés localement ne conviennent pas pour les charges de travail nécessitant des garanties locales des données à tout moment et sensible aux latences du nuage. Considérer l’utilisation des volumes locaux pour une de vos charges de travail, soyez conscient des éléments suivants :

- Les volumes ajoutés localement sont approvisionnés thickly et création de volumes locales a une incidence sur l’espace disponible pour les volumes hiérarchisés. Par conséquent, nous vous conseillons démarrez avec des volumes de petite taille et évoluer en tant que vos besoins de stockage augmente.

- Mise en service des volumes locaux est une opération longue qui peut impliquer de transmettre des données existantes à partir de volumes de plusieurs niveaux dans le nuage. Par conséquent, vous pouvez rencontrer des performances réduites sur ces volumes.

- Mise en service des volumes locaux est une opération longue. Le temps réel passé dépend de plusieurs facteurs : la taille du volume en cours de provision, les données sur votre périphérique et la bande passante disponible. Si vous n’avez pas sauvegardé vos volumes existants vers le nuage, puis création d’un volume est plus lente. Nous vous suggérons de que suivre des captures instantanées de nuage de vos volumes existants avant de vous mettre en service un volume local.
 
- Vous pouvez convertir des volumes existants hiérarchisés aux volumes ajoutés localement, et cette conversion implique la mise en service de l’espace sur le périphérique pour le volume résultant localement épinglé (en plus de l’interrompre hiérarchisée des données [le cas échéant] à partir du cloud). Là encore, il s’agit d’une longue opération qui dépend de facteurs que nous avons discuté ci-dessus. Nous vous recommandons de sauvegarder vos volumes existants avant la conversion que le processus sera même plus lent si des volumes existants ne sont pas sauvegardés. Votre appareil peut également rencontrer des performances réduites lors de ce processus.
    
Plus d’informations sur la [Création d’un volume local épinglé](storsimple-manage-volumes-u2.md#add-a-volume)

**Q.** Puis-je créer plusieurs volumes ajoutés localement en même temps ?

**A.** Oui, mais les travaux de création et d’extension volume épinglé localement sont traitées séquentiellement.

Thickly mise en service des volumes ajoutés localement et cela nécessite la création d’un espace local sur le périphérique (ce qui peut provoquer des données existantes à partir de volumes hiérarchisés pour être poussé vers le nuage pendant le processus de mise en service). Par conséquent, si une tâche de mise en service est en cours, autres travaux de création de volume local va être en file d’attente jusqu'à ce que cette tâche est terminée.

De même, si un volume local existant est en cours de développé ou un volume monté en cascade est converti en un volume localement épinglé, puis la création d’un nouveau volume localement épinglé est en file d’attente jusqu'à ce que la tâche précédente est terminée. Extension de la taille d’un volume local épinglé implique l’expansion de l’espace local existant pour ce volume. Conversion à partir d’un local épinglée hiérarchisé volume implique également la création d’un espace local pour obtenu localement pinned volume. Dans ces deux de ces opérations, la création ou l’extension de l’espace local est un long exécute travail.

Vous pouvez afficher ces tâches dans la page **tâches** du service Gestionnaire de StorSimple d’Azure. Le travail activement en cours de traitement est continuellement mis à jour pour refléter l’état d’avancement de la mise en service de l’espace. Les travaux de volume localement épinglé restant comme étant en cours d’exécution, mais leur progression est bloquée et qu’ils sont prélevés dans l’ordre qu’ils ont été mis en attente.

**Q.** J’ai supprimé un volume localement épinglé. Pourquoi ne puis-je pas voir l’espace récupéré est reflétée dans l’espace disponible lorsque j’essaie de créer un nouveau volume ? 

**A.** Si vous supprimez un volume localement épinglé, l’espace disponible pour les nouveaux volumes de ne peut pas être mis à jour immédiatement. Le Service Gestionnaire de StorSimple met à jour l’espace local environ toutes les heures. Nous vous recommandons de qu'attendre une heure avant de tenter de créer le nouveau volume.

**Q.** Les volumes ajoutés localement sont pris en charge sur la solution matérielle-logicielle le nuage ?

**A.** Volumes ajoutés localement ne sont pas pris en charge sur l’appliance de nuage (périphériques 8010 et 8020 anciennement dénommés le périphérique virtuel de StorSimple).

**Q.** Puis-je utiliser les applets de commande PowerShell de Azure pour créer et gérer des volumes ajoutés localement ? 

**A.** Non, vous ne peut pas créer de volumes ajoutés localement via les applets de commande PowerShell de Azure (n’importe quel volume que vous créez via Azure PowerShell est monté en cascade). Il est conseillé de ne pas utiliser les applets de commande PowerShell de Azure pour modifier les propriétés d’un volume local épinglé, comme il aura l’effet indésirable de la modification du type de volume à plusieurs niveaux.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Questions sur la sauvegarde d’un volume local épinglé

**Q.** Sont des snapshots locaux de prise en charge de volumes ajoutés localement ?

**A.** Oui, vous pouvez prendre des snapshots locaux de vos volumes ajoutés localement. Toutefois, nous vous recommandons vivement de sauvegarder régulièrement vos volumes ajoutés localement avec des snapshots de nuage afin de garantir que vos données sont protégées dans l’éventualité d’un sinistre.

**Q.** Y a-t-il des instructions pour la gestion des snapshots locaux pour les volumes ajoutés localement ?

**A.** Fréquentes snapshots locaux à côté d’un taux élevé d’évolution du données dans le volume localement épinglé peuvent provoquer un espace local sur le périphérique à consommer rapidement et de produire des données à partir de volumes hiérarchisés lancés le nuage. Nous vous suggérons par conséquent que vous réduisez le nombre de snapshots locaux.

**Q.** J’ai reçu une alerte indiquant que mon snapshots locaux de volumes ajoutés localement peuvent être invalidés. Lorsque cela peut se produire ?

**A.** Fréquentes snapshots locaux à côté d’un taux élevé d’évolution du données dans le volume localement épinglé peuvent provoquer un espace local sur le périphérique à consommer rapidement. Si les niveaux locales du périphérique sont largement utilisés, le périphérique ne soit plein peut entraîner une panne de cloud étendu, et les écritures entrantes sur le volume peuvent invalidation des snapshots (comme l’espace n’existe pour mettre à jour les instantanés pour désigner les anciens blocs de données qui ont été écrasées). Dans ce cas l’écriture sur le volume continue à être pris en charge, mais les snapshots locaux peuvent être non valides. Il n’y a aucun impact sur vos snapshots cloud existant.

L’avertissement d’alerte est pour vous informer que cette situation peut se produire et que la même adresse en temps voulu par les plannings de vos snapshots locaux à prendre des snapshots locaux moins fréquentes de validation ou de la suppression des snapshots locaux plus anciens qui ne sont plus nécessaires.

Si les snapshots locaux sont invalidées, vous recevrez une alerte d’information qui vous avertit que les snapshots locaux pour la stratégie de sauvegarde spécifique ont été invalidées à côté de la liste des horodatages des snapshots locaux qui ont été invalidées. Ces captures instantanées seront automatiquement supprimés et vous ne seront plus en mesure de les afficher dans la page **Sauvegarde catalogues** dans Azure portal classique.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Questions à propos de la conversion d’un volume monté en cascade sur un volume local épinglé

**Q.** Je suis en observant une lenteur sur le périphérique lors de la conversion d’un volume monté en cascade sur un volume local épinglé. Que se passe-t-il ? 

**A.** Le processus de conversion implique deux étapes : 

  1. Mise en service de l’espace sur le périphérique pour les meilleurs-vers--convertie localement épinglé volume.
  2. Téléchargement de données hiérarchisées du nuage pour garantir local des garanties.

Ces deux étapes sont longues exécutant des opérations qui dépendent de la taille du volume en cours de conversion, les données sur le périphérique et la bande passante disponible. Comme certaines données à partir des volumes existants hiérarchisés peuvent renversez vers le nuage en tant que partie du processus de provisionnement, votre périphérique pouvez rencontrer des performances réduites pendant ce temps. En outre, le processus de conversion peut être plus lent si :

- Les volumes existants n’ont pas été sauvegardés vers le nuage ; afin que nous vous recommandons de sauvegarder vos volumes avant de lancer une conversion.

- Les stratégies de limitation de la bande passante ont été appliquées, ce qui peut limiter la bande passante disponible vers le nuage ; Il est donc recommandé de qu'avoir un 40 Mbps dédié ou connexion plus vers le nuage.

- Le processus de conversion peut prendre plusieurs heures en raison de plusieurs facteurs expliqués ci-dessus ; Par conséquent, nous vous suggérons d’effectuer cette opération pendant les heures de pics non ou le week-end pour éviter l’impact sur les clients.

Plus d’informations sur la façon de convertir [un volume monté en cascade sur un volume local épinglé](storsimple-manage-volumes-u2.md#change-the-volume-type)

**Q.** Puis-je annuler l’opération de conversion de volume ?

**A.** Non, vous ne pouvez pas annuler l’opération de conversion une fois lancée. Comme indiqué dans la question précédente, veuillez être conscient des problèmes de performances potentiels que vous pourriez rencontrer durant le processus et suivez les méthodes conseillées répertoriées ci-dessus, lorsque vous planifiez votre conversion.

**Q.** Que se passe-t-il sur Mon volume en cas d’échec de l’opération de conversion ?

**A.** Conversion de volume peut échouer en raison de problèmes de connectivité de nuage. Le périphérique peut finalement cesser le processus de conversion après une série de tentatives infructueuses d’interrompre hiérarchisée des données à partir du cloud. Dans un tel scénario, le type de volume continueront à être la source du type de volume avant la conversion, et :

- Une alerte critique doit être générée pour vous informer de l’échec de conversion de volume. Plus d’informations sur les [alertes liées aux volumes ajoutés localement](storsimple-manage-alerts.md#locally-pinned-volume-alerts)

- Si vous convertissez un hiérarchisé sur un volume local épinglé, le volume continue à présenter les propriétés d’un volume monté en cascade comme données peuvent encore résider sur le nuage. Nous suggérons que résoudre les problèmes de connectivité et recommencez l’opération de conversion.
 
- De même, en cas d’échec de conversion de localement ajoutés à un volume monté en cascade, bien que le volume est marqué comme un volume localement épinglé, il fonctionnera comme un volume monté en cascade (car les données pourraient se sont répandues au nuage). Toutefois, il continuera occupent de l’espace sur les couches locales du périphérique. Cet espace ne sera pas disponible pour les autres volumes ajoutés localement. Nous vous recommandons de relancer cette opération afin de vous assurer que la conversion du volume est terminée et que l’espace local sur le périphérique peut être récupérée.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Questions sur la restauration d’un volume local épinglé

**Q.** Sont restaurées instantanément les volumes ajoutés localement ?

**A.** Oui, les volumes ajoutés localement sont restaurées instantanément. Dès que les informations de métadonnées pour le volume sont extraite à partir du cloud dans le cadre de l’opération de restauration, le volume est mis en ligne et est accessible par l’hôte. Toutefois, garanties locales pour le volume de données ne sera pas présentes jusqu'à ce que toutes les données a été téléchargée depuis le nuage, vous pouvez rencontrer réduit les performances de ces volumes pendant toute la durée de la restauration.

**Q.** Combien de temps faut-il pour restaurer un volume localement épinglé ?

**A.** Les volumes ajoutés localement sont restaurées instantanément et mises en ligne dès que les informations de métadonnées du volume sont récupérées à partir du cloud, tandis que les données du volume continuent à être téléchargées en arrière-plan. Cette dernière partie de l’opération de restauration : retrouvez les garanties locales pour les données du volume--est une opération longue et peut nécessiter plusieurs heures pour les données d’origine à nouveau. Le temps nécessaire pour effectuer la même dépend de plusieurs facteurs, tels que la taille du volume en cours de restauration et de la bande passante disponible. Si le volume d’origine qui est en cours de restauration a été supprimé, vous serez plus de temps pour créer de l’espace local sur le périphérique dans le cadre de l’opération de restauration.

**Q.** Je dois restaurer mon volume localement épinglé existant à un ancien cliché (lorsque le volume a été monté en cascade). Le volume est restauré comme niveaux dans ce cas ?

**A.** Non, le volume sera restauré comme un volume localement épinglé. Bien que les dates de capture instantanée à la fois lorsque le volume a été monté en cascade, lors de la restauration des volumes existants, StorSimple utilise toujours le type de volume sur le disque telle qu’elle existe actuellement.

**Q.** J’ai étendu Mon volume localement ajouté récemment, mais j’ai maintenant besoin restaurer les données à une heure le volume de plus petit taille. Restauration redimensionner le volume actuel et est-il nécessaire d’étendre la taille du volume, une fois la restauration terminée ?

**A.** Oui, la restauration sera redimensionné le volume, et vous devrez étendre la taille du volume, une fois la restauration terminée.

**Q.** Puis-je changer le type d’un volume lors de la restauration ?

**A.** Non, vous ne pouvez pas modifier le type de volume pendant la restauration.

- Les volumes qui ont été supprimés sont restaurés comme type stocké dans l’instantané.

- Les volumes existants sont restaurés en fonction de leur type actuel, quel que soit le type stocké dans la capture instantanée (reportez-vous aux deux questions précédentes).
 
**Q.** Je dois restaurer mon volume épinglé localement, mais j’ai choisi un point incorrect dans l’instantané. Puis-je annuler l’opération de restauration en cours ?

**A.** Oui, vous pouvez annuler une opération de restauration en cours. L’état du volume sera restaurée à l’état au début de la restauration. Toutefois, toutes les écritures qui ont été apportées au volume lors de la restauration en cours seront perdues.

**Q.** J’ai commencé une opération de restauration sur l’un de mes volumes ajoutés localement, et je vois un instantané dans mon catalogue de file d’attente que je n’équipe de création. À quoi il sert ?

**A.** Il s’agit de la capture instantanée temporaire qui est créée avant l’opération de restauration et est utilisée pour la restauration si la restauration est annulée ou échoue. Ne supprimez pas cet instantané ; Il sera automatiquement supprimé une fois la restauration terminée. Ce problème peut se produire si votre opération de restauration a épinglé que localement de volumes ou un mélange de localement les volumes épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas.

**Q.** Est-il possible de cloner un volume localement épinglé ?

**A.** Si, tu peux. Toutefois, le volume épinglé localement sera cloné comme un volume monté en cascade par défaut. Plus d’informations sur comment [clone un volume localement épinglé](storsimple-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Questions de basculer sur un volume local épinglé

**Q.** J’ai besoin de basculer sur mon périphérique à un autre périphérique physique. Mes volumes ajoutés localement peut être refusés plus épinglé localement ou niveaux ?

**A.** En fonction de la version du logiciel du périphérique cible, les volumes ajoutés localement seront alors basculés en tant que :

- Localement épinglé si l’équipement cible est en cours d’exécution StorSimple 8000 série de mise à jour 2
- Plusieurs niveaux si l’équipement cible est en cours d’exécution StorSimple 8000 série jour 1.x
- Niveaux si le périphérique cible est la solution de cloud (mise à jour de logiciel version 2 ou la mise à jour de 1.x)

Plus d’informations sur le [basculement et la reprise après sinistre de localement épinglé volumes entre les versions](storsimple-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Q.** Les volumes ajoutés localement instantanément restaurées au cours de reprise après sinistre (DR) ?

**A.** Oui, les volumes ajoutés localement sont restaurées instantanément au cours du basculement. Dès que les informations de métadonnées pour le volume sont extraite à partir du cloud dans le cadre de l’opération de basculement, le volume est mis en ligne sur le périphérique cible et est accessible par l’hôte. Pendant ce temps, les données du volume continuera à télécharger en arrière-plan, et vous pouvez rencontrer des performances réduites sur ces volumes pendant toute la durée du basculement.

**Q.** Voir le travail de basculement sur incident terminé, comment puis-je suivre l’évolution du volume épinglé localement en cours de restauration sur le périphérique cible ?

**A.** Lors d’une opération de basculement sur incident, le travail de basculement comme étant terminé une fois tous les volumes dans le jeu de basculement ont été restaurés et mis en ligne sur le périphérique cible instantanément. Cela inclut les volumes ajoutés localement qui peuvent avoir été basculées ; Toutefois, les garanties locales des données sera uniquement disponibles lorsque toutes les données pour le volume a été téléchargé. Vous pouvez suivre cette progression pour chaque volume local épinglé qui a échoué en surveillant les tâches de restauration correspondantes qui sont créés dans le cadre du basculement. Ces travaux de restauration individuels sera créé uniquement pour les volumes ajoutés localement.

**Q.** Puis-je changer le type d’un volume au cours du basculement ?

**A.** Non, vous ne pouvez pas modifier le type de volume lors d’un basculement. Si vous ne pouvez pas sur un autre périphérique physique qui est en cours d’exécution StorSimple 8000 série de mises à jour de 2, les volumes seront alors basculés en fonction du type de volume stocké dans l’instantané. Lors du basculement vers une autre version du périphérique, reportez-vous à la question ci-dessus sur le type de volume après un basculement.

**Q.** Puis-je basculer un conteneur de volume avec des volumes ajoutés localement à l’appliance de nuage ?

**A.** Si, tu peux. Les volumes ajoutés localement seront alors basculés en tant que volumes hiérarchisés. Plus d’informations sur le [basculement et la reprise après sinistre de localement épinglé volumes entre les versions](storsimple-device-failover-disaster-recovery.md#considerations-for-device-failover)


