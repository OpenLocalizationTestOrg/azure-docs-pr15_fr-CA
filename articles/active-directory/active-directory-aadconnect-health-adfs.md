
<properties
    pageTitle="À l’aide d’Active Directory Azure se connecter la santé avec AD FS | Microsoft Azure"
    description="Voici la page de publicité Azure se connecter la santé comment surveiller vos locaux ADFS."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-with-ad-fs"></a>À l’aide d’Active Directory Azure se connecter la santé avec AD FS
La documentation suivante est spécifique à l’analyse de votre infrastructure ADFS avec l’état connexion Azure AD. Pour plus d’informations sur la surveillance de connexion AD Azure (synchronisation) avec état de connexion AD Azure, consultez [AD à l’aide de Azure se connecter la santé pour la synchronisation](active-directory-aadconnect-health-sync.md). En outre, pour plus d’informations sur la surveillance des Services de domaine Active Directory avec l’état connexion Azure AD, consultez [AD à l’aide de Azure se connecter la santé avec les services AD DS](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Alertes pour AD FS
La section Azure AD connecter les alertes de santé vous fournit la liste des alertes actives. Chaque alerte inclut des informations pertinentes, des étapes de résolution et des liens vers la documentation associée.

Vous pouvez double-cliquer sur une alerte active ou résolue, pour ouvrir une nouvelle lame avec des informations supplémentaires, étapes à suivre pour résoudre l’alerte et des liens vers la documentation pertinente. Vous pouvez également afficher des données historiques sur les alertes qui ont été résolus dans le passé.

![Annonce Azure se connecter le portail sur la santé](./media/active-directory-aadconnect-health/alert2.png)



## <a name="usage-analytics-for-ad-fs"></a>Analytique de l’utilisation d’ADFS
Azure AD connecter la santé l’utilisation Analytique analyse le trafic d’authentification de vos serveurs de fédération. Vous pouvez double-cliquer sur la zone analytique de l’utilisation, pour ouvrir la lame utilisation analytique, qui affiche plusieurs métriques et regroupements.

>[AZURE.NOTE] Pour utiliser l’utilisation Analytique avec AD FS, vous devez vous assurer que AD FS l’audit est activé. Pour plus d’informations, consultez [Activation de l’audit pour AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).

![Annonce Azure se connecter le portail sur la santé](./media/active-directory-aadconnect-health/report1.png)

Pour sélectionner des mesures supplémentaires pour évaluer, de spécifier une plage de temps, ou pour modifier le regroupement, avec le bouton droit sur le graphique d’utilisation analytique et sélectionnez Modifier le graphique. Puis vous pouvez spécifier la plage de temps une mesure différente et sélectionnez Modifier le regroupement. Vous pouvez afficher la distribution du trafic d’authentification basée sur les « mesures » différentes et regrouper chaque mesure en utilisant les paramètres pertinents « Grouper par » décrits dans le tableau suivant :

| Métrique | Regrouper par | Le groupement signifie et pourquoi il est utile ? |
| ------ | -------- | -------------------------------------------- |
| Nombre total de demandes : Le nombre total de demandes traitées par le service de fédération | Tous les | Indique le nombre de nombre total de demandes sans regroupement. |
|  | Application | Le nombre total de demandes en fonction de la partie de confiance ciblée des groupes. Ce regroupement est utile de comprendre l’application qui reçoit le pourcentage du trafic total. |
|  | Serveur | Regroupe le nombre total de demandes basé sur le serveur qui a traité la demande. Ce regroupement est utile pour comprendre la répartition de la charge du trafic total. |
|  | Jointure de l’espace de travail | Le nombre total de demandes selon si elles proviennent de périphériques rejoint un espace de travail (connu) des groupes. Ce regroupement est utile pour comprendre si vos ressources sont accessibles à l’aide de périphériques qui sont inconnus de l’infrastructure d’identité. |
|  | Méthode d’authentification | Le nombre total de demandes selon la méthode d’authentification utilisée pour l’authentification des groupes. Ce regroupement est utile pour comprendre la méthode d’authentification commune utilisée pour l’authentification. Voici les méthodes d’authentification possibles <ol> <li>Authentification intégrée de Windows (Windows)</li> <li>Basée sur les formulaires d’authentification (formulaires)</li> <li>SSO (Single Sign On)</li> <li>X509 de certificat d’authentification (certificats)</li> <br>Si les serveurs de fédération reçoivent la demande avec un Cookie d’authentification unique, cette demande est comptée comme SSO (Single Sign On). Dans ce cas, si le cookie est valide, l’utilisateur n’est pas invité à fournir des informations d’identification et obtient un accès transparent à l’application. Ce problème est courant si vous disposez de plusieurs parties de confiance protégés par les serveurs de fédération. |
|  | Emplacement réseau | Le nombre total de demandes basé sur l’emplacement réseau de l’utilisateur des groupes. Il peut s’agir soit d’intranet ou extranet. Ce regroupement est utile de savoir quel est le pourcentage du trafic provenant de l’intranet et extranet. |
| Nombre total de demandes a échoué : Le nombre total Échec de demandes traitées par le service de fédération. <br> (Cette mesure est uniquement disponible sur AD FS pour Windows Server 2012 R2)| Type d’erreur | Affiche le nombre d’erreurs en fonction des types d’erreurs prédéfinis. Ce regroupement est utile de comprendre les types courants d’erreurs. <ul><li>Incorrect nom d’utilisateur ou mot de passe : des erreurs en raison de mot de passe ou nom d’utilisateur incorrect.</li> <li>« Verrouillage extranet » : les défaillances dues à des demandes provenant d’un utilisateur a été verrouillé de l’extranet </li><li> « Mot de passe arrivé à expiration » : les défaillances dues à des utilisateurs qui se connectent avec un mot de passe a expiré.</li><li>« Compte de désactivé » : défaillances des utilisateurs ouvrant une session avec un compte désactivé.</li><li>« Authentification de périphérique » : les défaillances dues à des utilisateurs ne parvient pas à s’authentifier à l’aide de l’authentification du périphérique.</li><li>« Authentification de certificat utilisateur » : les défaillances dues à des utilisateurs ne parvient pas à s’authentifier en raison d’un certificat non valide.</li><li>« AMF » : défaillances utilisateur ne parvient pas à s’authentifier à l’aide d’une authentification multifactorielle.</li><li>« Autres informations d’identification » : « Autorisation d’émission » : les défaillances dues à des erreurs d’autorisation.</li><li>« Délégation d’émission » : les défaillances dues à des erreurs de délégation d’émission.</li><li>« Acceptation de jeton » : les défaillances dues à ADFS rejetant le jeton à partir d’un fournisseur d’identité tiers.</li><li>« Protocole » : échec en raison d’erreurs de protocole.</li><li>« Inconnu » : intercepter toutes les. Les autres échecs qui n’entrent pas dans les catégories définies.</li> |
|  | Serveur | Regroupe les erreurs basés sur le serveur. Ce regroupement est utile pour comprendre la répartition des erreurs sur les serveurs. Distribution irrégulière peut être un indicateur d’un serveur dans un état défectueux. |
|  | Emplacement réseau | Regroupe les erreurs en fonction de l’emplacement réseau des demandes (vs d’intranet extranet). Ce regroupement est utile de comprendre le type de demandes qui ont échoué. |
|  | Application | Regroupe les échecs en fonction de l’application cible (partie de confiance). Ce regroupement est utile de comprendre quelle application ciblée voit le nombre le plus d’erreurs. |
| Nombre d’utilisateurs : le nombre moyen d’utilisateurs actifs dans le système | Tous les | Cette mesure fournit un décompte du nombre moyen d’utilisateurs à l’aide du service de fédération dans la tranche de temps sélectionné. Les utilisateurs ne sont pas regroupées. <br>La moyenne dépend de la tranche de temps sélectionnée. |
|  | Application | Le nombre moyen d’utilisateurs en fonction de l’application cible (partie de confiance) des groupes. Ce regroupement est utile de connaître le nombre d’utilisateurs est à l’aide de l’application. |


## <a name="performance-monitoring-for-ad-fs"></a>Analyse des performances pour ADFS
Azure se connecter santé performances analyse AD fournit des informations d’analyse des métriques. Sélection de la zone de surveillance, d’ouvre une nouvelle lame avec des informations détaillées sur les mesures.


![Annonce Azure se connecter le portail sur la santé](./media/active-directory-aadconnect-health/perf1.png)


En sélectionnant l’option de filtre en haut de la lame, vous pouvez filtrer par serveur pour voir les métriques d’un serveur individuel. Pour modifier la métrique, avec le bouton droit sur le graphique de contrôle sous la lame de contrôle et sélectionnez Modifier le graphique. Puis, à partir de la nouvelle blade qui s’ouvre, vous pouvez sélectionner des mesures supplémentaires dans la liste déroulante et spécifier une plage de temps pour afficher les données de performances.

## <a name="reports-for-ad-fs"></a>Rapports pour AD FS
Santé de connexion AD Azure fournit des rapports sur l’activité et des performances de AD FS. Ces rapports aident les administrateurs sur leurs activités sur leurs serveurs ADFS.

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>Top 50 utilisateurs avec les échecs de connexion nom d’utilisateur/mot de passe

Une des raisons courantes pour une demande d’authentification sur un serveur AD FS est une demande avec les informations d’identification non valides, c'est-à-dire un nom d’utilisateur incorrect ou un mot de passe. Se produit généralement aux utilisateurs en raison de fautes de frappe, mots de passe oubliés ou mots de passe complexes.

Mais il existe d’autres raisons pouvant entraîner un nombre inattendu de demandes traitées par vos serveurs AD FS, telles que : une application que l’expirent des informations d’identification utilisateur de caches et les informations d’identification ou à un utilisateur malveillant tente de vous connecter à un compte avec une série de mots de passe bien connus. Ces deux exemples représentent des raisons valables pouvant conduire à l’augmentation des demandes.

Azure santé AD se connecter pour ADFS fournit un rapport sur les 50 utilisateurs avec nom d’utilisateur non valide ou le mot de passe, les tentatives de connexion infructueuses. Cet état est obtenu par traitement des événements d’audit générés par tous les serveurs ADFS dans les batteries de serveurs

![Annonce Azure se connecter le portail sur la santé](./media/active-directory-aadconnect-health-adfs/report1a.png)

Dans ce rapport, vous avez un accès facile aux éléments d’information suivants :

- Nombre total de demandes ayant échoué, avec le nom d’utilisateur/mot de passe incorrect au cours des 30 derniers jours
- Nb. moyen d’utilisateurs qui ont échoué avec une connexion nom d’utilisateur/mot de passe erroné par jour.


Cliquez sur cette partie pour accéder à la blade d’état principal qui fournit des détails supplémentaires. Cette blade comprend un graphique avec des informations de tendance pour aider à établir une ligne de base sur les requêtes avec un mot de passe ou nom d’utilisateur incorrect. En outre, elle fournit la liste des 50 premiers utilisateurs avec le nombre de tentatives infructueuses.

Le graphique fournit les informations suivantes :

- Le nombre total d’échecs de connexion en raison d’un nom d’utilisateur/mot de passe incorrect sur une base par jour.
- Le nombre total d’utilisateurs uniques qui ont échoué de connexions d’accès sur une base par jour.
- Adresse IP du client de pour la dernière demande

![Annonce Azure se connecter le portail sur la santé](./media/active-directory-aadconnect-health-adfs/report3a.png)

Le rapport fournit les informations suivantes :

| Élément de rapport | Description
| ------ | -------- |
|ID de l’utilisateur| Affiche l’ID de l’utilisateur qui a été utilisé. Cette valeur est ce que l’utilisateur a tapé, qui dans certains cas est le mauvais utilisateur ID en cours d’utilisation.|
|Tentatives ayant échoué| Affiche le nombre total de tentatives infructueuses pour cet ID d’utilisateur spécifique. La table est triée avec le plus grand nombre de tentatives ayant échoué dans l’ordre décroissant.|
|Dernier échec| Indique l’horodatage lorsque le dernier échec s’est produit.
|Dernier échec IP | Affiche l’adresse IP du Client à partir de la dernière demande incorrecte.|



>[AZURE.NOTE] Ce rapport est automatiquement mis à jour une fois toutes les deux heures avec les nouvelles informations recueillies dans ce délai. En conséquence, les tentatives de connexion dans les deux dernières heures ne peut-être pas inclus dans le rapport.



## <a name="related-links"></a>Liens connexes

* [Annonce Azure se connecter à la santé](active-directory-aadconnect-health.md)
* [AD Azure se connecter l’Installation de l’Agent de la santé](active-directory-aadconnect-health-agent-install.md)
* [AD Azure se connecter à des opérations de santé](active-directory-aadconnect-health-operations.md)
* [À l’aide de la santé se connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Annonce Azure se connecter le Forum aux questions sur la santé](active-directory-aadconnect-health-faq.md)
* [Annonce Azure se connecter à l’historique de Version de la santé](active-directory-aadconnect-health-version-history.md)
