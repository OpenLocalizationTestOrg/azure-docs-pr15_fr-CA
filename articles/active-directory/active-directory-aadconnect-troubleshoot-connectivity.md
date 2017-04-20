<properties
    pageTitle="Azure AD connexion : Résolution des problèmes de connectivité | Microsoft Azure"
    description="Explique comment résoudre les problèmes de connectivité avec Azure Connect d’Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Résoudre les problèmes de connectivité avec Azure Connect de publicité
Cet article explique comment résoudre les problèmes de connectivité et fonctionne de la connectivité entre Azure Connect d’Active Directory et AD Azure. Ces problèmes sont plus susceptibles d’être visible dans un environnement avec un serveur proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Résoudre les problèmes de connectivité dans l’Assistant d’installation
Azure AD connexion utilise l’authentification modernes (à l’aide de la bibliothèque ADAL) pour l’authentification. L’Assistant d’installation et le moteur de synchronisation approprié nécessitent machine.config est configuré correctement dans la mesure où il s’agit des applications .NET.

Dans cet article, nous expliquerons comment Fabrikam se connecte à Azure AD via un serveur proxy. Le serveur proxy est nommé fabrikamproxy et utilise le port 8080.

Nous devons tout d’abord Assurez-vous que [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) est configuré correctement.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
Dans certains blogs non Microsoft, il est documenté modifications doivent être apportées au miiserver.exe.config à la place. Toutefois, ce fichier est remplacé sur chaque mise à jour même que s’il fonctionne au cours de l’installation initiale, le système s’arrête sur la première mise à niveau. Pour cette raison il est recommandé de mettre à jour le fichier machine.config au lieu de cela.

Le serveur proxy doit également avoir les URL requises ouverts. La liste officielle est documentée dans [Office 365 URL et les plages d’adresses IP ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Le tableau suivant est le strict minimum pour pouvoir se connecter à Active Directory Azure tout absolu. Cette liste n’inclut pas les fonctionnalités facultatives, telles que l’écriture différée de mot de passe ou AD Azure se connecter la santé. Il est documenté ici pour faciliter le dépannage de la configuration initiale.

URL | Port | Description
---- | ---- | ----
mscrl.Microsoft.com | HTTP/80 | Permet de télécharger des listes de révocation de certificats.
\*. verisign.com | HTTP/80 | Permet de télécharger des listes de révocation de certificats.
\*. entrust.com | HTTP/80 | Utilisé pour télécharger les listes de révocation de certificats de l’AMF.
\*. windows.net | HTTPS/443 | Utilisé pour se connecter à Active Directory Azure.
Secure.aadcdn.microsoftonline-p.com | HTTPS/443 | Utilisé pour AMF.
\*. microsoftonline.com | HTTPS/443 | Utilisé pour configurer votre répertoire AD Azure et importer/exporter des données.

## <a name="errors-in-the-wizard"></a>Erreurs dans l’Assistant
L’Assistant d’installation est en utilisant deux contextes de sécurité différents. Sur la page **se connecter à Active Directory Azure** qu’il utilise l’utilisateur actuellement connecté. Sur la page **configurer** il change pour [compte qui exécute le service pour le moteur de synchronisation](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). Les configurations de proxy nous rendre sont globales pour l’ordinateur s’il existe un problème, le problème sera plus probablement apparaissent déjà sur la page de **connexion à Active Directory Azure** dans l’Assistant.

Voici les erreurs les plus courantes que vous verrez dans l’Assistant d’installation.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>L’Assistant installation n’a pas été configuré correctement
Cette erreur s’affiche lorsque l’Assistant ne peut pas atteindre le serveur proxy.
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Si vous voyez ceci, vérifiez que le [fichier machine.config](active-directory-aadconnect-prerequisites.md#connectivity) a été correctement configuré.
- Si qui semble correcte, suivez les étapes de [connexion proxy de vérification](#verify-proxy-connectivity) pour voir si le problème se produit en dehors de l’Assistant.

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Le point de terminaison AMF ne peut pas être atteint.
Cette erreur apparaît si le point de terminaison **https://secure.aadcdn.microsoftonline-p.com** ne peut pas être atteint et si votre administrateur global a AMF activé.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- Si vous voyez cela, vérifiez que le point de terminaison secure.aadcdn.microsoftonline-p.com a été ajouté au serveur proxy.

### <a name="the-password-cannot-be-verified"></a>Le mot de passe ne peut pas être vérifiée.
Si l’Assistant installation a réussi lors de la connexion à Active Directory Azure, mais le mot de passe ne peut pas être vérifiée vous verrez ceci : ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- Est le mot de passe un mot de passe temporaire et doit être changé ? Il n’est en fait le mot de passe ? Essayez de vous connecter à https://login.microsoftonline.com (sur un autre ordinateur que le serveur Azure Connect de publicité) et vérifiez que le compte est utilisable.

### <a name="verify-proxy-connectivity"></a>Vérifiez la connectivité du serveur proxy
Pour vérifier si le serveur Azure Connect de AD dispose d’une connectivité réelle avec le Proxy et Internet, nous utiliserons certains PowerShell pour voir si le proxy est ce qui permet des requêtes web ou non. Dans une invite de commandes PowerShell, exécutez `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Techniquement le premier appel est de https://login.microsoftonline.com et cela fonctionnera également, mais l’autre URI est plus rapide de répondre.)

PowerShell utilisera la configuration dans le fichier machine.config pour contacter le serveur proxy. Les paramètres winhttp/netsh ne doivent pas pénaliser ces applets de commande.

Si le proxy est configuré correctement, vous devez obtenir un état de réussite : ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Si vous recevez **Impossible de se connecter au serveur distant** puis PowerShell tente de faire un appel direct sans utiliser le serveur proxy ou DNS n’est pas configuré correctement. Assurez-vous que le fichier **machine.config** est configuré correctement.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Si le proxy n’est pas correctement configuré, nous allons une erreur : ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

Erreur | Texte d’erreur | Commentaire
---- | ---- | ---- |
403 | Interdit | Le proxy n’a pas été ouvert pour l’URL demandée. Revoir la configuration du serveur proxy et assurez-vous que l' [URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) a été ouvert.
407 | Authentification proxy requise | Le serveur proxy requis de connexion, et aucun n’a été fourni. Si votre serveur proxy requiert une authentification, assurez-vous que vous disposez de ceci configuré dans le fichier machine.config. Assurez-vous également que vous utilisez des comptes de domaine pour l’utilisateur qui exécute l’Assistant ainsi que pour le compte de service.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Le modèle de communication entre Azure Connect d’Active Directory et AD Azure
Si vous avez suivi toutes les étapes ci-dessus et fixes ne peut pas se connecter vous pouvez à ce stade commencer en examinant les journaux de réseau. Cette section est de fournir un modèle de connectivité normale et réussie. Il est également mise en vente des harengs rouge courantes qui peuvent être ignorés si vous lisez les journaux de réseau.

- Il y aura des appels à https://dc.services.visualstudio.com. Il n’est pas obligé de ce est ouverte dans le proxy pour l’installation réussisse, et celles-ci peuvent être ignorées.
- Vous verrez que la résolution dns répertorie les hôtes réels dans les nsatc.net d’espace de nom DNS et d’autres espaces de noms non sous microsoftonline.com. Toutefois, il sera pas les demandes de service web sur les noms de serveur et vous n’êtes pas obligé de les ajouter au serveur proxy.
- Les points de terminaison adminwebservice et provisioningapi (voir ci-dessous dans les journaux) découverte de points de terminaison et permet de rechercher le point de terminaison à utiliser et va être différent en fonction de votre région.

### <a name="reference-proxy-logs"></a>Journaux du proxy référence
Voici une image à partir d’un journal de proxy réel et la page de l’Assistant installation à partir de l’endroit où elle a été prise (le même point de terminaison d’entrées en double ont été supprimées). Cela peut servir comme référence pour les journaux de votre propre réseau et de proxy. Les points de terminaison réels peuvent être différents dans votre environnement (en particulier ceux en *italique*).

**Connexion à Active Directory Azure**

Heure | URL
--- | ---
1/11/2016 8:31 | Connect://login.microsoftonline.com:443
1/11/2016 8:31 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | se connecter : / /*bba800-ancre*. microsoftonline.com:443
1/11/2016 8:32 | Connect://login.microsoftonline.com:443
1/11/2016 8:33 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | se connecter : / /*bwsc02-relais*. microsoftonline.com:443

**Configurer**

Heure | URL
--- | ---
1/11/2016 8:43 | Connect://login.microsoftonline.com:443
1/11/2016 8:43 | se connecter : / /*bba800-ancre*. microsoftonline.com:443
1/11/2016 8:43 | Connect://login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | se connecter : / /*bba900-ancre*. microsoftonline.com:443
1/11/2016 8:44 | Connect://login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | se connecter : / /*bba800-ancre*. microsoftonline.com:443
1/11/2016 8:44 | Connect://login.microsoftonline.com:443
1/11/2016 8:46 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | se connecter : / /*bwsc02-relais*. microsoftonline.com:443

**Synchronisation initiale**

Heure | URL
--- | ---
1/11/2016 8:48 | Connect://login.Windows.NET:443
1/11/2016 8:49 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | se connecter : / /*bba900-ancre*. microsoftonline.com:443
1/11/2016 8:49 | se connecter : / /*bba800-ancre*. microsoftonline.com:443

## <a name="authentication-errors"></a>Erreurs d’authentification
Cette section traite des erreurs qui peuvent être renvoyées à partir de ADAL (la bibliothèque d’authentification utilisée par Azure Connect de publicité) et PowerShell. L’erreur décrite doit vous aider dans les étapes suivantes.

### <a name="invalid-grant"></a>Licence non valide
Nom d’utilisateur non valide ou le mot de passe. Pour plus d’informations, voir [le mot de passe ne peut pas être vérifiée](#the-password-cannot-be-verified) .

### <a name="unknown-user-type"></a>Type d’utilisateur inconnu
Votre répertoire Azure AD ne peut pas être découverts ou résolu. Vous essayez peut-être de se connecter avec un nom d’utilisateur dans un domaine non vérifié ?

### <a name="user-realm-discovery-failed"></a>Échoué de la découverte de domaine utilisateur
Problèmes de configuration de réseau ou de serveur proxy. Le réseau ne peut pas être atteinte, consultez [résoudre les problèmes de connectivité dans l’Assistant d’installation](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Mot de passe a expiré.
Vos informations d’identification ont expiré. Modifier votre mot de passe.

### <a name="authorizationfailure"></a>AuthorizationFailure
Problème inconnu.

### <a name="authentication-cancelled"></a>Authentification annulée
La stimulation d’authentification à plusieurs facteurs (AMF) a été annulée.

### <a name="connecttomsonline"></a>ConnectToMSOnline
L’authentification a réussi, mais Azure AD PowerShell a un problème d’authentification.

### <a name="azurerolemissing"></a>AzureRoleMissing
L’authentification a réussi. Vous n’êtes pas un administrateur global.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
L’authentification a réussi. Gestion des identités privilégié a été activée et vous n’êtes actuellement pas un administrateur global. Pour plus d’informations, consultez [Gestion des identités privilégié](active-directory-privileged-identity-management-getting-started.md) .

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
L’authentification a réussi. Impossible de récupérer les informations de société AD Azure.

### <a name="retrievedomains"></a>RetrieveDomains
L’authentification a réussi. Impossible de récupérer les informations de domaine AD Azure.

## <a name="troubleshooting-steps-for-previous-releases"></a>Étapes de dépannage pour les versions précédentes.
Avec les versions commençant par le numéro de build 1.1.105.0 (publié février 2016), l’assistant de connexion a été déclassé. Cette section et la configuration ne devrait plus être requis, mais est conservé en tant que référence.

Pour la connexion unique dans l’assistant de travailler, winhttp doit être configuré. Vous pouvez faire avec [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>L’assistant de connexion n’a pas été configuré correctement
Cette erreur s’affiche lorsque l’assistant de connexion ne peut pas atteindre le serveur proxy ou le serveur proxy n’autorise pas la demande.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Si vous voyez, examinez la configuration du proxy de [netsh](active-directory-aadconnect-prerequisites.md#connectivity) et vérifiez qu’il est correct.
![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Si qui semble correcte, suivez les étapes de [connexion proxy de vérification](#verify-proxy-connectivity) pour voir si le problème se produit en dehors de l’Assistant.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
