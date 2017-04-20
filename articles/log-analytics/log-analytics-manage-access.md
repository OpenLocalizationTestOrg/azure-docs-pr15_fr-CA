<properties
    pageTitle="Gérer l’accès aux journaux Analytique | Microsoft Azure"
    description="Gérer l’accès aux Analytique du journal à l’aide d’une variété de tâches administratives sur les comptes d’utilisateurs, les comptes, les espaces de travail OMS et Azure."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="banders"/>

# <a name="manage-access-to-log-analytics"></a>Gérer l’accès aux journaux Analytique

Pour gérer l’accès aux journaux Analytique, vous allez utiliser une variété de tâches administratives sur les comptes d’utilisateurs, les comptes, les espaces de travail OMS et Azure. Pour créer un nouvel espace de travail dans la Suite de gestion des opérations (OMS), vous choisissez un nom d’espace de travail, l’associer à votre compte et que vous choisissez un emplacement géographique. Un espace de travail est essentiellement un conteneur qui comprend les informations de compte et les informations de configuration simple pour le compte. Vous ou les autres membres de votre organisation peuvent utiliser plusieurs espaces de travail OMS pour gérer différents ensembles de données qui sont collectées à partir de toutes ou certaines parties de votre infrastructure informatique.

L’article [mise en route de journal Analytique](log-analytics-get-started.md) vous indique comment obtenir rapidement et en cours d’exécution et que le reste de cet article décrit plus en détail certaines des actions que vous devrez gérer l’accès aux OMS.

Bien que vous n’ayez pas du tout à effectuer chaque tâche de gestion dans un premier temps, nous aborderons toutes les tâches courantes que vous pouvez utiliser dans les sections suivantes :

- Déterminer le nombre d’espaces de travail dont vous avez besoin
- Gérer les comptes et les utilisateurs
- Ajouter un groupe à un espace de travail existant
- Lier un espace de travail existant à un abonnement Azure
- Mise à niveau d’un espace de travail à un plan de données payées
- Modifier un type de plan de données
- Ajouter une organisation Azure du répertoire actif à un espace de travail existant
- Fermez votre espace de travail de l’OMS

## <a name="determine-the-number-of-workspaces-you-need"></a>Déterminer le nombre d’espaces de travail dont vous avez besoin

Un espace de travail est une ressource d’Azure et un conteneur dans lequel les données sont collectées, agrégées, analysées et présentées dans le portail de l’OMS.

Il est possible de créer plusieurs espaces de travail Analytique de journal OMS et les utilisateurs d’accéder à un ou plusieurs espaces de travail. En général, vous souhaitez réduire le nombre d’espaces de travail comme cela vous permettra d’interroger et de mettre en corrélation les données de la plupart des. Cette section décrit quand il peut être utile créer plus d’un espace de travail.

Aujourd'hui, un espace de travail du journal Analytique fournit :

- Un emplacement géographique pour le stockage de données
- Niveau de granularité pour la facturation
- Isolation des données

Les caractéristiques ci-dessus, vous pouvez avoir besoin de créer plusieurs espaces de travail si :

- Vous êtes une entreprise globale et vous avez besoin de données stockées dans des régions spécifiques pour des raisons relevant de la souveraineté ou de la conformité de données.
- Vous utilisez Azure et si vous voulez éviter les frais de transfert de données sortant en disposant d’un espace de travail du journal Analytique dans la même région en tant que ressources Azure qu'il gère.
- Vous souhaitez répartir les frais sur les différents départements ou des groupes en fonction de leur utilisation. Lorsque vous créez un espace de travail pour chaque département ou groupe d’entreprise, votre relevé de facturation et de l’utilisation de Azure indique séparément les frais pour chaque espace de travail.
- Vous êtes un fournisseur de services managés et ont besoin de conserver les données d’analytique de journal pour chaque client vous gérer isolé des autres données de client.
- Gérez plusieurs clients avec vous et vous souhaitez que chaque client ou un service ou un groupe d’entreprises de voir leurs propres données, mais pas les données pour les autres clients, services ou des groupes.

Lorsque vous utilisez des agents pour collecter des données, vous pouvez configurer chaque agent de signaler à l’espace de travail requis.

Si vous utilisez System Center Operations manager, chaque groupe d’administration Operations Manager peut être connecté avec un seul espace de travail. Vous pouvez installer l’Agent de surveillance de Microsoft sur les ordinateurs gérés par Operations Manager et que l’état de l’agent Operations Manager et un autre espace de travail du journal Analytique.

### <a name="workspace-information"></a>Informations d’espace de travail

Dans le portail de l’OMS, vous pouvez afficher les informations de votre espace de travail et choisissez si vous souhaitez recevoir des informations à partir de Microsoft.

#### <a name="view-workspace-information"></a>Afficher les informations sur espace de travail

1. De l’OMS, cliquez sur la fenêtre de **paramètres** .
2. Cliquez sur l’onglet **comptes** .
3. Cliquez sur l’onglet **Espace de travail** .  
  ![Informations d’espace de travail](./media/log-analytics-manage-access/workspace-information.png)

## <a name="manage-accounts-and-users"></a>Gérer les comptes et les utilisateurs

Chaque espace de travail peut avoir plusieurs comptes d’utilisateur lui est associés, et chaque compte d’utilisateur (compte Microsoft ou comptes) peut avoir accès à plusieurs espaces de travail OMS.

Par défaut, le compte de Microsoft ou d’organisation utilisé pour créer l’espace de travail devient l’administrateur de l’espace de travail. L’administrateur peut ensuite inviter d’autres comptes Microsoft ou Azure Active Directory de choix aux utilisateurs.

Offrant aux utilisateurs l’accès à l’espace de travail OMS est contrôlée à 2 emplacements différents :

- Dans Azure, vous pouvez utiliser le contrôle d’accès basé sur le rôle pour fournir l’accès à l’abonnement Azure et d’Azure ressources associées. Il est également utilisé pour l’accès PowerShell et l’API REST.
- Dans le portail de l’OMS, accéder uniquement au portail OMS - pas l’abonnement Azure associé.

Si vous accorder l’accès sur le portail de l’OMS, mais pas à l’abonnement Azure auquel elle est liée, puis les mosaïques de solution Automation, de sauvegarde et de restauration de Site, ne pas affichent toutes les données aux utilisateurs lorsqu’ils signe dans le portail de l’OMS.

Pour permettre à tous les utilisateurs de voir les données dans ces solutions, assurez-vous qu’ils ont au moins **lecteur** accéder pour la chambre forte automatisation compte, coffre-fort de la sauvegarde et la restauration de Site lié à l’espace de travail de l’OMS.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Gestion de l’accès à Analytique journal via le portail Azure

Si vous autoriser l’accès à l’espace de travail du journal Analytique à l’aide d’autorisations d’Azure, dans Azure portal, par exemple, puis les mêmes utilisateurs peuvent accéder au portail Analytique du journal. Si les utilisateurs sont dans le portail Azure, ils peuvent naviguer vers le portail de l’OMS en cliquant sur la tâche de **l’OMS Portal** lors de l’affichage de la ressource d’espace de travail Analytique du journal.

Certains points à prendre en compte sur le portail Azure :

- Ce n’est pas *un contrôle d’accès basée sur les rôles*. Si vous avez des autorisations d’accès de *lecteur* dans le portail Azure pour l’espace de travail du journal Analytique, vous pouvez apporter des modifications via le portail de l’OMS. Le portail de l’OMS a un concept de l’administrateur, le collaborateur et en lecture seule. Si le compte que vous êtes connecté à l’aide d’est dans Azure Active Directory liés à l’espace de travail vous sera un administrateur dans le portail de l’OMS, dans le cas contraire, vous serez un collaborateur.

- Lorsque vous ouvrez une session dans le portail de l’OMS à l’aide de http://mms.microsoft.com, par défaut, vous voyez la liste **Sélectionnez un espace de travail** . Il contient uniquement des espaces de travail qui ont été ajoutés à l’aide du portail de l’OMS. Pour afficher les espaces de travail, vous avez accès à des abonnements d’Azure, puis vous devez spécifier un client dans le cadre de l’URL. Par exemple :

  `mms.microsoft.com/?tenant=contoso.com`L’identificateur client est souvent de cette dernière partie de l’adresse de messagerie qui vous connectez-vous à l’aide.

- Si le compte vous connectez-vous à l’aide est un compte dans le locataire Azure Active Directory, qui est généralement le cas sauf si vous êtes la signature en tant que d’un fournisseur de services cryptographiques, vous serez l' *administrateur* dans le portail de l’OMS. Si votre compte n’est pas dans le locataire Azure Active Directory, vous serez un *utilisateur* dans le portail de l’OMS.

- Si vous souhaitez accéder directement à un portail que vous avez accès à l’utilisation des autorisations Azure, vous devez spécifier la ressource en tant que partie de l’URL. Il est possible d’obtenir cette URL à l’aide de PowerShell.

  Par exemple, `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  L’URL ressemble à :`https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`


### <a name="managing-users-in-the-oms-portal"></a>Gestion des utilisateurs dans le portail de l’OMS

Gestion des utilisateurs et groupe de l’onglet **Gérer les utilisateurs** sous l’onglet **comptes** de la page de paramètres. Vous pouvez effectuer les tâches dans les sections suivantes.  

![gérer les utilisateurs](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### <a name="add-a-user-to-an-existing-workspace"></a>Ajouter un utilisateur à un espace de travail existant

Utilisez la procédure suivante pour ajouter un utilisateur ou un groupe à un espace de travail de l’OMS. L’utilisateur ou le groupe sera en mesure d’afficher et d’agir sur toutes les alertes qui sont associés à cet espace de travail.

>[AZURE.NOTE] Si vous souhaitez ajouter un utilisateur ou un groupe à partir de votre compte d’organisation Azure Active Directory, vous devez d’abord vous assurer que vous avez associé votre compte OMS votre domaine Active Directory. Consultez [Ajouter une organisation Azure du répertoire actif à un espace de travail existant](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. De l’OMS, cliquez sur la fenêtre de **paramètres** .
2. Cliquez sur l’onglet **comptes** , puis sur l’onglet **Gérer les utilisateurs** .
3. Dans la section **Gérer les utilisateurs** , choisissez le type de compte à ajouter : **Compte d’organisation**, le **Compte de Microsoft**, **La prise en charge de Microsoft**.
    - Si vous choisissez Microsoft Account, tapez l’adresse de messagerie de l’utilisateur associé à l’Account de Microsoft.
    - Si vous choisissez compte d’organisation, vous pouvez entrer la part de l’utilisateur ou l’alias de nom ou courriel du groupe et une liste des utilisateurs et des groupes s’affiche. Sélectionnez un utilisateur ou un groupe.
    - Utilisez le Support Microsoft pour fournir un Support Microsoft concevoir un accès temporaire à votre espace de travail pour aider à la résolution des problèmes.

    >[AZURE.NOTE] Pour de meilleurs résultats, limitez le nombre de groupes Active Directory associé à un seul compte OMS à trois : un pour les administrateurs, un pour les collaborateurs et un pour les utilisateurs en lecture seule. L’utilisation de plusieurs groupes susceptibles d’affecter les performances du journal Analytique.

5. Choisissez le type d’utilisateur ou le groupe à ajouter : **administrateur**, **collaborateurs**ou **En lecture seule** .  
6. Cliquez sur **Ajouter**.

  Si vous ajoutez un compte Microsoft, une invitation à rejoindre l’espace de travail est envoyée à l’e-mail que vous avez fournies. Une fois que l’utilisateur suit les instructions fournies dans l’invitation à rejoindre l’OMS, l’utilisateur peut afficher les alertes et les informations de compte pour ce compte à OMS, et vous ne pourrez pas afficher les informations utilisateur dans l’onglet **comptes** de la page **paramètres** .
  Si vous ajoutez un compte d’organisation, l’utilisateur pourra accéder Analytique de journal immédiatement.  
  ![courrier électronique d’invitation](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

#### <a name="edit-an-existing-user-type"></a>Modifier un type d’utilisateur existant

Vous pouvez modifier le rôle de compte pour un utilisateur associé à votre compte à OMS. Vous disposez des options de rôle ci-dessous :

 - *Administrateur*: peut gérer les utilisateurs, afficher et agir sur toutes les alertes et ajouter et supprimer des serveurs

 - *Collaborateurs*: peuvent afficher et agir sur toutes les alertes et ajouter et supprimer des serveurs

 - *ReadOnly utilisateur*: utilisateurs marqués en lecture seule ne sera pas en mesure de :
   1. Ajouter ou supprimer des solutions. La galerie de solutions est masquée.
   2. Ajouter/modifier/supprimer des mosaïques sur **Mon tableau de bord**.
   3. Permet d’afficher les pages de **paramètres** . Les pages sont masqués.
   4. Recherche vue, PowerBI configuration, recherches enregistrées, les alertes, les tâches sont masquées.


#### <a name="to-edit-an-account"></a>Pour modifier un compte

1. De l’OMS, cliquez sur la fenêtre de **paramètres** .
2. Cliquez sur l’onglet **comptes** , puis sur l’onglet **Gérer les utilisateurs** .
3. Sélectionnez le rôle de l’utilisateur que vous souhaitez modifier.
2. Dans la boîte de dialogue de confirmation, cliquez sur **Oui**.

### <a name="remove-a-user-from-a-oms-workspace"></a>Supprimer un utilisateur d’un espace de travail de l’OMS

Utilisez les étapes suivantes pour supprimer un utilisateur d’un espace de travail de l’OMS. Notez que ceci ne clôt pas l’espace de travail de l’utilisateur. Au lieu de cela, elle supprime l’association entre cet utilisateur et l’espace de travail. Si un utilisateur est associé à plusieurs espaces de travail, cet utilisateur pourront se connecter à OMS et consultez les autres espaces de travail.

1. De l’OMS, cliquez sur la fenêtre de **paramètres** .
2. Cliquez sur l’onglet **comptes** , puis sur l’onglet **Gérer les utilisateurs** .
3. Cliquez sur **Supprimer** en regard du nom d’utilisateur que vous souhaitez supprimer.
4. Dans la boîte de dialogue de confirmation, cliquez sur **Oui**.


### <a name="add-a-group-to-an-existing-workspace"></a>Ajouter un groupe à un espace de travail existant

1.  Suivez les étapes 1-à 4 dans « To ajouter un utilisateur à un espace de travail existant », ci-dessus.
2.  Sous **Choisissez un utilisateur/groupe**, sélectionnez **groupe**.
    ![Ajouter un groupe à un espace de travail existant](./media/log-analytics-manage-access/add-group.png)
3.  Entrez le nom complet ou l’adresse E-mail pour le groupe que vous souhaitez ajouter.
4.  Sélectionnez le groupe dans les résultats de la liste, puis sur **Ajouter**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Lier un espace de travail existant à un abonnement Azure

Il est possible de créer un espace de travail à partir du site Web [microsoft.com/oms](https://microsoft.com/oms) .  Cependant, certaines limites existent pour ces espaces de travail, le plus le notable étant une limite de 500 Mo par jour de transferts de données si vous utilisez un compte gratuit. Pour apporter des modifications à cet espace de travail, vous devrez *lier votre espace de travail existant à un abonnement Azure*.

>[AZURE.IMPORTANT] Pour lier un espace de travail, votre compte Azure doit avoir accès à l’espace de travail que vous souhaitez lier.  En d’autres termes, le compte que vous utilisez pour accéder au portail Azure doit être **le même** que le compte que vous utilisez pour accéder à votre espace de travail de l’OMS. Si ce n’est pas le cas, consultez [Ajouter un utilisateur à un espace de travail existant](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Pour lier un espace de travail à un abonnement Azure dans le portail de l’OMS

Afin de lier un espace de travail à un abonnement Azure dans le portail de l’OMS, l’utilisateur connecté doit avoir un compte Azure payé. L’espace de travail que vous utilisez activement est lié au compte Azure.

1. De l’OMS, cliquez sur la fenêtre de **paramètres** .
2. Cliquez sur l’onglet **comptes** , puis sur l’onglet **abonnement Azure et Plan de données** .
3. Cliquez sur le plan des données que vous souhaitez utiliser.
4. Cliquez sur **Enregistrer**.  
  ![plans d’abonnement et de données](./media/log-analytics-manage-access/subscription-tab.png)

Votre nouveau plan de données s’affiche dans le ruban de portail OMS en haut de votre page web.

![Ruban de l’OMS](./media/log-analytics-manage-access/data-plan-changed.png)

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Pour lier un espace de travail à un abonnement Azure dans le portail Azure

1.  Connectez-vous au [portail Azure](http://portal.azure.com).
2.  Recherchez le **Journal Analytique (OMS)** et sélectionnez-le.
3.  Vous verrez la liste des espaces de travail existants. Cliquez sur **Ajouter**.  
    ![liste des espaces de travail](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.  Sous **Espace de travail de l’OMS**, cliquez sur **ou sur Créer un lien existant**.  
    ![créer un lien existant](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.  Cliquez sur **configurer les paramètres requis**.  
    ![configurer les paramètres requis](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.  Vous verrez la liste des espaces de travail qui ne sont pas encore lié à votre compte Azure. Sélectionnez un espace de travail.  
    ![Sélectionnez les espaces de travail](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.  Si nécessaire, vous pouvez modifier les valeurs pour les éléments suivants :
    - Abonnement
    - Groupe de ressources
    - Emplacement
    - Niveau de tarification  
        ![modifier les valeurs](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.  Cliquez sur **créer**. L’espace de travail est maintenant lié à votre compte Azure.

>[AZURE.NOTE] Si vous ne voyez pas l’espace de travail que vous souhaitez lier, puis votre abonnement Azure n’a pas accès à l’espace de travail OMS que vous avez créé à l’aide du site Web de l’OMS.  Vous devez accorder l’accès à ce compte à partir de l’intérieur de votre espace de travail de l’OMS en utilisant le site Web OMS. Pour ce faire, consultez [Ajouter un utilisateur à un espace de travail existant](#add-a-user-to-an-existing-workspace).



## <a name="upgrade-a-workspace-to-a-paid-data-plan"></a>Mise à niveau d’un espace de travail à un plan de données payées

Il existe trois données d’espace de travail planifier les types d’OMS : **libre**, **Standard**et **Premium**.  Si vous êtes sur un plan *gratuit* , vous avez peut-être atteint votre cache de données de 500 Mo.  Vous devez mettre à niveau votre espace de travail à un ***plan de paiement*** pour collecter des données au-delà de cette limite. À tout moment, vous pouvez convertir votre type de plan.  Pour plus d’informations sur la tarification de l’OMS, consultez [Détails de tarification](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

>[AZURE.IMPORTANT] Plans de l’espace de travail peuvent être modifiés uniquement si elles sont *liées* à un abonnement Azure.  Si vous avez créé votre espace de travail dans Azure, ou si vous avez *déjà* lié votre espace de travail, vous pouvez ignorer ce message.  Si vous avez créé votre espace de travail avec le [site Web d’OMS](http://www.microsoft.com/oms), vous devrez suivre les étapes en cliquant sur [le lien un espace de travail existant à un abonnement Azure](#link-an-existing-workspace-to-an-azure-subscription).

### <a name="using-entitlements-from-the-oms-add-on-for-system-center"></a>À l’aide des droits d’accès à partir du module complémentaire OMS pour System Center

Le complément de l’OMS pour System Center fournit un droit pour le plan Premium d’Analytique de journal OMS, décrit au [Prix de l’OMS](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

Lorsque vous achetez le complément de l’OMS pour System Center, le module complémentaire d’OMS est ajouté sous la forme d’un droit sur votre contrat de System Center. Aucun abonnement Azure qui est créé par le présent accord peut faire usage de l’habilitation. Cela vous permet, par exemple, plusieurs espaces de travail OMS qui utilisent le droit à partir du module complémentaire OMS.

Pour vous assurer que l’utilisation d’espace de travail OMS est appliquée à vos droits d’accès à partir du module complémentaire OMS, vous devrez :

1. Lier votre espace de travail de l’OMS à un abonnement Azure qui fait partie de l’accord d’entreprise qui inclut l’achat du module complémentaire OMS et utilisation d’abonnement Azure
2. Sélectionnez le plan de prime pour l’espace de travail

Lorsque vous examinez l’utilisation du portail Azure ou OMS, vous ne verrez les droits de module complémentaire OMS. Toutefois, vous pouvez voir droits dans Enterprise Portal.  

Si vous devez modifier l’abonnement Azure liée à votre espace de travail de l’OMS, vous pouvez utiliser l’applet de commande PowerShell de Azure [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) .

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Engagement de Azure à partir d’un accord d’entreprise

Si vous choisissez d’utiliser la tarification d’autonome pour les composants de l’OMS, vous devrez payer séparément pour chaque composant de l’OMS et l’utilisation apparaîtra sur votre facture Azure.

Si vous avez une validation monétaire Azure sur l’inscription de l’entreprise à laquelle vos abonnements Azure sont liés, toutes les utilisations de journal Analytique débitera automatiquement contre toute validation monétaire restante.

Si vous devez modifier l’abonnement Azure que l’espace de travail OMS est lié à vous pouvez utiliser l’applet de commande PowerShell de Azure [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) .  



### <a name="to-change-a-workspace-to-a-paid-data-plan"></a>Pour modifier un espace de travail à un plan de données payées

1.  Connectez-vous au [portail Azure](http://portal.azure.com).
2.  Recherchez le **Journal Analytique (OMS)** et sélectionnez-le.
3.  Vous verrez la liste des espaces de travail existants. Sélectionnez un espace de travail.  
    ![liste des espaces de travail](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.  Sous **paramètres**, cliquez sur **niveau de tarification**.  
    ![niveau de tarification](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.  Sous **niveau de tarification**, sélectionnez un plan de données et puis cliquez sur **Sélectionner**.  
    ![Sélectionnez le plan](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.  Lorsque vous actualisez l’affichage dans le portail Azure, vous verrez la mise à jour pour le plan que vous avez sélectionné le **niveau de tarification** .  
    ![mise à jour du niveau de tarification](./media/log-analytics-manage-access/manage-access-change-plan04.png)

Maintenant, vous pouvez collecter des données au-delà de l’extrémité des données « libre ».


## <a name="add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Ajouter une organisation Azure du répertoire actif à un espace de travail existant

Vous pouvez associer votre espace de travail du journal Analytique (OMS) avec un domaine Active Directory de Azure. Vous pouvez ainsi ajouter des utilisateurs à partir d’Active Directory directement à votre espace de travail de l’OMS, sans avoir besoin d’un compte distinct de Microsoft.

Lorsque vous créez l’espace de travail à partir du portail Azure, ou liez votre espace de travail à un abonnement Azure Azure Active Directory sera liée en tant que votre compte d’organisation.

Lorsque vous créez l’espace de travail à partir du portail de l’OMS que vous serez invité à lier à un abonnement Azure et d’un compte d’organisation.

### <a name="to-add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Pour ajouter une organisation Azure du répertoire actif à un espace de travail existant

1. Dans la page Paramètres de l’OMS, cliquez sur **comptes** et puis cliquez sur l’onglet **Espace de travail** .  
2. Passez en revue les informations sur les comptes de l’organisation, puis cliquez sur **Ajouter une organisation**.  
    ![Ajouter une organisation](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Entrez les informations d’identité de l’administrateur de votre domaine Active Directory de Azure. Ensuite, vous verrez un accusé de réception indiquant que votre espace de travail est lié à votre domaine Active Directory de Azure.
    ![accusé de réception d’espace de travail lié](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

>[AZURE.NOTE] Une fois que votre compte est lié à un compte d’organisation, la liaison ne peut pas être supprimée ou modifiée.

## <a name="close-your-oms-workspace"></a>Fermez votre espace de travail de l’OMS

Lorsque vous fermez un espace de travail de l’OMS, toutes les données relatives à votre espace de travail est supprimé à partir du service OMS dans les trente jours de fermeture de l’espace de travail.

Si vous êtes un administrateur et il y a plusieurs utilisateurs associés à l’espace de travail, l’association entre les utilisateurs et l’espace de travail est rompue. Si les utilisateurs sont associés à des espaces de travail, ils peuvent continuer à l’aide d’OMS avec ces autres espaces de travail. Toutefois, si elles ne sont pas associés à des espaces de travail, ils devrez créer un nouvel espace de travail pour utiliser OMS.

### <a name="to-close-an-oms-workspace"></a>Pour fermer un espace de travail de l’OMS

1. De l’OMS, cliquez sur la fenêtre de **paramètres** .
2. Cliquez sur l’onglet **comptes** , puis sur l’onglet **Espace de travail** .
3. Cliquez sur **espace de travail de fermer**.
4. Sélectionnez un des motifs de clôture de votre espace de travail, ou entrez un autre motif dans la zone de texte.
5. Cliquez sur **espace de travail de fermer**.

## <a name="next-steps"></a>Étapes suivantes

- Voir les [ordinateurs Windows de se connecter au journal Analytique](log-analytics-windows-agents.md) pour ajouter des agents et de collecter des données.
- [Solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et de collecter des données.
- [Configurer les paramètres de pare-feu et de proxy dans journal Analytique](log-analytics-proxy-firewall.md) si votre organisation utilise un serveur proxy ou un pare-feu afin que les agents peuvent communiquer avec le service de journal Analytique.
