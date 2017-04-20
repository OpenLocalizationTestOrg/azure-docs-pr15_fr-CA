<properties
    pageTitle="Travailler avec les domaines personnalisés dans le Proxy d’Application AD Azure | Microsoft Azure"
    description="Couvertures de fonctionnement avec les domaines personnalisés dans le Proxy d’Application Azure AD."
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

# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Travailler avec les domaines personnalisés dans le Proxy d’Application Azure AD

À l’aide d’un domaine par défaut vous permet de définir la même URL que l’URL interne et externe pour accéder à l’application afin que vos utilisateurs aient uniquement une seule URL à mémoriser accéder à l’application, peu importe où ils accèdent à partir de. Cela vous permet également de créer un seul raccourci clavier dans le panneau d’accès pour l’application. Si vous utilisez le domaine par défaut fourni par le Proxy d’Application AD Azure, il n’existe aucune configuration supplémentaire, que vous devez activer votre domaine. Dans le cas où vous utilisez un domaine personnalisé, il y a quelques choses à faire pour vous assurer que le Proxy d’Application reconnaît votre domaine et qu’il valide ses certificats.

## <a name="selecting-your-custom-domain"></a>Sélectionnez votre domaine personnalisé

1. Publication de votre application en suivant les instructions dans les [applications de publication avec le Proxy de l’Application](active-directory-application-proxy-publish.md).
2. Lorsque l’application s’affiche dans la liste des applications, sélectionnez-le, puis cliquez sur **configurer**.
3. Sous **URL externe**, entrez votre domaine personnalisé.
4. Si votre URL externe est https, vous êtes invité à télécharger un certificat afin que Azure peut valider l’URL de l’application. Vous pouvez également télécharger un certificat générique qui correspond à l’URL externe de l’application. Ce domaine doit être dans la liste de vos [Azure vérifié les domaines](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure doit disposer d’un certificat pour l’URL du domaine de l’application ou d’un certificat générique qui correspond à l’URL externe pour l’application.
5. Veillez à ajouter un enregistrement DNS qui achemine l’URL interne à l’application qui vous permet d’avoir la même URL pour l’accès interne et externe et un seul raccourci vers l’application dans la liste des applications de l’utilisateur.

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>Forum aux questions sur l’utilisation des domaines personnalisés

Q : est-il possible de sélectionner un certificat déjà téléchargé sans télécharger à nouveau ?  
A: précédemment téléchargé les certificats sont automatiquement liés à une application et il existe exactement un certificat correspondant le nom d’hôte de l’application.  

Q : comment pour ajouter un certificat et dans quel format le certificat exporté à télécharger ?  
R : le certificat doit être téléchargé à partir de la page de configuration d’application. Le certificat doit être un fichier PFX.  

Q : les certificats ECC utilisable ?  
R : il n’y a aucune limitation explicite sur les méthodes de signature.  

Q : les certificats SAN permet ?  
R : Oui.  

Q : les certificats génériques permet ?  
R : Oui.  

Q : un autre certificat utilisable sur chaque application ?  
R : Oui, à moins que les deux applications partagent le même hôte externe.  

Q : si j’enregistrer un nouveau domaine, puis-je utiliser ce domaine ?  
R : Oui, la liste des domaines est chargée à partir de la liste de domaine vérifié du locataire.  

Q : que se passe-t-il lorsqu’un certificat expire ?  
R : vous obtiendrez un message d’avertissement dans la section de certificat dans la page de configuration d’application. Lorsqu’un utilisateur essaie d’accéder à l’application, un avertissement de sécurité s’affiche.  

Q : que dois-je faire si je veux remplacer un certificat pour une application donnée ?  
R : télécharger un nouveau certificat à partir de la page de configuration d’application.  

Q : puis-je supprimer un certificat et le remplacer ?  
R : lorsque vous chargez un nouveau certificat, si l’ancien certificat n’est pas en cours d’utilisation par une autre application, il sera automatiquement supprimé.  

Q : que se passe-t-il lorsqu’un certificat est révoqué ?  
A: vérification de la révocation de n’est pas effectuées pour les certificats. Lorsqu’un utilisateur essaie d’accéder à l’application, en fonction du navigateur, un avertissement de sécurité peut s’afficher.  

Q : puis-je utiliser un certificat signé automatiquement ?  
R : Oui, les certificats auto-signés sont autorisés. Notez que si vous utilisez une autorité de certification privée, le CDP (point de distribution de point de révocation de certificats) pour le certificat doit être publique.  

Q : y a-t-il une place pour afficher tous les certificats pour mes clients ?  
R : il n’est pas pris en charge dans la version actuelle.  


## <a name="see-also"></a>Voir aussi

- [Publier des applications avec le Proxy de l’Application](active-directory-application-proxy-publish.md)
- [Activer l’ouverture de session unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Ajoutez votre nom de domaine personnalisé pour Azure AD](active-directory-add-domain.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
