<properties 
    pageTitle="Journalisation des services de formation de Machine web | Microsoft Azure" 
    description="Découvrez comment activer la journalisation pour les services web de formation de l’ordinateur. Enregistrement dans le journal fournit des informations supplémentaires pour vous aider à résoudre les problèmes liés à l’API." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>

# <a name="enable-logging-for-machine-learning-web-services"></a>Activer la journalisation pour les services web apprentissage automatique  

Ce document fournit des informations sur la fonctionnalité de journalisation de services Web classique. L’activation de l’enregistrement dans les services Web fournit des informations supplémentaires, au-delà d’un numéro d’erreur et un message, ce qui peut vous aider à résoudre les problèmes de vos appels à l’API d’apprentissage Machine.  

Pour activer l’enregistrement dans les Services Web dans Azure portal classique :   

1.  Connectez-vous au [portail de classique Azure](https://manage.windowsazure.com/)
2.  Dans la colonne de gauche des fonctionnalités, cliquez sur **Apprentissage automatique**.
3.  Cliquez sur votre espace de travail, puis les **SERVICES WEB**.
4.  Dans la liste de services Web, cliquez sur le nom du service Web.
5.  Dans la liste des points de terminaison, cliquez sur le nom de point de terminaison.
6.  Cliquez sur **configurer**.
7.  Définir le **Niveau de TRACE de diagnostic** *d’erreur* ou *tous les*, puis cliquez sur **Enregistrer**.

Pour activer l’enregistrement dans le portail Azure Machine formation Web Services.

1. Connectez-vous au portail [Azure Machine Learning Web Services](https://services.azureml.net) .
2. Cliquez sur Services Web classique.
3.  Dans la liste de services Web, cliquez sur le nom du service Web.
4.  Dans la liste des points de terminaison, cliquez sur le nom de point de terminaison.
5.  Cliquez sur **configurer**.
6.  Paramétrer l’option **journal** *d’erreur* ou *tous les*, puis cliquez sur **Enregistrer**.

## <a name="the-effects-of-enabling-logging"></a>Les effets de l’activation de la journalisation

Lorsque la journalisation est activée, tous les tests de diagnostic et les erreurs du point de terminaison sélectionné sont enregistrés dans le compte de stockage Azure liés avec l’espace de travail de l’utilisateur. Vous pouvez voir ce compte de stockage dans le portail classique Azure affichage tableau de bord (en bas de la section Aperçu rapide) de leur espace de travail.  

Les journaux peuvent être affichés à l’aide de plusieurs outils pour « explorer » un compte de stockage Azure. La plus simple peut consister à naviguer simplement dans le compte de stockage dans Azure portal classique et puis cliquez sur **les conteneurs**. Vous verrez ensuite un conteneur nommé **ml-diagnostics**. Ce conteneur contient toutes les informations de diagnostic pour toutes les extrémités de service web pour tous les espaces de travail associés à ce compte de stockage. 
 
## <a name="log-blob-detail-information"></a>Journal des informations détaillées sur les blob

Chaque objet blob dans le conteneur conserve les informations de diagnostic pour exactement l’une des opérations suivantes :

-   L’exécution de la méthode d’exécution de traitement par lots  
-   L’exécution de la méthode de requête-réponse  
-   Initialisation d’un conteneur de type requête-réponse
  
Le nom de chaque objet blob a un préfixe sous la forme suivante : 

{Id de l’espace de travail}-{service Id Web}-{Id point de terminaison} /log {type}  

Type de journal étant une des valeurs suivantes :  

- traitement par lots  
- score/requêtes  
- score/init  