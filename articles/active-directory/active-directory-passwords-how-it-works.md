<properties
    pageTitle="Fonctionnement : gestion de mot de passe Active Directory Azure | Microsoft Azure"
    description="En savoir plus sur les différents composants de la gestion de mot de passe Active Directory Azure, y compris où les utilisateurs enregistrement, réinitialiser et modifient leurs mots de passe et où les administrateurs de configurer, générer des rapports sur et activer la gestion des mots de passe Active Directory local."
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

# <a name="how-password-management-works"></a>Comment fonctionne la gestion de mot de passe

> [AZURE.IMPORTANT] **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Gestion des mots de passe dans Azure Active Directory est constituée de plusieurs composants logiques qui sont décrites ci-dessous.  Cliquez sur chaque lien pour en savoir plus sur ce composant.

- [**Portail de Configuration de gestion de mot de passe**](#password-management-configuration-portal) – les administrateurs peuvent contrôler les différents aspects de la façon dont les mots de passe sont gérés dans leurs locataires en accédant à l’onglet de configuration de leur répertoire dans le [Portail de gestion Azure](https://manage.windowsazure.com).
- [**Portail de l’enregistrement d’utilisateur**](#user-registration-portal) – les utilisateurs peuvent s’inscrire sur mot de passe réinitialisé par l’intermédiaire de ce portail web.
- [**Portail de réinitialisation de mot de passe utilisateur**](#user-password-reset-portal) – les utilisateurs peuvent réinitialiser leur mot de passe à l’aide d’un certain nombre de défis différents conformément à la stratégie de réinitialisation de mot de passe contrôlé par l’administrateur
- [**Portail de changer de mot de passe utilisateur**](#user-password-change-portal) – les utilisateurs peuvent modifier leurs mots de passe à tout moment en entrant leur mot de passe ancien et en sélectionnant un nouveau mot de passe à l’aide de ce portail web
- [**Rapports de gestion de mot de passe**](#password-management-reports) – les administrateurs peuvent afficher et analyser l’activité d’enregistrement et de réinitialisation de mot de passe de leurs clients en accédant à la section « Rapports d’activité » de l’onglet « Rapports » de leur répertoire dans le [Portail de gestion Azure](https://manage.windowsazure.com)
- [**Composant d’écriture différée de mot de passe d’Azure Connect de publicité**](#password-writeback-component-of-azure-ad-connect) – les administrateurs peuvent activer éventuellement la fonctionnalité d’écriture différée de mot de passe lors de l’installation Azure Connect d’Active Directory pour activer la gestion de fédérés ou mots de passe utilisateur du nuage de synchronisation de mot de passe.

## <a name="password-management-configuration-portal"></a>Portail de Configuration de gestion de mot de passe
Vous pouvez configurer des stratégies de gestion de mot de passe pour un répertoire spécifique à l’aide du [Portail de gestion Azure](https://manage.windowsazure.com) en accédant à la section **Stratégie de réinitialisation de mot de passe utilisateur** dans l’onglet de **configuration** de l’annuaire.  À partir de cette page, vous pouvez contrôler de nombreux aspects de la façon dont les mots de passe sont gérés dans votre organisation, y compris :

- Activation et désactivation du mot de passe pour tous les utilisateurs dans un répertoire de réinitialisation
- Définir le nombre de défis (une ou deux) d’un utilisateur à suivre pour réinitialiser son mot de passe
- Définition des types spécifiques de problèmes que vous souhaitez activer pour les utilisateurs de votre organisation dans la liste ci-dessous :
 - Téléphone portable (un code de vérification par l’intermédiaire de texte ou d’un appel vocal)
 - Téléphone du bureau (un appel vocal)
 - Autre E-mail (un code de vérification par courrier électronique)
 - Questions de sécurité (authentification basée sur les connaissances)
- Définition du nombre de questions un utilisateur doit inscrire pour utiliser la méthode d’authentification sécurité questions (visible uniquement si les questions de sécurité sont activées)
- Définition du nombre de questions un utilisateur doit fournir lors de la réinitialisation à utiliser la méthode d’authentification sécurité questions (visible uniquement si les questions de sécurité sont activées)
- À l’aide de questions de sécurité prédéfini, localisée, qu’un utilisateur peut choisir d’utiliser lors de l’enregistrement de mot de passe réinitialisé (visible uniquement si les questions de sécurité sont activées)
- Définissez les questions de sécurité personnalisées qu’un utilisateur peut choisir d’utiliser lors de l’enregistrement de mot de passe réinitialisé (visible uniquement si les questions de sécurité sont activées)
- Demander aux utilisateurs d’enregistrer le mot de passe lorsqu’ils accèdent à l’application d’accès au [http://myapps.microsoft.com](http://myapps.microsoft.com)de réinitialisation.
- Nécessitant des utilisateurs à confirmer leurs données précédemment enregistré après un nombre configurable de jours se sont écoulés (visibles uniquement si l’enregistrement forcé est activé)
- Fourniture d’une URL qui sera affiché aux utilisateurs au cas où ils ont un problème de réinitialisation de leurs mots de passe ou le courrier électronique du support technique personnalisé
- Activation ou désactivation de la fonctionnalité d’écriture différée de mot de passe (lors de l’écriture différée de mot de passe a été déployée à l’aide de la connexion de DAS)
- Affichage de l’état de l’agent d’écriture différée de mot de passe (lors de l’écriture différée de mot de passe a été déployée à l’aide de la connexion de DAS)
- Activation des notifications par courrier électronique aux utilisateurs lorsque leur propre mot de passe a été réinitialisé (trouvée dans la section **Notifications** du [Portail de gestion Azure](https://manage.windowsazure.com))
- Activation des notifications par courrier électronique aux administrateurs lorsque les autres administrateurs de réinitialiser leurs mots de passe (qui se trouvés dans la section **Notifications** du [Portail de gestion Azure](https://manage.windowsazure.com)
- Marque le mot de passe utilisateur réinitialisé portal et e-mails avec le logo et le nom de votre organisation de réinitialisation de mot de passe en utilisant le locataire personnalisation des fonctionnalités de personnalisation (qui se trouve dans la section **Propriétés de l’annuaire** du [Portail de gestion Azure](https://manage.windowsazure.com)

Pour en savoir plus sur la configuration de gestion de mot de passe dans votre organisation, reportez-vous à la section [mise en route : gestion de mot de passe Azure AD](active-directory-passwords-getting-started.md).

##<a name="user-registration-portal"></a>Portail de l’enregistrement d’utilisateur
Avant que les utilisateurs sont en mesure d’utiliser de réinitialisation de mot de passe, leurs comptes d’utilisateur de nuage doivent être mis à jour avec les données de l’authentification correcte pour s’assurer qu’ils peuvent passer par le nombre approprié de défis de réinitialisation de mot de passe définis par leur administrateur.  Les administrateurs peuvent également définir ces informations d’authentification sous le nom de leur utilisateur à l’aide de portails web Azure ou Office, synchronisation d’annuaire / Azure Connect de publicité ou de Windows PowerShell.

Toutefois, si vous préférez que vos utilisateurs enregistrer leurs propres données, nous proposons également une page web que les utilisateurs peuvent aller pour fournir ces informations.  Cette page permet aux utilisateurs de spécifier les informations d’authentification selon le mot de passe réinitialiser les stratégies qui ont été activés dans leur organisation.  Une fois ces données sont vérifiées, il est stocké dans son compte d’utilisateur de nuage à utiliser pour la récupération du compte à une date ultérieure. Voici ce à quoi l’inscription portail ressemble :

  ![][001]

Pour plus d’informations, consultez [mise en route : gestion de mot de passe Azure AD](active-directory-passwords-getting-started.md) et [recommandées : gestion de mot de passe Azure AD](active-directory-passwords-best-practices.md).

##<a name="user-password-reset-portal"></a>Portail de réinitialisation de mot de passe utilisateur
Une fois que vous avez activé le mot de passe libre-service réinitialiser, configurer la stratégie de réinitialisation de mot de passe de votre organisation et veiller à ce que vos utilisateurs que les données de contact appropriées dans le répertoire, les utilisateurs de votre organisation seront en mesure de réinitialiser leurs mots de passe automatiquement à partir de n’importe quelle page web qui utilise un compte de travail ou de l’établissement pour se connecter (par exemple, [portal.microsoftonline.com](https://portal.microsoftonline.com)). Sur les pages de ce type, les utilisateurs verront une **ne peut pas accéder à votre compte ?** lien.

  ![][002]

En cliquant sur ce lien lancera le portail de réinitialisation de mot de passe libre-service.

  ![][003]

Pour plus d’informations sur comment les utilisateurs peuvent réinitialiser leurs mots de passe, reportez-vous à la section [mise en route : gestion de mot de passe Azure AD](active-directory-passwords-getting-started.md).

##<a name="user-password-change-portal"></a>Portail de modification de mot de passe utilisateur
Si les utilisateurs souhaitent modifier leur mot de passe, ils peuvent le faire à l’aide du portail de modification de mot de passe à tout moment.  Les utilisateurs peuvent accéder au portail de modification de mot de passe via la page de profil du panneau d’accès, ou en cliquant sur le lien « Modifier le mot de passe » dans les applications Office 365.  Dans le cas lors de l’expiration de leur mot de passe, les utilisateurs également demandera les modifier automatiquement lors de votre inscription.

  ![][004]

Dans ces deux cas, si l’écriture différée de mot de passe a été activée et l’utilisateur est soit fédéré ou serait de la synchronisation de mot de passe, ces mots de passe modifiés sont écrites dans Active Directory sur site. Voici ce que le mot de passe changement portail similaire à :

  ![][005]

Pour plus d’informations sur la façon dont les utilisateurs peuvent modifier leurs mots de passe sur les sites Active Directory, voir [mise en route : gestion de mot de passe Azure AD](active-directory-passwords-getting-started.md).

##<a name="password-management-reports"></a>Rapports de gestion de mot de passe
En accédant à l’onglet **rapports** et recherche dans la section **Journaux d’activité** , vous verrez deux rapports de gestion de mot de passe : **activité de réinitialisation de mot de passe** et le **mot de passe réinitialisé l’activité d’enregistrement**.  À l’aide de ces deux rapports, vous pouvez obtenir une vue des utilisateurs inscrit et à l’aide du mot de passe réinitialisé dans votre organisation. Voici à quoi ressemblent ces rapports sur le [Portail de gestion Azure](https://manage.windowsazure.com):

  ![][006]

Pour plus d’informations, consultez [obtenir des aperçus : rapports de gestion de mot de passe Azure AD](active-directory-passwords-get-insights.md).

##<a name="password-writeback-component-of-azure-ad-connect"></a>Composant d’écriture différée de mot de passe d’Azure Connect de publicité
Si les mots de passe des utilisateurs dans votre organisation sont issus de votre environnement local (soit via la fédération ou la synchronisation de mot de passe), vous pouvez installer la dernière version de Azure Connect d’Active Directory pour activer la mise à jour de ces mots de passe directement à partir du cloud.  Cela signifie que lorsque les utilisateurs oublient ou want modifier leur mot de passe Active Directory, ils peuvent faire ainsi directement à partir du web.  Voici où trouver l’écriture différée de mot de passe dans l’Assistant d’installation Azure Connect d’annonce :

  ![][007]

Pour plus d’informations sur Azure Connect d’Active Directory, voir [mise en route : Azure Connect de AD](active-directory-aadconnect.md). Pour plus d’informations sur l’écriture différée de mot de passe, reportez-vous à la section [mise en route : gestion de mot de passe Azure AD](active-directory-passwords-getting-started.md).


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Des liens vers le mot de passe réinitialiser la documentation
Vous trouverez ci-dessous des liens vers toutes les pages de documentation de réinitialisation de mot de passe Active Directory Azure :

* **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Mise en route**](active-directory-passwords-getting-started.md) - Apprenez à vous permettent aux utilisateurs de réinitialiser et de modifier leurs mots de passe nuage ou sur site
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’aspect et la convivialité et le comportement du service pour les besoins de votre organisation
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) - Apprenez à déployer rapidement et efficacement gérer les mots de passe dans votre organisation
* [**Se référant**](active-directory-passwords-get-insights.md) - en savoir plus sur nos capacités de création de rapports intégrées
* [**Forum aux questions**](active-directory-passwords-faq.md) : obtenir des réponses aux questions fréquemment posées
* [**Dépannage**](active-directory-passwords-troubleshoot.md) - Apprenez à rapidement résoudre les problèmes avec le service
* [**En savoir plus**](active-directory-passwords-learn-more.md) - profondeur accédez aux détails techniques du fonctionne du service



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"
