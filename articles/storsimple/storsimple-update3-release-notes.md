<properties 
   pageTitle="Notes de version 3 de mise à jour StorSimple 8000 série | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, les problèmes et les solutions de contournement pour la série de mise à jour 3 StorSimple 8000."
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
   ms.date="10/14/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-3-release-notes"></a>Notes de mise à StorSimple 8000 série mise à jour 3  

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes décrivent les nouvelles fonctionnalités et identifient les problèmes critiques pour la série de mise à jour 3 StorSimple 8000. Ils contiennent également une liste des mises à jour de logiciel StorSimple inclus dans cette version. 

Mise à jour 3 peut être appliqué à n’importe quel périphérique de StorSimple en cours d’exécution version (GA) ou la mise à jour de 0,1 à mise à jour 2.2. La version de périphérique associée à 3 de la mise à jour est 6.3.9600.17759.

Veuillez consulter les informations contenues dans les notes de version avant de déployer la mise à jour de votre solution de StorSimple.

>[AZURE.IMPORTANT]
> 
> - Mise à jour 3 a logiciel de périphérique, le pilote LSI et microprogramme et Storport et Spaceport mises à jour. Il faut environ 1,5 à 2 heures pour installer cette mise à jour. 

> - De nouvelles versions, vous ne voyiez pas les mises à jour immédiatement, étant donné que nous avons un déploiement échelonné de mises à jour. Patientez quelques jours et puis d’analyse des mises à jour à nouveau en tant qu’elles ne sera bientôt plus disponible.


## <a name="whats-new-in-update-3"></a>Quelles sont les nouveautés dans la mise à jour 3

Les améliorations clées suivantes et les correctifs de bogues ont été apportées à la mise à jour 3.

 
- **Modifications de récupération automatique d’espace** – démarrage mise à jour 3, les algorithmes de récupération d’espace s’exécuter sur le contrôleur de secours du système, ce qui entraîne une exécution plus rapide. Pour plus d’informations sur les ports nécessaires à la récupération de l’espace de travail, consultez les [exigences de mise en réseau StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

- **Amélioration des performances** – mise à jour 3 a amélioré les performances en lecture-écriture vers le nuage.

- **Migration des améliorations** – dans cette version, plusieurs correctifs de bogues et améliorations ont été effectués pour la fonction de Migration à partir de périphériques de série 5000/7000 à des périphériques série 8000. Pour plus d’informations sur la façon d’utiliser la fonctionnalité de migration, consultez [Migration de périphérique de la gamme de périphérique de la gamme 8000 5000/7000](https://www.microsoft.com/en-us/download/details.aspx?id=47322). 

- La **surveillance des correctifs** - dans cette version, les bogues relatifs à la surveillance des graphiques, des tableaux de bord de service et du tableau de bord périphérique ont été résolus.


## <a name="issues-fixed-in-update-3"></a>Problèmes résolus dans la mise à jour 3

Le tableau suivant fournit un récapitulatif des problèmes qui ont été résolus dans la mise à jour 3.    

| N° | Fonctionnalité                                    | Problème                                                                                                                                                                                                                                                                                        | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Migration des données du côté hôte                      | Dans la version précédente, la solution de Cloud StorSimple devait en mode hors connexion lors d’une migration de données côté hôte. Ce problème est résolu dans cette version.                                                | N°                        | Oui                        |
| 2  | Volumes ajoutés localement                     | Dans la version précédente, il existait des problèmes liés aux échecs d’e/s, les échecs de conversion de volume et échecs datapath pour les volumes ajoutés localement. Ces problèmes ont été causés à la racine et résolus dans cette version.                | Oui                        | N°                       |
| 3  | Surveillance                                    | Plusieurs problèmes liés à la génération des unités et de la surveillance et les graphiques de tableau de bord périphérique où s’affichait des informations incorrectes pour les volumes ajoutés localement sont. Ces problèmes sont résolus dans cette version. | Oui                         | N°                       |
| 4  | E/s d’écritures lourd                          | Lors de l’utilisation de StorSimple pour les charges de travail impliquant des écritures lourds, l’utilisateur s’exécute dans un bogue rare où le jeu de travail a été monté en cascade dans le nuage. Ce bogue est corrigé dans cette version. | Oui                        | Oui                       |
| 5  | Sauvegarde                                     | Dans certains cas rares, dans les versions précédentes du logiciel, lorsque l’utilisateur d’effectuer une sauvegarde d’un clone à distance, qu’ils fonctionnaient dans les erreurs de nuage et l’opération serait d’erreur. Dans cette version, le problème est résolu, l’opération se termine avec succès.             | Oui                        | Oui                       |
| 6  | Stratégie de sauvegarde                                     | Dans certains cas rares, les versions antérieures du logiciel, il y avait un bogue lié à la suppression de la stratégie de sauvegarde. Ce problème est résolu dans cette version.       | Oui                        | Oui                       |



## <a name="known-issues-in-update-3"></a>Problèmes connus dans la mise à jour 3

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
| 22 | Mises à jour | Lorsque vous appliquez la mise à jour 3, la page de **Maintenance** dans Azure portal classique affiche le message suivant lié à la mise à jour 2 - « série 8000 de StorSimple mise à jour 2 inclut la capacité à Microsoft de collecter des informations du journal proactive à partir de votre périphérique lorsque nous détecter les problèmes potentiels ». Cela est trompeur car il indique que le périphérique est mis à jour pour la mise à jour 2. Une fois que le périphérique est mis à jour pour la mise à jour 3 de succeesfully, ce message disparaîtra. | Ce problème sera résolu dans une prochaine version. | Oui | N° |


## <a name="controller-and-firmware-updates-in-update-3"></a>Mises à jour de contrôleur et le microprogramme de mise à jour 3

Cette version a LSI mises à jour de pilotes et de microprogrammes. Pour plus d’informations sur la façon d’installer le pilote LSI et les mises à jour du firmware, consultez [installation de mise à jour 3](storsimple-install-update-3.md) sur le périphérique StorSimple.

 
## <a name="virtual-device-updates-in-update-3"></a>Mises à jour de périphérique virtuel à la mise à jour 3

Cette mise à jour ne peut pas être appliqué à l’Appliance de nuage StorSimple (également connu sous le nom, le périphérique virtuel). Nouveaux périphériques virtuels seront doivent être créés. 


## <a name="next-step"></a>Étape suivante

Découvrez comment faire pour [installer la mise à jour 3](storsimple-install-update-3.md) sur le périphérique StorSimple.
