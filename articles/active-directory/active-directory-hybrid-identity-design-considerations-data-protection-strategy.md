<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - définir la stratégie de protection des données | Microsoft Azure"
    description="Vous allez définir la stratégie de protection des données de votre solution d’identité hybride répondre aux besoins de l’entreprise que vous avez définie."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Définir la stratégie de protection des données de votre solution d’identité hybride

Dans cette tâche, vous allez définir la stratégie de protection des données de votre solution d’identité hybride répondre aux besoins de l’entreprise que vous avez défini dans :

- [Déterminer les exigences de protection de données](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
- [Déterminer les exigences en matière de gestion de contenu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [Déterminer les exigences de contrôle d’accès](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [Déterminer les exigences en matière de réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Définir les options de protection de données
Comme il a été expliqué dans les [exigences de synchronisation d’annuaire déterminer](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD peut synchroniser avec vos Services de domaine Active Directory (AD DS) situé en local. Cette intégration permet aux organisations d’exploiter AD Azure pour vérifier les informations d’identification de l’utilisateur lorsqu’ils tentent d’accéder aux ressources de l’entreprise. Cela peut être effectué pour les deux scénarios : les données au repos sur site et dans le cloud.  Accès aux données dans Active Directory Azure requiert l’authentification des utilisateurs via un service de jeton de sécurité (STS).

Une fois authentifié, le nom d’utilisateur principal (UPN) est lu à partir du jeton d’authentification et de la partition répliquée et conteneur correspondant au domaine de l’utilisateur est déterminé. Informations sur l’existence, l’état activé et le rôle de l’utilisateur sont utilisées par le système d’autorisation afin de déterminer si l’accès demandé au locataire cible est autorisé pour cet utilisateur dans cette session. Certaines actions autorisées (en particulier, créer un utilisateur, le mot de passe réinitialisé) créer une piste d’audit qui permet à un administrateur de clients pour gérer le respect de la réglementation ou des enquêtes.

Déplacement des données à partir de votre centre de données local dans le stockage Azure via une connexion Internet ne peuvent pas toujours être réalisables en raison des volumes de données, la disponibilité de la bande passante ou autres considérations. Le [Service importation/exportation de stockage Azure](../storage/storage-import-export-service.md) fournit une option basée sur le matériel pour le placement/récupération d’importants volumes de données dans le stockage blob. Il vous permet d’envoyer [chiffré BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) de lecteurs de disque dur directement à un centre de données Azure où opérateurs de cloud pour télécharger le contenu à votre compte de stockage, ou ils peuvent télécharger vos données Azure à vos lecteurs à revenir vers vous. Seuls les disques chiffrés sont acceptées pour ce processus (à l’aide d’une clé BitLocker générée par le service lui-même lors de la configuration de la tâche). La clé BitLocker est fournie pour Azure séparément, offrant ainsi de bande partage de clé.

Dans la mesure où les données en transit peuvent avoir lieu dans différents scénarios, est également utile de savoir que Microsoft Azure utilise [un réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) pour isoler le trafic des locataires à partir d’une autre, utilisant des mesures de pare-feux de niveau hôte et invité, le filtrage de paquets IP, le blocage des ports, des points de terminaison HTTPS. Cependant, la plupart des communications internes d’Azure, y compris l’infrastructure à l’infrastructure et l’infrastructure client (local), est également cryptée. Un autre scénario important est la communication au sein de centres de données Azure ; Microsoft gère les réseaux pour s’assurer qu’aucune machine virtuelle peut emprunter l’identité ou espionner sur l’adresse IP d’un autre. TLS/SSL est utilisé lors de l’accès au stockage Azure ou des bases de données SQL ou lors de la connexion à des Services en nuage. Dans ce cas, l’administrateur de client est responsable de l’obtention d’un certificat TLS/SSL et son déploiement sur leur infrastructure de clients. Données du trafic déplacement entre des Machines virtuelles dans le même déploiement, ou entre les utilisateurs dans un seul déploiement via le réseau virtuel de Microsoft Azure peut être protégé par le biais de protocoles de communication cryptée par exemple HTTPS, SSL/TLS ou autres.

En fonction de la façon dont vous avez répondu aux questions de [déterminer les exigences de protection de données](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), vous devez être en mesure de déterminer comment vous souhaitez protéger vos données et comment la solution d’identité hybride vous aider sur ce point. Le tableau indique les options prises en charge par Azure qui sont disponibles pour chaque scénario de protection des données.


| Options de protection des données         | Au repos dans le nuage | Au repos sur site | En transit |
|---------------------------------|----------------------|---------------------|------------|
| Chiffrement de lecteur BitLocker      | X                    | X                   |            |
| SQL Server pour crypter les bases de données | X                    | X                   |            |
| Cryptage de machine virtuelle-de-VM             |                      |                     | X          |
| SSL/TLS                         |                      |                     | X          |
| VPN                             |                      |                     | X          |


>[AZURE.NOTE]
Lire le [respect par la fonctionnalité de](https://azure.microsoft.com/support/trust-center/services/) [Centre de confidentialité de Microsoft Azure](https://azure.microsoft.com/support/trust-center/) pour en savoir plus sur les certifications chaque service Azure est conforme.
Dans la mesure où les options de protection des données utilisent une approche multicouche, comparaison entre ces options ne sont pas applicables pour cette tâche. Vous assurer que vous tirez parti toutes les options disponibles pour chaque état dans lequel les données seront.

## <a name="define-content-management-options"></a>Définir les options de gestion de contenu
Grâce à Azure AD pour gérer une infrastructure d’identité hybride est que le processus est totalement transparent à partir du point de vue de l’utilisateur final. L’utilisateur tente d’accéder à une ressource partagée, la ressource requiert l’authentification, l’utilisateur devra envoyer une demande d’authentification pour Azure AD pour obtenir le jeton et accéder à la ressource. Ce processus se produit en arrière-plan, sans intervention de l’utilisateur. Il est également possible d’accorder l’autorisation à un [groupe](active-directory-manage-groups.md#getting-started-with-access-management) d’utilisateurs afin de leur permettre d’effectuer certaines actions communes.

Les organisations qui sont généralement des préoccupations sur la confidentialité des données nécessitent la classification des données pour leur solution. Si leur infrastructure sur site en cours est déjà à l’aide de la classification des données, il est possible d’exploiter une annonce Azure comme référentiel principal pour l’identité de l’utilisateur. Un outil commun, qu’il s’agit des locaux utilisés pour la classification des données est appelée [Données Classification Shared Computer Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) pour Windows Server 2012 R2. Cet outil peut aider à identifier, classer et protéger les données sur les serveurs de fichiers dans votre cloud privé. Il est également possible d’exploiter la [Classification automatique de fichiers](https://technet.microsoft.com/library/hh831672.aspx) dans Windows Server 2012 pour effectuer cette opération.

Si votre organisation ne possède pas la classification des données en place, mais a besoin pour protéger des fichiers sensibles sans ajouter de nouveaux serveurs sur site, ils peuvent utiliser Microsoft [Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx).  RMS Azure utilise le cryptage, d’identité et de stratégies d’autorisation pour aider à sécuriser vos fichiers et vos e-mails, et il fonctionne sur plusieurs périphériques, téléphones, tablettes et PC. Car Azure RMS est un service en nuage, il n’est pas nécessaire de configurer explicitement des approbations avec d’autres organisations avant de pouvoir partager avec eux un contenu protégé. S’ils possèdent déjà un Office 365 ou un annuaire AD d’Azure, collaboration entre organisations est automatiquement prise en charge. Vous pouvez également synchroniser uniquement les attributs de répertoire Azure RMS doit prendre en charge d’une identité commune pour les comptes d’Active Directory sur site, à l’aide d’Azure Active Directory Synchronization Services (synchronisation DAS) ou Azure Connect d’AD.

Une partie essentielle de la gestion de contenu est de comprendre qui accède à la ressource, une fonctionnalité de journalisation riche est donc importante de la solution de gestion d’identité. Annonce Azure fournit journal plus de 30 jours, y compris :

- Modifications apportées à l’appartenance aux rôles (ex : utilisateur ajouté au rôle d’administrateur Global)
- Mises à jour des informations d’identification (ex : les modifications de mot de passe)
- Gestion de domaine (ex : vérification d’un domaine personnalisé, la suppression d’un domaine)
- Ajout ou suppression d’applications
- Gestion des utilisateurs (ex : ajout, suppression, mise à jour d’un utilisateur)
- Ajout ou suppression de licences

>[AZURE.NOTE]
Consultez [Gestion des journaux d’Audit et sécurité de Microsoft Azure](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) en savoir plus sur les fonctions de journalisation dans Azure.
En fonction de la façon dont vous avez répondu aux questions [en matière de gestion de contenu de déterminer](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), vous devez être en mesure de déterminer comment vous souhaitez que le contenu à gérer dans votre solution d’identité hybride. Alors que toutes les options présentées dans le tableau 6 sont capables de s’intégrer avec Active Directory Azure, il est important de définir qui est le plus approprié pour les besoins de votre entreprise.

| Options de gestion de contenu                                                               | Avantages                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Inconvénients                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Centralisée sur site (serveur de gestion de droits Active Directory)                      | Contrôle total sur l’infrastructure du serveur responsable de la classification des données <br> Fonctionnalité intégrée dans Windows Server, pas besoin d’abonnement ou d’une licence supplémentaire <br> Peut être intégré à AD Azure dans un scénario hybride <br> Prend en charge les fonctions de gestion (IRM) de droits des informations dans Microsoft Online services comme Exchange Online et SharePoint Online, Office 365 <br> Prend en charge les produits serveur Microsoft sur site, comme Exchange Server, SharePoint Server et des serveurs de fichiers exécutant Windows Server et l’Infrastructure de Classification de fichiers (FCI). | Supérieur, maintenance (conserver les mises à jour, configuration et mises à niveau potentielles), depuis informatique possède le serveur <br> Nécessite une infrastructure de serveur local<br> Doesn'tleverage fonctionnalités Azure en mode natif                                     |
| Centralisée dans le nuage (Azure RMS)                                                     | Plus facile à gérer de comparé à la solution sur site <br> Peut être intégré à AD DS dans un scénario hybride <br>  Totalement intégré à AD Azure <br> Ne nécessite pas un serveur sur site afin de déployer le service <br> Prend en charge les produits de serveur de Microsoft tels qu’Exchange Server, SharePoint, Server et serveurs de fichiers exécutant Windows Server et le fichier de Classification, Infrastructure (FCI) sur site <br> INFORMATIQUE, peuvent avoir un contrôle total sur la clé de leurs clients avec capacité de BYOK.                                                                                    | Votre entreprise doit disposer d’un abonnement de nuage qui prend en charge de RMS <br> Votre entreprise doit disposer d’un annuaire AD Azure pour prendre en charge l’authentification utilisateur pour RMS                                                                                  |
| Hybride (RMS Azure intégré à, sur site Active Directory Rights Management Server) | Ce scénario permet de cumuler les avantages des deux, centralisée sur site et dans le cloud.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Votre entreprise doit disposer d’un abonnement de nuage qui prend en charge de RMS <br> Votre entreprise doit disposer d’un annuaire AD Azure pour prendre en charge RMS, authentification de l’utilisateur <br> Requiert une connexion entre un service cloud Azure et infrastructure sur site |

## <a name="define-access-control-options"></a>Définir les options de contrôle d’accès
Grâce à l’authentification, l’autorisation et contrôle d’accès capacités disponibles dans AD Azure, vous serez en mesure de permettre à votre entreprise d’utiliser un référentiel central d’identité tout en autorisant les utilisateurs et les partenaires à utiliser de session unique (SSO), comme illustré dans la figure ci-dessous :

![](./media/hybrid-id-design-considerations/centralized-management.png)

Gestion centralisée et entièrement l’intégration avec d’autres annuaires

Azure Active Directory fournit une ouverture de session unique sur des milliers d’applications de SaaS et les applications web en local. Veuillez consulter la [liste de compatibilité de fédération Azure Active Directory : fournisseurs d’identité tiers qui peuvent être utilisés pour mettre en œuvre une ouverture de session unique](https://msdn.microsoft.com/library/azure/jj679342.aspx) article pour plus d’informations sur le tiers de l’authentification unique qui ont été testés par Microsoft. Cette fonctionnalité permet de mettre en œuvre une variété de scénarios de B2B tout en conservant le contrôle de la gestion des identités et des accès. Toutefois, pendant le B2B le processus de conception est importante de comprendre la méthode d’authentification qui sera utilisée par le partenaire et valide si cette méthode est prise en charge par Azure. Il s’agit actuellement des méthodes prises en charge par Active Directory Azure :

- Security Assertion Markup Language (SAML)
- OAuth
- Kerberos
- Jetons
- Certificats


>[AZURE.NOTE] lire [Azure des protocoles de l’authentification Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) pour en savoir plus sur chaque protocole et ses fonctionnalités dans Azure.

À l’aide de la prise en charge Active Directory Azure, les applications métier mobiles permet la même expérience de l’authentification simple Services mobiles pour permettre aux employés de se connecter à leurs applications mobiles avec leurs informations d’identification Active Directory d’entreprise. Grâce à cette fonctionnalité, Azure AD est pris en charge comme fournisseur d’identité dans les Services mobiles à côté avec les autres fournisseurs d’identité que déjà prise en charge (qui incluent Microsoft Accounts, Facebook ID, ID de Google et Twitter l’ID). Si les applications sur site utilise les informations d’identification de l’utilisateur qui se trouve au niveau des services AD DS l’entreprise, l’accès des partenaires et des utilisateurs provenant du nuage doit être transparent. Vous pouvez gérer le contrôle d’accès conditionnel de l’utilisateur pour les applications web (basé sur le cloud), API applications web, services de nuage de Microsoft, les applications SaaS 3ème partie et native client (mobile) et les avantages de la sécurité, l’audit, le reporting au même endroit. Toutefois, il est recommandé pour valider ce dans un environnement hors production ou à une quantité limitée d’utilisateurs.


>[AZURE.TIP] Il est important de mentionner que Azure AD n’a pas de stratégie de groupe a des services AD DS. Pour appliquer la stratégie pour les périphériques, vous devrez une solution de gestion de périphérique mobile comme [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).

Une fois que l’utilisateur est authentifié à l’aide d’Active Directory Azure, il est important d’évaluer le niveau d’accès de l’utilisateur qu’il. Le niveau d’accès que l’utilisateur aura sur une ressource peut varier, alors que l’annonce Azure peut ajouter une couche de sécurité supplémentaire en contrôlant l’accès à certaines ressources, vous devez gardez à l’esprit que la ressource elle-même peut également avoir sa propre liste de contrôle d’accès séparément, tels que le contrôle d’accès pour les fichiers situés dans un serveur de fichiers. La figure ci-dessous résume les différents niveaux de contrôle d’accès que vous pouvez avoir dans un scénario hybride :

![](./media/hybrid-id-design-considerations/accesscontrol.png)


Chaque interaction dans le schéma présenté dans la Figure X représente un scénario de contrôle accès qui peut être couvertes par AD Azure. Ci-dessous, vous avez une description de chaque scénario :

1 accès conditionnel aux applications qui sont hébergées sur site : vous pouvez utiliser des périphériques avec les stratégies d’accès pour les applications qui sont configurées pour utiliser ADFS avec Windows Server 2012 R2. Pour plus d’informations sur la configuration d’un accès conditionnel pour sur site, consultez [Configuration des accès conditionnel en local à l’aide d’Azure Active Directory périphérique d’enregistrement](active-directory-conditional-access-on-premises-setup.md).
2.les contrôle d’accès pour portail de gestion Azure : Azure a également la possibilité de contrôler l’accès au portail de gestion par l’utilisation de RBAC (rôle de base de contrôle d’accès). Cette méthode permet à la société limiter la quantité d’opérations qu’un individu peut faire une fois qu’il a accès au portail de gestion Azure. À l’aide de RBAC pour contrôler l’accès au portail, autorité de certification IT Admins accès délégué en utilisant les approches de gestion des accès suivantes :

 - Affectation de rôle de groupe : vous pouvez affecter des accès à des groupes d’annonces Azure peuvent être synchronisées à partir de votre Active Directory local. Cela vous permet d’exploiter les investissements existants dans les outils et processus de gestion des groupes de votre organisation. Vous pouvez également utiliser la fonctionnalité de gestion de groupe délégué d’Azure AD Premium.
 - Exploitez intégré dans des rôles dans Azure : vous pouvez utiliser les trois rôles : propriétaire, collaborateur et Reader, afin de garantir que les utilisateurs et groupes ont l’autorisation d’effectuer uniquement les tâches qu’ils ont besoin pour effectuer leur travail.
 - Granulaire de l’accès aux ressources : vous pouvez assigner des rôles aux utilisateurs et groupes pour un abonnement spécifique, groupe de ressources ou une ressource individuelle Azure comme un site Web ou une base de données. De cette façon, vous pouvez vous assurer que les utilisateurs ont accès à toutes les ressources que dont ils ont besoin et aucun accès aux ressources auxquelles ils n’ont pas besoin pour gérer.

>[AZURE.NOTE] lire le [contrôle d’accès par rôle dans Azure](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) pour en savoir plus sur cette fonctionnalité. Pour les développeurs qui créent des applications et want personnaliser le contrôle d’accès pour eux, il est également possible d’utiliser les rôles d’Application Azure AD pour l’autorisation. Examinez cet [exemple de WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) sur comment générer votre application pour utiliser cette fonctionnalité.

3 accès conditionnel pour les applications d’Office 365 avec Microsoft Intune : IT admins peut configurer des stratégies de périphérique d’accès conditionnel pour sécuriser les ressources de l’entreprise, tout en permettant des travailleurs de l’information sur les périphériques compatibles pour accéder aux services. Pour plus d’informations, reportez-vous à la section [Conditionnelle stratégies de périphérique d’accès pour les services d’Office 365](active-directory-conditional-access-device-policies.md).

4 accès conditionnel pour les applications Saas : [cette fonctionnalité](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) vous permet de configurer des règles d’accès par application, plusieurs facteurs d’authentification et de la possibilité de bloquer l’accès d’utilisateurs non sur un réseau approuvé. Vous pouvez appliquer les règles d’authentification à plusieurs facteurs pour tous les utilisateurs qui sont affectés à l’application, ou uniquement pour les utilisateurs au sein de groupes de sécurité spécifiés. Les utilisateurs peuvent être exclus de l’obligation d’authentification à plusieurs facteurs si ils accèdent à l’application à partir d’une adresse IP dans à l’intérieur de l’organisation du réseau.

Étant donné que les options de contrôle d’accès utilisent une approche multicouche, comparaison entre ces options ne sont pas applicables pour cette tâche. Vous assurer que vous tirez parti toutes les options disponibles pour chaque scénario, vous devez contrôler l’accès à vos ressources.

## <a name="define-incident-response-options"></a>Définir les options de réponse aux incidents
Annonce Azure peut aider IT d’identité éventuels risques de sécurité dans l’environnement par la surveillance de l’activité de l’utilisateur, informatiques peuvent exploitent l’accès de AD d’Azure et les rapports d’utilisation permet d’obtenir une visibilité sur l’intégrité et la sécurité de l’annuaire de votre organisation. Avec ces informations, un administrateur informatique peut mieux déterminer où peut se trouver des risques de sécurité afin qu’ils puissent correctement à atténuer ces risques.  [Abonnement Premium de publicité Azure](active-directory-get-started-premium.md) a un ensemble de rapports de sécurité qui peut permettre l’informatique pour obtenir ces informations. [Rapports de publicité Azure](active-directory-view-access-usage-reports.md) sont classées comme indiqué ci-dessous :

- **Rapports d’anomalie**: contenir le signe dans les événements que nous avons trouvé d’anormal. Notre objectif est de vous informer de cette activité et vous permettent d’être en mesure de déterminer si un événement est suspect.
- **État de l’Application intégrée**: fournit des informations sur l’utilisation des applications en nuage dans votre organisation. Azure Active Directory offre une parfaite intégration avec des milliers d’applications en nuage.
- **Les rapports d’erreurs**: indiquer les erreurs qui se produisent lors de la création de comptes à des applications externes.
- **Les rapports spécifiques à l’utilisateur**: afficher périphérique/dans les données de l’activité d’un utilisateur spécifique.
- **Journaux d’activité**: contiennent un enregistrement de tous les événements audités dans les dernières 24 heures 7 derniers jours, ou 30 derniers jours, ainsi que les changements d’activité groupe et activité d’enregistrement et de réinitialisation de mot de passe.

>[AZURE.TIP]
Un autre rapport qui peut également aider l’équipe de réponse aux incidents sur un incident est le rapport de [l’utilisateur avec les informations d’identification perdues](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) .  Ce rapport couvre toutes les correspondances entre ces listes de fuite d’informations d’identification et de vos clients.

Autres informations importantes dans les rapports dans Azure AD qui peut être utilisé au cours d’une enquête de réponse aux incidents et peuvent :

- **Activité de réinitialisation de mot de passe**: fournir à l’administrateur de comprendre comment activement réinitialisation de mot de passe est utilisée dans l’organisation.
- **L’activité d’enregistrement de réinitialisation de mot de passe**: offrent un aperçu dans lequel les utilisateurs ont enregistré leurs méthodes de réinitialisation de mot de passe, et les méthodes qu’ils ont sélectionné.
- **Activité de groupe**: fournit un historique des modifications apportées au groupe (ex : utilisateurs ajoutés ou supprimés) qui ont été ouverts dans le panneau d’accès.

En plus de la fonctionnalité de reporting de base disponible dans Azure AD Premium qui peuvent être exploitées lors d’un processus d’enquête de réponse aux incidents, informatique peuvent également tirer parti de rapport d’Audit pour obtenir des informations telles que :

- Modifications apportées à l’appartenance aux rôles (ex : utilisateur ajouté au rôle d’administrateur Global)
- Mises à jour des informations d’identification (ex : les modifications de mot de passe)
- Gestion de domaine (ex : vérification d’un domaine personnalisé, la suppression d’un domaine)
- Ajout ou suppression d’applications
- Gestion des utilisateurs (ex : ajout, suppression, mise à jour d’un utilisateur)
- Ajout ou suppression de licences

Étant donné que les options de réponse aux incidents utilisent une approche multicouche, comparaison entre ces options ne sont pas applicables pour cette tâche. Vous assurer que vous tirez parti toutes les options disponibles pour chaque scénario que vous devez utiliser les capacités de reporting AD Azure dans le cadre du processus de réponse aux incidents de votre entreprise.

## <a name="next-steps"></a>Étapes suivantes
[Déterminer les tâches de gestion d’identité hybride](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)


## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
