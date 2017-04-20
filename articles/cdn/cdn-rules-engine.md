<properties
    pageTitle="Le comportement par défaut HTTP dans Azure CDN utilisant le moteur de règles | Microsoft Azure"
    description="Le moteur de règles vous permet de personnaliser le mode de traitement des demandes HTTP par Azure CDN, telles que le blocage de la remise de certains types de contenu, de définir une stratégie de mise en cache et de modifier les en-têtes HTTP."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="override-default-http-behavior-using-the-rules-engine"></a>Substituer le comportement HTTP par défaut à l’aide du moteur de règles

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Vue d’ensemble

Le moteur de règles vous permet de personnaliser la gestion des requêtes HTTP, telles que le blocage de la remise de certains types de contenu, définition d’une stratégie de mise en cache et la modification des en-têtes HTTP.  Ce didacticiel va vous montrer création d’une règle qui change le comportement du cache des actifs CDN.  Contenu vidéo est également disponible dans la section «[Voir aussi](#see-also)».

## <a name="tutorial"></a>Didacticiel

1. À partir de la blade de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Cliquez sur l’onglet **HTTP volumineux** , suivi par le **Moteur de règles**.

    Options pour une nouvelle règle s’affichent.

    ![Nouvelles options de règle CDN](./media/cdn-rules-engine/cdn-new-rule.png)

    >[AZURE.IMPORTANT] L’ordre dans lequel apparaissent plusieurs règles affecte la façon dont ils sont gérés. Une règle suivante peut-être remplacer les actions spécifiées par une règle antérieure.
    
3. Entrez un nom dans la **nom / Description** zone de texte.

4. Identifiez le type de demandes que la règle s’applique à.  Par défaut, la condition de correspondance **toujours** est sélectionnée.  Vous allez utiliser **toujours** pour ce didacticiel, laissez sélectionné.

    ![Condition de correspondance CDN](./media/cdn-rules-engine/cdn-request-type.png)

    >[AZURE.TIP] Il existe de nombreux types de correspondance disponibles dans la liste déroulante des conditions.  En cliquant sur l’icône d’informations bleue à gauche de la condition de correspondance expliquera la condition sélectionnée en détail.
    >
    >Pour obtenir la liste complète des conditions de correspondance en détail, consultez [Condition de correspondre de moteur de règles et les détails de la fonction](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_0).

5.  Cliquez sur le **+** en regard de **fonctionnalités** pour ajouter une nouvelle fonction.  Dans la liste déroulante de gauche, sélectionnez la **Force interne Max-Age**.  Dans la zone de texte qui s’affiche, entrez **300**.  Laissez les valeurs par défaut restantes.

    ![Fonctionnalité CDN](./media/cdn-rules-engine/cdn-new-feature.png)

    >[AZURE.NOTE] Comme avec des conditions de correspondance, cliquez sur l’icône d’informations bleue à gauche de la nouvelle fonctionnalité affiche plus d’informations sur cette fonctionnalité.  Dans le cas **Force interne Max-Age**, nous sommes substituant **Cache-Control** et **Expires** en-têtes son de contrôle lorsque le nœud de bord CDN actualisera l’immobilisation à partir de l’origine.  Notre exemple de 300 secondes signifie que le nœud de bord CDN met en cache l’actif pendant 5 minutes avant d’actualiser l’immobilisation à partir de son origine.
    >
    >Pour obtenir la liste complète des fonctionnalités de manière détaillée, voir [Condition de correspondance de moteur de règles et les détails de la fonction](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1).

6.  Cliquez sur le bouton **Ajouter** pour enregistrer la nouvelle règle.  La nouvelle règle est maintenant en attente d’approbation. Une fois qu’il a été approuvé, le statut devient à partir de **XML en attente** **XML actif**.

    >[AZURE.IMPORTANT] Les modifications de règles peuvent prendre jusqu'à 90 minutes pour se propager à travers le CDN.

## <a name="see-also"></a>Voir aussi
* [Les vendredis azure : puissantes fonctionnalités Premium nouveau de Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vidéo)
* [Condition de correspondance de moteur de règles et des détails sur la fonction](https://msdn.microsoft.com/library/mt757336.aspx)
