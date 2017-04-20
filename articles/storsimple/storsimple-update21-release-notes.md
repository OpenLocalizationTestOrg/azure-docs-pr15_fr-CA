<properties 
   pageTitle="Notes de version 2.2 de mise à jour de série de 8000 StorSimple | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, les problèmes et les solutions de contournement pour StorSimple 8000 série de mise à jour 2.2."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-22-release-notes"></a>Notes de version 2.2 mise à jour de StorSimple 8000 Series  

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes décrivent les nouvelles fonctionnalités et identifient les problèmes critiques pour StorSimple 8000 série de mise à jour 2.2. Ils contiennent également une liste des mises à jour de logiciel StorSimple inclus dans cette version. 

Mise à jour 2.2 peut être appliqué à n’importe quel périphérique de StorSimple en cours d’exécution version (GA) ou la mise à jour de 0,1 à 2.1 de mise à jour. La version de périphérique associée à 2.2 de la mise à jour est 6.3.9600.17708.

Veuillez consulter les informations contenues dans les notes de version avant de déployer la mise à jour de votre solution de StorSimple.

>[AZURE.IMPORTANT]
> 
> - Mise à jour 2.2 a uniquement mises à jour de logiciel. Il faut environ 1,5 à 2 heures pour installer cette mise à jour. 

> - Si vous exécutez 2.1 de mise à jour, nous vous conseillons d’appliquer 2.2 de la mise à jour dès que possible.

> - De nouvelles versions, vous ne voyiez pas les mises à jour immédiatement, étant donné que nous avons un déploiement échelonné de mises à jour. Patientez quelques jours et puis d’analyse des mises à jour à nouveau en tant qu’elles ne sera bientôt plus disponible.


## <a name="whats-new-in-update-22"></a>Quelles sont les nouveautés dans la mise à jour 2.2

Les améliorations clées suivantes ont été apportées dans la mise à jour 2.2.

 
- **Optimisation de récupération automatique d’espace** – lors de la suppression de données sur des volumes provisionnées, les blocs d’espace de stockage inutilisé doivent être récupérés. Cette version a amélioré le processus de récupération d’espace à partir du cloud entraînant l’espace inutilisé deviennent disponibles plus rapidement que les versions précédentes.


- **Amélioration des performances de capture instantanée** – mise à jour 2.2 a amélioré la durée de traitement d’un nuage de capture instantanée dans certains scénarios où grands volumes sont utilisés et il est minime à aucune évolution de données. Les volumes d’archives est un scénario qui peut bénéficier de cette amélioration.


- **Collecte de renforcement de la prise en charge** – il y a eu des améliorations dans la façon dont le package de prise en charge est collectée et téléchargé dans cette version. 


- **Mettre à jour les améliorations de fiabilité** – cette version comporte des correctifs qui entraînent une amélioration de la fiabilité mise à jour.

  
 

## <a name="issues-fixed-in-update-22"></a>Problèmes résolus dans la mise à jour 2.2

Le tableau suivant fournit un récapitulatif des problèmes qui ont été résolus dans les mises à jour 2.2 et 2.1.    

| N° | Fonctionnalité                                    | Problème                                                                                                                                                                                                                                                                                        | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Performances de l’hôte                      | Dans la version précédente, les problèmes de performances du côté hôte ont été observées lors de la création d’un volume local épinglé et lors de la conversion d’un volume monté en cascade sur un volume local épinglé. Ces problèmes sont résolus dans cette version, ce qui entraîne une amélioration des performances pendant les procédures de création et la conversion du volume hôte.                                                                        | Oui                        | N°                        |
| 2  | Volumes ajoutés localement                     | Dans de rares cas, le système se bloquait lors de la création d’un volume local épinglé. Ce bogue a été corrigé dans cette version.                                                                                                                                                               | Oui                        | N°                        |
| 3  | Montage en cascade                                    | Ont été des incidents sporadiques lorsque les métadonnées pour les appareils de nuage StorSimple (8010 et 8020) montés en cascade dans le nuage. Ce problème est résolu dans cette version.                                                                                                                              | N°                         | Oui                       |
| 4  | Création de snapshots                          | Il existait des problèmes liés à la création d’instantanés incrémentiels dans les scénarios avec des volumes importants et minimes à aucune évolution de données. Ces problèmes sont résolus dans cette version.                                                                                                                 | Oui                        | Oui                       |
| 5  | Authentification d’Openstack                   | Lorsque vous utilisez Openstack comme le fournisseur de services de nuage, l’utilisateur s’exécute dans un bogue rare lié à l’authentification où l’analyseur JSON a provoqué une panne. Ce bogue est corrigé dans cette version.                                                                                                                              | Oui                        | N°                        |
| 6  | Copie du côté hôte                             | Dans les versions antérieures du logiciel, un bogue rare lié à la synchronisation du fichier ODX a été détecté lors de la copie des données d’un volume vers un autre volume. Cela donne lieu à un basculement sur incident du contrôleur et le système pourrait potentiellement passer en mode récupération. Ce bogue est corrigé dans cette version. | Oui                        | N°       |
| 7  | Windows Management Instrumentation (WMI) | Dans les versions précédentes du logiciel, il y avait plusieurs instances de l’échec du proxy web avec l’exception «<ManagementException> Échec de chargement du fournisseur ». Ce bogue a été attribué à une fuite de mémoire WMI et est désormais résolu.                                                               | Oui                        | N°                        |
| 8  | Mise à jour                                     | Dans certains cas rares, dans les versions précédentes du logiciel, l’utilisateur a reçu un « CisPowershellHcsscripterror » lorsque vous tentez d’analyser ou d’installer des mises à jour. Ce problème est résolu dans cette version.                                                                                        | Oui                        | Oui                       |
| 9  | Prise en charge                            | Dans cette version, les améliorations apportées à la façon dont le package de prise en charge est collectée, puis téléchargé ont été.                                                                                                                                                                                                      | Oui                        | Oui                                    |


## <a name="known-issues-in-update-22"></a>Problèmes connus dans la mise à jour 2.2

Le tableau suivant fournit un résumé des problèmes connus dans cette version.

| N° | Fonctionnalité | Problème | Commentaires / solution | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum de disque | Dans de rares cas, si la majorité des disques dans le boîtier de la EBOD d’un dispositif 8600 est déconnectée entraînant aucun quorum de disque, puis le pool de stockage est hors ligne. Il restera en mode hors connexion, même si les disques sont reconnectés. | Vous devez redémarrer le périphérique. Si le problème persiste, veuillez contacter le Support Microsoft pour les étapes suivantes. | Oui | N° |
| 2 | ID de contrôleur incorrecte | Lors d’un remplacement de contrôleur, contrôleur 0 apparaissent comme contrôleur 1. Pendant le remplacement de contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut afficheront initialement en tant qu’ID. du contrôleur de l’homologue Dans de rares cas, ce comportement peut également survenir après un redémarrage du système. | Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même après que le remplacement du contrôleur est terminé. | Oui | N° |
| 3 | Comptes de stockage | À l’aide du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela entraîne une situation dans laquelle les données utilisateur ne sont pas accessibles.|  | Oui | Oui |
| 4 | Basculement entre périphériques | Plusieurs basculements d’un conteneur de volume à partir du même périphérique source vers les équipements cibles différents ne sont pas pris en charge. Basculement à partir d’un seul périphérique mort à plusieurs périphériques rend les conteneurs de volume sur le premier échec via un périphérique perdent la propriété de données. Après un tel basculement, ces conteneurs de volume apparaît ou se comportent différemment lorsque vous les affichez dans Azure portal classique. | | Oui | N° |
| 5 | Installation | Au cours de la carte de StorSimple pour l’installation de SharePoint, vous devez fournir une adresse IP du périphérique afin que l’installation se termine avec succès.    | | Oui | N° |
| 6 | Proxy Web | Si votre configuration du serveur proxy web a HTTPS comme protocole spécifié, puis votre dispositif-service communication est affectée et accédera l’appareil mobile en mode hors connexion. Prise en charge sera également générée dans le processus, consomme énormément de ressources sur votre périphérique. | Assurez-vous que l’URL du proxy web a HTTP comme protocole spécifié. Pour plus d’informations, accédez au [proxy web de configurer votre périphérique](storsimple-configure-web-proxy.md). | Oui | N° |
| 7 | Proxy Web | Si vous configurez et activez le proxy web sur un périphérique enregistré, vous devrez redémarrer le contrôleur actif sur le périphérique. | | Oui | N° |
| 8 | Temps de latence élevé nuage et la charge de travail d’e/s élevée | Lorsque votre périphérique StorSimple rencontre une combinaison de latences de nuage très élevé (ordre de secondes) et la charge de travail d’e/s élevée, les volumes de périphérique passer dans un état dégradé et les e/s peut échouer avec une erreur « périphérique non prêt ». | Vous devez redémarrer les contrôleurs de périphérique manuellement ou d’effectuer un basculement sur incident de périphérique pour résoudre ce problème. | Oui | N° |
| 9 | PowerShell Azure | Lorsque vous utilisez l’applet de commande Get-AzureStorSimpleStorageAccountCredential de **des StorSimple & #124 ; Select-Object - attente d’abord 1 -** pour sélectionner le premier objet de sorte que vous pouvez créer un nouvel objet **VolumeContainer** , l’applet de commande renvoie tous les objets. | Retour à la ligne l’applet de commande entre parenthèses comme suit : **(Get-Azure-StorSimpleStorageAccountCredential) & #124 ; Select-Object - attente d’abord 1 -** | Oui | Oui |
| 10| Migration | Lorsque plusieurs conteneurs de volume sont passées pour la migration, l’ATE de la dernière sauvegarde est précise uniquement pour le premier conteneur de volume. En outre, migration parallèle démarrera après avoir migrent les sauvegardes des 4 premiers dans le premier conteneur de volume. | Nous vous recommandons de migrer un conteneur de volume à la fois. | Oui | N° |
| 11| Migration | Après la restauration, les volumes ne sont pas ajoutés à la stratégie de sauvegarde ou du groupe de disque virtuel. | Vous devez ajouter ces volumes à une stratégie de sauvegarde pour créer des sauvegardes. | Oui | Oui |
| 12| Migration | Une fois la migration terminée, le périphérique série 5000/7000 ne doit pas accéder les conteneurs de données migrées. | Nous vous recommandons de supprimer les conteneurs de données ayant migré après que la migration est terminé et validé. | Oui | N° |
| 13| Clone et reprise après sinistre | Un périphérique StorSimple 1 de mise à jour en cours d’exécution ne peut pas cloner ou effectuer la reprise après sinistre à un périphérique qui exécute le logiciel de 1 avant mise à jour. | Vous devez mettre à jour le périphérique cible pour la mise à jour de 1 pour permettre ces opérations | Oui | Oui |
| 14 | Migration | Sauvegarde de la configuration de la migration peut échouer sur un périphérique de la gamme 5000-7000 lorsqu’il existe des groupes de volumes sans volume associé. | Supprimer tous les groupes de volume vide sans volume associée, puis relancez la sauvegarde de la configuration.| Oui | N° |
| 15 | Applets de commande PowerShell Azure et volumes ajoutés localement | Impossible de créer un volume localement ajouté via les applets de commande PowerShell d’Azure. (N’importe quel volume que vous créez via PowerShell d’Azure va être monté en cascade.) |Toujours utiliser le service Gestionnaire de StorSimple pour configurer les volumes ajoutés localement.| Oui | N° |
| 16 |Espace disponible pour les volumes ajoutés localement | Si vous supprimez un volume localement épinglé, l’espace disponible pour les nouveaux volumes de ne peut pas être mis à jour immédiatement. Le service Gestionnaire de StorSimple met à jour l’espace local environ toutes les heures.| Patientez une heure avant de tenter de créer le nouveau volume. | Oui | N° |
| 17 | Volumes ajoutés localement | Votre travail de restauration expose la sauvegarde temporaire de snapshot dans le catalogue de sauvegarde, mais uniquement pour la durée de l’opération de restauration. En outre, il expose un groupe de disque virtuel avec le préfixe **tmpCollection** sur la page de **Stratégies de sauvegarde** , mais uniquement pour la durée de l’opération de restauration. | Ce problème peut se produire si votre opération de restauration a épinglé que localement de volumes ou un mélange de localement les volumes épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas. Aucune intervention de l’utilisateur n’est requise. | Oui | N° |
| 18 | Volumes ajoutés localement | Si vous annulez une opération de restauration et un basculement sur incident du contrôleur se produit immédiatement le travail de restauration affiche par la suite, **Échec** et non **annulé**. Si un travail de restauration échoue et un basculement sur incident du contrôleur se produit immédiatement le travail de restauration affiche par la suite, **annulée** au lieu de **l’Échec**. | Ce problème peut se produire si votre opération de restauration a épinglé que localement de volumes ou un mélange de localement les volumes épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas. Aucune intervention de l’utilisateur n’est requise. | Oui | N° |
| 19 |Volumes ajoutés localement | Si vous annulez une opération de restauration ou si une restauration échoue puis un basculement sur incident du contrôleur, un travail de restauration supplémentaires s’affiche sur la page **tâches** . | Ce problème peut se produire si votre opération de restauration a épinglé que localement de volumes ou un mélange de localement les volumes épinglés et hiérarchisés. Si le travail de restauration inclut uniquement les volumes hiérarchisés, puis ce problème se produira pas. Aucune intervention de l’utilisateur n’est requise. | Oui | N° |
| 20 |Volumes ajoutés localement | Si vous essayez de convertir un volume monté en cascade (créé et cloné avec mise à jour 1.2 ou version antérieur) sur un volume local épinglé et votre périphérique est espace insuffisant ou il y a une panne de nuage, l’onglet « clone » peut être endommagés.| Ce problème se produit uniquement avec les volumes qui ont été créés et cloné avec PRE-logiciel mise à jour 2.1. Il s’agit d’un scénario rare.|
| 21 | Conversion de volume | Ne pas mettre à jour les ACRs attachés à un volume lors de la conversion d’un volume est en cours (monté en cascade à épinglé localement ou vice versa). Mise à jour de l’ACRs peut entraîner une corruption des données. | Si nécessaire, les ACRs avant la conversion du volume de mise à jour et ne faites pas d’autres mises à jour de blocage lorsque la conversion est en cours. |

## <a name="controller-and-firmware-updates-in-update-22"></a>Contrôleur à jour dans la mise à jour 2.2

Cette version comporte des mises à jour de logiciel uniquement. Toutefois, si vous mettez à jour à partir d’une version antérieure à la mise à jour 2, vous devrez installer le pilote Storport, Spaceport et, dans certains cas, mises à jour du microprogramme de votre périphérique de disque.
 
Pour plus d’informations sur la façon d’installer le pilote Storport, Spaceport et mises à jour du microprogramme de disque, voir [installer la mise à jour 2.2](storsimple-install-update-21.md) sur le périphérique StorSimple.

 
## <a name="virtual-device-updates-in-update-22"></a>Mises à jour de périphérique virtuel dans la mise à jour 2.2

Cette mise à jour ne peut pas être appliqué au périphérique virtuel. Nouveaux périphériques virtuels seront doivent être créés. 

## <a name="next-step"></a>Étape suivante

Découvrez comment faire pour [installer la mise à jour 2.2](storsimple-install-update-21.md) sur le périphérique StorSimple.
