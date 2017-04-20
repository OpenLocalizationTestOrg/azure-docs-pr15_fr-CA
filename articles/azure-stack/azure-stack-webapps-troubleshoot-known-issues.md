<properties
    pageTitle="Web Apps sur Azure pile - problèmes connus et dépannage | Microsoft Azure"
    description="Instructions détaillées pour déployer des applications Web dans une pile d’Azure"
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
    
# <a name="web-apps-resource-provider---known-issues-and-troubleshooting"></a>Fournisseur de ressources d’applications Web - problèmes connus et dépannage

> [AZURE.NOTE] Les informations suivantes ne s’applique qu’aux déploiements de TP1 de pile Azure.

Si vous rencontrez des problèmes lors du déploiement ou de travailler avec des applications Web sur la pile d’Azure consultez les conseils ci-dessous.

## <a name="azure-stack-web-apps-not-available-in-the-portal"></a>Azure pile Web Apps n’est pas disponible sur le portail

![Applications Web de pile Azure créer la nouvelle application Web][1]

Une fois que vous avez terminé l' [enregistrement du fournisseur applications Azure pile Web](azure-stack-webapps-deploy.md#register-the-newly-deployed-azure-stack-web-apps-provider-with-arm) si vous ne pouvez pas rechercher le Web + une lame Mobile, essayez les opérations suivantes :
* **Déconnexion du portail** et **Fermer votre navigateur** et ensuite une **nouvelle connexion de fenêtre de navigateur sur le portail** et essayez à nouveau.

Si vous ne voyez toujours pas le web et une lame mobile essayez les opérations suivantes :

1.  Sur la **Machine hôte de pile Azure** dans le **Gestionnaire Hyper-V** , recherchez la **xRPVM** et de **se connecter** à la machine virtuelle.
2.  Ouvrez une **invite de commande en tant qu’administrateur** et exécutez **IISRESET**
3.  Revenir à la **ClientVM** et ouvrir une **nouvelle fenêtre de navigateur**, **connexion au portail** et essayez à nouveau.

## <a name="deployment-fails-when-creating-a-web-app-in-a-new-resource-group"></a>Déploiement échoue lors de la création d’une application Web dans un nouveau groupe de ressources

Dans le premier aperçu des applications Web, **Toutes les applications Web** doit être créé dans le même groupe de ressources que le fournisseur de ressources d’applications Web (**AppService-LOCAL**).  Ceci est un problème connu et sera résolu dans un futur aperçu.

## <a name="other-issues"></a>Autres problèmes

Si vous rencontrez d’autres problèmes avec des applications Web sur Azure pile Veuillez comptabiliser dans [le Forum de la pile Azure] (https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) où les membres de notre équipe sont surveillance des publications.


<!--Image references-->
[1]: ./media/azure-stack-webapps-troubleshoot-known-issues/NewWebandMobile.png



<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
