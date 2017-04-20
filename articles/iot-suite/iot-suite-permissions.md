<properties
  pageTitle="Suite de IoT Azure et d’Azure Active Directory | Microsoft Azure"
  description="Décrit comment Azure IoT Suite utilise Azure Active Directory pour gérer les autorisations."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/24/2016"
  ms.author="araguila"/>
  
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Autorisations sur le site azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Que se passe-t-il lors de la connexion

Lorsque vous vous connectez pour la première fois au [azureiotsuite.com][lnk-azureiotsuite], le site détermine les niveaux d’autorisation que vous avez sur la base des clients Azure Active Directory (DAS) actuellement sélectionné et abonnement Azure.

1.  Le site recherche d’abord d’Azure les locataires DAS vous appartenez pour remplir la liste de clients à côté de votre nom d’utilisateur connecté. À ce stade, le site ne pouvez obtenir les jetons d’utilisateur pour un client à la fois. Par conséquent, lorsque vous basculez vers un autre client à l’aide de la liste déroulante dans le coin supérieur droit, le site nouveau ouvre une session dans ce client afin d’obtenir les jetons pour ce client.

2.  Ensuite, le site recherche d’Azure les abonnements que vous avez associé avec le client sélectionné. Vous consultez les abonnements disponibles lorsque vous créez une nouvelle solution préconfigurée.

3.  Enfin, le site récupère toutes les ressources dans les abonnements et les groupes de ressources marquées en tant que solutions préconfigurées et remplit les mosaïques sur la page d’accueil.

Les sections suivantes décrivent les rôles qui contrôlent l’accès aux solutions préconfigurées.

## <a name="aad-roles"></a>Rôles du DAS

Les rôles de DAS contrôlent les solutions de fourniture préconfigurée de capacité et gérer des utilisateurs dans une solution préconfigurée.

Vous trouverez plus d’informations sur les rôles d’administrateur dans DAS [affectation rôle]d’administrateur dans Azure AD[lnk-aad-admin], mais cet article se concentre principalement sur les rôles **Utilisateur/membre de domaine** utilisé par les solutions préconfigurées et de l' **Administrateur Global** .

**Administrateur global :** Il peut y avoir de nombreux administrateurs globaux par le locataire des DAS. Lorsque vous créez un locataire DAS, vous êtes par défaut l’administrateur global de ce client. L’administrateur global peut fournir une solution préconfigurée et est affecté à un rôle **d’administrateur** de l’application à l’intérieur de leurs clients de DAS. Toutefois, si un autre utilisateur dans le locataire DAS même crée une application, le rôle par défaut de que l’administrateur global est accordée est **Implicite en lecture seule**. Les administrateurs globaux peuvent affecter des rôles pour les applications utilisant le [Azure portal classique][lnk-classic-portal].

**Utilisateur/membre de domaine :** Il peut exister de nombreux utilisateurs/membres du domaine par le locataire des DAS. Un utilisateur de domaine peut fournir une solution préconfigurée par l’intermédiaire de [azureiotsuite.com] [ lnk-azureiotsuite] site. Le rôle par défaut qu'ils sont accordées pour l’application de que leur mise en service est **l’administrateur**. Ils peuvent créer une application dans [azure-iot-de surveillance à distance] à l’aide de script build.cmd[ lnk-rm-github-repo] ou [azure-iot--maintenance PREVENTIVE] [ lnk-pm-github-repo] référentiel, mais le rôle par défaut ils sont accordées est **READONLY implicite**, car ils n’ont pas l’autorisation d’affecter des rôles. Si un autre utilisateur dans le locataire DAS crée une application, ils sont affectés du rôle **READONLY implicite** par défaut pour cette application. Ils n’ont pas de la possibilité d’attribuer des rôles pour les applications ; Par conséquent, ne peut pas ajouter des utilisateurs ou rôles pour les utilisateurs d’une application même si leur mise en service.

**Invité utilisateur/invité :** Il peut y avoir de nombreux utilisateurs/invités invité par le locataire des DAS. Les utilisateurs invités ont un ensemble limité de droits dans le locataire DAS. En conséquence, les utilisateurs invités ne peut pas fournir une solution préconfigurée dans le locataire DAS.

Pour plus d’informations, consultez les ressources suivantes :

- [Créer ou modifier des utilisateurs dans Active Directory Azure][lnk-create-edit-users]
- [Attribuer des rôles d’application dans un DAS][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Rôles d’administrateur abonnement Azure

Les rôles d’Azure administrateur contrôlent la capacité de mapper un abonnement Azure à un locataire AD.

Vous trouverez plus d’informations sur les rôles d’administrateur de collaboration Azure, administrateur de Service et l’administrateur de compte dans l’article [comment ajouter ou modifier l’administrateur coproduit d’Azure, administrateur de Service et compte administrateur][lnk-admin-roles].

## <a name="application-roles"></a>Rôles d’application

Les rôles d’application contrôlent l’accès aux périphériques de votre solution préconfigurée.

Il existe deux défini et un rôle implicite défini dans l’application qui est créée lorsque vous configurez une solution préconfigurée.

-   **Administrateur :** A le contrôle total à ajouter, gérer et supprimer des périphériques

-   **En lecture seule :** A la possibilité d’afficher les périphériques

-   **Uniquement de lecture implicite :** Cela est le même que lecture seule, mais est accordé à tous les utilisateurs de vos clients de DAS. Cela a été fait pour des raisons pratiques lors du développement. Vous pouvez supprimer ce rôle en modifiant le [RolePermissions.cs] [ lnk-resource-cs] fichier de code source.

### <a name="changing-application-roles-for-a-user"></a>Modification des rôles d’application pour un utilisateur

Vous pouvez utiliser la procédure suivante pour rendre un utilisateur dans Active Directory un administrateur de votre solution préconfigurée.

Vous devez être un administrateur global de DAS pour modifier les rôles d’un utilisateur :

1. Atteindre le [Azure portal classique][lnk-classic-portal].

2. Sélectionnez **Active Directory**.

3. Cliquez sur le nom de votre client DAS (qui est le répertoire que vous avez choisi de azureiotsuite.com lors du déploiement de votre solution).

4. Cliquez sur **Applications**.

5. Cliquez sur le nom de l’application correspondant au nom de votre solution préconfigurée. Si vous ne voyez pas votre application dans la liste, passez de la liste déroulante de **Afficher** pour **Applications de mon entreprise possède** et cliquez sur la case à cocher.

7. Cliquez sur **utilisateurs**.

8. Sélectionnez l’utilisateur que vous souhaitez changer de rôle.

9. Cliquez sur **attribuer** , puis sélectionnez le rôle (par exemple **Admin**) vous voulez affecter à l’utilisateur, cliquez sur la case à cocher.

## <a name="faq"></a>FAQ

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-do-this"></a>Je suis un administrateur de service, et je souhaite modifier le mappage de répertoire entre mon abonnement et un locataire DAS spécifique. Comment effectuer cette opération ?

1. Atteindre le [Azure portal classique][lnk-classic-portal], cliquez sur **paramètres** dans la liste des services sur le côté gauche.

2. Sélectionnez l’abonnement que vous souhaitez modifier le mappage de répertoire à.

3. Cliquez sur **Modifier le répertoire**.

4. Sélectionnez le **répertoire** que vous souhaitez utiliser dans la liste déroulante. Cliquez sur la flèche droite.

5. Confirmer le mappage de répertoire et affecté des administrateurs. Notez que si vous vous déplacez dans un autre répertoire, tous les administrateurs de collaboration à partir du répertoire d’origine sont supprimés.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Je suis un utilisateur/membre du domaine sur le locataire DAS et j’ai créé une solution préconfigurée. Comment j’obtenir un rôle attribué pour mon application ?

Demandez à un administrateur global pour vous attribuer un compte d’administrateur global sur cliente DAS pour obtenir l’autorisation d’affecter des rôles aux utilisateurs vous-même, ou demandez à un administrateur global pour vous attribuer un rôle. Si vous souhaitez modifier le locataire DAS votre solution préconfigurée a été déployé pour, consultez la question suivante.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Comment pour basculer le locataire DAS à que ma solution préconfigurée de surveillance à distance et l’application sont affectés ?

Vous pouvez exécuter un déploiement nuage à partir de <https://github.com/Azure/azure-iot-remote-monitoring> et redéployer avec un locataire DAS nouvellement créé. Puisque vous êtes par défaut un administrateur global lorsque vous créez un nouveau locataire DAS, vous aurez accès pour ajouter des utilisateurs et attribution des rôles aux utilisateurs.

1. Créer un nouveau répertoire DAS dans le [portail de gestion d’Azure][lnk-classic-portal].

2. Accédez à <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Exécutez `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (par exemple, `build.cmd cloud debug myRMSolution`)

4. Lorsque vous y êtes invité, définissez l' **tenantid** à vos clients nouvellement créé plutôt que vos clients précédents.


### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Je souhaite modifier un administrateur du Service ou co-l’administrateur lorsque vous êtes connecté avec un compte d’organisation

Consultez l’article de support [administrateur du Service de modification et de collaboration d’administrateur lorsque vous êtes connecté avec un compte d’organisation][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Pourquoi affiche-t-il cette erreur ? « Votre compte ne dispose pas des autorisations appropriées pour créer une solution. Vérifiez avec votre administrateur de compte ou essayez avec un autre compte. »

Examinons le diagramme ci-dessous :

![][img-flowchart]

> [AZURE.NOTE] Si vous continuez de voir l’erreur après validation vous sont administrateur global sur le locataire DAS et administrateur de collègues sur l’abonnement, demandez à votre administrateur de compte supprime l’utilisateur et réaffecter les autorisations nécessaires dans cet ordre : ajouter l’utilisateur en tant qu’administrateur global, puis utilisateur en tant qu’un administrateur de collègues sur l’abonnement Azure. Si les problèmes persistent, veuillez contacter [aide et assistance][lnk-help-support].

**Pourquoi affiche-t-il cette erreur lorsque j’ai un abonnement Azure ?** *Un abonnement Azure est nécessaire pour créer des solutions préconfigurées. Vous pouvez créer un compte d’évaluation gratuit en quelques minutes.*

Si vous êtes sûr que vous disposez d’un abonnement Azure, valider le locataire mappage pour votre abonnement et assurez-vous que le locataire correct est sélectionné dans la liste déroulante. Si vous avez validé le locataire votre choix est correct, suivez le schéma ci-dessus et valider le mappage de votre abonnement et ce client DAS.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir sur la Suite de IoT, voir comment vous pouvez [Personnaliser une solution préconfigurée][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
