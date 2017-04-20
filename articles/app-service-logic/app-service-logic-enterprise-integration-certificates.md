
<properties
    pageTitle="L’utilisation de certificats avec le Pack d’intégration entreprise | Microsoft Azure"
    description="Apprenez à utiliser des certificats avec le Pack d’intégration entreprise et la logique d’applications"
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
    ms.date="09/06/2016"
    ms.author="deonhe"/>

# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>En savoir plus sur les certificats et Pack d’intégration entreprise

## <a name="overview"></a>Vue d’ensemble
Intégration de l’entreprise utilise des certificats pour sécuriser les communications de B2B. Vous pouvez utiliser deux types de certificats dans vos applications d’intégration entreprise :

- Certificats publics, qui doivent être achetés auprès d’une autorité de certification (CA).
- Certificats privés, qui vous pouvez d’émettre vous-même. Ces certificats sont parfois appelés des certificats auto-signés.


## <a name="what-are-certificates"></a>Que sont les certificats ?
Les certificats sont des documents numériques qui vérifient l’identité des participants dans les communications électroniques et qui également sécuriser les communications électroniques.

## <a name="why-use-certificates"></a>Pourquoi utiliser des certificats ?
Parfois les communications B2B doivent rester confidentielles. Intégration de l’entreprise utilise des certificats pour sécuriser les communications de deux manières :

- Grâce au cryptage du contenu des messages
- En signant numériquement des messages  

## <a name="how-do-you-upload-certificates"></a>Comment télécharger des certificats ?

### <a name="public-certificates"></a>Certificats publics
Pour utiliser un *certificat public* dans vos applications de logique avec capacités de B2B, vous devez d’abord télécharger le certificat à votre compte d’intégration. Pour utiliser un *certificat auto-signé*, d’autre part, vous devez d’abord le charger [Azure clé coffre-fort](../key-vault/key-vault-get-started.md "en savoir plus sur le stockage en chambre forte de clé").

Après avoir téléchargé un certificat, il est disponible pour vous aider à sécuriser vos messages B2B lors de la définition de leurs propriétés dans les [accords](./app-service-logic-enterprise-integration-agreements.md) que vous créez.  

Voici les étapes détaillées pour le téléchargement de vos certificats publics dans votre compte d’intégration après que vous être inscrit sur le portail Azure :

1. Sélectionnez **Parcourir**.  
    ![Sélectionnez Parcourir](./media/app-service-logic-enterprise-integration-overview/overview-1.png)  

2. Entrez **l’intégration** dans la zone de filtre de recherche et sélectionnez **Intégration de comptes** à partir de la liste des résultats.     
    ![Sélectionnez les comptes de l’intégration](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. Sélectionnez le compte auquel vous souhaitez ajouter le certificat d’intégration.  
    ![Sélectionnez le compte auquel vous souhaitez ajouter le certificat d’intégration](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4.  Sélectionnez le carreau de **certificats** .  
    ![Sélectionnez le carreau de certificats](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. Dans la lame de **certificats** qui s’ouvre, sélectionnez le bouton **Ajouter** .
    ![Cliquez sur le bouton Ajouter](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Entrez un **nom** pour votre certificat et sélectionnez le type de certificat. (Dans cet exemple, nous avons utilisé le type de certificat public). Sélectionnez l’icône de dossier située à droite de la zone de texte du **certificat** .

7. Lorsque le sélecteur de fichier s’ouvre, recherchez et sélectionnez le fichier de certificat que vous voulez télécharger sur votre compte d’intégration.

8. Sélectionnez le certificat et cliquez sur **OK** dans le sélecteur de fichier. Il valide et télécharge le certificat à votre compte d’intégration.

8. Enfin, sur la lame **Ajouter certificat** , sélectionnez le bouton **OK** .  
    ![Cliquez sur le bouton OK](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)  

9. Dans environ une minute, vous verrez une notification indiquant que le téléchargement du certificat est terminé.

10. Sélectionnez le carreau de **certificats** . Vous devez voir le certificat nouvellement ajouté.  
    ![Consultez le nouveau certificat](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>Certificats privés
Vous pouvez télécharger les certificats privés sur votre compte d’intégration en effectuant les opérations suivantes :  

1. [Télécharger votre clé privée clé coffre-fort] (../key-vault/key-vault-get-started.md "En savoir plus sur la chambre forte de clé").  

    > [AZURE.TIP] Vous devez autoriser la fonctionnalité des applications de logique de Service d’application Azure pour effectuer des opérations sur le coffre-fort de la clé. Vous pouvez accorder l’accès à l’entité de sécurité de service des applications de logique à l’aide de la commande PowerShell suivante :`Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  

2. Créer un certificat privé.  

3. Télécharger le certificat privé à votre compte d’intégration.

Une fois que vous avez pris les étapes précédentes, vous pouvez utiliser le certificat privé pour créer des accords.

Voici les étapes détaillées pour le téléchargement de vos certificats privés dans votre compte d’intégration après que vous être inscrit sur le portail Azure :  

1. Sélectionnez **Parcourir**.  
    ![Télécharger vos certificats privés dans votre compte d’intégration](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    

2. Entrez **l’intégration** dans la zone de filtre de recherche et sélectionnez **Intégration de comptes** à partir de la liste des résultats.     
    ![Sélectionnez les comptes de l’intégration](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  

3. Sélectionnez le compte auquel vous souhaitez ajouter le certificat d’intégration.  
    ![Sélectionnez le compte auquel vous souhaitez ajouter le certificat d’intégration](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4. Sélectionnez le carreau de **certificats** .  
    ![Sélectionnez le carreau de certificats](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)  

5. Dans la lame de **certificats** qui s’ouvre, sélectionnez le bouton **Ajouter** .
    ![Cliquez sur le bouton Ajouter](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Entrez un **nom** pour votre certificat et sélectionnez le type de certificat. (Dans cet exemple, nous avons utilisé le type de certificat public). Sélectionnez l’icône de dossier située à droite de la zone de texte du **certificat** .

7. Lorsque le sélecteur de fichier s’ouvre, recherchez et sélectionnez le fichier de certificat que vous voulez télécharger sur votre compte d’intégration.

8. Une fois que vous avez sélectionné le certificat, sélectionnez **OK** dans le sélecteur de fichier. Cette action valide le certificat et le télécharge sur votre compte d’intégration.

9. Enfin, sur la lame **Ajouter certificat** , sélectionnez le bouton **OK** .  
    ![Ajouter le certificat](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)  

10. Dans environ une minute, vous verrez une notification indiquant que le téléchargement du certificat est terminé.

11. Sélectionnez le carreau de **certificats** . Vous devez voir le certificat nouvellement ajouté.
    ![Consultez le nouveau certificat](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)  

Après avoir téléchargé un certificat, il est disponible pour vous aider à sécuriser vos messages B2B lors de la définition de leurs propriétés dans des [accords](./app-service-logic-enterprise-integration-agreements.md).  

## <a name="next-steps"></a>Étapes suivantes
- [Créer une application de logique qui utilise des fonctionnalités B2B](./app-service-logic-enterprise-integration-b2b.md)  
- [Créer un accord B2B](./app-service-logic-enterprise-integration-agreements.md)  
- [En savoir plus sur le stockage en chambre forte de clé] (../key-vault/key-vault-get-started.md "En savoir plus sur la clé coffre-fort")  
