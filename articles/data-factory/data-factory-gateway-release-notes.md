<properties 
    pageTitle="Notes de publication pour la passerelle de gestion des données | Usine de données Azure" 
    description="Notes de mise à jour de données Management Gateway tory" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="spelluru"/>

# <a name="release-notes-for-data-management-gateway"></a>Notes de version de la passerelle de gestion des données
Un des défis de l’intégration de données modernes est de déplacer en toute transparence des données vers et à partir du site vers le cloud. Usine de données rend cette intégration transparente avec la passerelle de gestion des données, qui est un agent que vous pouvez installer sur site pour permettre le déplacement de données hybride.

Consultez les articles suivants pour plus d’informations sur la passerelle de gestion des données et comment l’utiliser : 

- [Passerelle de gestion des données](data-factory-data-management-gateway.md)
- [Déplacer les données entre les locaux et cloud à l’aide de la fabrique de données Azure](data-factory-move-data-between-onprem-and-cloud.md) 

## <a name="current-version-2260721"></a>Version actuelle (2.2.6072.1)

- Prend en charge la configuration de proxy HTTP pour la passerelle à l’aide du Gestionnaire de Configuration de passerelle. Si configuré, Azure Blob, Table d’Azure, lac de données Azure et Document de base de données sont accessibles via un serveur proxy HTTP.
- En-tête prend en charge gestion TextFormat lors de la copie des données depuis/vers le Blob Azure, magasin de LAC de données Azure, système de fichiers local et très sur site.
- Prend en charge la copie de données d’ajouter le Blob et le Blob de Page avec l’objet Blob de bloc déjà pris en charge.
- Introduit un nouveau statut de passerelle **en ligne (limité)**, qui indique que les principales fonctionnalités de la passerelle fonctionnent, à l’exception de la prise en charge de l’opération interactive pour l’Assistant de copie.
- Améliore la robustesse de l’enregistrement de passerelle à l’aide de la clé d’enregistrement.

## <a name="earlier-versions"></a>Versions antérieures

## <a name="2160401"></a>2.1.6040.1

- Pilote DB2 est maintenant inclus dans le package d’installation de passerelle. Vous n’avez pas besoin de l’installer séparément. 
- Pilote DB2 prend désormais en charge z/OS et DB2 pour i (AS / 400) ainsi que les plates-formes déjà pris en charge (Windows, Unix et Linux). 
- Prend en charge à l’aide de DocumentDB en tant que source ou de destination pour les banques de données locales
- Prend en charge la copie des données de/à froid/chaud blob de stockage avec le compte déjà prises en charge de stockage à usage général. 
- Permet de vous connecter à SQL Server de locaux via une passerelle avec les privilèges de connexion d’accès distant.  

## <a name="2060131"></a>2.0.6013.1

- Vous pouvez sélectionner la langue/culture devant être utilisée par une passerelle au cours d’une installation manuelle.
- Lorsque la passerelle ne fonctionne pas comme prévu, vous pouvez choisir d’envoyer des journaux de passerelle de sept jours à Microsoft pour faciliter le dépannage du problème. Si la passerelle n’est pas connecté au service cloud, vous pouvez choisir d’enregistrer et d’archiver les journaux de la passerelle.  
- Améliorations de l’interface utilisateur du Gestionnaire de configuration de passerelle :
    - Faire état de la passerelle plus visible sous l’onglet Accueil.
    - Contrôles de réorganisation et simplifiés.
- Vous pouvez copier des données à partir d’un stockage à l’aide de l' [outil Aperçu de la copie sans code](data-factory-copy-data-wizard-tutorial.md). Voir [Copie d’intermédiaire](data-factory-copy-activity-performance.md#staged-copy) pour plus d’informations sur cette fonctionnalité en général. 
- Vous pouvez utiliser la passerelle de gestion des données pour les données d’entrée directement à partir d’une base de données de SQL Server sur site en formation de Machine Azure.
- Améliorations des performances
    - Améliorer les performances d’affichage Schéma/aperçu par rapport à SQL Server dans l’outil Aperçu de la copie sans code.



## <a name="11259531"></a>1.12.5953.1
- Correctifs de bogues

## <a name="11159181"></a>1.11.5918.1

- Taille maximale du journal des événements passerelle a été augmentée de 1 Mo à 40 Mo.
- Une boîte de dialogue d’avertissement s’affiche si un redémarrage est nécessaire lors de la mise à jour automatique de passerelle. Vous pouvez choisir de redémarrer à droite puis ou version ultérieure. 
- En cas d’échec de la mise à jour automatique, d’installation tente de réexécuter la mise à jour automatique trois fois au maximum.
- Améliorations des performances
    - Améliorer les performances lors du chargement des tables de grande taille à partir du serveur local dans le scénario de la copie sans code.
- Correctifs de bogues

## <a name="11058921"></a>1.10.5892.1

- Améliorations des performances
- Correctifs de bogues

## <a name="1958652"></a>1.9.5865.2

- Aucune capacité de mise à jour automatique tactile
- Nouvelle icône de barre d’état avec les indicateurs d’état de passerelle
- Possibilité de « Mise à jour maintenant » à partir du client
- Possibilité de définir l’heure de mise à jour de la planification
- Script PowerShell pour activer/désactiver la mise à jour automatique activé/désactivé
- Prise en charge pour le format JSON  
- Améliorations des performances
- Correctifs de bogues

## <a name="1858221"></a>1.8.5822.1

- Améliorer l’expérience de résolution des problèmes
- Améliorations des performances
- Correctifs de bogues

### <a name="1757951"></a>1.7.5795.1

- Améliorations des performances
- Correctifs de bogues

### <a name="1757641"></a>1.7.5764.1

- Améliorations des performances
- Correctifs de bogues

### <a name="1657351"></a>1.6.5735.1

- Support sur site très Source/récepteur
- Améliorations des performances
- Correctifs de bogues

### <a name="1656961"></a>1.6.5696.1

- Améliorations des performances
- Correctifs de bogues

### <a name="1656761"></a>1.6.5676.1

- Outils de diagnostic de support sur le Gestionnaire de Configuration
- Prise en charge des colonnes de la table de sources de données tabulaires pour Azure Data Factory
- Prise en charge du Data Warehouse de SQL Azure Data Factory
- Prise en charge Reclusive dans BlobSource et FichierGestion de fabrique de données Azure
- Prise en charge CopyBehavior, MergeFiles, PreserveHierarchy et FlattenHierarchy dans BlobSink et FileSink avec une copie binaire pour Azure Data Factory
- Prise en charge de la copie de rapport d’activité progression pour Azure Data Factory
- Prise en charge Source connectivité la Validation des données Azure Data Factory
- Correctifs de bogues


### <a name="1656721"></a>1.6.5672.1

- Prise en charge du nom de la table de source de données ODBC pour Azure Data Factory
- Améliorations des performances
- Correctifs de bogues

### <a name="1656581"></a>1.6.5658.1

- Fichier de prise en charge de récepteurs pour Azure Data Factory
- Prise en charge de la conservation de hiérarchie dans une copie binaire pour Azure Data Factory
- Prise en charge d’idempotence activité de copie de données Azure usine
- Correctifs de bogues

### <a name="1656401"></a>1.6.5640.1

- Prend en charge les 3 autres sources de données pour Azure Data Factory (ODBC, OData, très)
- Prise en charge de guillemet dans l’Analyseur de csv pour Azure Data Factory
- Prise en charge de la compression (BZip2)
- Correctifs de bogues

### <a name="1556121"></a>1.5.5612.1

- Prise en charge de cinq bases de données relationnelles pour Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata et Sybase)
- Prise en charge de la compression (Gzip et Deflate)
- Améliorations des performances
- Correctifs de bogues


### <a name="1455491"></a>1.4.5549.1

- Ajouter la prise en charge de source de données Oracle pour Azure Data Factory
- Améliorations des performances
- Correctifs de bogues

### <a name="1454921"></a>1.4.5492.1

- Binaire unifié qui prend en charge les services à la fois Microsoft Azure Data Factory et Office 365 alimentation BI
- Affiner le processus de l’interface de Configuration et d’enregistrement
- Usine de données Azure – la prise en charge de Azure entrant et sortant de source de données SQL Server

### <a name="1253031"></a>1.2.5303.1

-   Résoudre les problème de délai d’attente pour la prise en charge de plusieurs connexions de source de données. 
    
### <a name="1155268"></a>1.1.5526.8

- Nécessite.NET Framework 4.5.1 comme composant requis lors de l’installation.

### <a name="1051442"></a>1.0.5144.2

- Aucune modification qui affectent les scénarios d’usine de données Azure. 
