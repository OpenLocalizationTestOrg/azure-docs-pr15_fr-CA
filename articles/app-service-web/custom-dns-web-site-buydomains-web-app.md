<properties
    pageTitle="Comment acheter un nom de domaine personnalisé dans Azure Application Service Web Apps"
    description="Découvrez comment acheter un nom de domaine personnalisé avec une application web dans le Service d’application Azure."
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
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="buy-and-configure-a-custom-domain-name-in-azure-app-service"></a>Acheter et configurer un nom de domaine personnalisé dans le Service d’application Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Lorsque vous créez une application web, Azure assigne à un sous-domaine de azurewebsites.net. Par exemple, si votre application web est nommée **contoso**, l’URL est **contoso.azurewebsites.net**. Azure assigne également une adresse IP virtuelle.

Pour une application web de production, vous souhaiterez probablement aux utilisateurs de voir un nom de domaine personnalisé. Cet article explique comment faire pour acheter et configurer un domaine personnalisé avec [l’Application de Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). 

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## <a name="overview"></a>Vue d’ensemble

Si vous n’avez pas un nom de domaine pour votre application web, vous pouvez facilement acheter un sur [Azure Portal](https://portal.azure.com/). Au cours du processus d’achat, vous pouvez choisir pour que les enregistrements DNS du domaine racine et WWW mapper automatiquement à votre application web. Vous pouvez également gérer votre droit de domaine à l’intérieur du portail Azure.


Utilisez les étapes suivantes pour acheter des noms de domaine et d’attribuer à votre application web.

1. Dans votre navigateur, ouvrez le [Portail Azure](https://portal.azure.com/).

2. Dans l’onglet **Applications du Web** , cliquez sur le nom de votre application web et sélectionnez **paramètres**puis sélectionnez **Custom domains**

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. De la lame **Custom domains** , cliquez sur **acheter des domaines**.

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. Dans la blade **d’Acheter des domaines** , utilisez la zone de texte pour taper le nom de domaine que vous souhaitez acheter et appuyez sur ENTRÉE. Les domaines disponibles proposées seront affiche juste en dessous de la zone de texte. Sélectionnez le domaine que vous souhaitez acheter. Vous pouvez choisir d’acheter plusieurs domaines à la fois. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. Cliquez sur les **Informations de Contact** et remplir le formulaire d’informations de contact du domaine.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

    > [AZURE.NOTE] Il est très important que vous remplissez tous les champs obligatoires avec autant de précision que possible, en particulier l’adresse de messagerie. En cas d’achat du domaine sans « Protection de confidentialité », vous pouvez être invité à vérifier votre adresse e-mail avant le domaine devient actif. Dans certains cas, des données incorrectes pour les coordonnées engendre d’acheter des domaines. 

6. Désormais, vous pouvez choisir

    un) « renouvellement automatique » votre domaine chaque année
    
    b) Opt de protection de la » confidentialité » qui est incluse dans le prix d’achat en libre (à l’exception des domaines de premier niveau qui a Registre ne prend en charge la confidentialité. Par exemple :. co.in,. co.uk, etc..)  
    
    c) « attribuer des noms d’hôte de la valeur par défaut » pour WWW et racine de domaine pour l’application Web en cours. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
    > [AZURE.NOTE] L’option C configure les liaisons DNS et nom d’hôte automatiquement pour vous.  De cette manière, votre application Web est accessible à l’aide du domaine personnalisé dès l’achat est terminée (baring des retards de propagation DNS dans certains cas). En cas, votre application Web est derrière Azure Traffic Manager, vous verrez pas une option pour affecter le domaine racine, que les enregistrements A ne fonctionnent pas avec le Gestionnaire de trafic. Vous pouvez toujours assigner les domaines/sub-domains acheté auprès d’une application Web à une autre application Web et vice versa. Reportez-vous à l’étape 8 pour plus de détails. 
    
7. Cliquez sur **Sélectionner** sur la blade **d’Acheter les domaines** , les informations d’achat s’affiche sur la lame de **confirmation d’achat** . Si vous acceptez les conditions juridiques, cliquez sur **acheter**, votre commande sera envoyée et vous pouvez surveiller le processus d’achat sur la **Notification**. Achat de domaine peut prendre plusieurs minutes. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. Si vous avez commandé avec succès un domaine, vous pouvez gérer le domaine et l’affecter à votre application web. Cliquez sur le **bouton «... »** situé sur le côté droit de votre domaine. Vous pouvez ensuite **Annuler l’achat** ou le **domaine de la gestion**. Cliquez sur **gérer le domaine**, puis nous pouvons lier **sous-domaine** à notre application web sur la lame de **domaine de gestion** . Si vous souhaitez lier un **sous-domaine** à une application Web différente, puis exécutez cette étape à partir de dans le contexte de l’application Web respectifs. Ici vous un choisir d’affecter le domaine au point de terminaison du trafic manager (si l’application Web est derrière TM) en sélectionnant simplement Traffic manager nom dans le menu déroulant. Ce faisant, domaine/sous-domaine automatiquement recevront à toutes les applications Web derrière ce point de terminaison de Traffic Manager. 

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

    > [AZURE.NOTE] Vous pouvez « annuler achat » sous 5 jours pour un remboursement intégral. Après 5 jours, vous serez à ne pas pouvoir « Annuler acheter », au lieu de cela, vous voyez une option « Supprimer » du domaine. Suppression du domaine entraîne libérer de votre abonnement sans restitution et devient domaine disponible. 

Une fois la configuration terminée, le nom de domaine personnalisé apparaît dans la section des **liaisons de nom d’hôte** de votre application web.

À ce stade, vous devez pouvoir Entrez le nom de domaine personnalisé dans votre navigateur et voir nécessaire avec succès à votre application web.
 
## <a name="what-happens-to-the-custom-domain-you-bought"></a>Que se passe-t-il pour le domaine personnalisé que vous avez acheté

Le domaine personnalisé que vous avez acheté la lame **Custom domains et SSL** est lié à l’abonnement Azure. Comme une ressource d’Azure, ce domaine personnalisé est distinct et indépendant de l’application de Service de l’application qui vous l’a acheté le domaine. Cela signifie que :

- Au sein du portail Azure, vous pouvez utiliser le domaine personnalisé que vous avez acheté de plus d’une application de Service de l’application et pas seulement pour l’application qui vous l’a acheté le domaine personnalisé pour. 
- Vous pouvez gérer tous les domaines personnalisés que vous avez acheté l’abonnement Azure en accédant à la blade **Custom domains et SSL** de *toute* application de Service de l’application de cet abonnement.
- Vous pouvez affecter n’importe quelle application de Service de l’application de l’abonnement Azure même à un sous-domaine dans ce domaine personnalisé.
- Si vous décidez de supprimer une application de Service de l’application, vous pouvez choisir ne pas à supprimer le domaine personnalisé, qu'il est lié à si vous souhaitez conserver l’utiliser pour d’autres applications.

## <a name="if-you-cant-see-the-custom-domain-you-bought"></a>Si vous ne voyez pas le domaine personnalisé que vous avez acheté

Si vous avez acheté le domaine personnalisé à partir de la lame **Custom domains et SSL** , mais ne peut pas voir le domaine personnalisé sous **les domaines gérés**, vérifiez les éléments suivants :

- La création du domaine personnalisé n’auriez pas atteint. Vérifiez la cloche notification en haut du portail Azure pour la progression.
- La création du domaine personnalisé a échoué pour une raison quelconque. Vérifiez la cloche notification en haut du portail Azure pour la progression.
- Le domaine personnalisé peut avoir réussi mais la lame peut être pas encore actualisée. Essayez de rouvrir la lame **Custom domains et SSL** .
- Vous avez peut-être supprimé du domaine personnalisé à un moment donné. Vérifiez les journaux d’audit en cliquant sur **paramètres** > **Les journaux d’Audit** du serveur lame principale de votre application. 
- La lame **Custom domains et SSL** , dans que vous ne cherchez pas peut-être appartenir à une application qui est créée dans un autre abonnement Azure. Basculer vers une autre application dans un autre abonnement et vérifier sa lame **Custom domains et SSL** .  
  Au sein du portail, vous ne pourrez afficher ou gérer les domaines personnalisés créés dans un autre abonnement Azure à l’application. Toutefois, si vous cliquez sur **Gestion avancée** de la lame du **domaine de la gestion** du domaine, vous serez redirigé vers le domaine du site Web fournisseur, où vous pourrez   [configurer manuellement votre domaine personnalisé comme n’importe quel domaine personnalisé externe](web-sites-custom-domain-name.md)  
   pour les applications créées dans un autre abonnement Azure. 


