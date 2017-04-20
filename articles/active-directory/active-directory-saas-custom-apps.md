<properties 
    pageTitle="Configuration de l’authentification unique pour les applications qui ne sont pas dans la galerie des applications Azure Active Directory | Microsoft Azure" 
    description="Découvrez comment connecter des applications Azure Active Directory à l’aide de SAML et fonctionnalité SSO basée sur le mot de passe pour le libre-service" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configuration de l’authentification unique pour les applications qui ne sont pas dans la galerie des applications Azure Active Directory

Cet article est une fonctionnalité qui permet aux administrateurs de configurer de l’authentification unique pour applications ne figurent pas dans l’Active Directory de Azure app galerie *sans écrire de code*. Cette fonctionnalité a été publiée à partir de la version bêta sur le 18 novembre 2015 et est incluse dans [Azure Active Directory prime](active-directory-editions.md). Si vous cherchez à la place les instructions destinées aux développeurs sur l’intégration d’applications personnalisées avec Azure AD par le biais de code, consultez [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

La galerie d’applications Azure Active Directory fournit une liste des applications qui prennent en charge un formulaire d’ouverture de session unique avec Azure Active Directory, comme décrit dans [cet article](active-directory-appssoaccess-whatis.md). Une fois que vous (en tant qu’informatique spécialiste ou système intégrateur de votre organisation) avez trouvé l’application que vous souhaitez vous connecter, vous pouvez commencer par suivre les instructions pas à pas présentées dans le portail de gestion Azure pour activer l’ouverture de session unique.

Les clients avec des licences [d’Azure Active Directory prime](active-directory-editions.md) obtiennent également ces fonctionnalités supplémentaires :

* Intégration de libre-service de n’importe quelle application qui prend en charge les fournisseurs d’identité SAML 2.0 (exécutée par le SP ou exécutée par IdP)
* Libre service d’intégration d’une application web qui a une basé sur HTML-page de connexion à l’aide de la [fonctionnalité SSO basée sur le mot de passe](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Libre service de connexion des applications qui utilisent le protocole SCIM pour l’utilisateur mise en service ([décrite ici](active-directory-scim-provisioning.md))
* Possibilité d’ajouter des liens vers toutes les applications du [Lanceur d’applications Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d’accès AD Azure](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Cela peut inclure non seulement les applications SaaS que vous utilisez, mais n'avez pas encore été sur-arraisonné à la galerie d’application Azure AD, mais les applications web de tiers que votre organisation a déployé sur les serveurs de que contrôle, soit dans le nuage ou sur site.

Ces fonctions, également connu sous le nom de *modèles d’intégration de app*, fournissent des points de connexion basée sur des normes pour les applications qui prennent en charge l’authentification basée sur les formulaires, SCIM ou SAML et incluent des options flexibles et les paramètres de compatibilité avec un grand nombre d’applications. 

##<a name="adding-an-unlisted-application"></a>Ajout d’une application non listée

Pour connecter une application à l’aide d’un modèle d’intégration de app, connectez-vous au portail de gestion Azure à l’aide de votre compte d’administrateur Active Directory de Azure et accédez à la **Active Directory > [répertoire] > Applications** , sélectionnez **Ajouter**, puis sur **Ajouter une application à partir de la galerie**. 

![][1]

Dans la galerie d’application, vous pouvez ajouter une application non listée à l’aide de la catégorie **personnalisée** sur la gauche, ou en sélectionnant le lien **Ajouter une application non listée** apparaissant dans les résultats de la recherche si votre application souhaitée n’a pas été trouvée. Après avoir entré un nom pour votre application, vous pouvez configurer les options d’ouverture de session unique et le comportement. 

**Astuce**: pour obtenir les meilleurs résultats, utilisez la fonction de recherche pour vérifier si l’application existe déjà dans la bibliothèque de l’application. Si l’application est trouvée et sa description mentionne « single sign-on », puis l’application sont déjà prise en charge fédéré de l’authentification unique. 

![][2]

Ajout d’une application de cette façon offre une expérience très similaire à celui qui est disponible pour les applications déjà intégrées. Pour commencer, sélectionnez la **Configuration de l’authentification unique**. L’écran suivant présente les trois options suivantes pour configurer l’authentification unique, qui sont décrites dans les sections suivantes.

![][3]


##<a name="azure-ad-single-sign-on"></a>Azure AD unique Single Sign-On

Sélectionnez cette option pour configurer l’authentification basée sur les SAML pour l’application. Cela nécessite que la prise en charge d’application SAML 2.0 et recueillent des informations sur la façon d’utiliser les capacités SAML de l’application avant de poursuivre. Après avoir sélectionné **suivant**, un message vous demandera d’entrer les trois différentes URL, correspondant aux points de terminaison SAML pour l’application. 

![][4]
 
Il s’agit :

* **URL de connexion (exécutée par le SP uniquement)** – où l’utilisateur va se connecter à cette application. Si l’application est configurée pour effectuer une authentification unique par le fournisseur de service sur, puis lorsqu’un utilisateur accède à cette URL, le fournisseur de service fera la redirection nécessaire pour AD Azure pour authentifier et de l’utilisateur dans une session. Si ce champ est renseigné, AD Azure utilisent cette URL pour lancer l’application à partir d’Office 365 et le panneau AD Azure. Si ce champ est ommited, alors qu’Active Directory Azure va effectuer fournisseur d’identité-initiée par ouverture de session lorsque l’application est lancée à partir d’Office 365, le panneau AD Azure, ou la publicité Azure unique session URL (copiable à partir de l’onglet de tableau de bord).

* **URL de l’émetteur** - l’émetteur URL doit identifier de manière unique l’application pour la connexion unique sur est en cours de configuration. Il s’agit de la valeur AD Azure envoie à l’application en tant que paramètre de **l’Audience** du jeton SAML, et l’application est prévue pour le valider. Cette valeur s’affiche également en tant que l' **ID d’entité** dans les métadonnées SAML fournie par l’application. Vérifiez la documentation d’application SAML pour plus d’informations sur ce qu’il s’agit de l’ID d’entité ou de la valeur public est. Voici un exemple de l’aspect de l’URL de l’Audience du jeton SAML renvoyée à l’application :

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **URL de la réponse** - l’URL de réponse est où l’application attend de recevoir le jeton SAML. Cela est également appelé l' **URL du Service de consommateur d’Assertion (ACS)**. Consultez la documentation d’application SAML pour plus d’informations sur les nouveautés de sa réponse jeton SAML URL ou ACS.
 Une fois que celles-ci ont été saisis, cliquez sur **suivant** pour passer à l’écran suivant. Cet écran fournit des informations sur ce qui doit être configuré sur le côté de l’application pour lui permettre d’accepter un jeton SAML depuis Active Directory Azure. 

![][5]

Les valeurs qui sont requises seront varient en fonction de l’application, reportez-vous à la documentation d’application SAML pour plus de détails. L' **Ouverture de session** et **déconnexion** URL de les résoudre au même point de terminaison, qui est le point de terminaison de gestion des demandes SAML pour votre instance d’Azure annonce du service. L’URL de l’émetteur est la valeur qui apparaît comme le « émetteur » à l’intérieur du jeton SAML émise à l’application. 

Une fois que votre application a été configurée, cliquez sur bouton **suivant** , puis la **Terminer** pour fermer la boîte de dialogue. 

##<a name="assigning-users-and-groups-to-your-saml-application"></a>Affectation d’utilisateurs et des groupes à votre application SAML 

Une fois que votre application a été configurée pour utiliser AD Azure comme un fournisseur d’identité basée sur SAML, il est presque prêt à tester. Sous la forme d’un contrôle de sécurité, Azure AD ne génère un jeton leur permettant de se connecter à l’application, sauf si elles ont accès à l’aide d’Active Directory Azure. Les utilisateurs peuvent accéder directement ou via un groupe qu’ils sont un membre de. 

Pour affecter un utilisateur ou un groupe à votre application, cliquez sur le bouton **Affecter des utilisateurs** . Sélectionnez l’utilisateur ou le groupe que vous souhaitez affecter, puis sélectionnez le bouton **attribuer** . 

![][6]

Affectation d’un utilisateur permettra d’Azure AD émettre un jeton pour l’utilisateur, ainsi qu’à l’origine d’une mosaïque de cette application s’affiche dans le panneau d’accès de l’utilisateur. Une mosaïque de l’application s’affiche également dans le Lanceur d’applications Office 365, si l’utilisateur est à l’aide d’Office 365. 

Vous pouvez charger un logo de mosaïque pour l’application en utilisant le bouton **Charger un Logo** sous l’onglet **configuration** de l’application. 

###<a name="customizing-the-claims-issued-in-the-saml-token"></a>Personnaliser les affirmations émises dans le jeton SAML 

Lorsqu’un utilisateur s’authentifie à l’application, AD Azure émet un jeton SAML à l’application qui contient des informations (ou demandes) sur l’utilisateur qui identifie de façon unique les. Par défaut cela inclut le nom d’utilisateur, adresse électronique, prénom et nom de l’utilisateur. 

Vous pouvez afficher ou modifier les revendications envoyées dans le jeton SAML à l’application sous l’onglet **attributs** . 

![][7]

Deux raisons peuvent expliquer pourquoi vous devrez peut-être modifier les affirmations émises dans le jeton SAML : •la application a été écrite pour exiger un ensemble différent de l’URI de demande ou des valeurs de déclaration d’application de •Your a été déployée d’une manière qui nécessite le NameIdentifier revendications autre chose que le nom d’utilisateur (UE nom utilisateur principal) stockée dans Active Directory de Azure. 

Pour plus d’informations sur la façon d’ajouter et de modifier des demandes de remboursement pour ces scénarios, consultez cet [article sur la personnalisation de revendications](active-directory-saml-claims-customization.md). 

###<a name="testing-the-saml-application"></a>Test de l’application SAML 

Après ont configuré la SAML URL et le certificat dans Active Directory Azure et dans l’application, les utilisateurs ou les groupes ont été affectés à l’application dans Azure, les revendications ont été revues et modifiées si nécessaire, puis l’utilisateur est prêt à vous connecter à l’application. 

Pour tester, simplement vous connecter à la publicité Azure accès au panneau de https://myapps.microsoft.com à l’aide d’un compte d’utilisateur que vous avez affecté à l’application, puis cliquez sur dans la fenêtre de l’application lancer le processus d’ouverture de session unique. Vous pouvez également parcourir directement à l’URL de session pour l’application et la connexion à partir de là. 

Conseils de débogage, consultez cet [article sur le débogage basée sur SAML SSO pour les applications](active-directory-saml-debugging.md) 

##<a name="password-single-sign-on"></a>Mot de passe de session unique

Sélectionnez cette option pour configurer le [mot de passe de l’authentification unique](active-directory-appssoaccess-whatis.md) pour une application web qui a une page de connexion HTML. Authentification par mot de passe, également appelé mise en chambre forte, un mot de passe vous permet de gérer l’accès des utilisateurs et des mots de passe pour les applications web qui ne prennent pas en charge la fédération d’identité. Il est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, comme aux comptes d’application média social de votre organisation. 

Après avoir sélectionné **suivant**, un message vous demandera d’entrer l’URL de sur le web-page de connexion l’application. Notez que cela doit être la page qui contient les champs d’entrée de nom d’utilisateur et le mot de passe. Une fois saisie, AD Azure démarre un processus pour analyser la page de connexion pour un nom d’utilisateur d’entrée et d’un mot de passe d’entrée. Si le processus n’a pas réussi, puis il vous guide à travers un autre processus d’installation d’une extension du navigateur (nécessite Internet Explorer, Chrome ou Firefox) qui vous permet de capturer manuellement les champs.

Une fois que la page de connexion est capturée, les utilisateurs et les groupes peuvent être affectés et les stratégies d’informations d’identification peuvent être définies comme normale [applications d’authentification de mot de passe](active-directory-appssoaccess-whatis.md).

Remarque : Vous pouvez charger un logo de mosaïque pour l’application en utilisant le bouton **Charger un Logo** sous l’onglet **configuration** de l’application. 

##<a name="existing-single-sign-on"></a>Existant de l’authentification unique

Sélectionnez cette option pour ajouter un lien vers une application de portail de panneau Azure AD ou Office 365 de votre entreprise. Vous pouvez utiliser cela pour ajouter des liens vers des applications web personnalisées qui utilisent actuellement Azure des Services de fédération Active Directory (ou un autre service de fédération) au lieu d’Azure AD pour l’authentification. Ou bien, vous pouvez ajouter des liens de profondes pour des pages SharePoint spécifiques ou d’autres pages web que vous souhaitez qu’apparaissent dans les panneaux d’accès de l’utilisateur. 

Après avoir sélectionné **suivant**, un message vous demandera d’entrer l’URL de l’application à lier à. Une fois terminé, les utilisateurs et les groupes peuvent être affectés à l’application, ce qui entraîne l’affichage de l’application dans le [Lanceur d’applications Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d’accès AD Azure](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) pour ces utilisateurs.

Remarque : Vous pouvez charger un logo de mosaïque pour l’application en utilisant le bouton **Charger un Logo** sous l’onglet **configuration** de l’application.

## <a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Comment personnaliser les affirmations émises dans le jeton SAML pour des applications](active-directory-saml-claims-customization.md)
- [Résolution des problèmes de base SAML Single Sign-On](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
