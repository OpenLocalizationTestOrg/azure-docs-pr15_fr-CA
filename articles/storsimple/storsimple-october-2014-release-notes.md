<properties 
    pageTitle="0,1 notes de mise à jour de 8000 StorSimple | Microsoft Azure"
    description="Décrit les nouvelles fonctions et correctifs, problèmes ouverts et des solutions de contournement disponibles pour le 2014 octobre Microsoft Azure StorSimple version (mise à jour de 0,1)."
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
    ms.date="09/21/2016"
    ms.author="alkohli" />

# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>Notes de version 0,1 mise à jour de la série StorSimple 8000 – octobre 2014  

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes identifient les problèmes critiques de mise à jour de série StorSimple 8000 0,1 publiée en octobre 2014. Ils contiennent également une liste StorSimple logiciels et microprogrammes mises à jour incluses dans cette version. Il s’agit de la première version après que la version de StorSimple 8000 série a été rendue disponible en juillet 2014 et correspond à la version du logiciel 6.3.9600.17312.  

Nous vous recommandons de vous recherchez et appliquez des mises à jour disponibles immédiatement après avoir installé le périphérique. Vous pouvez également activer les mises à jour automatiques pour télécharger et installer les mises à jour prioritaires de Microsoft dès qu’ils sont disponibles. Pour plus d’informations, consultez la mise à [jour de votre périphérique StorSimple](storsimple-update-device.md).  

Veuillez consulter les informations contenues dans les notes de version avant de déployer les mises à jour de votre solution de StorSimple.  

>[AZURE.IMPORTANT]
> 
-   Pour installer les mises à jour d’octobre, utilisez le Gestionnaire de StorSimple service et pas de Windows PowerShell pour StorSimple.  
-   Les mises à jour généralement prennent environ 3 heures.  
-   La version d’octobre de StorSimple ne contient-elle pas de mises à jour pour le périphérique virtuel StorSimple. Vous pouvez toujours appliquer les mises à jour Windows disponibles, y compris des correctifs de sécurité récents, mais vous ne verrez pas un changement de version pour le périphérique virtuel.  

Assurez-vous que les conditions préalables suivantes sont remplies avant la mise à jour de votre périphérique StorSimple.  

- Assurez-vous que les deux contrôleurs de périphérique sont en cours d’exécution avant l’analyse des mises à jour. Si aucun contrôleur n’est pas exécuté, l’analyse échouera. Pour vérifier que les contrôleurs sont dans un état sain, accédez à **l’État du matériel** sous la page de **Maintenance** . S’il sont a des composants qui **l’attention nécessaire**, contactez le Support Microsoft avant de continuer.  
- Vérifiez que IPs fixe pour les deux contrôleur 0 et contrôleur 1 sont routables et peuvent se connecter à Internet sont utilisées pour traiter les mises à jour pour le périphérique. Vous pouvez utiliser l' [applet de commande Test-connexion](https://technet.microsoft.com/library/hh849808.aspx) ping sur une adresse connue en dehors du réseau, tels que outlook.com, pour vérifier que le contrôleur dispose d’une connectivité au réseau externe.  
- Vérifiez que les ports de sortie requis sont disponibles sur votre appareil de StorSimple pour la communication sortante. Pour plus d’informations, consultez la [Configuration requise pour votre dispositif de StorSimple de réseau](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
- Si la version logicielle du périphérique est plus ancienne que 6.3.9600.17312 (mise à jour d’octobre 2014), désactivez les ports de données 2 et données 3, si activé, avant de commencer la mise à jour. Si vous laissez les données 2 ou 3 de données ports activés lors de l’application de la mise à jour, il risque de votre contrôleur de périphérique passer en mode de récupération. Notez que lorsque vous désactivez les interfaces réseau, tous les volumes associés doit être mis hors ligne et l’e/s est interrompue pendant la durée de la mise à jour.  

## <a name="whats-new-in-the-october-release"></a>Quelles sont les nouveautés dans la version d’octobre

Cette mise à jour inclut les améliorations suivantes :

- Vous pouvez maintenant utiliser le service de gestionnaire de StorSimple l’interface utilisateur pour gérer les contrôleurs de votre périphérique. La gestion actions incluent redémarrage, l’arrêt, ou activer un contrôleur. Pour plus d’informations, accédez à [StorSimple de gérer les contrôleurs de périphérique](storsimple-manage-device-controller.md).  
- Vous pouvez planifier l’allocation de bande passante WAN selon les combinaisons de jour de la semaine et l’heure de la journée. Cela vous permet de mieux utiliser la bande passante WAN pendant les heures creuses. Modèles de bande passante différentes sont autorisées pour les conteneurs de volume différent. Pour plus d’informations, accédez à [gérer vos modèles de bande passante de StorSimple](storsimple-manage-bandwidth-templates.md).  
- Vous pouvez configurer des notifications par courrier électronique pour informer les administrateurs et autres utilisateurs des problèmes existants ou éventuellement à venir. Pour plus d’informations, consultez [configurer paramètres d’alerte](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Problèmes résolus dans la version d’octobre


Le tableau suivant fournit un récapitulatif des problèmes qui ont été corrigés dans cette mise à jour.  

| N° | Fonctionnalité | Problème | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Interfaces réseau | Dans la version précédente, les interfaces de réseau données 2 et données 3 ont été échangées dans le logiciel. Ce problème a été corrigé dans cette mise à jour. Veuillez effacer les paramètres et désactiver ces interfaces réseau avant d’installer la mise à jour. Après avoir installé la mise à jour, vous devez reconfigurer ces interfaces. | Oui | N° |
| 2 | Prise en charge | Dans la version précédente, si vous avez exécuté l’applet de commande Windows PowerShell **Exportation-HcsSupportPackage** pour récupérer les journaux du contrôleur BMC (Baseboard Management), l’opération a échoué avec le message d’avertissement suivant : « l’opération a réussi sur ce contrôleur, mais a échoué sur le contrôleur de l’homologue en raison des erreurs suivantes. Vérifiez que l’homologue est sain et si le nœud actuel peut se connecter à l’hôte. » Ce problème est maintenant résolu. | Oui | N° |
| 3 | Basculement entre périphériques | La version précédente, il y avait une chance d’incohérence des données en cas d’échec d’un travail de **sauvegarde de découvrir** lors d’un basculement de périphérique. Ce problème est maintenant résolu. | Oui | N° |
| 4 | Basculement entre périphériques | Dans la version précédente, après un basculement de périphérique, les sauvegardes étaient visibles, mais le conteneur de volume associé n’est pas présent sur le périphérique cible. Ce problème est maintenant résolu. | Oui | N° |
| 5 | Basculement entre périphériques | Dans la version précédente, il a été un bogue dans l’énumération des sauvegardes de nuage pendant l’opération de restauration du Registre qui pourrait conduire à une incohérence des données s’il y a des problèmes de connectivité de nuage. | Oui | N° |
| 6 | Mise à jour du firmware | Dans la version précédente, la tâche de mise à jour du microprogramme du périphérique a échoué et affiche une erreur qui a déclaré que les applets de commande n’ont pas étaient reconnus, et par conséquent l’échec de la mise à jour. Le contrôleur est passé ensuite en mode récupération. Ce problème est maintenant résolu. | Oui | N° |
| 7 | Installation | Erreurs causées par le périphérique n’est ne pas correctement image lors de l’installation ont été résolus. | Oui | N° |
| 8 | Usine par défaut | Maintenant vous pouvez éventuellement omettre de la vérification du microprogramme d’usine par défaut. Il s’agit d’une modification de la version précédente. | Oui | N° |
| 9 | Usine par défaut | Dans la version précédente, lors de l’exécution d’une applet de commande factory reset, vérification de la version du firmware uniquement pour certains composants matériels ont été effectuées. Vérifications de microprogrammes supplémentaires ont été effectuées après le premier redémarrage dans le processus, ce qui peut provoquer l’échec de la réinitialisation. Ce correctif s’assure que toutes les vérifications de microprogramme sont effectuées lors de la fabrique de réinitialiser l’applet de commande est exécutée et avant le premier système de redémarrer. | Oui | N° |
| 10 | Rotation de la clé de stockage compte | L’applet de commande **Invoke-HcsmServiceDataEncryptionKeyChange** utilisée pour faire pivoter les clés de compte de stockage à présent invite l’utilisateur à entrer la clé de cryptage de données de service. Il s’agit d’une modification de la version précédente, dans laquelle la clé de cryptage de données de service a été passée comme paramètre en ligne. | Oui | N° |
| 11 | Retour en arrière dans les 24 heures | Lors de la récupération d’urgence, le nettoyage sur le périphérique source ne s’est pas passé restauration proprement, provoquant l’échec. Ce problème a été corrigé dans cette version. | Oui | N° |

## <a name="known-issues-in-the-october-release"></a>Problèmes connus dans la version d’octobre

Le tableau suivant fournit un résumé des problèmes connus dans cette version.

| N° | Fonctionnalité | Problème | Commentaires/solution | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Usine par défaut | Dans certains cas, lorsque vous effectuez une réinitialisation de l’usine, le périphérique StorSimple peuvent être bloqués et affiche ce message : **réinitialisation de la fabrique est en cours (phase 8)**. Cela se produit si vous appuyez sur CTRL + C alors que l’applet de commande est en cours. | Après avoir lancé une réinitialisation de l’usine n’appuyez pas sur CTRL + C. Si vous êtes déjà dans cet état, veuillez contacter le Support Microsoft pour les étapes suivantes. | Oui | N° |
| 2 | Usine par défaut | Faire pas en usine réinitialiser un périphérique StorSimple qui est mis à jour à partir de GA octobre 2014 mise à jour. | Cette opération fonctionne uniquement si un correctif est installé. Contactez le Support Microsoft pour obtenir ce correctif requis. | Oui | N° | 
| 3 | Quorum de disque | Dans de rares cas, si la majorité des disques dans le boîtier de la EBOD d’un dispositif 8600 est déconnectée aucun quorum de disque, ce qui entraîne alors le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont reconnectés. | Vous devez redémarrer le périphérique. Si le problème persiste, veuillez contacter le Support Microsoft pour les étapes suivantes. | Oui | N° |
| 4 | Échecs de capture instantanée de nuage | Dans de rares cas, un instantané de nuage peut échouer avec l’erreur **sauvegarde maximale atteinte**. Cela se produit si vous dépassez 255 clones en ligne sur le même périphérique, provenant du même volume d’origine qui a été supprimé. | | Oui | Oui |
| 5 | ID de contrôleur incorrecte | Lors d’un remplacement de contrôleur, contrôleur 0 apparaissent comme contrôleur 1. Pendant le remplacement de contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut afficheront initialement en tant qu’ID. du contrôleur de l’homologue Dans de rares cas, ce comportement peut également survenir après un redémarrage du système. |Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même après que le remplacement du contrôleur est terminé. | Oui | N° |
| 6 | Dispositif de surveillance des graphiques | Dans le service Gestionnaire de StorSimple, les graphiques d’analyse de périphérique ne fonctionnent pas lorsque la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour le périphérique. | Modifier la configuration de proxy web pour le périphérique enregistré avec votre service de gestionnaire de StorSimple ainsi que l’authentification est définie sur aucun. Pour ce faire, exécutez le le Windows PowerShell pour l’applet de commande Set-HcsWebProxy de StorSimple. | Oui | Oui |
| 7 | Comptes de stockage | À l’aide du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela entraîne une situation dans laquelle les données utilisateur ne sont pas accessibles. | | Oui | Oui |
| 8 | Basculement entre périphériques | Plusieurs basculements d’un conteneur de volume à partir du même périphérique source vers les équipements cibles différents ne sont pas pris en charge. | Basculement à partir d’un seul périphérique mort à plusieurs périphériques rend les conteneurs de volume sur le premier échec via un périphérique perdent la propriété de données. Après un tel basculement, ces conteneurs de volume apparaît ou se comportent différemment lorsque vous les affichez dans Azure portal classique. | Oui | N° |
| 9 | Installation | Au cours de la carte de StorSimple pour l’installation de SharePoint, vous devez fournir une adresse IP du périphérique afin que l’installation se termine avec succès.    | | Oui | N° |
| 10 | Proxy Web | Si votre configuration du serveur proxy web a HTTPS comme protocole spécifié, puis votre dispositif-service communication est affectée et accédera l’appareil mobile en mode hors connexion. Prise en charge sera également générée dans le processus, consomme énormément de ressources sur votre périphérique. | Assurez-vous que l’URL du proxy web a HTTP comme protocole spécifié. Plus d’informations sur le [proxy web de configuration de votre périphérique](storsimple-configure-web-proxy.md). | Oui | N° |
| 11 | Proxy Web | Si vous configurez et activez le proxy web sur un périphérique enregistré, vous devrez redémarrer le contrôleur actif sur le périphérique. | | Oui | N° |
| 12 | Temps de latence élevé nuage et la charge de travail d’e/s élevée | Lorsque votre périphérique StorSimple rencontre une combinaison de latences de nuage très élevé (ordre de secondes) et la charge de travail d’e/s élevée, les volumes de périphérique passer dans un état dégradé et les e/s peut échouer avec une erreur « périphérique non prêt ». | Vous devez redémarrer les contrôleurs de périphérique manuellement ou d’effectuer un basculement sur incident de périphérique pour résoudre ce problème. | Oui | N° |

## <a name="physical-device-updates-in-the-october-release"></a>Publication des mises à jour de périphérique physique dans l’octobre

Lorsque ces mises à jour sont appliquées à un périphérique physique, la version du logiciel devient 6.3.9600.17312. Sauf indication contraire, ces notes de publication s’appliquent à tous les modèles du périphérique StorSimple. Pour plus d’informations sur ces mises à jour, consultez [octobre 2014 physique logicielle mise à jour pour la solution matérielle-logicielle de Microsoft Azure StorSimple](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Attachement série un contrôleur SCSI (SAS) et les mises à jour du firmware d’octobre à la mise à jour

Cette version met à jour le pilote et le micrologiciel sur le contrôleur SAS de votre périphérique physique. Pour plus d’informations sur le contrôleur SAS mise à jour, consultez [2014 octobre mise à jour pour les contrôleurs LSI SAS dans la solution matérielle-logicielle de Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987020).   

Cette version s’applique également à une mise à jour du firmware cumulative qui résout des problèmes de fiabilité avec les composants matériels du périphérique. Pour plus d’informations sur la mise à jour du firmware, reportez-vous à la section [firmware octobre 2014 mise à jour pour la solution matérielle-logicielle de Microsoft Azure StorSimple](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Publication des mises à jour de périphérique virtuel dans l’octobre

Cette version ne contient-elle pas de mises à jour pour le périphérique virtuel. L’application de cette mise à jour ne modifie pas la version du logiciel d’un périphérique virtuel.
 
