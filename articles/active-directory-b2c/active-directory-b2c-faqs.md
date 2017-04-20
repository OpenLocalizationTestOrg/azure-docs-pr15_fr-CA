<properties
    pageTitle="B2C d’Azure Active Directory : FAQ | Microsoft Azure"
    description="Forum aux questions sur Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C : questions fréquentes

Cette page répond aux questions fréquemment posées sur le B2C Azure Active Directory (AD Azure). Conserver la vérification des mises à jour.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Puis-je utiliser les fonctionnalités d’Azure AD B2C dans mes clients AD Azure existante, basée sur l’employé ?

Azure AD B2C fonctionnalités ne peuvent pas être activées dans votre client AD Azure existante. Nous vous conseillons de créer un client distinct pour Azure AD B2C fonctionnalités permet de gérer vos consommateurs.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Puis-je utiliser Azure AD B2C pour fournir la connexion sociale (Facebook et Google +) dans Office 365 ?

Azure B2C d’Active Directory ne peut pas être utilisé avec Microsoft Office 365. En général, il ne peut pas être utilisé pour fournir l’authentification pour les applications SaaS (Office 365, force de vente, journée de travail, etc.). Il fournit la gestion des identités et des accès uniquement pour les applications mobiles et pour les consommateurs et n’est pas applicable aux scénarios d’employé ou un partenaire.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Quels sont les comptes locaux dans Azure AD B2C ? Comment sont-ils différents comptes de travail ou de l’école dans Azure AD ?

Dans un client AD Azure, chaque utilisateur dans le client (à l’exception des utilisateurs disposant de comptes existants de Microsoft) signe avec une adresse de courrier électronique du formulaire `<xyz>@<tenant domain>`, où `<tenant domain>` est un des domaines vérifiés dans le locataire ou l’initiale `<...>.onmicrosoft.com` domaine. Ce type de compte est un compte de travail ou l’école.

Dans un locataire Azure AD B2C, la plupart des applications que l’utilisateur se connecter avec n’importe quelle adresse arbitraire (par exemple, joe@comcast.net, bob@gmail.com, sarah@contoso.com, ou jim@live.com). Ce type de compte est un compte local. Aujourd'hui, nous prennent également en charge les noms utilisateur arbitraires (chaînes simples) en tant que comptes locaux (par exemple, joe, bob, sarah ou jim). Vous pouvez choisir une de ces deux types de compte local dans le service d’Azure AD B2C.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Les fournisseurs d’identité sociale vous prennent en charge maintenant ? Celles que vous prévoyez prendre en charge à l’avenir ?

Actuellement, nous prenons en charge Facebook, + de Google, LinkedIn et Amazon. Nous allons ajouter la prise en charge d’autres fournisseurs d’identité de sociaux populaires basé sur la demande du client.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Puis-je configurer des étendues pour rassembler plus d’informations sur les consommateurs de différents fournisseurs d’identité sociale ?

Non, mais cette fonctionnalité est notre plan d’évolution. Les portées par défaut utilisées pour notre jeu pris en charge de fournisseurs d’identité sociale sont :

- Facebook : e-mail
- Google + : e-mail
- Compte Microsoft : profil de messagerie openid
- Amazon : profil
- LinkedIn : r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Mon application doit-elle être exécuté dans Azure pour qu’il fonctionne avec Azure AD B2C ?

Non, vous pouvez héberger votre application n’importe où (le nuage ou sur site). Tout ce dont il a besoin pour interagir avec Azure AD B2C est la possibilité d’envoyer et de recevoir des demandes HTTP sur les points de terminaison accessibles publiquement.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>J’ai plusieurs Azure AD B2C locataires. Comment puis-je gérer les sur le portail Azure ?

Chaque client Azure AD B2C a sa propre lame de fonctionnalités B2C sur le portail Azure. Consultez [Azure AD B2C : enregistrer votre application](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) pour savoir comment accéder à lame de fonctionnalités B2C d’un client spécifique sur le portail Azure. Basculer entre les répertoires Azure AD B2C sur le portail Azure n’empêchent pas vos fonctionnalités B2C lame ouvrir sur la plupart des navigateurs.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Comment personnaliser les messages électroniques de vérification (le contenu et le « de : » champ) envoyés par Azure AD B2C ?

Vous pouvez utiliser la [fonctionnalité de personnalisation de société](../active-directory/active-directory-add-company-branding.md) pour personnaliser le contenu des messages électroniques de vérification. Plus précisément, ces deux éléments du message peuvent être personnalisés :

- **Bannière Logo**: affiché en bas à droite.
- **Couleur d’arrière-plan**: apparaît en haut.

    ![Capture d’écran d’un message de vérification personnalisée](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La signature de courrier électronique contient le nom du locataire B2C que vous avez fournies lorsque vous avez créé tout d’abord le locataire B2C. Vous pouvez modifier le nom à l’aide de ces instructions :

- Ouvrez une session dans [Azure portal classique](https://manage.windowsazure.com/) comme l’administrateur d’abonnement.
- Accédez à vos clients B2C.
- Cliquez sur l’onglet **configurer** .
- Modifiez le champ **nom de** la section **Propriétés de l’annuaire** .
- Cliquez sur **Enregistrer** en bas de la page.

Il n’existe actuellement aucun moyen de changer le « de : » champ e-mail. Si vous vous intéressez dans cette fonction et entièrement personnaliser le corps de l’e-mail de vérification, voter pour la fonctionnalité sur [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Comment puis-je migrer mes noms d’utilisateur existants, les mots de passe et les profils à partir de ma base de données vers Azure AD B2C ?

Vous pouvez utiliser l’API Azure AD graphique pour écrire votre outil de migration. Consultez l' [exemple d’API de graphe](active-directory-b2c-devquickstarts-graph-dotnet.md) pour plus de détails. Nous proposons plusieurs options de migration et outils-de-l’emploi à l’avenir.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Quelle stratégie de mot de passe est utilisé pour les comptes locaux dans Azure AD B2C ?

La stratégie de mot de passe Azure AD B2C pour les comptes locaux est basée sur la stratégie de publicité Azure. Azure AD B2C de l’inscription, d’inscription ou d’ouverture de session et le mot de passe utilise des stratégies de réinitialisation de la résistance de mot de passe « fort » et n’expire jamais les mots de passe. Lire la [stratégie de mot de passe Active Directory Azure](https://msdn.microsoft.com/library/azure/jj943764.aspx) pour plus de détails.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Puis-je utiliser Azure Connect d’Active Directory pour migrer les identités qui sont stockées sur mon sur site d’Active Directory vers Azure AD B2C ?

Non, AD Azure Connect n’est pas conçu pour fonctionner avec Azure AD B2C. Nous proposons plusieurs options de migration et outils-de-l’emploi à l’avenir.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C fonctionne avec des systèmes tels que Microsoft Dynamics CRM ?

Pas actuellement. L’intégration de ces systèmes est notre plan d’évolution.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Est Azure AD B2C avec SharePoint local 2016 ou une version antérieure ?

Pas actuellement. Azure B2C d’Active Directory n’a pas prise en charge des jetons SAML 1.1 qui les portails et les applications de commerce électronique basées sur la nécessité de sur les sites SharePoint. Notez que Azure AD B2C n’est pas conçu pour le SharePoint externe partenaire scénario de partage ; reportez-vous à la section [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) à la place.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Dois-je utiliser Azure AD B2C ou B2B pour gérer les identités externes ?

Lisez cet article sur les [identités externes](../active-directory/active-directory-b2b-compare-external-identities.md) pour en savoir plus sur l’application les fonctionnalités appropriées à vos scénarios d’identités externes.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Les rapports et les fonctionnalités d’audit Azure AD B2C fournit-il ? Ils sont identiques Azure AD Premium ?

Non, Azure AD B2C pas prend-il en charge le même jeu de rapports comme Azure AD Premium. Azure AD B2C publiera reporting de base et d’audit des API plus rapidement.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Est-il possible de localiser l’interface utilisateur des pages servies par Azure AD B2C ? Quelles langues sont prises en charge ?

Actuellement, Azure AD B2C est optimisé pour l’anglais uniquement. Nous prévoyons de déployer des fonctionnalités de localisation dès que possible.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Puis-je utiliser ma propre URL sur Mes pages d’inscription et de connexion qui sont pris en charge par Azure AD B2C ? Par exemple, puis modifier l’URL à partir de login.microsoftonline.com à login.contoso.com ?

Pas actuellement. Cette fonctionnalité est notre plan d’évolution. Notez également que la vérification de votre domaine dans l’onglet **domaines** de vos clients sur le portail classique Azure ne le ferons pas.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Comment supprimer mon locataire Azure AD B2C ?

Suivez ces étapes pour supprimer vos clients Azure AD B2C :

- Procédez comme suit pour [accéder à la lame de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
- Naviguez vers les blades **d’Applications**, de **fournisseurs d’identité** et de **toutes les stratégies** et supprimez toutes les entrées de chacun d’eux.
- Maintenant vous connecter à l' [Azure portal classique](https://manage.windowsazure.com/) comme l’administrateur d’abonnement. (C’est le même travail ou le compte de l’établissement ou le même compte Microsoft que vous avez utilisé pour vous inscrire pour Azure).
- Recherchez l’extension de Active Directory sur la gauche, puis cliquez sur vos clients B2C.
- Cliquez sur l’onglet **utilisateurs** .
- Sélectionnez chaque utilisateur à tour de rôle (exclure l’utilisateur que vous êtes actuellement connecté en tant que, par exemple, l’administrateur d’abonnement). Cliquez sur **Supprimer** au bas de la page et cliquez sur **Oui** lorsque vous y êtes invité.
- Cliquez sur l’onglet **Applications** .
- Sélectionnez les **Applications que mon entreprise possède** dans le champ de liste déroulante **Afficher** et cliquez sur la case à cocher.
- Vous verrez une application appelée **b2c-extensions-app** répertoriés ci-dessous. Cliquez sur **Supprimer** au bas de la page et cliquez sur **Oui** lorsque vous y êtes invité.
- Accédez à nouveau à l’extension de Active Directory et sélectionnez vos clients B2C.
- Cliquez sur **Supprimer** au bas de la page. Suivez les instructions à l’écran pour terminer le processus.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Puis-je B2C d’AD Azure en tant que partie d’une Suite de mobilité d’entreprise ?

Non, Azure AD B2C est un paiement à l’utilisation des services Azure et ne fait pas partie de la Suite de mobilité d’entreprise.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Comment signaler les problèmes rencontrés avec Azure AD B2C ?

Consultez le [fichier prend en charge les demandes d’Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="more-information"></a>Plus d’informations

Vous pouvez également passer en revue [les contraintes, les restrictions et les limitations de service](active-directory-b2c-limitations.md)en cours.
