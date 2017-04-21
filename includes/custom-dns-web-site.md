#<a name="configuring-a-custom-domain-name-for-an-azure-website"></a>Configuration d’un nom de domaine personnalisé pour un site Web d’Azure

Lorsque vous créez un site Web, Azure fournit un sous-domaine convivial sur le domaine azurewebsites.net afin que vos utilisateurs puissent accéder à votre site Web à l’aide d’une URL, comme http://&lt;monsite >. azurewebsites.net. Toutefois, si vous configurez vos sites Web partagé ou en mode Standard, vous pouvez mapper votre site Web à votre propre nom de domaine.

Si vous le souhaitez, vous pouvez utiliser Azure Traffic Manager pour charger le trafic entrant de solde sur votre site Web. Pour plus d’informations sur le fonctionnement du Gestionnaire de trafic avec des sites Web, consultez [Contrôle Azure Sites Web du trafic avec Azure Traffic Manager][trafficmanager].

> [AZURE.NOTE] Les procédures décrites dans cette tâche s’applique aux sites Web d’Azure ; pour les Services en nuage, consultez <a href="/develop/net/common-tasks/custom-dns/">configuration d’un nom de domaine personnalisé dans Azure</a>.

> [AZURE.NOTE] Les étapes de cette tâche requièrent que vous permet de configurer vos sites Web partagé ou en mode Standard, ce qui peut changer à combien vous êtes facturé pour votre abonnement. Pour plus d’informations, reportez-vous à la section <a href="/pricing/details/web-sites/">Détails de la tarification des sites Web</a> .

Dans cet article :

-   [Présentation des enregistrements CNAME et A](#understanding-records)
-   [Configurer vos sites web en mode partagé ou standard](#bkmk_configsharedmode)
-   [Ajoutez vos sites web vers le Gestionnaire de trafic](#trafficmanager)
-   [Ajouter un enregistrement CNAME pour votre domaine personnalisé](#bkmk_configurecname)
-   [Ajouter un enregistrement A pour votre domaine personnalisé](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Comprendre les enregistrements CNAME et A</h2>

CNAME (ou alias) et un enregistrements permettent d’associer un nom de domaine à un site Web, mais ils fonctionnent différemment.

###<a name="cname-or-alias-record"></a>Enregistrement de nom canonique ou l’Alias

Un enregistrement CNAME associe un domaine *spécifique* , comme **contoso.com** ou **www.contoso.com**, à un nom de domaine complet. Dans ce cas, le nom de domaine canonique est le le ** &lt;myapp >. azurewebsites.net** nom de domaine de votre site Web Azure ou la ** &lt;myapp >. trafficmgr.com** nom de domaine de votre profil de gestionnaire de trafic. Une fois créé, l’enregistrement CNAME crée un alias pour le ** &lt;myapp >. azurewebsites.net** ou ** &lt;myapp >. trafficmgr.com** nom de domaine. L’entrée CNAME résout l’adresse IP de votre ** &lt;myapp >. azurewebsites.net** ou ** &lt;myapp >. trafficmgr.com** nom de domaine automatiquement, donc si l’adresse IP du site Web change, vous n’avez pas à intervenir.

> [AZURE.NOTE] Certains bureaux de domaine uniquement vous permettent de mapper les sous-domaines lors de l’utilisation d’un enregistrement CNAME, par exemple www.contoso.com et non les noms de racine, par exemple, contoso.com. Pour plus d’informations sur les enregistrements CNAME, consultez la documentation fournie par le document <a href="http://tools.ietf.org/html/rfc1035">IETF de noms de domaine - implémentation et spécification</a> , <a href="http://en.wikipedia.org/wiki/CNAME_record">l’entrée de Wikipedia sur l’enregistrement CNAME</a>ou votre registraire.

###<a name="a-record"></a>Un enregistrement

Un enregistrement A mappe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un domaine générique* tel que ** \*. contoso.com**, à une adresse IP. Dans le cas d’un site Web d’Azure, soit l’adresse IP virtuelle du service ou un spécifique d’adresses IP que vous avez achetés pour votre site Web. L’avantage principal d’un enregistrement A sur un enregistrement CNAME est que vous pouvez avoir une entrée qui utilise un caractère générique, tel que * **. contoso.com**, qui prendrait en charge tels que les demandes de plusieurs sous-domaines * *mail.contoso.com**, * *login.contoso.com**, ou * *www.contso.com**.

> [AZURE.NOTE] Dans la mesure où un enregistrement A est mappé à une adresse IP statique, il ne peut pas résoudre automatiquement les modifications à l’adresse IP de votre site Web. Une adresse IP pour une utilisation avec un enregistrements est fournie lorsque vous configurez les paramètres de nom de domaine personnalisé pour votre site Web ; Toutefois, cette valeur peut changer si vous supprimez et recréez votre site Web ou modifiez le mode de site Web à sauvegarder pour libérer.

> [AZURE.NOTE] Impossible d’utiliser des enregistrements a pour le trafic avec le Gestionnaire de périphériques d’équilibrage de charge. Pour plus d’informations, consultez [Contrôle Azure Sites Web du trafic avec le Gestionnaire de trafic Azure][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Configurer vos sites Web en mode partagé ou standard</h2>

Définition d’un nom de domaine personnalisé sur un site Web est uniquement disponible pour les modes Standard pour les sites Web Azure Shared. Avant de passer à un site Web à partir du mode site Web gratuit sur le partagé ou site Web Standard, vous devez d’abord supprimer majuscules dépenses en place pour l’abonnement de votre site Web. Pour plus d’informations sur la tarification de mode partagé et Standard, consultez les [Détails de tarification][PricingDetails].

1. Dans votre navigateur, ouvrez le [Portail de gestion][portal].
2. Dans l’onglet **sites Web** , cliquez sur le nom de votre site.

    ![][standardmode1]

3. Cliquez sur l’onglet **échelle** .

    ![][standardmode2]


4. Dans la section **Général** , définissez le mode de site Web en cliquant sur **partagé**.

    ![][standardmode3]

    > [AZURE.NOTE] Si vous utilisez le Gestionnaire de trafic avec ce site Web, vous devez utiliser sélectionnez mode Standard et non partagé.

5. Cliquez sur **Enregistrer**.
6. Lorsque vous êtes invité à propos de l’augmentation des coûts pour le mode partagé (ou en mode Standard si vous choisissez Standard), cliquez sur **Oui** si vous l’acceptez.

    <!--![][standardmode4]-->

    **Remarque**<br />
   Si vous recevez une erreur « Echelle de configuration pour le site Web 'site Web ' nom a échoué », vous pouvez utiliser le bouton Détails pour obtenir plus d’informations.

<a name="trafficmanager"></a><h2>(Facultatif) Ajoutez vos sites Web vers le Gestionnaire de trafic</h2>

Si vous souhaitez utiliser votre site Web avec le Gestionnaire de trafic, effectuez les opérations suivantes.

1. Si vous ne disposez pas d’un profil de Traffic Manager, utilisez les informations de [Création d’un profil de gestionnaire de trafic à l’aide de création rapide] [ createprofile] pour en créer un. Remarque le **. trafficmgr.com** nom de domaine associé à votre profil de Traffic Manager. Il sera utilisé dans une étape ultérieure.

2. Utilisez les informations de [Ajouter ou supprimer des points de terminaison de] [ addendpoint] pour ajouter votre site Web sous la forme d’un point de terminaison dans votre profil de gestionnaire de trafic.

    > [AZURE.NOTE] Si votre site Web n’apparaît pas lors de l’ajout d’un point de terminaison, vérifiez qu’il est configuré pour le mode Standard. Vous devez utiliser le mode Standard pour votre site Web pour fonctionner avec le Gestionnaire de trafic.

3. Ouvrez une session sur le site Web de votre registraire DNS et accédez à la page de gestion de DNS. Recherchez des liens ou des zones du site étiqueté comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**.

4. Maintenant rechercher dans laquelle vous pouvez sélectionner ou entrer des enregistrements CNAME. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés. Vous devez rechercher les mots **CNAME**, **Alias**ou **sous-domaines**.

5. Vous devez également fournir l’alias de domaine ou un sous-domaine de l’enregistrement CNAME. Par exemple, **www** si vous souhaitez créer un alias pour **www.customdomain.com**.

5. Vous devez également fournir un nom d’hôte est le nom de domaine complet de cet alias CNAME. Il s’agit de la **. trafficmgr.com** nom de votre site Web.

Par exemple, l’enregistrement CNAME suivant transmet tout le trafic à partir de **www.contoso.com** à **contoso.trafficmgr.com**, le nom de domaine d’un site Web :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nom d’alias / d’hôte/sous-domaine</strong></td>
<td><strong>Domaine canonique</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.trafficmgr.com</td>
</tr>
</table>

Un visiteur de **www.contoso.com** ne verront jamais l’hôte true (contoso.azurewebsite.net), afin que le processus de transfert est invisible pour l’utilisateur final.

> [AZURE.NOTE] Si vous utilisez le Gestionnaire de trafic avec un site Web, vous n’avez pas besoin de suivre les étapes décrites dans les sections suivantes, «**Ajouter un enregistrement CNAME pour votre domaine personnalisé**» et «**Ajouter un enregistrement A pour votre domaine personnalisé**». L’enregistrement CNAME créé dans les étapes précédentes acheminera le trafic entrant à Traffic Manager, qui les dirige ensuite le trafic vers le point de terminaison de site Web.

<a name="bkmk_configurecname"></a><h2>Ajouter un enregistrement CNAME pour votre domaine personnalisé</h2>

Pour créer un enregistrement CNAME, vous devez ajouter une nouvelle entrée dans la table DNS pour votre domaine personnalisé à l’aide des outils fournis par votre registraire. Chaque serveur d’inscriptions a une méthode similaire, mais légèrement différente de la spécification d’un enregistrement CNAME, mais les concepts sont identiques.

1. Utilisez une des méthodes suivantes pour rechercher la **. azurewebsite.net** nom de domaine attribué à votre site Web.

    * Connexion au [Portail de gestion Azure][portal], sélectionnez votre site Web, sélectionnez le **tableau de bord**et ensuite de trouver l’entrée de **l’URL du Site** dans la section **Aperçu rapide** .

    * Installer et configurer [Azure Powershell](/manage/install-and-configure-windows-powershell/)et puis utiliser la commande suivante :

            get-azurewebsite yoursitename | select hostnames

    * Installer et configurer l' [Interface de ligne de commande Azure](/manage/install-and-configure-cli/)et utilisez ensuite la commande suivante :

            azure site domain list yoursitename

    Enregistrer ce **. azurewebsite.net** nom, car il sera utilisé dans les étapes suivantes.

3. Ouvrez une session sur le site Web de votre registraire DNS et accédez à la page de gestion de DNS. Recherchez des liens ou des zones du site étiqueté comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**.

4. Maintenant rechercher dans laquelle vous pouvez sélectionner ou entrer des enregistrements CNAME. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés. Vous devez rechercher les mots **CNAME**, **Alias**ou **sous-domaines**.

5. Vous devez également fournir l’alias de domaine ou un sous-domaine de l’enregistrement CNAME. Par exemple, **www** si vous souhaitez créer un alias pour **www.customdomain.com**. Si vous souhaitez créer un alias pour le domaine racine, il peut être répertorié comme le '**@**' symbole dans les outils de votre registraire DNS.

5. Vous devez également fournir un nom d’hôte est le nom de domaine complet de cet alias CNAME. Il s’agit de la **. azurewebsite.net** nom de votre site Web.

Par exemple, l’enregistrement CNAME suivant transmet tout le trafic à partir de **www.contoso.com** à **contoso.azurewebsite.net**, le nom de domaine d’un site Web :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nom d’alias / d’hôte/sous-domaine</strong></td>
<td><strong>Domaine canonique</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.azurewebsite.NET</td>
</tr>
</table>

Un visiteur de **www.contoso.com** ne verront jamais l’hôte true (contoso.azurewebsite.net), afin que le processus de transfert est invisible pour l’utilisateur final.

> [AZURE.NOTE] L’exemple ci-dessus ne s’applique qu’au trafic sur le sous-domaine __www__ . Dans la mesure où vous ne pouvez pas utiliser des caractères génériques avec des enregistrements CNAME, vous devez créer un CNAME pour chaque domaine/sous-domaine. Si vous souhaitez diriger le trafic à partir de sous-domaines, tel que *. contoso.com, à l’adresse azurewebsite.net, vous pouvez configurer une entrée de __Redirection d’URL__ ou __URL suivant__ dans vos paramètres DNS, ou créez un enregistrement A.

> [AZURE.NOTE] Il peut prendre un certain temps pour votre CNAME pour se propager à travers le système DNS. Vous ne pouvez pas définir l’enregistrement CNAME pour le site Web jusqu'à ce que l’enregistrement CNAME a propagé. Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l’enregistrement CNAME n’est disponible.

###<a name="add-the-domain-name-to-your-website"></a>Ajouter le nom de domaine pour votre site Web

Une fois l’enregistrement CNAME pour le nom de domaine a été propagée, vous devez l’associer à votre site Web. Vous pouvez ajouter le nom de domaine personnalisé défini par l’enregistrement CNAME à votre site Web à l’aide de l’Interface de ligne de commande de Azure (Azure CLI) ou via le portail de gestion Azure.

**Pour ajouter un nom de domaine à l’aide des outils de ligne de commande**

Installer et configurer l' [Interface de ligne de commande d’Azure](/manage/install-and-configure-cli/)et puis utiliser la commande suivante :

    azure site domain add customdomain yoursitename

Par exemple, celui-ci ajoute un nom de domaine personnalisé de **www.contoso.com** sur le site Web de **contoso.azurewebsite.net** :

    azure site domain add www.contoso.com contoso

Vous pouvez vérifier que le nom de domaine personnalisé a été ajouté au site Web à l’aide de la commande suivante :

    azure site domain list yoursitename

La liste renvoyée par cette commande doit contenir le nom de domaine personnalisé, ainsi que la valeur par défaut **. azurewebsite.net** entrée.

**Pour ajouter un nom de domaine en utilisant le portail de gestion Azure**

1. Dans votre navigateur, ouvrez le [Portail de gestion Azure][portal].

2. Dans l’onglet **sites Web** , cliquez sur le nom de votre site, sélectionnez le **tableau de bord**et puis sélectionnez **Gérer les domaines** à partir du bas de la page.

    ![][setcname2]

6. Dans la zone de texte **Noms de domaine** , tapez le nom de domaine que vous avez configurés.

    ![][setcname3]

6. Cliquez sur la case à cocher pour accepter le nom de domaine.

Une fois la configuration terminée, le nom de domaine personnalisé apparaît dans la section **noms de domaine** de la page de **configuration** de votre site Web.

<a name="bkmk_configurearecord"></a><h2>Ajouter un enregistrement A pour votre domaine personnalisé</h2>

Pour créer un enregistrement A, vous devez tout d’abord rechercher l’adresse IP de votre site Web. Puis ajoutez une entrée dans la table DNS pour votre domaine personnalisé en utilisant les outils fournis par votre registraire. Chaque serveur d’inscriptions a une méthode similaire, mais légèrement différente de la spécification d’un enregistrement de, mais les concepts sont identiques. En plus de créer un enregistrement A, vous devez également créer un enregistrement CNAME Azure utilise pour vérifier l’enregistrement A.

1. Dans votre navigateur, ouvrez le [Portail de gestion Azure][portal].

2. Dans l’onglet **sites Web** , cliquez sur le nom de votre site, sélectionnez le **tableau de bord**et puis sélectionnez **Gérer les domaines** à partir du bas de l’écran.

    ![][setcname2]

5. Dans la boîte de dialogue **Gérer les domaines personnalisés** , recherchez **L’adresse IP à utiliser lors de la configuration des enregistrements de**. Copiez l’adresse IP. Il sera utilisé lors de la création de l’enregistrement A.

5. Dans la boîte de dialogue **Gérer les domaines personnalisés** , notez le nom de domaine awverify à la fin du texte en haut de la boîte de dialogue. Il doit être **awverify.mysite.azurewebsites.net** où **monsite** est le nom de votre site Web. Copier, comme c’est le nom de domaine utilisé lors de la création de l’enregistrement CNAME de vérification.

6. Ouvrez une session sur le site Web de votre registraire DNS et accédez à la page de gestion de DNS. Recherchez des liens ou des zones du site étiqueté comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**.

6. Rechercher dans laquelle vous pouvez sélectionner ou entrer des enregistrements A et CNAME. Vous devrez peut-être sélectionner le type d’enregistrement dans une liste déroulante vers le bas, ou accéder à une page de paramètres avancés.

7. Effectuez les étapes suivantes pour créer l’enregistrement A :

    1. Sélectionnez ou entrez le domaine ou le sous-domaine qui utilise l’enregistrement A. Par exemple, sélectionnez **www** si vous souhaitez créer un alias pour **www.customdomain.com**. Si vous souhaitez créer une entrée de caractère générique pour tous les sous-domaines, entrez «__*__». Il couvre tous les sous-domaines, par exemple **mail.customdomain.com**, **login.customdomain.com**et **www.customdomain.com**.

        Si vous souhaitez créer un enregistrement A pour le domaine racine, il peut être répertorié comme le '**@**' symbole dans les outils de votre registraire DNS.

    2. Entrez l’adresse IP de votre service cloud dans le champ fourni. Cela associe l’entrée de domaine utilisée dans l’enregistrement A avec l’adresse IP du déploiement de votre service cloud.

        Par exemple, le suivant Qu'un enregistrement transmet tout le trafic provenant de **contoso.com** à **137.135.70.239**, l’adresse IP de notre application déployée :

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>Nom d’hôte/sous-domaine</strong></td>
        <td><strong>Adresse IP</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        Cet exemple illustre la création d’un enregistrement A pour le domaine racine. Si vous souhaitez créer une entrée de caractère générique pour couvrir tous les sous-domaines, vous devez entrer «__*__» que le sous-domaine.

7. Ensuite, créez un enregistrement CNAME qui a un alias de **awverify**et un domaine canonique **awverify.mysite.azurewebsites.net** que vous avez obtenu précédemment.

    > [AZURE.NOTE] Alors que l’alias awverify peut fonctionner pour certains bureaux d’enregistrement, d’autres peuvent nécessiter le nom de domaine complet alias d’awverify.www.customdomainname.com ou de awverify.customdomainname.com.

    Par exemple, le texte suivant crée un enregistrement CNAME Azure permet de vérifier la configuration de l’enregistrement A.

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>Nom d’alias / d’hôte/sous-domaine</strong></td>
    <td><strong>Domaine canonique</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.NET</td>
    </tr>
    </table>

> [AZURE.NOTE] Il peut prendre un certain temps pour l’awverify pour se propager à travers le système DNS CNAME. Vous ne pouvez pas définir le nom de domaine personnalisé défini par l’enregistrement A pour le site Web jusqu'à ce que l’awverify CNAME a propagé. Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l’enregistrement CNAME n’est disponible.

###<a name="add-the-domain-name-to-your-website"></a>Ajouter le nom de domaine pour votre site Web

Après que l’enregistrement CNAME de **awverify** pour le nom de domaine a été propagée, vous pouvez ensuite associer le domaine personnalisé défini par l’enregistrement de votre site Web. Vous pouvez ajouter le nom de domaine personnalisé défini par l’enregistrement de votre site Web en utilisant soit la CLI d’Azure, ou à l’aide du portail de gestion Azure.

**Pour ajouter un nom de domaine à l’aide de l’Interface de ligne de commande (CLI Azure) Azure**

Installer et configurer l' [Infrastructure du langage commun Azure](/manage/install-and-configure-cli/)et utilisez ensuite la commande suivante :

    azure site domain add customdomain yoursitename

Par exemple, celui-ci ajoute un nom de domaine personnalisé de **contoso.com** au site Web de **contoso.azurewebsite.net** :

    azure site domain add contoso.com contoso

Vous pouvez vérifier que le nom de domaine personnalisé a été ajouté au site Web à l’aide de la commande suivante :

    azure site domain list yoursitename

La liste renvoyée par cette commande doit contenir le nom de domaine personnalisé, ainsi que la valeur par défaut **. azurewebsite.net** entrée.

**Pour ajouter un nom de domaine en utilisant le portail de gestion Azure**

1. Dans votre navigateur, ouvrez le [Portail de gestion Azure][portal].

2. Dans l’onglet **sites Web** , cliquez sur le nom de votre site, sélectionnez le **tableau de bord**et puis sélectionnez **Gérer les domaines** à partir du bas de la page.

    ![][setcname2]

6. Dans la zone de texte **Noms de domaine** , tapez le nom de domaine que vous avez configurés.

    ![][setcname3]

6. Cliquez sur la case à cocher pour accepter le nom de domaine.

Une fois la configuration terminée, le nom de domaine personnalisé apparaît dans la section **noms de domaine** de la page de **configuration** de votre site Web.

> [AZURE.NOTE] Après avoir ajouté le nom de domaine personnalisé défini par l’enregistrement de votre site Web, vous pouvez supprimer l’enregistrement CNAME d’awverify à l’aide des outils fournis par votre registraire. Toutefois, si vous souhaitez ajouter un autre enregistrement à l’avenir, vous devrez recréer l’awverify enregistrement avant de pouvoir associer le nouveau nom de domaine défini par la nouvelle, un enregistrement avec le site Web.

## <a name="next-steps"></a>Étapes suivantes

-   [Comment faire pour gérer des sites web](/manage/services/web-sites/how-to-manage-websites/)

-   [Configurer un certificat SSL pour les Sites Web](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
