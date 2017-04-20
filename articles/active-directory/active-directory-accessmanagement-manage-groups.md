<properties
    pageTitle="Gestion des groupes dans Active Directory de Azure | Microsoft Azure"
    description="Comment faire pour créer et gérer des groupes pour gérer les utilisateurs Azure à l’aide d’Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/29/2016"
    ms.author="curtand"/>


# <a name="managing-groups-in-azure-active-directory"></a>Gestion des groupes dans Active Directory de Azure

> [AZURE.SELECTOR]
- [Azure portal](active-directory-groups-create-azure-portal.md)
- [Azure portal classique](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)


L’une des fonctionnalités de gestion des utilisateurs Azure Active Directory (AD Azure) est la capacité de créer des groupes d’utilisateurs. Vous utilisez un groupe à effectuer des tâches de gestion telles que l’assignation de licences ou d’autorisations à un nombre d’utilisateurs à la fois. Vous pouvez également utiliser des groupes pour affecter l’autorisation d’accès à

- Ressources telles que les objets de l’annuaire
- Ressources externes pour le répertoire telles que les applications SaaS, services Azure, les sites SharePoint, ou les ressources sur site

En outre, un propriétaire de la ressource peut également affecter des accès à une ressource à un groupe d’annonces de Azure appartenant à quelqu'un d’autre. Cette affectation accorde les membres de ce groupe l’accès à la ressource. Ensuite, le propriétaire du groupe gère l’appartenance dans le groupe. Effectivement, le propriétaire de la ressource délègue au propriétaire du groupe l’autorisation d’affecter des utilisateurs à leurs ressources.

## <a name="how-do-i-create-a-group"></a>Comment pour créer un groupe ?

Selon les services auxquels votre organisation est abonné, vous pouvez créer un groupe à l’aide d’une des opérations suivantes :
- le portail classique Azure
- le portail de compte Office 365
- le portail de compte de Windows Intune

Nous allons décrire les tâches comme effectuée dans Azure portal classique. Pour plus d’informations sur l’utilisation de portails de non-Azure pour gérer votre annuaire AD Azure, consultez [administration de votre annuaire AD Azure](active-directory-administer.md).

1. Dans [Azure portal classique](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis sélectionnez le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** .

3. Sélectionnez **Ajouter un groupe**.

4. Dans la fenêtre **Ajouter un groupe** , spécifiez le nom et la description d’un groupe.


## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Comment ajouter ou supprimer des utilisateurs dans un groupe de sécurité ?

**Pour ajouter un utilisateur à un groupe**

1. Dans [Azure portal classique](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis sélectionnez le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** .

3. Ouvrez le groupe auquel vous souhaitez ajouter des membres. Cliquez sur l’onglet **membres** du groupe sélectionné si elle n'affiche pas déjà.

4. Sélectionnez **Ajouter des membres**.

5. Dans la page **Ajouter des membres** , sélectionnez le nom de l’utilisateur ou un groupe que vous souhaitez ajouter en tant que membre de ce groupe. Assurez-vous que ce nom est ajouté au volet **sélectionné** .


**Pour supprimer un utilisateur d’un groupe**

1. Dans [Azure portal classique](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis sélectionnez le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** .

3. Ouvrez le groupe à partir duquel vous souhaitez supprimer des membres.

4. Sélectionnez l’onglet **membres** , sélectionnez le nom du membre que vous souhaitez supprimer de ce groupe, puis cliquez sur **Supprimer**.

6. À l’invite, confirmez que vous voulez supprimer ce membre du groupe.


## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>Comment puis-je gérer dynamiquement l’appartenance à un groupe ?

Dans AD Azure, vous pouvez très facilement définir une règle simple pour déterminer les utilisateurs qui sont membres du groupe. Une règle simple est celui qui ne fait qu’une seule comparaison. Par exemple, si un groupe est affecté à une application de SaaS, vous pouvez définir une règle pour ajouter les utilisateurs qui disposent d’une fonction de « Commercial ». Cette règle accorde ensuite l’accès à cette application SaaS à tous les utilisateurs qui occupent cette fonction dans votre répertoire.

Lorsque tous les attributs d’un changement d’utilisateur, le système évalue toutes les règles de groupe dynamique dans un répertoire pour voir si la modification de l’attribut de l’utilisateur déclencheraient tout groupe ajoute ou supprime. Si un utilisateur correspond à une règle sur un groupe, ils sont ajoutés en tant que membre de ce groupe. Si elles ne répondent plus à la règle d’un groupe qu’ils sont un membre, ils sont supprimés en tant que membres de ce groupe.

> [AZURE.NOTE] Vous pouvez définir une règle d’appartenance dynamique sur les groupes de sécurité ou des groupes d’Office 365. Appartenances aux groupes imbriqués ne sont pas actuellement pris en charge pour l’affectation de groupe aux applications.
>
> Appartenances dynamiques pour les groupes nécessitent une licence Azure AD Premium à assigner à
>
> - L’administrateur qui gère la règle sur un groupe
> - Tous les membres du groupe

**Pour activer l’appartenance dynamique d’un groupe**

1. Dans [Azure portal classique](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis sélectionnez le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** et ouvrez le groupe que vous souhaitez modifier.

3. Sélectionnez l’onglet **configurer** et puis définissez **Activer l’appartenance dynamique** à **Oui**.

4. Permet de paramétrer une règle simple et unique pour le groupe contrôler l’appartenance dynamique pour les fonctions de ce groupe. Assurez-vous que le **Ajouter des utilisateurs où** option est sélectionnée, puis sélectionnez une propriété de l’utilisateur dans la liste (par exemple, département, jobTitle, etc.),

5. Ensuite, sélectionnez une condition (pas égal à, égal à, pas commence par, commence par, pas contient, Contains, ne correspondent pas, correspondance).

6. Spécifiez une valeur de comparaison pour la propriété de l’utilisateur sélectionné.

Pour savoir comment créer des règles *avancées* (règles qui peuvent contenir plusieurs comparaisons) pour l’appartenance au groupe dynamique, reportez-vous [à l’aide des attributs pour créer des règles avancées](active-directory-accessmanagement-groups-with-advanced-rules.md).

## <a name="additional-information"></a>Informations supplémentaires

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec groupes Azure Active Directory](active-directory-manage-groups.md)

* [Azure applets de commande de Active Directory pour configurer les paramètres de groupe](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)

* [Nouveautés d’Azure Active Directory ?](active-directory-whatis.md)

* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
