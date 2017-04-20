<properties
   pageTitle="Sécurité d’Automation Azure | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble de la sécurité de l’automation et les différentes méthodes d’authentification disponibles pour les comptes d’Automation dans Azure Automation."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="sécurité d’Automation, automation sécurisée" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="magoedte" />

# <a name="azure-automation-security"></a>Sécurité d’Automation Azure
Azure Automation vous permet d’automatiser des tâches par rapport aux ressources dans Azure, sur site et avec d’autres fournisseurs de nuage comme Amazon Web Services (AWS).  Dans l’ordre pour une procédure opérationnelle d’exécuter les actions requises, il doit avoir des autorisations pour accéder en toute sécurité les ressources avec les droits minimums requis au sein de l’abonnement.  
Cet article couvre les différents scénarios d’authentification pris en charge par Automation d’Azure et vous montrera comment commencer en fonction de l’environnement ou les environnements que vous devez gérer.  

## <a name="automation-account-overview"></a>Vue d’ensemble du compte Automation
Lorsque vous démarrez l’Automation d’Azure pour la première fois, vous devez créer au moins un compte d’Automation. Automation comptes vous permettent d’isoler vos ressources Automation (procédures opérationnelles, actifs, configurations) à partir des ressources contenues dans d’autres comptes d’Automation. Vous pouvez utiliser des comptes de l’Automation à séparer les ressources dans des environnements logiques distincts. Par exemple, vous pouvez utiliser un compte pour le développement, l’autre pour la production et l’autre pour votre environnement local.  Un compte Azure Automation est différent de votre compte Microsoft ou des comptes créés dans votre abonnement Azure.

Les ressources d’automatisation pour chaque compte d’Automation sont associés à une seule région d’Azure, mais les comptes Automation peuvent gérer les ressources dans une région. La raison principale pour créer des comptes de l’Automation dans différentes régions serait si vous disposez de stratégies qui nécessitent des données et des ressources pour être isolé pour une région spécifique.

>[AZURE.NOTE]Comptes d’Automation et les ressources qu’ils contiennent qui sont créés dans le portail Azure, ne sont pas accessibles dans Azure portal classique. Si vous souhaitez gérer ces comptes ou leurs ressources avec Windows PowerShell, vous devez utiliser les modules du Gestionnaire de ressources Azure.

Toutes les tâches que vous effectuez par rapport aux ressources à l’aide du Gestionnaire de ressources Azure et les applets de commande Azure dans Azure Automation doivent s’authentifier pour Azure utilisant l’authentification par Azure Active Directory identité d’organisation basée sur les informations d’identification.  L’authentification par certificat est la méthode d’authentification d’origine avec le mode de gestion des services Azure, mais il était compliqué à configurer.  Authentification vers Azure avec utilisateur AD Azure a été arrière introduite en 2014 non seulement simplifier le processus pour configurer un compte d’authentification, mais prennent également en charge la possibilité d’authentifier de manière non interactive vers Azure avec un seul compte d’utilisateur qui a travaillé avec le Gestionnaire de ressources Azure et ressources classiques.   

Actuellement, lorsque vous créez un nouveau compte d’Automation dans le portail Azure, il crée automatiquement :

-  Exécuter en tant que compte qui crée une nouvelle entité de service dans Active Directory Azure, un certificat et assigne le contrôle d’accès basé sur le rôle de collaborateur (RBAC), qui sera utilisé pour gérer les ressources du Gestionnaire de ressources à l’aide de procédures opérationnelles.
-  Exécuter en tant que compte classique en téléchargeant un certificat de gestion, qui sera utilisé pour gérer la gestion des services Azure ou ressources classiques à l’aide de procédures opérationnelles.  

Contrôle d’accès basé sur les rôles est disponible avec le Gestionnaire de ressources Azure pour accorder les actions autorisées pour un compte d’utilisateur AD Azure et les exécuter en tant que compte, authentifier ce principal du service.  Veuillez lire le [contrôle d’accès par rôle dans Azure Automation article](../automation/automation-role-based-access-control.md) pour plus d’informations pour vous aider à développer votre modèle de gestion des autorisations d’Automation.  

Procédures opérationnelles en cours d’exécution sur un travailleur de procédure opérationnelle hybride dans votre centre de données ou contre les services dans AWS informatiques ne peuvent pas utiliser la même méthode que celle qui est généralement utilisée pour l’authentification des procédures opérationnelles aux ressources Azure.  C’est parce que ces ressources sont en cours d’exécution en dehors d’Azure et nécessitent, par conséquent, leurs propres informations d’identification de sécurité définies dans Automation pour s’authentifier auprès de ressources auxquels ils auront accès localement.  

## <a name="authentication-methods"></a>Méthodes d’authentification

Le tableau suivant résume les différentes méthodes d’authentification pour chaque environnement pris en charge par Azure Automation et de l’article qui décrit comment configurer l’authentification pour les procédures opérationnelles.

Méthode  |  Environnement  | Article
----------|----------|----------
Compte d’utilisateur d’AD Azure | Le Gestionnaire de ressources Azure et gestion des services Azure | [Authentifier les procédures opérationnelles avec un compte d’utilisateur d’AD Azure](../automation/automation-sec-configure-aduser-account.md)
Azure exécuter en tant que compte | Gestionnaire de ressources Azure | [Authentifier les procédures opérationnelles avec Azure exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)
Azure exécuter classique en tant que compte | Gestion des services Azure | [Authentifier les procédures opérationnelles avec Azure exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)
Authentification Windows | Centre de données sur site | [Authentifier des procédures opérationnelles pour les travailleurs de procédure opérationnelle hybride](../automation/automation-hybrid-runbook-worker.md)
Informations d’identification de l’AWS | Amazon Web Services | [Authentifier les procédures opérationnelles avec Amazon Web Services (AWS)](../automation/automation-sec-configure-aws-account.md)



