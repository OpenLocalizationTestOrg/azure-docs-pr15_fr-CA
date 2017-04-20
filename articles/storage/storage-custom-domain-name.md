<properties
    pageTitle="Configurer un nom de domaine pour votre point de terminaison du stockage Blob | Microsoft Azure"
    description="Découvrez comment mapper un domaine d’utilisateur personnalisé vers le point de terminaison du stockage Blob pour un compte de stockage Azure dans le portail classique d’Azure."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurer un nom de domaine personnalisé pour votre point de terminaison du stockage Blob

## <a name="overview"></a>Vue d’ensemble

Vous pouvez configurer un domaine personnalisé pour accéder aux données blob dans votre compte de stockage Azure. Le point de terminaison par défaut pour le stockage des objets Blob est `<storage-account-name>.blob.core.windows.net`. Si vous mappez un sous-domaine par exemple **www.contoso.com** et un domaine personnalisé au point de terminaison blob pour votre compte de stockage, puis votre les utilisateurs peuvent également accéder aux données de blob dans votre compte de stockage à l’aide de ce domaine.

>[AZURE.IMPORTANT] Stockage Azure ne gère pas encore HTTPS avec les domaines personnalisés. Nous sommes conscients que les clients sont intéressés par cette fonctionnalité, et il sera disponible dans une version ultérieure.

Il existe deux façons pour votre domaine personnalisé au point de terminaison blob pour votre compte de stockage. Le moyen le plus simple est de créer un enregistrement CNAME de votre domaine personnalisé et un sous-domaine de mappage pour le point de terminaison du blob. Un enregistrement CNAME est une fonctionnalité DNS qui mappe un domaine source vers un domaine de destination. Dans ce cas, le domaine source est votre domaine et sous-domaine--Notez que le sous-domaine est toujours requis. Le domaine de destination est votre point de terminaison de service d’objet Blob.

Le processus de mappage de votre domaine personnalisé pour votre point de terminaison blob peut, toutefois, entraîner une courte période de temps d’arrêt pour le domaine pendant que vous enregistrez le domaine du [Portail classique d’Azure](https://manage.windowsazure.com). Si votre domaine personnalisé est actuellement en charge d’une application avec un accord de niveau de service (SLA) qui requiert sans interruption de service, vous pouvez utiliser le sous-domaine Azure **asverify** pour fournir une étape intermédiaire d’enregistrement afin que les utilisateurs pourront accéder à votre domaine DNS mappage a lieu lors de le.

Le tableau suivant répertorie les URL de l’exemple pour accéder à des données blob dans un compte de stockage nommé **mystorageaccount**. Le domaine personnalisé inscrit pour le compte de stockage est **www.contoso.com**:

Type de ressource|Formats d’URL
---|---
Compte de stockage|**URL par défaut :** http://mystorageaccount.blob.core.windows.net<p>**L’URL du domaine personnalisé :** http://www.contoso.com</td>
Objet BLOB|**URL par défaut :** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**L’URL du domaine personnalisé :** http://www.contoso.com/mycontainer/myblob
Conteneur de racine|**URL par défaut :** http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$ racine/myblob<p>**L’URL du domaine personnalisé :** http://www.contoso.com/myblob ou http://www.contoso.com/$ racine/myblob

## <a name="register-a-custom-domain-for-your-storage-account"></a>Enregistrer un domaine personnalisé pour votre compte de stockage

Cette procédure permet d’enregistrer votre domaine personnalisé si vous n’avez pas de problèmes sur l’icône de domaine être brièvement indisponibles pour les utilisateurs, ou si votre domaine personnalisé n’est pas actuellement hébergeant une application.

Si votre domaine personnalisé est actuellement en charge d’une application qui ne tolèrent aucun temps d’arrêt, utilisez la procédure décrite dans <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">Enregistrer un domaine personnalisé pour votre compte de stockage utilisant le sous-domaine asverify intermédiaires</a>.

Pour configurer un nom de domaine personnalisé, vous devez créer un nouvel enregistrement CNAME à votre registraire de domaine. L’enregistrement CNAME spécifie un alias pour un nom de domaine ; Dans ce cas, il mappe l’adresse de votre domaine personnalisé pour le point de terminaison du stockage Blob pour votre compte de stockage.

Chaque serveur d’inscriptions a une méthode similaire, mais légèrement différente de la spécification d’un enregistrement CNAME, mais le concept est le même. Notez que de nombreux packages de l’inscription de domaine de base n’offrent pas de configuration de DNS, vous serez peut-être amené à mettre à niveau votre package d’inscription de domaine avant de pouvoir créer l’enregistrement CNAME.

1.  Dans le [Portail classique d’Azure](https://manage.windowsazure.com), accédez à l’onglet **stockage** .

2.  Dans l’onglet **stockage** , cliquez sur le nom du compte de stockage pour lequel vous souhaitez mapper le domaine personnalisé.

3.  Cliquez sur l’onglet **configurer** .

4.  En bas de l’écran, cliquez sur **Gérer un domaine** pour afficher la boîte de dialogue **Gérer un domaine personnalisé** . Dans le texte en haut de la boîte de dialogue, vous verrez des informations sur la façon de créer un enregistrement CNAME. Pour cette procédure, ignorer le texte qui fait référence au sous-domaine **asverify** .

5.  Ouvrez une session sur le site Web de votre registraire DNS et accédez à la page de gestion de DNS. Vous pouvez le trouver dans une section comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**.

6.  Recherchez la section de gestion des enregistrements CNAME. Il se peut que vous deviez accéder à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias**ou **sous-domaines**.

7.  Créer un nouvel enregistrement CNAME et fournir un alias de sous-domaine, **www** ou des **photos**. Ensuite fournir un nom d’hôte, qui est votre point de terminaison Blob service dans le format **mystorageaccount.blob.core.windows.net** (où **mystorageaccount** est le nom de votre compte de stockage). Pour utiliser le nom d’hôte est fourni pour vous dans le texte de la boîte de dialogue **Gérer un domaine personnalisé** .

8.  Après avoir créé l’enregistrement CNAME, revenir à la boîte de dialogue **Gérer un domaine personnalisé** et entrez le nom de votre domaine personnalisé, y compris le sous-domaine, dans le champ de **Nom de domaine personnalisé** . Par exemple, si votre domaine est **contoso.com** et votre sous-domaine **www**, entrez **www.contoso.com**; Si votre sous-domaine **photos**, entrez **photos.contoso.com**. Notez que le sous-domaine est requis.

9. Cliquez sur le bouton **Enregistrer** pour enregistrer votre domaine personnalisé.

    Si l’enregistrement est réussie, vous verrez le message de **votre domaine personnalisé est actif**. Les utilisateurs peuvent maintenant afficher blob les données sur votre domaine personnalisé, dans la mesure où ils disposent des autorisations appropriées.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Enregistrer un domaine personnalisé pour votre compte de stockage utilisant le sous-domaine asverify intermédiaire

Cette procédure permet d’enregistrer votre domaine si votre domaine personnalisé est actuellement en charge d’une application avec un SLA qui nécessite qu’il n’y ait aucune interruption de service. En créant un enregistrement CNAME qui pointe à partir d’asverify. &lt;sous-domaine&gt;. &lt;customdomain&gt; à asverify. &lt;storageaccount&gt;. blob.core.windows.net, inscrivez-vous votre domaine avec Azure. Vous pouvez ensuite créer un deuxième CNAME qui pointe de &lt;sous-domaine&gt;. &lt;customdomain&gt; à &lt;storageaccount&gt;. blob.core.windows.net, à partir de laquelle le trafic vers votre domaine personnalisé sera dirigé vers votre point de terminaison du blob.

Le sous-domaine asverify est un sous-domaine spécial reconnu par Azure. En ajoutant **asverify** à votre propre sous-domaine, vous autorisez Azure pour reconnaître votre domaine personnalisé sans modifier l’enregistrement DNS pour le domaine. Une fois que vous modifiez l’enregistrement DNS pour le domaine, il sera mappé au point de terminaison blob sans interruption de service.

1.  Dans le [Portail classique d’Azure](https://manage.windowsazure.com), accédez à l’onglet **stockage** .

2.  Dans l’onglet **stockage** , cliquez sur le nom du compte de stockage pour lequel vous souhaitez mapper le domaine personnalisé.

3.  Cliquez sur l’onglet **configurer** .

4.  En bas de l’écran, cliquez sur **Gérer un domaine** pour afficher la boîte de dialogue **Gérer un domaine personnalisé** . Dans le texte en haut de la boîte de dialogue, vous verrez des informations sur la création de l’enregistrement CNAME dans le sous-domaine **asverify** .

5.  Ouvrez une session sur le site Web de votre registraire DNS et accédez à la page de gestion de DNS. Vous pouvez le trouver dans une section comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**.

6.  Recherchez la section de gestion des enregistrements CNAME. Il se peut que vous deviez accéder à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias**ou **sous-domaines**.

7.  Créer un nouvel enregistrement CNAME et fournir un alias de sous-domaine qui inclut le sous-domaine asverify. Par exemple, le sous-domaine que vous spécifiez sera dans le format **asverify.www** ou **asverify.photos**. Ensuite fournir un nom d’hôte, qui est votre point de terminaison Blob service dans format **asverify.mystorageaccount.blob.core.windows.net** (où **mystorageaccount** est le nom de votre compte de stockage). Pour utiliser le nom d’hôte est fourni pour vous dans le texte de la boîte de dialogue **Gérer un domaine personnalisé** .

8.  Après avoir créé l’enregistrement CNAME, revenir à la boîte de dialogue **Gérer un domaine personnalisé** et entrez le nom de votre domaine personnalisé dans le champ de **Nom de domaine personnalisé** . Par exemple, si votre domaine est **contoso.com** et votre sous-domaine **www**, entrez **www.contoso.com**; Si votre sous-domaine **photos**, entrez **photos.contoso.com**. Notez que le sous-domaine est requis.

9.  Cliquez sur la case à cocher indiquant que **Avancé : permet d’enregistrer préalablement mon domaine personnalisé le sous-domaine 'asverify'**.

10. Cliquez sur le bouton **Enregistrer** pour enregistrer préalablement votre domaine personnalisé.

    Si le préenregistrement est réussie, vous verrez le message de **votre domaine personnalisé est actif**.

11. À ce stade, votre domaine personnalisé a été vérifiée par Azure, mais le trafic vers votre domaine n’est pas encore être routé à votre compte de stockage. Pour terminer le processus, revenir au site Web de votre registraire DNS et créer un autre enregistrement CNAME qui mappe votre sous-domaine à votre point de terminaison de service d’objet Blob. Par exemple, spécifier le sous-domaine en tant que **www** ou **photos**et le nom d’hôte en tant que **mystorageaccount.blob.core.windows.net** (où **mystorageaccount** est le nom de votre compte de stockage). À cette étape, l’enregistrement de votre domaine personnalisé est terminée.

12. Enfin, vous pouvez supprimer l’enregistrement CNAME, vous avez créé à l’aide **asverify**, tel qu’il était nécessaire que comme une étape intermédiaire.

Les utilisateurs peuvent maintenant afficher blob les données sur votre domaine personnalisé, dans la mesure où ils disposent des autorisations appropriées.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>Vérifiez que le domaine personnalisé fait référence à votre point de terminaison de service d’objet Blob

Pour vérifier que votre domaine personnalisé est en effet mappé à votre point de terminaison de service d’objet Blob, créer un objet blob dans un conteneur public dans votre compte de stockage. Puis, dans un navigateur web, utiliser un URI au format suivant pour accéder au blob :

-   http://< ;*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Par exemple, vous pouvez utiliser l’URI suivant pour accéder à un formulaire web via un sous-domaine personnalisé **photos.contoso.com** qui correspond à un objet blob dans le conteneur **myforms** :

-   http://photos.contoso.com/MyForms/ApplicationForm.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Annuler l’inscription d’un domaine personnalisé à partir de votre compte de stockage

Pour annuler l’inscription d’un domaine personnalisé, procédez comme suit : 

1. Connectez-vous à l' [Azure Portal classique](https://manage.windowsazure.com). 

2. Dans le volet de navigation, cliquez sur **stockage**. 

3. Dans **la page,** cliquez sur le nom du compte de stockage pour afficher le tableau de bord. 

5. Sur le ruban, cliquez sur **Gérer le domaine**. 

6. Dans la boîte de dialogue **Gérer un domaine personnalisé** , cliquez sur **désinscrire**. 


## <a name="additional-resources"></a>Ressources supplémentaires

-   [Le mappage personnalisé domaine au point de terminaison réseau CDN (Content Delivery)](../cdn/cdn-map-content-to-custom-domain.md)
