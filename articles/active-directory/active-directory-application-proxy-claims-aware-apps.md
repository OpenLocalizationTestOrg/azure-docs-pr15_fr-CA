<properties
    pageTitle="Travailler avec les déclarations des applications prenant en charge dans le Proxy d’Application"
    description="Explique comment devenir opérationnel avec le Proxy d’Application Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>



# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Travailler avec les déclarations des applications prenant en charge dans le Proxy d’Application

Applications prenant en charge des demandes d’effectuer une redirection vers le Service STS (Security Token), qui à son tour demande les informations d’identification de l’utilisateur en échange d’un jeton avant de rediriger l’utilisateur vers l’application. Pour activer le Proxy d’Application travailler avec ces redirections, les étapes suivantes doivent être prises.

## <a name="prerequisites"></a>Conditions préalables
Avant d’effectuer cette procédure, assurez-vous que le STS effectue une redirection vers l’application de connaissance de créances est disponible en dehors de votre réseau local.

## <a name="azure-classic-portal-configuration"></a>Configuration du portail Azure classique

1. Publier votre application en suivant les instructions décrites dans les [applications de publication avec le Proxy de l’Application](active-directory-application-proxy-publish.md).
2. Dans la liste des applications, sélectionnez l’application de connaissance de créances et cliquez sur **configurer**.
3. Si vous avez choisi de **relais** que votre **Méthode de la pré-authentification**, veillez à sélectionner **HTTPS** en tant que votre schéma **d’URL externe** .
4. Si vous avez choisi **d’Azure Active Directory** comme **Méthode de la pré-authentification**, sélectionnez **Aucun** comme **Méthode d’authentification interne**.


## <a name="adfs-configuration"></a>Configuration d’ADFS

1. Ouvrez Gestion d’ADFS.
2. Accédez aux **Approbations des parties de confiance**, cliquez avec le bouton droit sur l’application que vous publiez avec le Proxy de l’Application, puis choisissez **Propriétés**.  
  ![Approbations des parties de confiance cliquez droit sur le nom de l’application - screentshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. Sous l’onglet **points de terminaison** , sous **type de point de terminaison**, sélectionnez **WS-Federation**.
4. Sous **URL de confiance** , tapez l’URL que vous avez saisi dans le Proxy d’Application sous **l’URL externe** , puis cliquez sur **OK**.  
  ![Ajouter un point de terminaison - la valeur URL de confiance - capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>Voir aussi

- [Publier des applications avec le Proxy de l’Application](active-directory-application-proxy-publish.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Résoudre les problèmes que vous rencontrez avec le Proxy de l’Application](active-directory-application-proxy-troubleshoot.md)
- [Activer les applications client natif d’interagir avec les applications de proxy](active-directory-application-proxy-native-client.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
