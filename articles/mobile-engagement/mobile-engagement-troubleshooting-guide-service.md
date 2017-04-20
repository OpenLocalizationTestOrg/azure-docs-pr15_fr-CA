<properties 
   pageTitle="Engagement de Mobile Azure Guide - Service de dépannage" 
   description="Résolution des problèmes de repères pour Azure Engagement Mobile" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-service-issues"></a>Guide de dépannage pour les problèmes de Service

Les éléments suivants sont des problèmes possibles que vous pouvez rencontrer avec l’Engagement de Mobile Azure s’exécute.

## <a name="service-outages"></a>Interruptions de service

### <a name="issue"></a>Problème
- Problèmes qui semblent provenir d’Azure Mobile Engagement des interruptions de Service.

### <a name="causes"></a>Causes
- Problèmes qui semblent être provoqué par des pannes de Service Engagement Azure Mobile peuvent être dû à plusieurs problèmes différents :
    - Problèmes qui apparaissent initialement systémiques à l’ensemble de l’Engagement Mobile Azure
    - Problèmes causés par des pannes de serveur (pas toujours s’affiche dans l’état du serveur) :
    - Planification des retards, erreurs de ciblage, problèmes de mise à jour de Badge, arrêt de statistiques collecte, la transmission cesse de fonctionner, cessent de fonctionner, les nouvelles applications ou les utilisateurs de l’API Impossible de créer des erreurs DNS et délai d’attente dans l’interface utilisateur, les API ou les applications sur un périphérique.
    - Nuage dépendance pannes [statut Service Azure](http://status.azure.com/)
    - Poussez les pannes de dépendance (solution) de Notification Services
    - Pannes d’App Store

1) Pour un test pour voir si le problème est systémique, vous pouvez tester la même fonction à partir d’un autre
   
   - Application d’Engagement Mobile intégré Azure
   - Dispositif de test
   - Version de système d’exploitation périphérique de test
   - Campagne
   - Compte d’utilisateur administrateur
   - Navigateur (Internet Explorer, Firefox, Chrome, etc.)
   - Ordinateur

2) Pour tester si le problème n’affecte que l’interface utilisateur ou de l’API :

   - Test de la même fonction à partir de l’interface utilisateur de Azure Mobile Engagement et de l’API de Azure Mobile Engagement.

3) Pour tester si le problème vient de votre réseau de téléphone cellulaire :

   - Lorsque vous êtes connecté à Internet via le Wi-Fi et lorsque vous êtes connecté via votre réseau de téléphone cellulaire 3G de test.
   - Vérifiez que votre pare-feu ne bloque pas les Ports ou les adresses IP de Azure Mobile Engagement.

4) Pour tester si le problème avec votre périphérique :

   - Tester si votre appareil est capable de se connecter à Azure Mobile Engagement avec une autre application intégrée Azure Mobile Engagement.
   - Tests que vous pouvez générer des incidents, les tâches et les événements à partir de votre téléphone qui peut être affiché dans l’interface utilisateur de Azure Mobile Engagement. 
   - Vérifier si vous pouvez envoyer des notifications de type pousser à partir de l’interface utilisateur Engagement Azure Mobile sur votre appareil en fonction de son ID de périphérique. 

5) Pour tester si le problème survient avec votre application :

   - Installer et tester votre application à partir d’un émulateur et non à partir d’un périphérique physique :
   
6) Pour tester si avec mises à niveau du système d’exploitation pour l’utilisateur final périphériques qui nécessitent une mise à niveau du Kit de développement logiciel pour résoudre le problème :

   - Tester votre application sur différents périphériques avec des versions différentes du système d’exploitation.
   - Vérifiez que vous utilisez la version la plus récente du Kit de développement.
 
## <a name="connectivity-and-incorrect-information-issues"></a>Problèmes de connectivité et des informations incorrectes

### <a name="issue"></a>Problème
- Problèmes de connexion à l’interface utilisateur de Azure Mobile Engagement.
- Erreurs de connexion avec l’API de Azure Mobile Engagement.
- Problèmes de téléchargement des balises d’Info application via l’API de périphérique.
- Problèmes lors du téléchargement des journaux ou des données exportées à partir de l’Engagement de Mobile Azure.
- Informations incorrectes affichées dans l’interface utilisateur de Azure Mobile Engagement.
- Affiché dans les journaux Azure Mobile Engagement des informations incorrectes.

### <a name="causes"></a>Causes
* Vérifiez que votre compte d’utilisateur dispose des autorisations suffisantes pour effectuer la tâche.
* Vérifiez que le problème n’est pas limité à un seul ordinateur ou votre réseau local.
* Vérifiez que que le service Azure Mobile Engagement n’a pas signalé d’interruptions.
* Vérifiez que vos fichiers App Info balise suivent toutes ces règles :
    - Utilisez uniquement le jeu de caractères UTF8 (jeu de caractères ANSI n’est pas pris en charge).
    - Utilisez une virgule «, » comme séparateur (vous pouvez ouvrir un service demande afin de demander à changer le caractère de séparation .csv à partir d’une virgule «, » d’un autre caractère comme un point-virgule « ; »).
    - Utiliser des minuscules pour les valeurs booléennes « true » et « false ».
    - Utilisez un fichier plus petit que la taille de fichier maximale de 35 Mo.
 
