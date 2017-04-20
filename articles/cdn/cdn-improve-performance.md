<properties
    pageTitle="Améliorer les performances en compressant des fichiers dans Azure CDN | Microsoft Azure"
    description="Découvrez comment améliorer la vitesse de transfert de fichiers et augmente les performances de chargement de page en compressant vos fichiers dans Azure CDN."
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

# <a name="improve-performance-by-compressing-files"></a>Améliorer les performances en compressant des fichiers

La compression est une méthode simple et efficace pour améliorer la vitesse de transfert de fichiers et d’augmenter les performances de chargement de page en réduisant la taille du fichier avant de l’envoyer à partir du serveur. Il réduit les coûts de bande passante et offre une expérience plus réactive pour vos utilisateurs.

Il existe deux façons d’activer la compression :

- Vous pouvez activer la compression sur votre serveur d’origine, auquel cas le CDN va passer par les fichiers compressés et les fichiers compressés à des clients qui les demandent.
- Vous pouvez activer la compression directement sur les serveurs de périphérie CDN, auquel cas le CDN va compresser les fichiers et il servent aux utilisateurs finaux, même si elles ne sont pas compressées par le serveur d’origine.

> [AZURE.IMPORTANT] Les modifications de configuration CDN prennent un certain temps à se propager à travers le réseau.  Pour les profils <b>Azure CDN à partir d’Akamai</b> , la propagation se termine généralement en moins d’une minute.  Pour les profils <b>Azure CDN de Verizon</b> , vous apercevrez généralement vos modifications s’appliquent dans les 90 minutes.  Si c’est la première fois que vous avez paramétrer la compression pour le point de terminaison du Canada, vous devez envisager d’attente 1 à 2 heures pour vous assurer que la paramètres ont été propagées aux POP avant la résolution des problèmes de compression

## <a name="enabling-compression"></a>Activation de la compression

> [AZURE.NOTE] Les couches Standard et Premium CDN fournissent les mêmes fonctionnalités de compression, mais diffère de l’interface utilisateur.  Pour plus d’informations sur les différences entre les niveaux Standard et Premium CDN, consultez [Vue d’ensemble de Azure CDN](cdn-overview.md).

### <a name="standard-tier"></a>Couche standard

> [AZURE.NOTE] Cette section s’applique aux profils **Azure CDN Standard de Verizon** et **Azure CDN Standard à partir d’Akamai** .

1. À partir de la blade de profil CDN, cliquez sur le point de terminaison CDN que vous souhaitez gérer.

    ![Points de terminaison CDN profil lame](./media/cdn-file-compression/cdn-endpoints.png)

    La lame de point de terminaison CDN s’ouvre.

2. Cliquez sur le bouton **configurer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-file-compression/cdn-config-btn.png)

    La lame CDN Configuration s’ouvre.

3. Activer **la Compression**.

    ![Options de compression CDN](./media/cdn-file-compression/cdn-compress-standard.png)

4. Utilisez les types par défaut ou modifier la liste en supprimant ou ajoutant des types de fichiers.
    
    > [AZURE.TIP] Tant que possible, il n’est pas recommandé d’appliquer la compression à des formats compressés (ZIP, MP3, MP4, JPG, etc.).
    
5. Après avoir apporté vos modifications, cliquez sur le bouton **Enregistrer** .

### <a name="premium-tier"></a>Niveau de prime

> [AZURE.NOTE] Cette section s’applique aux profils de **Prime à Azure CDN auprès de Verizon** .

1. À partir de la blade de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton Gérer les lames de profil CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Survolez l’onglet **HTTP volumineux** , puis pointez sur la fenêtre de **Paramètres de Cache** mobile.  Cliquez sur **Compression**.

    Options de compression sont affichées.

    ![Compression de fichiers](./media/cdn-file-compression/cdn-compress-files.png)

3. Activer la compression en cliquant sur le bouton radio **Activé de Compression** .  Entrez les types MIME que vous souhaitez compresser sous la forme d’une liste délimitée par des virgules (sans espace) dans la zone **Types de fichiers** .
        
    > [AZURE.TIP] Tant que possible, il n’est pas recommandé d’appliquer la compression à des formats compressés (ZIP, MP3, MP4, JPG, etc.). 

4. Après avoir apporté vos modifications, cliquez sur le bouton de **mise à jour** .


## <a name="compression-rules"></a>Règles de compression

Ces tables décrivent le comportement de compression d’Azure CDN pour chaque scénario.

> [AZURE.IMPORTANT] Pour **Azure CDN de Verizon** (Standard et Premium), seuls les fichiers admissibles sont compressés.  Pour pouvoir bénéficier de la compression, un fichier doit :
>
> - Être supérieure à 128 octets.
> - Être inférieure à 1 Mo.
> 
> Pour **Azure CDN à partir d’Akamai**, tous les fichiers sont éligibles pour la compression.
>
> Pour tous les produits Azure CDN, un fichier doit être un type MIME qui a été [configuré pour la compression](#enabling-compression).
>
> Profils **d’Azure CDN de Verizon** (Standard et Premium) prend en charge **gzip**, **compressé**ou **bzip2** codage.  Profils **d’Azure CDN à partir d’Akamai** prennent uniquement en charge le codage de **gzip** .
>
> Les points de terminaison **Azure CDN à partir d’Akamai** demandent toujours des fichiers **gzip** codé depuis l’origine, quelle que soit la demande du client.

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Compression désactivée ou le fichier est inéligible pour la compression

|Format requis du client (via l’en-tête Accept-Encoding)|Format de fichier mis en cache|Réponse CDN au client|Notes|
|----------------|-----------|------------|-----|
|Compressé|Compressé|Compressé|   |
|Compressé|Non compressé|Non compressé|    | 
|Compressé|Non mis en cache|Compressé ou non compressé|Dépend de la réponse d’origine|
|Non compressé|Compressé|Non compressé|    |
|Non compressé|Non compressé|Non compressé|    |   
|Non compressé|Non mis en cache|Non compressé|     |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>Compression activée et le fichier est éligible pour la compression

|Format requis du client (via l’en-tête Accept-Encoding)|Format de fichier mis en cache|Réponse CDN au client|Notes|
|----------------|-----------|------------|-----|
|Compressé|Compressé|Compressé|Transcode CDN entre les formats pris en charge|
|Compressé|Non compressé|Compressé|Effectue une compression CDN|
|Compressé|Non mis en cache|Compressé|Paie Canada-Fichier effectue une compression si origine renvoie non compressé.  **Azure CDN de Verizon** va passer le fichier non compressé lors de la première demande et puis compresser et met en cache le fichier pour les demandes suivantes.  Fichiers avec `Cache-Control: no-cache` en-tête n’est jamais compressé. 
|Non compressé|Compressé|Non compressé|Paie Canada-Fichier effectue la décompression|
|Non compressé|Non compressé|Non compressé|     |  
|Non compressé|Non mis en cache|Non compressé|     |

## <a name="media-services-cdn-compression"></a>Compression du CDN de Services multimédia

De Media Services CDN activée en continu des points de terminaison, la compression est activée par défaut pour les types de contenu suivants : application/vnd.ms-sstr + xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m + xml. Vous ne pouvez pas activer ou désactiver la compression pour les types mentionnés en utilisant le portail Azure.  

## <a name="see-also"></a>Voir aussi
- [Résolution des problèmes de compression de fichier CDN](cdn-troubleshoot-compression.md)    
