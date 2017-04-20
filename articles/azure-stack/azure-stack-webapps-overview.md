<properties
    pageTitle="Pile Azure Web vue d’ensemble des applications | Microsoft Azure"
    description="Vue d’ensemble des applications Web de pile Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="azure-stack-web-apps-overview"></a>Vue d’ensemble des applications Azure pile Web
    
> [AZURE.NOTE] Les informations suivantes ne s’applique qu’aux déploiements de TP1 de pile Azure.

Azure pile Web Apps est le premier élément de l’offre de Service d’application Azure porté à la pile d’Azure. Le programme d’installation d’applications Web de pile Azure va créer une instance de chacun des cinq types de rôle requis et crée également un serveur de fichiers. Vous pouvez ajouter plusieurs instances pour chacun des types de rôle, gardez à l’esprit qu’il n’a pas l’espace pour les machines virtuelles dans Technical Preview 1. Les capacités actuelles pour les applications Web de pile Azure sont principalement les capacités de base qui sont nécessaires pour gérer les applications web hôte et le système.

![Portail de pile de pile Azure Application Service Web Apps dans l’Azure][1]

## <a name="limitations-of-the-technical-preview"></a>Limitations de la version bêta

Il n’y a aucune prise en charge pour les versions d’aperçu de Service d’application Azure pile. Ne placez des charges de travail sur cette version d’évaluation. Il n’existe aussi aucune mise à niveau entre les versions d’aperçu de Service d’application Azure pile. Les objectifs principaux de ces versions sont pour montrer ce que nous fournissons et pour obtenir des commentaires. 

## <a name="what-is-an-app-service-plan"></a>Quel est le Plan de Service d’une application ?

Le fournisseur de ressources d’applications Web de pile Azure utilise le même code que la fonctionnalité d’applications Web de Azure dans le Service d’application Azure utilise. Par conséquent, certains concepts communs valent décrivant. Dans les applications Web, le conteneur de tarification pour les applications web est appelé le plan de Service de l’application. Il représente l’ensemble des machines virtuelles dédié permettant de conserver vos applications. Dans un abonnement donné, vous pouvez avoir plusieurs plans de Service de l’application. Cela est également vrai dans les applications Web de pile Azure. 

Dans Azure, il y a des employés partagées et dédiés. Un travailleur partagé prend en charge l’hébergement des applications haute densité web partagé et il n'existe qu’un seul ensemble de travailleurs partagés. Des serveurs dédiés sont uniquement utilisées par priorité et se présentent sous trois formats : petites, moyennes et grandes. Les besoins des clients de locaux ne peuvent pas toujours être décrites à l’aide de ces termes. Dans les applications Web de pile Azure, administrateurs de fournisseur de ressources peuvent définir les niveaux de travail qu'ils souhaitent rendre disponibles tels qu’ils aient différents ensembles de travailleurs dédiés en fonction de leur besoins d’hébergement de web unique ou de plusieurs ensembles de travailleurs partagés. À l’aide de ces définitions de couche de travail, ils peuvent puis définir leur propre prix de références SKU.

## <a name="portal-features"></a>Fonctionnalités du portail

Comme c’est également vrai pour le back-end, applications Web de pile Azure utilise la même interface utilisateur que les applications Web Azure utilise. Certaines fonctionnalités sont désactivées et ne sont pas encore fonctionnelles dans la pile d’Azure. C’est pourquoi les attentes spécifiques Azure ou services nécessitant ces fonctionnalités ne sont pas encore disponibles dans la pile d’Azure. 

## <a name="next-steps"></a>Étapes suivantes

- [Avant de commencer avec Azure pile Web Apps](azure-stack-webapps-before-you-get-started.md)
- [Installez le fournisseur de ressources d’applications Web](azure-stack-webapps-deploy.md)

Vous pouvez également essayer autre [plate-forme en tant que service (PaaS) services](azure-stack-tools-paas-services.md), tels que le [fournisseur de ressources SQL Server](azure-stack-sql-rp-deploy-short.md) et le [fournisseur de ressources de MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-overview/AppService_Portal.png
