<properties
    pageTitle="Clonage des application Web à l’aide du portail Azure"
    description="Apprenez à vos applications Web pour les nouvelles applications Web à l’aide du portail Azure du clone."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/08/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Application de Service d’application Azure clonage à l’aide d’un portail Azure#

La fonction de clonage dans [Azure Application Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) vous permet de cloner facilement des applications web existantes vers une application créée dans une autre région ou dans la même région. Cela permettra aux clients de déployer un certain nombre d’applications dans différentes régions, rapidement et facilement.

Clonage de l’application est actuellement pris en charge uniquement pour les plans de service premium couche application. La nouvelle fonctionnalité utilise les mêmes limitations que la fonctionnalité de sauvegarde des applications Web, reportez-vous à la section [sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 


## <a name="cloning-an-existing-app"></a>Clonage d’une application existante ##

L’application web doit être en cours d’exécution dans le mode **Premium** pour que vous puissiez créer un clone de l’application web.

1. Dans le [Portail Azure](https://portal.azure.com/), ouvrez les lames de votre application web.
2. Cliquez sur **Outils**. Puis, dans la blade **d’Outils** , cliquez sur **Application de Clone**.

    ![][1]

    > [AZURE.NOTE]
    > Si l’application web n’est pas déjà en mode **Premium** , vous recevrez un message indiquant les modes pris en charge pour le clonage de l’application. À ce stade, vous avez la possibilité d’activer la **mise à niveau**.
    
3. De la lame de **Clone App** fournissent un nom de la nouvelle application web, le groupe de ressources et le Plan de Service d’application. Également l’utilisateur pourra choisir de cloner un certain nombre de paramètres de l’application web source ou non.

    ![][2]

4. Après avoir cliqué sur **créer** la plate-forme va commencer à travailler sur la création d’un clone de l’application web source.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonage d’une application existante vers un environnement de Service d’application##

Dans la lame **App de Clone** , le client aura l’option choisir un pool d’applications dans un environnement de Service d’application existant.

## <a name="current-restrictions"></a>Restrictions en cours ##

Cette fonctionnalité est actuellement en mode Aperçu, nous nous efforçons d’ajouter de nouvelles fonctions au fil du temps, la liste suivante sont les restrictions connues sur la prise en charge actuelle de clonage d’application dans Azure Portal :

- Paramètres de Traffic Manager Azure ne sont pas clonés.
- Paramètres d’échelle automatique ne sont pas clonés.
- Paramètres de planification de la sauvegarde ne sont pas clonés.
- Paramètres de VNET ne sont pas clonés.
- Idées d’application ne sont pas automatiquement définies sur l’application web de destination
- Paramètres d’authentification simples ne sont pas clonés.
- Extension de kudu ne sont pas clonés.
- Conseil les règles ne sont pas clonés.
- Contenu de la base de données ne sont pas clonés.


### <a name="references"></a>Références ###
- [Clonage des applications Web à l’aide de PowerShell](app-service-web-app-cloning.md)
- [Sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md)
- [Comment faire pour créer un environnement de Service d’application](app-service-web-how-to-create-an-app-service-environment.md)
- [Créer une application web dans un environnement de Service d’application](app-service-web-how-to-create-a-web-app-in-an-ase.md)
- [Introduction à l’environnement de Service d’application](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png