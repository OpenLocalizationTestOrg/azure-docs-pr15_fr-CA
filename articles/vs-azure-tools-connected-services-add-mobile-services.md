<properties 
   pageTitle="Ajout de Services mobiles à l’aide de Services connectés dans Visual Studio | Microsoft Azure"
   description="Ajouter des Services mobiles à l’aide de la boîte de dialogue Visual Studio ajouter Services connectés"
   services="visual-studio-online"
   documentationCenter="na"
   authors="mlhoop"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="12/16/2015"
   ms.author="mlearned" />

# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Ajout de Services mobiles à l’aide de Visual Studio Connected Services

Avec Visual Studio 2015, vous pouvez vous connecter aux Services de Mobile Azure à l’aide de la boîte de dialogue **Ajouter un Service connecté** . Vous pouvez vous connecter à partir de n’importe quelle application de client C#, toute application de JavaScript ou multiplates-formes Cordova app. Une fois que vous vous connectez, vous pouvez créer et accéder aux données, créer des API personnalisés et les tâches planifiées ou ajouter la prise en charge des notifications de type Pousser.  L’opération de services connectés ajoute toutes les références appropriées et le code de connexion. Vous pouvez également tirer parti de la prise en charge intégrée pour l’authentification avec une variété de systèmes d’identité les plus courants, tel que AD Azure, Facebook et Twitter Accounts Microsoft.

## <a name="supported-project-types"></a>Types de projet pris en charge

>[AZURE.NOTE] Dans Visual Studio 2015, l’ajout d’Azure Mobile Services à un projets universel de Windows (Windows 10) à l’aide de la boîte de dialogue Ajouter des Services connectés n’est pas pris en charge. Vous pouvez ajouter des Services mobiles de Azure en installant des packages appropriés à l’aide du Gestionnaire de package NuGet pour votre projet.

Vous pouvez utiliser la boîte de dialogue Services connectés pour se connecter aux Services de Mobile Azure dans les types de projet suivants.

- Projets .NET, Windows Store 8.1, téléphone et application universelle

- Projets de JavaScript, Windows Store 8.1, téléphone et application universelle

- Projets créés à l’aide de Visual Studio Tools pour Apache Cordova


## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Se connecter aux Services de Mobile Azure à l’aide de la boîte de dialogue Ajouter des Services connectés

1. Assurez-vous que vous disposez d’un compte Azure. Si vous n’avez pas un compte Azure, vous pouvez vous inscrire pour un [essai gratuit](http://go.microsoft.com/fwlink/?LinkId=518146).

1. Ouvrez la boîte de dialogue **Ajouter des Services connectés** .
 - Pour les applications .NET, ouvrez votre projet dans Visual Studio, ouvrez le menu contextuel pour le nœud **références** dans l’Explorateur de solutions et puis cliquez sur **Ajouter un Service connecté**
 
        ![Connecting to Azure Mobile Service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)

 - Pour les projets d’application de Cordova d’Apache, ouvrez votre projet dans Visual Studio, ouvrez le menu contextuel pour le nœud de projet dans l’Explorateur de solutions et puis cliquez sur **Ajouter un Service connecté**.

1. Dans la boîte de dialogue **Ajouter un Service connecté** , choisissez **Azure Mobile Services**et cliquez sur le bouton **configurer** . Vous pouvez être invité à ouvrir une session dans Azure si vous ne l’avez pas déjà fait.

    ![Ajout d’un Service Mobile Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)

1. Dans la boîte de dialogue **Services de Mobile d’Azure** , choisissez un service mobile existant si vous en avez un. Si vous avez besoin créer un nouveau service mobile Azure, suivez la procédure ci-dessous pour le faire. Sinon, passez à l’étape suivante.

    Pour créer un nouveau compte de service mobile :
    1. Cliquez sur le lien **Créer un Service **en bas de la boîte de dialogue.
        ![Ajouter nouveau service connecté mobile](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)




    2. Dans la boîte de dialogue **Créer un Service Mobile** , vous pouvez choisir un service mobile de back-end JavaScript, ou un service mobile de back-end de .NET à partir de la liste déroulante de **Runtime** . 
  
        ![Création d’un service mobile](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)

        Un service de back-end JavaScript est simple et puissante. Si vous créez un service mobile de back-end JavaScript, le code JavaScript côté serveur est stocké dans le nuage, mais vous pouvez modifier des scripts de serveur en utilisant l’Explorateur de serveurs ou le portail de gestion Azure. 

        Un service mobile de back-end de .NET vous offre la puissance et la flexibilité de l’API Web et Entity Framework. Si vous créez un service mobile de back-end de .NET, un projet est créé et ajouté à votre solution. 

    1. Choisissez la **zone** où vous souhaitez que le service mobile et puis entrez un nom d’utilisateur et le mot de passe pour le serveur.
 
    1. Une fois que vous avez entré toutes les informations requises, cliquez sur le bouton **créer** pour créer le service mobile.
    2. Le nouveau service mobile doit apparaître dans la liste de service dans la boîte de dialogue **Services de Mobile Azure** . Choisissez le nouveau service mobile dans la liste, puis choisissez le bouton **Ajouter** pour ajouter le service à votre projet.
    

1. Passez en revue la page de démarrage qui s’affiche et découvrez comment votre projet a été modifié. Chaque fois que vous ajoutez un service connecté, une page mise en route s’affiche dans votre navigateur. Vous pouvez passer en revue les étapes suggérées et les exemples de code, ou basculez vers la page de ce qui est arrivé pour voir quelles références ont été ajoutés à votre projet, et comment votre code et la configuration des fichiers ont été modifiés.

1. Appuyez-vous sur les exemples de code, démarrer l’écriture de code pour accéder à votre service mobile !

## <a name="how-your-project-is-modified"></a>Comment votre projet est modifié

Comment Visual Studio modifie votre projet dépend du type de projet. Pour les applications du client C#, consultez [qu’est devenu – projets C#](http://go.microsoft.com/fwlink/p/?LinkId=513119). Pour les applications de client JavaScript, voir [qu’est devenu – projets de JavaScript](http://go.microsoft.com/fwlink/p/?LinkId=513120). Pour les applications de Cordova, consultez [qu’est devenu – les projets Cordova](http://go.microsoft.com/fwlink/p/?LinkId=513116).


##<a name="next-steps"></a>Étapes suivantes

Poser des questions et obtenir de l’aide : 

 - [Forum MSDN : Les Services mobiles Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)

 - [Azure Services mobiles au Blog de l’équipe Microsoft Azure](https://azure.microsoft.com/blog/topics/mobile/)

 - [Services mobiles Azure à azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)

 - [Documentation d’Azure Services MSN Mobile à l’adresse azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)



