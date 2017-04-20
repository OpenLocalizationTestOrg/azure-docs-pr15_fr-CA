<properties 
   pageTitle="0,3 notes de mise à jour de 8000 StorSimple | Microsoft Azure"
   description="Décrit les nouvelles fonctions et correctifs, problèmes ouverts et des solutions de contournement disponibles pour le février 2015 Microsoft Azure StorSimple version (mise à jour de 0,3)."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>Notes de publication de 0,3 de mise à jour de la série StorSimple 8000 - février 2015

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes identifient les problèmes critiques pour la mise à jour de série StorSimple 8000 0,3 publiée en février 2015. Ils contiennent également une liste StorSimple logiciels et microprogrammes mises à jour incluses dans cette version. C’est la troisième version après que la version de StorSimple 8000 série a été rendue disponible en juillet 2014.
  
Cette mise à jour ne modifie pas la version logicielle du périphérique à partir de la mise à jour de janvier. Il continue à être la version 6.3.9600.17312. Vous pouvez confirmer que la mise à jour a été installé en vérifiant la date de **Dernière mise à jour** . Si la date est le 2/10/2015 ou version ultérieure, puis la mise à jour a été installée avec succès.  

Nous vous recommandons de vous recherchez et appliquez des mises à jour disponibles immédiatement après l’installation de votre périphérique StorSimple. Vous pouvez également activer les mises à jour automatiques pour télécharger et installer les mises à jour prioritaires de Microsoft dès qu’ils sont disponibles. Pour plus d’informations, consultez [mise à jour de votre périphérique StorSimple](storsimple-update-device.md).  

Veuillez consulter les informations contenues dans les notes de version avant de déployer la mise à jour de votre solution de StorSimple.  

>[AZURE.IMPORTANT]   
>
> - Utilisez le Gestionnaire de StorSimple service et pas de Windows PowerShell pour StorSimple pour installer la mise à jour de février.   
> - Il prend environ une heure pour installer cette mise à jour. Toutefois, si vous installez des mises à jour cumulatives, le processus peut prendre environ 3 heures.  
> - La version de février de StorSimple ne contient-elle pas de mises à jour pour le périphérique virtuel StorSimple. Vous pouvez toujours appliquer les mises à jour Windows disponibles sur le périphérique virtuel, y compris des correctifs de sécurité récents, mais vous ne verrez pas un changement de version pour le périphérique virtuel.  

Assurez-vous que les conditions préalables suivantes sont remplies avant la mise à jour de votre périphérique StorSimple.  

- Assurez-vous que les deux contrôleurs de périphérique sont en cours d’exécution avant l’analyse des mises à jour. Si aucun contrôleur n’est pas exécuté, l’analyse échouera. Pour vérifier que les contrôleurs sont dans un état sain, accédez à **l’État du matériel** sous la page de **Maintenance** . S’il sont a des composants qui **l’attention nécessaire**, contactez le Support Microsoft avant de continuer.
- Assurez-vous que les IPs fixe pour les contrôleurs 0 et 1 sont routables et peuvent se connecter à Internet sont utilisées pour traiter les mises à jour pour le périphérique. Vous pouvez utiliser l' [applet de commande Test-connexion](https://technet.microsoft.com/library/hh849808.aspx) ping sur une adresse connue en dehors du réseau, telles que outlook.com, pour vérifier que le contrôleur dispose d’une connectivité au réseau externe.
- Vérifiez que les ports 80 et 443 sont disponibles sur le périphérique StorSimple pour la communication sortante. Pour plus d’informations, consultez la [Configuration requise pour votre dispositif de StorSimple de réseau](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- Si la version logicielle du périphérique est plus ancienne que 6.3.9600.17312 (mise à jour d’octobre 2014), désactivez les ports de données 2 et données 3, si activé, avant de commencer la mise à jour. En laissant les données 2 ou 3 de données ports activés lorsque vous appliquez la mise à jour risque de votre contrôleur de périphérique passer en mode de récupération. Notez que lorsque vous désactivez les interfaces réseau, tous les volumes associés doit être mis hors ligne et les e/s est interrompue pendant la durée de la mise à jour.  
  
## <a name="whats-new-in-the-february-release"></a>Quelles sont les nouveautés dans la version de février

Cette mise à jour contient un correctif pour la fabrique de réinitialisation du problème qui s’est produite sur les périphériques qui ont été mis à niveau à partir de la GA mise à jour vers la version d’octobre 2014. Pour plus d’informations, reportez-vous à la section [problèmes résolus dans cette version](#issues-fixed-in-the-february-release).   

Cette mise à jour ne contient-elle pas de nouvelles fonctions ou fonctionnalités.  

## <a name="issues-fixed-in-the-february-release"></a>Problèmes résolus dans la version de février

Le tableau suivant décrit le problème qui a été corrigé dans cette mise à jour.  
 
| N° | Fonctionnalité | Problème | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|-----|---------|-------|---------------------------------|-------------------------------|
| 1 | Usine par défaut | Vous essayez d’effectuer une fabrique de réinitialiser sur un périphérique à l’origine avait la version GA (version 6.3.9600.17215) installée, mais a été mis à jour au mois d’octobre version (version 6.3.9600.17312). Échec de réinitialisation de l’usine et le périphérique devient instable. | Oui | N° |


## <a name="known-issues-in-the-february-release"></a>Problèmes connus dans la version de février

Le tableau suivant fournit un résumé des problèmes connus dans cette version.
 
| N° | Fonctionnalité | Problème | Commentaires/solution | S’applique à un périphérique physique  | S’applique à un périphérique virtuel |
|-----|---------|-------|----------------------------|-----------------------------|--------------------------|
| 1 | Usine par défaut | Dans certains cas, lorsque vous effectuez une réinitialisation de l’usine, le périphérique StorSimple peuvent être bloqués et affiche ce message : **réinitialisation de la fabrique est en cours (phase 8)**. Cela se produit si vous appuyez sur CTRL + C alors que l’applet de commande est en cours. | Après avoir lancé une réinitialisation de l’usine n’appuyez pas sur CTRL + C. Si vous êtes déjà dans cet état, veuillez contacter le Support Microsoft pour les étapes suivantes. | Oui | N° |
| 2 | Quorum de disque | Dans de rares cas, si la majorité des disques dans le boîtier de la EBOD d’un 8600device est déconnectée aucun quorum de disque, ce qui entraîne alors le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont reconnectés. | Vous devez redémarrer le périphérique. Si le problème persiste, veuillez contacter le Support Microsoft pour les étapes suivantes. | Oui | N° |
| 3 | Échecs de capture instantanée de nuage | Dans de rares cas, un instantané de nuage peut échouer avec l’erreur **sauvegarde maximale atteinte**. Cela se produit si vous dépassez 255 clones en ligne sur le même périphérique, provenant du même volume d’origine qui a été supprimé. |  | Oui | Oui |
| 4 | ID de contrôleur incorrecte | Lors d’un remplacement de contrôleur, contrôleur 0 apparaissent comme contrôleur 1. Pendant le remplacement de contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut afficheront initialement en tant qu’ID. du contrôleur de l’homologue Dans de rares cas, ce comportement peut également survenir après un redémarrage du système. | Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même après que le remplacement du contrôleur est terminé. | Oui | N° |
| 5 | Dispositif de surveillance des graphiques | Dans le service Gestionnaire de StorSimple, les graphiques d’analyse de périphérique ne fonctionnent pas lorsque la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour le périphérique. | Modifier la configuration de proxy web pour le périphérique enregistré avec votre service de gestionnaire de StorSimple ainsi que l’authentification est définie sur aucun. Pour ce faire, exécutez le le Windows PowerShell pour l’applet de commande Set-HcsWebProxy de StorSimple. | Oui | Oui |
| 6 | Comptes de stockage | À l’aide du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela entraîne une situation dans laquelle les données utilisateur ne sont pas accessibles. |  | Oui | Oui |
| 7 | Basculement entre périphériques | Plusieurs basculements d’un conteneur de volume à partir du même périphérique source vers les équipements cibles différents ne sont pas pris en charge.  Basculement à partir d’un seul périphérique mort à plusieurs périphériques rend les conteneurs de volume sur le premier échec via un périphérique perdent la propriété de données. Après un tel basculement, ces conteneurs de volume apparaît ou se comportent différemment lorsque vous les affichez dans Azure portal classique. |   | Oui | N° |
| 8 | Installation | Au cours de la carte de StorSimple pour l’installation de SharePoint, vous devez fournir une adresse IP du périphérique afin que l’installation se termine avec succès. |  | Oui | N° |
| 9 | Proxy Web | Si votre configuration du serveur proxy web a HTTPS comme protocole spécifié, puis votre dispositif-service communication est affectée et accédera l’appareil mobile en mode hors connexion. Prise en charge sera également générée dans le processus, consomme énormément de ressources sur votre périphérique. | Assurez-vous que l’URL du proxy web a HTTP comme protocole spécifié. Plus d’informations sur le [proxy web de configuration de votre périphérique](storsimple-configure-web-proxy.md). | Oui | N° |
| 10 | Proxy Web | Si vous configurez et activez le proxy web sur un périphérique enregistré, vous devrez redémarrer le contrôleur actif sur le périphérique. |  | Oui | N° |
| 11 | Temps de latence élevé nuage et la charge de travail d’e/s élevée | Lorsque votre périphérique StorSimple rencontre une combinaison de latences de nuage très élevé (ordre de secondes) et la charge de travail d’e/s élevée, les volumes de périphérique passer dans un état dégradé et les e/s peut échouer avec une erreur « périphérique non prêt ». | Vous devez redémarrer les contrôleurs de périphérique manuellement ou d’effectuer un basculement sur incident de périphérique pour résoudre ce problème. | Oui | N° |

## <a name="physical-device-updates-in-the-february-release"></a>Publication des mises à jour de périphérique physique dans le de février

Cette mise à jour résout le problème de réinitialisation de fabrique qui s’est produite sur les périphériques qui ont été mis à niveau à partir de la GA mise à jour vers la version d’octobre 2014. Il ne contient-elle pas d’autres mises à jour sur le périphérique StorSimple.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Attachement série un contrôleur SCSI (SAS) et les mises à jour du firmware dans le février mise à jour

Cette version ne contient-elle pas de mises à jour pour le contrôleur SCSI (SAS) attaché à la série ou du microprogramme. La mise à jour du pilote lors du octobre, version 2014.  

## <a name="virtual-device-updates-in-the-february-release"></a>Publication des mises à jour de périphérique virtuel dans le de février

Cette version ne contient-elle pas de mises à jour pour le périphérique virtuel. L’application de cette mise à jour ne modifie pas la version du logiciel d’un périphérique virtuel.
 
