<properties 
   pageTitle="0,2 notes de mise à jour de 8000 StorSimple | Microsoft Azure"
   description="Décrit les nouvelles fonctions et correctifs, problèmes ouverts et des solutions de contournement disponibles pour le janvier 2015 Microsoft Azure StorSimple version (mise à jour de 0,2)."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />


# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>Notes de publication de 0,2 de mise à jour de la série StorSimple 8000 - janvier 2015

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes identifient les problèmes critiques pour la version de janvier 2015 de Microsoft Azure StorSimple. Ils contiennent également une liste StorSimple logiciels et microprogrammes mises à jour incluses dans cette version. Il s’agit de la deuxième version après que la version de StorSimple 8000 série a été rendue disponible en juillet 2014.
  
Cette mise à jour ne modifie pas la version de logiciel de périphérique physique à partir de la mise à jour d’octobre. Il continue à être la version 6.3.9600.17312. L’image utilisée par l’image du périphérique virtuel a été modifié dans cette version. Par conséquent, tous les nouveaux périphériques virtuels créés après le 1/20/2015 affichera la version en tant que 6.3.9600.17361.  

Veuillez consulter les informations suivantes, contenues dans les notes de version pour la mise à jour de janvier 2015.

> [AZURE.IMPORTANT]  
>
>- Cette mise à jour n’est pas disponible via Windows Update et ne peut pas être installé comme les autres mises à jour. Votre périphérique ne recevront pas cette mise à jour même si vous avez appliqué les mises à jour à l’aide du portail classique Azure. Cette mise à jour s’applique uniquement aux périphériques virtuels créés après le 20 janvier 2015. 
> 
>- La version de janvier de StorSimple ne contient-elle pas de mises à jour pour le périphérique physique StorSimple. Vous pouvez toujours appliquer les mises à jour Windows disponibles sur le périphérique virtuel, y compris des correctifs de sécurité récents, mais vous ne verrez pas un changement de version pour le périphérique physique StorSimple.

## <a name="whats-new-in-the-january-release"></a>Quelles sont les nouveautés dans la version de janvier

Cette mise à jour contient un correctif lié aux volumes de passer en mode hors connexion sur le périphérique virtuel. (Reportez-vous à la section [problèmes résolus dans cette version](#issues-fixed-in-the-january-release).)  

La mise à jour ne contient-elle pas de nouvelles fonctions ou fonctionnalités.  

## <a name="issues-fixed-in-the-january-release"></a>Problèmes résolus dans la version de janvier

Le tableau suivant décrit le problème qui a été corrigé dans cette mise à jour.

|N°|Fonctionnalité|Problème|S’applique à un périphérique physique|S’applique à un périphérique virtuel 
|---|-------|-----|--------------------------|-------------------------
|1|Volumes hors connexion|Lorsque les latences de nuage haute persistent pendant plusieurs minutes, les volumes de périphérique virtuel StorSimple hors ligne sur les hôtes. Ce correctif augmente le seuil de latence de nuage, minimisant ainsi les situations qui pourraient les volumes passer en mode hors connexion sur les hôtes.|N°|Oui  

## <a name="known-issues-in-the-january-release"></a>Problèmes connus dans la version de janvier

Le tableau suivant fournit un résumé des problèmes connus dans cette version.
 
|N°|Fonctionnalité|Problème|Commentaires/solution|S’applique à un périphérique physique|S’applique à un périphérique virtuel 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1| Usine par défaut|  Dans certains cas, lorsque vous effectuez une réinitialisation de l’usine, le périphérique StorSimple peuvent être bloqués et affiche ce message : **réinitialisation de la fabrique est en cours (phase 8).** Cela se produit si vous appuyez sur CTRL + C alors que l’applet de commande est en cours.| Après avoir lancé une réinitialisation de l’usine n’appuyez pas sur CTRL + C. Si vous êtes déjà dans cet état, veuillez contacter le Support Microsoft pour les étapes suivantes.|Oui|N°|
|2|Quorum de disque| Dans de rares cas, si la majorité des disques dans le boîtier de la EBOD d’un dispositif 8600 est déconnectée aucun quorum de disque, ce qui entraîne alors le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont reconnectés.|Vous devez redémarrer le périphérique. Si le problème persiste, veuillez contacter le Support Microsoft pour les étapes suivantes.|Oui |N°
|3|Échecs de capture instantanée de nuage|Dans de rares cas, un instantané de nuage peut échouer avec l’erreur **sauvegarde maximale atteinte**. Cela se produit si vous dépassez 255 clones en ligne sur le même périphérique, provenant du même volume d’origine qui a été supprimé.||Oui|Oui
|4|ID de contrôleur incorrecte|Lors d’un remplacement de contrôleur, contrôleur 0 apparaissent comme contrôleur 1. Pendant le remplacement de contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut afficheront initialement en tant qu’ID. du contrôleur de l’homologue  Dans de rares cas, ce comportement peut également survenir après un redémarrage du système.|Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même après que le remplacement du contrôleur est terminé.|Oui|N° 
|5| Dispositif de surveillance des graphiques|Dans le service Gestionnaire de StorSimple, les graphiques d’analyse de périphérique ne fonctionnent pas lorsque la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour le périphérique.|Modifier la configuration de proxy web pour le périphérique enregistré avec votre service de gestionnaire de StorSimple ainsi que l’authentification est définie sur aucun. Pour ce faire, exécutez le le Windows PowerShell pour l’applet de commande Set-HcsWebProxy de StorSimple.|Oui|Oui
|6| Comptes de stockage|À l’aide du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela entraîne une situation dans laquelle les données utilisateur ne sont pas accessibles.|| Oui |  Oui
|7|Basculement entre périphériques| Plusieurs basculements d’un conteneur de volume à partir du même périphérique source vers les équipements cibles différents ne sont pas pris en charge.| Basculement à partir d’un seul périphérique mort à plusieurs périphériques rend les conteneurs de volume sur le premier échec via un périphérique perdent la propriété de données. Après un tel basculement, ces conteneurs de volume apparaît ou se comportent différemment lorsque vous les affichez dans Azure portal classique.|Oui|N°
|8| Installation|Au cours de la carte de StorSimple pour l’installation de SharePoint, vous devez fournir une adresse IP du périphérique afin que l’installation se termine avec succès.||Oui|N°
|9| Proxy Web|Si votre configuration du serveur proxy web a HTTPS comme protocole spécifié, puis votre dispositif-service communication est affectée et accédera l’appareil mobile en mode hors connexion. Prise en charge sera également générée dans le processus, consomme énormément de ressources sur votre périphérique.|Assurez-vous que l’URL du proxy web a HTTP comme protocole spécifié. Reportez-vous à la section plus d’informations sur le [proxy web de configuration de votre périphérique](storsimple-configure-web-proxy.md).|Oui |N°
|10|Proxy Web|  Si vous configurez et activez le proxy web sur un périphérique enregistré, vous devrez redémarrer le contrôleur actif sur le périphérique.|| Oui |N°
|11|Temps de latence élevé nuage et la charge de travail d’e/s élevée|Lorsque votre périphérique StorSimple rencontre une combinaison de latences de nuage très élevé (ordre de secondes) et la charge de travail d’e/s élevée, les volumes de périphérique passer dans un état dégradé et les e/s peut échouer avec une erreur « périphérique non prêt ».|Vous devez redémarrer les contrôleurs de périphérique manuellement ou d’effectuer un basculement sur incident de périphérique pour résoudre ce problème.|Oui|N°

## <a name="physical-device-updates-in-the-january-release"></a>Publication des mises à jour de périphérique physique au mois de janvier

Cette mise à jour ne contient-elle pas d’autres modifications sur le périphérique StorSimple.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Mise à jour associée à la série un contrôleur SCSI (SAS) et les mises à jour de microprogramme au mois de janvier

Cette version ne contient-elle pas de mises à jour pour le contrôleur SCSI (SAS) attaché à la série ou du microprogramme. La mise à jour du pilote lors du octobre, version 2014. 

## <a name="virtual-device-updates-in-the-january-release"></a>Publication des mises à jour de périphérique virtuel au mois de janvier

Cette version contient une image mise à jour pour le périphérique virtuel. Tous les périphériques virtuels créés après le 20 janvier 2015, affiche la version du logiciel en tant que 6.3.9600.17361.

 
