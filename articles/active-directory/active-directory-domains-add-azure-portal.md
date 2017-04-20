<properties
    pageTitle="Ajoutez votre nom de domaine personnalisé pour aperçu d’Azure Active Directory | Microsoft Azure"
    description="Comment ajouter les noms de domaine de votre société à Azure Active Directory et comment vérifier le nom de domaine."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="curtand"/>

# <a name="add-a-custom-domain-name-to-azure-active-directory-preview"></a>Ajouter un nom de domaine personnalisé à Aperçu d’Azure Active Directory

> [AZURE.SELECTOR]
- [Azure portal](active-directory-domains-add-azure-portal.md)
- [Azure portal classique](active-directory-add-domain.md)

Vous avez un ou plusieurs noms de domaine que votre organisation utilise pour faire des affaires, et que vos utilisateurs se connecter à votre réseau d’entreprise à l’aide de votre nom de domaine d’entreprise. À l’aide d’Aperçu d’Azure Active Directory (AD Azure), vous pouvez ajouter le nom de votre domaine d’entreprise à Azure annonce également. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Cela vous permet d’assigner des noms d’utilisateur dans le répertoire qui sont familiers aux utilisateurs, telles que ‘alice@contoso.com.’ le processus est simple :

1. Ajoutez le nom de domaine personnalisé à votre répertoire
2. Ajouter une entrée DNS pour le nom de domaine dans le Registre des noms de domaine
3. Vérifiez le nom de domaine personnalisé dans Azure AD

## <a name="how-do-i-add-a-domain-name"></a>Comment pour ajouter un nom de domaine ?

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez des **services supplémentaires**, entrez **Azure Active Directory** dans la zone de texte et puis sélectionnez **entrée**.

    ![Gestion des utilisateurs ouverture](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Sur la lame ***nom du répertoire*** , sélectionnez **les noms de domaine**.

4. Sur la lame ** *nom du répertoire* : noms de domaine** , sélectionnez la commande **Ajouter** .

  ![La commande Ajouter la sélection](./media/active-directory-domains-add-azure-portal/add-command.png)

5. Sur la lame de **nom de domaine** , entrez le nom de votre domaine personnalisé dans la zone, par exemple, « contoso.com » et sélectionnez **Ajouter un domaine**. Veillez à inclure le .com, .net ou une autre extension de niveau supérieur.

6. Sur la lame ***domainname*** (autrement dit, la lame qui s’ouvre avec votre nouveau nom de domaine dans le titre), obtenir les informations DNS que AD Azure utilisera pour vérifier que votre organisation possède le nom de domaine personnalisé.

  ![obtenir des informations d’entrée DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Maintenant que vous avez ajouté le nom de domaine, Active Directory Azure doit vérifier que votre organisation possède le nom de domaine. Avant que l’annonce Azure peut effectuer cette vérification, vous devez ajouter une entrée DNS dans le fichier de zone DNS pour le nom de domaine. Cette tâche est effectuée sur le site Web pour le Registre des noms de domaine pour le nom de domaine.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Ajoutez l’entrée DNS dans le Registre des noms de domaine pour le domaine

L’étape suivante pour utiliser votre nom de domaine personnalisé avec Azure AD est mise à jour le fichier de zone DNS pour le domaine. Cela permet d’Azure AD vérifier que votre organisation possède le nom de domaine personnalisé.

1.  Connectez-vous pour le Registre des noms de domaine pour le domaine. Si vous n’avez pas accès à l’entrée DNS de mettre à jour, demandez à la personne ou l’équipe qui a accès à l’étape 2 et pour vous avertir lorsqu’il est terminé.

2.  Mettre à jour le fichier de zone DNS pour le domaine en ajoutant l’entrée DNS fournie par AD Azure. Cette entrée DNS permet d’Azure AD vérifier que vous possédez du domaine. L’entrée DNS ne change pas les comportements, tels que le routage de courrier ou l’hébergement web.

Pour de l’aide avec cet ajout de l’entrée DNS, lire les [Instructions pour l’ajout d’une entrée DNS sur les bureaux d’enregistrement DNS les plus courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Vérifiez le nom de domaine avec Active Directory Azure

Une fois que vous avez ajouté l’entrée DNS, vous êtes prêt à vérifier le nom de domaine avec Active Directory Azure.

Un nom de domaine peut être vérifié qu’une fois que les enregistrements DNS ont été propagées. Cette propagation souvent ne prend que quelques secondes, mais il peut parfois prendre une heure ou plus. Si la vérification ne fonctionne pas la première fois, réessayez ultérieurement.

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez **Parcourir**et entrez la gestion des utilisateurs dans la zone de texte puis sélectionnez **entrée**.

    ![Gestion des utilisateurs ouverture](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Sur la blade de **gestion de l’utilisateur - noms de domaine** , sélectionnez le nom de domaine non vérifié que vous souhaitez vérifier.

4. Sur la lame ***domainname*** (autrement dit, la lame qui s’ouvre avec votre nouveau nom de domaine dans le titre), sélectionnez **Vérifier** pour terminer la vérification.

Maintenant, vous pouvez [affecter des noms d’utilisateur qui incluent le nom de votre domaine personnalisé](active-directory-users-create-azure-portal.md).

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous ne pouvez pas vérifier un nom de domaine personnalisé, essayez ce qui suit. Nous allons commencer par les plus courants et fonctionne sur les moins courantes.

1.  **Patientez une heure**. Enregistrements DNS ont besoin de se propager avant AD Azure peut vérifier le domaine. Cette opération peut prendre une heure ou plus.

2.  **Assurez-vous que l’enregistrement DNS a été saisie, et qu’il est correct**. Effectuez cette étape sur le site Web pour le Registre des noms de domaine pour le domaine. Azure AD ne peut pas vérifier le nom de domaine si l’entrée DNS n’est pas présente dans le fichier de zone DNS, ou s’il n’est pas une correspondance exacte avec l’entrée DNS cette publicité Azure vous a fourni. Si vous n’avez pas accès à mettre à jour les enregistrements DNS pour le domaine dans le Registre des noms de domaine, partager l’entrée DNS avec la personne ou l’équipe de votre organisation qui possède cet accès et demandez-lui d’ajouter l’entrée DNS.

3.  **Supprimer le nom de domaine à partir d’un autre répertoire dans Azure AD**. Un nom de domaine peut être vérifié dans uniquement un seul répertoire. Si un nom de domaine a été préalablement vérifié dans un autre répertoire, il doit être supprimé il avant qu’il peut être vérifié dans le nouveau répertoire. Pour en savoir plus sur la suppression de noms de domaine, lire des [noms de domaine personnalisé de gérer](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Ajouter d’autres noms de domaine personnalisé

Si votre organisation utilise plusieurs noms de domaine personnalisé, par exemple « contoso.com » et « contosobank.com », vous pouvez les ajouter au maximum 900 des noms de domaine. Utilisez les mêmes étapes dans cet article pour ajouter chacun de vos noms de domaine.

## <a name="next-steps"></a>Étapes suivantes

[Gérer les noms de domaine personnalisé](active-directory-domains-manage-azure-portal.md)
