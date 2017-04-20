<properties
    pageTitle="Vue d’ensemble des schémas et le module d’intégration entreprise | Microsoft Azure"
    description="Apprenez à utiliser des schémas avec les applications Enterprise Integration Pack et logique"
    services="logic-apps"
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
    ms.date="07/29/2016"
    ms.author="deonhe"/>

# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Obtenir des informations sur les schémas et le module d’intégration entreprise  

## <a name="why-use-a-schema"></a>Pourquoi utiliser un schéma ?
Utilisez des schémas pour confirmer que vous recevez des documents XML sont valides, avec les données attendues dans un format prédéfini. Les schémas sont utilisés pour valider les messages échangés dans un scénario B2B.

## <a name="add-a-schema"></a>Ajouter un schéma.
À partir du portail Azure :  

1. Sélectionnez les **autres services**.  
![Capture d’écran de Azure portail, avec « Services plus » mise en surbrillance](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    

2. Dans la zone Filtre de recherche, entrez **l’intégration**et sélectionnez les **Comptes d’intégration** à partir de la liste des résultats.     
![Capture d’écran de la zone de recherche de filtre](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Sélectionnez le **compte de l’intégration** à laquelle vous ajoutez le schéma.    
![Capture d’écran de la liste des comptes de l’intégration](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  

4. Sélectionnez le carreau de **schémas** .  
![Capture d’écran de IEP intégration compte « Schémas » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Ajouter un fichier de schéma est inférieure à 2 Mo  

1. De la lame de **schémas** s’ouvre (à partir de la procédure précédente), sélectionnez **Ajouter**.  
![Lame de capture d’écran de schémas, avec un bouton « Ajouter » en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  

2. Entrez un nom pour votre schéma. Ensuite, pour télécharger le fichier de schéma, sélectionnez l’icône de dossier située en regard de la zone de texte **schéma** . Une fois le processus de téléchargement est terminé, sélectionnez **OK**.    
![Capture d’écran de « Ajouter un schéma », avec « Petit fichier » mise en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Ajouter un fichier de schéma est supérieur à 2 Mo (jusqu'à un maximum de 8 Mo)  

Ce processus varie selon le niveau d’accès de conteneur blob : **Public** ou **pas d’accès anonyme**. Pour déterminer ce niveau d’accès, dans l' **Explorateur de stockage Azure**, sous **Conteneurs Blob**, sélectionnez le conteneur blob. Sélectionnez **sécurité**et sélectionnez l’onglet de **Niveau d’accès** .

1. Si le niveau d’accès de sécurité blob est **Public**, procédez comme suit.  
  ![Capture d’écran de Azure Explorateur de stockage, avec « Conteneurs Blob », « Sécurité » et « Public » est mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  

    une barre d’outils. Charger le schéma de stockage et copier l’URI.  
    ![Capture d’écran du compte de stockage, avec URI en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  

    b. Dans **Ajouter un schéma**, sélectionnez le **fichier de grande taille**et fournir l’URI dans la zone de texte **URI contenu** .  
    ![Capture d’écran de schémas, avec un bouton « Ajouter » et « Fichier volumineux » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

2. Si le niveau d’accès de sécurité blob n’est **pas l’accès anonyme**, procédez comme suit.  
  ![Capture d’écran de Azure Explorateur de stockage, avec « Conteneurs Blob », « Sécurité » et « Aucun accès anonyme » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  

    une barre d’outils. Charger le schéma de stockage.  
    ![Capture d’écran de compte de stockage](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

    b. Générer une signature d’accès partagé pour le schéma.  
    ![Capture d’écran de sccount de stockage, avec un onglet de signatures d’accès partagé en surbrillance](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

    c. Dans **Ajouter un schéma**, sélectionnez le **fichier de grande taille**et fournir la signature d’accès partagé URI dans la zone de texte **URI contenu** .  
    ![Capture d’écran de schémas, avec un bouton « Ajouter » et « Fichier volumineux » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

3. De la lame de **schémas** du compte EIP d’intégration, vous devez maintenant voir le nouveau schéma.  
![Capture d’écran de EIP intégration compte « Schémas » et le nouveau schéma de mise en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)
  

## <a name="edit-schemas"></a>Modifier des schémas
1. Sélectionnez le carreau de **schémas** .  
2. Sélectionnez le schéma que vous souhaitez modifier à partir de la blade de **schémas** qui s’ouvre.
3. Sur la lame de **schémas** , sélectionnez **Modifier**.  
![Lame de capture d’écran de schémas](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Sélectionnez le fichier de schéma que vous souhaitez modifier à l’aide de la boîte de dialogue de sélecteur de fichier qui s’ouvre.
5. Dans le sélecteur de fichier, sélectionnez **Ouvrir** .  
![Capture d’écran de sélecteur de fichier](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Vous recevez une notification indiquant que le téléchargement a réussi.  

## <a name="delete-schemas"></a>Suppression des schémas
1. Sélectionnez le carreau de **schémas** .  
2. Sélectionnez le schéma que vous souhaitez supprimer de la lame de **schémas** qui s’ouvre.  
3. Sur la lame de **schémas** , sélectionnez **Supprimer**.
![Lame de capture d’écran de schémas](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  

4. Pour confirmer votre choix, cliquez sur **Oui**.  
![Capture d’écran de message de confirmation « Supprimer schéma »](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Enfin, notez que la liste des schémas dans la lame de **schémas** est actualisée et le schéma que vous avez supprimé n’est plus répertorié.  
![Capture d’écran de EIP intégration compte « Schémas » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le pack d’intégration entreprise").  
