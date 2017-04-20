<properties
    pageTitle="Dépannage des points de terminaison Azure CDN renvoi d’état 404 | Microsoft Azure"
    description="Résoudre les problèmes de codes de réponse 404 avec les points de terminaison Azure CDN."
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
    
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>Dépannage des points de terminaison CDN 404 statuts de retour

Cet article vous aide à résoudre les problèmes avec les [points de terminaison CDN](cdn-create-new-endpoint.md) renvoi de 404 erreurs.

Si vous avez besoin d’obtenir de l’aide à tout moment dans cet article, vous pouvez contacter les experts Azure sur [l’Azure MSDN et les forums de débordement de pile](https://azure.microsoft.com/support/forums/). Sinon, vous pouvez également des fichiers un incident de support Azure. Accédez au [site de Support d’Azure](https://azure.microsoft.com/support/options/) et cliquez sur **Obtenir la prise en charge**.

## <a name="symptom"></a>Symptôme

Vous avez créé un profil CDN et un point de terminaison, mais votre contenu ne semble pas être disponible sur le CDN.  Les utilisateurs qui tentent d’accéder à votre contenu via l’URL CDN recevoir des codes d’état HTTP 404. 

## <a name="cause"></a>Cause

Il existe plusieurs causes possibles, y compris :

- Origine du fichier n’est pas visible pour le Canada
- Le point de terminaison est mal configuré, provoquant le CDN rechercher au mauvais endroit
- L’hôte rejette l’en-tête d’hôte à partir du CDN
- Le point de terminaison n’a pas eu le temps de se propager dans l’ensemble du CDN

## <a name="troubleshooting-steps"></a>Étapes de dépannage

> [AZURE.IMPORTANT] Après la création d’un point de terminaison CDN, il pas seront immédiatement disponible pour une utilisation, comme le temps requis pour l’enregistrement de se propager par l’intermédiaire du CDN.  Pour les profils <b>Azure CDN à partir d’Akamai</b> , la propagation se termine généralement en une minute.  Pour les profils <b>Azure CDN de Verizon</b> , propagation termine généralement l’opération dans les 90 minutes, mais dans certains cas, peut prendre plus de temps.  Si vous effectuez les étapes décrites dans ce document et que vous obtenez toujours les 404 réponses, pensez à attendre quelques heures pour vérifier à nouveau avant l’ouverture d’un ticket de support.

### <a name="check-the-origin-file"></a>Vérifiez le fichier d’origine

Tout d’abord, nous devons vérifier que le fichier que nous mis en cache est disponible sur notre origine et est accessible au public.  Le moyen le plus rapide de le faire est d’ouvrir un navigateur dans une en privé ou d’une session de Incognito et accédez directement au fichier.  Simplement, tapez ou collez l’URL dans la zone adresse et voir si qui se produit dans le fichier que vous attendez.  Pour cet exemple, je vais utiliser un fichier dans un compte de stockage Azure, à `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Comme vous pouvez le voir, elle passe avec succès le test.

![Succès !](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [AZURE.WARNING] Alors que c’est la façon plus rapide et la plus simple pour vérifier que votre fichier est disponible publiquement, certaines configurations de réseau de votre organisation pourraient vous donner l’illusion que ce fichier est publiquement disponible lorsqu’il est, en fait, uniquement visible par les utilisateurs de votre réseau (même si elle est hébergée dans Azure).  Si vous avez un navigateur externe à partir de laquelle vous pouvez tester, tel qu’un périphérique mobile qui n’est pas connecté au réseau de votre entreprise, ou une machine virtuelle dans Azure, qui serait préférable.

### <a name="check-the-origin-settings"></a>Vérifiez les paramètres d’origine

Maintenant que nous avons vérifié que le fichier est disponible publiquement sur internet, nous devons vérifier les paramètres d’origine.  Dans le [Portail Azure](https://portal.azure.com), accédez à votre profil CDN et cliquez sur le point de terminaison que vous effectuez un dépannage.  De la lame de **point de terminaison** qui en résulte, cliquez sur l’origine.  

![Lame de point de terminaison avec l’origine mis en surbrillance](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

La lame **d’origine** s’affiche. 

![Lame d’origine](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Nom d’hôte et le type d’origine

Vérifiez que le **type d’origine** est correct et le **nom d’hôte d’origine**.  Dans mon exemple, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, la partie nom d’hôte de l’URL est `cdndocdemo.blob.core.windows.net`.  Comme vous pouvez le voir dans la capture d’écran, c’est correct.  Pour le stockage Azure, Web App et origines de Service Cloud, le champ de **nom d’hôte d’origine** est une liste déroulante, nous n’avez pas besoin de vous inquiéter à propos d’est correctement orthographié.  Cependant, si vous utilisez une origine personnalisée, il est *absolument essentiel* que votre nom d’hôte est correctement orthographié !

#### <a name="http-and-https-ports"></a>Ports HTTP et HTTPS

L’autre chose à vérifier ici est votre **HTTP** et les **ports HTTPS**.  Dans la plupart des cas, 80 et 443 sont corrects, et vous n’aurez besoin d’aucune modification.  Toutefois, si le serveur d’origine est à l’écoute sur un port différent, que devez être représentée ici.  Si vous ne le savez pas, il suffit de considérer l’URL de votre fichier d’origine.  Les spécifications HTTP et HTTPS spécifier les ports 80 et 443 par défaut. Dans mon URL, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, un port n’est spécifié, la valeur par défaut 443 est supposé et mes paramètres sont corrects.  

Toutefois, supposons que l’URL de votre fichier d’origine que vous avez testé le plus haut est `http://www.contoso.com:8080/file.txt`.  Remarque le `:8080` à la fin du nom d’hôte du segment.  Qui indique au navigateur d’utiliser le port `8080` pour se connecter au serveur web à `www.contoso.com`, il vous faudra donc entrer 8080 dans le champ **port HTTP** .  Il est important de noter que ces paramètres du port n’affectent que le port du point de terminaison utilise pour récupérer des informations à partir de l’origine.

> [AZURE.NOTE] Les points de terminaison **Azure CDN à partir d’Akamai** n’autorisent pas la plage de ports TCP complet pour les origines.  Pour obtenir une liste des ports d’origine qui ne sont pas autorisés, consultez [Azure CDN à partir des Ports d’origine Akamai autorisés](https://msdn.microsoft.com/library/mt757337.aspx).  
  
### <a name="check-the-endpoint-settings"></a>Vérifiez les paramètres de point de terminaison

Sur la lame de **point de terminaison** , cliquez sur le bouton **configurer** .

![Lame de point de terminaison avec bouton Configurer mis en surbrillance](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Lame de **configuration** du point de terminaison s’affiche.

![Configurer des lames](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocoles

Pour les **protocoles**, vérifiez que le protocole utilisé par les clients est activée.  Le même protocole que celui utilisé par le client sera celle utilisée pour accéder à l’origine, il est important que les ports d’origine correctement configurés dans la section précédente.  Le point de terminaison écoute uniquement sur les ports par défaut HTTP et HTTPS (80 et 443), quels que soient les ports d’origine.

Nous allons revenir à notre exemple hypothétique avec `http://www.contoso.com:8080/file.txt`.  Comme vous vous en souvenir, Contoso spécifié `8080` en tant que leur HTTP port, mais supposons également qu’ils spécifié `44300` comme leur port HTTPS.  Si elles créé un point de terminaison nommé `contoso`, leur nom d’hôte du point de terminaison CDN serait `contoso.azureedge.net`.  Une demande de `http://contoso.azureedge.net/file.txt` est une demande HTTP, afin que le point de terminaison utiliserez le protocole HTTP sur le port 8080 pour récupérer à partir de l’origine.  Une demande sécurisée via HTTPS, `https://contoso.azureedge.net/file.txt`, provoquerait le point de terminaison à utiliser le protocole HTTPS sur le port 44300 lors de lors de la récupération du fichier à partir de l’origine.

#### <a name="origin-host-header"></a>En-tête de l’hôte origine

L' **en-tête hôte d’origine** est la valeur d’en-tête hôte envoyée à l’origine avec chaque demande.  Dans la plupart des cas, ce doit être le même que le **nom d’hôte d’origine** , nous avons vérifié précédemment.  Une valeur incorrecte dans ce champ ne provoque généralement 404 statuts, mais il est probable que d’autres États 4xx, en fonction de ce qu’attend l’origine.

#### <a name="origin-path"></a>Chemin d’accès d’origine

Enfin, nous devons vérifier notre **chemin d’accès d’origine**.  Par défaut ce champ est vide.  Vous devez uniquement utiliser ce champ si vous souhaitez limiter la portée des ressources hébergées par origine que vous souhaitez rendre disponibles sur le CDN.  

Par exemple, de mon point de sortie, je voulais toutes les ressources sur mon compte de stockage soit disponible, donc je vide le **chemin d’accès d’origine** .  Cela signifie qu’une demande de `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` se traduit par une connexion à partir de mon point de terminaison `cdndocdemo.core.windows.net` que les demandes `/publicblob/lorem.txt`.  De même, une demande de `https://cdndocdemo.azureedge.net/donotcache/status.png` résultats de la demande de point de terminaison `/donotcache/status.png` à l’origine.

Mais que se passe-t-il si je ne veux pas utiliser le CDN pour chaque chemin d’accès sur mon origine ?  Supposons que je voulais uniquement exposer la `publicblob` chemin d’accès.  Si la saisie */publicblob* dans Mon champ de **chemin d’accès d’origine** , qui provoque le point de terminaison insérer */publicblob* avant chaque demande effectuée à l’origine.  Cela signifie que la demande de `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` prendra désormais réellement la partie requête de l’URL, `/publicblob/lorem.txt`et ajoutez `/publicblob` au début. Cela se traduit par une demande de `/publicblob/publicblob/lorem.txt` à l’origine.  Si vous ne résout pas ce chemin d’accès à un fichier existant, l’origine renvoie un état 404.  L’URL correcte pour récupérer lorem.txt dans cet exemple serait effectivement `https://cdndocdemo.azureedge.net/lorem.txt`.  Notez que nous ne pas inclure le chemin d’accès */publicblob* , car la partie requête de l’URL est `/lorem.txt` et le point de terminaison ajoute `/publicblob`, ce qui donne `/publicblob/lorem.txt` la demande transmis à l’origine.
