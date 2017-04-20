<properties
    pageTitle="Azure Connect de AD sync : meilleures pratiques pour la modification de la configuration par défaut | Microsoft Azure"
    description="Décrit les méthodes conseillées pour la modification de la configuration par défaut d’Azure Connect de AD sync."
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
    ms.date="08/22/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure Connect de AD sync : meilleures pratiques pour la modification de la configuration par défaut
L’objectif de cette rubrique est de décrire des modifications prises en charge et non pris en charge la synchronisation Azure Connect d’AD.

La configuration créée par Azure Connect de publicité fonctionne « en l’état » pour la plupart des environnements qui se synchronisent sur site Active Directory avec AD Azure. Toutefois, dans certains cas, il est nécessaire d’appliquer des modifications à une configuration pour répondre à un besoin particulier ou la spécification.

## <a name="changes-to-the-service-account"></a>Les modifications apportées au compte de service
Azure AD connexion synchronisation s’exécute sous un compte de service créé par l’Assistant d’installation. Ce compte de service conserve les clés de cryptage pour la base de données utilisé par la synchronisation. Il est créé avec un mot de passe de 127 caractères de long et le mot de passe est définie à ne pas expirer.

- Il est **non pris en charge** pour modifier ou réinitialiser le mot de passe du compte de service. Cette opération supprime les clés de cryptage et que le service n’est pas en mesure d’accéder à la base de données et n’est pas en mesure de démarrer.

## <a name="changes-to-the-scheduler"></a>Modifications au planificateur
Démarrage avec les versions à partir de la version 1.1 (février 2016), vous pouvez configurer le [Planificateur](active-directory-aadconnectsync-feature-scheduler.md) d’avoir un cycle de synchronisation autre que celle par défaut à 30 minutes.

## <a name="changes-to-synchronization-rules"></a>Modifications apportées aux règles de synchronisation
L’Assistant installation propose une configuration qui est supposé pour fonctionner pour les scénarios les plus courants. Dans le cas où vous devez apporter des modifications à la configuration, vous devez suivre ces règles pour toujours avoir une configuration prise en charge.

- Vous pouvez [Modifier le flux d’attributs](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) si les flux direct des attributs par défaut ne sont pas adaptés à votre organisation.
- Si vous voulez [pas flux un attribut](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) et supprimez les valeurs d’attribut existantes dans Active Directory Azure, vous devez créer une règle pour ce scénario.
- [Désactiver une règle de synchronisation indésirables](#disable-an-unwanted-sync-rule) plutôt que de le supprimer. Une règle supprimée est recréée lors de la mise à niveau.
- Pour [Modifier une règle de prêts à l’emploi](#change-an-out-of-box-rule), vous devez faire une copie de la règle d’origine et de désactiver la règle out-of-box. L’éditeur de règles de synchronisation invite et vous aide à vous.
- Exporter vos règles de synchronisation personnalisé à l’aide de l’éditeur de règles de synchronisation. L’éditeur vous fournit un script PowerShell que vous permet de les recréer facilement dans un scénario de récupération après sinistre.

>[AZURE.WARNING] Les règles de synchronisation d’out-of-box ont une empreinte numérique. Si vous apportez une modification à ces règles, l’empreinte est ne correspondant plus aux. Vous pouvez avoir des problèmes lorsque vous essayez d’appliquer une nouvelle version de Azure Connect d’Active Directory. Seulement modifiez la façon dont il est décrit dans cet article.

### <a name="disable-an-unwanted-sync-rule"></a>Désactiver une règle de synchronisation indésirables
Ne pas supprimer une règle de synchronisation d’out-of-box. Il est recréé lors de la prochaine mise à niveau.

Dans certains cas, l’Assistant installation a produit une configuration qui ne fonctionne pas pour votre topologie. Par exemple, si vous disposez d’une topologie de forêt ressource compte mais que vous avez étendu le schéma dans la forêt de comptes avec le schéma Exchange, pour Exchange, les règles sont créées pour la forêt de comptes et de la forêt de ressources. Dans ce cas, vous devez désactiver la règle de synchronisation pour Exchange.

![Règle de synchronisation désactivé](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Dans l’image ci-dessus, l’Assistant installation a trouvé un ancien schéma Exchange 2003 dans la forêt de comptes. Cette extension de schéma a été ajoutée avant l’introduction dans l’environnement de Fabrikam la forêt de ressources. Pour assurer la que synchronisation pas d’attributs à partir de l’ancienne implémentation de Exchange, la règle de synchronisation doit être désactivée comme indiqué.

### <a name="change-an-out-of-box-rule"></a>Modifier une règle d’out-of-box
Si vous devez apporter des modifications à une règle d’out-of-box, vous devez faire une copie de la règle d’out-of-box et désactiver la règle initiale. Apportez les modifications apportées à la règle clonée. L’éditeur de règles de synchronisation vous aide à ces étapes. Lorsque vous ouvrez une règle out-of-box, cette boîte de dialogue s’affiche :  
![Avertissement de la règle de zone](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Cliquez sur **Oui** pour créer une copie de la règle. La règle clonée est ensuite ouvert.  
![Règle cloné](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Cette règle cloné, apportez les modifications nécessaires à portée, jointure et des transformations.

## <a name="next-steps"></a>Étapes suivantes

**Rubriques de vue d’ensemble**

- [Azure Connect de AD sync : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
