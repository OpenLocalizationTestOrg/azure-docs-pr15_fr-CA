<properties
   pageTitle="Gérer les Services d’analyse Azure | Microsoft Azure"
   description="Apprenez à gérer un serveur Analysis Services dans Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="manage-analysis-services"></a>Gérer les Services d’analyse

Une fois que vous avez créé un serveur Analysis Services dans Azure, il est peut-être certaines tâches d’administration et que vous devez effectuer tout de suite ou parfois vers le bas de la route. Par exemple, exécuter le traitement vers les données d’actualisation, contrôler qui peut accéder aux modèles sur votre serveur, ou surveiller la santé de votre serveur. Certaines tâches de gestion peuvent uniquement être effectuées dans Azure portal, d’autres dans SQL Server Management Studio (SSMS), et certaines tâches peuvent être effectuées dans une ou l’autre.

## <a name="azure-portal"></a>Azure portal
Le [portail Azure](http://portal.azure.com/) est où vous pouvez créer et supprimer des serveurs, surveiller les ressources de serveur, modifier la taille et gérez les personnes ayant accès à vos serveurs.  Si vous rencontrez des problèmes, vous pouvez également envoyer une demande de support.

![Obtenir le nom du serveur dans Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Connexion à votre serveur dans Azure est tout comme la connexion à une instance de serveur dans votre organisation. De SSMS, vous pouvez effectuer de nombreuses tâches telles que les données de processus ou créer un script de traitement, gérer les rôles et utiliser PowerShell.

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 Une des différences plus importants est l’authentification que vous utilisez pour vous connecter à votre serveur. Pour vous connecter à votre serveur Azure Analysis Services, vous devez sélectionner **L’authentification de mot de passe Active Directory**.

### <a name="to-connect-with-ssms"></a>Se connecter avec SSMS
1. Avant de vous connecter, vous devez obtenir le nom du serveur. Dans **Azure portal** > serveur > **vue d’ensemble** > **nom de serveur**, copiez le nom du serveur.

    ![Obtenir le nom du serveur dans Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

2. Dans SSMS > **Explorateur d’objets**, cliquez sur **se connecter** > **Analysis Services**.

3. Dans la boîte de dialogue **se connecter au serveur** , collez dans le nom du serveur, puis de **l’authentification**, choisissez l’une des opérations suivantes :

    **Authentification intégrée à active Directory** permet une ouverture de session unique avec Active Directory fédération d’Azure Active Directory.

    **L’authentification de mot de passe active Directory** d’utiliser un compte d’organisation. Par exemple, lorsque se connectant à partir d’un domaine non-rejoint ordinateur.

    Remarque : Si vous ne voyez pas l’authentification Active Directory, vous devrez peut-être [Activer l’authentification Active Directory de Azure](#enable-azure-active-directory-authentication) dans SSMS.

    ![Se connecter dans SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Étant donné que la gestion de votre serveur dans Azure à l’aide de SSMS est très similaire à la gestion d’un serveur local, nous n’allons pas dans les détails ici. Toute l’aide vous avez besoin sont accessibles dans la [Gestion des instances Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) sur MSDN.

## <a name="server-administrators"></a>Administrateurs de serveur
Vous pouvez utiliser des **Administrateurs de Services d’analyse** de la lame de contrôle de votre serveur dans Azure portal ou SSMS pour gérer les administrateurs de serveur. Administrateurs de Services d’analyse sont les administrateurs de serveur de base de données avec des droits pour des tâches d’administration de base de données courantes telles que l’ajout et la suppression des bases de données et la gestion des utilisateurs. Par défaut, l’utilisateur qui crée le serveur dans Azure portal est automatiquement ajouté en tant qu’administrateur Analysis Services.

Vous devez également savoir :

-   De Windows Live ID n’est pas un type d’identité pris en charge pour Azure Analysis Services.  
-   Administrateurs de Services d’analyse doivent être des utilisateurs de Azure Active Directory valides.
-   Si la création d’un serveur de Services d’analyse Azure via le Gestionnaire de ressources Azure des modèles, administrateurs de Services d’analyse prend un tableau JSON d’utilisateurs qui doivent être ajoutés en tant qu’administrateurs.

Administrateurs de Services d’analyse peuvent être différents des administrateurs de ressources Azure, qui peuvent gérer des ressources pour les abonnements d’Azure. Cela permet de conserver la compatibilité avec l’existant XMLA et TSML gérer les comportements dans Analysis Services et pour permettre de vous permettent de répartir les tâches entre l’analyse et de gestion de ressources Azure Services de gestion de base de données.

Pour afficher tous les rôles et accéder aux types pour votre ressource Azure Analysis Services, utilisez le contrôle d’accès (IAM) sur la lame de contrôle.

## <a name="database-users"></a>Utilisateurs de base de données
Utilisateurs de base de données de modèle Azure Analysis Services doivent être dans Azure Active Directory. Les noms d’utilisateurs spécifiés pour la base de données model doivent être par adresse de messagerie d’organisation ou UPN. Cela est différent de bases de données sur site modèle qui prend en charge les utilisateurs par des noms d’utilisateur de domaine Windows.

Vous pouvez ajouter des utilisateurs à l’aide [des affectations de rôle dans Azure Active Directory](../active-directory/role-based-access-control-configure.md) ou à l’aide du [Langage de script de modèle sous forme de tableau](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) dans SQL Server Management Studio.

**Exemple de script TMSL**

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="enable-azure-active-directory-authentication"></a>Activer l’authentification Azure Active Directory
Pour activer la fonctionnalité d’authentification Azure Active Directory pour SSMS dans le Registre, créez un fichier texte nommé EnableAAD.reg, puis copiez et collez le texte suivant :


```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

Enregistrez et exécutez le fichier.



## <a name="next-steps"></a>Étapes suivantes
Si vous ne l’avez pas déjà déployé un modèle sous forme de tableau vers le nouveau serveur, est un bon moment. Pour plus d’informations, voir [déploiement d’Azure Analysis Services](analysis-services-deploy.md).

Si vous avez déployé un modèle à votre serveur, vous êtes prêt à vous connecter à l’aide d’un client ou un navigateur. Pour plus d’informations, consultez [obtenir des données de serveur Azure Analysis Services](analysis-services-connect.md).
