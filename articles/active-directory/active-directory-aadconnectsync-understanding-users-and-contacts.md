<properties
    pageTitle="Azure Connect de AD sync : compréhension des utilisateurs et des Contacts | Microsoft Azure"
    description="Explique les utilisateurs et contacts dans Azure Connect de AD sync."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Azure Connect de AD sync : compréhension des utilisateurs et des Contacts

Il existe diverses raisons pourquoi il vous faudrait plusieurs forêts Active Directory et il existe plusieurs topologies de déploiement différentes. Modèles courants incluent un déploiement compte-ressource et des forêts de sync'ed de liste d’adresses globale après une fusion & l’acquisition. Mais même s’il existe des modèles purs, modèles hybrides sont également communs. La configuration par défaut dans Azure Connect de AD sync ne suppose pas un modèle particulier, mais en fonction de l’utilisateur correspondant a été sélectionné dans le guide d’installation, des comportements différents peuvent être observées.

Dans cette section, nous étudierons comment la configuration par défaut se comporte dans certaines topologies. Nous étudierons la configuration et l’éditeur de règles de synchronisation peut être utilisé pour vérifier la configuration.

Il existe quelques règles générales de que la configuration suppose :

- Quelle que soit la commande sur laquelle nous importer à partir de la source annuaires Active Directory, le résultat final doit toujours être la même.
- Un compte actif contribuera toujours les informations de connexion, y compris **userPrincipalName** et **sourceAnchor**.
- Un compte désactivé contribuera userPrincipalName et sourceAnchor, sauf s’il est une boîte aux lettres liée, s’il n’existe aucun compte active à trouver.
- Un compte avec une boîte aux lettres liée sera jamais utilisé pour userPrincipalName et sourceAnchor. Il est supposé qu’un compte actif se trouve plus loin.
- Un objet de contact peut être mis en service pour Azure AD sous la forme d’un contact ou un utilisateur. Vous ne connaissez vraiment pas tant que toutes les forêts d’Active Directory source ont été traités.

## <a name="contacts"></a>Contacts

Ayant des contacts représentant un utilisateur dans une autre forêt est courant après une fusion acquisitions où une solution GALSync est pontage de plusieurs forêts Exchange. L’objet de contact est toujours rejoindre à partir de l’espace de connecteur dans le métaverse à l’aide de l’attribut de messagerie. S’il existe déjà un objet contact ou utilisateur avec la même adresse de messagerie, les objets sont jointes. Ce paramètre est configuré dans la règle **dans à partir d’Active Directory – Contact joindre**. Il existe également une règle nommée **dans à partir d’Active Directory – Contact commun** avec un attribut de flux dans le métaverse attribut **TypeObjetSource** avec le **Contact**de constante. Cette règle a une priorité très faible, si n’importe quel objet utilisateur est joint à l’objet du métaverse même, puis la règle **dans à partir d’Active Directory – utilisateur courant** contribuera à la valeur utilisateur à cet attribut. Avec cette règle, cet attribut a la valeur Contact si aucun utilisateur n’a pas été joint et la valeur utilisateur si au moins un utilisateur a été trouvé.

Pour la mise en service d’un objet à Active Directory Azure, la règle de trafic sortant **les DAS – contactez joindre** crée un objet contact si la valeur de l' attribut de méta-verse **TypeObjetSource** à **contacter**. Si cet attribut est défini sur **utilisateur**, puis la règle **à DAS – utilisateur joindre** créera un objet utilisateur au lieu de cela.
Il est possible qu’un objet est promu de le Contact à utilisateur lorsque plusieurs annuaires d’Active Directory source sont importés et synchronisés.

Par exemple, dans une topologie GALSync nous trouverez objets contact pour tout le monde dans la deuxième forêt lorsque nous importer la première forêt. Cela s’effectuera les nouveaux objets contacts dans le connecteur de DAS. Lorsque par la suite, nous importer et synchroniser la deuxième forêt, nous trouver les utilisateurs réels et les joindre aux objets existants du métaverse. Puis nous supprimer l’objet de contact dans DAS et créer un nouvel objet utilisateur à la place.

Si vous disposez d’une topologie où les utilisateurs et représenté en tant que contacts, assurez-vous que vous sélectionnez pour faire correspondre les utilisateurs sur l’attribut mail dans le guide d’installation. Si vous sélectionnez une autre option, vous aurez une configuration dépendants de commande. Objets contact appartiendront toujours sur l’attribut de courrier, mais les objets utilisateur seront uniquement joindre sur l’attribut de messagerie si cette option a été sélectionnée dans le guide d’installation. Vous pourriez alors finir pas avec deux objets différents dans le métaverse avec l’attribut de messagerie même si l’objet de contact a été importé avant que l’objet utilisateur. Lors de l’exportation vers Azure AD, une erreur sera levée. Ce comportement est voulu par la conception et indique que des données incorrectes ou que la topologie n’a pas correctement identifiée lors de l’installation.

## <a name="disabled-accounts"></a>Comptes désactivés

Comptes désactivés sont synchronisés ainsi à Active Directory Azure. Comptes désactivés sont courants pour représenter les ressources Exchange, par exemple des salles de conférence. L’exception concerne les utilisateurs avec une boîte aux lettres liée ; comme mentionné précédemment, il configurera jamais un compte Azure AD.

L’hypothèse est que si un compte d’utilisateur désactivé est trouvé, puis nous ne trouve pas un autre compte actif ultérieurement et l’objet est mis en service pour Azure AD avec l’userPrincipalName et sourceAnchor trouvé. Dans le cas où un autre compte actif appartiendront au même objet du méta-verse, puis son userPrincipalName et sourceAnchor seront utilisés.

## <a name="changing-sourceanchor"></a>Modification de sourceAnchor

Lorsqu’un objet a été exporté vers Active Directory Azure, puis il est interdit de modifier le sourceAnchor de plus. Lorsque l’objet a été exporté le d’attribut du métaverse **cloudSourceAnchor** est définie avec la valeur de **sourceAnchor** acceptée par AD Azure. Si **sourceAnchor** est modifié et ne correspond pas à **cloudSourceAnchor**, la règle **les DAS – utilisateur jointure** lève l’erreur **l’attribut sourceAnchor a modifié**. Dans ce cas, la configuration ou données doivent être corrigées afin que le même sourceAnchor est présent dans le métaverse à nouveau avant que l’objet puisse être de nouveau synchronisé.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Azure Sync de connexion Active Directory : Options de personnalisation de la synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
