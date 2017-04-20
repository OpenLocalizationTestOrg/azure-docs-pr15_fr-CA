<properties 
    pageTitle="Comment faire pour effectuer un appel téléphonique à partir de Twilio (.NET) | Microsoft Azure" 
    description="Apprenez à effectuer un appel téléphonique et d’envoyer un message SMS avec le service Twilio API sur Azure. Échantillons de code rédigés dans .NET." 
    services="" 
    documentationCenter=".net" 
    authors="devinrader" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="05/04/2016" 
    ms.author="microsofthelp@twilio.com"/>




# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Comment faire pour effectuer un appel téléphonique à l’aide de Twilio dans un rôle web sur Azure

Ce guide montre comment utiliser Twilio pour effectuer un appel à partir d’une page web hébergée dans Azure. L’application résultante invite l’utilisateur pour les valeurs de l’appel téléphonique, comme illustré dans la capture d’écran suivante.

![Formulaire appel Azure à l’aide de ASP.NET et Twilio][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Conditions préalables

Vous devez effectuer les opérations suivantes pour utiliser le code dans cette rubrique :

1. Acquérir une authentification et un Twilio compte jeton. Pour vous familiariser avec Twilio, inscrivez-vous à la [https://www.twilio.com/try-twilio][try_twilio]. Vous pouvez évaluer la tarification au [http://www.twilio.com/pricing][twilio_pricing]. Pour plus d’informations sur l’API fournie par Twilio, voir [http://www.twilio.com/voice/api][twilio_api].
2. Ajoutez la bibliothèque Twilio .NET à votre rôle web. Reportez-vous à la section « pour ajouter les bibliothèques Twilio à votre projet de rôle web » plus loin dans cette rubrique.

Vous devez être familiarisé avec la création d’un rôle de base de web sur Azure.

## <a name="howtocreateform"></a>Comment : créer un formulaire web pour effectuer un appel

<a id="use_nuget"></a>Pour ajouter les bibliothèques Twilio à votre projet de rôle web :

1.  Ouvrez votre solution dans Visual Studio.
2.  Cliquez sur **références**.
3.  Cliquez sur **Gérer les Packages NuGet**.
4.  Cliquez sur **en ligne**.
5.  Dans la boîte de recherche en ligne, tapez *twilio*.
6.  Cliquez sur **installer** dans le package Twilio.

Le code suivant montre comment créer un formulaire web pour récupérer des données d’utilisateur d’un appel. Dans cet exemple, un rôle de web ASP.NET nommé **TwilioCloud** est créé.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <a id="howtocreatecode"></a>Comment : créer le code pour effectuer l’appel
Le code suivant, qui est appelé lorsque l’utilisateur remplit le formulaire, crée le message d’appel et génère l’appel. Dans cet exemple, le code est exécuté dans le Gestionnaire d’événements onclick du bouton sur le formulaire. (Utilisez votre compte de Twilio et de l’authentification des jetons au lieu des valeurs d’espace réservé affectées à **accountSID** et **jeton d’authentification** dans le code ci-dessous).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

L’appel est effectué, et le point de terminaison Twilio, version de l’API et le statut de l’appel sont affichés. La capture d’écran suivante affiche la sortie d’une exécution de l’exemple.

![Réponse appel d’Azure à l’aide de ASP.NET et Twilio][twilio_dotnet_basic_form_output]

Vous trouverez plus d’informations sur le TwiML à [http://www.twilio.com/docs/api/twiml][twiml]. Plus d’informations sur &lt;dire&gt; vous trouverez les autres verbes de Twilio à [http://www.twilio.com/docs/api/twiml/say]et[twilio_say].

## <a id="nextsteps"></a>Étapes suivantes
Ce code a été fourni pour vous montrer les fonctionnalités de base à l’aide de Twilio dans un rôle de web ASP.NET sur Azure. Avant de déployer sur Azure dans la production, vous souhaiterez sans doute ajouter plus de gestion des erreurs ou d’autres fonctionnalités. Par exemple :

* Au lieu d’utiliser un formulaire web, vous pouvez utiliser le stockage Azure Blob ou une instance de base de données de SQL Azure pour stocker les numéros de téléphone et appelez le texte. Pour plus d’informations sur l’utilisation des objets BLOB dans Azure, voir [comment utiliser le service de stockage Azure Blob dans .NET][howto_blob_storage_dotnet]. Pour plus d’informations sur l’utilisation de la base de données SQL, voir [comment utiliser la base de données de SQL Azure dans les applications .NET][howto_sql_azure_dotnet].
* Vous pouvez utiliser RoleEnvironment.getConfigurationSettings pour récupérer l’ID du compte Twilio et le jeton d’authentification dans les paramètres de configuration de votre déploiement, au lieu de coder en dur les valeurs dans votre formulaire. Pour plus d’informations sur la classe RoleEnvironment, consultez [Namespace de Microsoft.WindowsAzure.ServiceRuntime][azure_runtime_ref_dotnet].
* Lisez les consignes de sécurité Twilio à [https://www.twilio.com/docs/security][twilio_docs_security].
* En savoir plus sur les Twilio à [https://www.twilio.com/docs][twilio_docs].

##<a name="seealso"></a>Voir aussi
* [L’utilisation de Twilio pour la voix et les fonctionnalités SMS à partir d’Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
