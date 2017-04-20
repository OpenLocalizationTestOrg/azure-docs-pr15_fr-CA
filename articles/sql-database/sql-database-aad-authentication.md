<properties
   pageTitle="Se connecter à SQL de base de données ou de l’entrepôt de données SQL à l’aide de l’authentification Azure Active Directory | Microsoft Azure"
   description="Découvrez comment se connecter à la base de données de SQL à l’aide de l’authentification Active Directory Azure."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="connecting-to-sql-database-or-sql-data-warehouse-by-using-azure-active-directory-authentication"></a>Connexion de base de données SQL ou SQL Data Warehouse à l’aide de l’authentification Azure Active Directory

L’authentification Active Directory Azure est un mécanisme de connexion à la base de données SQL de Microsoft Azure et [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) à l’aide des identités dans Azure Active Directory (AD Azure). Avec l’authentification Azure Active Directory, vous pouvez gérer de façon centralisée l’identité des utilisateurs de la base de données et autres services de Microsoft dans un emplacement central. Gestion centrale des ID fournit un emplacement unique pour gérer les utilisateurs de base de données et simplifie la gestion des autorisations. Les avantages suivants :

- Il fournit une alternative à l’authentification SQL Server.
- Permet de bloquer la prolifération des identités des utilisateurs sur les serveurs de base de données.
- Permet la rotation de mot de passe dans un endroit unique
- Les clients peuvent gérer les autorisations de base de données à l’aide de groupes d’externes (DAS).
- Il peut éliminer le stockage des mots de passe en activant l’authentification intégrée de Windows et d’autres formes d’authentification pris en charge par Active Directory de Azure.
- Azure Active Directory utilise contenus de base de données utilisateurs de l’authentification pour authentifier les identités au niveau de la base de données.
- Azure Active Directory prend en charge l’authentification basée sur un jeton pour les applications se connectant à la base de données de SQL.
- L’authentification Active Directory Azure prend en charge l’ADFS (Fédération de domaine) ou l’authentification d’utilisateur/mot de passe natif pour un local Azure Active Directory sans synchronisation du domaine.  
- Azure Active Directory prend en charge les connexions à partir de SQL Server Management Studio qui utilisent l’authentification Active Directory universel, qui inclut plusieurs facteurs d’authentification (AMF).  AMF inclut l’authentification renforcée grâce à une gamme d’options de vérification facile, appel téléphonique, message texte, cartes à puce avec un code pin, ou la notification de l’application mobile. Pour plus d’informations, consultez [prise en charge de SSMS pour AMF de publicité Azure avec SQL de base de données et SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).

Les étapes de configuration incluent les procédures suivantes pour configurer et utiliser l’authentification Azure Active Directory.

1. Créer et remplir un Azure d’Active Directory.
2. Assurez-vous que votre base de données est V12 de base de données SQL Azure. (Non nécessaire pour l’entrepôt de données SQL).
3. Associer facultatif : Ou modifier active directory qui est actuellement associé à votre abonnement Azure.
4. Créer un administrateur Active Directory de Azure pour Azure SQL Server ou de [l’Entrepôt de données SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Configurez les ordinateurs clients.
6. Créer des utilisateurs de base de données contenus dans votre base de données mis en correspondance avec des identités AD Azure.
7. Se connecter à votre base de données à l’aide des identités AD Azure.


## <a name="trust-architecture"></a>Architecture de confiance

Le diagramme suivant de haut niveau résume l’architecture de la solution de l’utilisation de l’authentification Active Directory Azure avec la base de données de SQL Azure. Les mêmes concepts s’appliquent à l’entrepôt de données SQL. Pour prendre en charge de mot de passe utilisateur natif Azure Active Directory, uniquement la partie du nuage et le base de données SQL Azure AD/Azure est pris en compte. Pour prendre en charge l’authentification fédérée (ou utilisateur/mot de passe pour les informations d’identification de Windows), la communication avec ADFS bloc est requise. Les flèches indiquent les voies de communication.

![schéma d’authentification DAS][1]

Le diagramme suivant indique la fédération, approbation et relations d’hébergement qui autorisent un client à se connecter à une base de données, en envoyant un jeton. Le jeton est authentifié par une annonce Azure et approuvé par la base de données. Client 1 peut représenter Azure Active Directory avec les utilisateurs de natives ou Azure Active Directory avec les utilisateurs fédérés. Client 2 représente une solution possible, y compris les utilisateurs importés ; Dans cet exemple provenant d’un fédéré Azure Active Directory avec ADFS en cours de synchronisation avec Active Directory de Azure. Il est important de comprendre que l’accès à une base de données à l’aide de l’authentification Active Directory Azure requiert que l’abonnement d’hébergement est associé à l’Azure d’Active Directory. L’abonnement même doit être utilisé pour créer le SQL Server hébergeant la base de données de SQL Azure ou SQL Data Warehouse.

![relation d’abonnement][2]

## <a name="administrator-structure"></a>Structure de l’administrateur

Lorsque vous utilisez l’authentification Active Directory Azure, il existe deux comptes d’administrateur pour le serveur de base de données SQL ; l’administrateur de SQL Server d’origine et l’administrateur AD Azure. Les mêmes concepts s’appliquent à l’entrepôt de données SQL. Uniquement l’administrateur basée sur un compte Azure pouvez créer le premier utilisateur de la base de données Azure AD contenue dans une base de données de l’utilisateur. Connexion de l’administrateur AD Azure peut être un utilisateur AD Azure ou un groupe d’annonces Azure. Lorsque l’administrateur est un compte de groupe, il peut être utilisé par n’importe quel membre du groupe, l’activation de plusieurs administrateurs AD Azure pour l’instance de SQL Server. À l’aide du compte de groupe en tant qu’administrateur améliore la facilité de gestion en vous permettant d’ajouter et de supprimer des membres d’un groupe dans AD Azure sans modifier les utilisateurs ou les autorisations de base de données de SQL de manière centralisée. Seul un administrateur d’Azure AD (un utilisateur ou un groupe) peut être configuré à tout moment.

![structure de l’administration][3]

## <a name="permissions"></a>Autorisations

Pour créer de nouveaux utilisateurs, vous devez disposer du `ALTER ANY USER` autorisation dans la base de données. Le `ALTER ANY USER` autorisation peut être accordée à un utilisateur de base de données. Le `ALTER ANY USER` autorisation est également maintenue par les comptes d’administrateur de serveur et les utilisateurs de base de données avec le `CONTROL ON DATABASE` ou `ALTER ON DATABASE` autorisation pour cette base de données et par les membres de la `db_owner` rôle de base de données.

Pour créer un utilisateur de base de données contenus dans la base de données de SQL Azure ou SQL Data Warehouse, vous devez vous connecter à la base de données à l’aide d’une identité d’Azure AD. Pour créer le premier utilisateur de la base de données de contenu, vous devez vous connecter à la base de données à l’aide d’un administrateur d’Azure Active Directory (qui est le propriétaire de la base de données). Cela est illustré dans les étapes 4 et 5 ci-après. N’importe quelle authentification Azure Active Directory n’est possible que si l’administrateur Azure Active Directory a été créé pour la base de données de SQL Azure ou serveur SQL Data Warehouse. Si l’administrateur Azure Active Directory a été supprimé à partir du serveur, utilisateurs Azure Active Directory créés précédemment à l’intérieur de SQL Server peuvent ne plus se connecter à la base de données à l’aide de leurs informations d’identification Active Directory de Azure.

## <a name="azure-ad-features-and-limitations"></a>Fonctionnalités d’AD azure et limites

Les membres suivants de Azure Active Directory peuvent être mis en service dans Azure SQL Server ou de l’entrepôt de données de SQL :

- Membres natifs : un membre créé dans Active Directory Azure dans le domaine géré ou dans un domaine de client. Pour plus d’informations, consultez [Ajouter votre propre nom de domaine pour Active Directory Azure](../active-directory/active-directory-add-domain.md).

- Fédérée des membres du domaine : un membre créé dans Azure annonce avec un domaine fédéré. Pour plus d’informations, consultez [Microsoft Azure maintenant prend en charge la fédération avec Active Directory de Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).

- Membres importés à partir d’autres annuaires Active Directory Azure qui sont membres d’un domaine fédéré ou natif.

- Groupes Active Directory créés en tant que groupes de sécurité.

Les comptes Microsoft (par exemple outlook.com, hotmail.com, live.com) ou autres comptes d’invité (par exemple gmail.com, yahoo.com) ne sont pas pris en charge. Si vous pouvez vous connecter à [https://login.live.com](https://login.live.com) utilisant le compte et le mot de passe, vous utilisez un compte Microsoft, qui n’est pas pris en charge pour l’authentification AD Azure pour la base de données de SQL Azure ou de l’entrepôt de données SQL Azure.

### <a name="additional-considerations"></a>Considérations supplémentaires

- Pour améliorer la facilité de gestion, nous vous recommandons de que vous mettre en service un groupe Azure Active Directory en tant qu’administrateur.
- Seul un administrateur d’Azure AD (un utilisateur ou un groupe) peut être configuré pour un Azure SQL Server ou un magasin de données SQL Azure à tout moment.
- Seul un administrateur d’Azure Active Directory de SQL Server peut se connecter initialement à l’Azure SQL Server ou d’un entrepôt de données de SQL Azure en utilisant un compte Azure Active Directory. L’administrateur Active Directory peut configurer les utilisateurs de base de données Azure Active Directory suivants.
- Nous vous recommandons de définir le délai d’expiration de la connexion à 30 secondes.
- SQL Server 2016 Management Studio et les outils de données de SQL Server pour Visual Studio 2015 (version 14.0.60311.1April 2016 ou version ultérieure) prend en charge l’authentification Azure Active Directory. (L’authentification azure Active Directory est prise en charge par le **fournisseur de données.NET Framework pour SqlServer**; à la version minimale.NET Framework 4.6). Par conséquent, les dernières versions de ces outils et les applications de couche données (DAC et .bacpac) permet l’authentification Azure Active Directory.
- [Version d’ODBC 13.1](https://www.microsoft.com/download/details.aspx?id=53339) prend en charge l’authentification Active Directory de Azure toutefois `bcp.exe` Impossible de se connecter à l’aide de l’authentification Active Directory de Azure, car elle utilise un fournisseur ODBC plus ancien.
- `sqlcmd`prend en charge le début de l’authentification Azure Active Directory avec la version 13.1 disponible à partir du [Centre de téléchargement](http://go.microsoft.com/fwlink/?LinkID=825643).  
- Outils de données de SQL Server pour Visual Studio 2015 nécessite au moins la version d’avril 2016 des outils de données (version 14.0.60311.1). Actuellement les utilisateurs Azure Active Directory ne sont pas affichés dans l’Explorateur d’objets SSDT. Pour résoudre ce problème, afficher les utilisateurs dans [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [6.0 de pilote JDBC de Microsoft pour SQL Server](https://www.microsoft.com/en-us/download/details.aspx?id=11774) prend en charge l’authentification Azure Active Directory. Consultez également [la définition des propriétés de connexion](https://msdn.microsoft.com/library/ms378988.aspx).
- PolyBase ne peut pas s’authentifier à l’aide de l’authentification Active Directory de Azure.
- Certains outils comme BI et Excel ne sont pas pris en charge.
- L’authentification Active Directory Azure est prise en charge de base de données de SQL par les lames Azure **d’Importation de base de données** et **d’Exporter la base de données** de portail. Importation et exportation à l’aide de l’authentification Azure Active Directory est également pris en charge à partir de la commande PowerShell.


## <a name="1-create-and-populate-an-azure-ad"></a>1. créer et remplir une annonce Azure

Créer un Azure d’Active Directory et le remplir avec les utilisateurs et les groupes. Azure Active Directory peut être le domaine géré Azure AD de domaine initial. Azure Active Directory peut également être un local Services domaine Active Directory qui est fédéré avec Azure Active Directory.

Pour plus d’informations, voir [intégration des identités avec Azure Active Directory sur site](../active-directory/active-directory-aadconnect.md), [Ajouter votre propre nom de domaine pour Active Directory Azure](../active-directory/active-directory-add-domain.md), [Microsoft Azure maintenant prend en charge la fédération avec Active Directory de Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administration de votre annuaire AD Azure](https://msdn.microsoft.com/library/azure/hh967611.aspx)et [Gérer Azure AD à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## <a name="2-ensure-your-sql-database-is-version-12"></a>2. Assurez-vous que votre base de données SQL est la version 12

L’authentification Active Directory Azure est pris en charge dans la dernière V12 de base de données SQL. Pour plus d’informations sur V12 de base de données SQL et pour savoir si elle est disponible dans votre région, consultez [Nouveautés de la dernière mise à jour V12 SQL de base de données](sql-database-v12-whats-new.md). Cette étape n’est pas nécessaire pour l’entrepôt de données SQL Azure car SQL Data Warehouse est uniquement disponible dans V12.

Si vous avez une base de données existante, vérifiez qu’il est hébergé dans V12 de base de données SQL en vous connectant à la base de données (par exemple en utilisant SQL Server Management Studio) et l’exécution de `SELECT @@VERSION;`. Le résultat attendu pour une base de données SQL de base de données V12 est au moins **Microsoft SQL Azure (RTM) - 12.0**. Si votre base de données n’est pas hébergé dans V12 de base de données SQL, reportez-vous à la section [planifier et préparer la mise à niveau vers V12 de base de données SQL](sql-database-v12-plan-prepare-upgrade.md), puis rendez-vous sur le portail classique d’Azure pour migrer la base de données à V12 de base de données SQL.

Vous pouvez également créer une nouvelle base de données dans V12 de base de données SQL en suivant les étapes indiquées dans la [Création de votre première base de données SQL d’Azure](sql-database-get-started.md). **Conseil**: Lisez l’étape suivante avant de sélectionner un abonnement pour votre nouvelle base de données.

## <a name="3-optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>3. associer facultatif : Ou modifier active directory qui est actuellement associé à votre abonnement Azure

Pour associer votre base de données de l’annuaire Active Directory Azure pour votre organisation, sélectionnez le répertoire à un répertoire d’un niveau de confiance pour l’abonnement Azure qui héberge la base de données. Pour plus d’informations, consultez [comment Azure abonnements sont associés à une annonce Azure](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Plus d’informations :** Chaque abonnement Azure a une relation d’approbation avec une instance AD Azure. Cela signifie qu’il reconnaît ce répertoire pour authentifier les utilisateurs, les services et les périphériques. Plusieurs abonnements peuvent approuver le même répertoire, mais un abonnement fait confiance à un seul répertoire. Vous pouvez voir de quel répertoire est approuvé par votre abonnement sous l’onglet **paramètres** à [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Cette relation d’approbation qui dispose d’un abonnement avec un répertoire est différente de la relation entre un abonnement avec toutes les autres ressources dans Azure (sites Web, bases de données et ainsi de suite), qui sont apparentent à des ressources d’enfants d’un abonnement. Si un abonnement expire, puis accéder à ces autres ressources associées à l’abonnement s’arrête également. Mais le répertoire reste dans Azure, et vous pouvez associer un autre abonnement à ce répertoire et continuer à gérer les utilisateurs de l’annuaire. Pour plus d’informations sur les ressources, consultez [accès aux ressources de présentation dans Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Les procédures suivantes fournissent des instructions étape par étape comment modifier le répertoire associé à un abonnement donné.

1. Se connecter à votre [Portail classique d’Azure](https://manage.windowsazure.com/) à l’aide d’un administrateur d’abonnement Azure.
2. Dans la bannière de gauche, sélectionnez **paramètres**.
3. Vos abonnements s’affichent dans l’écran Paramètres. Si l’abonnement souhaité n’apparaît pas, cliquez sur **abonnements** en haut, déplacer vers le bas de la boîte de **Filtre par répertoire** le répertoire qui contient vos abonnements et sélectionnez puis cliquez sur **Appliquer**.

    ![Sélectionnez l’abonnement][4]
4. Dans la zone **paramètres** , cliquez sur votre abonnement, puis cliquez sur **Modifier le répertoire** au bas de la page.

    ![paramètres AD-portail][5]
5. Dans la zone **Modifier un répertoire** , sélectionnez Azure Active Directory qui est associé à votre SQL Server ou d’un entrepôt de données de SQL, puis cliquez sur la flèche suivant.

    ![modifier-directory-select][6]
6. Dans la boîte de dialogue mappage de répertoire **Confirmer** , vérifiez que «**tous les administrateurs de coopération seront supprimées.**»

    ![modifier-répertoire-confirmer][7]
7. Cliquez sur le contrôle pour recharger le portail.

> [AZURE.NOTE] Lorsque vous modifiez le répertoire, l’accès à tous les administrateurs de collaboration, AD Azure utilisateurs et groupes et reposant sur le répertoire de ressources utilisateurs sont supprimés et ils n’ont plus accès à ses ressources ou à cet abonnement. Seulement, en tant qu’un administrateur de service, pouvoir configurer l’accès pour les entités basées sur le nouveau répertoire. Cette modification peut prendre un certain temps de se propager à toutes les ressources. Si vous modifiez le répertoire, aussi l’administrateur AD Azure de base de données SQL et SQL Data Warehouse et interdire l’accès de base de données pour tous les utilisateurs AD Azure existants. L’administrateur AD Azure doit être réinitialisation (comme décrit ci-dessous) et Azure nouveaux utilisateurs d’Active Directory doivent être créés.

## <a name="4-create-an-azure-ad-administrator-for-azure-sql-server"></a>4. créer un administrateur AD Azure pour Azure SQL Server

Chaque SQL Server de Azure (qui héberge une base de données de SQL ou d’un entrepôt de données SQL) démarre avec un compte d’administrateur de serveur unique qui est l’administrateur de l’ensemble de SQL Server Azure. Un deuxième administrateur de SQL Server, qui est un compte Azure, doit être créé. Cette entité de sécurité est créée comme un utilisateur de base de données contenus dans la base de données master. En tant qu’administrateurs, les comptes d’administrateur de serveur sont les membres du rôle **db_owner** dans la base de données de chaque utilisateur et entrez chaque base de données de l’utilisateur que l’utilisateur **dbo** . Pour plus d’informations sur les comptes d’administrateur de serveur, voir [Gérer les bases de données et les connexions de base de données de SQL Azure](sql-database-manage-logins.md) et des **connexions et les utilisateurs** de [directives de sécurité de base de données SQL Azure et Limitations](sql-database-security-guidelines.md).

Lorsque vous utilisez Azure Active Directory avec la réplication de la zone géographique, l’administrateur d’Azure Active Directory doit être configuré pour le serveur principal et les serveurs secondaires. Si un serveur ne dispose pas d’un administrateur Azure Active Directory, puis les utilisateurs et les connexions d’accès Azure Active Directory reçoivent une « connexion impossible » avec le serveur.

> [AZURE.NOTE] Utilisateurs qui ne sont pas basées sur un compte Azure (y compris le compte d’administrateur de SQL Server Azure), ne peut pas créer les utilisateurs AD Azure, car ils n’ont pas d’autorisation pour valider les utilisateurs de base de données proposée avec l’annonce Azure.

### <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server-by-using-the-azure-portal"></a>Mettre en service un administrateur Azure Active Directory pour votre de SQL Server Azure via le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), dans le coin supérieur droit, cliquez sur la connexion à une liste de répertoires de Active possible de liste déroulante. Choisissez le répertoire actif par défaut Azure AD. Cette étape lie avec Active Directory avec SQL Server de Azure assurant que l’abonnement même est utilisé à la fois pour l’association d’abonnement Azure AD et SQL Server. (Le SQL Server Azure peuvent héberger soit une base de données de SQL Azure Azure SQL Data Warehouse.)

    ![Choisissez-ad][8]
2. Dans la bannière gauche Sélectionnez **les serveurs SQL**, sélectionnez votre **serveur SQL**, puis de la lame **De SQL Server** , du haut cliquez sur **paramètres**.

    ![paramètres d’annonces][9]
3. Dans la lame de **paramètres** , cliquez sur ** Admin de Active Directory.
4. De la lame **d’administration d’Active Directory** , cliquez sur **administration de Active Directory**et puis en haut, cliquez sur **admin**.
5. Dans la blade **admin d’ajouter** , de rechercher un utilisateur, sélectionnez l’utilisateur ou le groupe soit un administrateur, puis cliquez sur **Sélectionner**. (La lame d’administration Active Directory affiche tous les membres et groupes d’Active Directory. Impossible de sélectionner les utilisateurs ou les groupes qui sont grisées car ils ne sont pas pris en charge en tant qu’administrateurs AD Azure. (Voir la liste des administrateurs de prise en charge dans **Azure AD fonctionnalités et Limitations** ci-dessus.) Contrôle d’accès basé sur les rôles (RBAC) s’applique uniquement au portail et n’est pas propagé à SQL Server.
6. En haut de la lame **d’administration d’Active Directory** , cliquez sur **Enregistrer**.
    ![Cliquez sur admin][10]

    Le processus de modification de l’administrateur peut prendre plusieurs minutes. Le nouvel administrateur apparaît alors dans la zone **d’administration d’Active Directory** .

> [AZURE.NOTE] Lors du paramétrage de l’administrateur AD Azure, le nouveau nom admin (utilisateur ou groupe) ne peut pas déjà exister dans la base de données master virtuel comme un utilisateur de l’authentification SQL Server. Le cas échéant, le programme d’installation d’Active Directory Azure admin échoue ; annulation de sa création et indiquant que cet un admin (nom) déjà existent. Dans la mesure où un tel utilisateur de l’authentification de SQL Server n’est pas partie de la publicité Azure, aucun effort pour se connecter au serveur à l’aide de l’authentification Active Directory Azure échoue.

Pour supprimer un administrateur, dans la partie supérieure de la pale **d’administration d’Active Directory** , cliquez sur **Supprimer l’administrateur**, puis cliquez sur **Enregistrer**.

### <a name="provision-an-azure-ad-administrator-for-azure-sql-server-by-using-powershell"></a>Mettre en service un administrateur AD Azure pour Azure SQL Server à l’aide de PowerShell

Pour exécuter les applets de commande PowerShell, vous devez avoir Azure PowerShell installé et en cours d’exécution. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Pour configurer un administrateur AD Azure, exécutez les commandes de PowerShell d’Azure suivantes :

- Ajouter-AzureRmAccount
- Sélectionnez-AzureRmSubscription


Applets de commande permet de provisionner et de gérer les annonces Azure admin :

| Nom de l’applet de commande                                       | Description                                                                                                     |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Ensemble-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)    | Met en service un administrateur Active Directory de Azure pour Azure SQL Server ou de l’entrepôt de données SQL Azure. (Doit être de l’abonnement en cours.) |
| [Supprimer-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Supprime un administrateur Active Directory de Azure pour Azure SQL Server ou de l’entrepôt de données SQL Azure. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)    | Renvoie des informations sur un administrateur Azure Active Directory actuellement configuré pour Azure SQL Server ou de l’entrepôt de données SQL Azure. |

Permet de voir plus de détails, pour chacune de ces commandes, par exemple get-help de commande PowerShell ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Les dispositions suivantes de script un groupe d’administrateurs AD Azure nommé **DBA_Group** (id de l’objet `40b79501-b343-44ed-9ce7-da4c8cc7353f`) pour la **demo_server** de serveur dans un groupe de ressources appelé **groupe-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

Le paramètre d’entrée **DisplayName** accepte le nom d’affichage AD Azure ou le nom d’utilisateur Principal. Par exemple, ``DisplayName="John Smith"`` et ``DisplayName="johns@contoso.com"``. Nom d’affichage est pris en charge pour les groupes d’annonces Azure uniquement la publicité Azure.

> [AZURE.NOTE] La commande Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` ne vous empêche pas de mise en service des administrateurs AD Azure pour les utilisateurs non pris en charge. Un utilisateur non pris en charge peut être mis en service, mais ne peut pas se connecter à une base de données. (Voir la liste des administrateurs de prise en charge dans **Azure AD fonctionnalités et Limitations** ci-dessus.)

L’exemple suivant utilise l' option **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] Annonce Azure **ObjectID** est obligatoire lorsque le **DisplayName** n’est pas unique. Pour récupérer les valeurs **ObjectID** et **DisplayName** , utilisez la section Active Directory de portail classique d’Azure et afficher les propriétés d’un utilisateur ou un groupe.

L’exemple suivant renvoie des informations sur le cours administration AD Azure pour Azure SQL Server :

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

L’exemple suivant supprime un administrateur AD Azure :
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

Vous pouvez également prévoir un administrateurs Azure Active Directory à l’aide de l’API de reste. Pour plus d’informations, consultez la [référence sur l’API reste Service gestion et opérations pour les opérations de bases de données SQL Azure des bases de données SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="5-configure-your-client-computers"></a>5. Configurez les ordinateurs clients

Sur tous les ordinateurs clients, à partir de laquelle vos applications ou les utilisateurs se connectent à la base de données de SQL Azure ou entrepôt de données de SQL Azure en utilisant les identités AD Azure, vous devez installer les logiciels suivants :

- .NET Framework 4.6 ou version ultérieure à partir de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Bibliothèque d’authentification Azure Active Directory pour SQL Server (**, ADALSQL. DLL**) est disponible dans plusieurs langues (x86 et amd64) à partir du centre de téléchargement de [Bibliothèque Microsoft Active Directory Authentication pour Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### <a name="tools"></a>Outils

- Installez [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou [Outils de données de SQL Server pour Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) répond à l’exigence du point 4.6 du.NET Framework.
- SSMS installe le x86 la version de **ADALSQL. DLL**.
- SSDT installe la version amd64 de **ADALSQL. DLL**.
- La plus récente de Visual Studio à partir des [Téléchargements de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) répond à l’exigence du point 4.6 du.NET Framework, mais n’installe pas la version amd64 requis de **ADALSQL. DLL**.

## <a name="6-create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>6. créer des utilisateurs de base de données contenus dans votre base de données mis en correspondance avec des identités AD Azure

### <a name="about-contained-database-users"></a>À propos des utilisateurs de la base de données de contenu

L’authentification Active Directory Azure requiert des utilisateurs de base de données doit être créé en tant qu’utilisateurs de la base de données de contenu. Un utilisateur de base de données de contenu basé sur une identité AD Azure, est un utilisateur de base de données qui ne dispose pas d’une connexion d’accès dans la base de données master, et qui est mappé à une identité dans l’annuaire AD Azure qui est associé à la base de données. L’identité Active Directory Azure peut être soit un compte utilisateur individuel ou un groupe. Pour plus d’informations sur les utilisateurs de base de données de contenu, reportez-vous à la [Fabrication de contenus aux utilisateurs de base de données de votre base de données Portable](https://msdn.microsoft.com/library/ff929188.aspx).

> [AZURE.NOTE] Les utilisateurs de base de données (à l’exception des administrateurs) ne peuvent pas être créés à l’aide de portail. Les rôles RBAC ne sont pas propagées à SQL Server, de la base de données SQL ou SQL Data Warehouse. Les rôles RBAC Azure sont utilisés pour la gestion des ressources d’Azure et ne s’appliquent pas aux autorisations de base de données. Par exemple, le rôle de **Collaborateur de SQL Server** n’accorde pas d’accès pour vous connecter à la base de données de SQL ou SQL Data Warehouse. L’autorisation d’accès doit être accordée directement dans la base de données à l’aide des instructions de Transact-SQL.

### <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>Se connecter à l’entrepôt de données ou la base de données utilisateur à l’aide de SQL Server Management Studio ou les outils de données de SQL Server

Pour confirmer l’administrateur AD Azure est correctement configuré, vous connecter à la base de données **master** en utilisant le compte d’administrateur AD Azure.
Pour configurer un utilisateur de base de données de contenu basé sur les annonces Azure (autres que l’administrateur du serveur qui est propriétaire de la base de données), se connecter à la base de données avec une identité d’Azure AD qui a accès à la base de données.

> [AZURE.IMPORTANT] Prise en charge de l’authentification Active Directory de Azure est disponible avec [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et les [Outils de données de SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) dans Visual Studio 2015. La version d’août 2016 de SSMS inclut également la prise en charge de l’authentification Active Directory universel, qui permet aux administrateurs d’exiger l’authentification à facteurs multiples à l’aide d’un appel téléphonique, message texte, cartes à puce avec un code pin, ou la notification de l’application mobile.

#### <a name="connect-using-active-directory-integrated-authentication"></a>Se connecter à l’aide de l’authentification intégrée à Active Directory

Utilisez cette méthode si vous êtes connecté à Windows à l’aide de vos informations d’identification d’Azure Active Directory à partir d’un domaine fédéré.

1. Démarrez Management Studio ou les outils de données et dans la boîte de dialogue **se connecter au serveur** (ou de **se connecter au moteur de base de données**) dans la zone **authentification** , sélectionnez **Authentification intégrée à Active Directory**. Aucun mot de passe est nécessaire ou peut être entrée car vos informations d’identification existantes seront présentées pour la connexion.
    ![Activez l’authentification intégrée à Active Directory][11]

2. Cliquez sur le bouton **Options** et dans la page **Propriétés de connexion** , dans la zone **se connecter à la base de données** , tapez le nom de la base de données utilisateur que vous souhaitez vous connecter.
    ![Sélectionnez le nom de la base de données][13]


#### <a name="connect-using-active-directory-password-authentication"></a>Se connecter à l’aide de l’authentification du mot de passe Active Directory

Utilisez cette méthode lors de la connexion avec un nom principal Azure AD à l’aide de la publicité Azure gérés de domaine. Vous pouvez également l’utiliser pour compte fédéré sans accès au domaine, par exemple lorsque vous travaillez à distance.

Utilisez cette méthode si vous êtes connecté à Windows à l’aide des informations d’identification à partir d’un domaine qui n’est pas fédéré avec Azure, ou lorsque l’authentification AD Azure utilise Active Directory Azure basé sur initial ou le domaine du client.

1. Démarrez Management Studio ou les outils de données et dans la boîte de dialogue **se connecter au serveur** (ou de **se connecter au moteur de base de données**) dans la zone **authentification** , sélectionnez **Authentification de mot de passe Active Directory**.
2. Dans la zone **nom d’utilisateur** , tapez votre nom d’utilisateur Active Directory de Azure dans le format **username@domain.com**. Ce doit être un compte Azure Active Directory ou un compte d’un domaine se fédérer avec Azure Active Directory.
3. Dans la zone **mot de passe** , tapez votre mot de passe pour le compte Azure Active Directory ou fédéré de compte de domaine.
    ![Sélectionnez authentification de mot de passe Active Directory][12]

4. Cliquez sur le bouton **Options** et dans la page **Propriétés de connexion** , dans la zone **se connecter à la base de données** , tapez le nom de la base de données utilisateur que vous souhaitez vous connecter. (Voir le graphique dans l’option précédente.)


### <a name="create-an-azure-ad-contained-database-user-in-a-user-database"></a>Créer un utilisateur de base de données Azure AD contenue dans une base de données de l’utilisateur

Pour créer un utilisateur de base de données de contenu basé sur les annonces Azure (autres que l’administrateur du serveur qui est propriétaire de la base de données), se connecter à la base de données avec une identité AD Azure, en tant qu’utilisateur au moins l’autorisation **ALTER ANY USER** . Puis, utilisez la syntaxe Transact-SQL suivante :

    CREATE USER <Azure_AD_principal_name>
    FROM EXTERNAL PROVIDER;


*Azure_AD_principal_name* peut être le nom d’utilisateur principal d’un utilisateur AD Azure ou le nom d’affichage pour un groupe d’annonces Azure.

**Exemples :** Pour créer une base de données de contenu utilisateur représentant une publicité Azure fédérés ou gérés d’utilisateur de domaine :

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Pour créer un utilisateur de base de données de contenu représentant une publicité Azure ou fédéré de groupe de domaine, fournissez le nom complet d’un groupe de sécurité :

    CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Pour créer un utilisateur de base de données de contenu représentant une application qui se connecte à l’aide d’un jeton d’Azure AD :

    CREATE USER [appName] FROM EXTERNAL PROVIDER;

Pour plus d’informations sur la création d’utilisateurs de base de données de contenu basés sur les identités d’Azure Active Directory, reportez-vous à la section [Créer un utilisateur (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).


> [AZURE.NOTE] Suppression de l’administrateur Active Directory de Azure pour Azure SQL Server empêche la n’importe quel utilisateur de l’authentification AD Azure de se connecter au serveur. Si nécessaire, inutilisable utilisateurs AD Azure peuvent être supprimés manuellement par un administrateur de base de données SQL.

Lorsque vous créez un utilisateur de base de données, cet utilisateur reçoit l’autorisation de **se connecter** et peut se connecter à cette base de données en tant que membre du rôle **PUBLIC** . Initialement, les seules autorisations disponibles pour l’utilisateur sont toutes les autorisations accordées au rôle **PUBLIC** , ou que les autorisations accordées à tous les groupes Windows auxquels ils sont un membre. Une fois que vous devez configurer un utilisateur de base de données de contenu basé sur les annonces Azure, vous pouvez accorder à l’utilisateur des autorisations supplémentaires, la même façon que vous accordez l’autorisation à tout autre type d’utilisateur. En général, accorder des autorisations aux rôles de base de données et ajouter des utilisateurs à des rôles. Pour plus d’informations, consultez [Concepts de base autorisation de moteur de base de données](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Pour plus d’informations sur les rôles de base de données SQL spéciaux, voir [Gérer les bases de données et les connexions de base de données de SQL Azure](sql-database-manage-logins.md).
Un domaine fédéré qui est importé dans un domaine de gestion, vous devez utiliser l’identité de domaine géré.

> [AZURE.NOTE] Les utilisateurs AD Azure sont marquées dans les métadonnées de la base de données avec type E (EXTERNAL_USER) et pour les groupes de type X (EXTERNAL_GROUPS). Pour plus d’informations, consultez [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## <a name="7-connect-by-using-azure-ad-identities"></a>7. vous connecter à l’aide des identités AD Azure

L’authentification Active Directory Azure prend en charge les méthodes suivantes de la connexion à une base de données à l’aide des identités de publicité Azure :

- À l’aide de l’authentification intégrée de Windows
- À l’aide d’un nom principal de publicité Azure et un mot de passe
- À l’aide de l’authentification des jetons Application

### <a name="71-connecting-using-integrated-windows-authentication"></a>7.1. Connexion à l’aide de l’authentification intégrée (Windows)

Pour utiliser l’authentification intégrée de Windows, Active Directory de votre domaine doit être fédéré avec Azure Active Directory. Votre application cliente (ou un service de connexion à la base de données) doit être en cours d’exécution sur un ordinateur à un domaine sous les informations d’identification de domaine d’un utilisateur.

Pour vous connecter à une base de données à l’aide de l’authentification intégrée et une identité d’Azure AD, le mot clé d’authentification dans la chaîne de connexion de base de données doit être défini sur intégrée à Active Directory. L’exemple de code C# suivant utilise ADO .NET.

    string ConnectionString =
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Notez que la chaîne de connexion mot clé ``Integrated Security=True`` n’est pas pris en charge pour la connexion à la base de données de SQL Azure.
Notez que lors d’une connexion ODBC vous devrez supprimer les espaces et définir l’authentification à 'ActiveDirectoryIntegrated'.

### <a name="72-connecting-with-an-azure-ad-principal-name-and-a-password"></a>7.2. Connexion avec un nom principal de publicité Azure et un mot de passe
Pour vous connecter à une base de données à l’aide de l’authentification intégrée et une identité d’Azure AD, le mot clé d’authentification doit être défini pour le mot de passe Active Directory. La chaîne de connexion doit contenir les ID/ID d’utilisateur et les mots clés de mot de passe/mot de passe et les valeurs. L’exemple de code C# suivant utilise ADO .NET.

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Pour en savoir plus sur les méthodes d’authentification AD Azure utilisant les exemples de code de démonstration disponibles [Azure démonstration de GitHub de l’authentification Active Directory](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).


### <a name="73-connecting-with-an-azure-ad-token"></a>7.3 se connectant avec un jeton d’Azure AD
Cette méthode d’authentification permet de se connecter à la base de données de SQL Azure ou de l’entrepôt de données SQL Azure en obtenant un jeton d’Azure Active Directory (DAS) des services de niveau intermédiaire. Il permet des scénarios élaborés y compris l’authentification basée sur les certificats. Vous devez effectuer les quatre étapes de base pour utiliser l’authentification de jeton AD Azure :

1. Inscrire votre application dans Azure Active Directory et obtenir l’id client pour votre code. 
2. Créer un utilisateur de base de données qui représente l’application. (Effectuée à l’étape 6).
3. Créer un certificat sur l’ordinateur client exécute l’application.
4. Ajoutez le certificat comme une clé pour votre application.

Exemple de chaîne de connexion :

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Pour plus d’informations, consultez le [Blog de sécurité de SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/).

### <a name="connecting-with-sqlcmd"></a>Connexion à l’aide de sqlcmd  
Les instructions suivantes, vous connecter à l’aide de la version 13.1 de sqlcmd, qui est disponible à partir du [Centre de téléchargement](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="see-also"></a>Voir aussi

[Gestion des bases de données et des connexions de base de données SQL Azure](sql-database-manage-logins.md)

[Utilisateurs de base de données de contenu](https://msdn.microsoft.com/library/ff929071.aspx)

[CRÉER utilisateur (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

