<properties
    pageTitle="Conditions requises pour accéder à l’annonce Azure reporting API. | Microsoft Azure"
    description="En savoir plus sur les conditions requises pour accéder à l’annonce Azure reporting API"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Conditions requises pour accéder à l’annonce Azure reporting API 

La [publicité Azure reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) vous fournir l’accès par programme aux données via un ensemble d’API basées sur le reste. Vous pouvez appeler ces API à partir d’une variété de langages de programmation et outils.

L’API de création de rapports utilise [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour autoriser l’accès à l’API de web. 

Pour préparer votre accès à l’API de création de rapports, vous devez :

1. Créer une application dans vos clients AD Azure 

2. Accordez les autorisations appropriées d’application à accéder aux données AD Azure

3. Recueillir les paramètres de configuration à partir de votre répertoire

Vous avez des questions, des problèmes ou des commentaires, veuillez contacter [DAS Reporting aide](mailto:aadreportinghelp@microsoft.com).


## <a name="create-an-azure-ad-application"></a>Créer une application Azure AD

Pour configurer votre répertoire pour accéder à l’annonce Azure API de création de rapports, vous devez vous connecter au portail Azure classique avec un compte d’administrateur d’abonnement Azure est également un membre du rôle Administrateur général répertoire dans votre client AD Azure.

> [AZURE.IMPORTANT] Applications qui s’exécutent sous les informations d’identification avec des privilèges « admin » comme cela peuvent être très puissantes, donc prenez soin de protéger les informations d’identification d’ID/code secret de l’application.


1. Dans [Azure portal classique](https://manage.windowsazure.com), dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. À partir de la liste **active directory** , sélectionnez votre répertoire.

3. Dans le menu du haut, cliquez sur **Applications**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Sur la barre du bas, cliquez sur **Ajouter**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/03.png) 

5. Sur le **que voulez-vous faire ?** boîte de dialogue, cliquez sur **Ajouter une application à mon entreprise se développe**. 

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/04.png) 


6. Dans la boîte de dialogue **nous dire sur votre application** , effectuez les opérations suivantes : 

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/05.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez un nom (par exemple : Application des API de rapports).

    b. Sélectionnez **l’application Web et/ou des API web**.

    c. Cliquez sur **suivant**.


7. Dans la boîte de dialogue **Propriétés de l’application** , effectuez les opérations suivantes : 

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/06.png) 

    une barre d’outils. Dans la zone de texte **URL de session** , tapez `https://localhost`.

    b. Dans la zone de texte **URI d’ID App** , tapez ```https://localhost/ReportingApiApp```.

    c. Cliquez sur **terminé**.



## <a name="grant-your-application-permission-to-use-the-api"></a>Accorder l’autorisation de votre application d’utiliser l’API

1. Dans [Azure portal classique](https://manage.windowsazure.com/), dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. À partir de la liste **active directory** , sélectionnez votre répertoire.

3. Dans le menu du haut, cliquez sur **Applications**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/02.png)


3. Dans la liste des applications, sélectionnez l’application nouvellement créée.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/07.png)

4. Dans le menu du haut, cliquez sur **configurer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/08.png)


5. Dans la section **autorisations à d’autres applications** , pour la ressource **Azure Active Directory** , cliquez sur la liste déroulante **Autorisations de l’Application** et sélectionnez ensuite les **données d’annuaire en lecture**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/09.png)


5. Sur la barre du bas, cliquez sur **Enregistrer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/10.png)


## <a name="gather-configuration-settings-from-your-directory"></a>Recueillir les paramètres de configuration à partir de votre répertoire

Cette section vous indique comment obtenir les paramètres suivants de votre répertoire :

- Nom de domaine
- ID de client
- Secret des clients

Vous avez besoin de ces valeurs lors de la configuration des appels à l’API de création de rapports. 


### <a name="get-your-domain-name"></a>Obtenir votre nom de domaine

1. Dans [Azure portal classique](https://manage.windowsazure.com), dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. À partir de la liste **active directory** , sélectionnez votre répertoire.

3. Dans le menu du haut, cliquez sur **les domaines**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/11.png) 

4. Dans la colonne de **Nom de domaine** , copiez votre nom de domaine.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/12.png) 


### <a name="get-the-applications-client-id"></a>Obtenir l’ID client de l’application

1. Dans [Azure portal classique](https://manage.windowsazure.com), dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. À partir de la liste **active directory** , sélectionnez votre répertoire.

3. Dans le menu du haut, cliquez sur **Applications**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Dans la liste des applications, sélectionnez l’application nouvellement créée.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/07.png)

4. Dans le menu du haut, cliquez sur **configurer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/08.png)

4. Copiez votre **ID Client**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/13.png)


### <a name="get-the-applications-client-secret"></a>Obtenir le secret de l’application client

Pour obtenir un secret de client de votre application, vous devez créer une nouvelle clé et enregistrer sa valeur lors de l’enregistrement de la nouvelle clé, car il n’est pas possible de récupérer cette valeur ultérieurement plus.

1. Dans [Azure portal classique](https://manage.windowsazure.com), dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. À partir de la liste **active directory** , sélectionnez votre répertoire.

3. Dans le menu du haut, cliquez sur **Applications**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Dans la liste des applications, sélectionnez l’application nouvellement créée.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/07.png)

4. Dans le menu du haut, cliquez sur **configurer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/08.png)

5. Dans la section **touches** , effectuez les opérations suivantes : 

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/14.png)

    une barre d’outils. Dans la liste durée, sélectionnez une durée

    b. Sur la barre du bas, cliquez sur **Enregistrer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/10.png)

    c. Copier la valeur de clé.

## <a name="next-steps"></a>Étapes suivantes

- Vous souhaitez accéder aux données à partir de la publicité Azure reporting API de manière programmée ? Consultez [mise en route avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md).

- Si vous souhaitez en savoir plus sur les rapports d’Azure Active Directory, consultez le [Guide Azure Active Directory Reporting](active-directory-reporting-guide.md).  
