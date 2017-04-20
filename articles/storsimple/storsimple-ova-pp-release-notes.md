<properties 
   pageTitle="Notes de publication de tableau virtuel de StorSimple | Microsoft Azure"
   description="Décrit les problèmes critiques en cours et des résolutions pour le tableau virtuel StorSimple."
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
   ms.date="05/13/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-release-notes"></a>Notes de publication de tableau virtuel de StorSimple

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes identifient les problèmes critiques pour la version de mise à disposition générale (GA) mars 2016 du Microsoft Azure StorSimple virtuel tableau (également appelé le périphérique virtuel local de StorSimple ou le périphérique virtuel de StorSimple). Cette version correspond à la version 10.0.10271.0 du logiciel.

Les notes de publication sont mis à jour, et que les problèmes critiques nécessitant une solution de contournement sont découverts, ils sont ajoutés. Avant de déployer votre périphérique virtuel StorSimple, lisez attentivement les informations contenues dans les notes de version. 

Le tableau suivant fournit un résumé des problèmes connus dans cette version.


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
