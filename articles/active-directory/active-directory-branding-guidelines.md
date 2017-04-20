<properties
   pageTitle="Instructions de personnalisation pour les Applications | Microsoft Azure"
   description="Un guide complet sur les ressources destinées aux développeurs pour Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/23/2016"
   ms.author="mbaldwin"/>


# <a name="branding-guidelines-for-applications"></a>Instructions de personnalisation pour les Applications


Cette rubrique présente les instructions de personnalisation que vous devez utiliser lorsque vous développez des applications avec Azure Active Directory (AD Azure). Ces conseils aideront à diriger vos clients lorsque qu’ils souhaitent utiliser leur compte de travail ou à l’école, géré dans Azure AD, ou leur personnel compte d’abonnement et ouvrez une session dans votre application.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Comptes personnels et de travail ou les comptes de l’établissement de Microsoft

Microsoft gère deux types de comptes d’utilisateurs :

- **Comptes personnels** (anciennement de Windows Live ID). Ces comptes représentent les relations entre les utilisateurs *individuels* et de Microsoft et sont utilisées pour accéder aux périphériques et services de Microsoft. Ces comptes sont destinés à usage personnel.

- **Comptes de travail ou l’école.** Ces comptes sont gérés par Microsoft pour les entreprises qui utilisent Active Directory de Azure. Ces comptes sont utilisés pour se connecter à Office 365 et d’autres services d’entreprise de Microsoft.

Les comptes de travail ou de l’école sont généralement affectés aux utilisateurs finaux (employés, étudiants, employés fédéraux) par leurs organisations (société, école, agence gouvernementale) de Microsoft. Ces comptes sont maîtrisées directement dans le nuage, dans la plateforme Azure AD, ou synchronisées dans AD Azure à partir d’un répertoire local, tel que Windows Server Active Directory. Microsoft est le *dépositaire* des comptes de l’école ou de travail, mais les comptes sont détenus et contrôlés par l’organisation.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Référence à des comptes Active Directory Azure dans votre application

Microsoft n’expose pas aux utilisateurs finaux de l’Azure ou les noms de marque de Active Directory et ne vous convient.

- Une fois que les utilisateurs sont connectés, vous devez utiliser le nom et le logo autant que possible de l’organisation. Ceci est préférable à l’utilisation des termes génériques tels que « votre organisation ».

- Lorsque les utilisateurs n’êtes pas connectés, vous devez faire référence à leurs comptes en tant que « travail ou les comptes de l’école » et le logo Microsoft permet de transmettre que ces comptes sont gérés par Microsoft. N’utilisez pas de termes comme « compte d’entreprise », « compte » ou « compte d’entreprise » que créer la confusion de l’utilisateur.

## <a name="user-account-pictogram"></a>Pictogramme de compte d’utilisateur
Dans une version antérieure de ces instructions, il est recommandé à l’aide d’un pictogramme « badge bleu ». Basé sur les commentaires des utilisateurs et des développeurs, nous recommandent désormais l’utilisation du logo Microsoft à la place. Cela aidera les utilisateurs à comprendre qu’ils peuvent réutiliser le compte qu’ils utilisent les services avec Office 365 ou autres Microsoft pour vous connecter à votre application.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>L’inscription et la connexion avec Active Directory Azure

Votre application peut présenter des chemins d’accès distincts pour l’inscription et à la connexion et les sections suivantes fournissent des conseils visual pour les deux scénarios.

**Si votre application prend en charge l’authentification de l’utilisateur final à distance (par exemple, libre de modèle d’évaluation ou freemium)**: vous pouvez afficher un bouton de **connexion** qui permet aux utilisateurs d’accéder à votre application avec le compte de leur travail ou leur compte personnel. Annonce Azure affichera une invite de consentement lors de la première fois qu’ils accèdent à votre application.

**Si votre application requiert des autorisations que seuls les administrateurs peuvent consentir à, ou si votre application nécessite une licence d’organisation**: vous devez séparer acquisition d’administration à partir de la connexion utilisateur. Le **bouton « obtenir cette application »** redirigera les administrateurs pour ouvrir une session, puis leur demander de donner son accord au nom des utilisateurs dans leur organisation. Cela a l’avantage de la suppression des invites de consentement aux utilisateurs finaux de votre application.

## <a name="visual-guidance-for-app-acquisition"></a>Guide visuel pour l’acquisition de l’application

Le lien « obtenir l’application » doit rediriger l’utilisateur pour l’accès de la subvention AD Azure (autoriser) la page, afin de permettre l’administrateur d’une organisation autoriser votre application à accéder aux données de leur organisation qui sont hébergées par Microsoft. Pour plus d’informations sur la demande d’accès sont décrits dans l’article de [l’Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md) .

Une fois que les administrateurs de consent à votre application, ils peuvent choisir pour l’ajouter à application Lanceur expérience avec Office 365 leurs utilisateurs (accessible à partir de l’alvéolée et à partir de [https://portal.office.com/myapps](https://portal.office.com/myapps)). Si vous souhaitez publier cette fonctionnalité, vous pouvez utiliser des termes tels que « D’ajouter cette application dans votre organisation » et afficher un bouton comme suit :

![Types d’applications et de scénarios](./media/active-directory-branding-guidelines/add-to-my-org.png)

Toutefois, nous vous recommandons d’écrire un texte explicatif au lieu de compter sur les boutons. Par exemple :
> *Si vous utilisez déjà Office 365 ou autre service de l’entreprise auprès de Microsoft, vous pouvez simplement accéder < your_app_name > pour les données de votre organisation. Cela permettra à vos utilisateurs pour accéder à < your_app_name > avec leurs comptes de travail existants.*


## <a name="visual-guidance-for-sign-in"></a>Guide visuel pour la connexion
Votre application doit afficher un signe de bouton qui redirige les utilisateurs vers le point final qui correspond au protocole que vous permet d’intégrer AD Azure. La section suivante fournit des détails sur quoi doit ressembler ce bouton.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pictogramme et « Connectez-vous avec Microsoft »
Il s’agit de l’association du logo Microsoft et les termes « Microsoft d’in avec signe » qui représente de façon unique AD Azure parmi d’autres fournisseurs d’identité que votre application peut prendre en charge. Si vous n’avez pas suffisamment d’espace pour « Microsoft d’in avec signe », il est OK pour raccourcir à « Se connecter ».

![Types d’applications et de scénarios](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Types d’applications et de scénarios](./media/active-directory-branding-guidelines/sign-in-light.png)

Vous pouvez également utiliser un jeu de couleurs foncé pour les boutons.

![Types d’applications et de scénarios](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Types d’applications et de scénarios](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>Conseils de personnalisation

**« Compte de travail ou de l’école » utilisez en association avec le bouton « Microsoft d’in avec signe » afin de fournir des explications supplémentaires pour aider les utilisateurs finaux à reconnaître si elles peuvent l’utiliser.** **** N’utilisez pas autres termes tels que « compte d’entreprise », « compte » ou « compte d’entreprise ».

**** N’utilisez pas « Office 365 code » ou « Azure ». Office 365 est également le nom d’un consommateur de Microsoft qui n’utilise pas Azure AD pour l’authentification.

**Ne** modifiez pas le logo Microsoft.

**Ne pas** exposer aux utilisateurs finaux de marques Azure ou Active Directory. Il est toutefois OK pour utiliser ces termes avec les développeurs, les professionnels de l’informatique et les administrateurs.

## <a name="navigation-dos-and-donts"></a>Conseils de navigation

**Fournissez un moyen permettant aux utilisateurs de se déconnecter et de basculer vers un autre compte d’utilisateur.** Alors que la plupart des gens ont un compte personnel unique à partir de Microsoft/Facebook/Google/Twitter, personnes sont souvent associés à plus d’une organisation. Prise en charge de plusieurs utilisateurs signé sera bientôt disponible.
