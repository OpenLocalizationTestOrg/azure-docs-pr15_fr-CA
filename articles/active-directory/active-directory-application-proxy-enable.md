<properties
    pageTitle="Activer le Proxy d’Application AD Azure | Microsoft Azure"
    description="Activer le Proxy d’Application dans Azure portal classique et installer les connecteurs pour le serveur proxy inverse."
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
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>

# <a name="enable-application-proxy-in-the-azure-portal"></a>Activer le Proxy d’Application dans Azure portal

Cet article vous guide tout au long de la procédure pour activer le Proxy d’Application Microsoft Azure AD pour votre répertoire de nuage dans Azure AD.

Si vous n’êtes pas familiarisé avec le Proxy d’Application peut vous aider à faire, en savoir plus sur [comment fournir un accès à distance sécurisé aux applications sur site](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Composants requis d’application Proxy
Avant de pouvoir activer et utiliser les services de Proxy de l’Application, vous devez disposer :

- Un [abonnement premium ou de base de Microsoft Azure AD](active-directory-editions.md) et un annuaire AD Azure pour lesquels vous êtes un administrateur global.
- Un serveur exécutant Windows Server 2012 R2, ou Windows version 8.1 ou ultérieure, sur lesquels vous pouvez installer le Proxy d’Application Connector. Le serveur envoie des demandes pour les services de Proxy de l’Application dans le nuage, et il doit établir une connexion HTTP ou HTTPS pour les applications que vous publiez.

    - Pour de l’authentification unique pour les applications publiées, cette machine doit être à un domaine dans le même domaine Active Directory, comme les applications que vous publiez.

- Si le chemin d’accès est un pare-feu, assurez-vous qu’il est ouvert afin que le connecteur puisse faire des demandes HTTPS (TCP) pour le Proxy d’Application. Le connecteur utilise ces ports et sous-domaines qui font partie des domaines de haut niveau msappproxy.net et servicebus.windows.net. Veillez à ouvrir les ports suivants pour le trafic **sortant** :

  	| Numéro de port | Description |
  	| --- | --- |
  	| 80 | Activer le trafic HTTP sortant pour la validation de la sécurité. |
  	| 443 | Activer l’authentification des utilisateurs par rapport à publicité Azure (requise uniquement pour le processus d’inscription de connecteur) |
  	| 10100 – 10120 | Activer les réponses HTTP de LOB envoyées vers le serveur proxy |
  	| 9352, 5671 | Activer la communication entre le connecteur vers le service Azure pour les demandes entrantes. |
  	| 9350 | Facultatif, pour améliorer les performances pour les requêtes entrantes |
  	| 8080 | Activer la séquence d’amorçage de connecteur et de la mise à jour automatique de connecteur |
  	| 9090 | Activer l’enregistrement du connecteur (requis uniquement pour le processus d’inscription de connecteur) |
  	| 9091 | Activer le renouvellement automatique des certificats d’approbation de connecteur |

    Si votre pare-feu applique le trafic en fonction de l’origine des utilisateurs, ouvrir ces ports pour le trafic en provenance de services Windows en cours d’exécution en tant que Service réseau. En outre, assurez-vous qu’activer le port 8080 pour NT Authority\System.

- Si votre organisation utilise des serveurs proxy pour se connecter à internet, veuillez Examinez le billet de blog [fonctionne avec les serveurs proxy locaux existants](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) pour plus d’informations sur la façon de les configurer.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Étape 1 : Activer le Proxy d’Application dans Azure AD
1. Ouvrez une session en tant qu’administrateur dans [Azure portal classique](https://manage.windowsazure.com/).
2. Accédez à Active Directory et sélectionnez le répertoire dans lequel vous souhaitez activer le Proxy d’Application.

    ![Active Directory - icône](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Sélectionnez **configurer** dans la page répertoire et faites défiler jusqu'à **l’Application Proxy**.
4. Activer/désactiver **Activer les Services de Proxy d’Application pour ce répertoire** **activé**.

    ![Activer le Proxy d’Application](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Sélectionnez **Télécharger maintenant**. Cela vous amène à l' **Azure AD Application Proxy connecteur télécharger**. Lire et accepter le termes du contrat de licence et cliquez sur **Télécharger** pour enregistrer le fichier de programme d’installation de Windows (.exe) pour le connecteur.

## <a name="step-2-install-and-register-the-connector"></a>Étape 2 : Installer et inscrire le connecteur
1. Exécutez **AADApplicationProxyConnectorInstaller.exe** sur le serveur que vous avez préparé en fonction de la configuration requise.
2. Suivez les instructions de l’Assistant d’installation.
3. Lors de l’installation, vous êtes invité à enregistrer le connecteur avec l’Application de Proxy de vos clients AD Azure serez.

  - Fournir vos informations d’identification administrateur global de publicité Azure. Votre client de l’administrateur global peut être différente de vos informations d’identification Microsoft Azure.
  - Assurez-vous que l’administrateur qui enregistre le connecteur est dans le même répertoire que celui dans lequel vous avez activé le service de Proxy de l’Application. Par exemple, si le domaine client est contoso.com, l’administrateur doit être admin@contoso.com ou tout autre alias sur ce domaine.
  - Si **La Configuration de sécurité renforcée d’Internet Explorer** est activé **sur** le serveur où vous installez le connecteur, l’écran d’enregistrement peut-être être bloqué. Suivez les instructions dans le message d’erreur pour autoriser l’accès. Assurez-vous que la sécurité renforcée d’Internet Explorer est désactivé.
  - Si l’inscription du connecteur n’aboutit pas, consultez [Résoudre les problèmes de Proxy de l’Application](active-directory-application-proxy-troubleshoot.md).  

4. Une fois l’installation terminée, les deux nouveaux services sont ajoutés à votre serveur :

    - **Le connecteur Microsoft DAS Application Proxy** permet la connectivité
    - **Mise à jour du connecteur de Proxy Application Microsoft DAS** est un service de mise à jour automatique, qui vérifie les nouvelles versions du connecteur régulièrement et qui met à jour le connecteur en fonction des besoins.

    ![Services de connecteur de Proxy d’application - capture d’écran](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Cliquez sur **Terminer** dans la fenêtre d’installation.

À des fins de haute disponibilité, vous devez déployer au moins deux connecteurs. Pour déployer plusieurs connecteurs, répétez les étapes 2 et 3, ci-dessus. Chaque connecteur doit être enregistré séparément.

Si vous souhaitez désinstaller le connecteur, désinstallez le service du connecteur et le service de mise à jour. Redémarrez votre ordinateur pour supprimer complètement le service.


## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à [publier des applications avec le Proxy de l’Application](active-directory-application-proxy-publish.md).

Si vous avez des applications qui se trouvent sur des réseaux distincts ou des emplacements différents, vous pouvez utiliser des groupes de connecteur pour organiser les différents connecteurs en unités logiques. Pour en savoir plus sur [l’utilisation de connecteurs de Proxy de l’Application](active-directory-application-proxy-connectors.md).
