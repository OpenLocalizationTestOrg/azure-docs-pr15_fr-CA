<properties
 pageTitle="Concentrateur de IoT HA et DR | Microsoft Azure"
 description="Décrit les fonctions qui contribuent à créer des solutions IoT haute disponibilitées avec reprise après sinistre des capacités de récupération."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Concentrateur de IoT haute disponibilité et reprise après sinistre

Comme un service Azure, IoT concentrateur fournit la haute disponibilité (HA) à l’aide de redondances au niveau région Azure, sans aucun travail supplémentaire requis par la solution. En outre, Azure offre un certain nombre de fonctionnalités qui vous permettent de créer des solutions avec reprise (DR) ou de la disponibilité de cross-région si nécessaire. Vous devez concevoir et préparer vos solutions pour tirer parti de ces fonctionnalités de reprise après sinistre si vous souhaitez fournir global, le cross-région de haute disponibilité pour les périphériques ou les utilisateurs. L’article [Des conseils techniques Azure Business Continuity](../resiliency/resiliency-technical-guidance.md) décrit les fonctionnalités intégrées dans Azure pour la continuité d’activité et de reprise après sinistre. Le papier de [reprise après sinistre et une haute disponibilité pour les applications Azure][] fournit des conseils d’architecture sur les stratégies pour applications Azure atteindre une haute disponibilité et reprise après sinistre.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
En plus de la région intra HA, IoT concentrateur implémente des mécanismes de basculement pour une reprise après sinistre qui ne requièrent aucune intervention de l’utilisateur. IoT Hub DR est lancée automatiquement et a un objectif de temps de récupération (RTO) de 2-26 heures et les objectifs de point de récupération (RPO) suivants.

| Fonctionnalités | RPO |
| ------------- | --- |
| Disponibilité du service pour les opérations du Registre et de communication | Perte CName |
| Données d’identité dans le Registre d’identité de périphérique | 0-5 min perte de données |
| Messages de périphérique-nuage | Tous les messages non lus sont perdus |
| Messages de contrôle des opérations | Tous les messages non lus sont perdus |
| Messages du nuage vers le périphérique | 0-5 min perte de données |
| File d’attente de feedback de nuage-appareil | Tous les messages non lus sont perdus |

## <a name="regional-failover-with-iot-hub"></a>Basculement régionaux avec IoT concentrateur

Un traitement complet des topologies de déploiement dans les solutions IoT est en dehors de la portée de cet article, mais pour la haute disponibilité et reprise après sinistre que nous allons étudier le modèle de déploiement de *basculement régionaux* .

Dans un modèle de basculement régionaux, le back-end de solution s’exécute principalement dans un seul emplacement de centre de données, mais un concentrateur de IoT supplémentaire et d’un back-end sont déployés dans un autre emplacement de centre de données à des fins de basculement sur incident, au cas où le concentrateur IoT dans le centre de données principal subit une panne ou à la connectivité réseau à partir du périphérique au centre de données principal est interrompue d’une certaine manière. Les périphériques utilisent un point de terminaison de service secondaire dès que la passerelle principale ne peut pas être atteint. Avec une capacité de basculement de cross-région, la disponibilité de la solution peut être améliorée au-delà de la haute disponibilité d’une seule région.

À un haut niveau, pour mettre en œuvre un modèle de basculement régionaux avec IoT concentrateur, vous devrez les éléments suivants.

* **Un concentrateur de IoT et un périphérique logique de routage secondaire**: en cas de rupture de service dans votre région principale, périphériques doivent commencer à se connecter à votre zone secondaire. Étant donné la nature orientée sur l’état de la plupart des services concernés, il est courant pour une solution aux administrateurs de déclencher le processus de basculement de la région. La meilleure façon de communiquer le nouveau point de terminaison pour les périphériques, tout en conservant le contrôle du processus, est qu’elle vérifie régulièrement un service de *concierge* pour le point de terminaison active en cours. Le service de concierge peut être une application web simple qui est répliquée et conservée accessible à l’aide de techniques de la redirection DNS (par exemple, à l’aide du [Gestionnaire de trafic Azure][]).
* **Réplication du Registre identité** - pour être utilisable, la secondaire IoT concentrateur doit contenir toutes les identités des appareils qui peuvent se connecter à la solution. La solution doit conserver géo-répliquées des sauvegardes d’identités de périphérique et les télécharger vers le concentrateur IoT secondaire avant de passer le point de terminaison active pour les périphériques. La fonctionnalité d’exportation identité périphérique du concentrateur de IoT est très utile dans ce contexte. Pour plus d’informations, consultez le [Guide de développeur de concentrateur IoT - Registre de l’identité][].
* **Fusion logique** - lorsque la région primaire redevient disponible, tout l’état et les données qui ont été créées dans le site secondaire doivent être migrées en différé à la région principale. Cela concerne principalement identités des appareils et des meta-données d’application, qui doivent être fusionnées avec le concentrateur IoT primaire et tous les autres magasins spécifiques à l’application dans la région principale. Pour simplifier cette étape, il est généralement recommandé d’utiliser les opérations équipotentes. Cela permet de réduire des effets secondaires non seulement à partir d’une éventuelle distribution cohérente des événements, mais également de doublons ou de livraison d’ordre d’événements. En outre, la logique d’application doit être conçue à tolérer les incohérences ou potentiels « légèrement »-l’état de la date de. Cela en raison du temps supplémentaire que nécessaire pour le système « de correction » repose sur les objectifs de point de récupération (RPO).

## <a name="next-steps"></a>Étapes suivantes

Cliquez sur ces liens pour en savoir plus sur Azure IoT Hub :

- [Mise en route de concentrateurs de IoT (didacticiel)][lnk-get-started]
- [Quel est le concentrateur de IoT Azure ?][]

[Reprise après sinistre et une haute disponibilité pour les applications Azure]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Gestionnaire de trafic Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Guide du développeur IoT Hub - Registre de l’identité]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Quel est le concentrateur de IoT Azure ?]: iot-hub-what-is-iot-hub.md
