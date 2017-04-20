<properties 
   pageTitle="À l’aide du Bureau à distance avec les rôles d’Azure | Microsoft Azure"
   description="À l’aide du Bureau à distance avec les rôles d’Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-remote-desktop-with-azure-roles"></a>À l’aide du Bureau à distance avec les rôles d’Azure

En utilisant l’Azure SDK et les Services Bureau à distance, vous pouvez accéder à rôles d’Azure et les ordinateurs virtuels qui sont hébergés par Azure. Dans Visual Studio, vous pouvez configurer des Services Bureau à distance à partir d’un projet d’Azure. Pour activer les Services Bureau à distance, vous devez créer un projet qui contient un ou plusieurs rôles et puis le publier dans Azure.

>[AZURE.IMPORTANT] Vous devez accéder à un rôle Azure pour le dépannage ou de développement uniquement. L’objectif de chaque machine virtuelle est d’exécuter un rôle spécifique dans votre application Azure, ne pas d’exécuter d’autres applications clientes. Si vous souhaitez utiliser Azure à une machine virtuelle que vous pouvez utiliser à des fins d’hôte, consultez accès à des ordinateurs virtuels Azure à partir de l’Explorateur de serveurs.

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>Pour activer et utiliser le Bureau à distance pour un rôle Azure

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour votre projet et puis cliquez sur **Publier**.

    L’Assistant de **Publication des applications Azure** s’affiche.

    ![Publier le commande pour un projet de Service Cloud](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. Au bas de la page des **Paramètres de publication de Microsoft Azure** de l’Assistant, sélectionnez **Activer le Bureau à distance** pour tous les case à cocher de rôles. 

    La boîte de dialogue **Configuration du Bureau à distance** s’affiche.

1. Au bas de la boîte de dialogue **Configuration du Bureau à distance** , cliquez sur le bouton **Plus d’Options** . 
 
    Cela affiche une zone de liste déroulante qui vous permet de créer ou de sélectionner un certificat de sorte que vous pouvez crypter les informations d’identification lors de la connexion via le Bureau à distance.

1. Dans la liste déroulante, choisissez ** &lt;créer >**, ou choisir un existant dans la liste. 

    Si vous choisissez un certificat existant, ignorez les étapes suivantes.

    >[AZURE.NOTE] Les certificats dont vous avez besoin d’une connexion Bureau à distance sont différents des certificats que vous utilisez pour d’autres opérations d’Azure. Le certificat d’accès à distance doit avoir une clé privée.

    La boîte de dialogue **Créer un certificat** s’affiche.

    1. Fournir un nom convivial pour le nouveau certificat et cliquez sur le bouton **OK** . Le nouveau certificat s’affiche dans la zone de liste déroulante.

    1. Dans la boîte de dialogue **Configuration du Bureau à distance** , fournir un nom d’utilisateur et un mot de passe.
    
        Vous ne pouvez pas utiliser un compte existant. Ne spécifiez pas administrateur comme nom d’utilisateur pour le nouveau compte.

        >[AZURE.NOTE] Si le mot de passe ne répond pas aux exigences de complexité, une icône rouge s’affiche en regard de la zone de texte mot de passe. Le mot de passe doit inclure des majuscules, des minuscules et des numéros ou des symboles.

    1. Choisissez une date à laquelle le compte expire et après les connexions Bureau à distance seront bloquées.

    1. Une fois que vous avez fourni toutes les informations requises, cliquez sur le bouton **OK** .
    
        Plusieurs paramètres qui permettent de Remote Access Services sont ajoutés aux fichiers .cscfg et .csdef.

1. Dans l’Assistant des **Paramètres de publication de Microsoft Azure** , cliquez sur le bouton **OK** lorsque vous êtes prêt à publier votre service cloud.

    Si vous n’êtes pas prêt à publier, cliquez sur le bouton **Annuler** . Les paramètres de configuration sont enregistrées, et vous pouvez publier votre service cloud ultérieurement.

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Se connecter à un rôle d’Azure à l’aide du Bureau à distance

Après avoir publié votre service cloud sur Azure, vous pouvez utiliser l’Explorateur de serveurs pour ouvrir une session dans les ordinateurs virtuels qui héberge Azure. 

1. Dans l’Explorateur de serveurs, développez le nœud **d’Azure** et puis développez le nœud pour un service en nuage et d’un de ses rôles pour afficher une liste d’instances.

1. Ouvrir le menu contextuel pour un nœud de l’instance et puis cliquez sur **Connexion de bureau à distance à l’aide de**.

    ![Connexion via le Bureau à distance](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. Entrez le nom d’utilisateur et le mot de passe que vous avez créé précédemment. Vous êtes maintenant connecté à la session à distance.


