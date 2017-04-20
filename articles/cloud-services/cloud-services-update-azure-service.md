<properties
pageTitle="La mise à jour d’un service en nuage | Microsoft Azure"
description="Découvrez comment mettre à jour les services en nuage dans Azure. Découvrez comment une mise à jour sur un service en nuage continue à assurer la disponibilité."
services="cloud-services"
documentationCenter=""
authors="Thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/10/2016"
ms.author="adegeo"/>

# <a name="how-to-update-a-cloud-service"></a>La mise à jour d’un service en nuage

## <a name="overview"></a>Vue d’ensemble
À 10 000 pieds, mise à jour d’un service en nuage, y compris ses rôles et le système d’exploitation, invité est un processus en trois étapes. Tout d’abord, les fichiers binaires et les fichiers de configuration pour la version de système d’exploitation ou le service en nuage doivent être téléchargés. Ensuite, Azure réserve les ressources informatiques et de réseau pour le service en nuage selon les exigences de la nouvelle version de service cloud. Enfin, Azure effectue une mise à niveau propagée pour la mise à jour incrémentielle le locataire à la nouvelle version ou invité du système d’exploitation, tout en préservant votre disponibilité. Cet article décrit les détails de cette dernière étape – la mise à niveau propagée.

## <a name="update-an-azure-service"></a>Mettre à jour un Service Azure

Azure organise vos instances de rôles dans des groupements logiques appelés mise à niveau de domaines (UD). Domaines de mise à niveau (UD) sont des ensembles logiques des instances de rôle sont mis à jour en tant que groupe.  Les mises à jour Azure un cloud service un UD à la fois, ce qui permet aux instances dans autres UDs afin de continuer le trafic.

Nombre de domaines de mise à niveau est 5. Vous pouvez spécifier un autre nombre de domaines de mise à niveau en incluant l’attribut upgradeDomainCount dans le fichier de définition du service (.csdef). Pour plus d’informations sur l’attribut upgradeDomainCount, consultez [Schéma de WebRole](https://msdn.microsoft.com/library/azure/gg557553.aspx) ou [WorkerRole](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Lorsque vous effectuez une mise à jour sur place d’un ou plusieurs rôles dans votre service, Azure met à jour les jeux d’instances de rôle en fonction de la mise à niveau domaine auquel ils appartiennent. Azure mises à jour de toutes les instances d’un domaine donné de mise à niveau – les arrêter, les mettre à jour, mettre les sauvegarder en ligne – se déplace ensuite vers le domaine suivant. En arrêtant les instances en cours d’exécution dans le domaine en cours de mise à niveau, Azure permet de s’assurer qu’une mise à jour se produit avec le moins d’impact possible sur le service en cours d’exécution. Pour plus d’informations, consultez [comment la mise à jour se poursuit](#howanupgradeproceeds) plus loin dans cet article.

> [AZURE.NOTE] Alors que les conditions de **mise à jour** et de **mise à niveau** ont une signification légèrement différente dans le contexte Azure, elles peuvent être utilisées indifféremment pour les processus et les descriptions des fonctionnalités de ce document.

Votre service doit définir au moins deux instances d’un rôle pour le rôle à mettre à jour sur place sans interruption de service. Si le service se compose d’une seule instance d’un rôle, votre service n’est pas disponible jusqu'à ce que la mise à jour sur place est terminée.

Cette rubrique décrit les informations suivantes sur les mises à jour Azure :

-   [Autorisé des modifications apportées au service au cours d’une mise à jour](#AllowedChanges)
-   [Comment se déroule la mise à niveau](#howanupgradeproceeds)
-   [Restauration d’une mise à jour](#RollbackofanUpdate)
-   [Lancer plusieurs opérations de mutation sur un déploiement en cours](#multiplemutatingoperations)
-   [Distribution des rôles sur les domaines de mise à niveau](#distributiondfroles)

<a name="AllowedChanges"></a>
## <a name="allowed-service-changes-during-an-update"></a>Autorisé des modifications apportées au service au cours d’une mise à jour
Le tableau suivant montre les modifications autorisées à un service au cours d’une mise à jour :

|Modifications autorisées pour l’hébergement, des services et des rôles|Mise à jour sur place|Intermédiaire (swap VIP)|Supprimer et redéployer|
|---|---|---|---|
|Version du système d’exploitation|Oui|Oui|Oui
|Niveau de confiance de .NET|Oui|Oui|Oui|
|Taille de machine virtuelle<sup>1</sup>|Oui<sup>2</sup>|Oui|Oui|
|Paramètres de stockage local|Augmenter uniquement<sup>2</sup>|Oui|Oui|
|Ajouter ou supprimer des rôles dans un service|Oui|Oui|Oui|
|Nombre d’instances d’un rôle particulier|Oui|Oui|Oui|
|Nombre ou type de points de terminaison pour un service|Oui<sup>2</sup>|N°|Oui|
|Noms et valeurs des paramètres de configuration|Oui|Oui|Oui|
|Valeurs (mais pas les noms) des paramètres de configuration|Oui|Oui|Oui|
|Ajouter de nouveaux certificats|Oui|Oui|Oui|
|Modification des certificats existants|Oui|Oui|Oui|
|Déploiement de nouveau code|Oui|Oui|Oui|
<sup>1</sup> Modification de la taille limitée au sous-ensemble de tailles disponibles pour le service en nuage.

<sup>2</sup> Nécessite Azure SDK 1.5 ou une version ultérieure.

> [AZURE.WARNING] Modification de la taille de l’ordinateur virtuel va détruire les données locales.


Les éléments suivants ne sont pas pris en charge au cours d’une mise à jour :

-   Modification du nom d’un rôle. Supprimez, puis ajoutez le rôle avec le nouveau nom.
-   Modification de l’inventaire de la mise à niveau de domaine.
-   Réduire la taille des ressources locales.

Si vous effectuez d’autres mises à jour à la définition de votre service, tels que la diminution de la taille d’une ressource locale, vous devez effectuer une mise à jour de swap VIP à la place. Pour plus d’informations, consultez [Déploiement de remplacement](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>
## <a name="how-an-upgrade-proceeds"></a>Comment se déroule la mise à niveau
Vous pouvez décider si vous souhaitez mettre à jour tous les rôles dans votre service ou à un rôle unique dans le service. Dans les deux cas, toutes les instances de chaque rôle qui est en cours de mise à niveau et appartiennent pour le premier domaine de mise à niveau sont arrêtés, mis à niveau et remis en ligne. Une fois qu’ils sont en ligne, les instances dans le deuxième domaine de mise à niveau sont arrêtés, mis à niveau et remis en ligne. Un service en nuage peut avoir au plus une mise à niveau active à la fois. La mise à niveau s’effectue toujours par rapport à la version la plus récente du service nuage.

Le diagramme suivant illustre la façon dont la mise à niveau se poursuit si vous mettez à niveau tous les rôles dans le service :

![Service de mise à niveau] (media/cloud-services-update-azure-service/IC345879.png "Service de mise à niveau")

Ce diagramme suivant illustre l’exécution de la mise à jour si vous mettez à niveau un seul rôle uniquement :

![Mise à niveau de rôle] (media/cloud-services-update-azure-service/IC345880.png "Mise à niveau de rôle")  

> [AZURE.NOTE] Lors de la mise à niveau d’un service à partir d’une seule instance pour plusieurs instances de votre service seront vers le bas lors de la mise à niveau est effectuée en raison des manière dont les services de mises à niveau Azure. La disponibilité de service garante de service contrat de niveau s’applique uniquement aux services qui sont déployées avec plus d’une instance. La liste suivante décrit comment les données sur chaque disque sont affectées à chaque scénario de mise à niveau du service Azure :
>
>Redémarrage de la machine virtuelle :
>
-   C: conservés
-   D: conservés
-   E: conservés
>
>Redémarrage de portail :
>
-   C: conservés
-   D: conservés
-   E: détruit
>
>Portail créer une nouvelle image suivantes :
>
-   C: conservés
-   D: détruit
-   E: détruit

>Mise à niveau :
>
-   C: conservés
-   D: conservés
-   E: détruit
>
>Migration de nœud :
>
-   C: destruction de
-   D: détruit
-   E: détruit

>Notez que, dans la liste ci-dessus, le lecteur E: représente le lecteur de racine du rôle et ne doit pas être codées en dur. À la place, utilisez la variable d’environnement % RoleRoot % pour représenter le lecteur.

>Pour réduire le temps d’arrêt lors de la mise à niveau d’un service à instance unique, déployer un nouveau service d’instances multiples sur le serveur intermédiaire et effectuer une permutation VIP.

Au cours d’une mise à jour automatique, le contrôleur de tissu Azure évalue périodiquement le fonctionnement du service de cloud pour déterminer le moment opportun parcourir le UD suivant. Cette évaluation sanitaire est effectuée sur une base par rôle et considère uniquement les instances dans la version la plus récente (c'est-à-dire les instances de l’UDs qui ont déjà été parcourues). Il vérifie si un nombre minimal d’instances de rôle, pour chaque rôle, ont permis d’atteindre un état terminal.

### <a name="role-instance-start-timeout"></a>Délai de démarrage Instance de rôle
Le contrôleur de tissu attend 30 minutes pour chaque instance du rôle d’atteindre un état démarré. Si la durée du délai d’attente expire, le contrôleur de tissu continuera à remonter à la prochaine instance de rôle.

<a name="RollbackofanUpdate"></a>
## <a name="rollback-of-an-update"></a>Restauration d’une mise à jour
Azure offre une grande souplesse dans la gestion des services au cours d’une mise à jour en vous permettant de lancer d’autres opérations sur un service, après acceptation de la demande de mise à jour initiale par le contrôleur de tissu Azure. Une restauration ne peut être effectuée que lorsqu’une mise à jour (modification de la configuration) ou de mise à niveau est dans l’état **en cours** sur le déploiement. Une mise à niveau ou la mise à jour est considérée comme en cours tant qu’il existe au moins une instance du service qui n’a pas encore été mis à jour vers la nouvelle version. Pour tester si une annulation est autorisée, vérifiez la valeur de l’indicateur RollbackAllowed, retourné par les opérations de [Déploiement d’obtenir](https://msdn.microsoft.com/library/azure/ee460804.aspx) et [d’Obtenir les propriétés de Service Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx) , a la valeur True.

> [AZURE.NOTE] Il convient uniquement à l’appel de Rollback sur une mise à jour **sur place** ou de mise à niveau, car les mises à niveau de swap VIP impliquent le remplacement d’une instance en cours d’exécution entière de votre service avec un autre.

Restauration d’une mise à jour en cours a les effets suivants sur le déploiement :

-   Toutes les instances de rôles qui n’avaient pas encore été mis à jour ou mis à niveau vers la nouvelle version ne sont pas mis à jour ou mise à niveau, parce que ces instances sont en cours d’exécution la version cible du service.
-   Toutes les instances de rôle qui avaient déjà été mis à jour ou mise à niveau vers la nouvelle version du package de service (\*.cspkg) fichier ou la configuration du service (\*.cscfg) (ou les deux fichiers) sont rétablies à la version de pré-mise à niveau de ces fichiers.

Cette fonctionnalité est fournie par les fonctionnalités suivantes :

-   L’opération [Restauration mise à jour ou de mise à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx) , qui peut être appelée sur une mise à jour de la configuration (déclenché par l’appel de [Changement de Configuration de déploiement](https://msdn.microsoft.com/library/azure/ee460809.aspx)) ou une mise à niveau (déclenché par l’appel de [Déploiement de mise à niveau](https://msdn.microsoft.com/library/azure/ee460793.aspx)) tant qu’il existe au moins une instance dans le service qui n’a pas encore été mis à jour vers la nouvelle version.
-   L’élément verrouillé et l’élément RollbackAllowed, qui sont retournées dans le cadre des opérations de [Déploiement d’obtenir](https://msdn.microsoft.com/library/azure/ee460804.aspx) et [Obtenir les propriétés de Service Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx) , le corps de réponse :
    1.  L’élément verrouillé permet de détecter le moment où une opération de mutation peut être appelée sur un déploiement donné.
    2.  L’élément RollbackAllowed vous permet de détecter lorsque l’opération de [Restauration mise à jour ou de mise à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx) peut être appelée sur un déploiement donné.

    Pour effectuer une restauration, il est inutile de vérifier à la fois le verrouillé et les éléments RollbackAllowed. Il suffit pour confirmer que la RollbackAllowed est définie sur true. Ces éléments ne sont renvoyées que si ces méthodes sont appelées à l’aide de l’en-tête de la requête la valeur « x-ms-version : 2011-10-01 » ou une version ultérieure. Pour plus d’informations sur les en-têtes de contrôle de version, consultez [Versioning de gestion de Service](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Il existe des situations où une restauration d’une mise à jour ou mise à niveau n’est pas pris en charge, il s’agit de la manière suivante :

-   Réduction des ressources locales - si la mise à jour augmente les ressources locales pour un rôle de la plateforme Azure n’autorise pas la restauration. 
-   Limitations de quota - si la mise à jour a été une échelle vers le bas l’opération, vous risquez de ne plus avoir quota de calcul suffisante pour terminer l’opération de restauration. Chaque abonnement Azure a un quota associé qui spécifie le nombre maximal de noyaux qui peuvent être utilisés par tous les services hébergés qui appartiennent à cet abonnement. Si effectuez une restauration d’une mise à jour donnée risque de placer votre abonnement sur quota, puis qui une annulation ne sera pas activée.
-   Condition de concurrence critique - si la mise à jour initiale est terminée, une restauration n’est pas possible.

Un exemple de cas la restauration d’une mise à jour peut s’avérer utile est que si vous utilisez l’opération de [Mise à niveau le déploiement](https://msdn.microsoft.com/library/azure/ee460793.aspx) en mode manuel pour contrôler la fréquence à laquelle une importante mise à niveau sur place vers votre Azure le service hébergé est déployée.

Lors du déploiement de la mise à niveau, vous appelez le [Déploiement d’une mise à niveau](https://msdn.microsoft.com/library/azure/ee460793.aspx) en mode manuel et commencez à parcourir les domaines de mise à niveau. Si à un moment donné, que vous surveillez la mise à niveau, vous remarquez que certaines instances de rôles dans les domaines de mise à niveau premier que vous examinez ont cesser de répondre, vous pouvez appeler l’opération de [Restauration mise à jour ou de mise à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx) sur le déploiement, ce qui laisse intactes les instances qui n’avaient pas encore été mis à niveau et des instances de restauration qui avaient été mis à niveau pour la configuration et le package de service précédente.

<a name="multiplemutatingoperations"></a>
## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Lancer plusieurs opérations de mutation sur un déploiement en cours
Dans certains cas, vous souhaiterez lancer plusieurs opérations de mutants simultanées sur un déploiement en cours. Par exemple, vous pouvez effectuer une mise à jour du service et, alors que cette mise à jour est déployée au sein de votre service, vous souhaitez apporter une modification, par exemple, pour restaurer la mise à jour précédente, appliquer une autre mise à jour, ou même supprimer le déploiement. Un cas dans lequel il peut être nécessaire est si une mise à niveau du service contient un code incorrect qui provoque une rôle mis à niveau l’instance de tomber en panne à plusieurs reprises. Dans ce cas, le contrôleur de tissu Azure ne sera pas en mesure de réaliser des progrès dans l’application que dans la mesure où un nombre insuffisant d’instances dans le domaine mis à niveau est en bon état de la mise à niveau. Cet état est appelé un *blocage du déploiement*. Vous pouvez décoller le déploiement par l’annulation de la mise à jour ou l’application d’une mise à jour fraîche au-dessus de l’échec d’une.

Une fois que la requête initiale à mettre à jour ou le service de mise à niveau a été reçue par le contrôleur de tissu Azure, vous pouvez lancer des opérations de mutants suivantes. Autrement dit, il est inutile d’attendre que l’opération initiale à effectuer avant de commencer une autre opération de mutation.

Lancement d’une deuxième opération de mise à jour lors de la première mise à jour est en cours va effectuer similaire à l’opération de restauration. Si la deuxième mise à jour est en mode automatique, le premier domaine de mise à niveau mise à niveau immédiatement, éventuellement conduisant à des instances de plusieurs domaines de mise à niveau est en mode hors connexion au même point dans le temps.

Les opérations de mutants sont les suivantes : [Configuration de déploiement pour modifier](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Mettre à niveau un déploiement](https://msdn.microsoft.com/library/azure/ee460793.aspx), [État de déploiement de mise à jour](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Supprimer le déploiement](https://msdn.microsoft.com/library/azure/ee460815.aspx)et [Restauration mise à jour ou de mise à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Deux opérations, le [Déploiement d’obtenir](https://msdn.microsoft.com/library/azure/ee460804.aspx) et d' [Obtenir les propriétés de Service Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx), retournent l’indicateur verrouillé qui peut être examiné pour déterminer si une opération de mutation peut être appelée sur un déploiement donné.

Pour appeler la version de ces méthodes qui retourne l’indicateur verrouillé, vous devez définir l’en-tête de demande « x-ms-version : 2011-10-01 » ou une version ultérieure. Pour plus d’informations sur les en-têtes de contrôle de version, consultez [Versioning de gestion de Service](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>
## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribution des rôles sur les domaines de mise à niveau
Azure répartit les instances d’un rôle de manière égale dans un certain nombre de domaines de mise à niveau, qui peut être configuré en tant que partie du fichier de définition (.csdef) du service. Le nombre maximal de domaines de mise à niveau est de 20 et la valeur par défaut est 5. Pour plus d’informations sur la façon de modifier le fichier de définition de service, voir la [Définition de schéma du Service d’Azure (.csdef fichier)](cloud-services-model-and-package.md#csdef).

Par exemple, si votre rôle dispose des instances de dix, par défaut chaque domaine de mise à niveau contient deux instances. Si votre rôle dispose des 14 instances, puis quatre des domaines des mise à niveau contiennent trois instances, et un cinquième domaine contient deux.

Domaines de mise à niveau sont identifiés par un index de base zéro : le premier domaine de mise à niveau a un ID 0, et le deuxième domaine de mise à niveau a un ID 1 et ainsi de suite.

Le diagramme suivant illustre comment un service contenant les deux rôles sont distribués lorsque le service définit deux domaines de mise à niveau. Le service est en cours d’exécution huit instances de rôle web et neuf du rôle Collaborateur.

![Distribution des domaines de mise à niveau] (media/cloud-services-update-azure-service/IC345533.png "Distribution des domaines de mise à niveau")

> [AZURE.NOTE] Notez que les contrôles Azure comment les instances sont allouées entre des domaines de mise à niveau. Il n’est pas possible de spécifier les instances sont allouées à quel domaine.

## <a name="next-steps"></a>Étapes suivantes
[La gestion des Services en nuage](cloud-services-how-to-manage.md)  
[Comment faire pour surveiller les Services en nuage](cloud-services-how-to-monitor.md)  
[Comment faire pour configurer les Services en nuage](cloud-services-how-to-configure.md)  
