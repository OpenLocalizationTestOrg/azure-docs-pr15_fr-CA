<properties
   pageTitle="Migration : Utilitaire de Migration de l’entrepôt de données | Microsoft Azure"
   description="Migrer vers le magasin de données SQL."
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
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="data-warehouse-migration-utility-preview"></a>Utilitaire de Migration de magasin de données (aperçu)

> [AZURE.SELECTOR]
- [Télécharger l’utilitaire de Migration][]

L’utilitaire de Migration de données Warehouse est un outil conçu pour migrer le schéma et les données à partir de SQL Server et la base de données de SQL Azure à l’entrepôt de données SQL Azure. Lors de la migration du schéma, l’outil mappe automatiquement le schéma correspondant à partir de la source vers la destination. Une fois que le schéma a été migré, les outils offre la possibilité de déplacer des données à l’aide des scripts générés automatiquement.

En plus de la migration du schéma et des données, cet outil vous permet de générer des rapports de compatibilité qui résument les incompatibilités entre les instances source et cible qui empêcheraient la migration rationalisée.

## <a name="get-started"></a>Mise en route
Comme condition préalable pour l’installation, vous aurez besoin de l’utilitaire de ligne de commande BCP pour exécuter les scripts de migration et Office pour afficher le rapport de compatibilité. Après le lancement de l’exécutable qui est téléchargé un message vous demandera d’accepter le CLUF standard avant l’installation de l’outil.

En outre, pour exécuter la Migration Utiliy, vous devez le suivant les autorisations sur la base de données que vous cherchez à migrer : CREATE DATABASE, ALTER ANY DATABASE ou VIEW ANY DEFINITION.

### <a name="launching-the-tool-and-connecting"></a>Lancement de l’outil et la connexion
Lancez l’outil en cliquant sur l’icône du bureau qui s’affiche après installation. Lors de l’ouverture de l’outil, vous serez invité à une page de connexion initiale dans laquelle vous pouvez choisir votre source et destination pour l’outil de migration. À ce stade, nous prenons en charge de SQL Server et la base de données de SQL Azure en tant que sources et SQL Data Warehouse comme destination. Après la sélection de cette option, vous devez vous connecter à votre serveur source en renseignant dans nom du serveur et authentification et puis en cliquant sur 'Se connecter'.

Après l’authentification, l’outil affiche une liste des bases de données qui sont présents sur le serveur sur lequel vous êtes connecté à. Vous pouvez commencer la migration en sélectionnant une base de données que vous souhaitez migrer et puis en cliquant sur « Migrer sélectionné ».

## <a name="migration-report"></a>Rapport de migration
Sélectionner « Vérifier la compatibilité de base de données » dans l’outil génère un rapport résumant toutes les incompatibilités d’objet dans la base de données que vous souhaitez pour migrer. Vous trouverez une liste plus large de certaines fonctionnalités de SQL Server qui ne sont pas présente dans le magasin de données SQL dans notre [documentation de migration][]. Une fois que le rapport est généré, vous serez en mesure d’enregistrer et d’ouvrir le rapport dans Excel.

Notez que lors de la génération du schéma de migration, la plupart des problèmes identifiés en tant que 'Object' sera ajusté afin de permettre une migration immédiate de ces données. Vérifiez les modifications afin de que vous ne souhaitez pas apporter des modifications supplémentaires avant d’appliquer le schéma.

## <a name="migrate-schema"></a>Schéma de migration

Après la connexion, sélectionner « Migrer Schema » génère un script de migration du schéma pour les tables sélectionnées. Les ports de ce script la structure de la table, mappe les données incompatibles types de formulaires plus compatibles et crée le schéma et les informations d’identification de sécurité si cela est indiqué par l’utilisateur dans les paramètres de migration. Ce code peut être exécuté sur l’instance SQL Data Warehouse ciblée, enregistrées dans un fichier, copié dans le Presse-papiers ou même modifié en ligne avant d’entreprendre une autre action.  

Comme mentionné ci-dessus, lors de la migration schéma la migration les modifications que l’outil a effectué afin d’assurer que vous comprenez les.  

## <a name="migrate-data"></a>Migration des données

En cliquant sur l’option migrer les données, vous pouvez générer des scripts BCP qui va déplacer vos données tout d’abord des fichiers plats sur votre serveur, puis directement dans votre magasin de données SQL. Nous vous recommandons ce processus pour petites quantités de données et, en tant que tentatives de déplacement ne sont pas intégrées risquent d’échouer si il y a une perte de la connexion réseau. Pour exécuter cet exemple, vous devez disposer de l’utilitaire de ligne de commande BCP installé et que le schéma de données doit déjà avoir été créé.

Après avoir rempli les paramètres ci-dessus, il vous suffit de cliquez sur exécution de migration et un ensemble de deux packages sera généré à l’emplacement spécifié. Exécutez le fichier d’exportation pour exporter les données de la source de migration dans des fichiers plats et exécutez le fichier d’importation pour importer vos données dans l’entrepôt de données SQL.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez effectué une migration des données, découvrez comment [développer][].

<!--Image references-->

<!--Article references-->
[documentation de la migration]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Télécharger l’utilitaire de Migration]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip
