<properties
    pageTitle="Substitution de la clé de signature dans Azure annonce | Microsoft Azure"
    description="Cet article décrit les pratiques recommandées de substitution de la clé signature pour Azure Active Directory"
    services="active-directory"
    documentationCenter=".net"
    authors="gsacavdm"
    manager="krassk"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="gsacavdm"/>

# <a name="signing-key-rollover-in-azure-active-directory"></a>Substitution dans Azure Active Directory de la clé de signature

Cette rubrique explique ce que vous devez savoir sur les clés publiques utilisées dans Azure Active Directory (AD Azure) pour signer les jetons de sécurité. Il est important de noter que ces survol de clés sur une base périodique et, en cas d’urgence, peut être déployée immédiatement. Toutes les applications qui utilisent Active Directory Azure doivent être en mesure de gérer le processus de substitution de la clé par programme ou d’établir un processus périodique de survol manuelle. Continuer la lecture pour comprendre comment les touches fonctionnent, comment évaluer l’impact de la restauration de votre application et comment mettre à jour votre application ou établir un processus périodique de survol manuelle pour gérer la substitution de la clé si nécessaire.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Vue d’ensemble de clés de signature dans Azure AD

Annonce Azure utilise la cryptographie à clé publique basée sur les normes de l’industrie pour établir l’approbation entre lui-même et les applications qui l’utilisent. En termes pratiques, cela fonctionne de la manière suivante : AD Azure utilise une clé de signature qui se compose d’une paire de clés publique et privée. Lorsqu’un utilisateur se connecte à une application qui utilise Azure AD pour l’authentification, Azure AD crée un jeton de sécurité qui contient des informations sur l’utilisateur. Ce jeton est signé par Azure AD à l’aide de sa clé privée avant d’être envoyé à l’application. Pour vérifier que le jeton est valide et effectivement origines d’Azure AD, l’application doit valider signature du jeton à l’aide de la clé publique exposée par AD Azure qui est contenue dans le [document de découverte OpenID se connecter](http://openid.net/specs/openid-connect-discovery-1_0.html) ou de SAML/WS-Fed [document des métadonnées de fédération](active-directory-federation-metadata.md)du locataire.

Pour des raisons de sécurité, l’annonce Azure rouleaux de clé sur une base périodique et, en cas d’urgence, la signature peut être annulés immédiatement. N’importe quelle application qui s’intègre à Active Directory Azure doit être préparée pour gérer un événement de substitution de la clé, quelle que soit la fréquence à laquelle il peut se produire. Si elle n’et que votre application tente d’utiliser une clé a expiré pour vérifier la signature d’un jeton, la demande d’ouverture de session échoue.

Plus d’une clé valide est toujours disponible dans le document de découverte OpenID se connecter et le document de métadonnées de fédération. Votre application doit être prêt à utiliser une des clés spécifiées dans le document, dans la mesure où une clé peut être annulée plus rapidement, un autre peut être son remplacement et ainsi de suite.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Comment évaluer si votre application est affectée et ce qu’il faut faire

Comment votre application gère la substitution de la clé dépend de variables telles que le type d’application ou le protocole de l’identité et de la bibliothèque a été utilisé. Les sections ci-dessous d’évaluer si les types les plus courants d’applications sont affectées par la substitution de la clé et fournissent des conseils sur la façon de mettre à jour l’application prenne en charge la substitution automatique ou de mettre à jour manuellement la clé.

* [Applications client natif l’accès aux ressources](#nativeclient)
* [Applications Web / API de l’accès aux ressources](#webclient)
* [Les applications Web et API de protection des ressources créées à l’aide des Services d’application Azure](#appservices)
* [Applications Web / API de protection des ressources à l’aide de .NET OWIN OpenID de se connecter, WS-Fed ou des solutions middleware de WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Applications Web / API de protection des ressources à l’aide de middleware .NET Core OpenID connecter ou JwtBearerAuthentication](#owincore)
* [Applications Web / API de protection des ressources à l’aide du module de passport-azure-annonce Node.js](#passport)
* [Les applications Web et API de protection des ressources créées avec Visual Studio 2015](#vs2015)
* [Protection des ressources et créé avec Visual Studio 2013 des applications Web](#vs2013)
* [API Web de protection des ressources et créé avec Visual Studio 2013](#vs2013_webapi)
* [Protection des ressources et créé avec Visual Studio 2012 des applications Web](#vs2012)
* [Applications Web créé avec Visual Studio 2010, o 2008 à l’aide de Windows Identity Foundation et de protection des ressources](#vs2010)
* [Applications Web / API de protection des ressources à l’aide de toutes les autres bibliothèques ou implémenter manuellement un des protocoles pris en charge](#other)

Ce guide est **pas** applicable aux :

* Les applications ajoutées à partir de la galerie des applications Azure AD (y compris personnalisé) ont des directives spécifiques en ce qui concerne les clés de signature. [Plus d’informations.](active-directory-sso-certs.md)
* Les applications publiées via un proxy d’application n’ont pas à se soucier de clés de signature sur site.

### <a name="nativeclient"></a>Applications client natif l’accès aux ressources

Applications qui accèdent uniquement à des ressources (ex : Microsoft Graph, KeyVault, API d’Outlook et d’autres APIs de Microsoft) généralement uniquement obtenir un jeton et la passer au propriétaire de la ressource. Étant donné qu’ils ne protègent pas les ressources, ils n’inspectent pas le jeton et n’est donc pas nécessaire pour s’assurer qu’il est correctement signé.

Les applications clientes natif, si l’ordinateur de bureau ou mobile, entrent dans cette catégorie et ne sont donc pas concernées par la restauration.

### <a name="webclient"></a>Applications Web / API de l’accès aux ressources

Applications qui accèdent uniquement à des ressources (ex : Microsoft Graph, KeyVault, API d’Outlook et d’autres APIs de Microsoft) généralement uniquement obtenir un jeton et la passer au propriétaire de la ressource. Étant donné qu’ils ne protègent pas les ressources, ils n’inspectent pas le jeton et n’est donc pas nécessaire pour s’assurer qu’il est correctement signé.

Applications et web API qui utilisée le flux de l’application uniquement (informations d’identification client / certificat client), entrent dans cette catégorie et ne sont donc pas concernés par la restauration.

### <a name="appservices"></a>Les applications Web et API de protection des ressources créées à l’aide des Services d’application Azure

L’authentification des Services d’application Azure fonctionnalité d’autorisation (EasyAuth) a déjà la logique nécessaire pour gérer automatiquement de substitution de la clé.

### <a name="owin"></a>Applications Web / API de protection des ressources à l’aide de .NET OWIN OpenID de se connecter, WS-Fed ou des solutions middleware de WindowsAzureActiveDirectoryBearerAuthentication

Si votre application utilise le .NET OWIN OpenID de se connecter, WS-Fed ou des solutions middleware de WindowsAzureActiveDirectoryBearerAuthentication, il a déjà la logique nécessaire pour gérer automatiquement de substitution de la clé.

Vous pouvez vérifier que votre application utilise une de ces en recherchant des extraits de code suivants dans votre application Startup.cs ou Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
    });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Applications Web / API de protection des ressources à l’aide de middleware .NET Core OpenID connecter ou JwtBearerAuthentication

Si votre application utilise le middleware .NET Core OWIN OpenID de se connecter ou JwtBearerAuthentication, il a déjà la logique nécessaire pour gérer automatiquement de substitution de la clé.

Vous pouvez vérifier que votre application utilise une de ces en recherchant des extraits de code suivants dans votre application Startup.cs ou Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
    });
```

### <a name="passport"></a>Applications Web / API de protection des ressources à l’aide du module de passport-azure-annonce Node.js

Si votre application utilise le module de passport-ad Node.js, il possède déjà la logique nécessaire pour gérer automatiquement de substitution de la clé.

Vous pouvez vous assurer que votre annonce de passport de l’application en recherchant l’extrait de code suivant dans votre application app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Les applications Web et API de protection des ressources créées avec Visual Studio 2015

Si votre application a été générée à l’aide d’un modèle d’application web dans Visual Studio 2015 et que vous avez sélectionné des **Comptes de l’établissement et de travail** à partir du menu **Modifier l’authentification** , il possède déjà la logique nécessaire pour gérer automatiquement de substitution de la clé. Cette logique, incorporée dans le middleware se connecter de OpenID OWIN, extrait et met en cache les clés à partir du document de découverte OpenID connecter et actualise régulièrement les.

Si vous avez ajouté manuellement l’authentification à votre solution, votre application ne peut pas avoir la logique de substitution de la clé nécessaire. Vous devez écrire vous-même, ou suivre les étapes de [Web applications / API à l’aide de toutes les autres bibliothèques ou implémenter manuellement un des protocoles pris en charge.](#other).

### <a name="vs2013"></a>Protection des ressources et créé avec Visual Studio 2013 des applications Web

Si votre application a été générée à l’aide d’un modèle d’application web dans Visual Studio 2013 et que vous avez sélectionné des **Comptes d’organisation** à partir du menu **Modifier l’authentification** , il possède déjà la logique nécessaire pour gérer automatiquement de substitution de la clé. Cette logique stocke l’identificateur unique de votre organisation et les informations de clés de signature dans deux tables de base de données associés au projet. Vous trouverez la chaîne de connexion pour la base de données dans le fichier Web.config du fichier.

Si vous avez ajouté manuellement l’authentification à votre solution, votre application ne peut pas avoir la logique de substitution de la clé nécessaire. Vous devez écrire vous-même, ou suivre les étapes de [Web applications / API à l’aide de toutes les autres bibliothèques ou implémenter manuellement un des protocoles pris en charge.](#other).

Les étapes suivantes vous permettra de vérifier que la logique fonctionne correctement dans votre application.

1. Ouvrez la solution dans Visual Studio 2013 et puis cliquez sur l’onglet de **l’Explorateur de serveurs** dans la fenêtre de droite.
2. Développez les **connexions de données**, **DefaultConnection**et **Tables**. Localisez la table **IssuingAuthorityKeys** , dessus, puis cliquez sur **Afficher les données de Table**.
3. Dans la table **IssuingAuthorityKeys** , il y aura au moins une ligne, ce qui correspond à la valeur de l’empreinte de la clé. Supprimer toutes les lignes de la table.
4. Avec le bouton droit de la table de **locataires** , puis cliquez sur **Afficher les données de Table**.
5. Dans le tableau des **locataires** , il y aura au moins une ligne, ce qui correspond à un identificateur de clients d’annuaire unique. Supprimer toutes les lignes de la table. Si vous ne supprimez pas les lignes dans les tables de **locataires** et **IssuingAuthorityKeys** , vous obtiendrez une erreur lors de l’exécution.
6. Générez et exécutez l’application. Une fois que vous êtes connecté à votre compte, vous pouvez arrêter l’application.
7. Revenir à l' **Explorateur de serveurs** et examinez les valeurs dans la table **IssuingAuthorityKeys** et des **locataires** . Vous remarquerez qu’ils ont été automatiquement remplie à nouveau les informations appropriées à partir du document de métadonnées de fédération.

### <a name="vs2013"></a>API Web de protection des ressources et créé avec Visual Studio 2013

Si vous créé un site web application API dans Visual Studio 2013, l’utilisation du modèle API Web et ensuite sélectionné **D’organisation des comptes** dans le menu **Modifier l’authentification** , vous avez déjà la logique nécessaire dans votre application.

Si vous avez configuré manuellement l’authentification, suivez les instructions ci-dessous pour savoir comment configurer votre API Web pour mettre automatiquement à jour ses informations de clé.

L’extrait de code suivant montre comment obtenir les dernières clés à partir du document de métadonnées de fédération et le [Gestionnaire de jetons de JWT](https://msdn.microsoft.com/library/dn205065.aspx) permet de valider le jeton. L’extrait de code suppose que vous utiliserez votre propre mécanisme de mise en cache de la persistance de la clé pour valider les jetons futurs à partir d’AD Azure, que ce soit dans une base de données, le fichier de configuration ou ailleurs.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Protection des ressources et créé avec Visual Studio 2012 des applications Web

Si votre application a été créée dans Visual Studio 2012, vous avez utilisé probablement l’identité et l’outil d’accès à la configuration de votre application. Il est également probable que vous utilisez la [Validation de l’émetteur nom du Registre (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). La VINR est responsable de la mise à jour d’informations sur le fournisseur d’identité approuvé (Azure AD) et les clés utilisées pour valider des jetons émis par eux. La VINR facilite également mettre à jour automatiquement les informations de clé stockées dans un fichier Web.config en téléchargeant le dernier document de métadonnées de fédération associé à votre répertoire, vérifier si la configuration est à jour avec le dernier document et mise à jour de l’application pour utiliser la nouvelle clé si nécessaire.

Si vous avez créé votre application à l’aide des exemples de code ou de la documentation de procédure pas à pas fourni par Microsoft, la logique de substitution de la clé est déjà incluse dans votre projet. Vous remarquerez que le code suivant existe déjà dans votre projet. Si votre application ne possède pas déjà cette logique, suivez les étapes ci-dessous pour l’ajouter et vérifier qu’il fonctionne correctement.

1. Dans l' **Explorateur de solutions**, ajoutez une référence à l’assembly de **System.IdentityModel** pour le projet approprié.
2. Ouvrez le fichier **Global.asax.cs** et ajoutez le code suivant à l’aide de directives :
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Ajoutez la méthode suivante dans le fichier **Global.asax.cs** :
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Appelez la méthode **RefreshValidationSettings()** dans la méthode **Application_Start()** dans **Global.asax.cs** comme indiqué :
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Une fois que vous avez suivi ces étapes, le fichier Web.config de votre application sera mis à jour avec les dernières informations sur le document de métadonnées de fédération, y compris les dernières clés. Cette mise à jour se produisent chaque fois que votre pool d’applications est recyclé dans IIS ; par défaut, IIS est configuré pour recycler les applications toutes les heures 29.

Suivez les étapes ci-dessous pour vérifier que la logique de substitution de la clé fonctionne.

1. Après avoir vérifié que votre application utilise le code ci-dessus, ouvrez le fichier **Web.config** et accédez à le **<issuerNameRegistry>** bloc, spécifiquement pour l’élément suivant quelques lignes :
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. Dans le **<add thumbprint=””>** définition, de modifier la valeur de l’empreinte numérique en remplaçant un caractère par un autre. Enregistrez le fichier **Web.config** .

3. Générer l’application et ensuite l’exécuter. Si vous pouvez effectuer le processus de connexion, votre application est mise à jour de la clé en téléchargeant les informations requises à partir du document de métadonnées de fédération de votre annuaire. Si vous rencontrez des problèmes de connexion, vérifiez les modifications dans votre application sont correctes en lisant la rubrique [Ajout d’ouverture de session sur votre utilisation de l’Application Web AD Azure](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) , ou en téléchargeant et en examinant l’exemple de code suivant : [Application en nuage mutualisée pour Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).


### <a name="vs2010"></a>Protection des ressources et créé avec Visual Studio 2008 ou 2010 des applications Web et v1.0 Windows Identity Foundation (WIF) pour .NET 3.5

Si vous avez créé une application sur WIF v1.0, il n’existe aucun mécanisme fourni pour actualiser automatiquement la configuration de votre application pour utiliser une nouvelle clé.

- *Plus simple* Utilisez les outils de FedUtil inclus dans le SDK WIF, qui peut extraire le dernier document de métadonnées et de mettre à jour votre configuration.
- Mettre à jour votre application vers le .NET 4.5, qui inclut la version la plus récente de WIF situé dans l’espace de noms System. Vous pouvez ensuite utiliser la [Validation de l’émetteur nom du Registre (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) pour effectuer des mises à jour automatiques de la configuration de l’application.
- Effectuer une restauration manuelle selon les instructions fournies à la fin de ce document d’orientation.

Instructions d’utilisation de la FedUtil pour mettre à jour votre configuration :

1. Vérifiez que vous disposez de la v1.0 WIF SDK installé sur votre ordinateur de développement pour Visual Studio 2008 ou 2010. Vous pouvez [le télécharger à partir d’ici](https://www.microsoft.com/en-us/download/details.aspx?id=4451) , si vous le n'avez pas encore installé.
2. Dans Visual Studio, ouvrez la solution, puis droit sur le projet applicable et sélectionnez **mettre à jour des métadonnées de fédération**. Si cette option n’est pas disponible, FedUtil ou le Kit de développement logiciel de v1.0 WIF n'a pas été installé.
3. À l’invite, sélectionnez **mise à jour** pour mettre à jour les métadonnées de votre fédération. Si vous avez accès à l’environnement de serveur où l’application est hébergée, vous pouvez éventuellement utiliser l' de FedUtil [métadonnées automatique mise à jour du planificateur](https://msdn.microsoft.com/library/ee517272.aspx).
4. Cliquez sur **Terminer** pour terminer le processus de mise à jour.

### <a name="other"></a>Applications Web / API de protection des ressources à l’aide de toutes les autres bibliothèques ou implémenter manuellement un des protocoles pris en charge

Si vous utilisez une autre bibliothèque ou implémentées manuellement un des protocoles pris en charge, vous devez passer en revue la bibliothèque ou votre mise en œuvre pour vous assurer que la clé est extraite à partir du document de métadonnées de fédération ou le document de découverte OpenID se connecter. Une pour vérifier cela consiste à effectuer une recherche dans votre code ou le code de la bibliothèque pour tous les appels par le document de découverte OpenID ou le document de métadonnées de fédération.

Si la clé est stockée quelque part ou codées en dur dans votre application, vous pouvez manuellement récupérer la clé et la mise à jour il en conséquence effectuer une restauration manuelle selon les instructions fournies à la fin de ce document d’orientation. **Il est vivement recommandé que vous améliorez votre application pour prendre en charge la substitution automatique** à l’aide d’une des approches mettent en avant dans cet article afin d’éviter de futures perturbations et surcharge si AD Azure augmente est la cadence de survol ou a une substitution out-of-band d’urgence.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Comment faire pour tester votre application pour déterminer si elle est affectée.

Vous pouvez contrôler si votre application prend en charge la substitution automatique de la clé en téléchargeant les scripts et en suivant les instructions de [ce référentiel GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Comment faire pour effectuer une restauration manuelle si votre application ne gère pas la substitution automatique

Si votre application fait **pas** prise en charge de la substitution automatique, vous devez établir un processus qui surveille les clés de signature de l’annonce Azure périodiquement et qui effectue une restauration manuelle en conséquence. [GitHub ce référentiel](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contient des scripts et des instructions sur la procédure à suivre.
