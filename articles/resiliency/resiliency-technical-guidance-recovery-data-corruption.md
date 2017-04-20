<properties
   pageTitle="Guide technique de résilience de récupération à partir d’une suppression accidentelle ou de corruption des données | Microsoft Azure"
   description="L’article sur la compréhension des méthodes permettant de récupérer des données corrompues, de données ou suppression accidentelle de données sur les et conception d’applications à tolérance de pannes de réactifs, hautement disponibles, ainsi que la planification de reprise après sinistre"
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

#<a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Guide technique de résilience Azure : restauration à partir d’une suppression accidentelle ou de corruption des données

Partie d’un plan de continuité d’activité robuste a un plan si vos données obtient endommagées ou supprimées accidentellement. Voici les informations sur la récupération après que des données a été endommagées ou supprimées accidentellement, en raison d’erreurs d’application ou d’erreur de l’opérateur.

##<a name="virtual-machines"></a>Ordinateurs virtuels

Pour protéger vos ordinateurs virtuels de Azure (parfois appelée infrastructure-as-a-service VM) à partir des erreurs d’application ou de suppression accidentelle, utilisez [Azure sauvegarde](https://azure.microsoft.com/services/backup/). Sauvegarde Azure permet la création de sauvegardes cohérentes sur plusieurs disques de l’ordinateur virtuel. En outre, le coffre-fort de sauvegarde peuvent être répliqué sur plusieurs régions pour assurer la récupération de perte de la région.

##<a name="storage"></a>Stockage

Notez que pendant que le stockage Azure fournit la résilience des données par le biais de réplicas automatisées, cela n’empêche pas votre code d’application (ou les développeurs/utilisateurs) d’endommager des données par le biais de suppression accidentelle ou non intentionnelles, mise à jour et ainsi de suite. Maintenir la fidélité des données face à des erreurs d’application ou l’utilisateur nécessite des techniques plus avancées, telles que la copie des données vers un emplacement de stockage secondaire avec un journal d’audit. Les développeurs peuvent tirer parti de la blob [des capacités de capture instantanée](https://msdn.microsoft.com/library/azure/ee691971.aspx), qui peut créer des instantanés de point-à-temps en lecture seule du contenu de l’objet blob. Cela peut servir dans le cadre d’une solution de données haute fidélité pour les objets BLOB de stockage Azure.

###<a name="blob-and-table-storage-backup"></a>BLOB et sauvegarde de stockage de Table

Alors que les BLOB et les tables sont extrêmement durables, ils représentent toujours l’état actuel des données. Récupération de modifications indésirables ou la suppression de données peut-être nécessiter la restauration des données à un état antérieur. Ceci est possible en tirant parti des fonctionnalités fournies par Azure pour stocker et conserver des copies de point-à-temps.

Pour les objets BLOB Azure, vous pouvez effectuer des sauvegardes de point-à-temps à l’aide de la [fonction de snapshot de blob](https://msdn.microsoft.com/library/ee691971.aspx). Pour chaque capture instantanée, vous ne paient le stockage requis pour stocker les différences dans le blob depuis le dernier instantané d’état. Les captures instantanées sont dépendantes de l’existence du blob d’origine que dont ils dépendent, pour une opération de copie à un autre blob ou même un autre compte de stockage est recommandée. Cela garantit que les données de sauvegarde sont correctement protégées contre la suppression accidentelle. Pour les Tables Azure, vous pouvez créer des copies de point-à-temps pour une autre table ou les BLOB Azure. Plus des conseils et des exemples d’exécution de sauvegardes au niveau de l’application des tables et objets BLOB se trouve ici :

  * [Protection de vos Tables contre les erreurs d’Application](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
  * [Protection de vos objets BLOB contre les erreurs d’Application](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

##<a name="database"></a>Base de données

Il existe plusieurs options de [continuité d’activité](../sql-database/sql-database-business-continuity.md) (sauvegarde, restauration) de la base de données de SQL Azure. Bases de données peuvent être copiées à l’aide de la fonctionnalité de [Copie de la base de données](../sql-database/sql-database-copy.md) , ou en [exportant](../sql-database/sql-database-export.md) et en [important](https://msdn.microsoft.com/library/hh710052.aspx) un fichier de type sac à dos de SQL Server. Copie de la base de données fournit des résultats cohérentes, n’est pas le cas d’un type sac à DOS (par l’intermédiaire du service d’importation/exportation). Ces deux options exécutent en tant que file d’attente des services du centre de données, et elles ne fournissent pas actuellement un SLA de délais d’exécution.

>[AZURE.NOTE]Les options d’importation/exportation et de copie de base de données placer un degré significatif de la charge sur la base de données source. Ils peuvent déclencher des conflits de ressources ou de la limitation des événements.

###<a name="sql-database-backup"></a>Sauvegarde de base de données SQL

Point-à-temps des sauvegardes de base de données SQL de Microsoft Azure sont obtenus par la [copie de votre base de données SQL d’Azure](../sql-database/sql-database-copy.md). Vous pouvez utiliser cette commande pour créer une copie cohérente de façon transactionnelle d’une base de données sur le même serveur de base de données logique ou à un autre serveur. Dans les deux cas, la copie de la base de données est entièrement fonctionnelle et complètement indépendante de la base de données source. Chaque copie que vous créez représente une option de récupération de point-à-temps. Vous pouvez récupérer l’état de la base de données entièrement en renommant la nouvelle base de données portant le nom de la base de données source. Vous pouvez également récupérer un sous-ensemble spécifique de données à partir de la nouvelle base de données en utilisant des requêtes Transact-SQL. Pour plus de détails à propos de la base de données SQL, consultez [la vue d’ensemble de la continuité d’activité avec une base de données de SQL Azure](../sql-database/sql-database-business-continuity.md).

###<a name="sql-server-on-virtual-machines-backup"></a>SQL Server sauvegarde des Machines virtuelles

Pour SQL Server d’utilisé avec l’infrastructure Azure comme un machines virtuelles de service (souvent appelés IaaS IaaS VMs), deux options sont disponibles : les sauvegardes traditionnelles et l’envoi de journaux. À l’aide de sauvegardes traditionnelles permet de restaurer à un point spécifique dans le temps, mais le processus de récupération est lent. La restauration des sauvegardes traditionnelles, commençant par une sauvegarde initiale complète et en appliquant les sauvegardes effectuées après que. La deuxième option consiste à configurer une session pour retarder la restauration des sauvegardes de journal (par exemple, en deux heures) d’envoi de journaux. Cela fournit une fenêtre pour récupérer les erreurs sur le serveur principal.

##<a name="other-azure-platform-services"></a>Autres services de la plateforme Azure

Certains services de plateforme Azure stockent des informations dans un compte de stockage de contrôlées par l’utilisateur ou d’une base de données de SQL Azure. Si la ressource de stockage ou le compte est supprimée ou endommagée, cela peut provoquer des erreurs graves avec le service. Dans ces cas, il est important de conserver des sauvegardes vous permet de créer de nouveau ces ressources si elles ont été supprimées ou endommagées.

Pour les Sites Web Azure et d’Azure Mobile Services, vous devez sauvegarder et mettre à jour les bases de données associées. Pour Azure Media Service et les Machines virtuelles, vous devez gérer le compte de stockage Azure associé et toutes les ressources dans ce compte. Par exemple, pour les Machines virtuelles, vous devez sauvegarder et gérer les disques de l’ordinateur virtuel dans le stockage blob Azure.

##<a name="checklists-for-data-corruption-or-accidental-deletion"></a>Listes de vérification pour une suppression accidentelle ou de corruption des données

##<a name="virtual-machines-checklist"></a>Liste de contrôle de Machines virtuelles

  1. Consultez la section ordinateurs virtuels de ce document.
  2. Sauvegarde et maintenance des disques VM avec sauvegarde Azure (ou votre propre système de sauvegarde en utilisant le stockage blob Azure et les instantanés de disque dur virtuel).

##<a name="storage-checklist"></a>Liste de contrôle de stockage

  1. Consultez la section de stockage de ce document.
  2. Sauvegardez régulièrement les ressources de stockage critiques.
  3. Envisagez d’utiliser la fonction de snapshot pour les objets BLOB.

##<a name="database-checklist"></a>Liste de vérification de base de données

  1. Consultez la section de la base de données de ce document.
  2. Permet de créer des sauvegardes de point-à-temps à l’aide de la commande de copie de la base de données.

##<a name="sql-server-on-virtual-machines-backup-checklist"></a>SQL Server dans la liste de contrôle de sauvegarde des Machines virtuelles

  1. Passez en revue le SQL Server dans la section sauvegarde d’ordinateurs virtuels de ce document.
  2. Utilisez la sauvegarde traditionnelle et de restauration des techniques.
  3. Créer une session d’envoi de journaux différée.

##<a name="web-apps-checklist"></a>Liste des applications Web

  1. Sauvegarder et mettre à jour la base de données associé, le cas échéant.

##<a name="media-services-checklist"></a>Liste de contrôle des Services de support

  1. Sauvegarder et gérer les ressources de stockage associé.

##<a name="more-information"></a>Plus d’informations

Pour plus d’informations sur les fonctionnalités de sauvegarde et de restauration dans Azure, consultez [stockage, les scénarios de sauvegarde et de restauration](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de [conseils techniques de résilience Azure](./resiliency-technical-guidance.md). Si vous recherchez plus de souplesse, de reprise après sinistre et de ressources de haute disponibilité, consultez les conseils techniques de résilience Azure [des ressources supplémentaires](./resiliency-technical-guidance.md#additional-resources).