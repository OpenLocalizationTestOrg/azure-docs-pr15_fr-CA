<properties
   pageTitle="Service de guide de résilience | Microsoft Azure"
   description="Liens pour la reprise après sinistre et Guide de résilience et de disponibilité proactive pour les services Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

# <a name="microsoft-azure-service-resiliency-guidance"></a>Aide à la résilience des services Microsoft Azure
Microsoft Azure est conçu pour vous fournir les ressources que nécessaires, lorsque vous en avez besoin. Dans le cadre d’une bonne conception et pratiques d’exploitation, vous devez connaître à la fois comment concevoir votre utilisation des services Azure pour atteindre une haute disponibilité, ainsi que faire si votre application est touchée par une interruption de service. Pour vous aider dans ce processus, ce document contient des liens vers des instructions de récupération d’urgence, ainsi que des conseils de conception pour divers services Azure.

##<a name="disaster-recovery-guidance"></a>Guide de récupération après sinistre
Le Guide de récupération après sinistre sont des liens ci-dessous peut vous fournir les informations nécessaires pour vous aider à rapidement votre application en ligne qui vous sont affectées par une interruption de service Azure. Ces liens ont été créés pour vous aider à répondre à la question, « Je suis en cours concerné par une interruption de service Azure, que puis-je faire ? »

##<a name="design-guidance"></a>Conseils de conception
Les liens du Guide de conception ci-dessous sont conception et guide architectural qui a été créé pour vous aider à comprendre la meilleure façon d’utiliser chaque service Azure d’une manière qui optimise le temps de fonctionnement de votre application. Ces liens ont été créés pour vous aider à répondre à la question « Comment m’assurer qu’un bogue, panne matérielle, interruption de service ou toute autre défaillance n’affecte pas la disponibilité globale de mon application ? » S’il n’existe pas d’instructions spécifiques pour le service que vous cherchez actuellement, l’article de la [haute disponibilité des applications basées sur Microsoft Azure](./resiliency-high-availability-azure-applications.md) peut avoir des informations supplémentaires qui peuvent vous aider dans votre conception. 

##<a name="service-guidance"></a>Guide de service
| Service  | Guide de récupération après sinistre | Conseils de conception |
|:---------|:--------------------------:|:------------------:|
| [Services en nuage] (https://azure.microsoft.com/services/cloud-services/ "Services en nuage Azure")       | [lien] (../cloud-services/cloud-services-disaster-recovery-guidance.md "Guide de récupération après sinistre Azure Cloud Services")   | N’est pas disponible |
| [Chambre forte de clé] (https://azure.microsoft.com/services/key-vault/ "Chambre forte de clé Azure")                      | [lien] (../key-vault/key-vault-disaster-recovery-guidance.md "Guide de récupération après sinistre Azure clé coffre-fort")        | N’est pas disponible |
| [Stockage] (https://azure.microsoft.com/services/storage/ "Stockage Azure")                            | [lien] (../storage/storage-disaster-recovery-guidance.md "Guide de récupération après sinistre le stockage Azure")          | N’est pas disponible |
| [Bases de données SQL] (https://azure.microsoft.com/services/sql-database/ "Bases de données SQL Azure")           | [lien] (../sql-database/sql-database-disaster-recovery.md  "Guide de récupération après sinistre de base de données de SQL Azure")    | [lien] (../sql-database/sql-database-business-continuity.md "Vue d’ensemble de la continuité d’activité avec une base de données de SQL Azure") |
| [Ordinateurs virtuels] (https://azure.microsoft.com/services/virtual-machines/ "Machines virtuelles Azure") | [lien] (../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Instructions de récupération après sinistre d’ordinateurs virtuels Azure") | N’est pas disponible |
| [Réseau virtuel] (https://azure.microsoft.com/services/virtual-network/ "Azure réseau virtuel")    | [lien] (../virtual-network/virtual-network-disaster-recovery-guidance.md "Guide de récupération après sinistre réseau virtuel d’Azure")  | N’est pas disponible |

##<a name="next-steps"></a>Étapes suivantes
Si vous cherchez des conseils qui se concentre plus largement sur les systèmes et les solutions, veuillez lire la [reprise après sinistre et une haute disponibilité des applications basées sur Microsoft Azure](https://aka.ms/drtechguide).
