<properties 
   pageTitle="Guident de prise en charge et la stratégie de la retraite pour le système d’exploitation invité de Azure | Microsoft Azure" 
   description="Fournit des informations sur ce que Microsoft prend en charge en ce qui concerne pour le système d’exploitation invité de Azure utilisé par les Services en nuage." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="raiye" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="10/24/2016"
   ms.author="raiye"/>

# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Stratégie de prise en charge et de mise hors service du système d’exploitation invité Azure
Les informations de cette page est liée au système d’exploitation de Azure invité ([Système d’exploitation invité](cloud-services-guestos-update-matrix.md)) pour travail de Services en nuage et les rôles de web (PaaS). Il ne s’applique pas (IaaS) des machines virtuelles. 

Microsoft a une publication [prend en charge la stratégie pour le système d’exploitation invité](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). La page que vous lisez maintenant décrit la façon dont la stratégie est implémentée.

La stratégie est 

1. Microsoft prend en charge **au moins les deux dernières gammes de système d’exploitation invité**. Lorsqu’une famille est déclassée, les clients ont douze mois à compter de la date de retraite officiel pour mettre à jour à une famille de système d’exploitation invité plus récente pris en charge.
2. Microsoft prend en charge le **au moins les deux dernières versions de la famille du système d’exploitation invités pris en charge**. 
3. Microsoft prend en charge au **moins les deux versions plus récentes du Kit de développement Azure**. Lorsqu’une version du Kit de développement est déclassée, les clients auront des 12 mois à compter de la date de retraite officielle à jour vers une version plus récente. 

Parfois plus de deux familles ou versions peuvent être pris en charge. Informations de prise en charge de système d’exploitation invité officielles apparaîtra sur les [versions de système d’exploitation invité Azure et matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).


## <a name="when-a-guest-os-family-or-version-is-retired"></a>Lorsqu’une version ou une famille de système d’exploitation invité est déclassée 


Une nouvelle **famille** de de système d’exploitation invité est introduit un certain temps après le lancement d’une nouvelle version officielle du système d’exploitation Windows Server. Chaque fois qu’une nouvelle famille de système d’exploitation invité est introduite, Microsoft retirera la plus ancienne famille de système d’exploitation invité. 

Nouvelles **versions** de système d’exploitation invité sont introduits sur chaque mois pour incorporer les dernières mises à jour MSRC. Fait les mises à jour mensuelles régulières, une version de système d’exploitation invité est normalement désactivés 60 jours après sa publication. Cela permet de conserver au moins deux versions de système d’exploitation invité pour chaque famille disponible pour utilisation. 

### <a name="process-during-a-guest-os-family-retirement"></a>Processus au cours d’une retraite de famille du système d’exploitation invité 


Une fois l’annonce de la retraite, les clients ont une période de 12 mois « transition » avant la famille la plus ancienne est officiellement retirée du service. Ce temps de transition peut être étendu à la discrétion de Microsoft. Mises à jour sont validées sur les [versions de système d’exploitation invité Azure et matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).

Un processus de retraite progressive commencera 6 mois dans la période de transition. Pendant ce temps :

1. Microsoft avertira les clients de la retraite. 
2. La version la plus récente du Kit de développement Azure ne prend en charge la famille du système d’exploitation invité mis hors service.
3. Déploiement et redéploiement des Services en nuage pas autorisée sur la famille déclassée

Microsoft va continuer à introduire la nouvelle version du système d’exploitation invité incorporant les dernières mises à jour MSRC jusqu’au dernier jour de la période de transition, appelé « expiration date ». À ce stade, les Services en nuage tout en cours d’exécution est pris en charge sous le contrat SLA d’Azure. Microsoft a la discrétion pour forcer la mise à niveau, de supprimer ou d’arrêter ces services après cette date.



### <a name="process-during-a-guest-os-version-retirement"></a>Processus au cours d’une retraite de la Version du système d’exploitation invité 
Si leur système d’exploitation invité à mettre à jour automatiquement la valeur clients, ne sont jamais à vous préoccuper de gérer les versions de système d’exploitation invité. Il utilise toujours la dernière version du système d’exploitation invité.

Versions de système d’exploitation invité sont publiées chaque mois. En raison de la vitesse des versions régulières, chaque version possède une durée de vie fixe.

Au bout de 60 jours dans la durée de vie, une version est «*désactivé*». « Désactivé » signifie que la version est supprimée à partir du portail classique Azure. Il également n’est plus possible de définir à partir du fichier de configuration CSCFG. Déploiements existants sont laissés en cours d’exécution, mais les nouveaux déploiements et mises à jour de code et de configuration à des déploiements existants ne seront pas autorisés. 

À une date ultérieure, le système d’exploitation invité version «*expire*» et de toutes les installations que la version en cours d’exécution sont mis à niveau de force et ensemble pour mettre automatiquement à jour le système d’exploitation invité dans le futur. Expiration s’effectue dans des lots pour la période de temps à partir de la désactivation d’expiration peut varier. 

Ces périodes peuvent être effectués plus de temps à la discrétion de Microsoft pour faciliter les transitions de client. Les modifications seront communiquées sur les [versions de système d’exploitation invité Azure et matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).



### <a name="notifications-during-retirement"></a>Notifications pendant votre retraite 

* **Famille retraite** <br>Microsoft utilisera les billets de blog et Azure notification de portail classique. Les clients qui utilisent encore une famille de système d’exploitation invité déclassée seront avertis par une communication directe (e-mail, les messages de portail, appel téléphonique) pour les administrateurs de service affecté. Toutes les modifications sont validées dans cette page et le flux RSS répertoriés au début de cette page. 


* **Déclassement de la version** <br>Toutes les modifications sont validées dans cette page et le flux RSS répertoriés au début de cette page, y compris la version, désactivé et les dates d’expiration. Les administrateurs de services recevront des e-mails s’ils ont des déploiements en cours d’exécution sur une version de système d’exploitation invité désactivée ou de la famille. Le minutage de ces e-mails peut varier. Général, ils ont au moins un mois avant la désactivation, bien que ce délai n’est pas un contrat SLA officiel. 


## <a name="frequently-asked-questions"></a>Forum aux questions

**Comment puis-je pour atténuer l’impact de la migration ?**

Vous devez utiliser la dernière famille de système d’exploitation invité pour la conception de vos Services en nuage. 

1. Commencez la planification de votre migration vers une nouvelle famille dès le début. 
2. Permet de paramétrer les déploiements test temporaire pour tester votre Service Cloud s’exécutant sur la nouvelle gamme. 
3. Définissez votre version du système d’exploitation invité sur **automatique** (osVersion = * dans le fichier [.cscfg](cloud-services-model-and-package.md#cscfg) ) pour la migration vers de nouvelles versions de système d’exploitation invité s’effectue automatiquement.

**Que se passe-t-il si mon application web requiert une intégration plus étroite avec le système d’exploitation ?**

Si l’architecture de votre application web requiert les dépendances plus profonds sur le système d’exploitation sous-jacent, plate-forme utiliser fonctions prises en charge, tels que des [tâches de démarrage](cloud-services-startup-tasks.md) ou d’autres mécanismes d’extensibilité qui peuvent exister dans le futur. Sinon, vous pouvez également utiliser les [ordinateurs virtuels Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infrastructure en tant que Service), dans lequel vous êtes responsable de la maintenance du système d’exploitation sous-jacent.
 
## <a name="next-steps"></a>Étapes suivantes
Passez en revue les dernières [mises à jour de système d’exploitation invité](cloud-services-guestos-update-matrix.md).
