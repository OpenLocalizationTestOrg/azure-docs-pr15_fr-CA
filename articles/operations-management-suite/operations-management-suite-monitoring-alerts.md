<properties 
   pageTitle="Alertes de gestion, de surveillance des produits de Microsoft | Microsoft Azure"
   description="Une alerte indique un problème qui nécessite une attention de la part d’un administrateur.  Cet article décrit les différences de comment les alertes sont créés et gérés dans System Center Operations Manager (SCOM) et journal Analytique et fournit les meilleures pratiques en tirant parti de ces deux produits pour une stratégie de gestion des alertes hybride." 
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="managing-alerts-with-microsoft-monitoring"></a>Gestion des alertes de surveillance de Microsoft 

Une alerte indique un problème qui nécessite une attention de la part d’un administrateur.  Il existe des différences distinctes entre System Center Operations Manager (SCOM) et journal Analytique dans Operations Management Suite (OMS) en ce qui concerne la création des alertes, comment ils sont gérés et analysés, et comment vous en êtes averti qu’un problème critique a été détecté.

## <a name="alerts-in-operations-manager"></a>Alertes dans Operations Manager.
Alertes dans SCOM sont générées par des règles individuelles ou des moniteurs pour indiquer un problème spécifique.  Un moniteur peut générer une alerte lorsqu’il entre dans un état d’erreur pendant une règle peut générer une alerte pour indiquer un problème critique qui n’est pas directement lié à la santé d’un objet managé.  Packs d’administration incluent une variété de flux de travail créer des alertes pour l’application ou le service qu’ils gèrent.  Partie du processus de configuration d’un nouveau pack d’administration est de réglage pour vous assurer que vous ne recevez pas d’alertes excessif pour les problèmes que vous ne considérez pas comme critique.

![Affichage des alertes SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM offre une gestion complète des alertes alertes ayant un statut qui peut être modifié par les administrateurs lorsqu’ils travaillent pour résoudre le problème.  Lorsque le problème a été résolu, l’administrateur définit l’alerte de fermeture à quel moment il n’apparaîtra plus dans les modes d’affichage des alertes actives.  Les alertes qui sont générés à partir des écrans peuvent être résolus automatiquement lorsque le moniteur revient à un état sain.

![État d’alerte](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alertes dans le journal Analytique
Une alerte dans le journal Analytique est créée à partir d’une requête du journal qui s’exécute automatiquement à intervalles réguliers.  Vous pouvez créer une règle d’alerte à partir de n’importe quelle requête du journal.  Si la requête retourne des résultats qui correspondent aux critères que vous spécifiez, une alerte est créée.  Cela peut être une requête spécifique qui crée une alerte en cas de détection d’un événement particulier, ou vous pouvez utiliser une requête plus générale qui recherche des événements d’erreur relatifs à une application particulière.

Alertes d’Analytique de journal sont écrites dans le référentiel de l’OMS en tant qu’événement et peuvent être récupérées avec une requête de journal.  Ils n’ont pas d’état comme événements SCOM afin que vous pouvez indiquer lorsque le problème a été résolu.

![Alerte de l’OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Lorsque SCOM est utilisé comme source de données d’Analytique de journal, des alertes SCOM sont écrits dans le référentiel de l’OMS qu’ils sont créés et modifiés.  

![Alerte SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

La [solution de gestion des alertes](http://technet.microsoft.com/library/mt484092.aspx) fournit un résumé des alertes actives et plusieurs requêtes communes pour récupérer différents ensembles d’alertes.  Cela vous offre une analyse plus efficace de vos alertes à un rapport dans SCOM.  Vous pouvez défiler dans les résumés de données détaillées et créer des requêtes ad hoc pour récupérer des jeux différents d’alertes.

![Solution de gestion des alertes](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notifications
Notifications dans SCOM vous envoyer un mail ou du texte en réponse à des alertes qui correspondent à des critères particuliers.  Vous pouvez créer des abonnements de notification différents qui ont notifié en fonction de critères tels que l’objet en cours d’analyse, la gravité de l’alerte, le type de problème qui a détecté des personnes différentes, ou l’heure de la journée.

Abonnements de quelques peuvent être utilisés pour implémenter une stratégie de notification complète pour un grand nombre de packs d’administration.

![Alertes SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Analytique de journal peut vous informer par courrier qu’une alerte a été créée en définissant une action de notification de courrier électronique sur chaque [règle d’alerte](http://technet.microsoft.com/library/mt614775.aspx).  Il n’a pas la capacité de SCOM le s’abonner à des alertes multiples avec une seule règle.  Vous devez également créer vos propres règles d’alerte depuis l’OMS ne fournit aucune préconfiguré.

![Alertes d’Analytique de journal](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Vous ne peut pas gérer complètement alertes SCOM dans journal Analytique bien que dans la mesure où vous pouvez les modifier uniquement dans la Console.  Analytique de journal est utile en tant que partie d’une gestion des alertes traiter bien que pour fournir des outils d’analyse que SCOM seul ne possède pas.

## <a name="alert-remediation"></a>Alerte mise à jour
[Mise à jour](http://technet.microsoft.com/library/mt614775.aspx) fait référence à une tentative de corriger automatiquement le problème identifié par une alerte.
  
SCOM vous permet d’exécuter des Diagnostics et récupérations en réponse à un moniteur à un état défectueux.  Cela se produit simultanément à l’écran de création de l’alerte.  Diagnostics et récupérations sont généralement implémentées sous la forme d’un script qui s’exécute sur l’agent.  Un diagnostic tente de rassembler plus d’informations sur le problème détecté pendant une restauration pour tente de résoudre le problème.

Analytique de journal vous permet de démarrer une [procédure opérationnelle d’Azure Automation](https://azure.microsoft.com/documentation/services/automation/) ou appeler un webhook en réponse à une alerte de journal Analytique.  Procédures opérationnelles peuvent contenir une logique complexe implémentée dans PowerShell.  Le script s’exécute dans Azure et accéder aux ressources d’Azure et les ressources externes disponibles à partir du cloud.  Azure Automation n’a pas la possibilité d’exécuter des procédures opérationnelles sur un serveur dans votre centre de données local, mais cette fonctionnalité n’est pas actuellement disponible lors du démarrage de la procédure opérationnelle en réponse à des alertes de journal Analytique.

Les restaurations dans SCOM et les procédures opérationnelles de l’OMS peut contenir des scripts PowerShell, mais le restaurations sont plus difficiles à créer et à gérer, car ils doivent être contenus dans un pack d’administration.  Procédures opérationnelles sont stockés dans Automation Azure qui fournit des fonctionnalités pour la création, le test et la gestion de procédures opérationnelles.

Si vous utilisez SCOM comme source de données d’Analytique de journal, vous pouvez créer une alerte de journal Analytique à l’aide d’une requête de journaux pour récupérer les alertes SCOM stockés dans le référentiel de l’OMS.  Cela vous permettrait d’exécuter une procédure opérationnelle d’Azure Automation en réponse à une alerte SCOM.  Bien sûr, dans la mesure où la procédure opérationnelle s’exécute dans Azure, cela ne serait pas une stratégie viable pour les restaurations des problèmes de locaux.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les détails des [alertes dans System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).