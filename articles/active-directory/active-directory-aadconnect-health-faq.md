<properties
    pageTitle="Annonce Azure se connecter le Forum aux questions sur la santé"
    description="Ce forum aux questions répond aux questions concernant la santé se connecter Azure AD. Cette FAQ couvre les questions sur l’utilisation du service, y compris le modèle, fonctionnalités, limitations et prise en charge de facturation."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Annonce Azure se connecter santé Forum aux Questions (FAQ)

Ce forum aux questions répond aux questions concernant la santé se connecter Azure AD. Cette FAQ couvre les questions sur l’utilisation du service, y compris le modèle, fonctionnalités, limitations et prise en charge de facturation.

## <a name="general-questions"></a>Questions d’ordre générales



**Q : gérer plusieurs annuaires AD Azure. Comment passer à celle avec Azure Active Directory Premium ?**

Vous pouvez basculer entre différents locataires Azure annonce en sélectionnant l’actuellement signé dans le coin supérieur droit dans la zone Nom d’utilisateur, le compte approprié. Si le compte n’est pas répertorié ici, sélectionnez Fermer la session, puis utilisez les informations d’identification de l’administrateur global du répertoire qui a Azure Active Directory prime est activé pour vous connecter.

## <a name="installation-questions"></a>Questions sur l’installation



**Q : quel est l’impact de l’installation de l’Agent d’intégrité connexion AD Azure sur des serveurs individuels ?**

L’impact de l’installation du Microsoft Azure AD connecter Health Agents ADFS, les serveurs Proxy de l’Application Web, Azure AD Connect (sycn) serveurs, contrôleurs de domaine est minime par rapport à la CPU, la bande passante réseau de la consommation de mémoire et de stockage.

Les nombres ci-dessous sont une approximation.

- Utilisation de l’UC : ~ 1 % d’augmentation
- La consommation de mémoire : jusqu'à 10 % de la mémoire système totale

>[AZURE.NOTE] En cas de l’agent est incapable de communiquer vers Azure, l’agent stocke les données localement, dans la limite maximale définie. L’agent écrase les données « cache » sur une base « moins récemment pris en charge ».

- Stockage de mémoire tampon locale pour Azure Agents d’intégrité connexion AD : environ 20 Mo
- Pour les serveurs ADFS, il est recommandé que vous mettez en service un espace disque de 1024 Mo (1 Go) pour le canal d’Audit AD FS pour Agents d’intégrité AD Azure se connecter traiter toutes les données d’audit avant son remplacement.

**Q : dois-je redémarrer mon serveurs pendant l’installation des Agents d’intégrité connexion AD Azure ?**

N° L’installation des agents ne nécessitera pas de redémarrer le serveur. Toutefois, l’installation de certaines de ces étapes requises peut nécessiter un redémarrage du serveur.

Par exemple, sur Windows Server 2008 R2, l’installation de .net Framework 4.5 requiert un redémarrage du serveur.


**Q : est un Azure AD connecter les Services de santé fonctionne via un proxy http directe ?**

Oui.  Pour en cours des opérations, vous pouvez configurer l’Agent d’intégrité pour transmettre les requêtes http sortant à l’aide d’un HTTP Proxy. Pour plus d’informations, consultez [Azure de configurer Active Directory se connecter Agents d’intégrité à utiliser le serveur HTTP Proxy.](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)
Si vous avez besoin configurer un serveur proxy lors de l’inscription de l’Agent, vous devrez peut-être modifier vos paramètres de Proxy Internet Explorer au préalable.
1. Ouvrir Internet Explorer -> Paramètres -> Internet Options -> connexions -> Paramètres du réseau local.
2. Sélectionnez utiliser un serveur Proxy pour votre réseau local.
3. Sélectionnez Options avancées si vous avez des ports différents proxy pour HTTP et HTTPS/Secure.

**Q : AD Azure se connecter des Services de santé prend-il en charge l’authentification de base lors de la connexion à des serveurs proxy Http ?**

N° Un mécanisme pour spécifier arbitraire de nom d’utilisateur/mot de passe pour l’authentification de base n’est actuellement pas pris en charge.


**Q : quelle est la version des services AD DS sont pris en charge par la santé se connecter Azure AD pour AD DS ?**

Surveillance des services AD DS est pris en charge lors de l’installation sur les Versions de système d’exploitation suivantes :

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## <a name="operations-questions"></a>Questions d’opérations



**Q : dois-je activer l’audit sur mes serveurs de Proxy AD FS Application ou les serveurs Proxy de mon Application Web ?**

Non, l’audit n’a pas besoin d’être activé sur les serveurs Proxy d’Application Web (WAP). L’activer sur les serveurs ADFS.


**Q : comment résolus Azure AD connecter les alertes de fonctionnement ?**

Azure AD connecter les alertes de fonctionnement de résoudre une condition du succès. Azure Agents d’intégrité connexion AD détecter et signaler les conditions de réussite pour le service sur une base périodique. Pour quelques alertes, la suppression est basée sur le temps. En d’autres termes, si la même condition d’erreur n’est pas respectée dans les 72 heures à partir de la génération d’alertes, l’alerte est automatiquement résolu.




**Q : quels ports de pare-feu que je dois ouvrir pour l’Azure AD connecter Health Agent fonctionne ?**

Vous devez disposer des ports TCP/UDP 443 et 5671 ouvert pour l’Azure AD connexion Agent d’intégrité être en mesure de communiquer avec les points de terminaison de service Azure AD santé.


**Q : Pourquoi voir les deux serveurs avec le même nom dans le portail de la santé se connecter AD Azure ?**

Lorsque vous supprimez un agent à partir d’un serveur, le serveur n’est pas automatiquement supprimé à partir du portail de connexion AD Azure.  Si vous manuellement supprimé un agent d’un serveur ou le serveur lui-même, vous devez supprimer manuellement l’entrée de serveur à partir du portail de la santé se connecter Azure AD. Pour plus d’informations, consultez [Supprimer une instance de serveur ou le service.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)

Si vous qu’un serveur ou créer un nouveau serveur avec les mêmes détails (par exemple, le nom de l’ordinateur) et n’avez pas supprimé le serveur déjà enregistré à partir du portail de la santé se connecter Azure AD, d’installer l’agent sur le nouveau serveur, vous pouvez voir deux entrées avec le même nom.  
Dans ce cas, vous devez supprimer l’entrée appartenant à l’ancien serveur manuellement. Les données de ce serveur doivent être obsolètes.

## <a name="related-links"></a>Liens connexes

* [Annonce Azure se connecter à la santé](active-directory-aadconnect-health.md)
* [AD Azure se connecter l’Installation de l’Agent de la santé](active-directory-aadconnect-health-agent-install.md)
* [AD Azure se connecter à des opérations de santé](active-directory-aadconnect-health-operations.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [À l’aide de la santé se connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Annonce Azure se connecter à l’historique de Version de la santé](active-directory-aadconnect-health-version-history.md)
