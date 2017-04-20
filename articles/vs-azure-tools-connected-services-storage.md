<properties 
   pageTitle="Ajouter du stockage Azure dans Visual Studio à l’aide des Services de connexion | Microsoft Azure"
   description="Ajouter du stockage Azure pour votre application à l’aide de la boîte de dialogue Visual Studio ajouter Services connectés"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Ajout de stockage Azure à l’aide de Visual Studio Connected Services

## <a name="overview"></a>Vue d’ensemble

Avec Visual Studio 2015, vous pouvez vous connecter n’importe quel service de cloud C#, .NET back-end mobile service, site Web d’ASP.NET ou service, service 5 d’ASP.NET ou Azure WebJob pour le stockage Azure à l’aide de la boîte de dialogue **Ajouter des Services connectés** . La fonctionnalité service connecté ajoute toutes les références requises et le code de connexion et modifie vos fichiers de configuration de manière appropriée. La boîte de dialogue vous guidera également à la documentation qui vous indique quelles sont les étapes suivantes pour démarrer le stockage blob, les files d’attente et les tables.

## <a name="supported-project-types"></a>Types de projet pris en charge

Vous pouvez utiliser la boîte de dialogue Services connectés pour se connecter au stockage Azure dans les types de projet suivants.

- Projets Web d’ASP.NET

- ASP.NET 5 projets

- Rôle de .NET cloud Service Web et les projets de rôle de travail

- Projets de Services mobiles .NET

- Projets d’Azure WebJob


## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Se connecter au stockage Azure à l’aide de la boîte de dialogue Services connectés

1. Assurez-vous que vous disposez d’un compte Azure. Si vous n’avez pas un compte Azure, vous pouvez vous inscrire pour un [essai gratuit](http://go.microsoft.com/fwlink/?LinkId=518146). Une fois que vous avez un compte Azure, vous pouvez créer des comptes de stockage, créer des services mobiles et configurer Azure Active Directory.

1. Ouvrez votre projet dans Visual Studio, ouvrez le menu contextuel pour le nœud **références** dans l’Explorateur de solutions et puis cliquez sur **Ajouter un Service connecté**.

    ![Ajout d’un service connecté](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Dans la boîte de dialogue **Ajouter un Service connecté** , choisissez **Le stockage Azure**et cliquez sur le bouton **configurer** . Vous pouvez être invité à ouvrir une session dans Azure si vous ne l’avez pas déjà fait.

    ![Ajouter la boîte de dialogue de connexion de Service - stockage](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. Dans la boîte de dialogue **Stockage Azure** , sélectionnez un compte de stockage existant et sélectionnez **Ajouter**.

    Si vous avez besoin créer un nouveau compte de stockage, passez à l’étape suivante. Sinon, passez à l’étape 6.

    ![Boîte de dialogue stockage Azure](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. Pour créer un nouveau compte de stockage : 

    1. Cliquez sur le bouton **créer un nouveau compte de stockage** au bas de la boîte de dialogue stockage Azure.

    1. Remplissez la boîte de dialogue **Créer un compte de stockage** , puis choisissez le bouton **créer** .
    
        ![Boîte de dialogue stockage Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        Lorsque vous êtes de retour dans la boîte de dialogue **Stockage Azure** , le nouveau stockage apparaît dans la liste.

    1. Sélectionnez le nouveau stockage dans la liste, puis sur **Ajouter**.

1. Le service de stockage connecté s’affiche sous le nœud Références de Service de votre projet de WebJob.

    ![Stockage Azure dans le projet de travaux de web](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. Passez en revue la page de démarrage qui s’affiche et découvrez comment votre projet a été modifié. Chaque fois que vous ajoutez un service connecté, une page mise en route s’affiche dans votre navigateur. Vous pouvez passer en revue les étapes suggérées et les exemples de code, ou basculez vers la page de ce qui est arrivé pour voir quelles références ont été ajoutés à votre projet, et comment votre code et la configuration des fichiers ont été modifiés.

## <a name="how-your-project-is-modified"></a>Comment votre projet est modifié

Lorsque vous avez terminé la boîte de dialogue, Visual Studio ajoute des références et modifie certains fichiers de configuration. Les modifications spécifiques varient selon le type de projet. 

 - Pour les projets ASP.NET, reportez-vous à la section [qu’est devenu – les projets ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
 - Pour les projets ASP.NET 5, voir [qu’est devenu – projets 5 de ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
 - Pour le cloud des projets de service (rôles web et rôles worker), voir [ce qui est arrivé : projets de Service Cloud](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
 - Pour les projets WebJob, voir [ce qui s’est passé - WebJob projets](./storage/vs-storage-webjobs-what-happened.md).

## <a name="next-steps"></a>Étapes suivantes

1. Appuyez-vous sur les exemples de code de mise en route, créer le type de stockage que vous souhaitez, puis démarrer l’écriture de code pour accéder à votre compte de stockage !

1. Poser des questions et obtenir de l’aide
     - [Forum MSDN : Stockage Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Stockage à azure.microsoft.com](https://azure.microsoft.com/services/storage/)

     - [Documentation de stockage à l’adresse azure.microsoft.com](https://azure.microsoft.com/documentation/services/storage/)

