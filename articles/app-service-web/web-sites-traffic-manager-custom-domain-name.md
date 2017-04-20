<properties
    pageTitle="Configurer un nom de domaine personnalisé pour une application web dans le Service d’application Azure qui utilise le Gestionnaire de trafic d’équilibrage de la charge."
    description="Utilisez un nom de domaine personnalisé pour une une application web dans le Service d’application Azure qui comprend le Gestionnaire de trafic d’équilibrage de la charge."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configuration d’un nom de domaine personnalisé pour une application web dans le Service d’application Azure à l’aide du Gestionnaire de trafic

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article fournit des instructions pour l’utilisation d’un nom de domaine personnalisé avec le Service d’application Azure génériques qui utilisent le Gestionnaire de trafic d’équilibrage de la charge.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## <a name="understanding-dns-records"></a>Présentation des enregistrements DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## <a name="configure-your-web-apps-for-standard-mode"></a>Configurer vos applications web en mode standard

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## <a name="add-a-dns-record-for-your-custom-domain"></a>Ajouter un enregistrement DNS pour votre domaine personnalisé

> [AZURE.NOTE] Si vous avez acheté le domaine par le biais de Azure Application Service Web Apps puis passez comme suit et reportez-vous à l’étape finale de l’article [d’Acheter un domaine pour les applications Web](custom-dns-web-site-buydomains-web-app.md) .

Pour associer votre domaine personnalisé à une application web dans le Service d’application Azure, vous devez ajouter une nouvelle entrée dans la table DNS pour votre domaine personnalisé à l’aide des outils fournis par le greffier du domaine que vous avez acheté votre nom de domaine à partir de. Utilisez les étapes suivantes pour rechercher et utiliser les outils DNS.

1. Connectez-vous à votre compte à votre registraire de domaine et recherchez une page de gestion des enregistrements DNS. Recherchez des liens ou des zones du site étiqueté comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**. Souvent vous trouverez un lien vers cette page d’affichage des informations de votre compte, puis recherche d’un lien de **Mes domaines**.

1. Une fois que vous avez trouvé la page de gestion pour votre nom de domaine, recherchez un lien qui vous permet de modifier les enregistrements DNS. Cela peut être répertorié en tant que **fichier de Zone**, **Les enregistrements DNS**, ou sous la forme d’un lien de configuration **Avancé** .

    * La page a probablement quelques enregistrements déjà créés, par exemple, une entrée '**@**'ou'\*' avec une page « stationnement de domaine ». Il peut également contenir les enregistrements pour les sous-domaines courants tels que **www**.
    * La page sera mentionner les **enregistrements CNAME**ou fournir une liste déroulante pour sélectionner un type d’enregistrement. Il peut aussi mentionner les autres enregistrements tels que les **enregistrements MX**et **un enregistrement** . Dans certains cas, les enregistrements CNAME seront appelées par d’autres noms comme un **Enregistrement d’Alias**.
    * La page aura également des champs qui vous permettent de **mappage** à partir d’un **nom d’hôte** ou le **nom de domaine** à un autre nom de domaine.

1. *Que les caractéristiques de chaque inscription varient, en général vous mappez votre nom de domaine personnalisé (par exemple, **contoso.com**,)* *pour* le nom de domaine de Traffic Manager (**contoso.trafficmanager.net**) qui est utilisé pour votre application web.

    > [AZURE.NOTE] Vous pouvez également, si un enregistrement est déjà en cours d’utilisation et vous devez manière préemptive lier vos applications, vous pouvez créer un enregistrement CNAME supplémentaire. Par exemple, pour lier des manière préemptive **www.contoso.com** à votre application web, créer un enregistrement CNAME à partir de **awverify.www** à **contoso.trafficmanager.net**. Vous pouvez ensuite ajouter « www.contoso.com » à votre application Web sans modifier l’enregistrement CNAME « www ». Pour plus d’informations, consultez les [enregistrements DNS de créer pour une application web dans un domaine personnalisé][CREATEDNS].

1. Lorsque vous avez terminé d’ajouter ou de modifier des enregistrements DNS à votre registraire, enregistrer les modifications.

<a name="enabledomain"></a>
## <a name="enable-traffic-manager"></a>Activer le Gestionnaire de trafic

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
