<properties
    pageTitle="Pour en savoir plus : La gestion de mot de passe Active Directory Azure | Microsoft Azure"
    description="Rubriques avancées sur la gestion de mot de passe Active Directory Azure, y compris le fonctionnement de l’écriture différée de mot de passe, sécurité de mot de passe d’écriture différée, comment le mot de passe réinitialisé portail works et quelles données sont utilisées par la réinitialisation du mot de passe."
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

# <a name="learn-more-about-password-management"></a>Pour en savoir plus sur la gestion de mot de passe

> [AZURE.IMPORTANT] **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Si vous avez déjà déployé la gestion de mot de passe, ou que vous cherchiez pour en savoir plus sur la technique lançons-nous que PIERREUSES de son fonctionnement avant le déploiement, cette section vous donne une bonne vue d’ensemble des concepts du service techniques. Nous aborderons les opérations suivantes :

* [**Vue d’ensemble de mot de passe d’écriture différée**](#password-writeback-overview)
  - [Fonctionne de l’écriture différée de mot de passe](#how-password-writeback-works)
  - [Scénarios pris en charge pour l’écriture différée de mot de passe](#scenarios-supported-for-password-writeback)
  - [Modèle de sécurité de mot de passe d’écriture différée](#password-writeback-security-model)
* [**Comment le mot de passe réinitialiser des travaux portail ?**](#how-does-the-password-reset-portal-work)
  - [Les données sont utilisées par réinitialisation de mot de passe ?](#what-data-is-used-by-password-reset)
  - [L’accès de mot de passe à rétablir les données de vos utilisateurs](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>Vue d’ensemble de mot de passe d’écriture différée
L’écriture différée de mot de passe est un composant de [Azure Active Directory se connecter](active-directory-aadconnect.md) qui peut être activé et utilisé par les abonnés actuels de Azure Active Directory prime. Pour plus d’informations, consultez [Azure Active Directory éditions](active-directory-editions.md).

L’écriture différée de mot de passe vous permet de configurer vos clients de nuage pour écrire des mots de passe vous sur les sites Active Directory.  Il vous homologué rend d’avoir à configurer et gérer une solution de réinitialisation de mot de passe libre-service complexe sur site, et il fournit un moyen pratique de nuage à vos utilisateurs de réinitialiser leurs mots de passe locaux où qu’ils soient.  Lisez la suite pour quelques-unes des fonctionnalités clés de d’écriture différée de mot de passe :

- **Commentaires de zéro retard.**  L’écriture différée de mot de passe est une opération synchrone.  Les utilisateurs seront notifiés immédiatement si leur mot de passe ne respectait pas de stratégie ou n’a pas pu être réinitialisé ou modifié pour une raison quelconque.
- **Prend en charge la réinitialisation des mots de passe des utilisateurs à l’aide d’ADFS ou autres technologies de fédération.**  Avec le mot de passe d’écriture en différé, dans la mesure où les comptes d’utilisateurs fédérés sont synchronisées dans vos clients AD Azure, ils seront en mesure de gérer leurs locaux les mots de passe Active Directory à partir du cloud.
- **Prend en charge la réinitialisation des mots de passe des utilisateurs à l’aide de la synchronisation de hachage de mot de passe.** Lorsque le service de réinitialisation de mot de passe détecte qu’un compte d’utilisateur de synchronisation est activé pour la synchronisation de hachage de mot de passe, nous de réinitialisation sur les sites de ce compte et mot de passe de cloud simultanément.
- **Prend en charge la modification des mots de passe à partir du panneau d’accès et l’Office 365.**  Lorsque fédérés ou synchronisation de mot de passe utilisateurs viennent à modifier leur mot de passe a expiré ou non expirée, nous allons écrire ces mots de passe revenir à votre environnement Active Directory local.
- **Prend en charge la réécriture des mots de passe lorsqu’un administrateur les remettre à partir de la** [**Portail de gestion azure**](https://manage.windowsazure.com).  Chaque fois qu’un administrateur réinitialise mot de passe d’un utilisateur dans le [Portail de gestion Azure](https://manage.windowsazure.com), si cet utilisateur est fédéré ou la synchronisation de mot de passe, nous devez définir l’administrateur sélectionne sur votre Active Directory local, ainsi que le mot de passe.  Il est actuellement pas pris en charge dans le portail d’administration d’Office.
- **Applique vos locaux sur les stratégies de mot de passe Active Directory.**  Lorsqu’un utilisateur réinitialise son mot de passe, nous assurer qu’il répond à vos locaux par une stratégie AD avant de la valider dans ce répertoire.  Cela inclut l’historique, la complexité, l’âge, les filtres de mot de passe et d’autres restrictions de mot de passe que vous avez défini dans votre Active Directory local.
- **Ne nécessite pas les règles de pare-feu entrantes.**  L’écriture différée de mot de passe utilise un relais de Bus des services Azure comme un canal de communication sous-jacent, c'est-à-dire que vous ne disposez pas d’ouvrir de ports entrants sur votre pare-feu pour que cette fonctionnalité fonctionne.
- **N’est pas pris en charge pour les comptes utilisateur qui existent dans les groupes protégés dans Active Directory sur site.** Pour plus d’informations sur les groupes protégés, consultez [comptes protégés et des groupes dans Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Fonctionne de l’écriture différée de mot de passe
L’écriture différée de mot de passe comporte trois composants principaux :

- Service de cloud de réinitialisation de mot de passe (il est également intégré aux pages de modification du mot de passe de l’annonce Azure)
- Relais de Bus des services Azure de clients spécifiques
- Point de terminaison de réinitialisation de mot de passe sur prem

Elles s’adaptent comme décrit dans le schéma ci-dessous :

  ![][001]

Lors de la synchronisation d’un hachage de mot de passe ou fédéré serait utilisateur se réinitialiser ou modifier son mot de passe dans le nuage, le processus suivant a lieu :

1.  Nous vérifier quel type de mot de passe de l’utilisateur a.  Si nous voir que le mot de passe est géré sur site, nous assurer que le service de l’écriture différée est en cours d’exécution.  Si c’est le cas, nous laisser l’utilisateur poursuivre, s’il ne l’est pas, nous indiquer à l’utilisateur que son mot de passe ne peut pas être réinitialisée ici.
2.  Ensuite, l’utilisateur transmet les portails d’authentification approprié et atteint l’écran de mot de passe de réinitialisation.
3.  L’utilisateur sélectionne un nouveau mot de passe et il confirme.
4.  Lorsque vous cliquez sur Envoyer, nous crypter le mot de passe en texte brut avec une clé symétrique qui a été créée pendant l’installation de d’écriture différée.
5.  Après le cryptage du mot de passe, nous l’inclure dans une charge utile qui soit envoyée sur un canal HTTPS dans votre relais de bus de service spécifique de clients (que nous avons également configurés pour vous pendant le processus d’installation d’écriture différée).  Ce relais est protégé par un mot de passe généré de façon aléatoire capable uniquement l’installation sur site.
6.  Lorsque le message atteint le bus de service, le point de terminaison de réinitialisation de mot de passe automatiquement se réveille et découvre qu’une demande de réinitialisation en attente.
7.  Le service de recherche pour l’utilisateur en question à l’aide de l’attribut de point d’ancrage du nuage.  Pour réussir, cette recherche l’objet utilisateur doit exister dans l’espace du connecteur Active Directory, il doit être lié à l’objet MV correspondant, et il doit être lié à l’objet de connecteur correspondant DAS. Enfin, pour la synchronisation de trouver ce compte d’utilisateur, le lien à partir de l’objet du connecteur Active Directory pour MV doit avoir la règle de synchronisation `Microsoft.InfromADUserAccountEnabled.xxx` sur le lien.  Cela est nécessaire, car lorsque l’appel provient le nuage, le moteur de synchronisation utilise l’attribut cloudAnchor pour rechercher l’objet espace de connecteur DAS, puis suit le lien vers l’objet MV et puis elle suit le lien vers l’objet Active Directory. Dans la mesure où il peut y avoir plusieurs objets Active Directory (plusieurs forêts) pour le même utilisateur, le moteur de synchronisation s’appuie sur le `Microsoft.InfromADUserAccountEnabled.xxx` lien de choisir celle qui convient.
8.  Une fois que le compte d’utilisateur est trouvé, nous essayez de réinitialiser le mot de passe directement dans la forêt Active Directory appropriée.
9.  Si l’opération de définition du mot de passe réussit, nous indiquer à l’utilisateur que son mot de passe a été modifié et qu’ils peuvent aller sur leur implémentons.
10. Si le mot de passe défini l’opération échoue, nous renvoyer l’erreur à l’utilisateur et laissez les essayer à nouveau.  L’opération risque d’échouer, car le service était arrêté, car le mot de passe qu’ils ont sélectionné ne répondait pas aux stratégies de l’organisation, car nous n’avons pas trouvé l’utilisateur dans l’annuaire AD local, ou n’importe quel nombre de raisons.  Nous avons un message spécifique pour la plupart de ces cas et que vous indiquez à l’utilisateur ce qu’ils peuvent faire pour résoudre le problème.

### <a name="scenarios-supported-for-password-writeback"></a>Scénarios pris en charge pour l’écriture différée de mot de passe
Le tableau ci-dessous décrit les scénarios sont pris en charge pour les versions de nos capacités de synchronisation.  En général, il est vivement recommandé d’installer la dernière version de [Azure Connect d’annonces](active-directory-aadconnect.md#install-azure-ad-connect) si vous souhaitez utiliser le mot de passe d’écriture en différé.

  ![][002]

### <a name="password-writeback-security-model"></a>Modèle de sécurité de mot de passe d’écriture différée
L’écriture différée de mot de passe est un service hautement sécurisé et robuste.  Afin d’assurer que la protection de vos informations, nous permettent un modèle 4 niveaux de sécurité qui est décrite ci-dessous.

- **Relais de bus des services spécifiques de clients** – lorsque vous configurez le service, nous allons configurer un relais de bus de service clients spécifiques qui est protégé par un mot de passe fort généré de façon aléatoire que Microsoft n’a jamais accès à.
- **Verrouillés, clé de cryptage de mot de passe fort au niveau cryptographique,** – après avoir créé le relais de bus de service, nous créons une clé symétrique forte qui nous permet de crypter le mot de passe lorsqu’il arrive sur le réseau.  Cette clé se trouve uniquement dans le magasin des secrets de votre société dans le nuage, qui est largement verrouillé et vérifiée, tout comme n’importe quel mot de passe dans le répertoire.
- **TLS standard du secteur** – lorsqu’un mot de passe réinitialisé ou opération de modification se produit dans le nuage, nous prendre les mots de passe et de le crypter avec votre clé publique.  Nous qui constater ensuite dans un message HTTPS envoyé via un canal crypté à l’aide de certificats SSL de Microsoft pour votre relais de bus de service.  Après l’arrivée de ce message dans le Bus des services, votre agent sur prem se réveille, s’authentifie sur le Bus de Service en utilisant le mot de passe fort qui a été généré précédemment, récupère le message crypté, déchiffre à l’aide de la clé privée que nous avons généré, puis de tentatives de mot de passe via l’API de méthode SetPassword AD DS.  Cette étape est ce qui nous permet d’appliquer votre stratégie de mot de passe sur prem AD (complexité, âge, historique, filtres, etc.) dans le nuage.
- **Stratégies d’expiration de message** – Enfin, si pour une raison quelconque le message reste dans le Bus des services, car votre service sur prem est arrêté, il sera expiré et supprimé après quelques minutes afin de renforcer encore davantage la sécurité.

## <a name="how-does-the-password-reset-portal-work"></a>Comment le mot de passe réinitialiser des travaux portail ?
Lorsqu’un utilisateur accède au portail de réinitialisation du mot de passe, un flux de travail est lancée pour déterminer si ce compte d’utilisateur est valide, quelle organisation appartenant aux utilisateurs, où le mot de passe de cet utilisateur est géré et ou non de l’utilisateur est autorisé à utiliser la fonctionnalité.  Lisez les étapes ci-dessous pour en savoir plus sur la logique derrière la page de réinitialisation de mot de passe.

1.  Utilisateur clique sur le ne peut pas accéder à votre lien compte ou les passe directement à [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.  Utilisateur entre un id utilisateur et transmet un captcha.
3.  Annonce Azure vérifie si l’utilisateur est en mesure d’utiliser cette fonctionnalité en procédant comme suit :
    - Vérifie que l’utilisateur dispose de cette fonctionnalité est activée et une licence de publicité Azure affecté.
        - Si l’utilisateur n’a pas de cette fonctionnalité est activée ou une licence attribuée, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
    - Vérifie que l’utilisateur a le droit de conteste les données définies sur son compte, conformément à la stratégie de l’administrateur.
        - Si la stratégie ne requiert qu’un défi, il est assuré que l’utilisateur possède les données appropriées définies pour au moins un des défis activées par la stratégie de l’administrateur.
          - Si l’utilisateur n’est pas configuré, l’utilisateur est alors invité à contacter son administrateur pour réinitialiser son mot de passe.
        - Si la stratégie exige deux défis, il est assuré que l’utilisateur possède les données appropriées définies pour au moins deux des défis activées par la stratégie de l’administrateur.
          - Si l’utilisateur n’est pas configuré, puis nous l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
    - Contrôles ou non le mot de passe est gérée dans les établissements (fédérés ou synchronisation de hachage de mot de passe avait).
       - Si l’écriture différée est déployée et le mot de passe est géré sur site, l’utilisateur est autorisé à continuer à s’authentifier et de réinitialiser son mot de passe.
       - Si l’écriture différée n’est pas déployée et géré dans les locaux de mot de passe de l’utilisateur, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
4.  S’il est déterminé que l’utilisateur est en mesure de correctement son mot de passe, l’utilisateur est alors guidé à travers le processus de réinitialisation.

En savoir plus sur le déploiement de d’écriture différée de mot de passe à [mise en route : gestion de mot de passe Azure AD](active-directory-passwords-getting-started.md).

### <a name="what-data-is-used-by-password-reset"></a>Les données sont utilisées par réinitialisation de mot de passe ?
Le tableau suivant indique où et comment ces données sont utilisées lors de la réinitialisation de mot de passe et qu’il sont conçues pour vous aider à choisir les options d’authentification sont appropriées pour votre organisation. Ce tableau indique également les exigences de mise en forme pour les cas où vous fournissez des données au nom des utilisateurs à partir des chemins d’entrée qui ne valident pas ces données.

> [AZURE.NOTE] Téléphone du bureau n’apparaît pas dans le portail d’inscription car les utilisateurs ne sont actuellement pas en mesure de modifier cette propriété dans le répertoire.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nom de la méthode de contact</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Élément de données Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Utilisé / définissable où ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Exigences de format</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Téléphone du bureau</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>par exemple, Set-MsolUser - UserPrincipalName JWarner@contoso.com - PhoneNumber « 1234567890 + 1 x 1234 »</p>
            </td>
            <td>
              <p>Utilisé dans :</p>
              <p>Portail de réinitialisation de mot de passe</p>
              <p>Réglable à partir de :</p>
              <p>PhoneNumber ne peut être définie à partir de PowerShell, la synchronisation d’annuaire, portail de gestion Azure et le portail d’administration d’Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (par exemple, + 1 1234567890)</p>
              <ul>
                <li class="unordered">
Doit fournir un code de pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit fournir un code de région (le cas échéant)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Devez avoir fournir du code un + à l’avant du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit avoir un espace entre le code du pays et le reste du nombre<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Les extensions ne sont pas pris en charge, si vous avez des extensions spécifiées, nous supprimera il à partir du numéro avant de répartir l’appel téléphonique.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Téléphone mobile</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OU</p>
              <p>MobilePhone</p>
              <p>(L’authentification que téléphone est utilisé s’il présente des données, dans le cas contraire cela revient dans le champ téléphone mobile).</p>
              <p>par exemple, Set-MsolUser - UserPrincipalName JWarner@contoso.com - MobilePhone « 1234567890 + 1 x 1234 »</p>
            </td>
            <td>
              <p>Utilisé dans :</p>
              <p>Portail de réinitialisation de mot de passe</p>
              <p>Portail de l’enregistrement</p>
              <p>Réglable à partir de : </p>
              <p>AuthenticationPhone ne peut être définie à partir du portail de l’enregistrement de réinitialisation de mot de passe ou le portail de l’enregistrement AMF.</p>
              <p>MobilePhone ne peut être définie à partir de PowerShell, la synchronisation d’annuaire, portail de gestion Azure et le portail d’administration d’Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (par exemple, + 1 1234567890)</p>
              <ul>
                <li class="unordered">
Doit fournir un code de pays.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Indiquez un code de région (le cas échéant).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Devez avoir fournir du code un + à l’avant du pays.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit avoir un espace entre le code du pays et le reste du nombre.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Les extensions ne sont pas pris en charge, si vous avez des extensions spécifiées, nous l’ignorer lors de l’expédition de l’appel téléphonique.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Autre application de messagerie</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OU</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(L’authentification Qu'e-mail est utilisé s’il présente des données, dans le cas contraire cela revient dans le champ de l’autre application de messagerie).</p>
              <p>Remarque : le champ de l’autre application de messagerie est spécifié comme un tableau de chaînes dans le répertoire.  Nous utilisons la première entrée dans ce tableau.</p>
              <p>par exemple, Set-MsolUser - UserPrincipalName JWarner@contoso.com - AlternateEmailAddresses"email@live.com"</p>
            </td>
            <td>
              <p>Utilisé dans :</p>
              <p>Portail de réinitialisation de mot de passe</p>
              <p>Portail de l’enregistrement</p>
              <p>Réglable à partir de : </p>
              <p>AuthenticationEmail ne peut être définie à partir du portail de l’enregistrement de réinitialisation de mot de passe ou le portail de l’enregistrement AMF.</p>
              <p>AlternateEmail ne peut être définie à partir du portail d’administration d’Office, PowerShell et le portail de gestion Azure</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a>ou甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
E-mails doivent suivre la mise en forme standard comme indiqué.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
E-mails d’Unicode sont pris en charge.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Réponses et les Questions de sécurité</p>
            </td>
            <td>
              <p>Non disponible pour modifier directement dans le répertoire.</p>
            </td>
            <td>
              <p>Utilisé dans :</p>
              <p>Portail de réinitialisation de mot de passe</p>
              <p>Portail de l’enregistrement </p>
              <p>Réglable à partir de : </p>
              <p>Il est le seul moyen de définir des questions de sécurité via le portail de gestion Azure.</p>
              <p>Il est le seul moyen de définir des réponses à des questions de sécurité pour un utilisateur donné via le portail de l’enregistrement.</p>
            </td>
            <td>
              <p>Les questions de sécurité ont un maximum de 200 caractères et un minimum de 3 caractères</p>
              <p>Réponses ont un maximum de 40 caractères et un minimum de 3 caractères</p>
            </td>
          </tr>
        </tbody></table>

###<a name="how-to-access-password-reset-data-for-your-users"></a>L’accès de mot de passe à rétablir les données de vos utilisateurs
####<a name="data-settable-via-synchronization"></a>Peut être définies via la synchronisation de données
Les champs suivants peuvent être synchronisés sur site :

* Téléphone mobile
* Téléphone du bureau

####<a name="data-settable-with-azure-ad-powershell"></a>Données définissables avec Azure Active Directory PowerShell
Les champs suivants sont accessibles avec Azure AD PowerShell et l’API du graphique :

* Autre application de messagerie
* Téléphone mobile
* Téléphone du bureau
* Téléphone de l’authentification
* E-mail de l’authentification

####<a name="data-settable-with-registration-ui-only"></a>Données définissables avec inscription l’interface utilisateur
Les champs suivants sont uniquement accessibles via l’enregistrement SSPR UI (https://aka.ms/ssprsetup) :

* Réponses et les Questions de sécurité

####<a name="what-happens-when-a-user-registers"></a>Que se passe-t-il lorsqu’un utilisateur s’inscrit ?
Lorsqu’un utilisateur enregistre, la page d’inscription sera **toujours** définir les champs suivants :

* Téléphone de l’authentification
* E-mail de l’authentification
* Réponses et les Questions de sécurité

Si vous avez fourni une valeur pour un **téléphone Mobile** ou de **Messagerie alternative**, utilisateurs peuvent immédiatement utiliser ceux de réinitialiser leurs mots de passe, même s’ils n’ont pas enregistré pour le service.  En outre, les utilisateurs voir ces valeurs lors de l’enregistrement pour la première fois et les modifier s’ils le souhaitent.  Toutefois, après que qu’ils inscrivent correctement, ces valeurs seront conservées dans les champs **Téléphone d’authentification** et **D’authentification électronique** , respectivement.

Cela peut être un moyen utile pour débloquer le grand nombre d’utilisateurs à utiliser SSPR tout en permettant aux utilisateurs de valider ces informations via le processus d’inscription.

####<a name="setting-password-reset-data-with-powershell"></a>Données avec PowerShell de réinitialisation de mot de passe de paramètre
Vous pouvez définir des valeurs pour les champs suivants avec Azure AD PowerShell.

* Autre application de messagerie
* Téléphone mobile
* Téléphone du bureau

Pour commencer, vous devez tout d’abord [Télécharger](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)et installer le module PowerShell de publicité Azure.  Une fois que vous avez déjà installé, vous pouvez suivre les étapes ci-dessous pour configurer chaque champ.

#####<a name="alternate-email"></a>Autre application de messagerie
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####<a name="mobile-phone"></a>Téléphone mobile
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####<a name="office-phone"></a>Téléphone du bureau
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####<a name="reading-password-reset-data-with-powershell"></a>Données avec PowerShell de réinitialisation de mot de passe de lecture
Vous pouvez lire les valeurs dans les champs suivants avec Azure AD PowerShell.

* Autre application de messagerie
* Téléphone mobile
* Téléphone du bureau
* Téléphone de l’authentification
* E-mail de l’authentification

Pour commencer, vous devez tout d’abord [Télécharger](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)et installer le module PowerShell de publicité Azure.  Une fois que vous avez déjà installé, vous pouvez suivre les étapes ci-dessous pour configurer chaque champ.

#####<a name="alternate-email"></a>Autre application de messagerie
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####<a name="mobile-phone"></a>Téléphone mobile
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####<a name="office-phone"></a>Téléphone du bureau
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####<a name="authentication-phone"></a>Téléphone de l’authentification
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####<a name="authentication-email"></a>E-mail de l’authentification
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="links-to-password-reset-documentation"></a>Des liens vers le mot de passe réinitialiser la documentation
Vous trouverez ci-dessous des liens vers toutes les pages de documentation de réinitialisation de mot de passe Active Directory Azure :

* **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Mode de fonctionnement**](active-directory-passwords-how-it-works.md) - en savoir plus sur les différents composants de service et de six chacune d’entre-elles
* [**Mise en route**](active-directory-passwords-getting-started.md) - Apprenez à vous permettent aux utilisateurs de réinitialiser et de modifier leurs mots de passe nuage ou sur site
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’aspect et la convivialité et le comportement du service pour les besoins de votre organisation
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) - Apprenez à déployer rapidement et efficacement gérer les mots de passe dans votre organisation
* [**Se référant**](active-directory-passwords-get-insights.md) - en savoir plus sur nos capacités de création de rapports intégrées
* [**Forum aux questions**](active-directory-passwords-faq.md) : obtenir des réponses aux questions fréquemment posées
* [**Dépannage**](active-directory-passwords-troubleshoot.md) - Apprenez à rapidement résoudre les problèmes avec le service



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
