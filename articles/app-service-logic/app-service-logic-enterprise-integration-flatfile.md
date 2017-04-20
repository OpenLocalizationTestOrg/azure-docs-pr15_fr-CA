<properties
    pageTitle="Apprenez à coder ou décoder les fichiers plats, à l’aide d’applications Enterprise Integration Pack et logique | Service d’application Microsoft Azure | Microsoft Azure"
    description="Utiliser les fonctionnalités des applications Enterprise Integration Pack et logique pour coder ou décoder les fichiers plats"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="msftman"
    manager="erikre"
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-flat-files"></a>Intégration des fichiers plats

## <a name="overview"></a>Vue d’ensemble

Vous souhaiterez peut-être coder le contenu XML avant de l’envoyer à un partenaire commercial dans un scénario d’entreprise-entreprise (B2B). Dans une application de logique effectuée par la fonctionnalité d’applications de la logique du Service application Azure, vous pouvez utiliser le connecteur d’encodage du fichier plat pour effectuer cette opération. L’application de la logique que vous créez peut obtenir ses XML contenu à partir de diverses sources, y compris à partir d’un déclencheur de la demande HTTP, à partir d’une autre application ou même à partir d’un des nombreux [connecteurs](../connectors/apis-list.md). Pour plus d’informations sur les applications de logique, consultez la [documentation des applications logique](./app-service-logic-what-are-logic-apps.md "en savoir plus sur les applications de la logique").  

## <a name="how-to-create-the-flat-file-encoding-connector"></a>Comment créer le connecteur d’encodage du fichier plat

Suivez ces étapes pour ajouter un lien à votre application de la logique d’encodage du fichier plat.

1. Créer une application de logique et le [lier à votre compte d’intégration](./app-service-logic-enterprise-integration-accounts.md "apprendre à lier un compte de l’intégration à une application de logique"). Ce compte contient le schéma que vous allez utiliser pour coder les données XML.  
2. Ajouter un déclencheur **- requête HTTP d’un lors de la demande** à votre application de logique.  
![Capture d’écran de déclencheur pour sélectionner](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
3. Ajoutez le fichier plat de codage action, comme suit :

    une barre d’outils. Cliquez sur le signe **plus** .

    b. Cliquez sur le lien **Ajouter une action** (apparaît après que vous avez sélectionné le signe plus).

    c. Dans la zone Rechercher, entrez *plat* pour filtrer toutes les actions à celui que vous souhaitez utiliser.

    d. Dans la liste, sélectionnez l’option de **Codage du fichier plat** .   
![Option de capture d’écran de plat codage de fichier](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
4. Dans la boîte de dialogue **Codage de fichier plat** , sélectionnez la zone de texte **contenu** .  
![Capture d’écran de contenu zone de texte](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)  
5. Sélectionnez la balise body comme le contenu que vous voulez coder. La balise body remplit le champ de contenu.     
![Capture d’écran de la balise body](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)  
6. Sélectionnez la zone de liste **Nom de schéma** et sélectionnez le schéma que vous souhaitez utiliser pour coder le contenu d’entrée.    
![Zone de liste nom de la capture d’écran de schéma](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)  
7. Enregistrez votre travail.   
![Icône de capture d’écran d’enregistrement](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)  

À ce stade, vous avez terminé de configurer votre connecteur de codage de fichier plat. Dans une application réelle, vous souhaitez peut-être stocker les données codées dans une application de métier, tels que de la force de vente. Ou bien, vous pouvez envoyer que les données codées à un commercial partenaire. Vous pouvez facilement ajouter une action pour envoyer la sortie de l’action de codage à la force de vente, ou à vos partenaires commerciaux, à l’aide de l’un des autres connecteurs fournis.

Vous pouvez maintenant tester votre connecteur en effectuant une demande au point de terminaison HTTP, y compris le contenu XML dans le corps de la demande.  

## <a name="how-to-create-the-flat-file-decoding-connector"></a>Comment créer le connecteur de décodage de fichier plat

>[AZURE.NOTE] Pour effectuer ces étapes, vous devez disposer d’un fichier de schéma déjà téléchargé dans votre compte d’intégration.

1. Ajouter un déclencheur **- requête HTTP d’un lors de la demande** à votre application de logique.  
![Capture d’écran de déclencheur pour sélectionner](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
2. Ajoutez le fichier plat de décodage action, comme suit :

    une barre d’outils. Cliquez sur le signe **plus** .

    b. Cliquez sur le lien **Ajouter une action** (apparaît après que vous avez sélectionné le signe plus).

    c. Dans la zone Rechercher, entrez *plat* pour filtrer toutes les actions à celui que vous souhaitez utiliser.

    d. Dans la liste, sélectionnez l’option de **Décodage de fichier plat** .   
![Option de capture d’écran de plat décodage de fichier](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
- Sélectionnez le contrôle de **contenu** . Cela produit une liste du contenu à partir des étapes précédentes que vous pouvez utiliser comme contenu à décoder. Notez que le *corps* de la demande HTTP entrante est disponible pour être utilisé comme contenu pour décoder. Vous pouvez également entrer le contenu de décoder directement dans le contrôle de **contenu** .     
- Sélectionnez la balise *Body* . Notez que la balise body est présent dans le contrôle de **contenu** .
- Sélectionnez le nom du schéma que vous souhaitez utiliser pour décoder le contenu. La capture d’écran suivante montre que *OrderFile* est le nom de schéma sélectionné. Ce nom de schéma a été téléchargé précédemment dans le compte de l’intégration.

 ![Capture d’écran de plat décodage de fichier de boîte de dialogue](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)    
- Enregistrez votre travail.  
![Icône de capture d’écran d’enregistrement](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)    

À ce stade, vous avez terminé de configurer votre connecteur de décodage de fichier plat. Dans une application réelle, vous souhaitez peut-être stocker les données décodées dans une application métier de tel que de la force de vente. Vous pouvez facilement ajouter une action pour envoyer la sortie de l’action de décodage pour la force de vente.

Vous pouvez maintenant tester votre connecteur par une demande pour le point de terminaison HTTP et y compris le contenu XML que vous souhaitez décoder dans le corps de la demande.  

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack d’intégration entreprise").  
