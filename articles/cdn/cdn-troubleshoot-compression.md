<properties
    pageTitle="Résolution des problèmes de compression de fichier dans Azure CDN | Microsoft Azure"
    description="Résoudre les problèmes grâce à la compression de fichier Azure CDN."
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
    ms.date="09/01/2016"
    ms.author="casoper"/>
    
# <a name="troubleshooting-cdn-file-compression"></a>Résolution des problèmes de compression de fichier CDN

Cet article vous aide à résoudre les problèmes grâce à la [compression de fichier CDN](cdn-improve-performance.md).

Si vous avez besoin d’obtenir de l’aide à tout moment dans cet article, vous pouvez contacter les experts Azure sur [l’Azure MSDN et les forums de débordement de pile](https://azure.microsoft.com/support/forums/). Sinon, vous pouvez également des fichiers un incident de support Azure. Accédez au [site de Support d’Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir la prise en charge**.

## <a name="symptom"></a>Symptôme

Compression de votre point de terminaison est activée, mais les fichiers sont renvoyés comme non compressés.

>[AZURE.TIP] Pour vérifier que si vos fichiers sont retournés compressés, vous devez utiliser un outil tel que [Fiddler](http://www.telerik.com/fiddler) ou les [Outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)de la de votre navigateur.  Les en-têtes de réponse HTTP de vérification est retourné avec votre cache CDN contenus.  S’il existe un en-tête nommé `Content-Encoding` avec une valeur de **gzip**, **bzip2**ou **compressé**, votre contenu est compressé.
>
>![En-tête Content-Encoding](./media/cdn-troubleshoot-compression/cdn-content-header.png)

## <a name="cause"></a>Cause

Il existe plusieurs causes possibles, y compris :

- Le contenu demandé n’est pas éligible pour la compression.
- La compression n’est pas activée pour le type de fichier demandé.
- La demande HTTP n’incluait pas un en-tête de demande un type de compression valide.

## <a name="troubleshooting-steps"></a>Étapes de dépannage

> [AZURE.TIP] Comme avec le déploiement de nouveaux points de terminaison, les modifications de configuration CDN prennent un certain temps à se propager à travers le réseau.  En règle générale, les modifications sont appliquées dans les 90 minutes.  Si c’est la première fois que vous avez paramétrer la compression pour le point de terminaison du Canada, vous devez envisager d’attente 1 à 2 heures pour vous assurer que la paramètres ont été propagées aux POP de compression. 

### <a name="verify-the-request"></a>Vérifiez la demande

Tout d’abord, nous devons procéder à une vérification rapide de validité à la demande.  Vous pouvez utiliser votre navigateur [d’Outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) pour afficher les demandes en cours.

- Vérifiez que la demande est envoyée à votre URL de point de terminaison, `<endpointname>.azureedge.net`et non votre origine.
- Vérifiez que la demande contient un en-tête **Accept-Encoding** , et la valeur de cet en-tête **gzip**, **compressé**ou **bzip2**.

> [AZURE.NOTE] Profils **d’Azure CDN à partir d’Akamai** prennent uniquement en charge le codage de **gzip** .

![En-têtes de demande CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profile"></a>Vérifiez les paramètres de compression (profil Standard CDN)

> [AZURE.NOTE] Cette étape s’applique uniquement si votre profil CDN est un profil **Azure CDN Standard de Verizon** ou **Azure CDN Standard à partir d’Akamai** . 

Naviguez jusqu'à votre point de terminaison dans le [portail Azure](https://portal.azure.com) et cliquez sur le bouton **configurer** .

- Vérifiez que la compression est activée.
- Vérifiez que le type MIME pour le contenu doit être compressée est inclus dans la liste des formats compressés.

![Paramètres de compression CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profile"></a>Vérifiez les paramètres de compression (profil Premium CDN)

> [AZURE.NOTE] Cette étape s’applique uniquement si votre profil CDN est une **Prime à Azure CDN auprès de Verizon** .

Naviguez jusqu'à votre point de terminaison dans le [portail Azure](https://portal.azure.com) et cliquez sur le bouton **Gérer** .  Le portail supplémentaire s’ouvre.  Survolez l’onglet **HTTP volumineux** , puis pointez sur la fenêtre de **Paramètres de Cache** mobile.  Cliquez sur **Compression**. 

- Vérifiez que la compression est activée.
- Vérifiez que la liste des **Types de fichier** contient une liste séparée par des virgules (sans espaces) des types MIME.
- Vérifiez que le type MIME pour le contenu doit être compressée est inclus dans la liste des formats compressés.

![Paramètres de compression CDN premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached"></a>Vérifiez que le contenu est mis en cache.

> [AZURE.NOTE] Cette étape s’applique uniquement si votre profil CDN est un profil **Azure CDN de Verizon** (Standard ou Premium).

À l’aide des outils de développement de votre navigateur, vérifiez les en-têtes de réponse pour vérifier que le fichier est mis en cache dans la région où il est demandé.

- Vérifiez que l’en-tête de réponse de **serveur** .  L’en-tête doit avoir le format **plate-forme (ID/serveur POP)**, comme dans l’exemple suivant.
- Vérifiez que l’en-tête de réponse **Cache-X** .  L’en-tête doit indiquer **d’accès**.  

![En-têtes de réponse CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements"></a>Vérifiez le fichier correspond à la taille requise.

> [AZURE.NOTE] Cette étape s’applique uniquement si votre profil CDN est un profil **Azure CDN de Verizon** (Standard ou Premium).

Pour pouvoir bénéficier de la compression, un fichier doit remplir les dispositions suivantes :

- Plus de 128 octets.
- Inférieure à 1 Mo.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Vérifiez la demande sur le serveur d’origine pour un en-tête **Via**

L’en-tête **Via** HTTP indique au serveur web que la demande est passée par un serveur proxy.  Les serveurs web Microsoft IIS par défaut ne pas compresser les réponses lorsque la demande contient un en-tête **Via** .  Pour substituer ce comportement, effectuez les opérations suivantes :

- **IIS 6**: [HcNoCompressionForProxies de la valeur = « FALSE » dans les propriétés de la métabase IIS](https://msdn.microsoft.com/library/ms525390.aspx)
- **IIS 7 et jusqu'à**: [définir à la fois **noCompressionForHttp10** et **noCompressionForProxies** à False dans la configuration du serveur](http://www.iis.net/configreference/system.webserver/httpcompression)

