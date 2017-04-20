<properties
    pageTitle="Purge d’un point de terminaison Azure CDN | Microsoft Azure"
    description="Découvrez comment effacer tout le contenu mis en cache à partir d’un point de terminaison CDN."
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

# <a name="purge-an-azure-cdn-endpoint"></a>Purge d’un point de terminaison Azure CDN

## <a name="overview"></a>Vue d’ensemble

Noeuds Azure CDN cache actifs jusqu'à ce que time-to-live (TTL) l’actif expire.  Après expiration de durée de vie de l’immobilisation, lorsqu’un client demande l’immobilisation à partir du nœud de bord, le nœud de bord récupère une nouvelle copie mise à jour de l’actif pour répondre à la demande du client et banque d’actualiser le cache.

Vous pouvez parfois souhaiter purger le contenu mis en cache à partir de tous les noeuds et forcer tous les récupérer de nouvelles immobilisations mis à jour.  Cela peut être dû à des mises à jour pour votre application web, ou pour mettre à jour rapidement les ressources qui contiennent des informations incorrectes.

> [AZURE.TIP] Notez que la purge efface uniquement le contenu mis en cache sur les serveurs edge CDN.  Tous les caches en aval, tels que des serveurs proxy et les caches du navigateur local, peuvent détenir encore d’une copie mise en cache du fichier.  Il est important de ne pas oublier cet élément lorsque vous définissez un fichier time-to-live.  Vous pouvez forcer un client en aval pour demander la version la plus récente de votre fichier en lui donnant un nom unique chaque fois que vous mettez à jour ou en tirant parti de [la mise en cache de chaîne de requête](cdn-query-string.md).  

Ce didacticiel vous guide dans le processus de purge de capitaux à partir de tous les noeuds d’un point de terminaison.

## <a name="walkthrough"></a>Procédure pas à pas

1. Dans le [Portail Azure](https://portal.azure.com), accédez au profil CDN qui contient le point de terminaison que vous souhaitez nettoyer.

2. À partir de la blade de profil CDN, cliquez sur le bouton de purge.

    ![Lame de profil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)

    La lame de Purge s’ouvre.

    ![Lame de purge CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. Sur la lame de Purge, sélectionnez l’adresse de service que vous souhaitez purger dans la liste déroulante d’URL.

    ![Vider le formulaire](./media/cdn-purge-endpoint/cdn-purge-form.png)

    > [AZURE.NOTE] Vous pouvez également accéder à la lame de Purge en cliquant sur le bouton **Effacer** sur la lame de point de terminaison CDN.  Dans ce cas, le champ **URL** est préremplie avec l’adresse du service de ce point de terminaison spécifique.

4. Permet de sélectionner les actifs que vous souhaitez purger des nœuds de bord.  Si vous souhaitez effacer toutes les immobilisations, cliquez sur la case à cocher **Tout purger** .  Dans le cas contraire, tapez le chemin d’accès complet de chaque élément que vous souhaitez nettoyer (par exemple, `/pictures/kitten.png`) dans la zone de texte **chemin d’accès** .

    > [AZURE.TIP] Plusieurs zones de texte **chemin d’accès** s’affiche une fois que vous entrez du texte pour vous permettre de générer une liste de plusieurs actifs.  Vous pouvez supprimer des éléments dans la liste en cliquant sur le bouton de sélection (...).
    >
    > Chemins d’accès doivent être une URL relative qui correspondent à [l’expression régulière](https://msdn.microsoft.com/library/az24scfc.aspx)suivante : `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)*\*$";`.  Pour **Azure CDN de Verizon** (Standard et Premium), astérisque (\*) peut être utilisé comme un caractère générique (par exemple, `/music/*`).  Les caractères génériques et **Purger toutes** ne sont pas autorisées avec **Azure CDN à partir d’Akamai**.
    
5. Cliquez sur le bouton **Effacer** .

    ![Bouton purger](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] Demandes de purge prennent environ 2-3 minutes pour traiter avec **Azure CDN de Verizon** (Standard et Premium) et environ 7 minutes avec **Azure CDN à partir d’Akamai**.  Azure CDN a une limite de 50 simultanées de purger des demandes à un moment donné. 

## <a name="see-also"></a>Voir aussi
- [Précharger des actifs sur un point de terminaison Azure CDN](cdn-preload-endpoint.md)
- [Référence de l’API REST de CDN Azure - Purge ou chargez au préalable un point de terminaison](https://msdn.microsoft.com/library/mt634451.aspx)
