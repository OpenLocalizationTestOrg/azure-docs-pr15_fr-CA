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


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publier des applications sur des réseaux distincts et des emplacements à l’aide de groupes de connecteur

> [AZURE.SELECTOR]
- [Azure portal](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure portal classique](active-directory-application-proxy-connectors.md)


Connecteur groupes sont utiles pour divers scénarios, notamment :

- Sites avec plusieurs centres de données interconnectés. Dans ce cas, vous souhaitez conserver autant le trafic dans le centre de données que possible, car les liens des centres de données sont généralement coûteux et lente. Vous pouvez déployer des connecteurs dans chaque centre de données pour seulement les applications qui se trouvent dans le centre de données. Cette approche réduit les liens inter-centre de données et fournit une expérience entièrement transparente pour vos utilisateurs.
- Gestion des applications installées sur des réseaux isolés qui ne font pas partie du réseau principal de l’entreprise. Vous pouvez utiliser des groupes de connecteur pour installer des connecteurs dédiés sur les réseaux isolés également isoler les applications sur le réseau.
- Pour les applications installées sur IaaS pour accès de nuage, groupes de connecteur fournissent un service commun pour sécuriser l’accès à toutes les applications. Groupes de connecteur ne créer des dépendances supplémentaires sur votre réseau d’entreprise ou fragmenter l’expérience de l’application. Les connecteurs peuvent être installés sur chaque centre de données de nuage et servent uniquement les applications qui résident dans ce réseau. Vous pouvez installer plusieurs connecteurs pour atteindre une haute disponibilité.
- Prise en charge pour les environnements à plusieurs forêts dans lesquelles des connecteurs spécifiques peuvent être déployés par forêt et définies pour répondre à des applications spécifiques.
- Les groupes de connecteur peuvent être utilisés dans les sites de reprise après sinistre pour détecter soit de basculement sur incident ou en tant que sauvegarde pour le site principal.
- Connecteur groupes peuvent également être utilisés pour répondre à plusieurs sociétés à partir d’un client unique.

## <a name="prerequisite-create-your-connectors"></a>Au préalable : Créer vos connecteurs
Pour regrouper vos connecteurs, vous devez vérifiez que vous avez [installé plusieurs connecteurs](active-directory-application-proxy-enable.md)et que vous nommez les pour les regroupez ensuite. Enfin, vous devez les affecter à des applications spécifiques.

## <a name="step-1-create-connector-groups"></a>Étape 1 : Créer des groupes de connecteur
Vous pouvez créer autant de groupes de connecteur que vous le souhaitez. Création de groupes de connecteur s’effectue dans le portail classique Azure.

1. Sélectionnez le répertoire et cliquez sur **configurer**.  
    ![Proxy d’application, configurer la capture d’écran, cliquez sur Gérer les groupes de connecteur](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. Sous Proxy de l’Application, cliquez sur **Gérer les groupes de connecteur** et créer un nouveau groupe de connecteur par l’attribution d’un nom au groupe.  
    ![Connecteur de proxy d’application des groupes de capture d’écran - nom nouveau groupe](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Étape 2 : Affecter des connecteurs à vos groupes
Une fois les groupes de connecteur sont créés, vous pouvez déplacer les connecteurs vers le groupe approprié.

1. **Proxy de l’Application**, cliquez sur **Gérer les connecteurs**.
2. Sous **groupe**, sélectionnez le groupe souhaité pour chaque connecteur. Les connecteurs peut prendre jusqu'à 10 minutes pour devenir active dans le nouveau groupe.  
    ![Capture d’écran de connecteurs application proxy - sélectionner un groupe à partir du menu déroulant](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Étape 3 : Attribuer des applications à vos groupes de connecteur
La dernière étape consiste à définir chaque application au groupe de connecteur il servira.

1. Dans le portail Azure classique, dans votre répertoire, sélectionnez l’Application que vous souhaitez affecter au groupe et cliquez sur **configurer**.
2. Sous **groupe de connecteur**, sélectionnez le groupe que vous souhaitez que l’application à utiliser. Cette modification est appliquée immédiatement.  
    ![Application proxy connecteur groupe capture d’écran - Sélectionner un groupe à partir du menu déroulant](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## <a name="see-also"></a>Voir aussi

- [Activer le Proxy d’Application](active-directory-application-proxy-enable.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Résoudre les problèmes que vous rencontrez avec le Proxy de l’Application](active-directory-application-proxy-troubleshoot.md)

Pour les dernières informations et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
