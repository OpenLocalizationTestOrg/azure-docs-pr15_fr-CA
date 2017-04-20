<properties
    pageTitle="Single sign-on avec le Proxy d’Application | Microsoft Azure"
    description="Explique comment fournir de l’authentification unique à l’aide du Proxy d’Application Azure AD."
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
    ms.date="08/10/2016"
    ms.author="kgremban"/>


# <a name="single-sign-on-with-application-proxy"></a>Single sign-on avec le Proxy d’Application

L’ouverture de session unique est un élément clé du Proxy d’Application AD Azure. Il fournit la meilleure expérience utilisateur avec les étapes suivantes :

1. Un utilisateur s’inscrit sur le nuage  
2. Toutes les validations de sécurité se produisent dans le nuage (la pré-authentification)  
3. Lorsque la demande est envoyée à l’application sur prem, le connecteur de Proxy d’Application emprunte l’identité de l’utilisateur. L’application back-end pense qu’il s’agit d’un utilisateur normal en provenance d’un périphérique à un domaine.

![Diagramme d’accès de l’utilisateur final, par le biais de Proxy de l’Application, au réseau d’entreprise](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Le Proxy d’Application Azure AD vous permet de fournir une expérience d’authentification unique (SSO) pour vos utilisateurs. Suivez les instructions ci-dessous pour publier vos applications à l’aide de l’authentification unique :


## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>Authentification unique pour les applications sur les prem IWA avec le Proxy d’Application à l’aide de KCD
Vous pouvez activer l’ouverture de session unique à vos applications à l’aide de l’authentification Windows (intégrée) en attribuant l’autorisation de connecteurs de Proxy d’Application dans Active Directory pour emprunter l’identité des utilisateurs et d’envoyer et de recevoir des jetons en leur nom.


### <a name="network-diagram"></a>Diagramme de réseau

Ce diagramme explique le flux lorsqu’un utilisateur tente d’accéder à une application sur prem qui utilise IWA.

![Diagramme de flux de l’authentification de Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. L’utilisateur entre l’URL pour accéder à l’application sur prem via le Proxy de l’Application.
2. Proxy de l’application redirige la demande vers les services d’authentification AD Azure pour effectuer la pré-authentification. À ce stade, AD Azure s’applique tout d’authentification applicables et les stratégies d’autorisation, par exemple l’authentification multifactorielle. Si l’utilisateur est validé, Azure AD crée un jeton et l’envoie à l’utilisateur.
3. L’utilisateur passe le jeton à un Proxy d’Application.
4. Proxy de l’application valide le jeton et récupère le nom Principal utilisateur (UPN) et envoie ensuite la demande, le nom UPN et le nom de Principal du Service (SPN) pour le connecteur via un canal sécurisé double authentifié.
5. Le connecteur effectue la négociation de la délégation de contrainte de Kerberos (KCD) avec la prem AD, l’emprunt d’identité de l’utilisateur pour obtenir un jeton Kerberos à l’application.
6. Active Directory envoie le jeton Kerberos pour le connecteur de l’application.
7. Le connecteur envoie la demande d’origine vers le serveur d’application, en utilisant le jeton Kerberos que provenant d’Active Directory.
8. L’application envoie la réponse au connecteur qui est ensuite renvoyé au service du Proxy de l’Application et enfin à l’utilisateur.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer avec l’authentification unique pour le Proxy d’Application, assurez-vous que votre environnement est prêt avec les configurations et les paramètres suivants :

- Vos applications, comme les applications SharePoint Web, sont définies pour utiliser l’authentification intégrée de Windows. Pour plus d’informations consultez [Activer la prise en charge pour l’authentification Kerberos](https://technet.microsoft.com/library/dd759186.aspx)ou pour SharePoint, voir [planifier l’authentification Kerberos dans SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).

- Toutes vos applications ont des noms principaux de Service.

- Le serveur exécutant le connecteur et le serveur qui exécute l’application sont joint au domaine et une partie du même domaine ou de domaines autorisés à approuver. Pour plus d’informations sur la jointure de domaines, consultez [se joindre à un ordinateur à un domaine](https://technet.microsoft.com/library/dd807102.aspx).

- Le serveur exécutant le connecteur a accès en lecture le TokenGroupsGlobalAndUniversal pour les utilisateurs. Il s’agit d’un paramètre par défaut qui peut-être avoir été impacté par l’environnement de renforcement de la sécurité. Obtenir de l’aide plus avec cela dans [KB2009157](https://support.microsoft.com/en-us/kb/2009157).

### <a name="active-directory-configuration"></a>Configuration d’Active Directory

La configuration de Active Directory varie, selon que votre connecteur de Proxy d’Application et le serveur publié sont dans le même domaine ou non.

#### <a name="connector-and-published-server-in-the-same-domain"></a>Connecteur et le serveur publié dans le même domaine

1. Dans Active Directory, accédez à **Outils** > **les utilisateurs et les ordinateurs**.
2. Sélectionnez le serveur qui exécute le connecteur.
3. Avec le bouton droit et sélectionnez **Propriétés** > **délégation**.
4. Sélectionnez **Approuver cet ordinateur pour la délégation aux services spécifiés** et sous **les Services auxquels ce compte peut présenter des informations d’identification déléguées**, ajoutez la valeur de l’identité de nom principal de service du serveur d’application.
5. Ainsi, le connecteur de Proxy d’Application emprunter l’identité des utilisateurs dans Active Directory par rapport à des applications définies dans la liste.

![Capture d’écran de la fenêtre des propriétés de connecteur-SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>Connecteur et le serveur publié dans des domaines différents

1. Pour obtenir la liste des composants requis pour l’utilisation avec KCD sur plusieurs domaines, consultez [La délégation contrainte Kerberos sur plusieurs domaines](https://technet.microsoft.com/library/hh831477.aspx).
2. Windows 2012 R2, vous devez utiliser le `principalsallowedtodelegateto` propriété sur le serveur de connecteur pour activer le Proxy d’Application de délégué pour le serveur de connecteur, où le serveur publié est `sharepointserviceaccount` et la délégation du serveur est `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount`peut être le compte d’ordinateur de secours ou un compte de service sous lequel le pool d’applications SPS est en cours d’exécution.


### <a name="azure-classic-portal-configuration"></a>Configuration du portail Azure classique

1. Publier votre application en suivant les instructions décrites dans les [applications de publication avec le Proxy de l’Application](active-directory-application-proxy-publish.md). Veillez à sélectionner **Azure Active Directory** en tant que la **Méthode de la pré-authentification**.
2. Lorsque votre application s’affiche dans la liste des applications, sélectionnez-le, puis cliquez sur **configurer**.
3. Sous **Propriétés**, définir la **Méthode d’authentification interne** pour **L’authentification Windows intégrée**.  
  ![Configuration de l’Application avancée](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Entrez le **Nom principal de service Application interne** du serveur d’application. Dans cet exemple, le nom principal de service pour notre application publiée est http/lob.contoso.com.  

>[AZURE.IMPORTANT] Si votre UPN sur site et l’UPN dans Azure Active Directory ne sont pas identiques, vous devrez configurer la [délégation d’identité de connexion](#delegated-login-identity) afin que la pré-authentification de travailler.

|  |  |
| --- | --- |
| Méthode d’authentification interne | Si vous utilisez Active Directory Azure pour la pré-authentification, vous pouvez définir la méthode d’authentification interne pour permettre aux utilisateurs de bénéficier de l’authentification unique (SSO) pour cette application. <br><br> Sélectionnez **L’authentification Windows intégrée** (IWA) si votre application utilise IWA et vous pouvez configurer la délégation de contrainte de Kerberos (KCD) pour activer l’authentification unique pour cette application. Les applications qui utilisent IWA doivent être configurées à l’aide de KCD, sinon le Proxy d’Application ne sera pas en mesure de publier ces applications. <br><br> Sélectionnez **Aucun** si votre application n’utilise pas IWA. |
| Application interne SPN | Il s’agit du nom de Service Principal (SPN) de l’application interne tel que configuré dans l’annonce Azure sur prem. Le nom principal de service est utilisé par le connecteur de Proxy d’Application pour extraire des jetons Kerberos pour l’application à l’aide de KCD. |


## <a name="sso-for-non-windows-apps"></a>Authentification unique pour les applications non Windows
Le flux de la délégation Kerberos dans le Proxy d’Application Azure AD démarre lorsque AD Azure authentifie l’utilisateur dans le nuage. Une fois la demande arrive sur site, le connecteur Active Directory Application Proxy Azure émet un ticket Kerberos pour le compte de l’utilisateur en interagissant avec l’annuaire Active Directory local. Ce processus est connu comme Kerberos contrainte délégation (KCD). Dans la phase suivante, une demande est envoyée à l’application back-end avec ce ticket Kerberos. Il existe un certain nombre de protocoles qui définissent comment envoyer ces demandes. La plupart des serveurs non Windows s’attendent à négocier/SPNego est maintenant pris en charge par le Proxy d’Application Azure AD.

![Diagramme de l’authentification unique de Windows non](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>Identité de connexion de délégués
Identité de connexion délégué vous permet de gérer deux scénarios de connexion différents :

- Les applications non Windows qui voient généralement d’identité de l’utilisateur sous la forme d’un nom d’utilisateur ou un nom de compte SAM, pas une adresse de messagerie (username@domain).
- Les configurations de connexion secondaire où l’UPN dans Azure AD et le nom d’utilisateur principal sur site Active Directory différent.

Proxy de l’Application, vous pouvez sélectionner quelle identité permet d’obtenir le ticket Kerberos. Ce paramètre est par application. Certaines de ces options ne conviennent pas pour les systèmes qui n’acceptent pas le format d’adresse e-mail, d’autres sont conçus pour une autre connexion.

![Capture d’écran de paramètre identité connexion déléguée](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Si l’identité de connexion déléguée est utilisée, il se peut que la valeur ne peut pas être unique pour tous les domaines ou les forêts dans votre organisation. Vous pouvez éviter ce problème en publiant ces applications à l’aide des deux groupes différents de connecteur. Dans la mesure où chaque application possède un public différent, vous pouvez joindre ses connecteurs vers un autre domaine.


## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Utilisation de l’authentification unique lorsque locaux et cloud identités ne sont pas identiques
Sauf dans le cas contraire elle est configurée, le Proxy d’Application suppose que les utilisateurs ont exactement la même identité dans le nuage et sur site. Vous pouvez configurer, pour chaque application, quelle identité doit être utilisée lors de l’exécution de l’authentification unique.  

Cette fonctionnalité permet à de nombreuses organisations ayant différentes identités sur prem et nuage de devoir SSO du nuage-prem sur les applications sans demander aux utilisateurs d’entrer des mots de passe et noms d’utilisateur différents. Cela inclut les organisations qui :

- Disposent de plusieurs domaines en interne (joe@us.contoso.com, joe@eu.contoso.com) et d’un domaine unique dans le nuage (joe@contoso.com).

- Est le nom de domaine non routables en interne (joe@contoso.usa) et une valeur juridique dans le nuage.

- N’utilisez pas de noms de domaine en interne (Jean)

- Utilisez des alias différents-prem et dans le cloud. Par exemple joe-johns@contoso.comVS.joej@contoso.com  

Il permet également aux applications qui n’acceptent pas les adresses sous la forme d’une adresse de messagerie, ce qui est très fréquent que des serveurs non Windows principaux.


### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Définition de l’authentification unique pour les différentes identités de nuage et sur prem

1. Configurer les paramètres d’Azure AD connecter l’identité principale est donc l’adresse de messagerie (mail). Pour ce faire dans le cadre du processus de personnalisation, la modification du champ **Nom de principe de l’utilisateur** dans les paramètres de synchronisation. Notez que ces paramètres déterminent également comment les utilisateurs se connecteront à Office 365, les appareils de Windows10 et d’autres applications qui utilisent Active Directory Azure comme leur banque d’identités.  
  ![Capture d’écran Identification utilisateurs - liste déroulante le nom d’utilisateur Principal](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Dans les paramètres de Configuration de l’Application pour l’application que vous souhaitez modifier, sélectionnez l' **Identité de connexion déléguée** à utiliser :
  - Nom d’utilisateur principe :joe@contoso.com  
  - Autre principe nom d’utilisateur :joed@contoso.local  
  - Partie de nom d’utilisateur nom d’utilisateur principe : joe  
  - Partie de nom d’utilisateur autre principe du nom d’utilisateur : joed  
  - Nom de compte SAM sur site : configuration du contrôleur de domaine en fonction de prem

  ![Capture d’écran du menu déroulant connexion déléguée identité](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Résolution des problèmes d’authentification unique pour les différentes identités
S’il existe une erreur dans le processus d’authentification unique s’affiche dans le journal des événements connecteur machine comme expliqué dans la [résolution des problèmes](active-directory-application-proxy-troubleshoot.md).
Mais, dans certains cas, la demande sera correctement envoyée à l’application principale alors que cette application sera envoyé dans différents autres réponses HTTP. Résolution des problèmes liés à ces cas doivent commencer en examinant le numéro d’événement 24029 sur la machine de connecteur dans le journal d’événements de session Proxy de l’Application. L’identité de l’utilisateur qui a été utilisée pour la délégation s’affiche dans le champ « utilisateur » dans les détails de l’événement. Pour activer le journal de la session, sélectionnez **Afficher analytique et les journaux de débogage** dans le menu permet d’afficher l’Observateur d’événements.


## <a name="see-also"></a>Voir aussi

- [Publier des applications avec le Proxy de l’Application](active-directory-application-proxy-publish.md)
- [Résoudre les problèmes que vous rencontrez avec le Proxy de l’Application](active-directory-application-proxy-troubleshoot.md)
- [Utilisation d’applications prenant en charge des sinistres](active-directory-application-proxy-claims-aware-apps.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg
