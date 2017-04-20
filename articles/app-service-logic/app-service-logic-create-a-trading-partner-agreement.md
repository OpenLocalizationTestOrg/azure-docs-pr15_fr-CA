<properties 
   pageTitle="Créer un accord de partenariat commercial dans le Service d’application Azure | Microsoft Azure" 
   description="Créer la négociation d’accords de partenariat" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# <a name="creating-a-trading-partner-agreement"></a>Création d’un contrat de partenaire commercial   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Partenaires commerciaux sont les entités impliquées dans B2B les communications (Business-to-Business). Lorsque deux partenaires à établir une relation, il est appelé à un *accord*. L’accord défini est basée sur la communication les deux partenaires souhaite atteindre et soit protocole de transport spécifique. Les différents protocoles de B2B et transports pris en charge par le Service d’application Azure sont les suivants :

- AS2 (déclaration d’applicabilité 2)
- EDIFACT (organisation des Nations unies ou électroniques Data Interchange For Administration, Commerce and Transport (UN/EDIFACT))
- X12 ASC (X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>Applications API BizTalk qui prennent en charge les scénarios B2B
Les applications d’API suivantes activer ces fonctionnalités à l’aide d’une expérience riche et intuitive dans le portail Azure :


## <a name="biztalk-trading-partner-management-tpm"></a>BizTalk (TPM) gestion des partenaires commerciaux
- Création et gestion des identités, des profils et des partenaires
- Stockage et gestion de schémas EDI
- Stockage et gestion des certificats (utilisée dans le protocole de AS2)
- Création et gestion d’accords de AS2
- Création et gestion d’accords de EDIFACT (inclut le traitement par lots sur le côté de l’envoi)
- Création et gestion de X12 des accords (y compris le traitement par lots sur le côté de l’envoi)

![][1]


## <a name="as2-connector"></a>Connecteur AS2
- Exécute les accords AS2 tel que défini dans l’instance d’application API de module de plateforme sécurisée connexe
- Informations de suivi/traitement des surfaces AS2 pour le dépannage


## <a name="biztalk-edifact"></a>BizTalk EDIFACT
- Exécute les accords EDIFACT tel que défini dans l’instance d’application API de module de plateforme sécurisée connexe
- Informations de suivi/traitement des surfaces EDIFACT pour le dépannage
- Fournit la gestion de l’état des lots (start et stop) tels que définis dans les accords EDIFACT dans l’instance d’application API de module de plateforme sécurisée connexe


## <a name="biztalk-x12"></a>BizTalk X12
- Exécute X12 accords tels que définis dans l’instance d’application API de module de plateforme sécurisée connexe 
- Surfaces X12 traitement/suivi des informations pour le dépannage
- Fournit la gestion de l’état des lots (start et stop) comme défini dans X12 ou des contrats dans l’instance d’application API de module de plateforme sécurisée connexe

Comme indiqué auparavant, le AS2, X 12 et EDIFACT API applications nécessitent une application API de module de plateforme sécurisée de la fonction comme prévu.


## <a name="getting-started"></a>Mise en route
Pour créer des contrats avec les partenaires commerciaux :

1. Créez une instance du connecteur **BizTalk-gestion des partenaires commerciaux** . Cette opération nécessite une base de données SQL vide pour la fonction. Avant de démarrer, assurez-vous de disposer d’une base de données vide disponible et prêt à l’emploi.
2. Télécharger les schémas et les certificats requis par les accords. Pour ce faire, navigation sur l’instance du module de plateforme sécurisée créée et pas à pas détaillé dans la partie « Schémas » ou « Certificats »
3. Accédez à l’instance du module de plateforme sécurisée créée et détaillé de la part des **partenaires**
4. Créer des partenaires comme vous le souhaitez. Également modifier les profils selon le cas et ajouter les identités requises
5. Le cadre **d’accords** permet maintenant de créer des accords de. Lorsque vous créez un contrat, vous devez sélectionner le protocole qui sera utilisé. Les autres options de configuration sont basées sur le protocole que vous avez sélectionné.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 
