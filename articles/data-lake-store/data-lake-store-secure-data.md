<properties 
   pageTitle="Sécurisation des données stockées dans le magasin de LAC de données Azure | Microsoft Azure" 
   description="Apprenez à sécuriser les données dans le magasin de LAC de données Azure à l’aide de groupes et listes de contrôle d’accès" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/29/2016"
   ms.author="nitinme"/>

# <a name="securing-data-stored-in-azure-data-lake-store"></a>Sécurisation des données stockées dans le magasin de LAC de données Azure

Sécurisation des données dans le magasin de LAC de données Azure est une approche à trois étapes.

1. Commencez par créer des groupes de sécurité dans Azure Active Directory (DAS). Ces groupes de sécurité sont utilisés pour implémenter le contrôle d’accès basé sur les rôles (RBAC) dans Azure Portal. Pour plus d’informations, consultez [Contrôle d’accès par rôle dans Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Affecter les groupes de sécurité DAS pour le compte Azure données lac. Contrôle l’accès au compte de banque de données lac dans les opérations de portail et de gestion à partir du portail ou d’une API.

3. Affecter les groupes de sécurité DAS comme accès à des listes de contrôle (ACL) sur le système de fichiers de banque de données lac.

4. En outre, vous pouvez également définir une plage d’adresses IP pour les clients qui peuvent accéder aux données dans le magasin de données lac.

Cet article fournit des instructions sur l’utilisation du portail Azure pour effectuer les tâches ci-dessus. Pour plus d’informations sur la banque de données lac implémente la sécurité au niveau du compte et des données, consultez [sécurité Azure magasin de données lac](data-lake-store-security-overview.md). Pour approfondi d’informations sur comment les ACL est implémentés dans le magasin de LAC de données Azure, consultez [Vue d’ensemble du contrôle d’accès dans le magasin de données lac](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Compte d’une banque de LAC de données Azure**. Pour obtenir des instructions sur la façon d’en créer un, reportez-vous à la section [mise en route de magasin de LAC de données Azure](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Créer des groupes de sécurité dans Active Directory de Azure

Pour obtenir des instructions sur la création de groupes de sécurité DAS et comment ajouter des utilisateurs au groupe, consultez [Gestion des groupes de sécurité dans Active Directory de Azure](../active-directory/active-directory-accessmanagement-manage-groups.md).

## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Affecter des utilisateurs ou des groupes de sécurité à des comptes de banque de LAC de données Azure

Lorsque vous affectez des utilisateurs ou des groupes de sécurité à des comptes de banque de LAC de données Azure, vous contrôlez l’accès aux opérations de gestion sur le compte à l’aide des API du Gestionnaire de ressources Azure et Azure portal. 

1. Ouvrez un compte Azure données lac. Dans le volet gauche, cliquez sur **Parcourir**, cliquez sur la **Banque de données lac**et puis, à partir de la blade de magasin de données lac, cliquez sur le nom du compte auquel vous souhaitez affecter un utilisateur ou groupe de sécurité.

2. Dans la blade de compte de banque de données lac, cliquez sur **paramètres**. À partir de la blade de **paramètres** , cliquez sur **utilisateurs**.

    ![Affecter le groupe de sécurité pour le compte de la banque de LAC de données Azure] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Affecter le groupe de sécurité pour le compte de la banque de LAC de données Azure")

3. La lame de **utilisateur** par défaut répertorie le groupe **administrateurs de l’abonnement** en tant que propriétaire. 

    ![Ajouter des utilisateurs et des rôles] (./media/data-lake-store-secure-data/adl.add.group.roles.png "Ajouter des utilisateurs et des rôles")
 
    Il existe deux façons d’ajouter un groupe et affecter des rôles appropriés.

    * Ajouter un utilisateur/groupe au compte et ensuite affecter un rôle, ou
    * Ajouter un rôle et d’affecter les utilisateurs/groupes au rôle.

    Dans cette section, nous examinons la première approche, ajout d’un groupe et en attribuant des rôles. Vous pouvez exécuter une procédure similaire pour sélectionnez un rôle, puis affecter à ce rôle.
    
4. De la lame **d’utilisateurs** , cliquez sur **Ajouter** pour ouvrir la blade **d’Ajouter l’accès** . De la lame **d’Ajouter l’accès** , cliquez sur **Sélectionner un rôle**et puis sélectionnez un rôle pour l’utilisateur ou le groupe.

     ![Ajout d’un rôle pour l’utilisateur] (./media/data-lake-store-secure-data/adl.add.user.1.png "Ajout d’un rôle pour l’utilisateur")

    Le **propriétaire** et le rôle de **contributeur** donnent accès aux diverses fonctions d’administration sur le compte de LAC de données. Pour les utilisateurs vont interagir avec les données dans le lac de données, vous pouvez les ajouter au rôle de **lecteur **. La portée de ces rôles est limitée aux opérations de gestion associées au compte de banque de LAC de données Azure.

    Pour les données opérations individuelles les autorisations définissent ce que peuvent faire les utilisateurs. Par conséquent, un utilisateur ayant un rôle de lecteur peut uniquement afficher les paramètres d’administration associés au compte mais peut potentiellement lire et écrire des données en fonction des autorisations de système de fichiers qui leur sont affectées. Autorisations de système de fichiers de banque de données lac décrits à [affecter le groupe de sécurité comme les ACL pour le système de fichiers de banque de LAC de données Azure](#filepermissions).



5. De la lame **d’Ajouter l’accès** , cliquez sur **Ajouter des utilisateurs** pour ouvrir la blade **d’Ajouter des utilisateurs** . Dans cette blade, recherchez le groupe de sécurité que vous avez créé précédemment dans Azure Active Directory. Si vous avez un grand nombre de groupes à rechercher à partir de, vous pouvez utiliser la zone de texte en haut pour filtrer sur le nom du groupe. Cliquez sur **Sélectionner**.

    ![Ajouter un groupe de sécurité] (./media/data-lake-store-secure-data/adl.add.user.2.png "Ajouter un groupe de sécurité")

    Si vous souhaitez ajouter un utilisateur ou le groupe qui n’est pas répertorié, vous pouvez les inviter à l’aide de l’icône **inviter** et spécifier l’adresse de messagerie de l’utilisateur ou le groupe.

6. Cliquez sur **OK**. Vous devez voir le groupe de sécurité ajouté comme illustré ci-dessous.

    ![Groupe de sécurité ajouté] (./media/data-lake-store-secure-data/adl.add.user.3.png "Groupe de sécurité ajouté")

7. Votre groupe de sécurité des utilisateurs a maintenant accès au compte de banque de LAC de données Azure. Si vous souhaitez fournir un accès à des utilisateurs spécifiques, vous pouvez les ajouter au groupe de sécurité. De même, si vous souhaitez révoquer l’accès d’un utilisateur, vous pouvez les supprimer du groupe de sécurité. Vous pouvez également affecter plusieurs groupes de sécurité à un compte. 

## <a name="filepermissions"></a>Affecter des utilisateurs ou le groupe de sécurité comme les ACL pour le système de fichiers de banque de LAC de données Azure

En affectant des groupes de sécurité des utilisateurs pour le système de fichiers du lac de données Azure, vous définissez le contrôle d’accès sur les données stockées dans le magasin de LAC de données Azure.

1. Dans la blade de compte de banque de données lac, cliquez sur **Explorateur de données**.

    ![Créer des répertoires dans le compte de la banque de données lac] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Créer des répertoires de données lac compte")

2. De la lame **d’Explorateur de données** , cliquez sur le fichier ou le dossier pour lequel vous souhaitez configurer la liste ACL, puis cliquez sur **accès**. Pour affecter des ACL dans un fichier, vous devez cliquer sur **accès** à partir de la blade **d’Aperçu du fichier** .

    ![Définir les ACL sur le système de fichiers de données lac] (./media/data-lake-store-secure-data/adl.acl.1.png "Définir les ACL sur le système de fichiers de données lac")

3. La blade **d’accès** répertorie l’accès standard et personnalisés déjà affecté à la racine. Cliquez sur l’icône **Ajouter** pour ajouter les ACL personnaliser le niveau.

    ![Accès à la liste standard et personnalisé] (./media/data-lake-store-secure-data/adl.acl.2.png "Accès à la liste standard et personnalisé")

    * **Accéder à standard** est à l’accès de style UNIX, où vous spécifiez lire, écrire, exécuter (rwx) pour trois classes d’utilisateurs distincts : propriétaire, groupe et autres.
    * **L’accès personnalisé** correspond aux ACL POSIX qui vous permet de définir des autorisations pour des utilisateurs spécifiques désignés ou des groupes et non seulement du fichier propriétaire ou groupe. 
    
    Pour plus d’informations, consultez [Très ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Pour plus d’informations sur la façon dont les ACL est implémentés dans le magasin de données lac, consultez [Contrôle d’accès au magasin de données lac](data-lake-store-access-control.md).

4. Cliquez sur l’icône **Ajouter** pour ouvrir la blade **D’ajout d’accès personnalisé** . Dans cette carte, cliquez sur **Sélectionner un utilisateur ou un groupe**et de lame de **Sélectionner un utilisateur ou un groupe** , recherchez le groupe de sécurité que vous avez créé précédemment dans Azure Active Directory. Si vous avez un grand nombre de groupes à rechercher à partir de, vous pouvez utiliser la zone de texte en haut pour filtrer sur le nom du groupe. Cliquez sur le groupe que vous souhaitez ajouter, puis sur **Sélectionner**.

    ![Ajouter un groupe] (./media/data-lake-store-secure-data/adl.acl.3.png "Ajouter un groupe")

5. Cliquez sur **Sélectionner les autorisations**, sélectionnez les autorisations et si vous souhaitez attribuer les autorisations par défaut ACL, accéder aux ACL, ou les deux. Cliquez sur **OK**.

    ![Affecter des autorisations à un groupe] (./media/data-lake-store-secure-data/adl.acl.4.png "Affecter des autorisations à un groupe")

    Pour plus d’informations sur les autorisations dans la banque de données LAC et les ACL par défaut d’accès voir [Contrôle d’accès au magasin de données lac](data-lake-store-access-control.md).


6. De la lame **D’ajout d’accès personnalisé** , cliquez sur **OK**. Le groupe récemment créé, avec les autorisations associées, sera désormais répertorié dans la blade **d’accès** .

    ![Affecter des autorisations à un groupe] (./media/data-lake-store-secure-data/adl.acl.5.png "Affecter des autorisations à un groupe")

    > [AZURE.IMPORTANT] Dans la version actuelle, vous ne pouvez avoir que 9 entrées sous **L’accès personnalisé**. Si vous souhaitez ajouter des utilisateurs plus de 9, vous devez créer des groupes de sécurité, d’ajouter des utilisateurs aux groupes de sécurité, ajoutez fournir l’accès à ces groupes de sécurité pour le compte de la banque de données lac.

7. Si nécessaire, vous pouvez également modifier les autorisations d’accès après avoir ajouté le groupe. Activez ou désactivez la case à cocher pour chaque type d’autorisation (lecture, écriture, exécution) en fonction de si vous souhaitez supprimer ou affecter cette autorisation au groupe de sécurité. Cliquez sur **Enregistrer** pour enregistrer les modifications ou sur **Annuler** pour annuler les modifications.

## <a name="set-ip-address-range-for-data-access"></a>Définir la plage d’adresses IP pour l’accès aux données

Magasin de LAC de données Azure permet de mieux verrouiller l’accès à votre magasin de données au niveau du réseau. Vous pouvez activer le pare-feu, spécifier une adresse IP ou définir une plage d’adresses IP pour les clients de confiance. Une fois activé, seuls les clients qui ont des adresses IP dans la plage définie peuvent se connecter à la banque.

![Paramètres de pare-feu et accès IP] (./media/data-lake-store-secure-data/firewall-ip-access.png "Paramètres de pare-feu et l’adresse IP")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Supprimer des groupes de sécurité d’un compte de banque de LAC de données Azure

Lorsque vous supprimez des groupes de sécurité à partir de comptes de la banque de LAC de données Azure, vous modifiez uniquement l’accès aux opérations de gestion sur le compte en utilisant le portail Azure et l’API du Gestionnaire de ressources Azure.

1. Dans la blade de compte de banque de données lac, cliquez sur **paramètres**. À partir de la blade de **paramètres** , cliquez sur **utilisateurs**.

    ![Affecter le groupe de sécurité pour le compte du lac de données Azure] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Affecter le groupe de sécurité pour le compte du lac de données Azure")

2. De la lame **d’utilisateurs** , cliquez sur le groupe de sécurité que vous souhaitez supprimer.

    ![Groupe de sécurité à supprimer] (./media/data-lake-store-secure-data/adl.add.user.3.png "Groupe de sécurité à supprimer")

3. De la lame pour le groupe de sécurité, cliquez sur **Supprimer**.

    ![Groupe de sécurité supprimé] (./media/data-lake-store-secure-data/adl.remove.group.png "Groupe de sécurité supprimé")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Supprimer le groupe de sécurité ACL de système de fichiers de banque de LAC de données Azure

Lorsque vous supprimez des groupes de sécurité ACL de système de fichiers de banque de LAC de données Azure, vous modifiez l’accès aux données du magasin de données lac.

1. Dans la blade de compte de banque de données lac, cliquez sur **Explorateur de données**.

    ![Créer des répertoires de données lac compte] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Créer des répertoires de données lac compte")

2. Dans la blade **d’Explorateur de données** , cliquez sur le fichier ou le dossier pour lequel vous souhaitez supprimer de la liste ACL et de lame de votre compte, cliquez sur l’icône **d’accès** . Pour supprimer les ACL pour un fichier, vous devez cliquer sur **accès** à partir de la blade **d’Aperçu du fichier** .

    ![Définir les ACL sur le système de fichiers de données lac] (./media/data-lake-store-secure-data/adl.acl.1.png "Définir les ACL sur le système de fichiers de données lac")

3. De la lame **d’accès** , à partir de la section **Personnalisée accès** , cliquez sur le groupe de sécurité que vous souhaitez supprimer. Dans la lame **L’accès personnalisé** , cliquez sur **Supprimer** et puis cliquez sur **OK**.

    ![Affecter des autorisations à un groupe] (./media/data-lake-store-secure-data/adl.remove.acl.png "Affecter des autorisations à un groupe")


## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble Azure lac de magasin de données](data-lake-store-overview.md)
- [Copier des données d’objets BLOB de stockage Azure au magasin de données lac](data-lake-store-copy-data-azure-storage-blob.md)
- [Utiliser des données Azure lac Analytique avec le magasin de données lac](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Mise en route de magasin de LAC de données à l’aide de PowerShell](data-lake-store-get-started-powershell.md)
- [Mise en route de magasin lac de données à l’aide du Kit de développement .NET](data-lake-store-get-started-net-sdk.md)
- [Journaux de diagnostic d’accès pour le magasin de données lac](data-lake-store-diagnostic-logs.md)
