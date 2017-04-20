<properties
    pageTitle="Résoudre les problèmes de Proxy d’Application | Microsoft Azure"
    description="Explique comment résoudre les erreurs dans le Proxy d’Application Azure AD."
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
    ms.date="08/19/2016"
    ms.author="kgremban"/>



# <a name="troubleshoot-application-proxy"></a>Résoudre les problèmes de Proxy de l’Application

Si les erreurs se produisent lors de l’accès d’une application publiée ou de publier des applications, vérifiez les options suivantes pour voir si le Proxy d’Application Microsoft Azure AD fonctionne correctement :

- Ouvrez la console Services Windows et vérifiez que le service du **Connecteur de Microsoft DAS Application Proxy** est activé et en cours d’exécution. Vous pouvez également consulter la page de propriétés de service Proxy de l’Application, comme illustré dans l’image suivante :  
  ![Capture d’écran de la fenêtre Propriétés de connecteur Microsoft Proxy de l’Application DAS](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Ouvrez l’Observateur d’événements et recherchez les événements de connecteur de Proxy de l’Application dans les **Applications et les journaux des Services** > **Microsoft** > **AadApplicationProxy** > **connecteur** > **Admin**.
- Si nécessaire, des journaux plus détaillés sont disponibles en activant l’analytique et les journaux de débogage et en activant le journal de session Proxy de l’Application connecteur.

## <a name="the-page-is-not-rendered-correctly"></a>La page ne s’affiche pas correctement

Si vous n’obtenez pas un message d’erreur spécifique, vous ait des problèmes avec votre application rendu ou ne fonctionne pas correctement. Cela peut se produire si vous avez publié le chemin d’accès de l’article, mais l’application requiert le contenu qui existe à l’extérieur de ce chemin d’accès.

Par exemple, si vous publiez la https://yourapp/app chemin d’accès, mais l’application appelle les images dans https://yourapp/media, ils ne sont pas restitués. Assurez-vous que vous publiez l’application en utilisant le chemin d’accès au niveau le plus élevé, que vous devez inclure tous les contenus pertinents. Dans cet exemple, il serait http://yourapp/.

Si vous modifiez le chemin pour inclure le contenu référencé, mais devez les utilisateurs sur un lien plus profond dans le chemin d’accès, consultez le blog [définissant le lien droit pour les applications de Proxy de l’Application dans le panneau d’accès AD Azure et Office 365 Lanceur d’applications](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="general-errors"></a>Erreurs générales

Erreur | Description | Résolution
--- | --- | ---
Cette application d’entreprise ne sont pas accessibles. Vous n’êtes pas autorisé à accéder à cette application. Échoué de l’autorisation. Veillez à attribuer à l’utilisateur d’accéder à cette application. | Vous n’avez ne peut-être pas affecté l’utilisateur pour cette application. | Accédez à l’onglet **applications** , sous **utilisateurs et groupes**, affecter cet utilisateur ou ce groupe d’utilisateurs à cette application.
Cette application d’entreprise ne sont pas accessibles. Vous n’êtes pas autorisé à accéder à cette application. Échoué de l’autorisation. Assurez-vous que l’utilisateur possède une licence pour Azure Active Directory Premium ou Basic. | Les utilisateurs peuvent obtenir cette erreur lorsque vous tentez d’accéder à l’application que vous avez publié si elles n’ont pas été explicitement affectées avec une licence de base/prime par l’administrateur de l’abonné. | Cliquez sur onglet de Active Directory, **des licences** de l’abonné et de s’assurer que cet utilisateur ou ce groupe d’utilisateurs est affecté une prime ou une licence de base.


## <a name="connector-troubleshooting"></a>Dépannage du connecteur
Si l’enregistrement échoue au cours de l’installation de l’Assistant Connecteur, il existe deux manières d’afficher la raison de l’échec. Recherchez dans le journal des événements **Applications et Services Logs\Microsoft\AadApplicationProxy\Connector\Admin**, soit exécuter la commande Windows PowerShell suivante.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Erreur | Description | Résolution |
| --- | --- | --- |
| Échoué de l’enregistrement du connecteur : Assurez-vous que vous avez activé le Proxy d’Application dans le portail de gestion Azure et que vous avez entré votre nom d’utilisateur Active Directory et le mot de passe correctement. Erreur : « une ou plusieurs erreurs se sont produites. » | Vous avez fermé la fenêtre enregistrement sans effectuer de connexion d’accès à Active Directory Azure. | Exécutez de nouveau l’Assistant Connecteur et inscrire le connecteur. |
| Échoué de l’enregistrement du connecteur : Assurez-vous que vous avez activé le Proxy d’Application dans le portail de gestion Azure et que vous avez entré votre nom d’utilisateur Active Directory et le mot de passe correctement. Erreur : ' AADSTS50001 : ressource `https://proxy.cloudwebappproxy.net /registerapp` est désactivé. » | Proxy de l’application est désactivé.  | Veillez à qu'activer le Proxy d’Application dans Azure portal classique avant d’essayer d’inscrire le connecteur. Pour plus d’informations sur l’activation du Proxy d’Application, reportez-vous à la section [Activer le Proxy d’Application services](active-directory-application-proxy-enable.md). |
| Échoué de l’enregistrement du connecteur : Assurez-vous que vous avez activé le Proxy d’Application dans le portail de gestion Azure et que vous avez entré votre nom d’utilisateur Active Directory et le mot de passe correctement. Erreur : « une ou plusieurs erreurs se sont produites. » | Si la fenêtre d’enregistrement s’ouvre puis se ferme immédiatement sans vous permettant de vous connecter, vous obtiendrez probablement cette erreur. Cette erreur se produit lorsqu’il existe une erreur de réseau sur votre système. | Assurez-vous qu’il est possible de se connecter à partir d’un navigateur sur un site Web public et que les ports sont ouverts tel que spécifié dans les [conditions préalables du Proxy de l’Application](active-directory-application-proxy-enable.md). |
| Échoué de l’enregistrement du connecteur : Assurez-vous que votre ordinateur est connecté à Internet. Erreur : ' s’est produite à l’écoute sur aucun point de terminaison `https://connector.msappproxy.net :9090/register/RegisterConnector` qui peut accepter le message. Ceci est souvent dû à une adresse incorrecte ou une action SOAP. Consultez InnerException, le cas échéant, pour plus de détails. » | Si vous vous connecter en utilisant votre nom d’utilisateur d’AD Azure et d’un mot de passe puis recevez cette erreur, il peut être que tous les ports au-dessus 8081 sont bloqués. | Assurez-vous que les ports nécessaires sont ouverts. Pour plus d’informations, consultez [conditions préalables du Proxy de l’Application](active-directory-application-proxy-enable.md). |
| Erreur clair est présenté dans la fenêtre d’enregistrement. Ne peut pas continuer – uniquement pour fermer la fenêtre. | Vous avez entré le mot de passe ou un nom d’utilisateur incorrect. | Réessayez. |
| Échoué de l’enregistrement du connecteur : Assurez-vous que vous avez activé le Proxy d’Application dans le portail de gestion Azure et que vous avez entré votre nom d’utilisateur Active Directory et le mot de passe correctement. Erreur : ' AADSTS50059 : aucune information d’identification de clients trouvée dans la demande ou implicite par les informations d’identification fournies et recherche en principe de service URI a échoué. | Vous tentez d’ouvrir une session en utilisant un Account Microsoft et non à un domaine qui fait partie de l’ID de l’organisation de l’annuaire que vous essayez d’accéder. | Assurez-vous que l’administrateur fait partie du même nom de domaine que le domaine du client, par exemple, si le domaine AD Azure est contoso.com, l’administrateur doit être admin@contoso.com. |
| Impossible de récupérer la stratégie en cours d’exécution pour l’exécution de scripts PowerShell. | En cas d’échec de l’installation du connecteur, vérifiez que la stratégie d’exécution PowerShell n’est pas désactivé. | Ouvrez l’éditeur de stratégie de groupe. Accédez à **Configuration de l’ordinateur** > **Les modèles d’administration** > **Composants Windows** > **De Windows PowerShell** , puis double-cliquez sur **Activer l’exécution du Script**. Cette option peut être définie à **Non configuré** ou **activé**. Si **activée**, assurez-vous que sous Options, la stratégie d’exécution est définie soit **Autoriser les scripts locaux et à distance signé** ou **Autoriser tous les scripts**. |
| Échec de téléchargement de la configuration du connecteur. | Certificat client du connecteur, qui est utilisé pour l’authentification, a expiré. Cela peut également se produire si le connecteur est installé derrière un serveur proxy. Dans ce cas, le connecteur ne peut pas accéder à Internet et ne sera pas en mesure de fournir les applications aux utilisateurs distants. | Renouveler l’approbation manuellement à l’aide de la `Register-AppProxyConnector` applet de commande dans Windows PowerShell. Si le connecteur est derrière un proxy, il est nécessaire d’octroyer l’accès à Internet pour les comptes de connecteur « services réseau » et « système local ». Ceci est possible en leur accordant l’accès au Proxy ou en les définissant pour ignorer le proxy. |
| Échoué de l’enregistrement du connecteur : Assurez-vous que vous êtes un administrateur Global d’Active Directory pour inscrire le connecteur. Erreur : « la demande d’enregistrement a été refusée.' | L’alias que vous tentez d’ouvrir une session à l’aide n’est pas un administrateur sur ce domaine. Le connecteur est toujours installé pour le répertoire qui possède le domaine de l’utilisateur. | Assurez-vous que l’administrateur vous essayez de vous connecter a des autorisations globales au locataire AD Azure.|


## <a name="kerberos-errors"></a>Erreurs Kerberos

| Erreur | Description | Résolution |
| --- | --- | --- |
| Impossible de récupérer la stratégie en cours d’exécution pour l’exécution de scripts PowerShell. | En cas d’échec de l’installation du connecteur, vérifiez que la stratégie d’exécution PowerShell n’est pas désactivé. | Ouvrez l’éditeur de stratégie de groupe. Accédez à **Configuration de l’ordinateur** > **Les modèles d’administration** > **Composants Windows** > **De Windows PowerShell** , puis double-cliquez sur **Activer l’exécution du Script**. Cette option peut être définie à **Non configuré** ou **activé**. Si **activée**, assurez-vous que sous Options, la stratégie d’exécution est définie soit **Autoriser les scripts locaux et à distance signé** ou **Autoriser tous les scripts**. |
| 12008 - azure AD a dépassé le nombre maximal de tentatives d’authentification Kerberos autorisés au serveur principal. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et du serveur d’application principal, ou un problème de configuration de la date et l’heure sur les deux ordinateurs. | Le serveur principal a refusé le ticket Kerberos créé par AD Azure. Vérifiez qu’Active Directory Azure et le serveur d’application back-end sont configurées correctement. Assurez-vous que la configuration de la date et l’heure sur la publicité Azure et le serveur d’application back-end sont synchronisés. |
| 13016 - azure AD ne peut pas récupérer un ticket Kerberos pour le compte de l’utilisateur, car il n’y a aucun UPN dans le jeton de bord ou dans le cookie de l’accès. | Il existe un problème avec la configuration de SharePoint Team Services. | Corriger la configuration de la revendication UPN dans le STS. |
| 13019 - azure AD ne peut pas récupérer un ticket Kerberos pour le compte de l’utilisateur en raison de l’erreur API générale suivante. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur de contrôleur de domaine, ou un problème de configuration de la date et l’heure sur les deux ordinateurs. | Le contrôleur de domaine a refusé le ticket Kerberos créé par AD Azure. Vérifiez cette publicité Azure et le serveur d’application back-end sont configurées correctement, notamment la configuration du SPN. Assurez-vous que la publicité Azure est joint dans le même domaine que le contrôleur de domaine pour vous assurer que le contrôleur de domaine établit la relation d’approbation avec Azure AD au domaine. Assurez-vous que la configuration de la date et l’heure sur la publicité Azure et le contrôleur de domaine sont synchronisés. |
| 13020 - azure AD ne peut pas récupérer un ticket Kerberos pour le compte de l’utilisateur car le nom principal de service du serveur principal n’est pas défini. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur de contrôleur de domaine, ou un problème de configuration de la date et l’heure sur les deux ordinateurs. | Le contrôleur de domaine a refusé le ticket Kerberos créé par AD Azure. Vérifiez cette publicité Azure et le serveur d’application back-end sont configurées correctement, notamment la configuration du SPN. Assurez-vous que la publicité Azure est joint dans le même domaine que le contrôleur de domaine pour vous assurer que le contrôleur de domaine établit la relation d’approbation avec Azure AD au domaine. Assurez-vous que la configuration de la date et l’heure sur la publicité Azure et le contrôleur de domaine sont synchronisés. |
| 13022 - azure AD ne peut pas authentifier l’utilisateur, car le serveur principal répond aux tentatives d’authentification Kerberos avec une erreur HTTP 401. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et du serveur d’application principal, ou un problème de configuration de la date et l’heure sur les deux ordinateurs. | Le serveur principal a refusé le ticket Kerberos créé par AD Azure. Vérifiez qu’Active Directory Azure et le serveur d’application back-end sont configurées correctement. Assurez-vous que la configuration de la date et l’heure sur la publicité Azure et le serveur d’application back-end sont synchronisés. |
| Le site Web ne peut pas afficher la page. | L’utilisateur peut obtenir cette erreur lorsque vous tentez d’accéder à l’application que vous avez publié si l’application est une application IWA. Le nom principal de service définis pour cette application peut être incorrect. | Pour les applications authentifiées IWA : Assurez-vous que le nom principal de service configuré pour cette application est correct. |
| Le site Web ne peut pas afficher la page. | L’utilisateur peut obtenir cette erreur lorsque vous tentez d’accéder à l’application que vous avez publié si l’application est une application OWA. Cela peut être dû à une des opérations suivantes : <br> -Le nom principal de service définis pour cette application est incorrecte. <br> -L’utilisateur qui a tenté d’accéder à l’application est à l’aide d’un compte Microsoft plutôt que le compte d’entreprise approprié pour vous connecter, ou si l’utilisateur est un utilisateur invité. <br> -L’utilisateur qui a tenté d’accéder à l’application n’est pas correctement défini pour cette application sur le côté sur prem. | Les étapes pour réduire en conséquence : <br> -Assurez-vous que le nom principal de service configuré pour cette application est correct. <br> -Assurez-vous que l’utilisateur se connecte à l’aide de leur compte d’entreprise qui correspond au domaine de l’application publiée. Invités et des utilisateurs de Microsoft Account ne peut pas accéder à des applications de IWA. <br> -Assurez-vous que que cet utilisateur dispose des autorisations appropriées définies pour cette application back-end sur la machine sur prem. |
| Cette application d’entreprise ne sont pas accessibles. Vous n’êtes pas autorisé à accéder à cette application. Échoué de l’autorisation. Veillez à attribuer à l’utilisateur d’accéder à cette application. | Les utilisateurs peuvent obtenir cette erreur lorsque vous tentez d’accéder à l’application que vous avez publié si elles utilisent des comptes de Microsoft au lieu de leur compte entreprise pour vous connecter. Les utilisateurs invités peuvent également obtenir cette erreur. | Invités et les utilisateurs de Microsoft Account ne peut pas accéder à des applications de IWA. Assurez-vous que l’utilisateur se connecte à l’aide de leur compte d’entreprise qui correspond au domaine de l’application publiée. |
| Cette application d’entreprise ne sont pas accessibles actuellement. Veuillez réessayer ultérieurement... Le connecteur a expiré. | Les utilisateurs peuvent obtenir cette erreur lorsque vous tentez d’accéder à l’application que vous avez publié s’ils ne sont pas correctement définis pour cette application sur le côté sur prem. | Assurez-vous que vos utilisateurs ont les autorisations appropriées définies pour cette application back-end sur la machine sur prem. |


## <a name="see-also"></a>Voir aussi

- [Activer le Proxy d’Application pour Azure Active Directory](active-directory-application-proxy-enable.md)
- [Publier des applications avec le Proxy de l’Application](active-directory-application-proxy-publish.md)
- [Activer l’ouverture de session unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
