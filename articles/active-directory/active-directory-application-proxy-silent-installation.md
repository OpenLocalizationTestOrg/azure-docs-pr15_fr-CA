<properties
    pageTitle="Comment faire pour installer silencieusement le connecteur Active Directory Application Proxy Azure | Microsoft Azure"
    description="Explique comment effectuer une installation silencieuse de Azure connecteur de Proxy d’Application Active Directory pour fournir un accès distant sécurisé à vos applications sur site."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Comment faire pour installer silencieusement le connecteur Active Directory Application Proxy Azure

Vous souhaitez être en mesure d’envoyer un script d’installation sur plusieurs serveurs Windows ou aux serveurs Windows qui n’ont pas l’interface utilisateur. Cette rubrique explique comment créer un script Windows PowerShell qui permet une installation sans assistance installer et enregistrer votre connecteur de Proxy d’Application AD Azure.

## <a name="enabling-access"></a>Activation de l’accès
Proxy de l’application fonctionne en installant un service Windows Server Compact appelé le connecteur à l’intérieur de votre réseau. Pour le Proxy d’Application Connector il fonctionne doit être inscrit dans votre répertoire d’Azure AD à l’aide d’un administrateur global et un mot de passe. Normalement, il est entré au cours de l’installation du connecteur dans une boîte de dialogue contextuelle. Vous pouvez utiliser Windows PowerShell pour créer un objet d’informations d’identification pour entrer vos informations d’enregistrement, ou vous pouvez créer votre propre jeton et l’utiliser pour entrer vos informations d’enregistrement.

## <a name="step-1--install-the-connector-without-registration"></a>Étape 1 : Installer le connecteur sans inscription


Installez le MSI connecteur sans enregistrer le connecteur comme suit :


1. Ouvrez une invite de commande.
2. Exécutez la commande suivante dans laquelle la /q signifie installation en mode silencieux - l’installation vous invite pas à accepter le contrat de licence utilisateur final.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Étape 2 : Enregistrer le connecteur avec Azure Active Directory
Ceci peut être réalisé à l’aide d’une des méthodes suivantes :


- Inscrire le connecteur à l’aide d’un objet d’informations d’identification de Windows PowerShell
- Inscrire le connecteur à l’aide d’un jeton créé hors connexion

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Inscrire le connecteur à l’aide d’un objet d’informations d’identification de Windows PowerShell


1. Créer l’objet d’informations d’identification de Windows PowerShell en exécutant la commande suivante, où "<username>« et »<password>" doit être remplacée par le nom d’utilisateur et le mot de passe de votre répertoire :

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Accédez à **C:\Program Files\Microsoft DAS application Proxy Connector** et exécuter le script à l’aide de l’objet d’informations d’identification de PowerShell créé, où $cred est le nom de PowerShell de références d’objet que vous avez créé :

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### <a name="register-the-connector-using-a-token-created-offline"></a>Inscrire le connecteur à l’aide d’un jeton créé hors connexion

1. Créer un jeton hors connexion à l’aide de la classe AuthenticationContext à l’aide de valeurs dans l’extrait de code :


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Une fois que le jeton de créer une chaîne SecureString en utilisant le jeton : <br>
`$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Exécutez la commande suivante de Windows PowerShell, où SecureToken est le nom du jeton créé ci-dessus et tenantID les GUID de vos clients : <br>
`RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## <a name="see-also"></a>Voir aussi

- [Activer le Proxy d’Application pour Azure Active Directory](active-directory-application-proxy-enable.md)
- [Publier des applications à l’aide de votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Résoudre les problèmes que vous rencontrez avec le Proxy de l’Application](active-directory-application-proxy-troubleshoot.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
