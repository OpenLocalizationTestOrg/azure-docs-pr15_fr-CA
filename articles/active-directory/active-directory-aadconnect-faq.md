<properties
    pageTitle="Annonce Azure Connect : Forum aux questions sur | Microsoft Azure"
    description="Cette page a Forum aux questions sur Azure Connect d’Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-faq"></a>Annonce Azure se connecter Forum aux questions

## <a name="general-installation"></a>Installation générale
**Q : installation fonctionnera si l’administrateur Global AD Azure a 2FA activé ?**  
Avec les versions de février 2016, cela est pris en charge.

**Q : est-il possible d’installer Azure Connect AD sans assistance ?**  
Il est uniquement pris en charge pour installer Azure Connect AD à l’aide de l’Assistant d’installation. Une installation sans assistance et en mode silencieux n’est pas pris en charge.

**Q : j’ai une forêt où un domaine ne peut pas être contacté. Comment installer les Azure AD Connect ?**  
Avec les versions de février 2016, cela est pris en charge.

**Q : l’agent d’intégrité AD DS fonctionne sur server core ?**  
Oui. Après l’installation de l’agent, vous pouvez exécuter le processus d’inscription à l’aide de l’applet de commande PowerShell suivante : 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Réseau
**Q : vous avez un pare-feu, d’un périphérique réseau ou autre chose qui limite les connexions de durée maximale peut rester ouverte sur mon réseau. Combien de temps mon seuil de délai d’attente côté client convient lors de l’utilisation d’Azure AD Connect ?**  
Tous les logiciels réseau, périphériques physiques ou tout autre élément qui limite la durée maximale que peuvent les connexions restent ouvertes doit utiliser un seuil d’au moins 5 minutes (300 secondes) pour la connectivité entre le serveur où est installé le client Azure Connect de AD et Azure Active Directory. Cela s’applique également à tous les outils de synchronisation de Microsoft Identity publiées.

**Q : est des SLD (domaines en une seule) pris en charge ?**  
Non, AD Azure Connect ne gère pas les forêts sur site/à l’aide de SLD de domaines.

**Q : sont « pointillés » NetBios nommés pris en charge ?**  
Non, AD Azure Connect ne gère pas les forêts de locaux/domaines où le nom NetBios contient un point «. » dans le nom.

## <a name="federation"></a>Fédération
**Q : que faire si je reçois un e-mail qui me demande de renouveler mon certificat Office 365**  
Utilisez les instructions qui sont décrite dans la rubrique de [renouveler des certificats](active-directory-aadconnect-o365-certs.md) sur la manière de renouveler le certificat.

**Q : j’ai « Mettre à jour automatiquement partie de confiance » pour la partie de confiance O365. Avez aucune action lorsque ma automatiquement le certificat de signature de jeton survole ?**  
Utilisez les instructions qui sont décrite dans l’article de [renouveler des certificats](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Environnement
**Q : il pris en charge pour renommer le serveur une fois, Azure Connect d’Active Directory a été installé ?**  
N° Modification du nom de serveur entraîne le moteur de synchronisation ne peut pas se connecter à la base de données SQL et le service ne sera pas en mesure de démarrer.

## <a name="identity-data"></a>Données d’identité
**Q : l’attribut UPN (userPrincipalName) dans Active Directory Azure ne correspond pas à l’UPN sur prem - pourquoi ?**  
Consultez ces articles :

- [Noms d’utilisateur dans Office 365, Azure ou Intune ne correspondent pas à la local UPN ou un ID de connexion de remplacement](https://support.microsoft.com/en-us/kb/2523192)
- [Les modifications ne sont pas synchronisées par l’outil de synchronisation d’annuaire actif Azure après avoir modifié le nom UPN d’un compte d’utilisateur pour utiliser un autre domaine fédéré](https://support.microsoft.com/en-us/kb/2669550)

Vous pouvez également configurer AD Azure pour permettre au moteur de synchronisation mettre à jour l’userPrincipalName comme décrit dans les [fonctions de service de synchronisation Azure Connect d’Active Directory](active-directory-aadconnectsyncservice-features.md).

## <a name="custom-configuration"></a>Configuration personnalisée
**Q : où figurent les applets de commande PowerShell pour Azure AD Connect ?**  
À l’exception des applets de commande décrites sur ce site, autres applets de commande PowerShell dans Azure Connect de publicité ne sont pas pris en charge par le client.

* *Q : peut utiliser de « serveur exportation importer » est trouvé dans *un gestionnaire de Service de synchronisation* pour passer la configuration servers? **  
N° Cette option ne sera pas récupéré tous les paramètres de configuration et ne doit pas être utilisée. Vous devez plutôt utiliser l’Assistant pour créer la configuration de base sur le second serveur et utiliser l’éditeur de règles de synchronisation pour générer des scripts PowerShell pour déplacer une règle personnalisée entre les serveurs. Voir [déplacer une configuration personnalisée d’actif pour le serveur intermédiaire](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## <a name="troubleshooting"></a>Résolution des problèmes
**Q : Comment puis-je obtenir de l’aide avec Azure AD Connect ?**

[Recherche dans la Base de connaissances Microsoft (Ko)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Rechercher la Base de connaissances Microsoft (KB) pour des solutions techniques à des problèmes courants de réparation sur le Support pour Azure Connect d’Active Directory.

[Forums d’Active Directory de Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- Vous pouvez rechercher et parcourir des techniques questions et des réponses de la Communauté ou posez votre question en cliquant sur [ici](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Prise en charge du client AD Connect Azure](https://manage.windowsazure.com/?getsupport=true)

- Utilisez ce lien pour obtenir de l’aide via le portail Azure.
