<properties
    pageTitle="Activer l’état entreprise itinérant dans Azure Active Directory | Microsoft Azure"
    description="Forum aux questions sur itinérant d’état entreprise paramètres de périphériques Windows. État d’entreprise itinérance fournit aux utilisateurs une expérience unifiée sur leurs périphériques Windows et réduit le temps nécessaire pour la configuration d’un nouveau périphérique."
    services="active-directory"
    keywords="État entreprise itinérant, nuage de windows, comment faire pour activer l’itinérance d’état entreprise"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>



# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Activer l’état entreprise itinérant dans Azure Active Directory

Itinérance des état d’entreprise est disponible pour toute organisation avec un abonnement Premium Azure Active Directory (AD Azure). Pour plus d’informations sur la façon d’obtenir un abonnement Azure AD, consultez la [page du produit AD d’Azure](https://azure.microsoft.com/services/active-directory).

Lorsque vous activez l’itinérance des état d’entreprise, votre organisation est automatiquement accordée les licences pour un abonnement gratuit et d’utilisation limitée à la gestion des droits d’Azure. Abonnement gratuit est limité à crypter et décrypter les paramètres d’entreprise et les données d’application synchronisées par le service d’itinérance état entreprise ; Vous devez disposer d’un abonnement payant à utiliser toutes les fonctionnalités de gestion des droits Azure.

Après avoir obtenu un abonnement Premium AD Azure, procédez comme suit pour activer l’itinérance des état d’entreprise :

1. Connexion au portail classique Azure.
2. Sur la gauche, sélectionnez **ACTIVE DIRECTORY**et sélectionnez le répertoire pour lequel vous souhaitez activer l’itinérance d’état entreprise.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. Accédez à l’onglet **configurer** dans la partie supérieure.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.  Faites défiler la page vers le bas et sélectionnez **les utilisateurs peuvent synchroniser les paramètres et données d’application entreprise**puis cliquez sur **Enregistrer**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Pour un périphérique Windows 10 passer des paramètres avec le service d’itinérance d’état entreprise, le périphérique doit s’authentifier à l’aide d’une identité d’Azure AD. Pour les périphériques qui sont liés à l’annonce d’Azure, ouverture de session de l’utilisateur principal est l’identité Active Directory Azure, aucune configuration supplémentaire n’est nécessaire. Pour les périphériques qui utilisent un traditionnelles sur site Active Directory, l’administrateur doit [se connecter les périphériques à un domaine AD Azure pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="sync-data-storage"></a>Stockage de données de synchronisation
Données itinérantes d’état entreprise sont hébergées dans une ou plusieurs [régions Azure](https://azure.microsoft.com/regions/ ) mieux adaptée à la valeur de pays/région définie dans l’instance Azure Active Directory. Données itinérantes d’état entreprise sont partitionnées en fonction des trois principales régions : Amérique du Nord, EMEA et Asie-Pacifique. État d’entreprise itinérance des données pour les clients se trouve localement avec la région géographique et ne sont pas répliquées sur les régions.  Par exemple, les clients qui ont leur valeur de pays/région définie à l’un des pays de l’EMEA comme « France » ou « Zambie » auront leurs données hébergées dans une ou des régions d’Europe Azure.  Clients ayant valeur leur pays/région dans Azure AD à l’un des pays de l’Amérique du Nord comme « United States » ou « Canada » auront leurs données hébergées dans une ou plusieurs des régions Azure aux États-Unis.  Clients ayant valeur leur pays/région dans Azure AD à l’un des pays d’Asie-Pacifique comme « Australie » ou « Nouvelle-Zélande » auront leurs données hébergées dans un ou plusieurs des régions d’Asie Azure.  Pays d’Amérique du Sud et les données de l’Antarctique seront hébergées dans une ou plusieurs régions Azure aux États-Unis.  La valeur de pays/région est définie en tant que partie du processus de création de répertoire AD Azure et ne peut pas être modifiée par la suite. 

Si vous avez besoin de plus d’informations sur l’emplacement de stockage des données, veuillez soumettre un ticket avec [Azure prend en charge](https://azure.microsoft.com/support/options/).

## <a name="manage-enterprise-state-roaming"></a>Gérer l’état d’entreprise itinérant
Les administrateurs globaux Azure AD peuvent activer et désactiver l’itinérance des état d’entreprise dans le portail classique Azure.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Un administrateur global peut limiter la synchronisation des paramètres pour les groupes de sécurité spécifiques.

Administrateurs globaux peuvent également afficher un rapport d’état par utilisateur périphérique synchronisation par sélection d’un utilisateur particulier dans la liste des **utilisateurs** Active Directory instance et en cliquant sur l’onglet **périphériques** , afficher **périphériques la synchronisation des paramètres et des données d’application enterprise**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

##<a name="data-retention"></a>Rétention des données
Les données synchronisées dans Azure via l’itinérance d’état entreprise seront conservées indéfiniment, sauf si une opération de suppression manuelle est effectuée ou les données en question sont déterminées comme étant obsolètes. 

**Suppression explicite :** Les données sont supprimées lorsqu’un administrateur Azure supprime un utilisateur ou un répertoire ou un administrateur demande explicitement que les données doit être supprimé.

- **Suppression d’un utilisateur**: lorsqu’un utilisateur est supprimé dans Azure AD, le compte d’utilisateur itinérant de données sera marqué pour suppression et seront supprimé entre 90 et 180 jours. 
- **Suppression du répertoire**: suppression d’un répertoire entier dans Azure AD est immédiatement l’opération. Toutes les données de paramètres associés que répertoire sera marquée pour suppression et est supprimé entre 90 et 180 jours. 
- **Suppression de la demande**: si l’administrateur AD Azure souhaite supprimer manuellement les données ou paramètres d’un utilisateur spécifique, l’administrateur peut fichier un ticket avec [Azure prend en charge](https://azure.microsoft.com/support/). 

**Suppression des données obsolètes**: données qui n’ont pas été utilisées pour un an (« la période de rétention ») est considéré comme obsolète et peut être supprimé d’Azure. La période de rétention peut être modifiée, mais ne sera pas inférieure à 90 jours. Les données obsolètes peuvent être un ensemble spécifique de paramètres d’application/Windows ou tous les paramètres d’un utilisateur. Par exemple :
 
- Si aucun périphérique à accéder à une collection de paramètres particuliers (par exemple, une application est supprimée à partir du périphérique, ou un groupe de paramètres, tels que « Thème » est désactivé pour tous les périphériques d’un utilisateur), cette collection devient obsolète après la période de rétention et peuvent être supprimée. 
- Si un utilisateur a désactivé les paramètres de synchronisation sur tous ses périphériques, puis les données de paramètres seront accessibles et toutes les données de paramètres pour cet utilisateur deviennent obsolètes et peuvent être supprimées après la période de rétention. 
- Si l’administrateur d’annuaire AD Azure désactive itinérants d’état entreprise pour l’ensemble du répertoire, puis de tous les utilisateurs dans ce répertoire s’arrête la synchronisation de paramètres, et toutes les données de paramètres pour tous les utilisateurs deviendront obsolètes et peuvent être supprimées après la période de rétention. 

**Récupération des données supprimées**: la stratégie de rétention des données n’est pas configurable. Une fois que les données a été définitivement supprimées, il ne peut pas être récupéré. Toutefois, il est important de noter que les données de paramètres seront uniquement supprimées Azure, pas le périphérique de l’utilisateur final. Si n’importe quel périphérique se connecte de nouveau au service d’itinérance état entreprise, les paramètres seront à nouveau synchronisées et stockées dans Azure.


## <a name="related-topics"></a>Rubriques connexes
- [Vue d’ensemble de l’entreprise d’état itinérance](active-directory-windows-enterprise-state-roaming-overview.md)
- [Forum aux questions sur l’itinérance des données et les paramètres](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Les paramètres de stratégie et MDM pour la synchronisation des paramètres de groupe](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Référence des paramètres itinérant Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
