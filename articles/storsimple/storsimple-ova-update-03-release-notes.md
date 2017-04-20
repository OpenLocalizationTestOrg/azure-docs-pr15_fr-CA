<properties 
   pageTitle="Notes de publication de mises à jour de tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit les problèmes critiques en cours et des résolutions pour le tableau virtuel StorSimple exécute la mise à jour de 0,3."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/15/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-03-release-notes"></a>0,3 notes de mise à jour de tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes identifient les problèmes critiques et les problèmes résolus pour les mises à jour du tableau virtuel de Microsoft Azure StorSimple.

Les notes de publication sont mis à jour, et que les problèmes critiques nécessitant une solution de contournement sont découverts, ils sont ajoutés. Avant de déployer votre tableau virtuel StorSimple, lisez attentivement les informations contenues dans les notes de version.

Mise à jour de 0,3 correspond à la version de logiciel **10.0.10288.0**.

> [AZURE.NOTE] Mises à jour sont sans interruption de service et redémarrez l’appareil. Si les e/s sont en cours, le périphérique entraîne des interruptions de service.


## <a name="whats-new-in-the-update-03"></a>Quelles sont les nouveautés dans la mise à jour 0,3

Mise à jour de 0,3 est principalement une génération de résolution du bogue. Dans cette version, ce qui entraîne des échecs de sauvegarde dans la version précédente de plusieurs bogues ont été résolus.

## <a name="issues-fixed-in-the-update-03"></a>Problèmes résolus dans la mise à jour 0,3

Le tableau suivant fournit un résumé des problèmes corrigés dans cette version.

| N°  | Fonctionnalité                              | Problème                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | Sauvegardes                                |Un problème a été observé dans la version précédente échouent où les sauvegardes à effectuer pour un partage de fichiers. Si ce problème se produit, l’opération de sauvegarde échoue et une alerte critique a été déclenchée sur le service de gestionnaire de StorSimple pour notifier l’utilisateur. Ce problème n’a pas affecté les données sur les partages ou les accès aux données. L’origine du problème a été identifié et résolu dans cette version. <br></br> Le correctif ne s’applique pas rétroactivement aux partages que vous voyez déjà ce problème. Les clients qui voient ce problème doivent tout d’abord appliquer la mise à jour 0,3, puis contactez le Support Microsoft pour effectuer une sauvegarde complète du système pour résoudre le problème. Au lieu de contacter le Support Microsoft, clients peuvent également restaurer sur un nouveau partage à partir d’une sauvegarde en bon état pour les actions concernées.                                                                                                                                                                                 |
| 2    | iSCSI                         | Un problème a été observé dans la version précédente, où les volumes disparaît lors de la copie des données d’un volume sur le tableau virtuel StorSimple. Ce problème a été résolu dans cette version. <br></br> Les correctifs prennent effet uniquement sur nouvellement créé des volumes. Les correctifs ne s’appliquent pas rétroactivement aux volumes qui mesurent déjà ce problème. Il est conseillé aux utilisateurs à mettre les volumes affectés en ligne via le portail classique Azure, effectuer une sauvegarde de ces volumes et puis restaurer ces volumes vers de nouveaux volumes.                                                               |


## <a name="known-issues-in-the-update-03"></a>Problèmes connus dans la mise à jour 0,3

Le tableau suivant fournit un résumé des problèmes connus pour le tableau virtuel StorSimple et inclut les questions à partir des versions précédentes de noter-version. 


| N° | Fonctionnalité | Problème | Solution de contournement/commentaires |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Mises à jour | Les périphériques virtuels créés dans la version d’évaluation ne peut pas être mis à jour vers une version prise en charge de la disponibilité générale. | Ces périphériques virtuels doivent être basculées pour la version de mise à disposition générale à l’aide d’un flux de travail de reprise après sinistre. |
| **2.** | Disque de données mis en service | Une fois que vous avez déployé un disque de données d’une certaine taille spécifiée et créé le périphérique virtuel StorSimple correspondant, vous ne devez pas développer ou réduire le disque de données. Toute tentative entraîne une perte de toutes les données au niveau du périphérique local. |   |
| **3.** | Stratégie de groupe | Lorsqu’un périphérique est joint au domaine, application d’une stratégie de groupe peut affecter le fonctionnement du périphérique. | Assurez-vous que votre tableau virtuel se trouve dans sa propre unité d’organisation (UO) Active Directory et aucun objet de stratégie de groupe (GPO) n’appliqués.|
| **4.** | Interface utilisateur web de local | Si les fonctionnalités de sécurité améliorées sont activées dans Internet Explorer (IE ESC), certaines pages de l’interface utilisateur web local, tels que les opérations de dépannage ou de Maintenance peuvent ne pas fonctionnent correctement. Boutons sur ces pages ne peuvent également pas fonctionner. | Désactiver les fonctionnalités de sécurité renforcée d’Internet Explorer.|
| **5.** | Interface utilisateur web de local | Sur un ordinateur virtuel Hyper-V, les interfaces réseau sur le site web que l’interface utilisateur s’affichent sous forme de 10 Gbits/s les interfaces. | Ce comportement est une réflexion de Hyper-V. Hyper-V affiche toujours 10 Gbits/s pour les cartes réseau virtuelles. |
| **6.** | Plusieurs niveaux de volumes ou des partages | Plage d’octets de verrouillage pour les applications qui fonctionnent avec le StorSimple volumes monté en cascade n’est pas pris en charge. Si l’octet le verrouillage est activé, la hiérarchisation StorSimple ne fonctionne pas. | Mesures recommandées sont les suivantes : <br></br>Désactiver la plage d’octets de verrouillage dans votre logique d’application.<br></br>Choisir de placer les données de cette application dans les volumes ajoutés localement au lieu de volumes hiérarchisés.<br></br>*Avertissement*: lorsque l’aide localement épinglé volumes et octets le verrouillage est activé, le volume épinglé localement peut être en ligne avant même que la restauration est terminée. Dans ce cas, si une restauration est en cours, puis vous devez attendre la restauration. |
| **7.** | Partages hiérarchisés | Travailler avec des fichiers volumineux peut entraîner lente couche arrière. | Lorsque vous travaillez avec des fichiers volumineux, il est recommandé que le plus grand fichier inférieur à 3 % de la taille du partage. |
| **8.** | Utilisé de capacité pour les partages | Vous pouvez voir partager la consommation lorsqu’aucune donnée sur le partage. C’est parce que la capacité utilisée pour les partages inclut des métadonnées. |   |
| **9.** | Reprise après sinistre | Vous ne pouvez effectuer la récupération d’urgence d’un serveur de fichiers dans le même domaine que celui du périphérique source. Reprise après sinistre à un équipement cible dans un autre domaine n’est pas pris en charge dans cette version. | Il est implémenté dans une version ultérieure. |
| **10.** | PowerShell Azure | Les périphériques virtuels StorSimple ne peut pas être gérés via PowerShell Azure dans cette version. | Toute la gestion des périphériques virtuels doit être effectuée via le portail classique Azure et l’interface utilisateur de web local. |
| **11.** | Modification du mot de passe | La console de périphérique virtuel tableau accepte uniquement d’entrée au format de clavier en-US. |   |
| **12.** | CHAP | Impossible de supprimer les informations d’identification CHAP créées qu’une seule fois. En outre, si vous modifiez les informations d’identification CHAP, vous devez mettre les volumes hors ligne et ensuite les mettre en ligne pour que la modification soit prise en compte. | Ce problème est résolu dans une version ultérieure. |
| **13.** | serveur iSCSI  | Le 'a utilisé le stockage » affichée pour un volume iSCSI peut être différent dans le service Gestionnaire de StorSimple et de l’hôte iSCSI. | L’hôte iSCSI a la vue système de fichiers.<br></br>Le périphérique voit les blocs alloués lorsque le volume est à la taille maximale.|
| **14.** | Serveur de fichiers  | Si un fichier dans un dossier a un flux de données alternatifs (ADS) associé, les annonces n’est pas sauvegardé ou restauré par l’intermédiaire de reprise après sinistre, clone et la récupération de niveau élément.| |


## <a name="next-step"></a>Étape suivante

[Installez la mise à jour de 0,3](storsimple-ova-install-update-01.md) sur votre tableau virtuel StorSimple.

## <a name="references"></a>Références

La recherche d’une note de version antérieure ? Atteindre : 

- [Notes de mise à jour de StorSimple de la mise à jour de tableau virtuel 0,1 et 0,2](storsimple-ova-update-01-release-notes.md)

- [Notes de publication de disponibilité générale de tableau virtuel StorSimple](storsimple-ova-pp-release-notes.md)
 

