<properties
    pageTitle="Forum aux questions sur Azure plusieurs facteurs d’authentification"
    description="Fournit une liste de questions fréquentes et réponses relatives à l’authentification à plusieurs facteurs Azure. Plusieurs facteurs d’authentification est une méthode de vérification de l’identité d’un utilisateur qui nécessite plus d’un nom d’utilisateur et le mot de passe. Il fournit une couche supplémentaire de sécurité pour l’authentification de l’utilisateur et les transactions."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="azure-multi-factor-authentication-faq"></a>Forum aux questions sur Azure plusieurs facteurs d’authentification


Ce forum aux questions répond aux questions courantes sur l’authentification à plusieurs facteurs Azure et utilisant le service d’authentification à plusieurs facteurs, y compris les questions sur la facturation de modèle et facilité d’utilisation.

## <a name="general"></a>Général

**Q : comment Azure plusieurs facteurs d’authentification serveur ne gère pas les données utilisateur ?**

Avec le serveur d’authentification multifactorielle, les données utilisateur sont stockées uniquement sur les serveurs locaux. Aucune donnée utilisateur permanentes n’est stockée dans le nuage. Lorsque l’utilisateur effectue une vérification de deux étapes, plusieurs facteurs d’authentification envoie des données vers le service en nuage Azure, plusieurs facteurs d’authentification pour l’authentification. La communication entre le serveur de l’authentification multifactorielle et le service en nuage plusieurs facteurs d’authentification utilise Secure Sockets Layer (SSL) ou la sécurité TLS (Transport Layer) sur le port 443 sortant.

Lorsque les demandes d’authentification sont envoyées au service de cloud, les données sont collectées pour l’authentification et l’utilisation des rapports. Les champs de données inclus dans les journaux de vérification en deux étapes sont les suivantes :

- **Numéro d’identification unique** (un nom ou sur site plusieurs facteurs d’authentification serveur ID utilisateur)
- **Nom et prénom** (facultatif)
- **Adresse de courriel** (facultatif)
- **Numéro de téléphone** (lorsque vous utilisez un appel vocal ou une authentification de SMS)
- **Jeton de périphérique** (lorsque vous utilisez l’authentification de l’application mobile)
- **Mode d’authentification**
- **Résultat de l’authentification**
- **Nom de serveur d’authentification à plusieurs facteurs**
- **Plusieurs facteurs d’authentification serveur IP**
- **Client IP** (si disponible)

Les champs facultatifs peuvent être configurés dans plusieurs facteurs d’authentification serveur.

Le résultat de la vérification (succès ou refus) et la raison pour laquelle si elle a été refusée, est stockée avec les données d’authentification et est disponibles dans les rapports d’authentification et d’utilisation.


## <a name="billing"></a>Facturation

Peut répondre à la plupart des questions de facturation en vous reportant à la [page de tarification de l’authentification multifactorielle](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**Q : mon organisation facturée pour des appels téléphoniques ou des messages de texte sert à authentifier mes utilisateurs ?**

Organisations ne sont pas facturées pour des appels téléphoniques sont placés ou le texte messages envoyés aux utilisateurs par le biais de l’authentification multifactorielle Azure. Propriétaires de téléphone peuvent être facturés pour les appels téléphoniques ou des messages qu’ils reçoivent, en fonction de leur service de numéro de téléphone personnel.

**Q : fait de la redevance de modèle par utilisateur basée sur le nombre d’utilisateurs qui sont configurés pour utiliser une authentification multifacteur ou le nombre d’utilisateurs qui effectuent des vérifications ?**

Facturation est basée sur le nombre d’utilisateurs configurés pour utiliser une authentification multifacteur.

**Q : comment fonctionne la facturation de plusieurs facteurs d’authentification ?**

Lorsque vous utilisez le « par utilisateur » ou « par authentification » AMF modèle, Azure est une ressource basée sur la consommation. Les frais sont facturés à un abonnement Azure de l’organisation tout comme les ordinateurs virtuels, sites Web, etc..

Lorsque vous utilisez le modèle de licence, licences d’Azure, plusieurs facteurs d’authentification sont achetés et ensuite affectés aux utilisateurs, comme pour Office 365 et d’autres produits de l’abonnement.

**Q : y a-t-il une version gratuite d’Azure, plusieurs facteurs d’authentification pour les administrateurs ?**

Dans certains cas, Oui. Authentification à plusieurs facteurs pour les administrateurs d’Azure offre un sous-ensemble des fonctionnalités AMF d’Azure sans frais. Cette offre s’applique aux membres du groupe Administrateurs Global d’Azure dans les instances Azure Active Directory qui ne sont pas liées à un fournisseur d’authentification à plusieurs facteurs Azure en fonction de la consommation. À l’aide d’un fournisseur d’authentification à plusieurs facteurs met à niveau tous les administrateurs et les utilisateurs sont configurés pour utiliser une authentification multifacteur vers la version complète d’Azure, plusieurs facteurs d’authentification dans le répertoire.

**Q : y a-t-il une version gratuite d’Azure, plusieurs facteurs d’authentification pour les utilisateurs de Office 365 ?**

Dans certains cas, Oui. Authentification à plusieurs facteurs pour Office 365 offre un sous-ensemble des fonctionnalités AMF d’Azure sans frais. Cette offre s’applique aux utilisateurs qui possèdent une licence Office 365 affectée, lorsqu’un fournisseur d’authentification à plusieurs facteurs Azure en fonction de la consommation n’a pas été lié à l’instance correspondante d’Azure Active Directory. En utilisant le fournisseur d’authentification à plusieurs facteurs met à niveau tous les administrateurs et les utilisateurs sont configurés pour utiliser une authentification multifacteur vers la version complète d’Azure, plusieurs facteurs d’authentification dans le répertoire.

**Q : mon organisation passer par utilisateur et par authentification modèles de facturation de la consommation à tout moment ?**

Votre organisation choisit un modèle de facturation lorsqu’il crée une ressource. Vous ne pouvez pas modifier un modèle de facturation une fois que la ressource est mise en service. Vous pouvez, toutefois, créer une autre ressource d’authentification à plusieurs facteurs pour remplacer l’original. Impossible de transférer des paramètres utilisateur et les options de configuration pour la nouvelle ressource.

**Q mon organisation peut basculer entre la facturation de la consommation et le modèle de licence à tout moment ?**

Lorsque des licences sont ajoutés dans un répertoire qui dispose déjà d’un fournisseur d’authentification à plusieurs facteurs Azure par utilisateur, en fonction de la consommation de facturation est décrémentée du nombre de licences détenues. Si tous les utilisateurs configurés pour utiliser une authentification multifacteur ont des licences attribuées, l’administrateur peut supprimer le fournisseur d’authentification à plusieurs facteurs Azure.

Vous ne pouvez pas mélanger la facturation de la consommation par authentification avec un modèle de licence. Lorsqu’un fournisseur d’authentification à plusieurs facteurs par authentification est lié à un répertoire, l’organisation est facturée pour toutes les demandes de vérification d’authentification à plusieurs facteurs, indépendamment des licences détenues.

**Q : mon organisation doit-elle utiliser et synchroniser les identités pour utiliser l’authentification selon plusieurs facteurs Azure ?**

Lorsqu’une organisation utilise un modèle de facturation basée sur la consommation, Azure Active Directory n’est pas nécessaire. Liaison d’un fournisseur d’authentification à facteurs multiples dans un répertoire est facultative. Si votre organisation n’est pas liée à un répertoire, il peut déployer Azure plusieurs facteurs d’authentification serveur ou l’Azure SDK de l’authentification multicritères sur site.

Azure Active Directory est requis pour le modèle de licence car les licences sont ajoutés au répertoire lorsque vous achetez et les affecter aux utilisateurs dans l’annuaire.


## <a name="usability"></a>Facilité d’utilisation

**Q : quel est un utilisateur ? s’ils ne reçoivent une réponse sur leur téléphone, ou si le téléphone n’est pas disponible pour l’utilisateur**

Si l’utilisateur a configuré un téléphone de sauvegarde, ils doivent essayer à nouveau et sélectionnez ce téléphone lorsque vous y êtes invité dans la page de connexion. Si l’utilisateur ne possède pas une autre méthode configurée, l’administrateur de l’organisation peut mettre à jour le numéro de téléphone principal de l’utilisateur.


**Q : quel est l’administrateur ? si un utilisateur contacte l’administrateur sur un compte qui ne sont plus accessibles à l’utilisateur**

L’administrateur peut réinitialiser le compte d’utilisateur en posant la question pour accéder à nouveau à travers le processus d’inscription. En savoir plus sur la [Gestion des paramètres utilisateur et périphérique avec une authentification à facteurs multiples Azure dans le nuage](multi-factor-authentication-manage-users-and-devices.md).

**Q : que fait un administrateur ? si le téléphone d’un utilisateur qui est à l’aide de mots de passe app est perdu ou volé**

L’administrateur peut supprimer les mots de passe de tous les utilisateurs application pour empêcher les accès non autorisés. Une fois que l’utilisateur dispose d’une unité de rechange, l’utilisateur peut recréer les mots de passe. En savoir plus sur la [Gestion des paramètres utilisateur et périphérique avec une authentification à facteurs multiples Azure dans le nuage](multi-factor-authentication-manage-users-and-devices.md).

**Q : que se passe-t-il si l’utilisateur ne peut pas vous connecter à des applications de navigateur non ?**

Un utilisateur qui est configuré pour utiliser une authentification multifactorielle nécessite un mot de passe pour vous connecter à des applications autres que des navigateurs app. Un utilisateur doit effacer les informations de connexion (supprimer), redémarrez l’application et vous connecter en utilisant leur utilisateur nom et application de mot de passe.

Obtenir plus d’informations sur la création de mots de passe app et autres [aide de mots de passe de l’application](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Authentification moderne pour les clients Office 2013
>
> Les clients Office 2013 (y compris Outlook) prend en charge les nouveaux protocoles d’authentification. Vous pouvez configurer Office 2013 pour prendre en charge d’authentification à plusieurs facteurs. Une fois que vous configurez Office 2013, les mots de passe app ne sont pas nécessaires pour les clients Office 2013. Pour plus d’informations, consultez l' [annonce de présentation publique d’authentification modernes Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Q : quel est un utilisateur ? si l’utilisateur ne reçoit pas un message de texte, ou si l’utilisateur répond à un message de texte bidirectionnel, mais la vérification de l’expiration**

Envoyer des messages texte, et réception des réponses dans SMS bidirectionnelle n’est pas garantie car il existe des facteurs incontrôlables susceptibles d’affecter la fiabilité du service. Ces facteurs incluent le pays de destination, l’opérateur de téléphonie mobile et l’intensité du signal.

Les utilisateurs qui rencontrent des difficultés à réception fiable de messages texte Sélectionnez à la place la méthode app ou appel téléphonique mobile. L’application mobile peut recevoir des notifications à la fois sur les connexion cellulaires et Wi-Fi. En outre, l’application mobile peut générer des codes de vérification même lorsque le périphérique n’a pas de signal. L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Si vous devez utiliser des messages texte, nous vous recommandons d’à l’aide de SMS unidirectionnelles plutôt que SMS bidirectionnel lorsque cela est possible. SMS à sens unique est plus fiable, et il empêche les utilisateurs de ses frais SMS globales à partir de la réponse à un message de texte qui a été envoyé à partir d’un autre pays.


**Q : puis-je utiliser des jetons matériels avec Azure plusieurs facteurs d’authentification serveur ?**

Si vous utilisez le serveur de l’authentification multifactorielle Azure, vous pouvez importer des jetons de mot de passe unique, basée sur le temps (TOTP) l’authentification ouverte (serment) tiers et les utiliser pour la vérification des deux étapes.

Vous pouvez utiliser les jetons de ActiveIdentity qui sont des jetons de serment TOTP si vous placez le fichier de clé secrète dans un fichier CSV et les importer vers Azure plusieurs facteurs d’authentification serveur. Vous pouvez utiliser les jetons de serment avec Active Directory Federation Services (ADFS), accès à distance utilisateur Service RADIUS (Remote Authentication) lorsque le système client peut traiter les réponses de stimulation de l’accès et l’authentification basée sur les formulaires d’Internet Information Server (IIS).

Vous pouvez importer des jetons de serment TOTP tiers avec les formats suivants :  
- Conteneur portable de clé symétrique (PSKC)  
- CSV si le fichier contient un numéro de série, une clé secrète au format Base 32 et un intervalle de temps  

**Q : puis-je utiliser Azure plusieurs facteurs d’authentification serveur pour sécuriser les Services Terminal Server ?**

Oui, mais, si vous êtes à l’aide de Windows Server 2012 R2 ou une version ultérieure, uniquement à l’aide de passerelle des services Bureau à distance (passerelle des services Bureau à distance).

Modifications de sécurité dans Windows Server 2012 R2 ont changé la façon dont le serveur de l’authentification multifactorielle Azure se connecte pour le package de sécurité de protection continue des données (LSA, Local Security Authority) dans Windows Server 2012 et les versions antérieures. Pour les versions des Services Terminal Server dans Windows Server 2012 ou version antérieure, vous pouvez [sécuriser une application avec l’authentification Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Si vous utilisez Windows Server 2012 R2, vous avez besoin de passerelle Bureau à distance.

**Q : pourquoi un utilisateur reçoit un appel plusieurs facteurs d’authentification d’un appelant anonyme après la configuration des ID de l’appelant ?**

Lorsque des appels de plusieurs facteurs d’authentification sont placés par l’intermédiaire du réseau téléphonique public, parfois ils sont acheminés via un opérateur qui ne prennent pas en charge les ID de l’appelant. De ce fait, ID de l’appelant n’est pas garanti, même si le système d’authentification à plusieurs facteurs envoie toujours.


## <a name="errors"></a>Erreurs

**Q : que doivent faire les utilisateurs s’ils voir un message d’erreur « requête d’authentification n’est pas pour un compte activé » lors de l’utilisation de notifications de l’application mobile ?**

Dites-leur de suivre cette procédure pour supprimer leur compte de l’application mobile, puis ajoutez-la à nouveau :

1. Accédez à [votre profil de portail Azure](https://account.activedirectory.windowsazure.com/profile/) et connectez-vous à votre compte d’organisation.
2. Sélectionnez la **vérification de sécurité supplémentaires**.
4. Supprimer le compte existant à partir de l’application mobile.
5. Cliquez sur **configurer**et suivez les instructions pour reconfigurer l’application mobile.


**Q : que doivent faire les utilisateurs s’ils voir un message d’erreur 0x800434D4L lorsque vous vous connectez à une application de navigateur non ?**

Actuellement, un utilisateur peut utiliser la vérification de la sécurité supplémentaires qu’avec les applications et les services auxquels l’utilisateur peut accéder via un navigateur. Les applications non-navigateur (également appelées *applications clientes riches*) qui sont installées sur un ordinateur local, comme Windows PowerShell, ne fonctionne pas avec les comptes nécessitant une vérification de sécurité supplémentaires. Dans ce cas, l’utilisateur peut voir l’application génère une erreur 0x800434D4L.

Une solution de contournement pour cela est d’avoir des utilisateurs distincts des comptes pour administration liées et les opérations non-admin. Par la suite, vous pouvez lier des boîtes aux lettres entre votre compte d’administrateur et un compte non administrateur pour que vous pouvez vous connecter à Outlook avec votre compte non-administrateur. Pour plus d’informations à ce sujet, découvrez comment [donner à un administrateur la possibilité d’ouvrir et d’afficher le contenu de la boîte aux lettres de l’utilisateur](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Étapes suivantes

Si votre question n’est pas réponse ici, veuillez laisser dans les commentaires au bas de la page. Ou bien, voici quelques options supplémentaires pour obtenir de l’aide :


**Q : Comment puis-je obtenir de l’aide avec authentification à plusieurs facteurs Azure ?**

- Rechercher des solutions aux problèmes techniques courants, la [Base de connaissances Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) .

- Rechercher et parcourir des questions techniques et des réponses de la Communauté ou poser vos propres questions dans les [forums de Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- Si vous êtes un client hérité de PhoneFactor et si vous avez des questions ou besoin d’aide pour réinitialiser un mot de passe, cliquez sur le lien de [réinitialisation de mot de passe](mailto:phonefactorsupport@microsoft.com) pour ouvrir une demande de support.

- Contactez un professionnel du support technique par le biais de [la prise en charge du serveur de l’authentification multifactorielle Azure (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Lorsque vous nous contactez, il est utile si vous pouvez inclure autant d’informations concernant votre problème possible. Vous pouvez fournir des informations comprennent la page où vous l’avez vu l’erreur, le code d’erreur spécifique, l’ID de session spécifiques et l’ID de l’utilisateur qui ont vu l’erreur.
