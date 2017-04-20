<properties
    pageTitle="Utilisation des connecteurs de Proxy de l’Application AD Azure | Microsoft Azure"
    description="Explique comment créer et gérer des groupes de connecteurs dans le Proxy d’Application Azure AD."
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
    ms.date="09/09/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>Publier des applications sur des réseaux distincts et des emplacements à l’aide de groupes de connecteur - version d’évaluation

> [AZURE.SELECTOR]
- [Azure portal](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure portal classique](active-directory-application-proxy-connectors.md)


Connecteur groupes sont utiles pour divers scénarios, notamment :

- Sites avec plusieurs centres de données interconnectés. Dans ce cas, vous souhaitez conserver autant le trafic dans le centre de données que possible, car les liens des centres de données sont onéreuses et plus lentes. Vous pouvez déployer des connecteurs dans chaque centre de données pour seulement les applications qui se trouvent dans le centre de données. Cette approche réduit les liens inter-centre de données et fournit une expérience entièrement transparente pour vos utilisateurs.
- Gestion des applications installées sur des réseaux isolés qui ne font pas partie du réseau principal de l’entreprise. Vous pouvez utiliser des groupes de connecteur pour installer des connecteurs dédiés sur les réseaux isolés également isoler les applications sur le réseau.
- Pour les applications installées sur IaaS pour accès de nuage, groupes de connecteur fournissent un service commun pour sécuriser l’accès à toutes les applications. Groupes de connecteur ne créer des dépendances supplémentaires sur votre réseau d’entreprise ou fragmenter l’expérience de l’application. Les connecteurs peuvent être installés sur chaque centre de données de nuage et servent uniquement les applications qui résident dans ce réseau. Vous pouvez installer plusieurs connecteurs pour atteindre une haute disponibilité.
- Prise en charge pour les environnements à plusieurs forêts dans lesquelles des connecteurs spécifiques peuvent être déployés par forêt et définies pour répondre à des applications spécifiques.
- Les groupes de connecteur peuvent être utilisés dans les sites de reprise après sinistre pour détecter soit de basculement sur incident ou en tant que sauvegarde pour le site principal.
- Connecteur groupes peuvent également être utilisés pour répondre à plusieurs sociétés à partir d’un client unique.

## <a name="prerequisite-create-your-connectors"></a>Au préalable : Créer vos connecteurs
Pour regrouper vos connecteurs, vous devez vous assurer vous [plusieurs connecteurs installés](active-directory-application-proxy-enable.md). Lorsque vous installez un connecteur, il rejoint automatiquement le groupe **par défaut** de connecteur.

## <a name="step-1-create-connector-groups"></a>Étape 1 : Créer des groupes de connecteur
Vous pouvez créer autant de groupes de connecteur que vous le souhaitez. Création de groupes de connecteur s’effectue dans le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Azure Active Directory** pour accéder au tableau de bord de gestion pour votre répertoire. À partir de là, sélectionnez **les applications d’entreprise** > **proxy de l’Application**.

2. Cliquez sur le bouton **Groupes de connecteur** . La lame du nouveau groupe de connecteur s’affiche.

3. Nommez votre nouveau groupe de connecteur, puis utilisez le menu déroulant pour sélectionner les connecteurs appartiennent à ce groupe.

4. Sélectionnez **Enregistrer** lorsque votre connecteur de groupe est terminée.

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Étape 2 : Attribuer des applications à vos groupes de connecteur
La dernière étape consiste à définir chaque application au groupe de connecteur il servira.

1. Dans le tableau de bord de gestion de votre répertoire, sélectionnez **les applications d’entreprise** > **toutes les applications** > l’application que vous souhaitez affecter à un groupe de connecteur > **Proxy de l’Application**.
2. Sous **groupe de connecteur**, utilisez le menu déroulant pour sélectionner le groupe que vous souhaitez que l’application à utiliser.
3. Cliquez sur **Enregistrer** pour appliquer la modification.


## <a name="see-also"></a>Voir aussi

- [Activer le Proxy d’Application](active-directory-application-proxy-enable.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Résoudre les problèmes que vous rencontrez avec le Proxy de l’Application](active-directory-application-proxy-troubleshoot.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
