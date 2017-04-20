<properties 
    pageTitle="Comment faire pour configurer l’authentification TLS mutuelle pour l’application Web" 
    description="Découvrez comment configurer votre application web pour utiliser l’authentification par certificat client TLS." 
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/08/2016" 
    ms.author="naziml"/>    

# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Comment faire pour configurer l’authentification TLS mutuelle pour l’application Web

## <a name="overview"></a>Vue d’ensemble ##
Vous pouvez limiter l’accès à votre application web Azure grâce à différents types d’authentification pour qu’elle. Une façon de faire est de s’authentifier à l’aide d’un certificat client lorsque la demande est sur TLS/SSL. Ce mécanisme est appelé authentification mutuelle de TLS ou certificat de client d’authentification et de cet article vous expliquer en détail comment configurer votre application web pour utiliser l’authentification par certificat client.

> **Remarque :** Si vous accédez à votre site via HTTP et HTTPS pas, vous ne recevrez pas les certificats clients. Par conséquent, si votre application exige des certificats client vous ne devez pas autoriser les demandes à votre application via HTTP.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="configure-web-app-for-client-certificate-authentication"></a>Configurer l’application Web pour l’authentification de certificat Client ##
Pour configurer votre application web pour exiger des certificats clients, vous devez ajouter le paramètre de site clientCertEnabled pour votre application web et affectez-lui la valeur true. Ce paramètre n’est pas actuellement disponible, grâce à la gestion dans le portail et l’API REST devra être utilisé pour cela.

Vous pouvez utiliser l' [outil de ARMClient](https://github.com/projectkudu/ARMClient) pour le rendre facile à concevoir l’appel de l’API REST. Une fois que vous ouvrez une session avec l’outil vous devez émettre la commande suivante :

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose
    
remplacement de tous les éléments de {} avec les informations pour votre application web et la création d’un fichier appelé enableclientcert.json avec le JSON suivant contenu :

> {« emplacement » : « Mon application emplacement Web »,   
>   « propriétés » : {  
>     « clientCertEnabled » : true}}  

Veillez à modifier la valeur de « location » à chaque fois que votre application web se trouve, par exemple du Nord centrale nous ou Ouest américaine etc..

> **Remarque :** Si vous exécutez ARMClient à partir de Powershell, vous devrez quitter le @ symbole pour le fichier JSON avec un caractère '.

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Accès le certificat Client à partir de votre application Web ##
Si vous utilisez ASP.NET et configurez votre application pour utiliser l’authentification par certificat, le certificat sera disponible par le biais de la propriété **HttpRequest.ClientCertificate** . Pour les autres piles d’applications, le certificat client sera disponible dans votre application par une valeur codée en base64 dans l’en-tête de la demande « ARR-X-ClientCert ». Votre application peut créer un certificat à partir de cette valeur et ensuite l’utiliser à des fins d’authentification et d’autorisation de votre application.

## <a name="special-considerations-for-certificate-validation"></a>Considérations particulières pour la Validation de certificat ##
Le certificat client qui est envoyé à l’application ne fait intervenir aucune validation par la plateforme Azure Web Apps. Validation de ce certificat est la responsabilité de l’application web. Voici des exemples de code ASP.NET qui valide les propriétés du certificat pour l’authentification.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;
                
                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;
                
                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
