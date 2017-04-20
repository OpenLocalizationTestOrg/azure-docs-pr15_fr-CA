<properties
   pageTitle="Mise en route avec les modèles de privé | Microsoft Azure"
   description="Ajouter, gérer et partager vos modèles privées en utilisant le portail Azure, Azure CLI ou PowerShell."
   services="marketplace-customer"
   documentationCenter=""
   authors="VybavaRamadoss"
   manager="asimm"
   editor=""
   tags="marketplace, azure-resource-manager"
   keywords=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="vybavar"/>

# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Mise en route des modèles privés sur le portail Azure

Un modèle de [Gestionnaire de ressources Azure](../resource-group-authoring-templates.md) est un modèle déclaratif utilisé pour définir votre déploiement. Vous pouvez définir les ressources pour déployer une solution et spécifier des paramètres et des variables qui permettent d’entrer des valeurs pour les différents environnements. Le modèle est composé de JSON et des expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement.

Vous pouvez utiliser la nouvelle fonctionnalité de **modèles** dans le [Portail Azure](https://portal.azure.com) avec le fournisseur de ressources de **Microsoft.Gallery** en tant qu’extension de [Azure Marketplace](https://azure.microsoft.com/marketplace/) pour permettre aux utilisateurs de créer, de gérer et de déployer des modèles de privé à partir d’une bibliothèque personnelle.

Ce document vous guide à travers l’ajout, de gestion et de partage privé **modèle** via le portail d’Azure.

## <a name="guidance"></a>Conseils

Les suggestions suivantes vous aideront à tirer pleinement parti des **modèles** lorsque vous travaillez avec vos solutions :

- Un **modèle** est une ressource d’encapsulation qui contient un modèle de gestionnaire de ressources et de métadonnées supplémentaires. Il se comporte de manière très similaire un élément sur le marché. La principale différence est qu’il s’agit d’un élément privé et non les éléments de marché publics.
- La bibliothèque de **modèles** fonctionne bien pour les utilisateurs qui ont besoin pour personnaliser leurs déploiements.
- **Les modèles** fonctionnent bien pour les utilisateurs qui ont besoin d’un simple référentiel dans Azure.
- Démarrez avec un modèle de gestionnaire de ressources existant. Rechercher des modèles dans [github](https://github.com/Azure/azure-quickstart-templates) ou de [l’exportation du modèle](../resource-manager-export-template.md) à partir d’un groupe de ressources existant.
- **Les modèles** sont liés à l’utilisateur qui les publie. Nom de l’éditeur est visible à toute personne ayant un accès en lecture à celui-ci.
- **Les modèles** sont des ressources du Gestionnaire de ressources et ne peut pas être renommé une fois publié.

## <a name="add-a-template-resource"></a>Ajouter une ressource de modèle

Il existe deux façons de créer une ressource de **modèle** dans le portail Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Méthode 1 : Créer une nouvelle ressource de modèle à partir d’un groupe de ressources en cours d’exécution

1. Accédez à un groupe de ressources existant sur le portail d’Azure. Dans **paramètres**, sélectionnez **Exporter le modèle** .
2. Une fois le modèle de gestionnaire de ressources est exporté, utilisez le bouton **Enregistrer le modèle** pour l’enregistrer dans le référentiel de **modèles** . Rechercher des détails complets pour le modèle d’exportation [ici](../resource-manager-export-template.md).
<br /><br />
![Exportation de groupe de ressources](media/rg-export-portal1.PNG)  <br />

3. Cliquez sur le bouton de commande **Enregistrer dans le modèle** .
<br /><br />

4. Entrez les informations suivantes :

    - Nom – nom de l’objet modèle (Remarque : il s’agit d’un nom de gestionnaire de ressources Azure en fonction. Toutes les restrictions d’affectation de noms s’appliquent et il ne peut pas être modifié une fois créé).
    - Description – il s’agit d’un résumé rapide sur le modèle.

    ![Enregistrer le modèle](media/save-template-portal1.PNG)  <br />

5. Cliquez sur **Enregistrer**.

    > [AZURE.NOTE] La lame du modèle exportation affiche des notifications lorsque le modèle de gestionnaire de ressources exporté comporte des erreurs, mais vous serez toujours en mesure d’enregistrer ce modèle de gestionnaire de ressources pour les modèles. Assurez-vous que vous vérifiez et résoudre des problèmes de modèle de n’importe quel gestionnaire de ressources avant de redéployer le modèle exporté de gestionnaire de ressources.

### <a name="b-method-2--add-a-new-template-resource-from-browse"></a>B. Méthode 2 : Ajouter une nouvelle ressource de modèle de navigation

Vous pouvez également ajouter un nouveau **modèle** en utilisant le + ajouter le bouton de commande dans **Parcourir > modèles**. Vous devez fournir le nom, la Description et le modèle du Gestionnaire de ressources JSON.

![Ajouter un modèle](media/add-template-portal1.PNG)  <br />

> [AZURE.NOTE] Microsoft.Gallery est un client fournisseur de ressources Azure. La ressource de modèle est liée à l’utilisateur qui l’a créé. Il n’est pas lié à un abonnement spécifique. Un abonnement doit être choisi lorsque vous déployez un modèle.

## <a name="view-template-resources"></a>Afficher les ressources de modèle

Tous les **modèles** disponibles peuvent être visualisées à **Parcourir > modèles**. Cela inclut les **modèles** que vous avez créés ainsi que de ceux qui ont été partagés avec vous, avec différents niveaux d’autorisations. Plus de détails dans la section de [contrôle d’accès](#access-control-for-a-tenant-resource-provider) ci-dessous.

![Modèle d’affichage](media/view-template-portal1.PNG)  <br />

Vous pouvez afficher les détails d’un **modèle** en cliquant sur un élément dans la liste.

![Modèle d’affichage](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Modifier une ressource de modèle

Vous pouvez lancer le flux de modification d’un **modèle** en cliquant avec le bouton droit sur l’élément sur la liste de navigation ou en cliquant sur le bouton de la commande modifier.

![Modifier le modèle](media/edit-template-portal1a.PNG)  <br />

Vous pouvez modifier la description ou le texte du modèle de gestionnaire de ressources. Vous ne pouvez pas modifier le nom car il s’agit d’un nom de ressource de gestionnaire de ressources. Lorsque vous modifiez le modèle de gestionnaire de ressources JSON valide pour vous assurer qu’il est JSON valide. Cliquez sur **OK** , puis sur **Enregistrer** pour enregistrer le modèle mis à jour.

![Modifier le modèle](media/edit-template-portal2a.PNG)  <br />

Une fois le **modèle** enregistré, vous verrez un message de confirmation.

![Modifier le modèle](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Déployer un modèle de ressource

Vous pouvez déployer un **modèle** que vous avez des autorisations de **lecture** sur. Le flux de déploiement lance la lame de déploiement Azure modèle standard. Remplissez les valeurs pour les paramètres de modèle de gestionnaire de ressources effectuer le déploiement.

![Déployer le modèle](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Partager une ressource de modèle

Une ressource de **modèle** peut être partagée avec d’autres utilisateurs. Partage se comporte de la même façon pour [l’attribution de rôle pour n’importe quelle ressource dans Azure](../active-directory/role-based-access-control-configure.md). Le propriétaire de **modèle** fournit des autorisations aux autres utilisateurs qui peuvent interagir avec une ressource de modèle. La personne ou le groupe de personnes que vous partagez le **modèle** avec sera en mesure de voir le modèle du Gestionnaire de ressources et ses propriétés de la galerie.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Contrôle d’accès pour les ressources Microsoft.Gallery

Rôle | Autorisations
---|----
Propriétaire | Permet un contrôle total sur la ressource de modèle, y compris de la part de
Lecteur | Permet de lire et Execute(Deploy) sur la ressource de modèle
Collaborateur | Permet de modifier et supprimer l’autorisation sur la ressource de modèle. Utilisateur ne peuvent pas partager le modèle avec d’autres personnes

Sélectionnez **partager** sur l’élément de navigation en cliquant ou sur la lame de l’affichage d’un élément spécifique. Cette action lance une expérience de partage.

![Modèle de partage](media/share-template-portal1a.png)  <br />

 Vous pouvez maintenant choisir un rôle et un utilisateur ou un groupe pour fournir l’accès à un **modèle**de particulier. Les rôles disponibles sont propriétaire, lecteur et collaborateur. Plus de détails dans la section de [contrôle d’accès](#access-control-for-a-tenant-resource-provider) ci-dessus.

![Modèle de partage](media/share-template-portal2b.png)  <br />

![Modèle de partage](media/share-template-portal3b.png)  <br />

Cliquez sur **Sélectionner** et **Ok**. Vous pouvez maintenant voir les utilisateurs ou les groupes que vous avez ajouté à la ressource.

![Modèle de partage](media/share-template-portal4b.png)  <br />

> [AZURE.NOTE] Un modèle peut uniquement être partagé avec les utilisateurs et les groupes dans le même locataire Azure Active Directory. Si vous partagez un modèle avec une adresse de messagerie qui n’est pas votre client, une invitation sera envoyée demandant à l’utilisateur de joindre le client en tant qu’invité.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles du Gestionnaire de ressources, consultez [Création de modèles](../resource-group-authoring-templates.md)
- Pour comprendre les fonctions que vous pouvez utiliser dans un modèle de gestionnaire de ressources, reportez-vous à la section [fonctions de modèle](../resource-group-template-functions.md)
- Pour obtenir des conseils sur la conception des modèles, consultez [méthodes conseillées pour la conception de modèles de gestionnaire de ressources Azure](../best-practices-resource-manager-design-templates.md)
