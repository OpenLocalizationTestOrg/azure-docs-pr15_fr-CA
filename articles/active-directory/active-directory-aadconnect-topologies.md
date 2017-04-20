<properties
    pageTitle="Azure AD Connect : Prise en charge des topologies | Microsoft Azure"
    description="Cette rubrique détaille les topologies prises en charge et non pris en charge pour les connexion AD Azure"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="billmath"/>

# <a name="topologies-for-azure-ad-connect"></a>Topologies de publicité Azure se connecter
L’objectif de cette rubrique est de décrire les topologies AD Azure Sync Azure AD connexion que la solution d’intégration de clé et des locaux différents. Il décrit les configurations prises en charge et non pris en charge.

Légende des images dans le document :

Description | Icône
-----|-----
Forêt Active Directory sur site| ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)
Active Directory avec importation filtrée| ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Serveur de synchronisation AD Connect Azure| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync1.png)
Annonce Azure sync server « transit mode connexion »| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync2.png)
GALSync avec FIM2010 ou MIM2016| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Serveur de synchronisation AD Connect Azure, détaillée| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Répertoire de publicité Azure |![DAS](./media/active-directory-aadconnect-topologies/LegendAAD.png)
Scénario non pris en charge | ![Non pris en charge](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)

## <a name="single-forest-single-azure-ad-tenant"></a>Une seule forêt, seul les clients AD Azure
![Mono-utilisateur de forêt unique](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topologie la plus courante est une seule forêt sur site, avec un ou plusieurs domaines et une publicité Azure client. Pour l’authentification AD Azure, la synchronisation de mot de passe est utilisée. L’installation expresse de Azure AD Connect prend en charge uniquement cette topologie.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Forêt unique, plusieurs serveurs de synchronisation de priorité Azure AD
![Une seule forêt filtrée non pris en charge](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Il n’est pas pris en charge pour plusieurs serveurs de synchronisation Azure AD connecter connecté au locataire AD Azure même, à l’exception d’un [serveur de test](#staging-server). Il est non pris en charge, même si ceux-ci sont configurés pour synchroniser un jeu mutuellement exclusif d’objets. Vous auriez pu considérer cela si vous ne pouvez pas atteindre tous les domaines de la forêt à partir d’un seul serveur ou pour répartir la charge sur plusieurs serveurs.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Plusieurs forêts, seul les clients AD Azure
![Mono-utilisateur de plusieurs forêts](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

De nombreuses entreprises possèdent des environnements comptant plusieurs forêts d’Active Directory sur site. Il existe diverses raisons pour avoir plus d’une forêt d’Active Directory sur site. Exemples typiques sont des modèles avec les forêts compte-ressource et par conséquent après une fusion ou une acquisition.

Lorsque vous avez plusieurs forêts, toutes les forêts ne doit être accessible par simple connexion AD Azure serveur de sync. Vous n’êtes pas obligé de joindre le serveur à un domaine. Si elle est nécessaire pour accéder à toutes les forêts, le serveur peut être placé dans un réseau DMZ.

L’Assistant d’installation Azure Connect d’annonce offre plusieurs options de consolider d’utilisateurs représentés dans plusieurs forêts. L’objectif est qu’un utilisateur est uniquement représenté qu’une seule fois dans Azure AD. Il existe certaines topologies courantes que vous pouvez configurer dans le chemin d’accès de l’installation personnalisée dans l’Assistant installation. Sélectionnez l’option correspondante qui représente votre topologie sur la page **identifiant de vos utilisateurs**. La consolidation est configurée uniquement pour les utilisateurs. Groupes dupliqués ne sont pas consolidées avec la configuration par défaut.

Topologies courantes sont abordées dans la section suivante : [topologies distinctes](#multiple-forests-separate-topologies)et [Compte-ressource](#multiple-forests-account-resource-forest) [maille pleine](#multiple-forests-full-mesh-with-optional-galsync).

La configuration par défaut dans Azure AD connexion synchronisation suppose :

1. Les utilisateurs ont un seul compte activé et la forêt où se trouve ce compte est utilisée pour authentifier l’utilisateur. Cette hypothèse est pour la synchronisation de mot de passe à la fois et pour la fédération. UserPrincipalName et sourceAnchor/immutableID proviennent de cette forêt.
2. Les utilisateurs ont uniquement une boîte aux lettres.
3. La forêt qui héberge la boîte aux lettres d’un utilisateur possède la meilleure qualité de données pour les attributs visibles dans la liste d’adresses globale Exchange (GAL). S’il n’existe aucune boîte aux lettres de l’utilisateur, puis une forêt permet à contribute de ces valeurs d’attribut.
4. Si vous avez une boîte aux lettres liée, puis il existe également un autre compte dans une autre forêt utilisée pour l’ouverture de session.

Si votre environnement ne correspond pas à ces hypothèses, les événements suivants se produisent :

- Si vous avez plus d’un compte d’actif ou de plusieurs boîtes aux lettres, le moteur de synchronisation sélectionne l’une et l’autre ignore.
- Une boîte aux lettres liée avec aucun autre compte actif n’est pas exportée vers Azure AD. Le compte d’utilisateur n’est pas représenté en tant que membre d’un groupe. Une boîte aux lettres liée dans la synchronisation d’annuaire est toujours représentée sous la forme d’une boîte aux lettres normal. Cette modification est intentionnellement un comportement différent pour mieux prendre en charge les scénarios de plusieurs forêts.

Vous trouverez plus de détails dans la [Présentation de la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Plusieurs forêts, plusieurs serveurs de synchronisation de priorité Azure AD
![Non prise en charge de plusieurs forêts multiples Sync](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Il n’est pas pris en charge pour avoir plus d’un serveur de synchronisation de connexion AD Azure connecté à un seul client d’Azure AD. L’exception est l’utilisation d’un [serveur de test](#staging-server).

### <a name="multiple-forests--separate-topologies"></a>Plusieurs forêts – topologies distinctes
**Les utilisateurs sont représentés qu’une seule fois sur tous les répertoires**

![Une fois les utilisateurs de plusieurs forêts](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Topologies de forêt de multiples séparés](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Dans cet environnement, toutes les forêts locales sont traitées comme des entités distinctes et aucun utilisateur n’est présent dans une autre forêt.
Chaque forêt possède sa propre organisation Exchange et qu’il n’y a aucun GALSync entre les forêts. Cette topologie peut être la situation après une fusion/absorption ou dans une organisation où chaque division est d’exploitation isolé des autres. Ces forêts sont dans la même organisation dans Active Directory Azure et s’affiche avec une liste d’adresses globale unifiée.
Dans cette illustration, chaque objet dans chaque forêt est représentée une seule fois dans le métaverse et agrégée dans le locataire cible AD Azure.

### <a name="multiple-forests--match-users"></a>Plusieurs forêts, les utilisateurs de correspondance
**Identités utilisateur existent sur plusieurs annuaires**

Commun à tous ces scénarios est que distribution et les groupes de sécurité peuvent contenir un mélange d’utilisateurs, les contacts et les FSP (entités de sécurité étrangères)

FSP permettent de représenter des membres d’autres forêts dans un groupe de sécurité en ajoute. Tous les FSP est résolues à l’objet réel dans AD Azure.

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>Plusieurs forêts – maille pleine avec GALSync facultatif
**Il existe des identités utilisateur sur plusieurs annuaires. Correspond à l’utilisation : l’attribut de messagerie**

![Messages des utilisateurs de plusieurs forêts](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Maille pleine de plusieurs forêts](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Une topologie avec maillage complet permet aux utilisateurs et des ressources se trouve dans une forêt et généralement il y aura des approbations bidirectionnelles entre les forêts.

Si Exchange est présent dans plus d’une forêt, il peut éventuellement être local GALSync. Chaque utilisateur est représenté en tant que contact dans toutes les autres forêts. GALSync est généralement implémenté à l’aide de Forefront Identity Manager 2010 ou Microsoft Identity Manager 2016. Azure Connect d’Active Directory ne peut pas être utilisé pour locaux GALSync.

Dans ce scénario, les objets identity sont jointes à l’aide de l’attribut de messagerie. Un utilisateur avec une boîte aux lettres d’une forêt est joint avec les contacts dans les autres forêts.

### <a name="multiple-forests--account-resource-forest"></a>Plusieurs forêts – forêt compte-ressource
**Il existe des identités utilisateur sur plusieurs annuaires. Correspond à l’utilisation : attributs ObjectSID et msExchMasterAccountSID**

![Forêt de multiples utilisateurs ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Forêt de multiples AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Dans une topologie à forêt compte-ressource, vous avez une ou plusieurs forêts de comptes avec les comptes d’utilisateurs actifs. Vous avez également une ou plusieurs forêts de ressources avec des comptes désactivés.

Dans ce scénario, une (ou plusieurs) **forêt ressource** approuve toutes les **forêts de comptes**. La forêt de ressources a généralement un schéma Active Directory étendu avec Exchange et Lync. Tous les services Exchange et Lync ainsi que d’autres services partagés sont situés dans cette forêt. Les utilisateurs disposent d’un compte d’utilisateur désactivé dans cette forêt et la boîte aux lettres est associée à la forêt de comptes.

## <a name="office-365-and-topology-considerations"></a>Office 365 et considérations relatives à la topologie
Certaines charges de travail Office 365 ont certaines restrictions pour les topologies prises en charge. Si vous prévoyez d’utiliser un de ces, lisez la rubrique topologies prises en charge pour la charge de travail.

Charge de travail |  
--------- | ---------
Exchange en ligne | S’il existe plus d’une organisation Exchange sur les sites (c'est-à-dire Exchange a été déployé à plus d’une forêt), vous devez utiliser Exchange 2013 SP1 ou une version ultérieure. Détails se trouvent ici : [Déploiement hybride avec plusieurs forêts Active Directory](https://technet.microsoft.com/library/jj873754.aspx)
Skype pour entreprise | Si vous utilisez plusieurs forêts sur site, la topologie à forêt compte-ressource uniquement est pris en charge. Détails pour les topologies prises en charge se trouve ici : [des exigences environnementales pour Skype pour Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx)

## <a name="staging-server"></a>Serveur intermédiaire
![Serveur intermédiaire](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure Connect de publicité, prend en charge l’installation d’un second serveur en **mode de mise en attente**. Un serveur dans ce mode, lit les données de tous les annuaires connectés mais n’écrit pas dans les annuaires connectés. Il utilise le cycle de synchronisation normale et a donc une copie mise à jour des données d’identité. En cas d’urgence, lorsque le serveur principal ne vous peut basculer sur le serveur intermédiaire. Pour cela, utilisez l’Assistant Azure Connect d’Active Directory. Ce deuxième serveur peut être situé de préférence dans un centre de données différente dans la mesure où aucune infrastructure n’est partagé avec le serveur principal. Vous devez copier manuellement les changements de configuration sur le serveur principal vers le second serveur.

Un serveur intermédiaire peut également être utilisé pour tester une nouvelle configuration personnalisée et les répercussions sur vos données. Vous pouvez prévisualiser les modifications et ajuster la configuration. Lorsque vous êtes satisfait de la nouvelle configuration, vous pouvez rendre le serveur intermédiaire le serveur actif et la valeur de l’ancien serveur active en mode de mise en attente.

Cette méthode peut également être utilisée pour remplacer le serveur de synchronisation active. Préparer le nouveau serveur et définir dans le mode de mise en attente. Assurez-vous qu’il est en bon état, désactiver mode (rendant actif), la zone de transit et d’arrêter le serveur en cours.

Il est possible d’avoir plus d’un serveur intermédiaire lorsque vous souhaitez créer plusieurs sauvegardes dans les centres de données différents.

## <a name="multiple-azure-ad-tenants"></a>Différents utilisateurs AD Azure
Microsoft vous recommande d’avoir un mono-utilisateur dans Azure AD pour une organisation.
Avant que vous prévoyez d’utiliser les différents utilisateurs AD Azure, ces rubriques traitent des scénarios courants, ce qui vous permet d’utiliser un seul client.

Rubrique |  
--------- | ---------
À l’aide d’unités administratives de délégation | [Gestion des unités d’administration dans Active Directory Azure](active-directory-administrative-units-management.md)

![Clients de multiples forêts multiples](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Il existe une relation 1:1 entre un serveur de synchronisation d’Azure Connect de AD et un locataire AD Azure. Pour chaque client AD Azure, vous avez besoin d’une installation de serveur de synchronisation Azure Connect d’AD. Les instances de clients AD Azure sont par conception isolée et les utilisateurs dans un ne peut pas voir les utilisateurs dans les autres clients. Si cette séparation est prévue, puis il s’agit d’une configuration prise en charge, mais dans le cas contraire, vous devez utiliser la simple modèle de clients AD Azure.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Chaque objet une seule fois dans un locataire Azure AD
![Une seule forêt filtrée](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Dans cette topologie, un serveur de synchronisation d’Azure Connect de publicité est connecté à chaque client AD Azure. Les serveurs de synchronisation d’Azure Connect de AD doivent être configurés pour chaque donc un ensemble mutuellement exclusif d’objets pour fonctionner sur le filtrage. Vous pouvez par exemple limiter l’étendue de chaque serveur d’un domaine particulier ou d’une unité d’organisation. Un serveur DNS ne peut être inscrite que dans un seul les clients AD Azure. L’UPN des utilisateurs dans les locaux AD doit utiliser des espaces de noms distincts ainsi. Par exemple, dans l’image ci-dessus trois UPN différents suffixes sont enregistrés dans les locaux AD : wingtiptoys.com contoso.com et fabrikam.com. Les utilisateurs dans chaque domaine Active Directory sur site utiliser un espace de noms différent.

Il n’est aucun GALsync entre les instances de clients AD Azure. Le carnet d’adresses dans Exchange Online et Skype pour les utilisateurs professionnels uniquement présente dans le même client.

Cette topologie comprend les éléments suivants restrictions sinon les scénarios pris en charge :

- Seul les locataires AD Azure permettent à Exchange hybride avec Active Directory sur site.
- Les périphériques Windows 10 ne peuvent être associés à un locataire d’Azure AD.

L’exigence d’ensemble mutuellement exclusive d’objets s’applique également pour l’écriture différée. Certaines fonctionnalités d’écriture différée ne sont pas pris en charge avec cette topologie dans la mesure où ces fonctions supposent une seule configuration sur site :

-   Écriture différée du groupe avec la configuration par défaut
-   Écriture différée de périphérique

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Chaque objet plusieurs fois dans un locataire Azure AD
![Non prise en charge des clients de multiples d’une seule forêt](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Connecteurs de multiples d’une seule forêt non pris en charge](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- Il est pris en charge pour le même utilisateur sur plusieurs annonces Azure locataires de synchronisation.
- Il est non pris en charge pour modifier une configuration pour afficher les utilisateurs dans une annonce Azure en tant que contacts dans un autre client d’Azure AD.
- Il est pris en charge pour modifier la synchronisation Azure AD Connect pour vous connecter à différents utilisateurs AD Azure.

### <a name="galsync-by-using-writeback"></a>GALsync à l’aide de d’écriture différée
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Les locataires AD Azure sont par conception isolée.

- Il est pris en charge pour modifier la configuration de la synchronisation d’Azure AD Connect pour lire des données à partir d’un autre client d’Azure AD.
- Il est pris en charge pour exporter les utilisateurs en tant que contacts vers un autre sur site AD à l’aide de la synchronisation d’Azure Connect d’AD.

### <a name="galsync-with-on-premises-sync-server"></a>GALsync avec le serveur de synchronisation en local
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Il est pris en charge pour utiliser FIM2010/MIM2016 sur site aux utilisateurs GALsync entre deux organisations Exchange. Les utilisateurs d’une organisation s’affiche en tant qu’utilisateurs/contacts à l’étranger de l’autre organisation. Ces annonces locales différentes peuvent être ensuite synchronisés à leurs propres locataires AD Azure.

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment installer Azure Connect AD pour ces scénarios, consultez [installation personnalisée de Azure Connect d’Active Directory](./connect/active-directory-aadconnect-get-started-custom.md).

Pour en savoir plus sur la configuration [d’Azure AD connexion de synchronisation](active-directory-aadconnectsync-whatis.md) .

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
