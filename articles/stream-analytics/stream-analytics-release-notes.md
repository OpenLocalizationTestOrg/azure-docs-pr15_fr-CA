<properties 
    pageTitle="Notes de publication de flux Analytique | Microsoft Azure" 
    description="Notes de mise à jour de flux Analytique" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="stream-analytics-release-notes"></a>Notes de publication de flux Analytique

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Notes de version 04/15/2016 de flux Analytique ##

Cette version contient la mise à jour suivante.

Titre | Description
---|---
Disponibilité générale pour les sorties de puissance BI  | [Sorties d’alimentation BI](stream-analytics-power-bi-dashboard.md) sont désormais généralement disponibles. L’expiration de l’autorisation de 90 jours pour le décisionnel d’alimentation a été supprimée. Pour plus d’informations sur les scénarios où l’autorisation doit être renouvelé, consultez la section [autorisation du renouvellement](stream-analytics-power-bi-dashboard.md#Renew-authorization) de la création d’un tableau de bord d’alimentation.

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Notes de version du 03/03/2016 de flux Analytique ##

Cette version contient la mise à jour suivante.

Titre | Description
---|---
Nouveaux éléments de langage de requête Analytique de flux  | SAQL a maintenant [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "Page de MSDN GetType"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST Page de MSDN") et [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN Page").

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Notes de version 12/10/2015 de flux Analytique ##

Cette version contient la mise à jour suivante.

Titre | Description
---|---
Mise à jour de la version API REST | La version de l’API REST a été mis à jour à 01-10-2015. Vous trouverez plus d’informations sur MSDN à l’adresse de [Référence de l’API de flux Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx) et [l’intégration d’apprentissage automatique dans le flux de données Analytique](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Intégration d’apprentissage Machine Azure | Cette version s’accompagne de prise en charge de la Machine Azure apprendre les fonctions définies par l’utilisateur. Consultez le [didacticiel](stream-analytics-machine-learning-integration-tutorial.md) pour plus d’informations, ainsi que l' [annonce de blog général](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx).

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Notes de version de 11/12/2015 de flux Analytique ##

Cette version contient la mise à jour suivante.

Titre | Description
---|---
Nouveau comportement de l’instruction SELECT | Sélectionnez dans le flux de données Analytique a été étendue pour autoriser * comme un accesseur de propriété d’un enregistrement imbriqué. Pour plus d’informations, consultez la [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Des Types de données complexes").

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Notes de version de 22/10/2015 de flux Analytique ##

Cette version contient les mises à jour suivantes.

Titre | Description
---|---
Fonctionnalités de langage de requête supplémentaires | Flux de données Analytique a développé le langage de requête en incluant les fonctionnalités suivantes : [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), un [plafond](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [signe](https://msdn.microsoft.com/library/azure/mt605290.aspx), [carré](https://msdn.microsoft.com/library/azure/mt605288.aspx)et [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Supprimer les limites d’agrégation  | Cette version supprime la limite de 15 agrégats dans une requête. Il n’existe désormais aucune limite au nombre d’agrégats par requête.
Fonction ajoutée de groupe par System.Timestamp | La fonction [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) permet désormais de window_type ou de [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
OFFSET ajouté de secouage et de saut de windows | Par défaut, windows de [secouage](https://msdn.microsoft.com/library/azure/dn835055.aspx) et de [saut](https://msdn.microsoft.com/library/azure/dn835041.aspx) sont alignés par rapport à zéro heure (1/1/0001 12:00:00 : 00 UTC). Le nouveau paramètre (facultatif) 'offsetsize' permet de spécifiant un offset (ou alignement) personnalisé.


## <a name="notes-for-09292015-release-of-stream-analytics"></a>Notes de version de 29/09/2015 de flux Analytique ##

Cette version contient les mises à jour suivantes.

Titre | Description
---|---
IoT Azure Suite version d’évaluation | Flux de données Analytique est inclus dans la version d’évaluation de la Suite de IoT Azure.
Intégration de portail Azure | En plus de la présence continue dans le portail de gestion d’Azure, flux Analytique est maintenant intégré dans le [Portail Azure](https://azure.microsoft.com/overview/preview-portal/). Remarque qu’Analytique du flux de fonctionnalité dans le portail d’aperçu est actuellement un sous-ensemble des fonctionnalités offertes dans le portail de gestion d’Azure, sans prise en charge pour le test de la requête de navigateur, BI de puissance de sortie configuration et à la navigation ou création de ressources de sortie et de la nouvelle entrée dans les abonnements que vous avez accès à.
Prise en charge de la sortie de DocumentDB | Les tâches de flux de données Analytique peuvent à présent sortir à [DocumentDB](https://azure.microsoft.com/services/documentdb/).
Prise en charge pour l’entrée de concentrateur de IoT | Travaux d’Analytique de flux peuvent maintenant traiter des données de concentrateurs de IoT.
HORODATAGE par des événements hétérogènes | Lorsqu’un flux de données contient plusieurs types d’événements ayant des horodatages dans différents domaines, vous pouvez maintenant utiliser [TIMESTAMP en](http://msdn.microsoft.com/library/mt573293.aspx) avec des expressions pour spécifier les champs d’horodatage différentes pour chaque cas.

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Notes de version 09/10/2015 de flux Analytique ##

Cette version contient les mises à jour suivantes.

Titre|Description
---|---
Prise en charge des groupes de PowerBI|Pour activer le partage de données avec d’autres utilisateurs de puissance BI, les tâches de flux de données Analytique peuvent maintenant écrire dans [groupes de PowerBI](stream-analytics-define-outputs.md#power-bi) à l’intérieur de votre compte courant BI.

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Notes de version de 20/08/2015 de flux Analytique ##

Cette version contient les mises à jour suivantes.

Titre|Description
---|---
Ajouté la dernière fonction |La [dernière](http://msdn.microsoft.com/library/mt421186.aspx) fonction est maintenant disponible dans les travaux Analytique de flux, vous permettant ainsi de récupérer l’événement le plus récent dans un flux d’événements dans un intervalle donné.
Nouvelles fonctions de tableau|Fonctions de tableau [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) et [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) sont désormais disponibles.
Nouvelles fonctions d’enregistrement|Enregistrement des fonctions [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) et [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) sont désormais disponibles.

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Notes de version 07/30/2015 de flux Analytique ##

Cette version contient les mises à jour suivantes.

Titre|Description
---|---
Id d’organisation BI alimentation découplée à partir de l’Id d’Azure|Cette fonctionnalité permet la [sortie de puissance BI](stream-analytics-power-bi-dashboard.md) pour travaux ASA sous n’importe quel type de compte Azure (Live Id ou Id d’organisation). En outre, vous pouvez disposer d’un Id d’organisation pour votre compte Azure et utiliser un nom différent pour autoriser la sortie de puissance BI.
Prise en charge de la sortie de files d’attente de Bus de Service|Sorties de [Files d’attente de Bus de service](stream-analytics-define-outputs.md#service-bus-queues) sont maintenant disponibles dans les tâches de flux de données Analytique.
Prise en charge de la sortie des thèmes du Bus des services|Sorties de [Rubriques de Bus de service](stream-analytics-define-outputs.md#service-bus-topics) sont désormais disponibles dans les tâches de flux de données Analytique.

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Notes de version 07/09/2015 de flux Analytique ##

Cette version contient les mises à jour suivantes.


Titre|Description
---|---
Personnalisé Blob de partitionnement de sortie|Sorties de stockage BLOB autorisent désormais une option pour spécifier la fréquence de cette sortie BLOB est écrits et la structure et le format de la structure de dossiers du chemin d’accès de données sortie. 

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Notes de version 05/03/2015 de flux Analytique ##

Cette version contient les mises à jour suivantes.


Titre|Description
---|---
Une augmentation de la valeur maximale de fenêtre de tolérance de la sortie de la commande|La taille maximale de la fenêtre de tolérance de la sortie de la commande est désormais 59:59 (mm : ss)
JSON Format de sortie : Ligne séparée ou un tableau|Il existe désormais une option lors de la sortie pour le stockage des objets Blob ou concentrateur d’événements sous la forme d’un tableau d’objets JSON ou en séparant les objets JSON par une nouvelle ligne. 

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Notes de version 16/04/2015 de flux Analytique ##


Titre|Description
---|---
Retard dans la configuration du compte du stockage Azure|Lorsque vous créez une tâche de flux de données Analytique dans une région pour la première fois, vous êtes invité à créer un nouveau compte de stockage, ou spécifiez un compte existant pour le suivi des tâches de flux Analytique de cette région. En raison de la latence dans la configuration de l’analyse, la création d’une autre tâche de flux de données Analytique dans la même région dans les 30 minutes vous invite pour la spécification d’un deuxième compte de stockage au lieu d’afficher celle récemment configuré dans la liste déroulante analyse de compte de stockage. Pour éviter la création d’un compte de stockage inutile, attendez 30 minutes après la création d’un travail dans une région pour la première fois avant la mise en service des tâches supplémentaires dans cette région.
Mise à niveau de la tâche|À ce stade, flux Analytique ne gère pas les modifications direct jusqu'à la définition ou la configuration d’une tâche en cours d’exécution. Pour modifier l’entrée, sortie, requête, échelle ou la configuration d’une tâche en cours d’exécution, vous devez d’abord arrêter le travail.
Types de données déduits à partir de la source d’entrée|Si une instruction CREATE TABLE n’est pas utilisée, le type d’entrée est dérivé de format d’entrée, par exemple tous les champs d’un CSV sont de type string. Champs doivent être explicitement convertis au type correct à l’aide de la fonction CAST pour éviter les erreurs d’incompatibilité de type.
Champs manquants sont envoyées en tant que valeurs null|Référence à un champ qui n’est pas présent dans la source d’entrée génère des valeurs null dans l’événement de sortie.
AVEC les instructions doivent précéder des instructions SELECT|Dans votre requête, les instructions SELECT doivent suivre des sous-requêtes définies avec les instructions.
Problème d’insuffisance de mémoire|Redémarrage de travaux d’Analytique en continu avec une grande tolérance pour les événements à la sortie de commande et/ou des requêtes complexes en conservant qu'une grande quantité d’état risque de manquer de mémoire, ce qui entraîne un travail de la tâche. Les opérations de démarrage et d’arrêt sera visibles dans les journaux d’opération de la tâche. Pour éviter ce comportement, mise à l’échelle la requête sur plusieurs partitions. Dans une version ultérieure, cette limitation sera résolue par la dégradation des performances sur les tâches d’impactés au lieu de les redémarrer.
Entrées de blob volumineux sans timestamp de la charge utile peuvent provoquer le problème d’insuffisance de mémoire|Consomme des fichiers volumineux depuis le stockage Blob peut entraîner des travaux de flux Analytique blocage si un champ d’horodatage n’est pas spécifié par le biais d’horodatage par. Pour éviter ce problème, conservez chaque blob inférieure à 10 Mo de taille.
Limitation de volume à des événements de la base de données SQL|Lors de l’utilisation de la base de données SQL comme une cible de sortie, de très gros volumes de données de sortie peuvent provoquer le travail Analytique de flux pour le délai d’attente. Pour résoudre ce problème, réduisez le volume de sortie à l’aide des agrégats ou des opérateurs de filtre, soit choisir à la place des concentrateurs d’événement ou de stockage des objets Blob Azure sous la forme d’une cible de sortie.
Une table ne peut contenir que des groupes de données PowerBI|PowerBI ne gère pas plus d’une table dans un groupe de données donné.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
