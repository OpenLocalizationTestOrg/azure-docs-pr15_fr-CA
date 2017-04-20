<properties
   pageTitle="Connexion de publicité Azure : Comptes et les autorisations | Microsoft Azure"
   description="Cette rubrique décrit les comptes utilisés et créé et les autorisations requises."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/04/2016"
   ms.author="billmath"/>


# <a name="azure-ad-connect-accounts-and-permissions"></a>Connexion de publicité Azure : Comptes et les autorisations
L’Assistant d’installation Azure Connect d’annonce offre deux chemins différents :

- Dans les paramètres Express, l’Assistant requiert davantage de privilèges de sorte qu’il peut configurer votre configuration facilement, sans avoir à créer d’utilisateurs ou de configurer séparément les autorisations.

- Dans les paramètres personnalisés, l’Assistant vous propose plus de choix et d’options, mais il existe certains cas dans lesquels vous devez disposer des autorisations correctes vous-même.

## <a name="related-documentation"></a>Documentation connexe
Si vous ne pas lire la documentation sur [l’intégration des identités avec Azure Active Directory sur site](../active-directory-aadconnect.md), le tableau suivant fournit des liens vers les rubriques connexes.

Rubrique |  
--------- | ---------
L’installation en utilisant les paramètres Express | [Installation rapide d’Azure Connect de publicité](active-directory-aadconnect-get-started-express.md)
L’installation en utilisant les paramètres personnalisés | [Installation personnalisée de Azure Connect de publicité](active-directory-aadconnect-get-started-custom.md)
Mise à niveau à partir de la synchronisation d’annuaire | [Mise à niveau à partir de l’outil de synchronisation AD Azure (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)


## <a name="express-settings-installation"></a>Installation de paramètres Express
Dans les paramètres Express, l’Assistant d’installation vous demande des informations d’identification de l’administrateur de l’entreprise AD DS pour Active Directory sur site peut être configuré avec les autorisations requises pour Azure Connect d’AD. Si vous mettez à niveau à partir de la synchronisation d’annuaire, les informations d’identification des administrateurs d’entreprise AD DS sont utilisées pour réinitialiser le mot de passe pour le compte utilisé par la synchronisation d’annuaire. Vous devez également Azure AD Global administrateur.

Page de l’Assistant  | Informations d’identification collectées | Autorisations requises| Utilisé pour
------------- | ------------- |------------- |-------------
N/A|Utilisateur qui exécute l’Assistant d’installation| Administrateur du serveur local| <li>Crée le compte local qui est utilisé comme [compte de service de moteur de synchronisation](#azure-ad-connect-sync-service-account).
Connexion à Active Directory Azure| Informations d’identification du répertoire AD Azure | Rôle Administrateur général dans Azure AD | <li>Activation de la synchronisation dans l’annuaire AD Azure.</li>  <li>Création du [compte Active Directory Azure](#azure-ad-service-account) qui est utilisé pour les opérations de synchronisation de par la suite dans Azure AD.</li>
Se connecter aux services AD DS | Informations d’identification Active Directory sur site | Membre du groupe Administrateurs de l’entreprise (EA) dans Active Directory| <li>Crée un [compte](#active-directory-account) dans Active Directory et accorde des autorisations. Ce compte est utilisé pour lire et écrire des informations de l’annuaire pendant la synchronisation.</li>

### <a name="enterprise-admin-credentials"></a>Informations d’identification de l’administrateur de l’entreprise
Ces informations d’identification ne sont utilisées que lors de l’installation et sont utilisées une fois l’installation terminée. Il est administrateur de l’entreprise et pas administrateur de domaine, assurez-vous que les autorisations dans Active Directory peuvent être définies dans tous les domaines.

### <a name="global-admin-credentials"></a>Informations d’identification administrateur globales
Ces informations d’identification ne sont utilisées que lors de l’installation et ne sont pas utilisées une fois l’installation terminée. Il est utilisé pour créer le [compte Azure AD](#azure-ad-service-account) utilisé pour la synchronisation des modifications à Active Directory Azure. Le compte permet également de sync en tant que fonctionnalité dans Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Autorisations pour les services AD DS créé de compte pour les paramètres express
Le [compte](#active-directory-account) est créé pour lire et écrire dans les services AD DS ont les autorisations suivantes lors de la création par les paramètres express :

Autorisation | Utilisé pour
---- | ----
<li>Répliquer les modifications de l’annuaire</li><li>Répertoire de répliquer toutes les modifications | Synchronisation de mot de passe
En lecture/écriture toutes les propriétés utilisateur | Hybrides d’importation et d’Exchange
En lecture/écriture toutes les propriétés iNetOrgPerson | Hybrides d’importation et d’Exchange
Groupe de toutes les propriétés en lecture/écriture | Hybrides d’importation et d’Exchange
Contact de toutes les propriétés en lecture/écriture | Hybrides d’importation et d’Exchange
Réinitialiser le mot de passe | Préparation pour l’activation de l’écriture différée de mot de passe

## <a name="custom-settings-installation"></a>Installation de paramètres personnalisés
Lors de l’utilisation des paramètres personnalisés, le compte utilisé pour se connecter à Active Directory doit être créé avant l’installation. Vous trouverez les autorisations que vous devez accorder à ce compte dans [le compte de service d’annuaire Active Directory de créer](#create-the-ad-ds-account).

Page de l’Assistant  | Informations d’identification collectées | Autorisations requises| Utilisé pour
------------- | ------------- |------------- |-------------
N/A | Utilisateur qui exécute l’Assistant d’installation|<li>Administrateur du serveur local</li><li>Si vous utilisez un SQL Server complète, l’utilisateur doit être administrateur système (SA) dans SQL</li>| Par défaut, crée le compte local qui est utilisé comme [compte de service de moteur de synchronisation](#azure-ad-connect-sync-service-account). Le compte n’est créé que lorsque l’administrateur ne spécifie pas un compte particulier.
Installer les services de synchronisation, option de compte de Service | Annonces ou informations d’identification du compte utilisateur local | Utilisateur, les autorisations sont accordées par l’Assistant d’installation | Si l’administrateur spécifie un compte, ce compte est utilisé comme compte de service pour le service de synchronisation.
Connexion à Active Directory Azure | Informations d’identification du répertoire AD Azure| Rôle Administrateur général dans Azure AD| <li>Activation de la synchronisation dans l’annuaire AD Azure.</li>  <li>Création du [compte Active Directory Azure](#azure-ad-service-account) qui est utilisé pour les opérations de synchronisation de par la suite dans Azure AD.</li>
Connecter vos répertoires | Informations d’identification Active Directory pour chaque forêt qui est connecté à Active Directory Azure sur site | Les autorisations dépendent des fonctionnalités vous activez et se trouve dans le [compte de service d’annuaire Active Directory de créer](#create-the-ad-ds-account) |Ce compte est utilisé pour lire et écrire des informations de l’annuaire pendant la synchronisation.
Serveurs AD FS | Pour chaque serveur dans la liste, l’Assistant collecte les informations d’identification lorsque les informations d’identification d’ouverture de session de l’utilisateur qui exécute l’Assistant sont insuffisantes pour se connecter | Administrateur de domaine | Installation et configuration du rôle de serveur AD FS.
Serveurs proxy d’application Web |Pour chaque serveur dans la liste, l’Assistant collecte les informations d’identification lorsque les informations d’identification d’ouverture de session de l’utilisateur qui exécute l’Assistant sont insuffisantes pour se connecter | Administrateur local sur l’ordinateur cible | Installation et configuration du rôle de serveur WAP.
Informations d’identification de proxy confiance |Informations d’identification (les informations d’identification du proxy utilise pour s’inscrire pour un certificat de confiance dans le système de fichiers de confiance du service de fédération |Compte de domaine qui est un administrateur local du serveur ADFS | Inscription initiale du certificat de confiance FS-WAP.
Page de compte de Service FS AD, « Utiliser une option de compte de domaine utilisateur » | Références de compte d’utilisateur Active Directory | Utilisateur de domaine | Le compte d’utilisateur Active Directory dont les références sont fournies est utilisé comme compte d’ouverture de session du service AD FS.

### <a name="create-the-ad-ds-account"></a>Créer le compte de service d’annuaire Active Directory
Lorsque vous installez Azure Connect d’Active Directory, le compte que vous spécifiez dans la page de **connexion de vos répertoires** doit être présent dans Active Directory et disposer d’une autorisation. L’Assistant d’installation ne vérifie pas les autorisations et tous les problèmes sont uniquement disponibles lors de la synchronisation.

Les autorisations que vous avez besoin dépend des fonctionnalités facultatives que vous activez. Si vous avez plusieurs domaines, les autorisations doivent être accordées pour tous les domaines de la forêt. Si vous n’activez aucune de ces fonctionnalités, les autorisations **d’Utilisateur de domaine** par défaut sont suffisantes.

Fonctionnalité | Autorisations
------ | ------
Synchronisation de mot de passe | <li>Répliquer les modifications de l’annuaire</li>  <li>Répertoire de répliquer toutes les modifications
Déploiement hybride d’Exchange | Autorisations d’écriture pour les attributs documentées dans [Exchange hybride différée](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) pour les utilisateurs, les groupes et les contacts.
Écriture différée de mot de passe | Autorisations d’écriture sur les attributs décrits dans [mise en route avec la gestion de mot de passe](../active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) pour les utilisateurs.
Écriture différée de périphérique | Autorisations accordées à l’aide d’un script PowerShell comme décrit dans [l’écriture différée du périphérique](../active-directory-aadconnect-feature-device-writeback.md).
Écriture différée du groupe | Lire, créer, mettre à jour et supprimer des objets de groupe dans l’unité d’organisation où les groupes de distributions doivent se trouver.

## <a name="upgrade"></a>Mise à niveau
Lors de la mise à niveau vers une nouvelle version à partir d’une version d’Azure Connect d’Active Directory, vous devez disposer des autorisations suivantes :

Principal | Autorisations requises | Utilisé pour
---- | ---- | ----
Utilisateur qui exécute l’Assistant d’installation | Administrateur du serveur local | Mettre à jour les fichiers binaires.
Utilisateur qui exécute l’Assistant d’installation | Membre ADSyncAdmins | Modifier les règles de synchronisation et d’autres configurations.
Utilisateur qui exécute l’Assistant d’installation | Si vous utilisez un serveur SQL complet : DBO (ou similaires) de la base de données du moteur de synchronisation | Apporter des modifications au niveau de base de données, telles que la mise à jour des tables avec les nouvelles colonnes.

## <a name="more-about-the-created-accounts"></a>En savoir plus sur les comptes

### <a name="active-directory-account"></a>Compte Active Directory
Si vous utilisez les paramètres express, un compte est créé dans Active Directory qui est utilisé pour la synchronisation. Le compte créé se trouve dans le domaine racine de forêt dans le conteneur utilisateurs et a son nom précédé de **MSOL_**. Le compte est créé avec un mot de passe longs et complexe qui n’expire pas. Si vous avez une stratégie de mot de passe de votre domaine, assurez-vous que long et mots de passe complexes seraient autorisés pour ce compte.

![Compte Active Directory](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### <a name="azure-ad-connect-sync-service-accounts"></a>Comptes de service de synchronisation AD Connect Azure
Un compte de service local est créé par l’Assistant d’installation (sauf si vous spécifiez le compte à utiliser dans les paramètres personnalisés). Le compte est le préfixe **AAD_** et permet d’exécuter en tant que pour le service de synchronisation réelle. Si vous installez Azure Connect d’Active Directory sur un contrôleur de domaine, le compte est créé dans le domaine. Si vous utilisez un serveur distant exécutant le serveur SQL ou si vous utilisez un serveur proxy qui requiert une authentification, le compte de service **AAD_** doit se trouver dans le domaine.

![Compte de Service de synchronisation](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Le compte est créé avec un mot de passe longs et complexe qui n’expire pas.

Ce compte est utilisé pour stocker les mots de passe pour les autres comptes de manière sécurisée. Ces autres mots de passe de comptes sont stockées cryptées dans la base de données. Les clés privées pour les clés de cryptage sont protégées par le chiffrement de clé secrète de services de chiffrement à l’aide des API de Protection des données (DPAPI) Windows. Vous ne devez pas réinitialiser le mot de passe du compte de service dans la mesure où Windows puis détruit les clés de cryptage pour des raisons de sécurité.

Si vous utilisez un total de SQL Server, le compte de service est le propriétaire de la base de données pour le moteur de synchronisation. Le service ne fonctionnera pas comme prévu avec toutes les autres autorisations. Une connexion d’accès SQL est également créée.

Le compte bénéficie également des autorisations pour les fichiers, clés de Registre et autres objets en rapport avec le moteur de synchronisation.

### <a name="azure-ad-service-account"></a>Compte de service AD Azure
Un compte dans Active Directory Azure est créé pour une utilisation du service de synchronisation. Ce compte peut être identifié par son nom complet.

![Compte Active Directory](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Le nom du serveur utilisé sur le compte peut être identifié dans la deuxième partie du nom d’utilisateur. Dans l’image, le nom du serveur est FABRIKAMCON. Si vous avez mis en œuvre de serveurs, chaque serveur possède son propre compte. Il existe une limite de 10 comptes de service de synchronisation dans Azure AD.

Le compte de service est créé avec un mot de passe longs et complexe qui n’expire pas. Il est accordé un rôle spécial **Comptes de synchronisation d’annuaire** qui dispose uniquement des autorisations pour effectuer des tâches de synchronisation d’annuaire. Ce rôle intégré spécial ne peut être accordé à l’extérieur de l’Assistant Azure Connect d’Active Directory et le portail Azure montre ce compte **d’utilisateur**.

![Rôle de compte d’Active Directory](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](../active-directory-aadconnect.md).
