<properties 
    pageTitle="Engagement de Mobile Azure - intégration de back-end" 
    description="Engagement de Mobile Azure de se connecter avec un back-end de SharePoint pour créer des campagnes à partir de SharePoint" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement---api-integration"></a>Engagement de Mobile Azure - intégration d’API

Dans un système automatisé de marketing, création et l’activation des campagnes marketing également se produisent automatiquement. À cette fin - Azure Mobile Engagement permet de créer ces campagnes marketing automatisées à l’aide d’API ainsi. 

En principe les clients utilisent l’interface de front-end Mobile Engagement à créer des annonces/sondages etc. dans le cadre de leurs campagnes marketing. Toutefois les campagnes marketing devenant matures, il est nécessaire d’exploiter les données verrouillées dans les systèmes back-end (comme n’importe quel système CRM ou un système CMS tels que SharePoint) de sorte que vous pouvez créer un pipeline entièrement automatisé qui crée des campagnes dans l’Engagement Mobile dynamique basé sur les données contenues dans des systèmes back-end. 

![][5]

Ce didacticiel passe par un scénario où un utilisateur professionnel de SharePoint remplit une liste SharePoint avec les données de marketing et un processus automatique prélève des éléments de la liste et qu’il se connecte avec le système de l’Engagement de Mobile à l’aide de l’API reste disponible pour créer une campagne marketing à partir des données SharePoint. 
 
> [AZURE.IMPORTANT] En général, vous pouvez utiliser cet exemple comme point de départ pour comprendre comment appeler les API REST de mission Mobile comme il décrit en détail les deux principaux aspects de l’appel de l’API - authentification et en passant les paramètres. 

## <a name="sharepoint-integration"></a>Intégration à SharePoint
1. L’exemple de liste SharePoint ressemble à ceci. **Titre**, **catégorie**, **NotificationTitle**, **Message** et **URL** sont utilisés pour la création de l’annonce. Il existe une colonne appelée **IsProcessed** qui est utilisée par le processus d’automatisation exemple sous la forme d’un programme de console. Vous pouvez exécuter cette console du programme sous la forme d’un WebJob d’Azure afin que vous pouvez la planifier, ou vous pouvez utiliser directement le flux de travail SharePoint à un programme de création et l’activation de l’annonce lorsqu’un élément est inséré dans la liste SharePoint. Dans cet exemple, nous utilisons le programme de console qui parcourt les éléments de la liste SharePoint et créer l’annonce dans Azure Mobile Engagement pour chacun d’eux et puis enfin le marque à l’indicateur **IsProcessed** pour avoir la valeur true lors de la création de l’annonce réussie.

    ![][1]

2. Nous utilisons le code de l’exemple *d’authentification à distance dans SharePoint en ligne en utilisant le modèle d’objet Client* [ici](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) pour authentifier avec la liste SharePoint.
 
3. Une fois authentifié, nous en boucle les éléments de la liste pour trouver les éléments nouvellement créée (qui aura **IsProcessed** = false). 

        static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Intégration de l’Engagement de Mobile
1.  Une fois que nous avons trouvé un article qui nécessite un traitement - nous extraire les informations requises pour créer une annonce à partir de l’élément de la liste et appelez `CreateAzMECampaign` pour le créer et par la suite `ActivateAzMECampaign` pour l’activer. Ce sont essentiellement les appels API REST appelant pour le back-end de l’Engagement de Mobile. 

2.  Les API de reste d’Engagement Mobile requièrent un **en-tête HTTP de base auth régime d’autorisation** qui se compose de la `ApplicationId` et le `ApiKey` que vous ouvrez à partir du portail Azure. Assurez-vous que vous utilisez la clé à partir de la section **clés de l’api** et *non* à partir de la section **clés du Kit de développement logiciel** . 

    ![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. Pour la création de l’annonce tapez campagne - reportez-vous à la [documentation](https://msdn.microsoft.com/library/azure/mt683750.aspx). Vous devez vous assurer que vous spécifiez la campagne `kind` en tant que *l’annonce* et la [charge utile](https://msdn.microsoft.com/library/azure/mt683751.aspx) et en lui passant comme FormUrlEncodedContent. 

        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Une fois que vous avez l’annonce créé, vous verrez quelque chose comme ce qui suit sur le portail Mobile Engagement (Notez que l’état = Brouillon et activé = n/a)

    ![][3]

5. `CreateAzMECampaign`Crée une campagne d’annonce et retourne son Id à l’appelant. `ActivateAzMECampaign`requiert cet Id comme paramètre pour activer la campagne. 

        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Une fois que vous avez l’annonce activé, vous verrez quelque chose comme ce qui suit sur le portail de l’Engagement de Mobile :

    ![][4]

7. Dès que la campagne est activée, tous les périphériques qui satisfont le critère sur la campagne seront commencer à voir les notifications. 

8. Vous remarquerez également que l’élément de liste marquée avec IsProcessed = false a la valeur True une fois la création de la campagne de l’annonce.  

Cet exemple créé une campagne simple annonce spécifiant principalement les propriétés requises. Vous pouvez le personnaliser comme autant que possible à partir du portail en utilisant les informations [ici](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 
