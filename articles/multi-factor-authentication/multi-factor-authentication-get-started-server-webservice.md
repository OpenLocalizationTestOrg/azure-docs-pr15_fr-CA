<properties 
    pageTitle="Mise en route du Service Web de App AMF serveur Mobile"
    description="L’application de l’authentification multifactorielle Azure offre une option supplémentaire d’authentification d’out-of-band.  Il permet au serveur d’AMF d’utiliser des notifications de type Pousser aux utilisateurs."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="getting-started-the-mfa-server-mobile-app-web-service"></a>Mise en route du Service Web de App AMF serveur Mobile

L’application de l’authentification multifactorielle Azure offre une option supplémentaire d’authentification d’out-of-band. Au lieu de placer un appel de téléphone automatique ou un SMS à l’utilisateur lors de la connexion, Azure, plusieurs facteurs d’authentification exécute un push d’une notification à l’application de l’authentification multifactorielle Azure sur tablette ou smartphone de l’utilisateur. L’utilisateur clique simplement sur « Authentification » (ou entre un NIP et clique sur « Authentification ») dans l’application à se connecter.

Pour utiliser l’application de l’authentification multifactorielle Azure, suivantes sont requises pour que l’application peut communiquer avec le Service application Web Mobile :

- Consultez matérielle et logicielle requise pour la configuration matérielle et logicielle
- Vous devez utiliser une version 6.0 ou ultérieure du serveur d’authentification selon plusieurs facteurs Azure
- Le Service application Web mobile doit être installé sur un serveur de web orienté Internet exécutant Microsoft® Internet Information Services (IIS) IIS 7.x ou version ultérieure.  Pour plus d’informations sur IIS, consultez [IIS.NET](http://www.iis.net/).
- Assurez-vous que v4.0.30319 ASP.NET est installée, inscrit et autorisé
- Services de rôle requis sont ASP.NET et la compatibilité avec la métabase IIS 6
- Le Service application Web mobile doit être accessible via une URL publique
- Le Service application Web mobile doit être sécurisé avec un certificat SSL.
- Azure plusieurs facteurs d’authentification Web Service SDK doit être installé dans IIS 7.x ou version ultérieure sur le serveur que le serveur de l’authentification multifactorielle Azure
- Le Kit de développement de logiciel Azure plusieurs facteurs d’authentification Web Service doivent être sécurisée avec un certificat SSL.
- Le Service application Web mobile doit être en mesure de se connecter à Azure plusieurs facteurs d’authentification Web Service SDK via SSL
- Le Service application Web mobile doit être en mesure d’authentifier à la Azure plusieurs facteurs d’authentification SDK du Service Web en utilisant les informations d’identification d’un compte de service qui est membre d’un groupe de sécurité appelé « Administrateurs de PhoneFactor ». Ce compte de service et un groupe existent dans Active Directory si le serveur de l’authentification multifactorielle Azure s’exécute sur un serveur à un domaine. Ce compte de service et un groupe existent localement sur le serveur de l’authentification multifactorielle Azure s’il n’est pas joint à un domaine.


L’installation du portail de l’utilisateur sur un serveur autre que le serveur de l’authentification multifactorielle Azure requiert les trois étapes suivantes :

1. Installer le Kit de développement de service web
2. Installer le service web application mobile
3. Configurer les paramètres d’application mobile sur le serveur de l’authentification multifactorielle Azure
4. Activer l’application de l’authentification multifactorielle Azure pour les utilisateurs finaux

## <a name="install-the-web-service-sdk"></a>Installer le Kit de développement de service web

Si le Kit de développement de logiciel Azure plusieurs facteurs d’authentification Web Service n’est pas déjà installé sur le serveur de l’authentification multifactorielle Azure, accéder à ce serveur et ouvrez le serveur de l’authentification multicritères Azure. Cliquez sur l’icône du SDK du Service Web, cliquez sur le Kit de développement de Service d’installation de Web... bouton et suivez les instructions affichées. Le Kit de développement de Service Web doit être sécurisé avec un certificat SSL. Un certificat auto-signé est OK à cet effet, mais il doit être importé dans le magasin « Autorités principales de confiance » du compte d’ordinateur Local sur le serveur de web Portal de l’utilisateur afin qu’il feront confiance à ce certificat lors de l’initialisation de la connexion SSL.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## <a name="install-the-mobile-app-web-service"></a>Installer le service web application mobile
Avant d’installer le service web application mobile, prenez les éléments suivants :

- Si le portail Azure plusieurs facteurs d’authentification utilisateur est déjà installé sur le serveur via Internet, le nom d’utilisateur, le mot de passe et l’URL pour le Kit de développement de Service Web peuvent être copiées à partir du fichier web.config du portail de l’utilisateur.
- Il est utile d’ouvrir un navigateur web sur le serveur web via Internet et accédez à l’URL du Kit de développement de Service Web qui ont été entrées dans le fichier web.config. Si le navigateur peut obtenir avec succès au service web, il vous invite d’informations d’identification. Entrez le nom d’utilisateur et le mot de passe qui ont été entrées dans le fichier web.config, exactement comme il apparaît dans le fichier. Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.
- Si un pare-feu ou un proxy inverse est assis devant le serveur web le Service application Web Mobile et effectuez le déchargement SSL, vous pouvez modifier le fichier web.config de Mobile application Web Service et ajoutez la clé suivante à la <appSettings> section afin que le Service Web d’application Mobile peut utiliser http au lieu de https. Cependant, SSL est toujours requis à partir de l’application Mobile pour le pare-feu/proxy inverse. <add key="SSL_REQUIRED" value="false"/>

### <a name="to-install-the-mobile-app-web-service"></a>Pour installer le service web application mobile

<ol>
<li>Ouvrez l’Explorateur Windows sur le serveur de l’authentification multifactorielle Azure et naviguez jusqu’au dossier où est installé le serveur de l’authentification multifactorielle Azure (par exemple, C:\Program Files\Azure plusieurs facteurs d’authentification). Choisir la version 32 bits ou 64 bits du fichier installation Azure plusieurs facteurs AuthenticationPhoneAppWebServiceSetup comme appropriés pour le serveur qui sera installé sur le Service application Web Mobile. Copiez le fichier d’installation sur le serveur via Internet.</li>

<li>Sur le serveur web via Internet, le fichier d’installation doit être exécuté avec des droits d’administrateur. La façon la plus simple pour ce faire est d’ouvrir une invite de commande en tant qu’administrateur et accédez à l’emplacement où le fichier d’installation a été copié.</li>  

<li>Exécutez le fichier d’installation AuthenticationMobileAppWebServiceSetup de plusieurs facteurs, modifier le Site si vous le souhaitez et modifiez le répertoire virtuel un nom court, par exemple « PA ». Un nom court de répertoire virtuel est recommandé dans la mesure où les utilisateurs doivent entrer l’URL du Service Web application Mobile dans le périphérique mobile lors de l’activation.</li>

<li>Après avoir terminé l’installation de l’AuthenticationMobileAppWebServiceSetup de plusieurs facteurs d’Azure, accédez à C:\inetpub\wwwroot\PA (ou répertoire approprié basé sur le nom du répertoire virtuel) et modifier le fichier web.config.</li>  

<li>Recherchez les clés de WEB_SERVICE_SDK_AUTHENTICATION_USERNAME et de WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD et de définir les valeurs pour le nom d’utilisateur et le mot de passe du compte de service qui est un membre de la sécurité PhoneFactor Admins groupe (voir la section Configuration requise ci-dessus). Cela peut être le même compte que celui utilisé comme identité du portail Azure plusieurs facteurs d’authentification utilisateur si qui a été précédemment installé. Veillez à entrer le nom d’utilisateur et le mot de passe entre les guillemets à la fin de la ligne (valeur = "" / >). Il est recommandé d’utiliser un nom d’utilisateur complet (par exemple, DOMAINE\nom d’utilisateur ou ordinateur om d’utilisateur).</li>  

<li>Recherchez le paramètre d’application Web Service_pfwssdk_PfWsSdk pfMobile et modifiez la valeur de « http://localhost:4898/PfWsSdk.asmx » à l’URL du Kit de développement de Service Web qui s’exécute sur le serveur de l’authentification multifactorielle Azure (par exemple, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Étant donné que SSL est utilisé pour cette connexion, vous devez référencer le Kit de développement de Service Web par le nom du serveur et non pas l’adresse IP dans la mesure où le certificat SSL ne sera ont été émis pour le nom du serveur et de l’URL utilisée doit correspondre au nom sur le certificat. Si le nom du serveur ne résout pas à une adresse IP du serveur via Internet, ajoutez une entrée au fichier hosts sur ce serveur pour mapper le nom du serveur d’authentification selon plusieurs facteurs Azure à son adresse IP. Enregistrez le fichier web.config après que des modifications ont été apportées.</li>  

<li>Si le site Web que le Service application Web Mobile a été installé sous (par exemple, Site Web par défaut) n’a pas déjà été relié à l’aide d’un certificat public signé, installer le certificat sur le serveur si ce n’est pas déjà installé, ouvrez le Gestionnaire des services IIS et lier le certificat au site Web.</li>  

<li>Ouvrez un navigateur web à partir de n’importe quel ordinateur et accéder à l’URL où le Service application Web Mobile a été installé (par exemple, https://www.publicwebsite.com/PA). Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.</li>

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurer les paramètres d’application mobile sur le serveur de l’authentification multifactorielle Azure
Maintenant que le service web application mobile est installé, vous devez configurer le serveur d’authentification selon plusieurs facteurs Azure pour travailler avec le portail.

#### <a name="to-configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Pour configurer les paramètres d’application mobile sur le serveur de l’authentification multifactorielle Azure

1. Dans le serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de portail de l’utilisateur. Si les utilisateurs sont autorisés à contrôler les méthodes d’authentification, sous l’onglet Paramètres, sous Autoriser les utilisateurs à sélectionner la méthode, vérifier une application Mobile. Sans cette fonctionnalité est activée, les utilisateurs finaux devront contacter votre centre d’assistance pour effectuer l’activation pour l’application Mobile.
2. Vérifiez les utilisateurs autoriser pour activer la zone de l’application Mobile.
3. Cochez la case Autoriser l’inscription utilisateur.
4. Cliquez sur l’icône de l’application Mobile.
5. Entrez l’URL utilisée par le répertoire virtuel qui a été créé lors de l’installation de l’AuthenticationMobileAppWebServiceSetup de plusieurs facteurs Azure. Un nom de compte peut être entré dans l’espace fourni. Ce nom de société s’affiche dans l’application mobile. Si laissé vide, le nom de votre fournisseur d’authentification selon plusieurs facteurs créés dans le portail de gestion Azure s’affichera.



<center>![Programme d’installation](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
