<properties
    pageTitle="Mapper un nom de domaine personnalisé à une application Azure"
    description="Découvrez comment mapper un nom de domaine personnalisé (domaine vanity) à votre application de Service d’application Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"
    tags="top-support-issue"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="cephalin"/>

# <a name="map-a-custom-domain-name-to-an-azure-app"></a>Mapper un nom de domaine personnalisé à une application Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Cet article vous montre comment mapper manuellement un nom de domaine personnalisé pour votre application web, back-end de l’application mobile ou API app dans le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md). 

Votre application est déjà fourni avec un sous-domaine unique de azurewebsites.net. Par exemple, si le nom de votre application est **contoso**, son nom de domaine est **contoso.azurewebsites.net**. Toutefois, vous pouvez mapper un domaine personnalisé nom d’application ainsi que son URL, tel que `www.contoso.com`, reflète votre image de marque.

>[AZURE.NOTE] Obtenir de l’aide d’experts Azure sur les [forums Azure](https://azure.microsoft.com/support/forums/). Niveau encore plus élevé de prise en charge, rendez-vous sur le [site d’Azure prend en charge](https://azure.microsoft.com/support/options/) et cliquez sur **Obtenir la prise en charge**.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## <a name="buy-a-new-custom-domain-in-azure-portal"></a>Acheter un nouveau domaine personnalisé dans Azure portal

Si vous ne l’avez pas déjà acheté un nom de domaine personnalisé, vous pouvez acheter et gérer directement dans les paramètres de votre application dans [Azure portal](https://portal.azure.com). Cette option permet de mapper un domaine personnalisé pour votre application, si votre application utilise le [Gestionnaire de trafic Azure](web-sites-traffic-manager-custom-domain-name.md) ou non. 

Pour obtenir des instructions, reportez-vous à la section [acheter un nom de domaine personnalisé pour le Service de l’application](custom-dns-web-site-buydomains-web-app.md).

## <a name="map-a-custom-domain-you-purchased-externally"></a>Mapper un domaine personnalisé que vous avez acheté en externe

Si vous avez déjà acheté un domaine personnalisé [d’Azure DNS](https://azure.microsoft.com/services/dns/) ou d’un fournisseur tiers, il existe trois principales étapes pour mapper le domaine personnalisé pour votre application :

1. [Adresse IP de l’application *(un enregistrement uniquement)* Get](#vip).
2. [Créer les enregistrements DNS qui mappent votre domaine pour votre application](#createdns). 
    - **Où**: votre outil de gestion de registrar du domaine (par exemple DNS d’Azure, GoDaddy, etc.).
    - **Pourquoi**: afin que votre registraire de domaine sait qu’il doit résoudre le domaine personnalisé souhaité pour votre application Azure.
1. [Activer le nom de domaine personnalisé pour votre application Azure](#enable).
    - **Où**: le [portail Azure](https://portal.azure.com).
    - **Pourquoi**: afin que votre application sait qu’il doit pour répondre aux demandes effectuées au nom de domaine personnalisé.
3. [Propagation de vérifier le DNS](#verify).

### <a name="types-of-domains-you-can-map"></a>Types de domaines que vous pouvez mapper

Service d’application Azure vous permet de mapper les catégories suivantes de domaines personnalisés pour votre application.

- **Domaine racine** : le nom de domaine que vous avez réservée dans le Registre de domaine (représenté par la `@` hôte enregistrement, en général). Par exemple, **contoso.com**.
- **Sous-domaine** - n’importe quel domaine qui se trouve sous votre domaine racine. Par exemple, **www.contoso.com** (représenté par la `www` enregistrement hôte).  Vous pouvez mapper des noms de sous-domaines différents du même domaine racine à différentes applications dans Azure.
- **Domaine générique** - [tout sous-domaine dont l’étiquette DNS à l’extrême gauche est `*` ](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (par exemple, les enregistrements d’hôte `*` et `*.blogs`). Par exemple, ** \*. contoso.com**.

### <a name="types-of-dns-records-you-can-use"></a>Types d’enregistrements DNS que vous pouvez utiliser

En fonction de vos besoins, vous pouvez utiliser deux types d’enregistrements DNS standard pour mapper votre domaine personnalisé : 

- [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) - mappages d’adresses directement votre nom de domaine personnalisé pour l’adresse IP virtuelle de l’application Azure. 
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record) - mappe le nom de votre domaine personnalisé au nom de domaine Azure de votre application, * *&lt;*appname*>. azurewebsites.net**. 

L’avantage de CNAME est qu’il persiste à travers les changements d’adresse IP. Si vous supprimez et recréez votre application ou modifiez à partir d’un niveau plus élevé de la tarification à la couche **Shared** , l’adresse IP virtuelle de votre application peut-être changer. Grâce à une telle modification, un enregistrement CNAME est toujours valide, considérant qu’un enregistrement A nécessite une mise à jour. 

Le didacticiel vous indique les étapes à l’aide de l’enregistrement A ainsi également l’enregistrement CNAME.

>[AZURE.IMPORTANT] Ne créez pas un enregistrement CNAME pour votre domaine racine (c'est-à-dire le « enregistrement racine »). Pour plus d’informations, reportez-vous à la section [Pourquoi un enregistrement CNAME est inutilisable dans le domaine racine](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
Pour mapper un domaine racine pour votre application Azure, utilisez un enregistrement A.

<a name="vip"></a>
## <a name="step-1-a-record-only-get-apps-ip-address"></a>Étape 1. *(Enregistrement uniquement)* Obtenir l’adresse IP de l’application
Pour mapper un nom de domaine personnalisé à l’aide d’un enregistrement A, vous avez besoin de l’adresse IP de de votre application Azure. Si vous allez mapper à l’aide d’un enregistrement CNAME à la place, ignorez cette étape et passez à la section suivante.

1.  Connectez-vous au [portail Azure](https://portal.azure.com).

2.  Dans le menu de gauche, cliquez sur **Services d’application** .

4.  Cliquez sur votre application, puis cliquez sur **Custom domains**.

6.  Prenez note de l’adresse IP au-dessus de la section de noms d’hôtes...

    ![Nom de domaine personnalisé de carte avec un enregistrement : adresse IP d’obtenir pour votre application de Service d’application Azure](./media/web-sites-custom-domain-name/virtual-ip-address.png)

7.  Maintenir cette blade de portail. Vous allez y revenir une fois que vous créez les enregistrements DNS.

<a name="createdns"></a>
## <a name="step-2-create-the-dns-records"></a>Étape 2. Créer les enregistrements DNS

Connectez-vous à votre registraire de domaine et de leur outil permet d’ajouter un enregistrement ou un enregistrement CNAME. L’interface utilisateur de chaque registraire étant légèrement différent, vous devez consulter la documentation de votre fournisseur. Toutefois, voici quelques indications générales.

1.  Recherchez la page de gestion des enregistrements DNS. Recherchez des liens ou des zones du site intitulé **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**. Souvent, vous pouvez trouver le lien Affichage des informations de votre compte, puis cliquez sur puis un lien comme **Mon domaines**.
2.  Recherchez un lien qui permet d’ajouter ou de modifier des enregistrements DNS. Cela peut être un **fichier de Zone** ou **Les enregistrements DNS** lien ou un lien de configuration **Avancé** .
3.  Créer l’enregistrement et enregistrez vos modifications.
    - [Instructions pour un enregistrement de sont ici](#a).
    - [Instructions pour un enregistrement CNAME sont ici](#cname).

<a name="a"></a>
### <a name="create-an-a-record"></a>Créer un enregistrement A

Pour utiliser un enregistrement de mapper à l’adresse IP de de votre application Azure, vous devez en fait créer un enregistrement TXT et un enregistrement A. L’enregistrement A est pour la résolution DNS lui-même et l’enregistrement TXT est pour Azure pour vérifier que vous possédez le nom de domaine personnalisé. 

Configurer votre enregistrement A comme suit (@ représente généralement le domaine racine) :
 
<table cellspacing="0" border="1">
  <tr>
    <th>Exemple de nom de domaine complet</th>
    <th>Un hôte</th>
    <th>Une valeur</th>
  </tr>
  <tr>
    <td>Contoso.com (racine)</td>
    <td>@</td>
    <td>Adresse IP de <a href="#vip">étape 1</a></td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>Adresse IP de <a href="#vip">étape 1</a></td>
  </tr>
  <tr>
    <td>*. contoso.com (générique)</td>
    <td>*</td>
    <td>Adresse IP de <a href="#vip">étape 1</a></td>
  </tr>
</table>

Votre enregistrement TXT supplémentaire prend en charge la convention qui mappe à partir de &lt; *sous-domaine*>. &lt; *rootdomain*> à &lt; *appname*>. azurewebsites.net. Configurez l’enregistrement TXT comme suit :

<table cellspacing="0" border="1">
  <tr>
    <th>Exemple de nom de domaine complet</th>
    <th>TXT hôte</th>
    <th>Valeur TXT</th>
  </tr>
  <tr>
    <td>Contoso.com (racine)</td>
    <td>@</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>*. contoso.com (générique)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
###Créer un enregistrement CNAME

Si vous utilisez un enregistrement CNAME pour mapper à un nom de domaine par défaut de votre application Azure, vous n’avez pas besoin un enregistrement TXT supplémentaire comme vous le faites avec un enregistrement A. 

>[AZURE.IMPORTANT] Ne créez pas un enregistrement CNAME pour votre domaine racine (c'est-à-dire le « enregistrement racine »). Pour plus d’informations, reportez-vous à la section [Pourquoi un enregistrement CNAME est inutilisable dans le domaine racine](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
Pour mapper un domaine racine pour votre application Azure, utilisez un [enregistrement A](#a) .

Configurer votre enregistrement CNAME comme suit (@ représente généralement le domaine racine) :

<table cellspacing="0" border="1">
  <tr>
    <th>Exemple de nom de domaine complet</th>
    <th>CNAME hôte</th>
    <th>Valeur CNAME</th>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>*. contoso.com (générique)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
</table>

<a name="enable"></a>
##Étape 3. Activer le nom de domaine personnalisé pour votre application

De retour dans la lame de **Domaines personnalisés** dans le portail Azure (voir [étape 1](#vip)), vous devez ajouter le nom de domaine pleinement qualifié (FQDN) de votre domaine personnalisé à la liste.

1.  Si vous n’avez pas fait, connexion au [portail Azure](https://portal.azure.com).

2.  Dans le portail Azure, cliquez sur **Services d’application** dans le menu de gauche.

3.  Cliquez sur votre application, puis cliquez sur **Custom domains** > **nom d’hôte de l’ajouter**.

4.  Ajoutez le nom de domaine complet de votre domaine personnalisé à la liste (par exemple **www.contoso.com**).

    ![Mapper un nom de domaine personnalisé à une application Azure : ajouter à la liste des noms de domaine](./media/web-sites-custom-domain-name/add-custom-domain.png)

    >[AZURE.NOTE] Azure va tenter de vérifier le nom de domaine que vous utilisez ici. N’oubliez pas qu’il est le même nom de domaine pour lequel vous avez créé un enregistrement DNS à [l’étape 2](#createdns). 

5.  Cliquez sur **Valider**.

6.  Après avoir cliqué sur **Valider** Azure va lancer des processus de vérification du domaine. Il vérifiera propriétaire de domaine ainsi que les succès de rapport et de la disponibilité du nom d’hôte ou erreur détaillé avec des guidence comment corriger l’erreur.    

7.  Si la validation réussit **Ajouter hostname** bouton devient actif et vous pourrez le nom d’hôte de l’affectation. 

8.  Une fois, Azure a terminé la configuration de votre nouveau nom de domaine personnalisé, accédez à votre nom de domaine personnalisé dans un navigateur. Le navigateur devrait ouvrir votre application Azure, ce qui signifie que le nom de votre domaine personnalisé est correctement configuré.

> [AZURE.NOTE] Si l’enregistrement DNS est déjà utiliser (scénario de trafic services domaine active) et vous avez besoin de manière préemptive le lier votre application web pour la vérification du domaine, créez simplement un enregistrements TXT en tant qu’exemples indiqués dans le tableau suivant. Votre enregistrement TXT supplémentaire prend en charge la convention qui mappe à partir de &lt; *sous-domaine*>. &lt; *rootdomain*> à &lt; *appname*>. azurewebsites.net. 
> <table cellspacing="0" border="1">
  <tr>
    <th>Exemple de nom de domaine complet</th>
    <th>TXT hôte</th>
    <th>Valeur TXT</th>
  </tr>
  <tr>
    <td>Contoso.com (racine)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
    <tr>
    <td>*. contoso.com (sub)</td>
    <td>awverify.*.contoso.com</td>
    <td>&lt;<i>appname</i>>. azurewebsites.net</td>
  </tr>
</table>
Une fois cet enregistrement DNS est créé, revenez au portail Azure et ajouter votre nom de domaine personnalisé pour votre application web.
 

<a name="verify"></a>
##Vérifier la propagation DNS

Après avoir terminé les étapes de configuration, il peut prendre un certain temps pour que les modifications pour se propager, en fonction de votre fournisseur DNS. Vous pouvez vérifier que la propagation DNS fonctionne comme prévu à l’aide de [http://digwebinterface.com/](http://digwebinterface.com/). Une fois que vous accédez au site, spécifiez les noms d’hôte dans la zone de texte, puis cliquez sur **Aller**. Vérifier les résultats pour vérifier si les modifications récentes ont pris effet.  

![Mapper un nom de domaine personnalisé à une application Azure : vérifier la propagation DNS](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] La propagation des entrées DNS peut prendre jusqu'à 48 heures (parfois plus longtemps). Si vous avez tout configuré correctement, vous devez attendre la propagation réussisse.

## <a name="next-steps"></a>Étapes suivantes
Apprenez à sécuriser votre nom de domaine personnalisé avec HTTPS par [l’achat d’un certificat SSL dans Azure](web-sites-purchase-ssl-web-site.md) , ou [à l’aide d’un certificat SSL à partir d’un autre emplacement](web-sites-configure-ssl-certificate.md).

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

[Mise en route de DNS d’Azure](../dns/dns-getstarted-create-dnszone.md)  
[Créer des enregistrements DNS pour une application web dans un domaine personnalisé](../dns/dns-web-sites-custom-domain.md)  
[Domaine délégué à DNS Azure](../dns/dns-domain-delegation.md)


<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
