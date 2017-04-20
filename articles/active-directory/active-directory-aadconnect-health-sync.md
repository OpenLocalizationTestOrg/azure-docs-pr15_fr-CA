
<properties
    pageTitle="À l’aide de la santé se connecter Azure AD avec synchronisation | Microsoft Azure"
    description="Il s’agit de la page AD Azure se connecter la santé qui va expliquer comment surveiller Azure Connect de AD sync."
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

# <a name="using-azure-ad-connect-health-for-sync"></a>À l’aide de la santé se connecter Azure AD pour la synchronisation
La documentation suivante est spécifique à l’analyse d’Azure AD de connexion (synchronisation) avec l’état connexion Azure AD.  Pour plus d’informations sur la surveillance AD FS avec état de connexion AD Azure consultez [AD à l’aide de Azure se connecter la santé avec AD FS](active-directory-aadconnect-health-adfs.md). En outre, pour plus d’informations sur la surveillance des Services de domaine Active Directory avec l’état connexion Azure AD voir [AD à l’aide de Azure se connecter la santé avec les services AD DS](active-directory-aadconnect-health-adds.md).

![Annonce Azure se connecter la santé pour la synchronisation](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertes pour Azure AD connecter la santé pour la synchronisation
L’Azure AD connecter alertes de fonctionnement pour la section synchronisation vous fournit la liste des alertes actives. Chaque alerte inclut des informations pertinentes, des étapes de résolution et des liens vers la documentation associée. En sélectionnant une alerte active ou résolue, vous verrez une nouvelle lame avec des informations supplémentaires, ainsi que les étapes à que suivre pour résoudre l’alerte et des liens vers la documentation supplémentaire. Vous pouvez également afficher des données historiques sur les alertes qui ont été résolus dans le passé.

En sélectionnant une alerte que vous êtes fournie avec des informations supplémentaires ainsi que les étapes que vous pouvez prendre pour résoudre l’alerte et les liens vers la documentation supplémentaire.

![Erreur de synchronisation AD Connect Azure](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Évaluation limitée d’alertes
Si Azure Connect de AD n’utilise pas la configuration par défaut (par exemple, si le filtrage de l’attribut est modifié à partir de la configuration par défaut pour une configuration personnalisée), puis l’agent d’intégrité de connexion AD Azure pas télécharger les événements d’erreur liés à Azure Connect d’Active Directory.

Cela limite l’évaluation des alertes par le service. Devrait s’affiche une bannière indiquant cette condition dans le portail Azure sous votre service.

![Annonce Azure se connecter la santé pour la synchronisation](./media/active-directory-aadconnect-health-sync/banner.png)

Vous pouvez le modifier en cliquant sur « Paramètres » qui permet à agent de santé de connexion AD Azure télécharger tous les journaux d’erreurs.

![Annonce Azure se connecter la santé pour la synchronisation](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Aperçu de la synchronisation
Souvent les administrateurs veulent le temps nécessaire à la synchronisation des modifications à Active Directory Azure et la quantité de modifications effectuées. Cette fonctionnalité offre un moyen facile de représenter ceci à l’aide de la sous graphes :   

- Latence des opérations de synchronisation
- Tendance de modification d’objet

### <a name="sync-latency"></a>Latence de synchronisation
Cette fonction fournit une tendance de graphique de la latence des opérations sync (importation, exportation, etc.) pour les connecteurs.  Cela offre un moyen rapide et facile à comprendre non seulement le temps de latence de vos opérations (davantage si vous disposez d’un grand ensemble de modifications en cours), mais aussi un moyen de détecter les anomalies dans le temps de latence qui peut nécessiter un examen supplémentaire.

![Latence de synchronisation](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Par défaut, seulement la latence de le « Exportation » pour le connecteur Active Directory Azure s’affiche.  Pour voir davantage d’opérations sur le connecteur, ou pour afficher les opérations à partir des autres connecteurs, un clic droit sur le graphique, sélectionnez Modifier le graphique ou cliquez sur le bouton « Modifier de latence Chart » et choisissez les connecteurs et l’opération spécifique.

### <a name="sync-object-changes"></a>Modifications de l’objet Sync
Cette fonctionnalité fournit une graphique tendance du nombre de modifications qui sont évaluées et exportés vers Azure AD.  Aujourd'hui, il est difficile d’essayer de rassembler ces informations dans les journaux de synchronisation.  Le graphique vous donne, non seulement un moyen plus simple de surveiller le nombre de modifications qui se produisent dans votre environnement, mais aussi visualiser les défaillances qui se produisent.

![Latence de synchronisation](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Rapport d’erreur de synchronisation de niveau objet (aperçu)
Cette fonctionnalité fournit un rapport sur les erreurs de synchronisation qui peuvent se produire lorsque les données d’identité sont synchronisées entre Windows Server Active Directory et AD Azure à l’aide d’Azure Connect d’Active Directory.

- Le rapport couvre les erreurs enregistrées par le client de synchronisation (Azure AD Connect version 1.1.281.0 ou ultérieure)
- Il contient les erreurs qui se sont produites lors de la dernière opération de synchronisation sur le moteur de synchronisation. (« Exporter » sur le connecteur Active Directory Azure.)
- Azure agent de santé de connexion Active Directory pour la synchronisation doit avoir une connectivité sortante aux points de terminaison requis pour le rapport et inclure les données les plus récentes. Reportez-vous à la [section Configuration requise](active-directory-aadconnect-health-agent-install.md#Requirements) pour plus de détails.
- Le rapport est **mis à jour toutes les 30 minutes** à l’aide de données téléchargées par l’agent de la santé se connecter Azure AD pour la synchronisation.
Il fournit les capacités essentielles suivantes

    - Catégorisation des erreurs
    - Liste d’objets avec l’erreur par catégorie
    - Toutes les données sur les erreurs à un seul endroit
    - Côté par comparaison d’objets avec l’erreur en raison d’un conflit
    - Télécharger le rapport d’erreurs sous la forme d’un CVS (prochainement)

### <a name="categorization-of-errors"></a>Catégorisation des erreurs
Le rapport classe les erreurs de synchronisation existant dans les catégories suivantes :

| Catégorie | Description |
| -------------- | ----------- |
| Attribut en double | Erreurs lors de l’Azure AD Connect tente créer ou mettre à jour des objets avec des valeurs dupliquées d’un ou plusieurs attributs dans Active Directory Azure qui doit être unique dans un client, comme l’attribut proxyAddresses UserPrincipalName. |
| Incompatibilité de données | Erreurs lors de la correspondance de soft ne correspond pas à objets qui génèrent des erreurs de synchronisation. |
| Échec de Validation de données | Erreurs liées à des données non valides, tels que des caractères non pris en charge dans les attributs critiques comme UserPrincipalName, mettre en forme les erreurs échouent de la validation avant d’être écrites dans Azure AD.|
| Attribut de grande taille | Erreurs lors d’un ou plusieurs attributs sont plus grandes que la taille autorisée, la longueur ou le nombre.|
| Autres | Toutes les autres erreurs qui ne rentrent pas dans les catégories ci-dessus. En fonction des commentaires, cette catégorie sera fractionnée en sous-catégories.

![Résumé de rapport d’erreur de synchronisation](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![catégories de rapport d’erreur de synchronisation](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Liste d’objets avec l’erreur par catégorie
Dans chaque catégorie de forage fournira la liste des objets ayant l’erreur dans cette catégorie.
![Liste de rapports d’erreur Sync](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Détails de l’erreur
Suivant les données est disponible dans la vue détaillée pour chaque erreur.

- Identificateurs de l' *Objet AD* impliqués
- Identificateurs de l' *Objet AD de Azure* impliqués (le cas échéant)
- Description de l’erreur et comment la résoudre
- Articles connexes

![Rapport Détails de l’erreur Sync](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Télécharger le rapport d’erreur au format CSV
Cette fonction sera bientôt disponible. Tenez-vous informé des mises à jour.



## <a name="related-links"></a>Liens connexes
* [Dépannage des erreurs lors de la synchronisation](active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Résilience d’attribut en double](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Annonce Azure se connecter à la santé](active-directory-aadconnect-health.md)
* [AD Azure se connecter l’Installation de l’Agent de la santé](active-directory-aadconnect-health-agent-install.md)
* [AD Azure se connecter à des opérations de santé](active-directory-aadconnect-health-operations.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Annonce Azure se connecter le Forum aux questions sur la santé](active-directory-aadconnect-health-faq.md)
* [Annonce Azure se connecter à l’historique de Version de la santé](active-directory-aadconnect-health-version-history.md)
