<properties 
    pageTitle="Intégration de vos identités sur site avec Azure Active Directory."
    description="Il s’agit de la connexion AD Azure qui décrit ce qu’il s’agit et pourquoi l’utiliser."
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
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Création de plusieurs facteurs d’authentification dans les applications personnalisées (SDK)

> [AZURE.IMPORTANT]  Si vous souhaitez télécharger le Kit de développement logiciel, vous devrez créer un fournisseur d’authentification selon plusieurs facteurs Azure même si vous avez Azure AMF, DAS Premium ou licences de l’EMS.  Si vous créez un fournisseur d’authentification selon plusieurs facteurs Azure à cet effet et que vous avez déjà des licences, il est nécessaire pour créer le fournisseur avec le modèle **Par utilisateur** et lier le fournisseur dans le répertoire qui contient les licences Azure AMF, Azure AD Premium ou EMS.  Cela garantit que vous ne sont pas facturés à moins d’avoir plusieurs utilisateurs uniques en utilisant le Kit de développement logiciel que le nombre de licences que vous possédez.

Le Kit de développement logiciel (SDK) Azure plusieurs facteurs d’authentification vous permet de générer des appels téléphoniques et le texte vérification message directement dans les processus de connexion ou de la transaction des applications dans votre client AD Azure.

Le Kit de développement de l’authentification multifactorielle est disponible pour C#, Visual Basic (.NET), Java, Perl, PHP et Ruby. Le SDK fournit un wrapper mince autour d’authentification à plusieurs facteurs. Il comprend tout ce que vous devez écrire votre code, y compris les fichiers de code source commenté, fichiers d’exemple et un fichier Lisez-moi détaillé. Chaque kit de développement logiciel inclut également un certificat et une clé privée pour chiffrer les transactions qui est unique à votre fournisseur d’authentification selon plusieurs facteurs. Tant que vous disposez d’un fournisseur, vous pouvez télécharger le SDK dans autant de langues et de formats que vous le souhaitez.

La structure des API dans le Kit de développement de l’authentification multifactorielle est assez simple. Vous apportez une fonction à une API avec les paramètres d’option de plusieurs facteurs, tels que le mode de vérification et les données utilisateur, telles que le numéro de téléphone à appeler ou le code confidentiel pour valider. Les API de traduire l’appel de fonction dans les demandes de services web pour le Service nuage Azure plusieurs facteurs d’authentification. Tous les appels doivent inclure une référence au certificat privé qui est inclus dans chaque kit de développement logiciel.

Dans la mesure où les API n’ont pas d’accès pour les utilisateurs enregistrés dans Azure Active Directory, vous devez fournir les informations utilisateur, telles que les numéros de téléphone et codes PIN, dans un fichier ou une base de données. En outre, les API ne fournissent pas de fonctionnalités de gestion de l’inscription ou de l’utilisateur, et vous devez donc créer ces processus dans votre application.






## <a name="download-the-azure-multi-factor-authentication-sdk"></a>Télécharger l’authentification selon plusieurs facteur Azure SDK

Télécharger le Kit de développement de plusieurs facteurs Azure requiert un [Fournisseur d’authentification Azure multi-facteurs](multi-factor-authentication-get-started-auth-provider.md).  Cela nécessite un abonnement Azure complète, même si les licences Azure AMF, Azure AD Premium ou Suite de mobilité d’entreprise appartiennent.  Pour télécharger le Kit de développement logiciel, vous devez naviguer vers le portail de gestion de plusieurs facteurs en gérant le fournisseur d’authentification selon plusieurs facteurs directement, soit en cliquant sur le lien **« Aller vers le portail »** dans la page Paramètres du service AMF.


### <a name="to-download-the-azure-multi-factor-authentication-sdk-from-the-azure-portal"></a>Pour télécharger le Kit de développement Azure plusieurs facteurs d’authentification à partir du portail Azure


1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez Active Directory.
3. Dans la page Active Directory, en haut, cliquez sur **Fournisseurs d’authentification selon plusieurs facteurs**
4. Cliquez sur **Gérer** dans le bas.
5. Une nouvelle page s’ouvre.  Sur la gauche, en bas, cliquez sur Kit de développement logiciel.
<center>![Télécharger](./media/multi-factor-authentication-sdk/download.png)</center>
6. Sélectionnez la langue souhaitée, puis cliquez sur les liens de téléchargement associé.
7. Enregistrer le fichier téléchargé.



### <a name="to-download-the-azure-multi-factor-authentication-sdk-via-the-service-settings"></a>Pour télécharger le Kit de développement Azure plusieurs facteurs d’authentification via les paramètres de service


1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez Active Directory.
3. Double cliquez sur votre instance de publicité Azure.
4. Dans la partie supérieure, cliquez sur **configurer**
5. Sous authentification à facteurs multiples, sélectionnez **Gérer les paramètres de service**
![télécharger](./media/multi-factor-authentication-sdk/download2.png)
6. Dans la page Paramètres de services, en bas de l’écran, cliquez sur **Atteindre le portail**.
![Télécharger](./media/multi-factor-authentication-sdk/download3a.png)
7. Une nouvelle page s’ouvre.  Sur la gauche, en bas, cliquez sur Kit de développement logiciel.
8. Sélectionnez la langue souhaitée, puis cliquez sur les liens de téléchargement associé.
9. Enregistrer le fichier téléchargé.

## <a name="contents-of-the-azure-multi-factor-authentication-sdk"></a>Contenu de l’authentification multifactorielle Azure SDK
Dans le Kit de développement, vous trouverez les éléments suivants :

- **Fichier Lisezmoi**. Explique comment utiliser les API de l’authentification multifactorielle dans une application nouvelle ou existante.
- **Source fichier (s)** pour l’authentification multifactorielle
- **Certificat client** que vous utilisez pour communiquer avec le service d’authentification à plusieurs facteurs
- **Clé privée** pour le certificat
- **Appel.** Liste des codes de résultat d’appel. Pour ouvrir ce fichier, utilisez une application avec mise en forme, par exemple WordPad. Les codes de résultat appel permet de tester et de résoudre les problèmes liés à l’implémentation d’une authentification multifactorielle dans votre application. Ils ne sont pas des codes d’état de l’authentification.
- **Exemples.** Exemples de code pour une implémentation de travail de base de plusieurs facteurs d’authentification.


>[AZURE.WARNING]Le certificat client est un certificat privé unique qui a été généré, spécialement pour vous. Ne pas partager ou perdre ce fichier. C’est votre clé pour garantir la sécurité de vos communications avec le service d’authentification à plusieurs facteurs.

## <a name="code-sample-standard-mode-phone-verification"></a>L’exemple de code : Mode Standard vérification par téléphone

Cet exemple de code montre comment utiliser les API du SDK de l’authentification multicritères Azure pour ajouter la vérification d’appel en mode standard vocale à votre application. Le mode standard est un appel téléphonique répondant à l’utilisateur en appuyant sur la touche #.

Cet exemple utilise le C# 2.0 plusieurs facteurs d’authentification SDK .NET dans une application ASP.NET de base avec une logique côté serveur C#, mais le processus est très similaire pour des implémentations simples dans d’autres langues. Étant donné que le Kit de développement inclut des fichiers sources, fichiers non exécutables, vous pouvez générer les fichiers et y faire référence ou les inclure directement dans votre application.

>[AZURE.NOTE]Lors de l’implémentation d’une authentification multifacteur, utiliser les facteurs supplémentaires en tant que vérification secondaire ou tertiaire en complément de votre méthode principale d’authentification. Ces méthodes ne sont pas conçus pour être utilisé en tant que méthodes d’authentification principales.

### <a name="code-sample-overview"></a>Vue d’ensemble des exemples de code
Cet exemple de code pour une application de démonstration web très simple utilise un appel téléphonique avec une réponse clé # pour effectuer l’authentification de l’utilisateur. Ce facteur de l’appel téléphonique est appelé dans plusieurs facteurs d’authentification en mode standard.

Le code côté client n’inclut pas tous les éléments spécifiques d’authentification à plusieurs facteurs. Dans la mesure où les facteurs d’authentification supplémentaires sont indépendantes de l’authentification principale, vous pouvez les ajouter sans modification de l’interface d’ouverture de session existant. Les API du SDK de facteurs multiples vous permettent de personnaliser l’expérience utilisateur, mais que vous n’ayez pas à modifier quoi que ce soit tout.

Le code côté serveur, ajoute l’authentification du mode standard à l’étape 2. Il crée un objet PfAuthParams avec les paramètres qui sont requis pour la vérification du mode standard : nom d’utilisateur, numéro et le mode et le chemin d’accès au certificat client (CertFilePath), qui est requis dans chaque appel de téléphone. Pour une démonstration de tous les paramètres dans PfAuthParams, consultez le fichier d’exemple dans le Kit de développement.

Le code passe ensuite l’objet PfAuthParams à la fonction pf_authenticate(). La valeur de retour indique le succès ou l’échec de l’authentification. Les paramètres de sortie, callStatus et le code d’erreur, contiennent des informations de résultat d’appel supplémentaire. Les codes de résultat d’appel sont documentés dans le fichier de résultats des appels dans le Kit de développement logiciel.

Cette implémentation minimale peut être écrite dans une seulement quelques lignes. Toutefois, dans le code de production, vous pouvez inclure plus sophistiquées de gestion des erreurs, le code de base de données supplémentaires et une expérience utilisateur améliorée.

### <a name="web-client-code"></a>Code de Client Web

Voici le code de client web pour une page de démonstration.


    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Code côté serveur

Dans le code côté serveur suivant, plusieurs facteurs d’authentification est configuré et exécuté à l’étape 2. Mode standard (MODE_STANDARD) est un appel téléphonique à laquelle l’utilisateur répond en appuyant sur la touche #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "9134884271";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = " Multi-Factor Authentication failed.";
                }
            }

        }
    }
