<properties
    pageTitle="Précharger des actifs sur un point de terminaison Azure CDN | Microsoft Azure"
    description="Découvrez comment précharger le contenu mis en cache sur un point de terminaison CDN."
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

# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Précharger des actifs sur un point de terminaison Azure CDN

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Par défaut, actifs sont tout d’abord mis en cache lorsqu’elles sont demandées. Cela signifie que la première requête de chaque région peut prendre plus de temps, dans la mesure où les serveurs edge n’aura pas le contenu mis en cache et qu’il devra transmettre la demande au serveur d’origine. Précharge le contenu évite cette latence d’accès premier.

En plus de fournir une meilleure expérience client, vos ressources mises en cache de précharge peut également réduire le trafic réseau sur le serveur d’origine.

> [AZURE.NOTE] Préchargement des ressources est utile pour les événements de grande taille ou contenu devient accessibles simultanément par un grand nombre d’utilisateurs, par exemple une nouvelle version de film ou d’une mise à jour de logiciel.

Ce didacticiel vous guide tout au long de précharge le contenu mis en cache sur tous les nœuds de bord Azure CDN.

## <a name="walkthrough"></a>Procédure pas à pas

1. Dans le [Portail Azure](https://portal.azure.com), recherchez le profil CDN qui contient le point de terminaison que vous souhaitez charger.  La lame de profil s’ouvre.

2. Cliquez sur le point de terminaison dans la liste.  La lame de point de terminaison s’ouvre.

3. À partir de la blade de point de terminaison CDN, cliquez sur le bouton Charger.

    ![Lame de point de terminaison CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)

    La lame de charge s’ouvre.

    ![Lame de charge CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)

4. Entrez le chemin d’accès complet de chaque élément que vous souhaitez charger (par exemple, `/pictures/kitten.png`) dans la zone de texte **chemin d’accès** .

    > [AZURE.TIP] Plusieurs zones de texte **chemin d’accès** s’affiche une fois que vous entrez du texte pour vous permettre de générer une liste de plusieurs actifs.  Vous pouvez supprimer des éléments dans la liste en cliquant sur le bouton de sélection (...).
    >
    > Chemins d’accès doivent être une URL relative qui correspond à [l’expression régulière](https://msdn.microsoft.com/library/az24scfc.aspx)suivante : `^(?:\/[a-zA-Z0-9-_.\u0020]+)+$`.  Chaque immobilisation doit avoir son propre chemin d’accès.  Il n’existe aucune fonctionnalité de caractère générique pour le chargement des ressources.

    ![Bouton Charger](./media/cdn-preload-endpoint/cdn-load-paths.png)

5. Cliquez sur le bouton **charger** .

    ![Bouton Charger](./media/cdn-preload-endpoint/cdn-load-button.png)

> [AZURE.NOTE] Il existe une limitation de demandes de charge de 10 par minute par profil CDN.

## <a name="see-also"></a>Voir aussi
- [Purge d’un point de terminaison Azure CDN](cdn-purge-endpoint.md)
- [Référence de l’API REST de CDN Azure - Purge ou chargez au préalable un point de terminaison](https://msdn.microsoft.com/library/mt634451.aspx)
