<properties
   pageTitle="Créer des solutions intégrées avec SQL Data Warehouse | Microsoft Azure"
   description="Les outils et les partenaires grâce à des solutions qui s’intègrent avec SQL Data Warehouse. "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="leverage-other-services-with-sql-data-warehouse"></a>Tirer parti des autres services avec SQL Data Warehouse
En plus de ses fonctionnalités principales, SQL Data Warehouse permet aux utilisateurs de tirer parti des nombreux autres services dans Azure à ses côtés.  En particulier, nous avons actuellement pris des mesures pour intégrer en profondeur avec les éléments suivants :

+ Alimentation BI
+ Usine de données Azure
+ Apprentissage automatique Azure
+ Analytique de flux Azure

Nous nous efforçons de vous connecter avec d’autres services au sein de l’écosystème Azure.

##<a name="power-bi"></a>Alimentation BI
Intégration de BI d’alimentation vous permet de tirer parti de la puissance de calcul de l’entrepôt de données de SQL avec la création de rapports dynamiques et visualisation de puissance BI. Intégration de BI d’alimentation actuellement inclut :

+ **Connexion directe**: plus avancées de la connexion avec le menu déroulant des logique par rapport à l’entrepôt de données SQL.  Il offre une analyse plus rapide à grande échelle.
+ **Ouvrir dans BI d’alimentation**: le bouton « Ouvrir dans une alimentation BI » transmet les informations d’instance à alimentation BI, permettant une connexion plus transparente.

Reportez-vous à [intégrer avec alimentation BI](./sql-data-warehouse-integrate-power-bi.md) ou la [documentation de BI de puissance](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) pour plus d’informations.

##<a name="azure-data-factory"></a>Usine de données Azure
Azure Data Factory offre aux utilisateurs une plate-forme gérée pour créer des pipelines extrait-charge complexes.  Intégration du magasin de données SQL Azure Data Factory inclut les éléments suivants :

+ **Procédures stockées**: orchestrer l’exécution des procédures stockées sur SQL Data Warehouse.
+ **Copie**: utiliser le chargeur automatique de documents pour déplacer des données dans Data Warehouse de SQL.  Cette opération permet de mécanisme de mouvement de données standard du chargeur automatique de documents ou PolyBase en coulisses. 

Reportez-vous à [intégrer avec Azure Data Factory](./sql-data-warehouse-integrate-azure-data-factory.md) ou la [documentation de l’usine de données Azure](https://azure.microsoft.com/documentation/services/data-factory/) pour plus d’informations.

##<a name="azure-machine-learning"></a>Apprentissage automatique Azure
L’apprentissage Machine Azure est un service entièrement géré analytique qui permet aux utilisateurs de créer des modèles complexes en exploitant un grand ensemble d’outils de prévision.  SQL Data Warehouse est pris en charge comme à la fois une source et une destination pour ces modèles avec les fonctionnalités suivantes :

+ **Lecture des données :** Lecteur de modèles à l’échelle à l’aide de T-SQL par rapport à l’entrepôt de données SQL.
+ **Écrire les données :** Valider les modifications au magasin de données SQL à partir de n’importe quel modèle.

Reportez-vous à [intégrer avec formation de Machine Azure](./sql-data-warehouse-integrate-azure-machine-learning.md) ou la [documentation de formation de Machine Azure](https://azure.microsoft.com/services/machine-learning/) pour plus d’informations.

##<a name="azure-stream-analytics"></a>Analytique de flux Azure
Analytique de flux Azure est une infrastructure complexe, entièrement gérée pour le traitement et l’utilisation des données d’événement générées à partir du concentrateur d’événements Azure.  Intégration avec SQL Data Warehouse permet la diffusion de données pour être traitées et stockées avec les données relationnelles permettant une analyse plus approfondie et plus avancée efficacement.  

+ **Sortie de la tâche :** Envoyer la sortie à partir de projets de flux de données Analytique directement au magasin de données SQL.

Reportez-vous à [intégrer avec Azure flux Analytique](./sql-data-warehouse-integrate-azure-stream-analytics.md) ou la [documentation d’Azure flux Analytique](https://azure.microsoft.com/documentation/services/stream-analytics/) pour plus d’informations.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
