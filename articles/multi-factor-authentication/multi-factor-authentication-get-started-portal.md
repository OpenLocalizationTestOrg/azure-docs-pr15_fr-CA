<properties 
    pageTitle="Déploiement du portail de l’utilisateur pour le serveur de l’authentification multifactorielle Azure"
    description="Il s’agit de la page d’authentification Azure plusieurs facteurs qui décrit la procédure de mise en route avec Azure AMF et le portail de l’utilisateur."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="deploying-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Déploiement du portail de l’utilisateur pour le serveur de l’authentification multifactorielle Azure

Le portail de l’utilisateur permet à l’administrateur installer et configurer le portail Azure plusieurs facteurs d’authentification utilisateur. Le portail de l’utilisateur est un site web IIS qui permet aux utilisateurs de s’inscrire dans une authentification à facteurs multiples Azure et gérer leurs comptes. Un utilisateur peut modifier leur numéro de téléphone, modifier leur code PIN ou ignorer Azure une authentification multifacteur lors de leur prochaine ouverture de session sur.

Les utilisateurs se connecter sur le portail de l’utilisateur à l’aide de leur nom d’utilisateur normal et d’un mot de passe et sera soit effectuer un appel d’Azure, plusieurs facteurs d’authentification ou répondre à des questions de sécurité pour terminer leur authentification. Si l’inscription de l’utilisateur est autorisée, l’utilisateur sera configurer leur numéro de téléphone et le code PIN la première fois qu’ils ouvrent une session sur le portail de l’utilisateur.

Les administrateurs du portail utilisateur peut être paramétrés et autorisés à ajouter de nouveaux utilisateurs et de mettre à jour les utilisateurs existants.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploying-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Déploiement du portail de l’utilisateur sur le même serveur que le serveur de l’authentification multifactorielle Azure

Les composants requis suivants sont requis pour l’installation du portail aux utilisateurs sur le même serveur que le serveur de l’authentification multifactorielle Azure :

- IIS doit être installé, y compris asp.net et compatibilité de base IIS 6 meta (pour IIS 7 ou version ultérieure)
- Connecté utilisateur doit disposer des droits d’administrateur pour l’ordinateur et le domaine le cas échéant.  C’est parce que le compte a besoin d’autorisations pour créer des groupes de sécurité Active Directory.

### <a name="to-deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Pour déployer le portail de l’utilisateur pour le serveur de l’authentification multifactorielle Azure

1. Au sein du serveur de l’authentification multifactorielle Azure : cliquez sur l’icône Portal de l’utilisateur dans le menu de gauche, cliquez sur le bouton d’installation User Portal.
1. Cliquez sur Suivant.
1. Cliquez sur Suivant.
1. Si l’ordinateur est joint à un domaine et que la configuration de Active Directory pour sécuriser les communications entre le portail de l’utilisateur et le service d’authentification à plusieurs facteurs Azure n’est pas terminée, l’étape de Active Directory s’affichera. Cliquez sur le bouton suivant pour compléter automatiquement cette configuration.
1. Cliquez sur Suivant.
1. Cliquez sur Suivant.
1. Cliquez sur Fermer.
1. Ouvrez un navigateur web à partir de n’importe quel ordinateur et accéder à l’URL où a été installé le portail de l’utilisateur (par exemple, https://www.publicwebsite.com/MultiFactorAuth). Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploying-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Déployer le portail d’utilisateur de serveur Azure plusieurs facteurs d’authentification sur un serveur distinct

Pour utiliser l’application de l’authentification multifactorielle Azure, suivantes sont requises pour que l’application peut communiquer avec le portail de l’utilisateur :

Consultez matérielle et logicielle requise pour la configuration matérielle et logicielle :

- Vous devez utiliser une version 6.0 ou ultérieure du serveur d’authentification selon plusieurs facteurs Azure.
- Portail de l’utilisateur doit être installé sur un serveur de web orienté Internet Microsoft® Internet Information Services (IIS) en cours d’exécution 6.x, IIS 7.x ou version ultérieure.
- Lorsque vous utilisez IIS 6.x, vérifiez ASP.NET v2.0.50727 est installé, inscrit et autorisé.
- Requises des services de rôle lors de l’utilisation de IIS 7.x ou inclure plus de ASP.NET et compatibilité avec la métabase IIS 6.
- Portail de l’utilisateur doit être sécurisé avec un certificat SSL.
- Azure plusieurs facteurs d’authentification Web Service SDK doit être installé dans IIS 6.x, IIS 7.x ou version ultérieure sur le serveur n’est installé sur le serveur de l’authentification multifactorielle Azure.
- Le Kit de développement de logiciel Azure plusieurs facteurs d’authentification Web Service doivent être sécurisée avec un certificat SSL.
- Portail de l’utilisateur doit être capable de se connecter au SDK de Service Azure plusieurs facteurs d’authentification Web via le protocole SSL.
- Portail de l’utilisateur doit être en mesure de s’authentifier sur le Azure plusieurs facteurs d’authentification SDK du Service Web en utilisant les informations d’identification d’un compte de service qui est membre d’un groupe de sécurité appelé « PhoneFactor Admins ». Ce compte de service et un groupe existent dans Active Directory si le serveur de l’authentification multifactorielle Azure s’exécute sur un serveur à un domaine. Ce compte de service et un groupe existent localement sur le serveur de l’authentification multifactorielle Azure s’il n’est pas joint à un domaine.

L’installation du portail de l’utilisateur sur un serveur autre que le serveur de l’authentification multifactorielle Azure requiert les trois étapes suivantes :

1. Installer le Kit de développement de service web
2. Installez le portail de l’utilisateur
3. Configurer les paramètres utilisateur du portail sur le serveur d’authentification à plusieurs facteurs Azure


### <a name="install-the-web-service-sdk"></a>Installer le Kit de développement de service web

Si le Kit de développement de logiciel Azure plusieurs facteurs d’authentification Web Service n’est pas déjà installé sur le serveur de l’authentification multifactorielle Azure, accéder à ce serveur et ouvrez le serveur de l’authentification multicritères Azure. Cliquez sur l’icône du SDK du Service Web, cliquez sur le Kit de développement de Service d’installation de Web... bouton et suivez les instructions affichées. Le Kit de développement de Service Web doit être sécurisé avec un certificat SSL. Un certificat auto-signé est OK à cet effet, mais il doit être importé dans le magasin « Autorités principales de confiance » du compte d’ordinateur Local sur le serveur de web Portal de l’utilisateur afin qu’il feront confiance à ce certificat lors de l’initialisation de la connexion SSL.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="install-the-user-portal"></a>Installez le portail de l’utilisateur

Avant d’installer le portail de l’utilisateur sur un serveur distinct, prenez les éléments suivants :

- Il est utile d’ouvrir un navigateur web sur le serveur web via Internet et accédez à l’URL du Kit de développement de Service Web qui ont été entrées dans le fichier web.config. Si le navigateur peut obtenir avec succès au service web, il vous invite d’informations d’identification. Entrez le nom d’utilisateur et le mot de passe qui ont été entrées dans le fichier web.config, exactement comme il apparaît dans le fichier. Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.
- Si un pare-feu ou un proxy inverse est assis devant le serveur web Portal de l’utilisateur et effectuer le déchargement SSL, vous pouvez modifier le fichier web.config de portail de l’utilisateur et ajoutez la clé suivante dans le <appSettings> section afin que le portail utilisateur puissent utiliser http au lieu de https. <add key="SSL_REQUIRED" value="false"/>

#### <a name="to-install-the-user-portal"></a>Pour installer le portail de l’utilisateur

1. Ouvrez l’Explorateur Windows sur le serveur du serveur de l’authentification multifactorielle Azure et naviguez jusqu’au dossier où est installé le serveur de l’authentification multifactorielle Azure (par exemple, C:\Program Files\Multi-facteur d’authentification serveur). Choisir la version 32 bits ou 64 bits du fichier installation MultiFactorAuthenticationUserPortalSetup comme appropriés pour le serveur qui sera installée sur le portail de l’utilisateur. Copiez le fichier d’installation sur le serveur via Internet.
2. Sur le serveur web via Internet, le fichier d’installation doit être exécuté avec des droits d’administrateur. La façon la plus simple pour ce faire est d’ouvrir une invite de commande en tant qu’administrateur et accédez à l’emplacement où le fichier d’installation a été copié.
3. Exécutez le fichier d’installation MultiFactorAuthenticationUserPortalSetup64, modifiez le nom du Site et le répertoire virtuel si vous le souhaitez.
4. Après avoir terminé l’installation du portail utilisateur, accédez à C:\inetpub\wwwroot\MultiFactorAuth (ou répertoire approprié basé sur le nom du répertoire virtuel) et modifier le fichier web.config.
5. Recherchez la clé USE_WEB_SERVICE_SDK et modifiez la valeur de false à true. Recherchez les clés de WEB_SERVICE_SDK_AUTHENTICATION_USERNAME et de WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD et de définir les valeurs pour le nom d’utilisateur et le mot de passe du compte de service qui est un membre de la sécurité PhoneFactor Admins groupe (voir la section Configuration requise ci-dessus). Veillez à entrer le nom d’utilisateur et le mot de passe entre les guillemets à la fin de la ligne (valeur = "" / >). Il est recommandé d’utiliser un nom d’utilisateur complet (par exemple, DOMAINE\nom d’utilisateur ou ordinateur om d’utilisateur)
6. Recherchez le paramètre pfup_pfwssdk_PfWsSdk et modifiez la valeur de « http://localhost:4898/PfWsSdk.asmx » à l’URL du Kit de développement de Service Web qui s’exécute sur le serveur de l’authentification multifactorielle Azure (par exemple, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Étant donné que SSL est utilisé pour cette connexion, vous devez référencer le Kit de développement de Service Web par le nom du serveur et non pas l’adresse IP dans la mesure où le certificat SSL ne sera ont été émis pour le nom du serveur et de l’URL utilisée doit correspondre au nom sur le certificat. Si le nom du serveur ne résout pas à une adresse IP du serveur via Internet, ajoutez une entrée au fichier hosts sur ce serveur pour mapper le nom du serveur d’authentification selon plusieurs facteurs Azure à son adresse IP. Enregistrez le fichier web.config après que des modifications ont été apportées.
7. Si le site Web que User Portal a été installé sous (par exemple, Site Web par défaut) n’a pas déjà été relié à l’aide d’un certificat public signé, installer le certificat sur le serveur si ce n’est pas déjà installé, ouvrez le Gestionnaire des services IIS et lier le certificat au site Web.
8. Ouvrez un navigateur web à partir de n’importe quel ordinateur et accéder à l’URL où a été installé le portail de l’utilisateur (par exemple, https://www.publicwebsite.com/MultiFactorAuth). Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.



## <a name="configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurer les paramètres utilisateur du portail sur le serveur de l’authentification multifactorielle Azure
Maintenant que le portail est installé, vous devez configurer le serveur d’authentification selon plusieurs facteurs Azure pour travailler avec le portail.

Serveur d’authentification à plusieurs facteurs Azure fournit plusieurs options pour le portail de l’utilisateur.  Le tableau suivant fournit une liste de ces options et d’obtenir une explication de ce qu’ils sont utilisés pour.

Paramètres utilisateur du portail|Description|
:------------- | :------------- |
URL du portail utilisateur| Vous permet d’entrer l’URL de l’endroit où le portail est hébergé.
Authentification principale| Vous permet de spécifier le type d’authentification à utiliser lorsque vous vous connectez au portail.  Authentification Windows, Radius ou LDAP.
Autoriser les utilisateurs à se connecter|Permet aux utilisateurs d’entrer un nom d’utilisateur et le mot de passe sur la page de connexion pour le portail de l’utilisateur.  Si ce n’est pas sélectionné, les zones sont grisés.
Autoriser l’inscription de l’utilisateur|Permet à l’utilisateur pour s’inscrire dans une authentification à facteurs multiples grâce à un écran d’installation qui les invite à entrer des informations supplémentaires comme le numéro de téléphone.  Invite pour téléphone de secours permet aux utilisateurs de spécifier un numéro de téléphone secondaire.  Demander les tiers jeton de serment permet aux utilisateurs de spécifier un jeton de serment 3ème partie.
Permettre aux utilisateurs de lancer le contournement de l’unique| Cela permet aux utilisateurs de lancer une exception unique.  Si cette installation il prendra un ensembles d’utilisateurs affectent la prochaine fois que l’utilisateur se connecte.  Demander un contournement secondes fournit à l’utilisateur une boîte afin qu’ils peuvent modifier la valeur par défaut de 300 secondes.  Dans le cas contraire, le contournement unique convient uniquement pour les 300 secondes.
Permettre aux utilisateurs de sélectionner la méthode| Permet aux utilisateurs de spécifier leur méthode de contact principale.  Cela peut être appel téléphonique, message texte, application mobile ou jeton de serment.
Permettre aux utilisateurs de sélectionner la langue|  Permet à l’utilisateur de modifier la langue utilisée pour l’appel téléphonique, message texte, application mobile ou jeton de serment.
Permettre aux utilisateurs d’activer l’application mobile| Permet aux utilisateurs de générer un code d’activation pour terminer le processus d’activation d’application mobile est utilisé avec le serveur.  Vous pouvez également définir le nombre de périphériques qu’ils peuvent activer ce sur.  Entre 1 et 10.
Utilisez les questions de sécurité de secours|Vous permet d’utiliser des questions de sécurité en cas d’échec de l’authentification à plusieurs facteurs.  Vous pouvez spécifier le nombre de questions de sécurité qui doivent être traitées avec succès.
Permettre aux utilisateurs d’associer des tiers serment jeton| Permet aux utilisateurs de spécifier un jeton de serment tiers.
Utilisez le jeton de serment de secours|Permet l’utilisation d’un jeton de serment que plusieurs facteurs d’authentification n’est pas réussie.  Vous pouvez également spécifier le délai d’expiration de la session en minutes.
Activer la journalisation|Active la journalisation sur le portail de l’utilisateur.  Les fichiers journaux se trouvent dans : C:\Program Files\Multi-facteur d’authentification Server\Logs.

La plupart de ces paramètres sont visibles à l’utilisateur une fois qu’ils sont activés et les signes de l’utilisateur dans le portail de l’utilisateur.

![Paramètres utilisateur du portail](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Pour configurer les paramètres utilisateur du portail sur le serveur de l’authentification multifactorielle Azure




1. Dans le serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de portail de l’utilisateur. Sous l’onglet Paramètres, entrez l’URL vers le portail de l’utilisateur dans la zone de texte URL du portail utilisateur. Cette URL sera insérée dans les messages électroniques qui sont envoyés aux utilisateurs lorsqu’ils sont importés dans le serveur de l’authentification multifactorielle Azure si la fonctionnalité de courrier électronique a été activée.
2. Choisissez les paramètres que vous souhaitez utiliser dans le portail de l’utilisateur. Par exemple, si les utilisateurs sont autorisés à contrôler les méthodes d’authentification, assurez-vous que l’option Autoriser les utilisateurs à sélectionner la méthode est activée ainsi que les méthodes, ils peuvent choisir à partir de.
3. Cliquez sur le lien aide dans le coin supérieur droit pour comprendre les paramètres affichés.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Onglet administrateurs
Cet onglet vous permet simplement d’ajouter les utilisateurs ayant des privilèges d’administrateur.  Lors de l’ajout d’un administrateur, vous pouvez régler les autorisations qu’ils reçoivent.  De cette façon, vous pouvez être sûr de n’autoriser que les autorisations nécessaires à l’administrateur.  Cliquez simplement sur le bouton Ajouter et sélectionnez utilisateur et leurs autorisations et puis cliquez sur Ajouter.

![Administrateurs du portail utilisateur](./media/multi-factor-authentication-get-started-portal/admin.png)


## <a name="security-questions"></a>Questions de sécurité
Cet onglet vous permet de spécifier les questions de sécurité que les utilisateurs devront fournir les réponses aux si les questions de sécurité d’utilisation de l’option de secours est sélectionné.  Serveur d’authentification selon plusieurs facteurs Azure est fourni avec les questions par défaut que vous pouvez utiliser.  Vous pouvez également modifier l’ordre ou ajouter vos propres questions.  Lors de l’ajout de vos propres questions, vous pouvez spécifier la langue que vous souhaitez que ces apparaissent également dans la question.

![Questions de sécurité portail utilisateur](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## <a name="passed-sessions"></a>Sessions passées

## <a name="saml"></a>SAML
Vous permet de configurer le portail utilisateur pour accepter les demandes de remboursement auprès d’un fournisseur d’identité à l’aide de SAML.  Vous pouvez spécifier la délai d’expiration session, spécifiez le certificat de vérification et de la déconnexion des URL de redirection.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>Adresses IP autorisées
Cet onglet permet de spécifier des adresses IP uniques ou plages d’adresses IP qui peuvent être ajoutés afin que si un utilisateur est connecté à partir d’une de ces adresses IP, plusieurs facteurs d’authentification est ignorée.

![Portail de l’utilisateur des adresses IP autorisées](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Inscription d’utilisateur de libre Service
Si vous souhaitez que vos utilisateurs à se connecter et de s’inscrire, vous devez sélectionner l’autoriser les utilisateurs à connexion et autoriser des options d’inscription de l’utilisateur. N’oubliez pas que les paramètres que vous sélectionnez affecte l’expérience de connexion utilisateur.

Par exemple, lorsqu’un utilisateur ouvre une session le portail de l’utilisateur et clique sur le bouton se connecter, ils sont utilisées à la page le programme d’installation de Azure plusieurs facteurs d’authentification utilisateur.  En fonction de comment vous avez configuré l’authentification selon plusieurs facteurs Azure, l’utilisateur peut être en mesure de sélectionner leur méthode d’authentification.  

Sélectionnez la méthode d’authentification vocale appeler ou ont été préconfigurés pour utiliser cette méthode, la page vous invite l’utilisateur à entrer leur numéro de téléphone principal et leur extension, le cas échéant.  Ils peuvent également être autorisés à entrer un numéro de téléphone de sauvegarde.  

![Portail de l’utilisateur des adresses IP autorisées](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Si l’utilisateur est tenu d’utiliser un code PIN lors de l’authentification, la page également les invite à entrer un code PIN.  Après avoir entré leurs numéros de téléphone et le PIN (le cas échéant), l’utilisateur clique sur l’appeler m’authentifier bouton.  Authentification à plusieurs facteurs Azure effectue une authentification d’appel téléphonique pour le numéro de téléphone principal de l’utilisateur.  L’utilisateur doit répondre à l’appel téléphonique et entrer son code PIN (le cas échéant) et appuyez sur la touche # pour passer à l’étape suivante du processus d’inscription automatique.   

Si l’utilisateur sélectionne la méthode d’authentification de texte SMS ou a été préconfiguré pour utiliser cette méthode, la page invite l’utilisateur pour leur numéro de téléphone portable.  Si l’utilisateur est tenu d’utiliser un code PIN lors de l’authentification, la page également les invite à entrer un code PIN.  Après avoir entré leur numéro de téléphone et un code PIN (le cas échéant), l’utilisateur clique sur le texte m’authentifier bouton.  Authentification à plusieurs facteurs Azure effectue une authentification de SMS à un téléphone mobile de l’utilisateur.  L’utilisateur doit recevoir le SMS qui contient un 1-heure-mot de passe (OTP) et la réponse au message avec cet usage unique plus son code PIN, le cas échéant) pour passer à l’étape suivante du processus d’inscription automatique.

![Portail de l’utilisateur SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

Si l’utilisateur sélectionne la méthode d’authentification de Mobile application ou a été préconfiguré pour utiliser cette méthode, la page vous invite à l’utilisateur d’installer l’application Azure une authentification multifactorielle sur leur appareil et générer un code d’activation.  Après avoir installé l’application Azure, plusieurs facteurs d’authentification, l’utilisateur clique sur le bouton Générer le Code d’Activation.    

>[AZURE.NOTE]Pour utiliser l’application Azure, plusieurs facteurs d’authentification, l’utilisateur doit activer les notifications de type pousser à leur appareil.

La page affiche un code d’activation et une URL et une image de code-barres.  Si l’utilisateur est tenu d’utiliser un code PIN lors de l’authentification, la page également les invite à entrer un code PIN.  L’utilisateur insère le code d’activation et l’URL dans l’application Azure, plusieurs facteurs d’authentification ou utilise le lecteur de codes-barres pour numériser l’image de code-barres et clique sur le bouton Activer.    

Une fois l’activation terminée, l’utilisateur clique sur le bouton authentifier maintenant.  Authentification à plusieurs facteurs Azure effectue une authentification pour l’application mobile de l’utilisateur.  L’utilisateur doit entrer son code PIN (le cas échéant) et appuyez sur le bouton authentifier dans leur application mobile pour passer à l’étape suivante du processus d’inscription automatique.  


Si les administrateurs ont configuré le serveur d’authentification selon plusieurs facteurs Azure pour collecter les réponses et les questions de sécurité, l’utilisateur est ensuite dirigé vers la page des Questions de sécurité.  L’utilisateur doit sélectionner quatre questions de sécurité et fournir des réponses à leurs questions sélectionnées.    

![Questions de sécurité portail utilisateur](./media/multi-factor-authentication-get-started-portal/secq.png)  

L’auto-inscription de l’utilisateur est maintenant terminée et que l’utilisateur a ouvert une session le portail de l’utilisateur.  Les utilisateurs peuvent se reconnecter au portail utilisateur à tout moment dans le futur pour modifier leurs numéros de téléphone, les broches, les méthodes d’authentification et les questions de sécurité si autorisé par leurs administrateurs.
