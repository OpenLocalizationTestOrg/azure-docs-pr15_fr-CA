<properties
   pageTitle="L’audit SQL Azure Data Warehouse | Microsoft Azure"
   description="Mise en route de l’audit dans l’entrepôt de données SQL Azure"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="auditing-in-azure-sql-data-warehouse"></a>L’audit dans l’entrepôt de données SQL Azure

> [AZURE.SELECTOR]
- [L’audit](sql-data-warehouse-auditing-overview.md)
- [Détection de la menace](sql-data-warehouse-security-threat-detection.md)

L’audit de SQL Data Warehouse vous permet d’enregistrement du journal des événements dans votre base de données à un audit de votre compte de stockage Azure. L’audit peut vous aider à assurer la conformité réglementaire, comprendre l’activité de la base de données et mieux les différences et les anomalies qui peuvent indiquer des problèmes de l’entreprise ou suspectés de violations de la sécurité. L’audit de SQL Data Warehouse s’intègre également avec Microsoft BI de puissance de reporting et d’analyse descendante.

Outils d’audit activer et facilitent le respect des normes de conformité, mais ne garantissent pas la conformité. Pour plus d’informations sur les programmes Azure qui prennent en charge de la conformité aux normes, consultez le <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centre d’approbation Azure</a>.

+ [Principes fondamentaux de l’audit de base de données]
+ [Configurer l’audit pour votre base de données]
+ [Analyser les journaux d’audit et rapports]

##<a id="subheading-1"></a>Bases d’audit de base de données de magasin données SQL Azure


L’audit de base de données SQL Data Warehouse vous permet de :

- **Conserver** une trace d’audit d’événements sélectionnés. Vous pouvez définir des catégories d’actions de base de données à auditer.
- **Rapport** sur l’activité de la base de données. Vous pouvez utiliser un tableau de bord et de rapports préconfigurés pour vous familiariser rapidement avec les activités et les rapports d’événements.
- Rapports **d’analyse** . Vous pouvez trouver les événements suspects, toute activité inhabituelle et des tendances.

Vous pouvez configurer l’audit pour les catégories d’événements suivantes :

**SQL simple** et **Paramétrée de SQL** pour laquelle les collectées journaux d’audit sont classés en  

- **Accès aux données**
- **Modifications de schéma (DDL)**
- **Modifications de données (DML)**
- **Comptes, rôles et autorisations (DCL)**
- La **procédure stockée**, **nom de connexion** et de **Gestion des transactions**.

Pour chaque catégorie d’événements, audit des **succès** et des **échecs** sont configurés séparément.

Pour plus de détails sur les activités et les événements audités, consultez la <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Référence des formats de journal d’Audit (téléchargement de fichier doc)</a>.

Les journaux d’audit sont stockés dans votre compte de stockage Azure. Vous pouvez définir une période de rétention du journal d’audit.

Une stratégie d’audit peut être définie pour une base de données spécifique, ou sous la forme d’une stratégie de serveur par défaut. Une stratégie d’audit de serveur par défaut s’appliquent à toutes les bases sur un serveur qui ne disposent pas d’une substitution de base de données audit stratégie spécifique définie.

Avant de définir à distance l’audit à cocher audit si vous utilisez un [« Client de niveau inférieur »](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Configurer l’audit pour votre base de données

1. Lancer le <a href="https://portal.azure.com" target="_blank">portail Azure</a>.

2. Accédez à la lame de la configuration de la base de données SQL Data Warehouse / de SQL Server que vous souhaitez auditer. Cliquez sur le bouton **paramètres** en haut, puis, dans la lame de paramètre et sélectionnez **audit**.

    ![][1]

3. De la lame de configuration d’audit, désactivez d’abord la case à cocher **Hériter des paramètres d’audit de serveur** . Cela vous permet de spécifier les paramètres pour une base de données particulière.

    ![][2]

4. Ensuite, activez l’audit en cliquant **sur** le bouton.

    ![][3]

5. Dans la lame de configuration d’audit, sélectionnez **Détails sur le stockage** pour ouvrir la lame de stockage de journaux d’Audit. Sélectionnez le compte de stockage Azure où les journaux doivent être enregistrés et, la période de rétention. **Conseil :** Utiliser le même compte de stockage pour toutes les bases de données audités pour tirer le meilleur parti des modèles de le rapports préconfigurés.

    ![][4]

6. Cliquez sur le bouton **OK** pour enregistrer la configuration de détails de stockage.


7. Sous **Enregistrement par événement**, cliquez sur pour enregistrer tous les événements de **Réussite** et **Échec** , ou choisir les catégories d’événements.


8. Si vous configurez l’audit pour une base de données, vous devrez peut-être modifier la chaîne de connexion de votre client pour assurer l’audit des données est correctement capturée. Consultez la rubrique [Modifier les FQDN de serveur dans la chaîne de connexion](sql-data-warehouse-auditing-downlevel-clients.md) pour les connexions de client de niveau inférieur.

9. Cliquez sur **OK**.


##<a id="subheading-3">Analyser les journaux d’audit et rapports</a>

Les journaux d’audit sont regroupés dans une collection de Tables de la banque d’informations avec un préfixe **SQLDBAuditLogs** dans le compte de stockage Azure que vous avez choisi lors de l’installation. Vous pouvez afficher les fichiers journaux à l’aide d’un outil tel que <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorateur de stockage Azure</a>.

Un modèle de rapport de tableau de bord préconfigurés est disponible sous la forme d’une <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">feuille de calcul Excel téléchargeable</a> pour vous aider à analyser rapidement les données du journal. Pour utiliser le modèle de vos journaux d’audit, vous devez 2013 et ultérieures d’Excel et de requêtes d’alimentation, que vous pouvez télécharger <a href="http://www.microsoft.com/download/details.aspx?id=39379">ici</a>.

Le modèle comporte des données d’exemple fictif, et vous pouvez définir des requêtes d’alimentation pour importer vos journaux d’audit directement à partir de votre compte de stockage Azure.

Pour obtenir des instructions plus détaillées sur l’utilisation du modèle d’état, lisez <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Comment (téléchargement doc)</a>.

![][5]


##<a id="subheading-4">Méthodes conseillées pour l’utilisation dans la production</a>
La description dans cette section fait référence aux captures d’écran ci-dessus. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> ou <a href= "https://manage.windowsazure.com/" target="_bank">Portail de classique Azure classique</a> peut être utilisé.


##<a id="subheading-5"></a>Régénération de clé de stockage

Dans la production, vous êtes susceptible d’actualiser régulièrement les clés de votre stockage. Lorsque actualisez vos clés vous devez ré-enregistrer la stratégie. Le processus est le suivant :.


1. De la lame d’audit de configuration (décrite ci-dessus dans l’ensemble de l’audit de section) basculer la **Clé d’accès de stockage** *du* *secondaire* et **d’Enregistrer**.
![][4]
2. Atteindre la lame de configuration de stockage et **Régénérer** la *Clé d’accès primaire*.

3. Accédez à la lame de configuration d’audit, changer la **Clé d’accès de stockage** de *secondaire* *principal* , cliquez sur **Enregistrer**.

4. Revenir à l’interface utilisateur de stockage et de **Régénérer** la *Clé d’accès secondaire* (en vue de préparer le prochain cycle d’actualisation de clés.

##<a id="subheading-6"></a>Automation
Il existe plusieurs applets de commande PowerShell que vous pouvez utiliser pour configurer l’audit dans la base de données de SQL Azure. Pour accéder aux applets de commande audit vous devez exécuter PowerShell en mode de Gestionnaire des ressources Azure.

> [AZURE.NOTE] Le module [Gestionnaire de ressources Azure](https://msdn.microsoft.com/library/dn654592.aspx) est en cours d’aperçu. Il peut ne pas fournit les mêmes fonctionnalités de gestion que le module Azure.

Lorsque vous êtes en mode de Gestionnaire des ressources Azure, exécutez `Get-Command *AzureSql*` liste des applets de commande disponibles.


<!--Anchors-->
[Principes fondamentaux de l’audit de base de données]: #subheading-1
[Configurer l’audit pour votre base de données]: #subheading-2
[Analyser les journaux d’audit et rapports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
