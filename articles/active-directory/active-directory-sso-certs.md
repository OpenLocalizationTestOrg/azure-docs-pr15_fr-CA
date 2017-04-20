<properties
    pageTitle="Comment faire pour gérer les certificats de la fédération dans Azure annonce | Microsoft Azure"
    description="Apprenez comment personnaliser la date d’expiration pour les certificats de votre fédération et renouveler des certificats qui va bientôt expirer."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestion des certificats pour fédéré de l’authentification unique dans Azure Active Directory

Cet article répond aux questions liées aux certificats qu’Azure Active Directory crée pour établir fédéré de session unique (SSO) à vos applications SaaS.

Cet article ne s’applique aux applications qui sont configurées pour utiliser **Azure AD Single Sign-On**, comme illustré dans l’exemple ci-dessous :

![Azure AD unique Single Sign-On](./media/active-directory-sso-certs/fed-sso.PNG)

##<a name="how-to-customize-the-expiration-date-for-your-federation-certificate"></a>Comment personnaliser la Date d’Expiration de votre certificat de fédération

Par défaut, les certificats sont configurés pour expirer après deux ans. Vous pouvez choisir une autre date d’expiration de votre certificat en suivant les étapes ci-dessous. Les captures d’écran inclus utilisent la force de vente pour cet exemple, mais ces étapes peuvent s’appliquent à n’importe quelle application SaaS fédérée.

1. Dans Azure Active Directory, sur la page de démarrage rapide pour votre application, cliquez sur dans la zone **de configurer l’authentification unique**.

    ![Ouvrez l’Assistant de configuration de l’authentification unique.](./media/active-directory-sso-certs/config-sso.png)

2. Sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

3. Tapez l' **URL de connexion** de votre application et sélectionnez la case à cocher pour **configurer le certificat utilisé pour fédéré de l’authentification unique**. Puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On](./media/active-directory-sso-certs/new-app-config-sso.PNG)

4. Sur la page suivante, sélectionnez **Générer un nouveau certificat**, puis sélectionnez la durée pendant laquelle vous souhaitez que le certificat doit être valide pour. Puis cliquez sur **suivant**.

    ![Générer un nouveau certificat](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Ensuite, cliquez sur **Télécharger le certificat**. Pour savoir comment télécharger le certificat à votre application de SaaS particulier, cliquez sur **Afficher les instructions de configuration**.

    ![Téléchargez, puis télécharger le certificat](./media/active-directory-sso-certs/new-app-config-app.PNG)

6. Le certificat est désactivé jusqu'à ce que vous sélectionnez la case à cocher de confirmation en bas de la boîte de dialogue et que vous appuyez sur Soumettre.

##<a name="how-to-renew-a-certificate-that-will-soon-expire"></a>Comment renouveler un certificat qui va bientôt expirer

La procédure de réinscription ci-dessous doit idéalement entraîner aucun temps d’arrêt important pour vos utilisateurs. Les captures d’écran utilisé dans cette fonctionnalité section force de vente comme exemple, mais ces étapes peuvent appliquer à n’importe quelle application SaaS fédérée.

1. Dans Azure Active Directory, sur la page de démarrage rapide pour votre application, cliquez sur **Configuration de l’authentification unique**.

    ![Ouvrir l’Assistant de configuration de l’authentification unique](./media/active-directory-sso-certs/renew-sso-button.PNG)

2. Sur la première page de la boîte de dialogue, **Azure AD Single Sign-On** doit déjà être sélectionnée, cliquez sur **suivant**.

3. Sur la deuxième page, sélectionnez la case à cocher pour **configurer le certificat utilisé pour fédéré de l’authentification unique**. Puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On](./media/active-directory-sso-certs/renew-config-sso.PNG)

4. Sur la page suivante, sélectionnez **Générer un nouveau certificat**, puis sélectionnez la durée pendant laquelle vous souhaitez que le nouveau certificat soit valide pour. Puis cliquez sur **suivant**.

    ![Générer un nouveau certificat](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Cliquez sur **Télécharger le certificat**. À rewnew avec succès votre certificat, vous devez effectuer les deux étapes suivantes :

    - Téléchargez le nouveau certificat à l’écran de configuration d’ouverture de session unique de l’application SaaS. Pour savoir comment procéder pour votre application SaaS particulier, cliquez sur **Afficher les instructions de configuration**.

    - Dans Azure AD, activez la case à cocher de confirmation en bas de la boîte de dialogue pour activer le nouveau certificat, puis cliquez sur **suivant** pour soumettre.

    > [AZURE.IMPORTANT] Ouverture de session unique pour l’application sera désactivé au moment où l’une de ces deux étapes est terminé, mais il est de nouveau activé une fois la deuxième étape est terminée. Par conséquent, pour minimiser les interruptions, préparez réaliser les deux étapes dans un court laps de temps entre eux.

    ![Téléchargez, puis télécharger le certificat](./media/active-directory-sso-certs/renew-config-app.PNG)

## <a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Accès aux applications et à l’ouverture de session unique avec Active Directory de Azure](active-directory-appssoaccess-whatis.md)
- [Résolution des problèmes de base SAML Single Sign-On](active-directory-saml-debugging.md)
