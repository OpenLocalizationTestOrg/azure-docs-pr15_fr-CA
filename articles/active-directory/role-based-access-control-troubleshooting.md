<properties
    pageTitle="Résolution des problèmes de contrôle de l’accès basée sur les rôles | Microsoft Azure"
    description="Obtenir de l’aide sur des problèmes ou des questions sur les ressources du contrôle d’accès rôle."
    services="azure-portal"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="kgremban"/>

# <a name="role-based-access-control-troubleshooting"></a>Dépannage de contrôle d’accès basée sur les rôles

## <a name="introduction"></a>Introduction

[Contrôle d’accès basée sur les rôles](role-based-access-control-configure.md) est une fonctionnalité puissante qui vous permet de déléguer l’accès précis aux ressources dans Azure. Cela signifie que vous pouvez être sûr de l’octroi d’une personne en particulier le droit d’utiliser exactement ce qu’ils recherchent et pas davantage. Toutefois, le modèle de ressources pour les ressources Azure peut parfois complexe et il peut être difficile de comprendre exactement ce que vous accordez des autorisations à.

Ce document vous permettra de savoir ce qui se passe lorsque vous utilisez certains des rôles dans le portail Azure. Ces trois rôles couvrent tous les types de ressources :

- Propriétaire  
- Collaborateur  
- Lecteur  

Propriétaires et contributeurs ont un accès complet à l’environnement de gestion, mais un collaborateur ne peut pas accéder à d’autres utilisateurs ou groupes. Les choses deviennent un peu plus intéressants avec le rôle de lecteur, voilà où nous allons passer quelque temps. Consultez l' [article de démarrer le contrôle d’accès basée sur les rôles](role-based-access-control-configure.md) pour plus d’informations sur la façon d’accorder l’accès.

## <a name="app-service-workloads"></a>Charges de service d’application

### <a name="write-access-capabilities"></a>Fonctionnalités de l’accès en écriture

Si vous accordez à un utilisateur l’accès en lecture seule à une application web unique, certaines fonctionnalités sont désactivées, que vous ne pouvez pas vous y attendre. Les fonctionnalités de gestion suivantes requièrent un accès en **écriture** à une application web (Contributeur ou propriétaire) et ne sont pas disponibles dans tous les scénarios en lecture seule.

- Commandes (par exemple, Démarrer, arrêter, etc.)
- Modification des paramètres tels que la configuration générale, paramètres d’échelle, les paramètres de sauvegarde et les paramètres de surveillance
- L’accès aux informations d’identification de publication et autres secrets tels que les paramètres de l’application et des chaînes de connexion
- Diffusion en continu de journaux
- Configuration de journaux de diagnostic
- Console (invite de commande)
- Déploiements actives et récentes (pour un déploiement continu de git local)
- Estimation des dépenses
- Tests Web
- Réseau virtuel (visible uniquement par un lecteur si un réseau virtuel a été configuré précédemment par un utilisateur avec un accès en écriture).

Si vous ne pouvez pas accéder à aucune de ces tuiles, vous devez demander à votre administrateur d’accès de type collaborateur à l’application web.

### <a name="dealing-with-related-resources"></a>Gérer les ressources connexes

Les applications Web sont compliquées par la présence de quelques différentes ressources interaction. Voici un groupe de ressources de type avec deux sites :

![Groupe de ressources d’application Web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Par conséquent, si vous lui accordez l’accès à tout l’application web, la plupart des fonctionnalités sur la lame de site Web dans le portail Azure sera désactivé.

Ces éléments nécessitent un accès en **écriture** pour le **service d’application** qui correspond à votre site Web :  

- Affichage de l’application web de tarification de niveau (libre ou Standard)  
- Configuration de l’échelle (nombre d’instances, taille de l’ordinateur virtuel, les paramètres d’échelle)  
- Quotas (stockage, bande passante, CPU)  

Ces éléments nécessitent un accès en **écriture** à l’ensemble du **groupe de ressources** qui contient votre site Web :  

- Certificats SSL et les liaisons (c’est parce que les certificats SSL peuvent être partagés entre les sites dans le même groupe de ressources et de géolocalisation)  
- Règles d’alerte  
- Paramètres d’échelle automatique  
- Composants d’idées d’application  
- Tests Web  

## <a name="virtual-machine-workloads"></a>Charges de travail de machine virtuelle

Bien comme avec les applications web, certaines fonctionnalités sur la lame de machine virtuelle requièrent un accès en écriture à la machine virtuelle, ou à d’autres ressources du groupe de ressources.

Ordinateurs virtuels associés à des noms de domaine, réseaux virtuels, comptes de stockage et les règles d’alerte.

Ces éléments nécessitent un accès en **écriture** à la **machine virtuelle**:

- Points de terminaison  
- Adresses IP  
- Disques  
- Extensions  

Elles nécessitent l’accès en **écriture** à la **machine virtuelle**et le **groupe de ressources** (ainsi que le nom de domaine) où il se trouve :  

- Ensemble de disponibilité  
- Charge équilibrée  
- Règles d’alerte  

Si vous ne pouvez pas accéder à aucune de ces tuiles, vous devez demander à votre administrateur d’accès de type collaborateur au groupe de ressources.

## <a name="see-more"></a>En savoir plus
- [Contrôle d’accès rôle](role-based-access-control-configure.md): mise en route de RBAC dans Azure portal.
- [Rôles intégrés](role-based-access-built-in-roles.md): obtenir des informations sur les rôles qui sont fournis par défaut dans RBAC.
- [Les rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md): Apprenez à créer des rôles personnalisés pour répondre à vos besoins d’accès.
- [Créer un accès de rapport historique des modifications](role-based-access-control-access-change-history-report.md): effectuer le suivi de modification des attributions de rôle dans RBAC.
