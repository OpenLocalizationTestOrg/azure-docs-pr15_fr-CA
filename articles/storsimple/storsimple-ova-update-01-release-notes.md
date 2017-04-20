<properties 
   pageTitle="Notes de publication de mises à jour de tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit les problèmes critiques en cours et des résolutions pour le tableau virtuel StorSimple exécute la mise à jour de 0,2 et 0,1."
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
   ms.date="06/16/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Notes de mise à jour du tableau virtuel 0,2 et 0,1 StorSimple

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes identifient les problèmes critiques et les problèmes résolus pour les mises à jour du tableau virtuel de Microsoft Azure StorSimple. (Tableau virtuel de Microsoft Azure StorSimple est également connu sous le nom du périphérique virtuel sur site de StorSimple ou le périphérique virtuel StorSimple.) 

Les notes de publication sont mis à jour, et que les problèmes critiques nécessitant une solution de contournement sont découverts, ils sont ajoutés. Avant de déployer votre périphérique virtuel StorSimple, lisez attentivement les informations contenues dans les notes de version.

Mise à jour de 0,2 correspond à la version du logiciel **10.0.10280.0**; Mise à jour de 0,1 est la version **10.0.10279.0**. Les sections suivantes répertorient les modifications pour chaque mise à jour. 

> [AZURE.NOTE] Mises à jour des interruptions d’activité et va redémarrer votre périphérique. Si les e/s sont en cours, le périphérique implique des temps d’arrêt.

## <a name="issues-fixed-in-the-update-02"></a>Problèmes résolus dans la mise à jour de 0,2
Mise à jour de 0,2 inclut toutes les modifications de la mise à jour de 0,1 en plus le correctif décrit dans le tableau suivant :

Fonctionnalité                              | Problème                                                                                                                                                                                                                                                                                                                           |
--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
Mises à jour                                 | Dans la dernière version, les mises à jour n’ont pas été détectés automatiquement dans Azure portal classique, afin que vous deviez utiliser l’interface utilisateur Web local pour installer les mises à jour. Ce problème est résolu dans cette version. Après avoir installé la mise à jour de 0,2, vous pouvez installer des mises à jour ultérieures à l’aide du portail classique Azure.                       

## <a name="whats-new-in-the-update-01"></a>Quelles sont les nouveautés dans la mise à jour 0,1

Mise à jour de 0,1 contient les correctifs de bogues et les améliorations suivantes. 

- **Résilience améliorée pour les interruptions de service cloud**: cette version a plusieurs résolutions de bogue autour de reprise après sinistre, la sauvegarde, la restauration et la hiérarchisation en cas d’interruption de la connectivité cloud. 

- **Amélioration des performances de restauration**: cette version comporte des correctifs de bogues ont considérablement réduire le temps d’exécution des tâches de restauration.

- **Optimisation de la récupération automatique espace**: lors de la suppression des données dans les volumes provisionnées, les blocs d’espace de stockage inutilisé doivent être récupérés. Cette version a amélioré le processus de récupération d’espace à partir du cloud entraînant l’espace inutilisé deviennent disponibles plus rapidement que les versions précédentes.

- **Nouvelles images de disque virtuel**: nouveau disque dur virtuel, VHDX et VMDK sont maintenant disponibles via le portail classique Azure. Vous pouvez télécharger ces images pour configurer de nouveaux appareils de mise à jour de 0,1.

- **Amélioration de la précision de l’état des travaux dans le portail**: dans la version antérieure du logiciel, l’état des travaux dans le portail n’était pas précis. Ce problème est résolu dans cette version.

- **Expérience de la jonction de domaine**: correctifs liés à jonction de domaine et changement de nom du périphérique.


## <a name="issues-fixed-in-the-update-01"></a>Problèmes résolus dans la mise à jour 0,1

Le tableau suivant fournit un résumé des problèmes corrigés dans cette version.

| N°  | Fonctionnalité                              | Problème                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | VMDK                                 | Dans certaines versions de VMware, le disque du système d’exploitation a été vu en creux à l’origine des alertes et perturber l’exploitation normale. Ce problème a été corrigé dans cette version.                                                                                                                                                                                    |
| 2    | serveur iSCSI                         | Dans la dernière version, l’utilisateur était requise pour spécifier une passerelle pour chaque interface de réseau activés de votre périphérique virtuel StorSimple. Ce comportement est modifié dans cette version, afin que l’utilisateur doit configurer au moins une passerelle pour toutes les interfaces réseau activée.                                                                              |
| 3    | Prise en charge                      | Dans la version antérieure du logiciel, prise en charge de collection du lot a échoué lors de la taille de lot était supérieure à 1 Go. Ce problème est résolu dans cette version.                                                                                                                                                                               |
| 4    | Accès du nuage                         |  La dernière version, si le tableau virtuel StorSimple ne dispose pas de connectivité réseau et a été redémarré, l’interface utilisateur local vous avez des problèmes de connectivité. Ce problème est résolu dans cette version.                                                                                                                            |
| 5    | Surveillance des graphiques                    | Dans la version précédente, après un basculement sur incident du périphérique, les graphiques de l’utilisation de capacité de nuage affichent des valeurs incorrectes dans Azure portal classique. Ce problème est corrigé dans la version actuelle.                                                                                                                          |



## <a name="known-issues-in-the-update-01"></a>Problèmes connus dans la mise à jour 0,1

Le tableau suivant fournit un résumé des problèmes connus pour le tableau virtuel StorSimple et inclut les questions à partir des versions précédentes de noter-version. **La version de problèmes de noter dans cette version sont marquées d’un astérisque. Presque tous les problèmes de cette liste ont reporté à partir de la version de GA de tableau virtuel de StorSimple.**


| N° | Fonctionnalité | Problème | Solution de contournement/commentaires |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Mises à jour | Les périphériques virtuels créés dans la version d’évaluation ne peut pas être mis à jour vers une version prise en charge de la disponibilité générale. | Ces périphériques virtuels doivent être basculées pour la version de mise à disposition générale à l’aide d’un flux de travail de reprise après sinistre. |
| **2.** | Disque de données mis en service | Une fois que vous avez déployé un disque de données d’une certaine taille spécifiée et créé le périphérique virtuel StorSimple correspondant, vous ne devez pas développer ou réduire le disque de données. Toute tentative entraîne une perte de toutes les données au niveau du périphérique local. |   |
| **3.** | Stratégie de groupe | Lorsqu’un périphérique est joint au domaine, application d’une stratégie de groupe peut affecter le fonctionnement du périphérique. | Assurez-vous que votre tableau virtuel se trouve dans sa propre unité d’organisation (UO) Active Directory et aucun objet de stratégie de groupe (GPO) n’appliqués.|
| **4.** | Interface utilisateur web de local | Si les fonctionnalités de sécurité améliorées sont activées dans Internet Explorer (IE ESC), certaines pages de l’interface utilisateur web local, tels que les opérations de dépannage ou de Maintenance peuvent ne pas fonctionnent correctement. Boutons sur ces pages ne peuvent également pas fonctionner. | Désactiver les fonctionnalités de sécurité renforcée d’Internet Explorer.|
| **5.** | Interface utilisateur web de local | Sur un ordinateur virtuel Hyper-V, les interfaces réseau sur le site web que l’interface utilisateur s’affichent sous forme de 10 Gbits/s les interfaces. | Ce comportement est une réflexion de Hyper-V. Hyper-V affiche toujours 10 Gbits/s pour les cartes réseau virtuelles. |
| **6.** | Plusieurs niveaux de volumes ou des partages | Plage d’octets de verrouillage pour les applications qui fonctionnent avec le StorSimple volumes monté en cascade n’est pas pris en charge. Si l’octet le verrouillage est activé, la hiérarchisation de la StorSimple ne fonctionnera pas. | Mesures recommandées sont les suivantes : <br></br>Désactiver la plage d’octets de verrouillage dans votre logique d’application.<br></br>Choisir de placer les données de cette application dans les volumes ajoutés localement au lieu de volumes hiérarchisés.<br></br>*Inconvénient*: si l’aide localement épinglé volumes et octets le verrouillage est activé, sachez que le volume épinglé localement peut être en ligne avant même que la restauration est terminée. Dans ce cas, si une restauration est en cours, puis vous devez attendre la restauration. |
| **7.** | Partages hiérarchisés | Travailler avec des fichiers volumineux peut entraîner lente couche arrière. | Lorsque vous travaillez avec des fichiers volumineux, il est recommandé que le plus grand fichier inférieur à 3 % de la taille du partage. |
| **8.** | Utilisé de capacité pour les partages | Vous pouvez voir partager la consommation en l’absence de toutes les données sur le partage. C’est parce que la capacité utilisée pour les partages inclut des métadonnées. |   |
| **9.** | Reprise après sinistre | Vous ne pouvez effectuer la récupération d’urgence d’un serveur de fichiers dans le même domaine que celui du périphérique source. Reprise après sinistre à un équipement cible dans un autre domaine n’est pas pris en charge dans cette version. | Ce est implémenté dans une version ultérieure. |
| **10.** | PowerShell Azure | Les périphériques virtuels StorSimple ne peut pas être gérés via PowerShell Azure dans cette version. | Toute la gestion des périphériques virtuels doit être effectuée via le portail classique Azure et l’interface utilisateur de web local. |
| **11.** | Modification du mot de passe | La console de périphérique virtuel tableau accepte uniquement d’entrée au format de clavier en-US. |   |
| **12.** | CHAP | Impossible de supprimer les informations d’identification CHAP créées qu’une seule fois. En outre, si vous modifiez les informations d’identification CHAP, vous devrez mettre les volumes hors ligne et ensuite les mettre en ligne pour que la modification soit prise en compte. | Il seront résolus dans une version ultérieure. |
| **13.** | serveur iSCSI  | Le 'a utilisé le stockage » affichée pour un volume iSCSI peut être différent dans le service Gestionnaire de StorSimple et de l’hôte iSCSI. | L’hôte iSCSI a la vue système de fichiers.<br></br>Le périphérique voit les blocs alloués lorsque le volume est à la taille maximale.|
| **14.** | Serveur de fichier *  | Si un fichier dans un dossier a un flux de données alternatifs (ADS) associé, les annonces n’est pas sauvegardé ou restauré par l’intermédiaire de reprise après sinistre, clone et la récupération de niveau élément.| |


## <a name="next-step"></a>Étape suivante

[Installer les mises à jour](storsimple-ova-install-update-01.md) sur votre tableau virtuel StorSimple.
