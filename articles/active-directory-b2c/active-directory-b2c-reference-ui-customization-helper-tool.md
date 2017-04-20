<properties
    pageTitle="Azure B2C de répertoire Active : Outil d’assistance Page UI personnalisation | Microsoft Azure"
    description="Un outil d’assistance utilisé pour illustrer la fonctionnalité de personnalisation de l’interface utilisateur de page dans Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure B2C de répertoire actif : Un outil d’assistance utilisé pour illustrer la fonctionnalité de personnalisation de l’interface (interface utilisateur) utilisateur page

Cet article est associée à l' [article de personnalisation de l’interface utilisateur principale](active-directory-b2c-reference-ui-customization.md) dans Azure Active Directory (AD Azure) B2C. Les étapes suivantes décrivent comment la fonctionnalité de personnalisation de l’interface utilisateur de page à l’aide d’exemples HTML et CSS de contenu que nous vous avons fourni.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtenir un locataire Azure AD B2C

Avant de pouvoir personnaliser quoi que ce soit, vous devrez [obtenir un locataire Azure AD B2C](active-directory-b2c-get-started.md) si vous n’en avez pas déjà.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Créer une stratégie d’inscription ou d’ouverture de session

L’exemple de contenu que nous vous avons fourni peut être utilisé pour les pages customze deux dans une [stratégie d’inscription ou de connexion](active-directory-b2c-reference-policies.md): la [page de connexion unifiée](active-directory-b2c-reference-ui-customization.md) et la [page attributs d’identification automatique](active-directory-b2c-reference-ui-customization.md). Lors de la [Création de votre stratégie d’abonnement ou d’ouverture de session](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), ajoutez le compte Local (adresse e-mail), Facebook, Google et Microsoft en tant que **fournisseurs d’identité**. Il s’agit des seules IDPs qui accepte notre exemple de contenu HTML.  Vous pouvez également ajouter un sous-ensemble de ces IDPs si vous le souhaitez.

## <a name="register-an-application"></a>Inscription d’une application

Vous devez [Enregistrer une application](active-directory-b2c-app-registration.md) dans vos clients B2C qui peut être utilisé pour exécuter votre stratégie. Après l’enregistrement de votre application, vous disposez de plusieurs options qui vous permet d’exécuter votre stratégie d’inscription :

- Créer un de la AD B2C Azure applications de démarrage rapide répertoriées dans la section « Mise en route » de [signer vers le haut et se connecter aux consommateurs dans vos applications](active-directory-b2c-overview.md#getting-started).
- Utilisez l’application [Azure AD B2C laboratoire](https://aadb2cplayground.azurewebsites.net) prédéfinie. Si vous choisissez d’utiliser le terrain de jeu, vous devez enregistrer une application dans vos clients B2C à l’aide de l' **URI de redirection** `https://aadb2cplayground.azurewebsites.net/`.
- Utilisez le bouton **Exécuter maintenant** sur votre stratégie dans [Azure portal](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Personnaliser votre stratégie

Pour personnaliser l’apparence de votre stratégie, vous devez d’abord créer des fichiers HTML et CSS selon les conventions spécifiques d’Azure AD B2C. Vous pouvez ensuite télécharger votre contenu statique vers un emplacement accessible au public afin que Azure AD B2C peut y accéder. Cela peut être votre propre serveur web dédié, le stockage Blob Azure, Azure Content Delivery Network ou tout autre statique qui héberge des ressources fournisseur. Les seules conditions sont que votre contenu est disponible via le protocole HTTPS et qu’il est accessible à l’aide de CORS. Une fois que vous avez exposé votre contenu statique sur le web, vous pouvez modifier votre stratégie pour désigner cet emplacement et présentent ce contenu à vos clients. [principal article de personnalisation de l’interface utilisateur](active-directory-b2c-reference-ui-customization.md) décrit en détail le fonctionnement de la fonctionnalité de personnalisation Azure AD B2C.

Pour les besoins de ce didacticiel, nous avons déjà créé des exemples de contenu et il hébergés sur le stockage Blob Azure. L’exemple de contenu est une personnalisation de base dans le thème de notre société fictive, « Jouets Wingtip ». Pour l’essayer dans votre propre stratégie, procédez comme suit :

1. Connectez-vous à vos clients sur le [portail Azure](https://portal.azure.com/) et accédez à la lame de fonctionnalités B2C.
2. Cliquez sur **stratégies d’inscription ou de connexion** et puis cliquez sur votre stratégie (par exemple, « b2c\_1\_signe\_des\_signe\_dans »).
3. Cliquez sur **Personnalisation de l’interface utilisateur de la Page** , puis sur **page d’inscription ou de connexion unifiée**.
4. Basculez le bouton de **page personnalisée d’utilisation** à **Oui**. Dans le champ **URI de la page personnalisée** , entrez `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Cliquez sur **OK**.
5. Cliquez sur **page d’inscription de compte Local**. Activer/désactiver le commutateur **d’utiliser un modèle personnalisé** à **Oui**. Dans le champ **URI de la page personnalisée** , entrez `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
5. Répétez la même étape de la **page d’inscription du compte Social**.
 Cliquez sur **OK** deux fois pour fermer les lames de personnalisation de l’interface utilisateur.
6. Cliquez sur **Enregistrer**.

Vous pouvez maintenant tester votre stratégie personnalisée. Si vous le souhaitez, mais vous pouvez également cliquer sur la commande **Exécuter maintenant** dans la carte de stratégie, vous pouvez utiliser votre propre application ou le laboratoire d’Azure AD B2C. Sélectionnez votre application dans la zone de liste déroulante et choisissez l’URI de redirection approprié. Cliquez sur le bouton **Exécuter maintenant** . Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter par l’intermédiaire de l’expérience utilisateur de l’inscription de votre application avec le nouveau contenu en place !

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Télécharger l’exemple de contenu pour le stockage des objets Blob Azure

Si vous souhaitez utiliser le stockage Blob Azure pour héberger le contenu de votre page, vous pouvez créer votre propre compte de stockage et utilisez notre outil d’assistance de B2C pour télécharger vos fichiers.

### <a name="create-a-storage-account"></a>Créer un compte de stockage

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ Nouveau** > **données + stockage** > **compte de stockage**. Vous aurez besoin d’un abonnement Azure pour créer un compte de stockage des objets Blob Azure. Vous pouvez vous inscrire gratuitement sur le [site Web Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Fournir un **nom** pour le compte de stockage (par exemple, « contoso ») et sélectionnez les options concernant le **niveau de tarification**, **groupe de ressources** et **d’abonnement**. Assurez-vous que vous disposez de l’option **Épingler à Startboard** activée. Cliquez sur **créer**.
4. Revenir à la Startboard et cliquez sur le compte de stockage que vous venez de créer.
5. Dans la section **Résumé** , cliquez sur **les conteneurs**, puis cliquez sur **+ Ajouter**.
6. Fournir un **nom** pour le conteneur (par exemple, « b2c ») et sélectionnez le **Blob** en tant que le **type d’accès**. Cliquez sur **OK**.
7. Le conteneur que vous avez créé s’affiche dans la liste sur la blade **d’objets BLOB** . Notez l’URL du conteneur ; par exemple, il doit ressembler à `https://contoso.blob.core.windows.net/b2c`. Fermez la blade **d’objets BLOB** .
8. Sur la lame de compte de stockage, cliquez sur **les clés** et notez les valeurs des champs **Nom du compte de stockage** et de la **Clé d’accès primaire** .

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ Nouveau** > **données + stockage** > **compte de stockage**. Vous aurez besoin d’un abonnement Azure pour créer un compte de stockage des objets Blob Azure. Vous pouvez vous inscrire gratuitement sur le [site Web Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Sélectionnez le **Stockage Blob** , sous **Type de compte**et laissez les autres valeurs par défaut.  Si vous le souhaitez, vous pouvez modifier le groupe de ressources et l’emplacement.  Cliquez sur **créer**.
4. Revenir à la Startboard et cliquez sur le compte de stockage que vous venez de créer.
5. Dans la section **Résumé** , cliquez sur **+ conteneur**.
6. Fournir un **nom** pour le conteneur (par exemple, « b2c ») et sélectionnez le **Blob** en tant que le **type d’accès**. Cliquez sur **OK**.
7. Ouvrez les **Propriétés**du conteneur et notez l’URL du conteneur ; par exemple, il doit ressembler à `https://contoso.blob.core.windows.net/b2c`. Fermez la lame du conteneur.
8. Sur la lame de compte de stockage, cliquez sur l' **Icône de clé** et notez les valeurs des champs **Nom du compte de stockage** et de la **Clé d’accès primaire** .

> [AZURE.NOTE]
    **Clé d’accès primaire** est une information d’identification de sécurité important.

### <a name="download-the-helper-tool-and-sample-files"></a>Télécharger les fichiers d’exemple et outil d’assistance

Vous pouvez télécharger le [stockage d’objets Blob Azure d’outil et exemple de fichiers sous la forme d’un fichier .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) ou cloner GitHub :

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Ce référentiel contient un `sample_templates\wingtip` répertoire, qui contient l’exemple de code HTML, CSS et images. Pour ces modèles faire référence à votre propre compte de stockage des objets Blob Azure, vous devez modifier les fichiers HTML. Ouvrir `unified.html` et `selfasserted.html` et remplacer toutes les instances de `https://localhost` par l’URL de votre propre conteneur que vous avez noté dans les étapes précédentes. Vous devez utiliser le chemin absolu des fichiers HTML, car dans ce cas, le code HTML sera servi par AD Azure, sous le domaine `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Télécharger les fichiers d’exemple

Dans le même référentiel, décompressez `B2CAzureStorageClient.zip` et exécuter la `B2CAzureStorageClient.exe` dans le fichier. Ce programme sera simplement télécharger tous les fichiers dans le répertoire que vous spécifiez pour votre compte de stockage et activer l’accès CORS pour ces fichiers. Si vous avez suivi les étapes ci-dessus, les fichiers HTML et CSS seront maintenant pointer vers votre compte de stockage. Notez que la partie qui précède le nom de votre compte de stockage `blob.core.windows.net`; par exemple, `contoso`. Vous pouvez vérifier que le contenu a été téléchargé correctement lorsque vous essayez d’accéder à `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` dans un navigateur. Également utiliser [http://test-cors.org/](http://test-cors.org/) pour vous assurer que le contenu est désormais CORS activé. (Recherchez « état XHR : 200 » dans le résultat.)

### <a name="customize-your-policy-again"></a>Personnaliser votre stratégie, nouveau

Maintenant que vous avez téléchargé les exemples de contenu à votre compte de stockage, vous devez modifier votre stratégie d’inscription pour le référencer. Répétez les étapes de la section [« Personnaliser votre stratégie »](#customize-your-policy) ci-dessus, cette fois avec l’URL de votre propre compte stockage. Par exemple, l’emplacement de votre `unified.html` fichier serait `<url-of-your-container>/wingtip/unified.html`.

Maintenant, vous pouvez utiliser le bouton **Exécuter maintenant** ou votre propre application d’exécuter votre stratégie à nouveau. Le résultat doit être quasiment identique : vous avez utilisé le même exemple de code HTML et CSS dans les deux cas. Toutefois, vos stratégies font référence maintenant à votre propre instance de stockage des objets Blob Azure, et vous êtes libre de modifier et de télécharger les fichiers à nouveau que vous Veuillez. Pour plus d’informations sur la personnalisation du code HTML et CSS, reportez-vous à l' [article de personnalisation de l’interface utilisateur principale](active-directory-b2c-reference-ui-customization.md).
