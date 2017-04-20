<properties 
    pageTitle="L’interrogation de longues opérations | Microsoft Azure" 
    description="Cette rubrique montre comment interroger les opérations longues." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="delivering-live-streaming-with-azure-media-services"></a>Distribution en continu avec Azure Media Services

##<a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services propose des API qui envoient des requêtes à des Services multimédias pour démarrer des opérations (par exemple : créer, Démarrer, arrêter ou supprimer une voie). Ces opérations sont longues.

Media Services .NET SDK fournit des API qui envoie la demande et attendez que l’opération se termine (en interne, les API sont l’interrogation de la progression de l’opération à des intervalles). Par exemple, lorsque vous appelez le canal. Start(), la méthode retourne après le démarrage de la chaîne. Vous pouvez également utiliser la version asynchrone : attente de canal. StartAsync() (pour plus d’informations sur le modèle asynchrone basé sur des tâches, reportez-vous à la section [cliquez sur](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). API qui envoie une demande d’opération et interroge ensuite le statut jusqu'à ce que l’opération est terminée est appelés des « méthodes d’interrogation ». Ces méthodes (en particulier la version asynchrone) sont recommandées pour les applications clientes riches et/ou de services avec état.

Il existe des scénarios dans lesquels une application ne peut pas attendre d’une demande http longue et souhaite interroger la progression de l’opération manuellement. Voici un exemple type serait un navigateur d’interagir avec un service web sans état : lorsque le navigateur demande pour créer un canal, le service web lance une opération de longue durée et renvoie l’ID d’opération pour le navigateur. Le navigateur peut demander au service web pour obtenir l’état de l’opération en fonction de l’identificateur. Media Services .NET SDK fournit des API qui est utiles pour ce scénario. Ces API est appelées « méthodes d’interrogation non ».
Les « méthodes d’interrogation non » sont le modèle d’affectation de noms suivant : envoyer l’opération*NomOpération*(par exemple, SendCreateOperation). Envoyer des méthodes d’opération*NomOpération*retournent l’objet **IOperation** ; l’objet retourné contient des informations qui peuvent être utilisées pour effectuer le suivi de l’opération. Les méthodes d’envoi*NomOpération*OperationAsync retournent **tâche<IOperation>**.

Actuellement, les classes suivantes prennent en charge les méthodes d’interrogation non : **canal**, **StreamingEndpoint**et **programme**.

Pour interroger l’état de l’opération, utilisez la méthode **GetOperation** sur la classe **OperationBaseCollection** . Utilisez les intervalles suivants pour vérifier l’état de l’opération : pour les opérations de **canal** et de **StreamingEndpoint** , utilisez 30 secondes ; pour les opérations du **programme** , utiliser 10 secondes.


##<a name="example"></a>Exemple

L’exemple suivant définit une classe appelée **ChannelOperations**. Cette définition de classe peut être un point de départ pour votre définition de classe de service web. Pour plus de simplicité, les exemples suivants utilisent les versions non-async de méthodes.

L’exemple montre également comment le client peut utiliser cette classe.

###<a name="channeloperations-class-definition"></a>Définition de la classe ChannelOperations

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelPreview CreateChannelPreview()
        {
            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###<a name="the-client-code"></a>Le code de client

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
