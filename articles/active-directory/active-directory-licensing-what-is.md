<properties
    pageTitle="Ce qui est de licence Microsoft Azure Active Directory ? | Microsoft Azure"
    description="Description du contrat de licence Microsoft Azure Active Directory, comment il fonctionne, comment démarrer et meilleures pratiques, y compris Office 365, Microsoft Intune, Azure Active Directory prime et des éditions de base"
    services="active-directory"
      keywords="Le Gestionnaire de licences Azure AD"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="what-is-microsoft-azure-active-directory-licensing"></a>Ce qui est de licence Microsoft Azure Active Directory ?

##<a name="description"></a>Description
Azure Active Directory (AD Azure) est identité de Microsoft sous la forme d’une plate-forme et une solution de Service (IDaaS). Azure AD est proposé dans un certain nombre de techniques et fonctionnelles des versions allant d’Azure libres d’Active Directory, ce qui est disponible avec tous les services Microsoft tels que Office 365, Dynamics, Microsoft Intune et Azure (AD Azure ne génère pas les éventuels frais de consommation dans ce mode,) pour Azure AD payé versions comme Suite de mobilité d’entreprise (EMS), Azure AD Premium et Basic, Azure plusieurs facteurs d’authentification (AMF). Comme la plupart des services en ligne de Microsoft, versions de publicité payée plus Azure sont remies par le biais de droits de l’utilisateur comme dans Office 365, Microsoft Intune et Azure AD. Dans ces cas, le fournisseur de service est représentée par un ou plusieurs abonnements, et chaque abonnement comprend un nombre avant achat de licences de vos clients. Droits d’utilisateur sont réalisés via l’attribution de licence, création d’un lien entre l’utilisateur et le produit, activer les composants de service pour l’utilisateur et l’utilisation d’une des licences prépayés.

[Essayez maintenant de prime d’Azure AD.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Portail d’administration AD Azure fait partie du portail Azure classique. Tout en utilisant AD Azure ne nécessite pas d’achats Azure, accéder à ce portail requiert un abonnement Azure actif ou une [version d’essai Azure](https://azure.microsoft.com/pricing/free-trial/).

Pour obtenir une vue d’ensemble des fonctionnalités du service publicité Azure, consultez [AD Azure](active-directory-whatis.md).
[En savoir plus sur les niveaux de service Azure AD](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Les abonnements Azure retenues sont différents : tandis qu’également représentée dans votre répertoire, ces abonnements permettre la création de ressources Azure et les mapper à votre mode de paiement. Dans ce cas il n’y a aucun décompte de licence associé à l’abonnement. Association d’utilisateurs avec l’abonnement, l’accès des utilisateurs à la gestion des ressources de l’abonnement, est obtenue en leur accordant des autorisations pour fonctionner sur des ressources Azure mappées à l’abonnement.


##<a name="how-does-azure-ad-licensing-work"></a>Comment fonctionne la licence d’Azure AD ?

Basée sur une licence de publicité Azure (en fonction des droits) des services en activant un abonnement dans votre client de service d’annuaire AD Azure. Une fois que l’abonnement est actif les fonctionnalités du service peuvent être gérées par les administrateurs de service d’annuaire et utilisées par les utilisateurs sous licence.

Lorsque vous achetez ou que vous activez une Suite de mobilité d’entreprise, Azure AD Premium ou de base de publicité Azure, votre répertoire est mis à jour avec l’abonnement, y compris sa période de validité et les licences prépayés. Informations sur votre abonnement, notamment l’état, les événements de cycle de vie suivant et le nombre de licences affectées ou disponibles est disponible via le portail Azure classique sous l’onglet licences pour le répertoire. C’est également au meilleur endroit pour gérer vos affectations de licence.

Chaque abonnement se compose d’un ou plusieurs plans de service, chaque mappage le niveau fonctionnel inclus du type de service ; par exemple, publicité Azure, Azure AMF, Microsoft Intune, Exchange Online et SharePoint Online. Gestion des licences AD Azure ne nécessite pas de gestion au niveau du plan de service. Ceci diffère d’Office 365, qui s’appuie sur ce mode de configuration avancé pour gérer l’accès aux services inclus. Il repose sur Azure AD dans la configuration du service, pour activer des fonctionnalités et de gérer des autorisations individuelles.

En règle générale, les informations d’abonnement Azure AD sont gérées via le portail classique Azure, sous l’onglet licences pour le répertoire. Les abonnements AD Azure, à l’exception de prime de publicité Azure, ne s’affichent pas dans le portail Office.

> [AZURE.IMPORTANT] Azure AD Premium et Basic, ainsi que d’abonnements de la Suite de mobilité d’entreprise, sont limités à leur répertoire/locataire mis en service. Les abonnements ne peuvent pas être répartis entre les répertoires ou utilisés pour habilite dans d’autres répertoires. Le déplacement entre répertoires un abonnement est possible, mais nécessite l’envoi d’un ticket de support ou la résiliation et ré-achat pour les achats directs effectués.

> Lors de l’achat d’Azure AD ou Suite de mobilité d’entreprise par le biais de licences en Volume Activation de l’abonnement se produit automatiquement lorsque l’accord inclut des autres Microsoft Online services, par exemple, Office 365.

Payé Azure les fonctionnalités Active Directory s’étendent sur la largeur de l’annuaire. Voici quelques exemples :
- Basée sur le groupe d’affectation aux applications, qui est activée dans l’application spécifique que vous gérez.
- Avancées et des fonctions de gestion libre-service de groupe sont disponibles sous configuration de l’annuaire ou au sein du groupe spécifique.
- Les rapports de sécurité Premium sont sur l’onglet Rapports
- Découverte d’applications cloud s’affiche dans le portail Azure sous une identité.

###<a name="assigning-licenses"></a>Attribution des licences
En obtenir un abonnement suffit pour configurer les fonctionnalités payées, à l’aide de votre annonce Azure payé des fonctionnalités nécessite de distribution de licences aux personnes appropriées. En règle générale, une licence doit être affecté à n’importe quel utilisateur qui doit pouvoir accéder à ou qui est géré via une annonce Azure payé la fonctionnalité. Une attribution de licence est un mappage entre un utilisateur et d’un service acheté, Azure AD Premium, Basic ou Suite de mobilité d’entreprise.

Il est simple de gérer les utilisateurs de votre annuaire doivent disposer d’une licence. Il est possible en les affectant à un groupe pour créer des règles d’affectation via le portail d’administration AD Azure ou par attribution des licences directement aux personnes appropriées, via un portail, PowerShell ou API. Lors de l’attribution des licences à un groupe, tous les membres du groupe seront affectés une licence. Si les utilisateurs sont ajoutés ou supprimés du groupe qu’ils sont affectés ou supprimés de la licence appropriée. Affectation de groupe peut utiliser une gestion de groupe disponible et est cohérente avec basée sur le groupe d’affectation d’applications. Avec cette approche, vous pouvez définir des règles telles que tous les utilisateurs de votre répertoire sont automatiquement affectés, assurez-vous que tout le monde avec le titre approprié de travail dispose d’une licence ou même déléguer la décision pour les autres responsables dans l’organisation.

Avec l’attribution de licence basée sur le groupe, n’importe quel utilisateur manquant d’un emplacement d’utilisation hériteront de l’emplacement du répertoire lors de l’affectation. Cet emplacement peut être modifié à tout moment par l’administrateur. Dans le cas où l’affectation automatique a échoué en raison d’une erreur, les informations utilisateur sous ce type de licence reflètent cet état.

##<a name="getting-started-with-azure-ad-licensing"></a>Mise en route avec le Gestionnaire de licences AD Azure

Mise en route avec AD Azure est simple ; Vous pouvez toujours créer votre répertoire dans le cadre de l’inscription à un Azure gratuit d’évaluation. [En savoir plus sur l’inscription en tant qu’organisation](sign-up-organization.md). La liste suivante peut vous aider à vous assurer que votre répertoire est mieux aligné avec les autres services Microsoft pouvez consommer ou vous envisagez de consommer et de vos objectifs en vue d’obtenir le service.

Voici quelques méthodes recommandées :
- Si vous utilisez déjà les services d’organisation de Microsoft, vous avez déjà un répertoire AD Azure. Dans ce cas, vous devez continuer à utiliser le même répertoire pour d’autres services, afin que la gestion des identités core, y compris la mise en service et hybride SSO, peut être utilisée dans les services. Les utilisateurs bénéficient d’une ouverture de session unique et bénéficieront de fonctionnalités plus riches sur les services. Par conséquent, si vous décidez d’acheter une annonce Azure payé le service pour votre personnel, nous vous recommandons d’utiliser le même répertoire pour ce faire.
- Si vous envisagez d’utiliser Active Directory Azure pour un ensemble différent d’utilisateurs (clients, partenaires et ainsi de suite), ou si vous souhaitez évaluer les services Azure AD et que vous souhaitez faire indépendamment de votre service de production, ou si vous recherchez pour le programme d’installation d’un environnement sandbox pour vos services, nous vous recommandons de créer un nouveau répertoire via le portail classique Azure Azure. [En savoir plus sur la création d’un annuaire AD Azure dans Azure portal classique](active-directory-licensing-directory-independence.md). Le nouveau répertoire sera créé avec votre compte comme un utilisateur externe avec des autorisations d’administrateur global. Pour vous connecter au portail Azure classique avec ce compte, vous ne pourrez pas voir ce répertoire et accéder à toutes les tâches d’administration de répertoire. Nous vous recommandons de créer un compte local avec les privilèges appropriés pour gérer les autres services de Microsoft (ceux non accessible via le portail classique Azure). [En savoir plus sur la création de comptes d’utilisateurs dans Active Directory Azure](active-directory-create-users.md).

> [AZURE.NOTE] Annonce Azure prend en charge des « utilisateurs externes », qui sont des comptes d’utilisateur dans une instance d’Azure AD qui ont été créés à l’aide d’un compte Microsoft (MSA), soit une identité AD Azure à partir d’un autre répertoire. Nous sont occupés étendre cette fonctionnalité à tous les services d’organisation de Microsoft, maintenant ces comptes pas prend en charge des expériences des services ; par exemple, le portail d’administration de Office 365 ne prend pas en charge ces utilisateurs. Par conséquent, les utilisateurs externes avec des comptes de Microsoft ne sera pas accéder au portail d’administration de Office 365, tandis que les utilisateurs externes à partir d’autres annuaires AD Azure seront ignorés. Dans ce dernier cas, uniquement le compte d’utilisateur local, la publicité Azure ou répertoire Office 365 dans lequel l’utilisateur a été initialement créé, seraient accessibles par le biais de ces expériences.

Comme indiqué, Azure AD a différentes versions payantes. Ces versions présentent de légères différences dans leur disponibilité d’achat :


| Produit   | EA/VL     | Ouvrir  |   FOURNISSEUR DE SERVICES CRYPTOGRAPHIQUES |   Droits d’utilisation MPN  |   Achat direct | Version d’évaluation |
|---|---|---|---|---|---|---|
| Suite de mobilité d’entreprise |   X | X | X | X |  |      X |
| Azure AD Premium  | X | X | X |   | X | X |
| Azure AD Basic    | X | X | X | X |  <br /> |  <br />  |

###<a name="select-one-or-more-license-trials"></a>Sélectionnez un ou plusieurs essais de licence
 Dans tous les cas, vous pouvez activer un abonnement d’essai Azure AD Premium ou Suite de mobilité d’entreprise en sélectionnant la version d’évaluation spécifique souhaitées sous l’onglet licences dans votre répertoire. Une version d’évaluation contient un abonnement de 30 jours avec 100 licences.

![Plans de licence d’essai Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Plans de licence d’évaluation Suite de mobilité d’entreprise](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Plans de licence d’évaluation](./media/active-directory-licensing-what-is/active_license_trials.png)

###<a name="assign-licenses"></a>Attribution de licences
Une fois que l’abonnement est actif, vous devez attribuer une licence à vous-même et actualisez le navigateur afin de que vous voyez toutes les fonctionnalités de votre. L’étape suivante consiste à attribuer des licences aux utilisateurs qui devront accéder à, ou être inclus dans, payé les fonctionnalités AD Azure. Comme nous l’avons mentionné ci-dessus dans « Affectation licences », la meilleure façon de procéder est d’identifier le groupe qui représente l’audience voulue et l’assigner à la licence ; de cette façon, les utilisateurs qui sont ajoutés ou supprimés du groupe pendant son cycle de vie est affectés à ou supprimés de la licence.

Pour attribuer une licence à un groupe ou d’utilisateurs individuels, sélectionnez le plan de licence que vous souhaitez attribuer, puis cliquez sur **attribuer** sur la barre de commandes.

![Plans de licence d’évaluation](./media/active-directory-licensing-what-is/assign_licenses.png)

Une fois dans la boîte de dialogue affectation pour le plan sélectionné, vous pouvez sélectionner les utilisateurs et les ajouter à la colonne **affecter** sur la droite. Vous pouvez parcourir la liste des utilisateurs ou rechercher des personnes spécifiques à l’aide de la recherche de verre en haut à droite de la grille de l’utilisateur. Pour affecter des groupes, sélectionnez « Groupes » dans le menu **Afficher** et puis cliquez sur le bouton de vérification sur le droit d’actualiser les affectations qui sont affichées.

![Attribution des licences à des groupes](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Vous pouvez maintenant rechercher ou parcourir les groupes et les ajouter à la colonne **affecter** de la même manière. Vous pouvez utiliser ces pour assigner une combinaison d’utilisateurs et de groupes en une seule opération. Pour terminer le processus d’affectation, cliquez sur le bouton de vérification dans le coin inférieur droit de la page.

![Message de progression d’affectation licences](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Lorsqu’un groupe est affecté, ses membres héritent les licences dans les 30 minutes, mais dans un délai de 1 à 2 minutes.

Erreurs d’affectation peuvent se produire lors de l’affectation de licence AD Azure, mais sont relativement rares. Erreurs d’affectation potentiels sont limitées à :
- Conflit d’affectation - lorsqu’un utilisateur a été affecté précédemment une licence qui n’est pas compatible avec la licence actuelle. Dans ce cas, l’affectation de la nouvelle licence nécessite la suppression de le.
- Les licences disponibles dépassées - lorsque le nombre d’utilisateurs dans les groupes affectés dépasse les licences disponibles, état de l’affectation des utilisateurs reflète une défaillance pour affecter en raison du manque de licences.

###<a name="view-assigned-licenses"></a>Afficher attribué les licences

Une vue synthétique de licences attribuées, y compris les événements de cycle de vie abonnement disponible, attribué et suivant sont affichés dans l’onglet **licences** .

![Afficher le nombre de licences attribuées](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Une liste détaillée des utilisateurs et des groupes, y compris l’état de l’affectation et le chemin d’accès (direct ou héritée à partir d’un ou plusieurs groupes) est disponible lors de la navigation dans un plan de licence.

![Affichage du détail des licences attribuées pour un plan de licence](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Suppression des licences est aussi simple que de les affecter. Si l’utilisateur est directement affecté ou pour un groupe affecté, vous pouvez supprimer la licence en sélectionnant le type de licence, **suppression**, ajout à la liste de suppression de l’utilisateur ou le groupe et confirme l’action. Vous pouvez également ouvrir un type de licence, sélectionnez l’utilisateur spécifique ou le groupe et cliquez sur **Supprimer** dans la barre de commande. Pour mettre fin à l’héritage de l’utilisateur d’une licence à partir d’un groupe, supprimez simplement l’utilisateur du groupe.

###<a name="extending-trials"></a>Extension des essais

Les extensions d’essai pour les clients sont disponibles en libre service via le portail Office 365. Un client de l’administrateur peut accéder au [portail Office](https://portal.office.com/#Billing) (accès dépend des autorisations pour le portail Office) et sélectionnez votre version d’essai Azure AD Premium. Cliquez sur le lien **étendre d’évaluation** et suivez les instructions. Vous devez entrer une carte de crédit, mais il ne sera pas débitée.

![Extension d’une version d’évaluation de la licence dans le portail Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

Les clients peuvent également demander une extension d’évaluation en envoyant une demande de prise en charge. Un administrateur de client peut atteindre le Office 365 portail de [prise en charge de la page](http://aka.ms/extendAADtrial) (accès dépend des autorisations de la page de support Office). Sur cette page, sélectionnez « Abonnements et essais » sous fonctionnalités et « Aux questions d’évaluation » sous le symptôme. Enfin, entrez les informations sur les circonstances

![Extension d’une version d’évaluation de la licence à l’aide d’une demande de support](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant, vous pouvez être prêt à configurer et à utiliser certaines fonctionnalités d’Azure AD Premium.

- [Réinitialisation du mot de passe libre-service](active-directory-manage-passwords.md)
- [Gestion de groupe de libre-service](active-directory-accessmanagement-self-service-group-management.md)
- [Intégrité des annonces Connect Azure](active-directory-aadconnect-health.md)
- [Affectation des groupes d’applications](active-directory-manage-groups.md)
- [Authentification à plusieurs facteurs Azure](../multi-factor-authentication/multi-factor-authentication.md)
- [Achat direct de licences d’Azure AD Premium](http://aka.ms/buyaadp)
