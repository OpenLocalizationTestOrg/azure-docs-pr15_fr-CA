<properties
 pageTitle="Vue d’ensemble de gestion de périphérique IoT Hub | Microsoft Azure"
 description="Cet article fournit une vue d’ensemble de la gestion des périphériques dans Azure IoT concentrateur : périphérique entreprise du cycle de vie, redémarrage, usine par défaut, mise à jour du firmware, configuration, jumeaux de périphérique, des requêtes, travaux"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/03/2016"
 ms.author="bzurcher"/>

# <a name="overview-of-azure-iot-hub-device-management-preview"></a>Présentation de la gestion de périphérique concentrateur de IoT Azure (aperçu)

## <a name="introduction"></a>Introduction

Concentrateur de IoT Azure fournit les fonctionnalités et un modèle d’extensibilité qui permettent aux périphériques et back-end aux développeurs de créer des solutions de gestion de périphérique IoT robustes. IoT périphériques vont de capteurs de contraintes et seul but Microcontrôleurs, à des passerelles puissantes qui acheminent les communications pour les groupes de périphériques.  En outre, les cas d’usage et de la configuration requise pour les opérateurs IoT varie considérablement entre les secteurs d’activité.  Malgré cette variation, gestion des périphériques de concentrateur de IoT Azure fournit les capacités, les motifs et les bibliothèques de code pour répondre à un ensemble varié de périphériques et les utilisateurs finaux.

Un élément crucial de créer avec succès une entreprise solution IoT pour fournir une stratégie pour comment les opérateurs de gérer la gestion permanente de leur collection de périphériques. Les opérateurs IoT nécessitent des outils simples et fiables et des applications qui leur permettent de se concentrer sur les aspects les plus stratégiques de leur travail. Cet article fournit :

- Une brève présentation de l’approche Azure IoT concentrateur au Gestionnaire de périphérique.
- Une description des principes de gestion des périphériques courants.
- Description du cycle de vie de périphérique.
- Vue d’ensemble des modèles de gestion de périphérique courants.

## <a name="iot-device-management-principles"></a>Principes de gestion de périphérique IoT

IoT offre un ensemble unique de défis de gestion de dispositif, et chaque solution d’entreprise doit traiter les principes suivants :

![Graphique du principes de gestion du périphérique IoT concentrateur Azure][img-dm_principles]

- **Échelle et automation**: IoT solutions nécessitent des outils simples qui peuvent automatiser les tâches de routine et activer un peu au personnel de gérer des millions de périphériques. Au quotidien, les opérateurs s’attendent à gérer à distance les opérations de périphérique, en vrac et uniquement être averti lorsque des problèmes surviennent qui nécessitent leur attention directe.

- **Ouverture et compatibilité**: IoT l’écosystème de périphériques est extraordinairement divers. Outils de gestion doivent être adaptées pour tenir compte d’une multitude de protocoles, de plates-formes et de classes de périphériques. Opérateurs doivent être en mesure de prendre en charge de nombreux types de périphériques, à partir de puces de processus unique, plus contraignants en embedded, sur des ordinateurs puissants et entièrement fonctionnels.

- **Prise de conscience de contexte**: IoT environnements sont dynamiques et en constante évolution. La fiabilité du service est primordiale. Les opérations de gestion de périphérique doivent prendre en compte SLA des fenêtres de maintenance, les États d’alimentation et de réseau, les conditions d’utilisation et dispositif de géolocalisation pour garantir des interruptions de service maintenance n’affectent les opérations critiques ou créez des conditions dangereuses.

- **Service de nombreux rôles**: prise en charge pour les flux de travail unique et les processus IoT des rôles d’opérations est cruciale. Le personnel d’exploitation doit fonctionnent en harmonie avec les contraintes donnés de départements internes.  Ils doivent également découvrir comment durable des informations sur les opérations en temps réel de surface périphérique pour les superviseurs et les autres rôles de gestion d’entreprise.

## <a name="iot-device-lifecycle"></a>Cycle de vie de périphérique IoT

Il existe un ensemble d’étapes de gestion de dispositif général qui sont communes à tous les projets d’entreprise IoT. Il existe cinq étapes dans le cycle de vie du dispositif IoT dans Azure IoT :

![Les cinq phases du cycle de vie de périphérique Azure IoT : planifier, de configurer, de configurer, de surveiller, de retraite][img-device_lifecycle]

Dans chacun de ces cinq étapes, il existe plusieurs conditions d’opérateur de périphérique qui doivent être remplies pour fournir une solution complète :

- **Plan**: permettre aux opérateurs de créer un schéma de métadonnées de périphérique qui leur permet de facilement et précisément pour la requête et de cibler un groupe de périphériques pour les opérations de gestion en bloc. Vous pouvez utiliser le double dispositif pour stocker des métadonnées de ce dispositif sous la forme de balises et propriétés.

    *Informations supplémentaires*: [mise en route de jumeaux de périphérique][lnk-twins-getstarted], [jumeaux de périphérique comprendre][lnk-twins-devguide], [comment utiliser les propriétés double][lnk-twin-properties]

- **Disposition**: configurer de nouveaux appareils à IoT concentrateur et de permettre aux opérateurs de détecter immédiatement les fonctionnalités du périphérique en toute sécurité.  Le Registre de périphérique IoT Hub permet de créer des informations d’identification et les identités de dispositif flexible et effectuer cette opération en bloc à l’aide d’une tâche. Permet de créer des périphériques pour signaler leurs possibilités et les conditions via les propriétés de périphérique dans le double du périphérique.

    *Informations supplémentaires*: [Gérer les identités de périphérique][lnk-identity-registry], [gestion en bloc des identités de périphérique][lnk-bulk-identity], [comment utiliser les propriétés double][lnk-twin-properties]

- **Configurer**: faciliter les modifications de configuration en bloc et mises à jour du firmware des périphériques tout en préservant l’intégrité et la sécurité. Effectuer ces opérations de gestion de périphérique en vrac à l’aide des propriétés ou méthodes directes et les travaux de diffusion.

    *Informations supplémentaires*: [utiliser les méthodes directes][lnk-c2d-methods], [appeler une méthode directe sur un périphérique][lnk-methods-devguide], [comment utiliser deux propriétés][lnk-twin-properties], [planification et diffusion des travaux][lnk-jobs], [planifier des tâches sur plusieurs périphériques][lnk-jobs-devguide]

- **Moniteur**: périphérique collection état de santé général, l’état des opérations en cours, de surveiller et d’avertir les utilisateurs des problèmes qui peuvent nécessiter une attention particulière.  Appliquer le double dispositif pour autoriser les périphériques à signaler des conditions d’exploitation en temps réel et l’état des opérations de mise à jour. Permet de générer des rapports de tableau de bord puissants qui les problèmes plus immédiats de surface à l’aide de requêtes de double dispositif.

    *Informations supplémentaires*: [comment utiliser les propriétés double][lnk-twin-properties], [langage de requête pour les travaux et jumeaux][lnk-query-language]

- **Déclassement**: remplacer ou de retirer des périphériques après une défaillance, cycle, de la mise à niveau ou à la fin de la durée de vie du service.  Permet de tenir à jour les informations du périphérique si le périphérique physique est le double de périphérique remplacé ou archivés si en cours de déclassement. Utiliser le Registre de périphérique IoT concentrateur de révocation en toute sécurité les informations d’identification et les identités des appareils.

    *Informations supplémentaires*: [comment utiliser les propriétés double][lnk-twin-properties], [Gérer les identités de périphérique][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>Modèles de gestion de périphérique IoT concentrateur

IoT Hub permet à l’ensemble des modèles de gestion de périphérique suivantes.  Les [didacticiels de gestion de périphérique] [ lnk-get-started] vous montrer plus en détail comment étendre ces modèles pour l’adapter à votre scénario exact et concevoir de nouveaux modèles basés sur ces modèles de base.

- **Redémarrage** - l’application back-end informe le périphérique via une méthode directe qu’il a initié un redémarrage.  Le périphérique utilise le périphérique double a signalé les propriétés mettre à jour l’état de redémarrage du périphérique.

    ![Gestion des périphériques IoT concentrateur Azure redémarrer graphique de modèle][img-reboot_pattern]

- **Factory Reset** - l’application back-end informe le périphérique via une méthode directe qu’il a initié une réinitialisation de l’usine.  Le périphérique utilise la double DISPOSITIF propriétés signalées pour mettre à jour de la fabrique de réinitialiser l’état du périphérique.

    ![Usine de gestion de périphérique IoT concentrateur Azure réinitialiser le graphique de modèle][img-facreset_pattern]

- **Configuration** - l’application back-end utilise les propriétés du périphérique double souhaité pour configurer le logiciel en cours d’exécution sur le périphérique.  Le périphérique utilise le périphérique double a signalé des propriétés à mettre à jour le statut de la configuration du périphérique.

    ![Graphique de modèle d’Azure IoT concentrateur périphérique gestion configuration][img-config_pattern]

- **Mise à jour du microprogramme** - l’application back-end informe le périphérique via une méthode directe qu’elle a lancé une mise à jour du firmware.  Le périphérique établit un processus en plusieurs étapes pour télécharger le micrologiciel, appliquez le package de micrologiciel et finalement se reconnecter au service IoT concentrateur.  Tout au long du processus mult-étape, le périphérique utilise le périphérique double a signalé des propriétés à mettre à jour la progression et l’état du périphérique.

    ![Mise à jour de microprogramme de gestion de périphérique IoT concentrateur Azure graphique de modèle][img-fwupdate_pattern]

- **Signaler la progression et l’état** - application back-end, requêtes de double de périphérique, s’exécute sur un ensemble de périphériques, un rapport sur l’état et la progression des actions s’exécutant sur les périphériques.

    ![Gestion des périphériques IoT concentrateur Azure reporting graphique de modèle de progression et l’état][img-report_progress_pattern]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez utiliser les fonctions, les motifs et les bibliothèques de code assurant la gestion des périphériques Azure IoT concentrateur, pour créer des applications IoT qui remplissent les exigences d’opérateur IoT entreprise dans chaque étape de leur cycle de vie du périphérique.

Pour en savoir sur les fonctionnalités de gestion de périphérique Azure IoT concentrateur, reportez-vous à la [mise en route de la gestion des périphériques Azure IoT concentrateur] [ lnk-get-started] didacticiel.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md