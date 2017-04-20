<properties
    pageTitle="Meilleures pratiques : Mot de passe Active Directory Azure Management | Microsoft Azure"
    description="Meilleures pratiques de déploiement et d’utilisation, exemple de documentation de l’utilisateur final et des guides de formation pour la gestion de mot de passe dans Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="deploying-password-management-and-training-users-to-use-it"></a>Déploiement de la gestion de mot de passe et la formation des utilisateurs de l’utiliser

> [AZURE.IMPORTANT] **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Après la réinitialisation du mot de passe de l’activation, l’étape suivante, que vous devez prendre est à encourager les utilisateurs à l’aide du service de votre organisation. Pour ce faire, vous devrez vous assurer que vos utilisateurs sont configurés pour utiliser le service correctement et également que vos utilisateurs disposent de la formation qu’ils nécessaires à leur réussite dans la gestion de leur propre mot de passe. Cet article va vous expliquer pour vous les concepts suivants :

* [**Comment obtenir les utilisateurs configurés pour la gestion de mot de passe**](#how-to-get-users-configured-for-password-reset)
  * [Ce qui rend un compte configuré pour la réinitialisation de mot de passe](#what-makes-an-account-configured)
  * [Vous pouvez pour remplir des données d’authentification vous-même des méthodes](#ways-to-populate-authentication-data)
* [**Les meilleures façons de restaurer le mot de passe réinitialisé à votre organisation**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Exemple + déploiement de messagerie électronique messagerie électronique](#email-based-rollout)
  * [Créer votre propre portail de gestion de mot de passe personnalisé pour vos utilisateurs](#creating-your-own-password-portal)
  * [L’utilisation d’enregistrement appliquée pour forcer les utilisateurs à s’inscrire à l’ouverture de session](#using-enforced-registration)
  * [Comment faire pour télécharger des données d’authentification des comptes d’utilisateurs](#uploading-data-yourself)
* [**Exemple d’utilisateur et de la prise en charge des supports de formation (disponible prochainement)**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>Comment obtenir les utilisateurs configurés pour la réinitialisation de mot de passe
Cette section décrit les différentes méthodes par lesquelles vous pouvez vous assurer que tous les utilisateurs de votre organisation peuvent utiliser le mot de passe réinitialisé efficace dans le cas où ils oublient leur mot de passe vous.

### <a name="what-makes-an-account-configured"></a>Ce qui rend un compte configuré
Un utilisateur peut utiliser le mot de passe réinitialisé, **toutes les** conditions suivantes doivent être remplies :

1.  Réinitialisation du mot de passe doit être activée dans le répertoire.  Apprenez comment activer le mot de passe réinitialisé par lecture de [permettre aux utilisateurs de réinitialiser leurs mots de passe Active Directory Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) ou [permettre aux utilisateurs de réinitialiser ou de modifier leurs mots de passe Active Directory](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
2.  L’utilisateur doit être sous licence.
 - Pour les utilisateurs de nuage, l’utilisateur doit disposer d' **une licence Office 365 payante**, ou une **Base DAS** ou **DAS Premium licence** attribuée.
 - Pour sous-prem utilisateurs (fédérés ou synchronisées de hachage, l’utilisateur **doit disposer d’une licence de DAS prime affectée)**.
3.  L’utilisateur doit disposer de l' **ensemble minimal de données d’authentification définies** conformément à la stratégie de réinitialisation de mot de passe actuel.
 - Données d’authentification sont considérée comme défini si le champ correspondant dans le répertoire contient les données bien formées.
 - Un jeu de données d’authentification est défini à **au moins un** des options d’authentification activés si une stratégie d’une grille est configurée, ou au **moins deux** des options d’authentification activés si une stratégie deux porte n’est configurée.
4.  Si l’utilisateur utilise un compte local, puis [L’écriture différée de mot de passe](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) doit être activé et allumé

### <a name="ways-to-populate-authentication-data"></a>Manières de remplir des données d’authentification
Vous disposez de plusieurs options spécifier les données pour les utilisateurs de votre organisation à utiliser pour la réinitialisation de mot de passe.

- Modifier les utilisateurs dans le [Portail de gestion Azure](https://manage.windowsazure.com) ou le [Portail d’administration d’Office 365](https://portal.microsoftonline.com)
- Utilisez Azure AD Sync pour synchroniser les propriétés de l’utilisateur dans Active Directory Azure à partir d’un domaine d’Active Directory sur site
- Utiliser Windows PowerShell pour modifier les propriétés de l’utilisateur en [suivant les étapes décrites ici](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Permettre aux utilisateurs d’enregistrer leurs propres données en les guidant vers le portail de l’enregistrement à [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Obliger les utilisateurs à s’inscrire pour réinitialiser lorsqu’ils se connectent à leur compte Azure AD en définissant un mot de passe du [**obliger les utilisateurs à enregistrer lorsque vous vous connectez ?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) option de configuration sur **Yes**.

Les utilisateurs doivent s’inscrire pas de mot de passe réinitialiser pour le système fonctionne.  Par exemple, si vous avez un existant mobile ou les numéros de téléphone des office dans votre répertoire local, vous pouvez les synchroniser dans Azure AD et nous les utiliserons pour réinitialiser automatiquement un mot de passe.

Vous pouvez également lire plus sur la [manière dont les données sont utilisées par mot de passe réinitialisé](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) et la [comment vous pouvez remplir les champs individuels de l’authentification avec PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>Quelle est la meilleure façon de déployer pour les utilisateurs de réinitialisation de mot de passe ?
Les étapes de déploiement général de réinitialisation de mot de passe sont les suivantes :

1.  Activer la réinitialisation de votre répertoire en cliquant sur l’onglet **configurer** dans le [Portail de gestion Azure](https://manage.windowsazure.com) et si vous sélectionnez **Oui** pour l’option **Les utilisateurs activés pour la réinitialisation du mot de passe** mot de passe.
2.  Attribuer les licences appropriées à chaque utilisateur auquel vous souhaitez offrir de réinitialisation de mot de passe l’en accédant à l’onglet **licences** dans le [Portail de gestion Azure](https://manage.windowsazure.com).
3.  Éventuellement limiter le mot de passe réinitialisé à un groupe d’utilisateurs de déployer la fonctionnalité lentement dans le temps en définissant la bascule de **Limiter l’accès au mot de passe réinitialisé** sur **Yes** et en sélectionnant un groupe de sécurité pour activer la réinitialisation du mot de passe (Notez ces utilisateurs doivent avoir toutes les licences attribuées).
4.  Demandez à vos utilisateurs d’utiliser le mot de passe réinitialiser en l’envoyant un message électronique demandant à enregistrer, l’activation de mise en œuvre d’inscription sur le panneau d’accès, ou en chargeant les données d’authentification pour les utilisateurs via la synchronisation d’annuaire, PowerShell et le [Portail de gestion Azure](https://manage.windowsazure.com).  Vous trouverez ci-dessous plus d’informations.
5.  Au fil du temps, consultez inscription en accédant à l’onglet Rapports et l’affichage du rapport [**d’Activité de l’enregistrement de réinitialisation de mot de passe**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) des utilisateurs.
6.  Une fois un bon nombre d’utilisateurs ont enregistré, les regarder à utiliser en accédant à l’onglet Rapports et en affichant le rapport [**d’Activité de réinitialisation de mot de passe**](active-directory-passwords-get-insights.md#view-password-reset-activity) de réinitialisation de mot de passe.

Il existe plusieurs façons d’informer vos utilisateurs qu’ils peuvent inscrire et utiliser le mot de passe réinitialisé dans votre organisation.  Elles sont détaillées ci-dessous.

### <a name="email-based-rollout"></a>Déploiement de messagerie électronique
L’approche la plus simple pour informer vos utilisateurs à inscrire ou utiliser le mot de passe de réinitialisation est peut-être en leur envoyant un message électronique leur donner l’instruction à le faire.  Voici un modèle que vous pouvez utiliser pour cela.  N’hésitez pas à remplacer les couleurs / logos avec ceux de votre choix personnaliser pour l’adapter à vos besoins.

  ![][001]

Vous pouvez [Télécharger le modèle d’e-mail](http://1drv.ms/1xWFtQM).

### <a name="creating-your-own-password-portal"></a>Création de votre propre portail de mot de passe
Une stratégie qui fonctionne bien pour les clients plus importants, déploiement de fonctionnalités de gestion de mot de passe est de créer un seul « mot de passe « portail » que vos utilisateurs peuvent utiliser pour gérer tous les éléments liés à leurs mots de passe dans un endroit unique.  

La plupart de nos plus gros clients Choisir créer une racine des entrées DNS, comme https://passwords.contoso.com avec les liens vers le mot de passe Active Directory Azure réinitialiser portal, portail de l’enregistrement de réinitialisation de mot de passe et les pages de modification du mot de passe.  De cette façon, les communications par messagerie électronique ou un prospectus que vous envoyez, vous pouvez inclure dans un unique, facile à retenir, URL que les utilisateurs peuvent aller lorsqu’ils ont une seconde mise en route du service.

Pour commencer ici, nous avez créé une page simple qui utilise les dernières réactive UI design paradigmes et fonctionnera sur tous les navigateurs et les périphériques mobiles.

  ![][007]

Vous pouvez [télécharger ici le modèle de site Web](https://github.com/kenhoff/password-reset-page).  Nous vous recommandons de personnaliser le logo et les couleurs pour les besoins de votre organisation.

### <a name="using-enforced-registration"></a>À l’aide de l’enregistrement forcé
Si vous souhaitez que vos utilisateurs à s’inscrire pour réinitialiser leur mot de passe, vous pouvez également forcer à inscrire lors de leur inscription pour le panneau d’accès à [http://myapps.microsoft.com](http://myapps.microsoft.com).  Vous pouvez activer cette option à partir de l’onglet de **configuration** de votre répertoire en activant l’option **Exiger des utilisateurs au Registre lors de la signature le panneau d’accès** .  

Vous pouvez également définir ou non leur demandera d’inscrire de nouveau après une période configurable en modifiant l’option **nombre de jours avant que les utilisateurs doivent confirmer leurs données de contact** à une valeur différente de zéro. Pour plus d’informations, reportez-vous à la section [Personnaliser le comportement de gestion de mot de passe utilisateur](active-directory-passwords-customize.md#password-management-behavior) .

  ![][002]

Après avoir activé cette option, lorsque les utilisateurs se connecter dans le panneau d’accès, ils verront un message qui les informe que l’administrateur a requis peuvent vérifier leurs informations de contact. Ils peuvent l’utiliser pour réinitialiser leur mot de passe s’ils arrivait de perdre l’accès à son compte.

  ![][003]

En cliquant sur **Vérifier maintenant** les apporte le **portail de l’enregistrement de réinitialisation de mot de passe** à [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) et a besoin d’enregistrer.  L’inscription via cette méthode peut être désactivée en cliquant sur le bouton **Annuler** ou fermer la fenêtre, mais les utilisateurs reçoivent un rappel à chaque fois qu’ils vous connecter si elles ne s’enregistrent pas.

  ![][004]

### <a name="uploading-data-yourself"></a>Téléchargement de données
Si vous souhaitez télécharger vous-même les données d’authentification, les utilisateurs doivent s’inscrire pas de mot de passe réinitialisé avant de pouvoir réinitialiser leurs mots de passe.  Dans la mesure où les utilisateurs ont les données d’authentification définies sur leur compte répondant le mot de passe réinitialiser la stratégie que vous avez définie, puis les utilisateurs seront en mesure de réinitialiser leurs mots de passe.

Pour savoir quelles propriétés peuvent être définies via DAS se connecter ou de Windows PowerShell, voir [les données d’utilisation par la réinitialisation du mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Vous pouvez télécharger les données d’authentification via le [Portail de gestion Azure](https://manage.windowsazure.com) en suivant les étapes ci-dessous :

1.  Accédez au répertoire dans l' **extension de Active Directory** dans le [Portail de gestion Azure](https://manage.windowsazure.com).
2.  Cliquez sur l’onglet **utilisateurs** .
3.  Sélectionnez l’utilisateur qui que vous intéressez dans la liste.
4.  Sur le premier onglet, vous trouverez un **Autre E-mail**, qui peut être utilisé en tant que propriété pour activer la réinitialisation du mot de passe.

    ![][005]

5.  Cliquez sur l’onglet **Info de travail** .
6.  Vous trouverez sur cette page, **Téléphone professionnel**, **téléphone Mobile**, **Téléphone de l’authentification**et **L’authentification E-mail**.  Ces propriétés peuvent également être définies pour permettre à un utilisateur réinitialiser son mot de passe.

    ![][006]

Consultez [les données sont utilisées par la réinitialisation du mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) pour voir comment chacune de ces propriétés peut être utilisé.

Consultez [l’accès de mot de passe à rétablir les données de vos utilisateurs à partir de PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) pour voir comment vous pouvez lire et définir ces données avec PowerShell.

## <a name="sample-training-materials"></a>Supports de formation exemple
Nous travaillons sur des supports de formation exemples que vous pouvez utiliser pour obtenir votre organisation informatique et vos utilisateurs à maîtriser rapidement sur la façon de déployer et d’utiliser de réinitialisation de mot de passe.  Restez en ligne !


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Des liens vers le mot de passe réinitialiser la documentation
Vous trouverez ci-dessous des liens vers toutes les pages de documentation de réinitialisation de mot de passe Active Directory Azure :

* **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Mode de fonctionnement**](active-directory-passwords-how-it-works.md) - en savoir plus sur les différents composants de service et de six chacune d’entre-elles
* [**Mise en route**](active-directory-passwords-getting-started.md) - Apprenez à vous permettent aux utilisateurs de réinitialiser et de modifier leurs mots de passe nuage ou sur site
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’aspect et la convivialité et le comportement du service pour les besoins de votre organisation
* [**Se référant**](active-directory-passwords-get-insights.md) - en savoir plus sur nos capacités de création de rapports intégrées
* [**Forum aux questions**](active-directory-passwords-faq.md) : obtenir des réponses aux questions fréquemment posées
* [**Dépannage**](active-directory-passwords-troubleshoot.md) - Apprenez à rapidement résoudre les problèmes avec le service
* [**En savoir plus**](active-directory-passwords-learn-more.md) - profondeur accédez aux détails techniques du fonctionne du service



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"
