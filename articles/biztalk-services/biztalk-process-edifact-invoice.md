<properties
   pageTitle="Didacticiel : Traiter les factures EDIFACT en utilisant des Services BizTalk Azure | Services BizTalk Microsoft Azure"
   description="Comment créer et configurer l’application Connecteur de zone ou les API et l’utiliser dans une logique d’application dans le Service d’application Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="deonhe"/>

# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Didacticiel : Processus EDIFACT factures à l’aide des Services de BizTalk Azure
Vous pouvez utiliser le portail de Services de BizTalk pour configurer et déployer des X12 et des accords EDIFACT. Dans ce didacticiel, nous examinons comment créer un accord EDIFACT pour échanger des factures entre partenaires commerciaux. Ce didacticiel est écrit autour d’une solution d’entreprise de bout en bout impliquant deux partenaires commerciaux, Northwind et Contoso qui échangent des messages EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>Exemple en fonction de ce didacticiel
Ce didacticiel est écrit autour d’un échantillon, **Envoi EDIFACT factures à l’aide de BizTalk Services**, qui est disponible en téléchargement à partir de la [Galerie de Code MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Vous pouvez utiliser l’exemple et parcourir le didacticiel pour comprendre le mode de génération de l’exemple. Ou bien, vous pouvez utiliser ce didacticiel pour créer votre propre solution sol à distance. Ce didacticiel est ciblé sur la deuxième approche afin que vous compreniez comment cette solution a été créée. En outre, autant que possible, le didacticiel est cohérent avec l’échantillon et utilise les mêmes noms pour les artefacts (par exemple, des schémas, des transformations) que celui utilisé dans l’exemple.  

>[AZURE.NOTE] Étant donné que cette solution implique l’envoi d’un message à partir d’un pont EAI à un pont de l’EDI, il réutilise l’exemple [exemple de chaînage de pont des Services BizTalk](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) .  

## <a name="what-does-the-solution-do"></a>Quelle est la solution ?

Dans cette solution, Northwind reçoit les factures EDIFACT de Contoso. Ces factures ne sont pas au format standard EDI. Par conséquent, avant d’envoyer la facture à Northwind, il doit être transformé dans un document de facture (également appelé INVOIC) EDIFACT. À la réception, Northwind doit traiter la facture EDIFACT et renvoyer un message de contrôle (également appelé commande) à Contoso.

![][1]  

Pour réaliser ce scénario d’entreprise, Contoso utilise les fonctionnalités fournies avec Microsoft Azure BizTalk Services.

*   Contoso utilise les ponts EAI pour transformer la facture d’origine à EDIFACT INVOIC.

*   Le pont EAI envoie ensuite le message à un pont d’envoi EDI déployé dans le cadre d’un accord configuré dans le portail de Services de BizTalk.

*   Le pont d’envoi EDI traite la EDIFACT INVOIC et l’achemine vers Northwind.

*   Après réception de la facture, Northwind renvoie une commande du message à l’EDI recevoir pont déployé dans le cadre de l’accord.  

> [AZURE.NOTE] Le cas échéant, cette solution montre également comment utiliser le traitement par lots pour envoyer les factures par lots, au lieu d’envoyer chaque facture séparément.  

Pour terminer le scénario, nous utilisons des files d’attente de Bus de Service à envoyer la facture à partir de Contoso à Northwind ou de recevoir un accusé de réception à partir de Northwind. Ces files d’attente peuvent être créées à l’aide d’une application cliente, qui est disponible en téléchargement et est incluse dans le package d’exemple qui est disponible dans le cadre de ce didacticiel.  

## <a name="prerequisites"></a>Conditions préalables

*   Vous devez disposer d’un espace de noms du Bus de Service. Pour obtenir des instructions sur la création d’un espace de noms, consultez [Comment faire : créer ou modifier un Namespace de Service de Bus de Service](https://msdn.microsoft.com/library/azure/hh674478.aspx). Supposons que vous disposez déjà d’un espace de noms du Bus de Service mis en service, appelé **edifactbts**.

*   Vous devez disposer d’un abonnement de Services de BizTalk. Pour obtenir des instructions, consultez [Création d’un BizTalk Service à l’aide d’Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=302280). Pour ce didacticiel, nous vous possédez un abonnement Services BizTalk, appelé **contosowabs**.

*   Enregistrez votre abonnement Services BizTalk sur le portail de Services de BizTalk. Pour obtenir des instructions, consultez [inscription d’un déploiement de Service BizTalk sur le portail de Services de BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)

*   Vous devez disposer de Visual Studio installée.

*   Vous devez avoir installé SDK des Services BizTalk. Vous pouvez télécharger le SDK à partir de [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Étape 1 : Créer les files d’attente de Bus de Service  
Cette solution utilise des files d’attente de Bus de Service d’échanger des messages entre les partenaires commerciaux. Contoso et les Comptoirs envoient des messages pour les files d’attente d’où les ponts EAI et/ou EDI les consomment. Pour cette solution, vous devez disposer de trois files d’attente de Bus de Service :

*   **northwindreceive** – Northwind reçoit la facture de Contoso sur cette file d’attente.

*   **contosoreceive** – Contoso reçoit l’accusé de réception à partir de Northwind sur cette file d’attente.

*   **suspendu** – suspendu tous les messages sont routés vers cette file d’attente. Les messages sont suspendues si elles échouent au cours du traitement.

Vous pouvez créer ces files d’attente de Bus de Service à l’aide d’une application client incluse dans le package d’exemple.  

1.  À partir de l’emplacement où vous avez téléchargé l’exemple, ouvrez le **Didacticiel envoi de factures à l’aide de BizTalk Services EDI Bridges.sln**.

2.  Appuyez sur **F5** pour générer et démarrer l’application **Cliente de didacticiel** .

3.  Dans l’écran, entrez la clé de l’émetteur, nom de l’émetteur et espace de noms ACS de Bus de Service.

    ![][2]  
4.  Un message vous demande que trois files d’attente seront créées dans votre espace de noms du Bus de Service. Cliquez sur **OK**.

5.  Laissez le Client du didacticiel en cours d’exécution. Ouvrir, cliquez sur le **Bus de Service** > **_votre espace de noms du Bus des services_** > **files d’attente**et vérifier que les trois files d’attente sont créées.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Étape 2 : Créer et déployer l’accord de partenariat commercial
Créer un accord de partenariat commercial entre Contoso et les Comptoirs. Un accord de partenariat commercial définit un contrat commercial entre les partenaires commerciaux, tels que le schéma de message à utiliser, le protocole de messagerie à utiliser, etc.. Un accord de partenariat commercial inclut deux ponts EDI, pour envoyer des messages à des partenaires commerciaux (appelés l' **EDI envoyer un pont**) et recevoir des messages à partir des partenaires commerciaux (appelés le **pont de réception EDI**).

Dans le cadre de cette solution, le pont d’envoi EDI correspond à côté de l’envoi de l’accord et est utilisé pour envoyer la facture EDIFACT de Contoso à Northwind. De même, le pont de réception EDI correspond à côté de la réception de l’accord et est utilisé pour recevoir des accusés de réception de Northwind.  

### <a name="create-the-trading-partners"></a>Créer les partenaires commerciaux

Commencez par créer des partenaires commerciaux pour Contoso et les Comptoirs.  

1.  Dans le portail de Services de BizTalk, dans l’onglet **partenaires** , cliquez sur **Ajouter**.

2.  Dans la nouvelle page partenaire, entrez **Contoso** en tant que partenaire nom, puis cliquez sur **Enregistrer**.

3.  Répétez l’étape pour créer le deuxième partenaire, **Northwind**.  

### <a name="create-the-agreement"></a>Créer l’accord
Accords entre partenaires commerciaux sont créés entre les profils des partenaires commerciaux. Cette solution utilise les profils de partenaires qui sont automatiquement créés lorsque nous avons créé les partenaires.  

1.  Dans le portail de Services BizTalk, cliquez sur **accords** > **Ajouter**.

2.  Dans la page des **Paramètres généraux** du nouvel accord, spécifiez les valeurs comme indiqué dans l’image ci-dessous, puis cliquez sur **Continuer**.

    ![][3]  

    Après avoir cliqué sur **Continuer**, deux onglets, **Les paramètres de recevoir** et **Envoyer** deviennent disponibles.

3.  Créer l’accord de l’envoi entre Contoso et les Comptoirs. Ce contrat régit comment Contoso envoie la facture EDIFACT à Northwind.

    1.  Cliquez sur **paramètres d’envoi**.

    2.  Conserver les valeurs par défaut dans les onglets en **Entrant une URL**, de **transformer**et **Batching** .

    3.  Sous l’onglet **protocole** , dans la section des **schémas** , télécharger le schéma **EFACT_D93A_INVOIC.xsd** . Ce schéma est disponible avec le kit.

        ![][4]  
    4.  Sous l’onglet **Transport** , spécifiez les détails pour les files d’attente de Bus de Service. De l’accord du côté envoi, nous utilisons la file d’attente de **northwindreceive** d’envoyer la facture EDIFACT à Northwind et que la file **d’attente** pour acheminer tous les messages qui échouent au cours du traitement et sont suspendus. Vous avez créé ces files d’attente de **étape 1 : créer les files d’attente de Bus de Service** (dans cette rubrique).

        ![][5]  

        Sous **paramètres de Transport > type de Transport** et **paramètres de Suspension des messages > type de Transport**, sélectionnez le Bus des services Azure et fournir les valeurs comme indiqué dans l’image.

4.  Créer l’accord de réception entre Contoso et les Comptoirs. Ce contrat régit comment Contoso reçoit l’accusé de réception à partir de Northwind.

    1.  Cliquez sur **paramètres**.

    2.  Conserver les valeurs par défaut dans les onglets de **Transport** et de **transformation** .

    3.  Sous l’onglet **protocole** , dans la section des **schémas** , télécharger le schéma **EFACT_4.1_CONTRL.xsd** . Ce schéma est disponible avec le kit.

    4.  Sous l’onglet **routage** , créez un filtre pour s’assurer que seuls les accusés de réception à partir de Northwind sont routés vers Contoso. Sous **Paramètres d’itinéraire**, cliquez sur **Ajouter** pour créer le filtre de routage.

        ![][6]  
        1.  Fournir des valeurs pour le **Nom de la règle**, **règle d’itinéraire**et **destination de l’itinéraire** , comme illustré dans l’image.

        2.  Cliquez sur **Enregistrer**.

    5.  Sous l’onglet **routage** , spécifiez où les accusés de réception suspendus (accusés de réception qui ont échoué au cours du traitement) sont routés vers. Définir le type de transport pour le Bus de Service d’Azure, acheminer le type de destination à la **file d’attente**, type d’authentification à la **Signature de l’accès partagé** (SAS), fournir la chaîne de connexion de SAS pour l’espace de noms du Bus de Service, puis entrez le nom de la file d’attente **suspendue**.

5.  Enfin, cliquez sur **Deploy** pour déployer l’accord. Notez les points de terminaison où les envoyer et de recevoir des accords se déploie.

    *   Sous l’onglet **Paramètres d’envoi** , sous **Entrant les URL**, notez le point de terminaison. Pour envoyer un message à partir de Contoso à l’aide de bridge envoi EDI de Northwind, vous devez envoyer un message à ce point de terminaison.

    *   Sous l’onglet **Paramètres de recevoir** , sous **Transport**, notez le point de terminaison. Pour envoyer un message à partir de Northwind à Contoso à l’aide de l’EDI recevoir de pont, vous devez envoyer un message à ce point de terminaison.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Étape 3 : Créer et déployer le projet BizTalk Services

À l’étape précédente, vous déployé l’envoi EDI et recevez des accords pour traiter les factures EDIFACT et les accusés de réception. Ces accords peuvent uniquement traiter les messages qui sont conformes au schéma de message standard EDIFACT. Toutefois, par le scénario de cette solution, Contoso envoie une facture à Northwind dans un schéma propriétaire en interne. Par conséquent, avant que le message est envoyé à la passerelle d’envoi EDI, il doit être transformé à partir du schéma interne au schéma facture standard EDIFACT. Le projet BizTalk Services EAI effectue cette opération.

Le projet BizTalk Services, **InvoiceProcessingBridge**, qui transforme le message est également inclus dans le cadre de l’exemple que vous avez téléchargé. Le projet inclut les artefacts suivants :

*   **INHOUSEINVOICE. XSD** – schéma de la facture en interne qui est envoyée à Northwind.

*   EFACT_D93A_INVOIC de **. XSD** – schéma de la facture standard EDIFACT.

*   EFACT_4.1_CONTRL de **. XSD** – schéma de l’accusé de réception EDIFACT que Northwind envoie à Contoso.

*   INHOUSEINVOICE_to_D93AINVOIC de **. TRFM** – la transformation qui mappe le schéma facture interne au schéma facture standard EDIFACT.  

### <a name="create-the-biztalk-services-project"></a>Créer le projet BizTalk Services
1.  Dans la solution Visual Studio, développez le projet InvoiceProcessingBridge, puis ouvrez le fichier **MessageFlowItinerary.bcs** .

2.  Cliquez n’importe où sur la zone de dessin et de définir l' **URL du Service BizTalk** dans la zone de propriété pour spécifier le nom de votre abonnement de Services de BizTalk. Par exemple, `https://contosowabs.biztalk.windows.net`.

    ![][7]  
3.  À partir de la boîte à outils, faites glisser un **Pont unidirectionnel de Xml** pour la toile. Définissez les propriétés de **Nom de l’entité** et **l’Adresse Relative** du pont à **ProcessInvoiceBridge**. Double-cliquez sur **ProcessInvoiceBridge** pour ouvrir la surface configuration de pont.

4.  Dans la zone **Types de Message** , cliquez sur le signe plus (**+**) pour spécifier le schéma du message entrant. Le message entrant pour le pont EAI étant toujours la facture en interne, attribuez à **INHOUSEINVOICE**.

    ![][8]  
5.  Cliquez sur la forme de la **Transformation du Xml** et dans la zone de propriété pour la propriété de **mappages** , cliquez sur le bouton de sélection (**...**). Dans la boîte de dialogue de **Sélection de cartes** , sélectionnez le fichier de transformation **INHOUSEINVOICE_to_D93AINVOIC** , puis cliquez sur **OK**.

    ![][9]  
6.  Revenir à **MessageFlowItinerary.bcs**et à partir de la boîte à outils, faites glisser un **Point de terminaison de Service externe bidirectionnel** à droite de la **ProcessInvoiceBridge**. Définissez sa propriété de **Nom de l’entité** à **EDIBridge**.

7.  Dans l’Explorateur de solutions, développez le **MessageFlowItinerary.bcs** et double-cliquez sur le fichier **EDIBridge.config** . Remplacez le contenu de **EDIBridge.config** suit.

    > [AZURE.NOTE] Pourquoi dois-je modifier le fichier .config ? Le point de terminaison de service externe que nous avons ajouté à la zone de dessin de Concepteur de pont représente les ponts EDI que nous avons déployé précédemment. Ponts de l’EDI sont des ponts bidirectionnelles, avec un envoi et recevoir de côté. Toutefois, le pont EAI que nous avons ajoutés au Concepteur de pont est un pont à sens unique. Par conséquent, pour gérer les modèles d’échange de message différent des deux ponts, nous utilisons un comportement personnalisé de pont en y compris sa configuration dans le fichier .config. En outre, le comportement personnalisé gère également l’authentification pour le point de terminaison du pont envoi EDI. Ce comportement personnalisé est disponible sous la forme d’un échantillon séparé au [pont des Services BizTalk chaînage sample - EAI à EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Cette solution réutilise l’échantillon.  
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  Mettre à jour le fichier EDIBridge.config pour inclure les détails de configuration

    *   Sous _<behaviors>_, fournir de l’espace de noms ACS et clé associée à l’abonnement des Services BizTalk.

    *   Sous _<client>_, fournir le point de terminaison où l’accord d’envoi EDI est déployé.

    Enregistrer les modifications et fermer le fichier de configuration.

9.  À partir de la boîte à outils, cliquez sur le **connecteur** et rejoignez les composants **ProcessInvoiceBridge** et **EDIBridge** . Sélectionnez le connecteur et dans la boîte de propriétés, la valeur **Condition de filtre** **Tout faire correspondre désélectionnée**. Cela garantit que tous les messages traités par le pont EAI sont routés vers le pont de l’EDI.

    ![][10]  
10.  Enregistrer les modifications apportées à la solution.  

### <a name="deploy-the-project"></a>Déployer le projet

1.  Sur l’ordinateur où vous avez créé le projet BizTalk Services, téléchargez et installez le certificat SSL pour votre abonnement aux Services de BizTalk. À partir, sous les Services BizTalk, cliquez sur le **tableau de bord**, puis cliquez sur **Télécharger un certificat SSL**. Double-cliquez sur le certificat et suivez l’invite pour terminer l’installation. Veillez à qu'installer le certificat dans le magasin de certificats des **Autorités de Certification racine de confiance** .

2.  Dans l’Explorateur de solutions de Visual Studio, cliquez sur le projet **InvoiceProcessingBridge** , puis cliquez sur **déployer**.

3.  Fournir les valeurs comme indiqué dans l’image, puis cliquez sur **déployer**. Vous pouvez obtenir les informations d’identification de l’ACS pour les Services BizTalk en cliquant sur **Informations de connexion** à partir du tableau de bord des Services BizTalk.

    ![][11]  

    Dans le volet sortie, copiez le point de terminaison où est déployé le pont EAI, par exemple, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Vous devez ensuite cette URL de point de terminaison.  

## <a name="step-4-test-the-solution"></a>Étape 4 : Test de la solution


Dans cette rubrique, nous examinons comment tester la solution à l’aide de l’application **Client de didacticiel** fournie dans le cadre de l’échantillon.  

1.  Dans Visual Studio, appuyez sur F5 pour démarrer le **Client du didacticiel**.

2.  L’écran doit avoir des valeurs préremplies à partir de l’étape où nous avons créé les files d’attente de Bus de Service. Cliquez sur **suivant**.

3.  Dans la fenêtre suivante, fournir des informations d’identification de l’ACS pour l’abonnement des Services BizTalk et les points de terminaison où EAI et EDI (réception) des ponts sont déployés.

    Le point de terminaison du pont EAI copié à l’étape précédente. Pour les standards EDI recevoir pont de point de terminaison, dans le portail de Services BizTalk, accédez à l’accord > recevoir les paramètres > Transport > point de terminaison.

    ![][12]  
4.  Dans la fenêtre suivante, Contoso, cliquez sur le bouton **Envoyer la facture en interne** . Dans le fichier de boîte de dialogue, ouvrez le fichier INHOUSEINVOICE.txt. Examiner le contenu du fichier, puis sur **OK** pour envoyer la facture.

    ![][13]  
5.  En quelques secondes, la facture est reçue de Northwind. Cliquez sur le lien **Afficher le Message** pour afficher la facture reçue par Northwind. Notez comment la facture reçue par Northwind dans le schéma de standard EDIFACT tandis que celui envoyé par Contoso a été un schéma interne.

    ![][14]  
6.  Sélectionnez la facture, puis cliquez sur **Envoyer un accusé de réception**. Dans la boîte de dialogue qui s’affiche, notez que l’ID de l’échange est identique dans la facture reçue et l’accusé de réception envoyés. Cliquez sur OK dans la boîte de dialogue **Envoyer un accusé de réception** .

    ![][15]  
7.  En quelques secondes, l’accusé de réception est correctement reçue de Contoso.

    ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Étape 5 (facultatif) : facture EDIFACT d’envoi par lots 
Les ponts EDI de BizTalk Services prend également en charge le traitement par lots des messages sortants. Cette fonctionnalité est utile pour la réception des partenaires qui préfèrent recevoir un lot de messages (critère de réunion) au lieu de messages individuels.

L’aspect le plus important lorsque vous travaillez avec des lots est la version du lot, également appelé les critères de publication. Les critères de publication peuvent reposer sur la manière dont le partenaire de réception souhaite recevoir des messages. Si le traitement par lots est activé, le pont EDI n’envoie pas de message sortant vers le partenaire de réception jusqu'à ce que les critères de publication est exécutée. Par exemple, les critères de traitement par lots basé sur les expéditions de taille de message un lot uniquement lorsque « n » les messages sont regroupés. Critères de traitement par lots peuvent également être basée sur le temps, tels qu’un lot est envoyé à une heure fixe chaque jour. Dans cette solution, nous essayons de la base de critères de taille des messages.

1.  Dans le portail de Services BizTalk, cliquez sur l’accord que vous avez créé précédemment. Cliquez sur paramètres d’envoi > traitement par lots > ajouter le traitement par lots.

2.  Pour nom, entrez **InvoiceBatch**, fournir une description et puis cliquez sur **suivant**.

3.  Spécifier des critères de traitement par lots, qui définit quels messages doivent être regroupés par lots. Dans cette solution, nous avons par lots de tous les messages. Par conséquent, sélectionnez l’utilisation avancée de définitions et entrez **1 = 1**. Il s’agit d’une condition qui est toujours true, et, par conséquent, tous les messages sont regroupés par lots. Cliquez sur **suivant**.

    ![][17]  
4.  Spécifier des critères de lancement par lots. Dans la zone de dépôt, sélectionnez **MessageCountBased**et **Count**, spécifiez **3**. Cela signifie qu’un lot de trois messages sera envoyé à Northwind. Cliquez sur **suivant**.

    ![][18]  
5.  Consultez le résumé, puis cliquez sur **Enregistrer**. Cliquez sur **Deploy** pour redéployer l’accord.

6.  Revenir au **Didacticiel Client**et cliquez sur **Envoyer la facture en interne**, suivez les invites pour envoyer la facture. Vous remarquerez qu’aucune facture n’est reçu de Northwind, car la taille de lot n’est pas remplie. Répétez cette étape deux fois de plus, afin que vous ayez trois messages de factures envoyées à Northwind. Il répond aux critères de version lot de 3 messages et vous devez maintenant voir une facture de Northwind.


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

