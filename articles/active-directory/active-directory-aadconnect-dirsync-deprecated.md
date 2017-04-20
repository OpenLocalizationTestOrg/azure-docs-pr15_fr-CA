<properties
    pageTitle="Mise à niveau à partir de la synchronisation d’annuaire et de la synchronisation Active Directory Azure | Microsoft Azure"
    description="Décrit comment mettre à niveau à partir de la synchronisation d’annuaire et Azure AD Sync pour Azure Connect d’Active Directory."
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


# <a name="upgrade-windows-azure-active-directory-sync-dirsync-and-azure-active-directory-sync-azure-ad-sync"></a>Mise à niveau de la synchronisation de Active Directory de Windows Azure (« synchronisation ») et Azure Active Directory synchronisation (« Azure AD Sync »)
Azure Connect d’Active Directory est la meilleure façon de connecter votre répertoire local avec AD Azure et Office 365. C’est le moment idéal pour mettre à niveau vers Azure Connect de AD Windows Azure Active Directory Sync (DirSync) ou Azure AD Sync que ces outils sont maintenant désapprouvées et fin du support le 13 avril 2017.

Les outils de synchronisation de deux identités qui sont obsolètes ont été proposées pour les clients de forêt unique (DirSync) et pour plusieurs forêts et d’autres avancées clients (Azure AD Sync). Ces outils ont été remplacés par une solution unique, qui est disponible pour tous les scénarios : Azure Connect d’Active Directory. Il offre de nouvelles fonctionnalités et améliorations de la fonctionnalité prise en charge de nouveaux scénarios. Pour pouvoir continuer à synchroniser vos données d’identité sur site AD Azure et Office 365, nous recommandons vivement que vous mettez à niveau vers Azure Connect d’AD.

La dernière version de synchronisation d’annuaire a été publiée en juillet 2014, et la dernière version de Azure AD Sync a été publiée en mai 2015.

## <a name="what-is-azure-ad-connect"></a>Nouveautés d’Azure AD Connect
La connexion AD Azure est le successeur de synchronisation d’annuaire et Azure AD Sync. Il combine ces deux prises en charge à tous les scénarios. Vous pouvez en savoir plus sur elle dans [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Planification de l’amortissement

Date | Commentaire
 --- | ---
13 avril 2016 | Windows Azure Active Directory Sync (« synchronisation ») et Microsoft Azure Active Directory Sync (« Azure AD Sync ») sont annoncés comme étant deprecated.
13 avril 2017. | Fin du support. Les clients ne seront plus en mesure d’ouvrir un cas de support sans tout d’abord mettre à niveau vers Azure Connect d’AD.

## <a name="how-to-transition-to-azure-ad-connect"></a>Comment la transition vers Azure Connect de publicité
Si vous utilisez la synchronisation d ' annuaire, il existe deux méthodes que vous pouvez mettre à niveau : déploiement parallèle et de mise à niveau sur place. Une mise à niveau est recommandée pour la plupart des clients, et si vous avez un récent système d’exploitation et moins de 50 000 objets. Dans les autres cas, il est recommandé d’effectuer un déploiement parallèle où la configuration de la synchronisation d’annuaire est déplacée vers un nouveau serveur exécutant Azure Connect d’Active Directory.

Si vous utilisez la synchronisation Active Directory Azure une mise à niveau sur place est recommandé. Si vous le souhaitez, il est possible d’installer un nouveau serveur Azure Connect de publicité en parallèle et d’effectuer une migration de swing à partir de votre serveur Azure AD Sync pour Azure Connect d’Active Directory.

Solution | Scénario
----- | -----
[Mise à niveau à partir de la synchronisation d’annuaire](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Si vous disposez d’un serveur de synchronisation d’annuaire existant déjà en cours d’exécution.</li>
[Mise à niveau à partir de la synchronisation Active Directory Azure](active-directory-aadconnect-upgrade-previous-version.md)| <li>Si vous vous déplacez d’Azure AD Sync.</li>

Si vous voulez voir comment faire une mise à niveau à partir de la synchronisation d’annuaire pour établir la connexion AD Azure, consultez cette vidéo de Channel 9 :

> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]

## <a name="faq"></a>FAQ
**Q : j’ai reçu une notification par e-mail à partir de l’équipe Azure et/ou un message à partir du centre de messages d’Office 365, mais j’utilise une connexion.**  
La notification a été également envoyée aux clients à l’aide d’Azure AD Connect avec un numéro de version 1.0. \*.0 (à l’aide d’une version pre-1.1). Microsoft recommande aux clients de rester au courant des versions d’Azure Connect d’Active Directory. 1.1 que la fonctionnalité de [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) rend très facile de toujours avoir une version récente de Azure Connect d’Active Directory installé.

**Q : sera/Azure de synchronisation d’annuaire AD Sync interrompre le 13 avril 2017 ?**  
N° La date de lorsqu’il ne sera plus en mesure de communiquer avec Active Directory Azure est annoncée à une date ultérieure. Vous pourrez trouver ces informations dans cette rubrique, lorsqu’elles sont disponibles.

**Q : quelles versions de synchronisation d’annuaire puis-je mettre à niveau ?**  
Il est pris en charge pour la mise à niveau à partir de n’importe quelle version de synchronisation d’annuaire en cours d’utilisation.

**Q : que se passe-t-il sur le connecteur Active Directory Azure pour FIM/MIM ?**  
Le connecteur Active Directory Azure pour FIM/MIM n’a **pas** été désapprouvées. Il est à la **fonction Figer**; Aucune nouvelle fonctionnalité n’est ajoutée et il ne reçoit aucun correctif. Microsoft recommande aux clients à l’aide de la planification de déplacer à partir de celui-ci pour établir la connexion AD Azure. Il est fortement recommandé de ne pas démarrer les nouveaux déploiements de l’utiliser. Ce connecteur sera annoncé déconseillée à l’avenir.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
