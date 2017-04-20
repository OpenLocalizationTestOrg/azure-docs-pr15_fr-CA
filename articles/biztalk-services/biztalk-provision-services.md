<properties
    pageTitle="Créer des Services de BizTalk Azure dans le portail Azure | Microsoft Azure"
    description="Apprenez à configurer ou créer des Services de BizTalk Azure dans Azure portal ; MABS, WABS"
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>



# <a name="create-biztalk-services-using-the-azure-portal"></a>Créer des Services BizTalk en utilisant le portail Azure

Créer des Services de BizTalk Azure dans le portail Azure.

> [AZURE.TIP] Pour vous connecter au portail Azure, vous avez besoin d’un compte Azure et un abonnement Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Consultez la [version d’essai Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## <a name="create-a-biztalk-service"></a>Création d’un Service BizTalk
En fonction de l’édition que vous choisissez, pas tous les paramètres de BizTalk Service peuvent être disponibles.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation en bas, sélectionnez à **Nouveau**:  
![Cliquez sur le bouton Nouveau][NEWButton]

3. Sélectionnez les **SERVICES d’application** > **SERVICE BIZTALK** > **créer personnalisé**:  
![Sélectionnez le BizTalk Service et sélectionnez Créer des personnalisées][NewBizTalkService]

4. Entrez les paramètres de BizTalk Service :

    <table border="1">
    <tr>
    <td><strong>Nom du service BizTalk</strong></td>
    <td>Vous pouvez entrer n’importe quel nom mais spécifique. Voici quelques exemples :<br/><br/>
    <em>MyCompany</em>. biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>. biztalk.windows.net<br/>
    <em>MyApplication</em>. biztalk.windows.net<br/><br/>«. biztalk.windows.net » est ajoutée automatiquement au nom que vous entrez. Cette opération crée une URL qui est utilisée pour accéder à votre BizTalk Service, comme <strong>https://<em>myapplication</em>. biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Édition</strong></td>
    <td>Si vous êtes dans la phase de test, de développement, sélectionnez <strong>développeur</strong>. Si vous êtes dans la phase de production, utilisez le <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">Services BizTalk : graphique des éditions de</a> pour déterminer si la <strong>prime</strong>, <strong>Standard</strong>ou <strong>base</strong> est le bon choix pour votre scénario d’entreprise.
    </td>
    </tr>
    <tr>
    <td><strong>Région</strong></td>
    <td>Sélectionnez la zone géographique pour héberger votre BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>URL de domaine</strong></td>
    <td><strong>Facultatif</strong>. Par défaut, l’URL de domaine est <em>YourBizTalkServiceName</em>. biztalk.windows.net. Vous pouvez également entrer un domaine personnalisé. Par exemple, si votre domaine est <em>contoso</em>, vous pouvez entrer : <br/><br/>
    <em>MyCompany</em>. contoso.com<br/>
    <em>MyCompanyMyApplication</em>. contoso.com<br/>
    <em>MyApplication</em>. contoso.com<br/>
    <em>YourBizTalkServiceName</em>. contoso.com<br/>
    </td>
    </tr>
    </table>
Cliquez sur la flèche suivante.

5. Permet d’entrer le stockage et les paramètres de la base de données :

    <table border="1">
    <tr>
    <td><strong>Compte de stockage de surveillance/archivage</strong></td>
    <td>Sélectionnez un compte de stockage existant ou créer un nouveau compte de stockage. <br/><br/>Si vous créez un nouveau compte de stockage, entrez le <strong>Nom du compte de stockage</strong>.</td>
    </tr>
    <tr>
    <td><strong>Base de données de suivi</strong></td>
    <td>Si vous utilisez une base de données SQL Azure, il ne peut pas être utilisé par un autre Service de BizTalk. Vous avez besoin du nom de connexion et mot de passe entré lorsque que le serveur de base de données SQL Azure a été créé.<br/><br/><strong>Conseil</strong> Créer la base de données de suivi et le compte de stockage / l’archivage de l’analyse dans la même région, comme le BizTalk Service.</td>
    </tr>
    </table>
Cliquez sur la flèche suivante.

6. Entrez les paramètres de base de données :

    <table border="1">
    <tr>
    <td><strong>Nom</strong></td>
    <td>Cette option est disponible lorsque vous <strong>créez une nouvelle instance de la base de données SQL</strong> est sélectionné dans l’écran précédent.
    <br/><br/>
   Entrez un nom de base de données SQL à utiliser par le BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Serveur</strong></td>
    <td>Cette option est disponible lorsque vous <strong>créez une nouvelle instance de la base de données SQL</strong> est sélectionné dans l’écran précédent.
    <br/><br/>
   Sélectionnez un serveur de base de données SQL existant ou créer un nouveau serveur de base de données SQL.</td>
    </tr>
    <tr>
    <td><strong>Nom de connexion de serveur</strong></td>
    <td>Entrez le nom d’utilisateur de connexion.</td>
    </tr>
    <tr>
    <td><strong>Mot de passe de connexion serveur</strong></td>
    <td>Entrez le mot de passe de connexion.</td>
    </tr>
    <tr>
    <td><strong>Région</strong></td>
    <td>Disponible quand l’option <strong>créer une nouvelle instance de la base de données SQL</strong> est sélectionnée. Sélectionnez la zone géographique pour héberger la base de données SQL.</td>
    </tr>
    </table>

Activez la case à cocher pour terminer l’Assistant. L’icône de progression s’affiche :  
![Icône de progression s’affiche lorsque vous avez terminé][ProgressComplete]

Lorsque vous avez terminé, le BizTalk Service Azure est créé et prêt pour vos applications. Les paramètres par défaut sont suffisantes. Si vous souhaitez modifier les paramètres par défaut, sélectionnez les **SERVICES BIZTALK** dans le volet de navigation gauche et puis sélectionnez votre BizTalk Service. Des paramètres supplémentaires sont affichés dans les [onglets de tableau de bord, moniteur et l’échelle](biztalk-dashboard-monitor-scale-tabs.md) du haut.

Selon l’état du BizTalk Service, il existe certaines opérations qui ne peut pas être effectuées. Pour une liste de ces opérations, accédez au [Graphique d’état BizTalk Services](biztalk-service-state-chart.md).


## <a name="post-provisioning-steps"></a>Étapes après mise en service

-  [Installer le certificat sur un ordinateur local](#InstallCert)
-  [Ajouter un certificat de production prêts à l’emploi](#AddCert)
-  [Obtenir de l’espace de noms du contrôle d’accès](#ACS)

#### <a name="InstallCert"></a>Installer le certificat sur un ordinateur local
Dans le cadre de la mise en service le BizTalk Service, un certificat auto-signé est créé et associé à votre abonnement au Service de BizTalk. Vous devez télécharger ce certificat et l’installer sur les ordinateurs où vous déployez des applications de BizTalk Service ou envoyez des messages à un point de terminaison de BizTalk Service.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Sélectionnez les **SERVICES BIZTALK** dans le volet de navigation de gauche et sélectionnez votre abonnement au Service de BizTalk.
3. Sélectionnez l’onglet **tableau de bord** .
4. Sélectionnez **Télécharger le certificat SSL**:  
![Modifier le certificat SSL][QuickGlance]
5. Double-cliquez sur le certificat et exécuter l’Assistant pour installer le certificat. Veillez à qu'installer le certificat dans le magasin **d’Autorités de certification racine de confiance** .

#### <a name="AddCert"></a>Ajouter un certificat de production prêts à l’emploi
Le certificat auto-signé qui est créé automatiquement lors de la création de Services de BizTalk est conçu pour une utilisation dans les environnements de développement uniquement. Scénarios de production, vous devez le remplacer par un certificat de production prêts à l’emploi.

1. Sous l’onglet **tableau de bord** , sélectionnez le **Certificat SSL de mise à jour**.
2. Accédez à votre certificat SSL privée (.pfx*CertificateName*) qui inclut le nom de votre BizTalk Service et entrez le mot de passe puis cliquez sur la case à cocher.

#### <a name="ACS"></a>Obtenir de l’espace de noms du contrôle d’accès

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Sélectionnez les **SERVICES BIZTALK** dans le volet de navigation de gauche et sélectionnez votre BizTalk Service.
3. Dans la barre des tâches, sélectionnez **Informations de connexion**:  
![Sélectionnez les informations de connexion][ACSConnectInfo]

4. Copier les valeurs de contrôle d’accès.

Lorsque vous déployez un projet de BizTalk Service à partir de Visual Studio, vous entrez cet espace de noms du contrôle d’accès. L’espace de noms du contrôle d’accès est automatiquement créé pour votre BizTalk Service.

Les valeurs de contrôle d’accès peuvent être utilisés avec n’importe quelle application. Lors de la création de Services de BizTalk Azure, cet espace de noms du contrôle d’accès au contrôle de l’authentification avec le déploiement de votre Service de BizTalk. Si vous souhaitez modifier l’abonnement ou gérer l’espace de noms, sélectionnez **ACTIVE DIRECTORY** dans le volet de navigation de gauche, puis sélectionnez votre espace de noms. La barre des tâches répertorie vos options.

Cliquez sur **Gérer** pour ouvrir le portail de gestion de contrôle de l’accès. Dans le portail de gestion de contrôle de l’accès, le BizTalk Service utilise les **identités de Service**:  
![Portail de gestion de contrôle des identités de Service ACS dans l’accès][ACSServiceIdentities]

L’identité de service de contrôle d’accès est un ensemble d’informations d’identification qui permettent aux clients de s’authentifier directement avec contrôle d’accès et reçoit un jeton ou aux applications.

> [AZURE.IMPORTANT]Le BizTalk Service utilise le **propriétaire** de l’identité de service par défaut et le **mot de passe** . Si vous utilisez la valeur de la clé symétrique au lieu de la valeur de mot de passe, l’erreur suivante peut se produire.<br/><br/>*Impossible de créer le compte de Service de gestion de contrôle de l’accès avec les informations d’identification spécifiées*

[Gestion de votre Namespace d’ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx) répertorie des instructions et des recommandations.

## <a name="requirements-explained"></a>Exigences expliqués

Ces exigences ne s’appliquent pas à l’édition gratuite.
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Ce dont vous avez besoin</strong></td>
        <td><strong>Pourquoi vous en avez besoin</strong></td>
</tr>
<tr>
<td>Abonnement Azure</td>
<td>L’abonnement détermine qui peut se connecter au portail Azure. Le titulaire du compte crée l’abonnement à des <a HREF="https://account.windowsazure.com/Subscriptions">Abonnements d’Azure</a>.
<br/><br/>
Le compte Azure peut avoir plusieurs abonnements et peut être géré par une personne qui est autorisée. Par exemple, le titulaire du compte Azure crée un abonnement nommé <em>BizTalkServiceSubscription</em> et donne aux administrateurs BizTalk au sein de votre société (par exemple, ContosoBTSAdmins@live.com) accès à cet abonnement. Dans ce scénario, les administrateurs de BizTalk se connecter au portail Azure et disposer des droits administrateur complets à tous les services hébergés dans l’abonnement, y compris les Services BizTalk Azure. Les administrateurs de BizTalk ne sont pas les titulaires de compte Azure et par conséquent n’ont pas accès à des informations de facturation.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Gérer les abonnements et les comptes de stockage dans le portail Azure</a> fournit plus d’informations.
</td>
</tr>
<tr>
<td>Base de données SQL Azure</td>
<td>Stocke les tables, les vues et les procédures stockées utilisées par le BizTalk Service, y compris les données de suivi.
<br/><br/>
Lorsque vous créez un BizTalk Service, vous pouvez utiliser une existante de SQL Server Azure, de base de données SQL Azure, ou créer automatiquement un nouveau serveur ou base de données.
<br/><br/>
L’échelle de la base de données SQL est automatiquement configuré. En général, l’échelle par défaut est suffisant pour un BizTalk Service. Modification de l’échelle a un impact sur les prix. Reportez-vous à la section <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">comptes et facturation dans la base de données SQL Azure</a>
<br/><br/>
<strong>Notes</strong>
<br/>
<ul>
<li> Lorsque vous créez un nouveau Azure SQL Server et base de données, Services Azure est automatiquement activée. Le BizTalk Service requiert des Services Azure être activé.</li>
<li>Si vous créez une nouvelle base de données de SQL Azure sur une existante de SQL Server Azure, les règles de pare-feu du serveur ne sont pas modifiées. Par conséquent, il est possible de qu'autres Services Azure n’ont pas d’accès aux bases de données du serveur.</li>
</ul>
</td>
</tr>
<tr>
<td>Espace de noms de contrôle d’accès Azure</td>
<td>Authentifie avec les Services BizTalk Azure. Lorsque vous déployez un projet de BizTalk Service à partir de Visual Studio, vous entrez cet espace de noms du contrôle d’accès. Lorsque vous créez un BizTalk Service, l’espace de noms du contrôle d’accès est automatiquement créé.</td>
</tr>

<tr>
<td>Compte de stockage Azure</td>
<td>Donne accès aux tables, les objets BLOB et les files d’attente utilisés par votre BizTalk Service pour enregistrer les éléments suivants :

<ul>
<li>Fichiers journaux qui surveillent le BizTalk Service. La surveillance est également affiché dans l’onglet **analyse** dans Azure portal.</li>
<li>Lors de la création d’un X12 ou accord AS2 entre partenaires, vous pouvez activer la fonctionnalité d’archivage stocker des propriétés de message. Ces données sont enregistrées dans le compte de stockage.</li>
</ul>
<br/>
Lorsque vous créez un BizTalk Service, vous pouvez utiliser un compte de stockage existant ou en créer automatiquement un nouveau compte de stockage.
<br/><br/>
Les paramètres de stockage par défaut sont suffisants pour un BizTalk Service.
<br/><br/>
Lorsque vous créez un compte de stockage, une clé primaire et la clé secondaire sont automatiquement créés. Ces clés contrôlent l’accès à votre compte de stockage. Le BizTalk Service utilise automatiquement la clé primaire.
<br/><br/>
Pour plus d’informations, consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">stockage</a> .
</td>
</tr>

<tr>
<td>Certificat privé de SSL</td>
<td>
Lors de la création d’un BizTalk Service d’Azure, une URL HTTPS qui inclut le nom de votre BizTalk Service est également créée. Cette URL est automatiquement configurée pour utiliser un certificat auto-signé de destiné uniquement au développement. Pour la production, vous avez besoin d’un certificat SSL privé.
<br/><br/>
<strong>Informations sur le certificat SSL important</strong>

<ul>
<li>La date d’expiration du certificat doit être inférieure à 5 ans.</li>
<li>Tous les certificats privés exigent un mot de passe. Ce mot de passe et pour obtenir les meilleurs résultats, partager ce mot de passe avec vos administrateurs.</li>
<li>Les certificats auto-signés sont utilisés dans un environnement de test/développement. Lorsque vous utilisez des certificats autosignés, importez le certificat dans votre magasin de certificats personnel et le magasin de certificats Autorités de Certification racine de confiance.</li>
</ul>
<br/>Lors de l’envoi de la demande de certificat de production à votre autorité de certification, donner les propriétés du certificat suivant :
<br/>

<ul>
<li><strong>Utilisation de clé améliorée</strong>: au minimum, Azure BizTalk Services requiert l’authentification du serveur.</li>
<li><strong>Nom commun</strong>: entrez le nom de domaine complet (FQDN) de l’URL de votre Service Azure BizTalk. Dans cet article, reportez-vous à la section <a HREF="#BizTalk">Création d’un BizTalk Service</a> .</li>
</ul>
<br/>
Un certificat de nouveau ou différent peut être ajouté après avoir créé le BizTalk Service.
</td>
</tr>
</table>



## <a name="hybrid-connections"></a>Connexions hybride

Lorsque vous créez un Service de BizTalk Azure, l’onglet **Connexions de l’hybride** est disponible :

![Onglet de connexions hybride][HybridConnectionTab]

Les connexions hybride permettent de connecter un site Web Azure ou l’Azure service mobile à n’importe quelle ressource locale qui utilise un port TCP statique, tel que SQL Server, MySQL, API de Web HTTP, les Services mobiles et la plupart des Services Web personnalisés.  Les connexions hybride et le Service d’adaptateur BizTalk sont différents. Le Service d’adaptateur BizTalk sert à connecter des Services de BizTalk Azure à un système de ligne d’activité (LOB) sur site.

 Consultez [Les connexions hybride](integration-hybrid-connection-overview.md) pour en savoir plus, notamment la création et la gestion des connexions des hybrides.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que la création d’un BizTalk Service, familiarisez-vous avec les différents [Services BizTalk : onglets de tableau de bord, du moniteur et échelle](biztalk-dashboard-monitor-scale-tabs.md). Votre BizTalk Service est prêt pour vos applications. Pour démarrer la création d’applications, consultez [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Voir aussi
- [Services de BizTalk : Graphique des éditions](biztalk-editions-feature-chart.md)<br/>
- [Services de BizTalk : Graphique de l’état](biztalk-service-state-chart.md)<br/>
- [Services de BizTalk : Sauvegarde et restauration](biztalk-backup-restore.md)<br/>
- [Les Services BizTalk : limitation](biztalk-throttling-thresholds.md)<br/>
- [Les Services BizTalk : Nom de l’émetteur et la clé de l’émetteur](biztalk-issuer-name-issuer-key.md)<br/>
- [Comment faire démarrer à l’aide du SDK des Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Connexions hybride](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
