<properties
 pageTitle="Comprendre et résoudre les erreurs de WebHCat sur HDInsight"
 description="Découvrez comment à propos des erreurs courantes renvoyées par WebHCat sur HDInsight et comment les résoudre."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/27/2016"
 ms.author="larryfr"/>

#<a name="understand-and-resolve-errors-received-from-webhcat-templeton-on-hdinsight"></a>Comprendre et résoudre les erreurs reçues à partir de WebHCat (Templeton), sur le HDInsight

Lors de l’utilisation de WebHCat (anciennement appelé Templeton,) pour travailler avec HDInsight, vous pouvez recevoir des erreurs. Ce document fournit des conseils sur les erreurs communs – pourquoi ils se produisent et de ce que vous pouvez faire pour les résoudre.

##<a name="what-is-webhcat"></a>Quel est WebHCat ?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) est une API REST pour couche de gestion de [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), d’une table et storage pour Hadoop. WebHCat est activé par défaut sur les clusters de HDInsight et est utilisée par les différents outils de soumettre des travaux, d’obtenir le statut de la tâche, etc. sans se connecter au cluster.

##<a name="modifying-configuration"></a>Modification de la configuration

> [AZURE.IMPORTANT] Plusieurs des erreurs répertoriées dans ce document se produisent car une configuration maximale a été dépassée. Lors de l’étape de résolution mentionne que vous pouvez modifier une valeur, vous devez utiliser une des méthodes suivantes pour effectuer la modification :

* Pour les clusters **Windows** : utiliser une action de script pour configurer la valeur lors de la création du cluster. Pour plus d’informations, voir [développer les actions de script](hdinsight-hadoop-script-actions.md).

* Pour les clusters **Linux** : utilisez Ambari (web ou API REST) pour modifier la valeur. Pour plus d’informations, voir [Gérer les HDInsight à l’aide de Ambari](hdinsight-hadoop-manage-ambari.md)

###<a name="default-configuration"></a>Configuration par défaut

Les valeurs de configuration par défaut qui peuvent avoir un impact sur les performances de WebHCat ou de provoquer des erreurs si ces valeurs sont dépassées, sont les suivantes :

| Paramètre | Ce qu’il fait | Valeur par défaut |
| ------- | ------------ | ------------- |
| [yarn.Scheduler.Capacity.maximum-applications][maximum-applications] | Le nombre maximal de tâches pouvant être actives simultanément (en attente ou en cours d’exécution) | 10 000 |
| [templeton.Exec.max-proc.][max-procs] | Le nombre maximal de demandes pouvant être traités simultanément | 20 |
| [MapReduce.jobhistory.max-age-ms][max-age-ms] | Le nombre de jours que seront conservé l’historique des travaux | 7 derniers jours |

##<a name="too-many-requests"></a>Trop de demandes

**Code d’état HTTP**: 429

| Cause | Résolution |
| ----- | ---------- |
| Vous avez dépassé le nombre maximal de requêtes simultané pris en charge par WebHCat par minute (par défaut, 20) | Réduire votre charge de travail pour vous assurer que vous n’envoyez pas plus que le nombre maximal de demandes simultanées ou augmentez la limite des demandes simultanées en modifiant `templeton.exec.max-procs`. Pour plus d’informations, reportez-vous à la section [configuration de modification](#modifying-configuration) |

##<a name="server-unavailable"></a>Serveur non disponible

**Code d’état HTTP**: 503

| Cause | Résolution |
| ---------------- | ------------------- |
| Cela se produit généralement lors du basculement entre le HeadNode principal et secondaire pour le cluster | Attendez deux minutes, puis réessayez l’opération |

##<a name="bad-request-content-could-not-find-job"></a>Demande incorrecte dans laquelle contenu : Impossible de trouver travail

**Code d’état HTTP**: 400

| Cause | Résolution |
| ---------------- | ------------------- |
| Détails de la tâche ont été nettoyés par l’historique des travaux de nettoyage | La période de rétention par défaut de l’historique des travaux est de 7 jours. Cela peut être modifié en modifiant `mapreduce.jobhistory.max-age-ms`. Pour plus d’informations, reportez-vous à la section [configuration de modification](#modifying-configuration) |
| Travail qui a été supprimé en raison d’un basculement sur incident | Retenter la soumission de projet pendant deux minutes |
| Un id de tâche non valide a été utilisé. | Vérifiez si l’id de travail est correcte |

##<a name="bad-gateway"></a>Passerelle incorrecte

**Code d’état HTTP**: 502

| Cause | Résolution |
| ---------------- | ------------------- |
| Nettoyage de la mémoire interne se produit dans le processus de WebHCat | Attendre le garbage collection à terminer ou redémarrez le service WebHCat |
| Délai d’attente en attente d’une réponse du service de ResourceManager. Cela peut se produire lorsque le nombre d’applications actives est le maximum configuré (par défaut 10 000) | Attendre pour terminer, ou augmentez la limite de tâches simultanées en modifiant les tâches en cours d’exécution `yarn.scheduler.capacity.maximum-applications`. Pour plus d’informations, reportez-vous à la section [configuration de modification](#modifying-configuration)  |
| Lorsque vous tentez d’extraire toutes les tâches par le biais de l’appel de [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) lors de la `Fields` a la valeur`*` | Ne pas récupérer *tous les* détails d’une tâche, utilisez à la place `jobid` pour extraire les détails des travaux seulement supérieurs à certain id de tâche. Ou bien, n’utilisez pas`Fields` |
| Le service WebHCat est arrêté au cours du basculement de HeadNode | Attendez deux minutes, puis réessayez l’opération |
| Il n’y a plus de 500 travaux en attente, envoyés par l’intermédiaire de WebHCat | Attendez que les traitements actuellement en attente aient terminé avant de soumettre des travaux plus |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 
