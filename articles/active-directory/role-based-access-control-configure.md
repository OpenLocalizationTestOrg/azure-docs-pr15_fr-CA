<properties
    pageTitle="Utilisez le contrôle d’accès basé sur les rôles dans le portail Azure | Microsoft Azure"
    description="Mise en route de la gestion de l’accès avec contrôle d’accès basée sur les rôles dans le portail Azure. Affectations de rôle permet d’affecter des autorisations à vos ressources."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="use-role-assignments-to-manage-access-to-your-azure-subscription-resources"></a>Affectations de rôle permet de gérer l’accès aux ressources de votre abonnement Azure

> [AZURE.SELECTOR]
- [Gérer l’accès par utilisateur ou par groupe](role-based-access-control-manage-assignments.md)
- [Gérer l’accès à la ressource](role-based-access-control-configure.md)

Azure contrôle d’accès basée sur les rôles (RBAC) permet la gestion d’accès à granularité fine pour Azure. L’utilisation de RBAC, vous pouvez accorder uniquement le montant d’accès que les utilisateurs ont besoin pour effectuer leur travail. Cet article vous aide à devenir opérationnel avec RBAC dans Azure portal. Si vous souhaitez plus de détails sur comment RBAC vous permet de gérer l’accès, consultez [contrôle d’accès basée sur les rôles](role-based-access-control-what-is.md).

## <a name="view-access"></a>Accès à l’affichage
Vous pouvez voir qui a accès à une ressource, un groupe de ressources ou un abonnement à partir de son principal blade dans [Azure portal](https://portal.azure.com). Par exemple, nous voulons voir qui a accès à l’un de nos groupes de ressources :

1. Sélectionnez les **groupes de ressources** dans la barre de navigation sur la gauche.  
    ![Groupes de ressources - icône](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Sélectionnez le nom du groupe de ressources à partir de la blade de **groupes de ressources** .
3. Sélectionnez **le contrôle d’accès (IAM)** dans le menu de gauche.  
4. La lame de contrôle d’accès répertorie tous les utilisateurs, les groupes et les applications qui ont accès au groupe de ressources.  

    ![Lame d’utilisateurs - vs héritées affecté la capture d’écran de l’accès](./media/role-based-access-control-configure/view-access.png)

Notez que certains utilisateurs ont été **affectés** y accéder tandis que d’autres **hérité** . L’accès est attribué spécifiquement pour le groupe de ressources ou hérité d’une affectation à l’abonnement de parent.

> [AZURE.NOTE] Co-administrateurs et administrateurs d’abonnement classique sont considérés comme les propriétaires de l’abonnement dans le nouveau modèle RBAC.


## <a name="add-access"></a>Ajout de l’accès
Vous accordez l’accès à partir de la ressource, le groupe de ressources ou l’abonnement qui est la portée de l’affectation de rôle.

1. Sélectionnez **Ajouter** sur la lame de contrôle d’accès.  
2. Sélectionnez le rôle que vous souhaitez affecter à partir de la blade de **Sélectionner un rôle** .
3. Dans le répertoire que vous souhaitez accorder l’accès à, sélectionnez l’utilisateur, le groupe ou l’application. Vous pouvez rechercher le répertoire avec des noms complets, les adresses e-mail et les identificateurs d’objet.  

    ![Ajouter des lames d’utilisateurs - capture d’écran de recherche](./media/role-based-access-control-configure/grant-access2.png)

4. Cliquez sur **OK** pour créer le devoir. La fenêtre contextuelle **d’Ajout utilisateur** suit la progression.  
    ![Barre de progression Ajout utilisateur - capture d’écran](./media/role-based-access-control-configure/addinguser_popup.png)

Après avoir ajouté avec succès une attribution de rôle, il apparaîtra sur la blade **d’utilisateurs** .

## <a name="remove-access"></a>Supprimer l’accès

1. Sélectionnez l’affectation de rôle sur la lame de contrôle d’accès.
2. Sélectionnez **Supprimer** dans la lame de détails d’affectation.  
3. Sélectionnez **Oui** pour confirmer la suppression.  
    ![Lame d’utilisateurs - supprimer à partir de la capture d’écran de rôle](./media/role-based-access-control-configure/remove-access1.png)

Affectations héritées ne peut pas être supprimées. Dans l’image ci-dessous, notez que le bouton Supprimer est grisé. Au lieu de cela, examinez les détails **Affecté à** . Consultez la ressource répertoriée pour supprimer l’affectation de rôle.

![Lame d’utilisateurs - accès héritées désactive supprimer bouton capture d’écran](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Autres outils de gestion des accès
Vous pouvez attribuer des rôles et gérer l’accès aux commandes Azure RBAC dans les outils de portail Azure.  Suivez les liens pour en savoir plus sur les conditions préalables et la mise en route avec les commandes Azure RBAC.

- [PowerShell Azure](role-based-access-control-manage-access-powershell.md)
- [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Étapes suivantes
- [Créer un rapport de l’historique de modification access](role-based-access-control-access-change-history-report.md)
- Consultez les [rôles intégrés de RBAC](role-based-access-built-in-roles.md)
- Définir vos propres [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md)
