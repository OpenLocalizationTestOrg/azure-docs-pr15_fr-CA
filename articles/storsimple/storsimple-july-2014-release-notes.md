<properties 
   pageTitle="Notes de version version de 8000 StorSimple | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, les problèmes ouverts et les solutions de contournement disponibles pour le juillet 2014 Microsoft Azure StorSimple version."
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

# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>Notes de Version de mise à jour de série StorSimple 8000 - juillet 2014 

## <a name="overview"></a>Vue d’ensemble

Les notes suivantes identifient des problèmes critiques pour la série 8000 StorSimple version de disponibilité générale (GA) de juillet 2014 de Microsoft Azure StorSimple. Cette version correspond à la version 6.3.9600.17215 du logiciel.  

Sauf indication contraire, ces notes de publication s’appliquent à tous les modèles du périphérique StorSimple. Les notes de publication sont constamment mis à jour ; problèmes critiques nécessitant une solution de contournement sont découverts, ils sont ajoutés. Avant de déployer votre solution Microsoft Azure StorSimple, tenez compte des informations suivantes.  

## <a name="known-issues-in-this-release"></a>Problèmes connus dans cette version
Le tableau suivant fournit un résumé des problèmes connus dans cette version.  
 
| N° | Fonctionnalité | Problème | Commentaires/solution | S’applique à un périphérique physique | S’applique à un périphérique virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Usine par défaut | Dans certains cas, lorsque vous effectuez une réinitialisation de l’usine, le périphérique StorSimple peuvent être bloqués et affiche ce message : **réinitialisation de la fabrique est en cours (phase 8)**. Cela se produit si vous appuyez sur CTRL + C alors que l’applet de commande est en cours. | Après avoir lancé une réinitialisation de l’usine n’appuyez pas sur CTRL + C. Si vous êtes déjà dans cet état, veuillez contacter le Support Microsoft pour les étapes suivantes. | Oui | N° |
| 2 | Quorum de disque | Dans de rares cas, si la majorité des disques dans le boîtier de la EBOD d’un dispositif 8600 est déconnectée aucun quorum de disque, ce qui entraîne alors le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont reconnectés. | Vous devez redémarrer le périphérique. Si le problème persiste, veuillez contacter le Support Microsoft pour les étapes suivantes. | Oui | N° |
| 3 | Échecs de capture instantanée de nuage | Dans de rares cas, un instantané de nuage peut échouer avec l’erreur **sauvegarde maximale atteinte**. Cela se produit si vous dépassez 255 clones en ligne sur le même périphérique, provenant du même volume d’origine qui a été supprimé. | | Oui | Oui |
| 4 | ID de contrôleur incorrecte | Lors d’un remplacement de contrôleur, contrôleur 0 apparaissent comme contrôleur 1. Pendant le remplacement de contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut afficheront initialement en tant qu’ID. du contrôleur de l’homologue Dans de rares cas, ce comportement peut également survenir après un redémarrage du système. | Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même après que le remplacement du contrôleur est terminé. | Oui | N° |
| 5 | Dispositif de surveillance des graphiques | Dans le service Gestionnaire de StorSimple, les graphiques d’analyse de périphérique ne fonctionnent pas lorsque la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour le périphérique. | Modifier la configuration de proxy web pour le périphérique enregistré avec votre service de gestionnaire de StorSimple ainsi que l’authentification est définie sur aucun. Pour ce faire, exécutez le le Windows PowerShell pour l’applet de commande Set-HcsWebProxy de StorSimple. | Oui | Oui |
| 6 | Comptes de stockage | À l’aide du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela entraîne une situation dans laquelle les données utilisateur ne sont pas accessibles. | | Oui | Oui |
| 7 | Retour arrière | Un retour en arrière dans les 24 heures de reprise après sinistre (DR) n’est pas pris en charge. | | Oui | N° |
| 8 | Basculement entre périphériques | Plusieurs basculements d’un conteneur de volume à partir du même périphérique source vers les équipements cibles différents ne sont pas pris en charge. Basculement à partir d’un seul périphérique mort à plusieurs périphériques rend les conteneurs de volume sur le premier échec via un périphérique perdent la propriété de données. Après un tel basculement, ces conteneurs de volume apparaît ou se comportent différemment lorsque vous les affichez dans Azure portal classique. | | Oui | N° |
| 9 | Installation | Au cours de la carte de StorSimple pour l’installation de SharePoint, vous devez fournir une adresse IP du périphérique pour que l’installation se termine avec succès. | | Oui | N° |
| 10 | Interfaces réseau | Interfaces réseau données 2 et données 3 ont été échangées sur le logiciel. | Veuillez contacter le Support Microsoft si vous devez configurer ces interfaces. | Oui | N° |


 
