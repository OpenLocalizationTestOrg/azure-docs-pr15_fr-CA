<properties 
   pageTitle="Gestion des données d’Azure automatisation | Microsoft Azure"
   description="Cet article contient plusieurs rubriques pour la gestion d’un environnement d’automatisation d’Azure.  Inclut actuellement la rétention des données et la sauvegarde de la récupération d’urgence Azure Automation dans Azure Automation."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren;sngun" />

# <a name="managing-azure-automation-data"></a>Gestion de données Azure Automation

Cet article contient plusieurs rubriques pour la gestion d’un environnement d’automatisation d’Azure.

## <a name="data-retention"></a>Rétention des données

Lorsque vous supprimez une ressource d’Automation d’Azure, il est conservé pendant 90 jours à des fins d’audit avant d’être supprimés définitivement.  Impossible de voir ou utiliser la ressource pendant cette période.  Cette stratégie s’applique également aux ressources qui appartiennent à un compte d’automation qui est supprimé.

Automation Azure supprime automatiquement et supprime définitivement les travaux de plus de 90 jours.

Le tableau suivant récapitule la stratégie de rétention pour les différentes ressources.

|Données|Stratégie de|
|:---|:---|
|Comptes|Définitivement supprimées de 90 jours après que le compte est supprimé par un utilisateur.|
|Actifs|Définitivement supprimés 90 jours après que l’actif est supprimé par un utilisateur, ou 90 jours après le compte qui détient que l’actif est supprimé par un utilisateur.|
|Modules|Définitivement supprimés 90 jours après que le module est supprimé par un utilisateur, ou 90 jours après le compte qui contient que le module est supprimé par un utilisateur.|
|Procédures opérationnelles|Définitivement supprimées de 90 jours, une fois que la ressource est supprimée par un utilisateur, ou 90 jours après le compte qui contient que la ressource est supprimée par un utilisateur.|
|Travaux|Supprimés et retirés définitivement de 90 jours après la dernière en cours de modification. Il peut s’agir d’une fois la tâche terminée, est arrêtée ou est interrompue.|
|Fichiers de Configurations/MOF de nœud| Ancienne configuration de nœud est supprimée définitivement de 90 jours après la génération d’une nouvelle configuration de nœud.|
|Nœuds de DSC| Définitivement supprimées de 90 jours après que le nœud est annulé à partir du compte d’Automation à l’aide d’Azure portal ou l’applet de commande [Unregister-AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) dans Windows PowerShell. Nœuds sont également définitivement supprimés 90 jours après le compte qui contient que le nœud est supprimé par un utilisateur. |
|Rapports de nœud| Suppression définitive de 90 jours après qu’un nouveau rapport est généré pour ce nœud|

La stratégie de rétention s’applique à tous les utilisateurs et ne peuvent pas être personnalisée.

## <a name="backing-up-azure-automation"></a>Sauvegarde Azure Automation

Lorsque vous supprimez un compte d’automation dans Microsoft Azure, tous les objets dans le compte sont supprimés, y compris les procédures opérationnelles, modules, configurations, paramètres, tâches et ressources. Impossible de récupérer les objets une fois que le compte est supprimé.  Vous pouvez utiliser les informations suivantes pour sauvegarder le contenu de votre compte d’automation avant de le supprimer. 

### <a name="runbooks"></a>Procédures opérationnelles

Vous pouvez exporter les procédures opérationnelles pour les fichiers de script à l’aide de l’applet de commande [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) ou du portail de gestion Azure dans Windows PowerShell.  Ces fichiers de script peuvent être importés dans un autre compte d’automation, comme expliqué dans [Création ou importation d’une procédure opérationnelle](https://msdn.microsoft.com/library/dn643637.aspx).


### <a name="integration-modules"></a>Modules d’intégration

Vous ne pouvez pas exporter les modules d’intégration Azure Automation.  Vous devez vous assurer qu’elles sont disponibles à l’extérieur du compte de l’automation.

### <a name="assets"></a>Actifs

Vous ne pouvez pas exporter des [actifs](https://msdn.microsoft.com/library/dn939988.aspx) Azure Automation.  À l’aide du portail de gestion Azure, vous devez noter les détails des variables, d’informations d’identification, des certificats, des connexions et des planifications.  Vous devez créer manuellement les actifs qui sont utilisés par les procédures opérationnelles que vous importez dans l’automatisation d’une autre.

Vous pouvez utiliser les [applets de commande Azure](https://msdn.microsoft.com/library/dn690262.aspx) pour récupérer les informations d’actifs non cryptées et enregistrer les pour référence ultérieure ou de créer des actifs équivalents dans un autre compte d’automation.

Impossible de récupérer la valeur de variables cryptés ou le champ de mot de passe des informations d’identification à l’aide des applets de commande.  Si vous ne connaissez pas ces valeurs, vous pouvez les récupérer à partir d’une procédure opérationnelle à l’aide des activités de [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) et [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) .

Vous ne pouvez pas exporter certificats Azure Automation.  Vous devez vous assurer que tous les certificats sont disponibles en dehors d’Azure.

### <a name="dsc-configurations"></a>Configurations de DSC

Vous pouvez exporter vos configurations pour les fichiers de script en utilisant le portail de gestion Azure ou l’applet de commande [Exportation-AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) dans Windows PowerShell. Ces configurations peuvent être importées et utilisées dans un autre compte d’automation.


##<a name="geo-replication-in-azure-automation"></a>Geo-réplication dans Azure Automation

Geo-réplication, standard dans les comptes de l’automatisation d’Azure, sauvegarde les données de compte à une région géographique différente pour la redondance. Lors de la configuration de votre compte, vous pouvez choisir une zone principale, puis une zone secondaire est affectée automatiquement. Les données secondaires, copiées à partir de la zone primaire, sont continuellement mis à jour en cas de perte de données.  

Le tableau suivant indique les appariements de la région principale et secondaire disponible.

|Principal            |Secondaire
| ---------------   |----------------
|États-Unis centre sud   |États-Unis centre nord
|États-Unis East 2          |États-Unis centre
|Europe de l’ouest        |Europe du Nord
|Asie du Sud-est    |Asie de l’est
|Nord-est du Japon         |Ouest du Japon

Dans le cas peu probable qu’une donnée de la région principale est perdue, Microsoft tente de récupérer. Si les données principales ne peut pas être récupérées, puis géo-basculement est effectuée, et les clients concernés seront communiquées à ce sujet par le biais de leur abonnement.

