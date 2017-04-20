<properties
    pageTitle="Créer des applications .NET HDInsight de l’authentification non interactive | Microsoft Azure"
    description="Apprenez à créer des applications de HDInsight de .NET de l’authentification non interactive."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Créer des applications de HDInsight de .NET de l’authentification non interactive

Vous pouvez exécuter votre application .NET Azure HDInsight sous l’identité de l’application (non interactif) ou sous l’identité de l’utilisateur connecté de l’application (interactive). Pour voir un exemple de l’application interactive, [travaux de soumettre des porcs/ruche/Sqoop à l’aide du Kit de développement .NET HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Cet article vous montre comment créer l’application .NET de l’authentification non interactive à se connecter à HDInsight d’Azure et soumettre une tâche de la ruche.

À partir de votre application .NET, vous devez :

- votre ID de clients d’abonnement Azure
- l’ID de client d’application Azure Directory
- clé secrète application Azure Directory.  

Le processus principal inclut les étapes suivantes :

2. Créer une application Azure Directory.
2. Attribuer des rôles à l’application d’Active Directory.
3. Développer votre application cliente.


##<a name="prerequisites"></a>Conditions préalables

- Cluster de HDInsight. Vous pouvez en créer une en suivant les instructions dans le [didacticiel de mise en route](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## <a name="create-azure-directory-application"></a>Créer l’application Azure Directory 
Lorsque vous créez une application Active Directory, il crée l’application et un service principal. Vous pouvez exécuter l’application sous l’identité de l’application.

Actuellement, vous devez utiliser le portail classique Azure pour créer une nouvelle application d’Active Directory. Cette capacité sera ajoutée au portail Azure dans une version ultérieure. Vous pouvez également effectuer ces étapes via Azure PowerShell ou Azure CLI. Pour plus d’informations sur l’utilisation de PowerShell ou l’interface CLI avec le service principal, voir [service d’authentification principal avec le Gestionnaire de ressources Azure](../resource-group-authenticate-service-principal.md).

**Pour créer une application Azure Directory**

1.  Connectez-vous à l' [Azure portal classique]( https://manage.windowsazure.com/).
2.  Dans le volet gauche, sélectionnez **Active Directory** .

    ![Répertoire actif portail classique Azure](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.  Sélectionnez le répertoire que vous souhaitez utiliser pour la création de la nouvelle application. Il doit être celui qui existe déjà.
4.  Cliquez sur **Applications** à partir du haut de la liste des applications existantes.
5.  Cliquez sur **Ajouter** à partir du bas pour ajouter une nouvelle application.
6.  Entrez le **nom**, sélectionnez **l’application Web et/ou des API Web**et puis cliquez sur **suivant**.

    ![nouvelle application azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.  Entrez **l’ouverture de session d’URL** et les **URI d’ID App**. Pour **Les URL de session**, fournir l’URI à un site web qui décrit votre application. L’existence du site web-n’est pas validé. Pour l’URI d’ID APP, fournissent l’URI qui identifie votre application. Puis sur **Terminer**.
Il faut quelques minutes pour créer l’application.  Une fois que l’application est créée, le portail affiche la page d’activité rapide de la nouvelle application. Ne fermez pas le portail. 

    ![nouvelles propriétés d’application azure répertoire actif](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Pour obtenir l’application cliente ID et la clé secrète**

1.  À partir de la page d’application AD nouvellement créée, cliquez sur **configurer** dans le menu supérieur.
2.  Effectuez une copie de **l’ID de Client**. Vous en aurez besoin dans votre application .NET.
3.  Sous **clés**, cliquez sur **Sélectionner la durée de** la liste déroulante et sélectionnez **1 an** ou **2 ans**. La valeur de clé ne s’affichera pas tant que vous enregistrez la configuration.
4.  Cliquez sur **Enregistrer** dans la partie inférieure de la page. Lorsque la clé secrète s’affiche, effectuez une copie de la clé. Vous en aurez besoin dans votre application .NET.

##<a name="assign-ad-application-to-role"></a>Attribuer l’application AD au rôle

Vous devez affecter l’application à un [rôle](../active-directory/role-based-access-built-in-roles.md) pour lui accorder des autorisations pour effectuer des actions. Vous pouvez définir l’étendue au niveau de l’abonnement, le groupe de ressources ou la ressource. Les autorisations sont héritées à des niveaux inférieurs de la portée (par exemple, ajouter qu'une application au rôle de lecteur pour un groupe de ressources signifie qu’il peut lire le groupe de ressources et les ressources qu’il contient). Dans ce didacticiel, vous allez définir la portée au niveau du groupe de ressources.  Étant donné que le portail classique Azure ne prend pas en charge les groupes de ressources, cette partie doit être effectuée à partir du portail Azure. 

**Pour ajouter le rôle de propriétaire à l’application d’Active Directory**

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le volet gauche, cliquez sur **Groupe de ressources** .
3.  Cliquez sur le groupe de ressource contenant le cluster HDInsight où vous exécuterez votre requête ruche plus loin dans ce didacticiel. S’il y a trop de groupes de ressources, vous pouvez utiliser le filtre.
4.  Cliquez sur **accès** à partir de la blade de cluster.

    ![icône nuage et thunderbolt = quickstart](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.  À partir de la blade **d’utilisateurs** , cliquez sur **Ajouter** .
6.  Suivez les instructions pour ajouter le rôle de **propriétaire** à l’application d’Active Directory que vous avez créé dans la dernière procédure. Lorsque vous avez terminé avec succès, vous veillent à l’application répertoriée dans la blade d’utilisateurs avec le rôle de propriétaire.


##<a name="develop-hdinsight-client-application"></a>Développer des applications de client HDInsight

Créer une application de console C# .net suivant les instructions de [Hadoop de soumettre des tâches dans HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Puis remplacez la méthode GetTokenCloudCredentials par le texte suivant :

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Pour récupérer l’ID de client par le biais de PowerShell :

    Get-AzureRmSubscription

Ou bien, Azure CLI :

    azure account show --json

      
## <a name="see-also"></a>Voir aussi

- [Soumettre des travaux d’Hadoop dans HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Créer une application Active Directory et principal du service à l’aide du portail](../resource-group-create-service-principal-portal.md)
- [Authentifier le principal du service avec le Gestionnaire de ressources Azure](../resource-group-authenticate-service-principal.md)
- [Contrôle d’accès basé sur rôle de Azure](../active-directory/role-based-access-control-configure.md)
