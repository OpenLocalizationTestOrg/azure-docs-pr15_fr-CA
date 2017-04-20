<properties
    pageTitle="Plate-forme prise en charge de migration de IaaS ressources classique pour le Gestionnaire de ressources Azure | Microsoft Azure"
    description="Cet article explique la migration de plate-forme prise en charge des ressources de classique pour le Gestionnaire de ressources Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plate-forme prise en charge de migration de IaaS ressources classique pour le Gestionnaire de ressources Azure

Dans cet article, nous décrivons comment nous déployons une migration de l’infrastructure comme une ressource de service (IaaS) de la classique pour les modèles de déploiement Gestionnaire de ressources. Vous pouvez en savoir plus sur les [avantages et les fonctionnalités du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md). Nous en détail comment connecter des ressources dans les deux modèles de déploiement qui coexistent dans votre abonnement à l’aide des passerelles de réseau virtuel site-à-site. 

## <a name="goal-for-migration"></a>Objectif de la migration

Le Gestionnaire de ressources permet de déployer des applications complexes grâce à des modèles, configure les ordinateurs virtuels à l’aide des extensions de la machine virtuelle et intègre la gestion des accès et balisage. Azure le Gestionnaire de ressources comprend un déploiement évolutif, parallèle pour les machines virtuelles dans des jeux de disponibilité. Le nouveau modèle de déploiement fournit également la gestion du cycle de vie de calcul, de réseau et de stockage de l’indépendamment. Enfin, un focus est sur l’activation de la sécurité par défaut à l’exécution de machines virtuelles dans un réseau virtuel.

Presque toutes les fonctionnalités du modèle de déploiement classiques sont pris en charge pour le calcul, de réseau et de stockage dans le Gestionnaire de ressources Azure. Pour bénéficier de nouvelles fonctionnalités dans le Gestionnaire de ressources Azure, vous pouvez migrer des déploiements existants à partir du modèle de déploiement standard.

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Modifications apportées à votre automation et l’outillage après la migration

Dans le cadre de la migration de vos ressources à partir du modèle de déploiement standard pour le modèle de déploiement du Gestionnaire de ressources, vous devez mettre à jour votre automation existante ou un outillage pour s’assurer qu’il continue à fonctionner après la migration.

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Sens de la migration de ressources de IaaS de classique pour le Gestionnaire de ressources

Avant de nous descendre dans les détails, examinons la différence entre les données-plan et plan de gestion des opérations sur les ressources IaaS.

- *Plan de gestion* décrit les appels qui entrent dans le plan de gestion ou de l’API pour la modification des ressources. Par exemple, les opérations telles que la création d’une machine virtuelle, le redémarrage d’un ordinateur virtuel et mise à jour d’un réseau virtuel avec un nouveau sous-réseau gérer les ressources en cours d’exécution. Elles ne concernent pas directement se connecter aux instances.
- *Plan de données* (application) décrit le moment de l’exécution de l’application elle-même et implique une interaction avec les instances qui ne passent pas par l’API d’Azure. Accéder à votre site Web ou l’extraction des données à partir d’une instance de SQL Server en cours d’exécution ou un serveur de MongoDB est considérée comme données interaction de plan ou de l’application. Copie d’un objet blob à partir d’un compte de stockage et d’accès à une adresse IP publique SSH ou de RDP sur l’ordinateur virtuel sont également plan de données. Ces opérations de maintenir l’application en cours d’exécution sur le calcul, de réseau et de stockage.

>[AZURE.NOTE] Dans certains scénarios de migration, la plateforme Azure s’arrête, libère et redémarrage de vos ordinateurs virtuels. Cela entraîne un temps mort de données-plan.

## <a name="supported-scopes-of-migration"></a>Portées prises en charge de la migration

Il existe trois étendues de migration qui visent principalement les compute, réseau et stockage. 

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migration de machines virtuelles (pas dans un réseau virtuel)

Dans le modèle de déploiement Gestionnaire de ressources, la sécurité est appliquée par défaut pour vos applications. Tous les ordinateurs virtuels doivent être dans un réseau virtuel dans le modèle de gestionnaire de ressources. Le redémarrage de la plateforme Azure (`Stop`, `Deallocate`, et `Start`) les ordinateurs virtuels dans le cadre de la migration. Vous avez deux options pour les réseaux virtuels :

- Vous pouvez demander la plate-forme pour créer un nouveau réseau virtuel et de migrer l’ordinateur virtuel dans le nouveau réseau virtuel.
- Vous pouvez migrer l’ordinateur virtuel dans un réseau virtuel dans le Gestionnaire de ressources.

>[AZURE.NOTE] Dans cette portée de migration, à la fois les opérations du plan de gestion et les opérations de données-plan ne pourra être pour une période de temps pendant la migration.

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migration des ordinateurs virtuels (dans un réseau virtuel)

Pour la plupart des configurations d’ordinateur virtuel, seules les métadonnées migre entre les modèles de déploiement classique et Gestionnaire de ressources. Les ordinateurs virtuels sous-jacentes sont en cours d’exécution sur le même matériel, dans le même réseau et avec le même stockage. Les opérations de gestion plan ne pourra être pendant une certaine période de temps pendant la migration. Toutefois, le plan de données continue de fonctionner. Autrement dit, vos applications s’exécutant sur des machines virtuelles (classiques) n’entraînent pas de temps d’arrêt lors de la migration.

Les configurations suivantes ne sont actuellement pas pris en charge. Si la prise en charge est ajoutée à l’avenir, certains ordinateurs virtuels de cette configuration peut subir des interruptions de service (allez à l’arrêt, libérer et redémarrer les opérations de la machine virtuelle).

-   Vous avez plus d’une disponibilité définie dans un service cloud unique.
-   Vous avez un ou plusieurs ensembles de disponibilité et les ordinateurs virtuels qui ne sont pas une disponibilité définie dans un service cloud unique.

>[AZURE.NOTE] Dans cette portée de la migration, le plan de gestion ne pourra être pour une période de temps pendant la migration. Pour certaines configurations comme indiqué plus haut, temps d’indisponibilité des données-plan se produit.

### <a name="storage-accounts-migration"></a>Migration des comptes de stockage

Pour permettre une migration transparente, vous pouvez déployer des ordinateurs virtuels de gestionnaire de ressources dans un compte de stockage classique. Avec cette fonctionnalité, les ressources informatiques et réseau peuvent et doivent être migrées indépendamment des comptes de stockage. Une fois la migration sur vos Machines virtuelles et des réseaux virtuels, vous devez migrer vos comptes de stockage pour terminer le processus de migration. 

>[AZURE.NOTE] Le modèle de déploiement du Gestionnaire de ressources n’a pas le concept des images de standard et des disques. Lorsque le compte de stockage est migrés, le classiques des images et disques ne sont pas visibles dans la pile du Gestionnaire de ressources, mais le stockage de disques durs virtuels restent dans le compte de stockage. 

## <a name="unsupported-features-and-configurations"></a>Configurations et les fonctionnalités non prises en charge

Nous ne pas actuellement en charge des fonctionnalités et des configurations. Les sections suivantes décrivent des nos recommandations autour d’eux.

### <a name="unsupported-features"></a>Fonctionnalités non prises en charge

Les fonctionnalités suivantes ne sont actuellement pas pris en charge. Vous pouvez éventuellement supprimer ces paramètres migrer les ordinateurs virtuels et activer de nouveau les paramètres dans le modèle de déploiement du Gestionnaire de ressources.

Fournisseur de ressources | Fonctionnalité
---------- | ------------
Calculer | Disques des machines virtuelles non associée.
Calculer | Images de machine virtuelle.
Réseau | ACL du point de terminaison.
Réseau | Passerelles de réseau virtuel (ExpressRoute d’Azure, passerelle d’application, de site à site, pointez sur site).
Réseau | Réseaux virtuels à l’aide de l’homologation de VNet. (Migration de VNet vers ARM, puis de l’homologue) En savoir plus sur [homologation VNet] (... /Virtual-Network/Virtual-Network-Peering-Overview.MD).
Réseau | Profils de gestionnaire de trafic.

### <a name="unsupported-configurations"></a>Configurations non prises en charge

Les configurations suivantes ne sont actuellement pas pris en charge.

Service | Configuration de | Recommandation
---------- | ------------ | ------------
Gestionnaire de ressources | Rôle de base de contrôle d’accès (RBAC) pour les ressources classiques | Car l’URI des ressources est modifié après la migration, il est recommandé de planifier les mises à jour de stratégie RBAC à effectuer après la migration.
Calculer | Plusieurs sous-réseaux associés à une machine virtuelle | Mettre à jour la configuration du sous-réseau pour référencer uniquement les sous-réseaux.
Calculer | Machines virtuelles qui appartiennent à un réseau virtuel, mais n’avez pas un sous-réseau explicit affecté | Vous pouvez éventuellement supprimer la machine virtuelle.
Calculer | Machines virtuelles disposant d’alertes, stratégies d’échelle automatique | La migration s’effectue par le biais d’et ces paramètres sont ignorés. Il est vivement recommandé d’évaluer votre environnement avant de procéder à la migration. Sinon, vous pouvez reconfigurer les paramètres d’alerte une fois la migration terminée.
Calculer | Extensions XML VM (BGInfo 1.*, débogueur de Visual Studio, Web Deploy et le débogage distant) | Ce n’est pas pris en charge. Il est recommandé de vous supprimez ces extensions à partir de l’ordinateur virtuel afin de continuer la migration ou ils seront supprimés automatiquement au cours du processus de migration.
Calculer | Démarrage des diagnostics avec stockage de prime | Désactiver les Diagnostics de démarrage pour les ordinateurs virtuels avant de poursuivre la migration. Vous pouvez réactiver les diagnostics de démarrage dans la pile du Gestionnaire de ressources une fois la migration terminée. En outre, BLOB qui est utilisés pour la capture d’écran et les journaux de série doit être supprimés afin que vous n’êtes n’est plus chargé pour les objets BLOB.
Calculer | Services en nuage qui contiennent les rôles web/travailleur | Ceci n’est actuellement pas pris en charge.
Réseau | Réseaux virtuels qui contiennent des machines virtuelles et les rôles de web/travailleur |  Ceci n’est actuellement pas pris en charge.
Service d’application Azure | Réseaux virtuels qui contiennent des environnements de Service d’application | Ceci n’est actuellement pas pris en charge.
HDInsight Azure | Réseaux virtuels qui contiennent des services de HDInsight | Ceci n’est actuellement pas pris en charge.
Services du cycle de vie de Microsoft Dynamics | Réseaux virtuels qui contiennent des ordinateurs virtuels qui sont gérés par les Services du cycle de vie de Dynamics | Ceci n’est actuellement pas pris en charge.
Calculer | Extensions du centre de sécurité Azure avec un VNET qui a une passerelle VPN ou ER avec serveur DNS sur les prem | Le centre de sécurité Azure installera automatiquement les extensions sur vos ordinateurs virtuels pour contrôler leur sécurité et déclencher des alertes. Ces extensions en général est automatiquement installées si la stratégie d’Azure le centre de sécurité est activée sur l’abonnement. Migration de passerelle n’est pas pris en charge actuellement et la passerelle doit être supprimé avant de procéder à la validation de la migration, l’accès à internet pour le compte de stockage de machine virtuelle est perdu lorsque la passerelle est supprimée. La migration ne va pas dans ce cas que le blob d’état de l’agent invité ne peut pas être rempli. Il est recommandé de désactiver le centre de sécurité Azure de stratégie sur l’abonnement 3 heures avant de procéder à la migration.

## <a name="the-migration-experience"></a>L’expérience de migration

Avant de commencer l’expérience de la migration, la configuration suivante est recommandée :

- Assurez-vous que les ressources que vous souhaitez migrer n’utilisent pas des fonctionnalités non prises en charge ou des configurations. Généralement, la plate-forme détecte ces problèmes et génère une erreur.
- Si vous avez des ordinateurs virtuels qui ne sont pas dans un réseau virtuel, elles seront arrêtés et libérés dans le cadre de l’opération de préparation. Si vous ne souhaitez pas perdre l’adresse IP publique, regardez dans la réservation de l’adresse IP avant de déclencher l’opération de préparation. Toutefois, si les ordinateurs virtuels sont dans un réseau virtuel, ils ne sont pas arrêtés et libérés.
- Planifier votre migration pendant les heures creuses afin de résoudre les défaillances éventuelles inattendus peuvent se produire lors de la migration.
- Télécharger la configuration actuelle de vos ordinateurs virtuels à l’aide de PowerShell, les commandes de l’interface de ligne de commande (CLI) ou autres API pour simplifier la validation une fois l’étape de préparation terminée.
- Mettre à jour vos scripts d’automation/operationalization pour traiter le modèle de déploiement du Gestionnaire de ressources avant de démarrer la migration. Vous pouvez éventuellement effectuer les opérations GET lorsque les ressources sont en état préparé.
- Évaluer les stratégies RBAC qui sont configurées sur les ressources IaaS classiques et prévoir une fois la migration terminée.

Le processus de migration est comme suit.

![Capture d’écran qui illustre le processus de migration](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Toutes les opérations décrites dans les sections suivantes sont idempotents. Si vous avez un problème autre qu’une fonction non prise en charge ou une erreur de configuration, il est recommandé de relancer la préparation, l’annulation ou opération de validation. La plateforme Azure tente à nouveau de l’action.

### <a name="validate"></a>Valider

L’opération de validation est la première étape dans le processus de migration. L’objectif de cette étape consiste à analyser les données en arrière-plan pour les ressources en cours de migration et de réussite/échec de retour si les ressources sont capables de migration.

Vous sélectionnez le réseau virtuel ou le service hébergé (si elle n’est pas un réseau virtuel) que vous souhaitez valider pour la migration.

* Si la ressource n’est pas capable de la migration, la plateforme Azure répertorie toutes les raisons de la raison pour laquelle il n’est pas disponible pour la migration.

### <a name="prepare"></a>Préparer

L’opération de préparation est la deuxième étape du processus de migration. L’objectif de cette étape est de simuler la transformation des ressources IaaS de classique à des ressources du Gestionnaire de ressources et de les présenter côte à côte pour que vous puissiez visualiser.

Vous sélectionnez le réseau virtuel ou le service hébergé (si elle n’est pas un réseau virtuel) que vous souhaitez préparer pour la migration.

* Si la ressource n’est pas capable de la migration, la plateforme Azure arrête le processus de migration et indique la raison de l’échec de l’opération de préparation.
* Si la ressource est capable de la migration, la plateforme Azure premier verrouille les opérations du plan de gestion pour les ressources en cours de migration. Par exemple, vous n’êtes pas en mesure d’ajouter un disque de données à un ordinateur virtuel en cours de migration.

La plateforme Azure puis commence la migration de métadonnées standard pour le Gestionnaire de ressources pour les migration des ressources.

Une fois l’opération de préparation terminée, vous avez la possibilité de visualiser les ressources dans les deux classique et Gestionnaire de ressources. Pour chaque service de cloud dans le modèle de déploiement classique, la plateforme Azure crée un nom de groupe de ressources avec le modèle de `cloud-service-name>-migrated`.

>[AZURE.NOTE] Les Machines virtuelles qui ne sont pas dans un réseau virtuel classique sont arrêtés libérés dans cette phase de la migration.

### <a name="check-manual-or-scripted"></a>Vérification (manuelle ou par script)

Dans l’étape de vérification, vous pouvez éventuellement utiliser la configuration que vous avez téléchargé précédemment pour valider que la migration semble correcte. Vous pouvez également vous connecter au portail et le place les propriétés et les ressources pour valider que la migration des métadonnées est une bonne.

Si vous migrez un réseau virtuel, la plupart des configuration d’ordinateurs virtuels n’est pas redémarrée. Pour les applications sur les ordinateurs virtuels, vous pouvez vérifier que l’application est toujours en cours d’exécution.

Vous pouvez tester votre contrôle/automation et les scripts d’exploitation pour voir si les ordinateurs virtuels fonctionnent comme prévu et si vos scripts de mise à jour fonctionnent correctement. Seules les opérations GET sont prises en charge lorsque les ressources sont en état préparé.

Il n’y a aucune fenêtre ensemble avant laquelle vous devez valider la migration. Vous pouvez prendre autant de temps que vous le souhaitez dans cet état. Toutefois, le plan de gestion est verrouillé pour ces ressources jusqu'à ce que vous l’annulation ou la validez.

Si vous voyez des problèmes, vous pouvez toujours annuler la migration et revenir en arrière pour le modèle de déploiement classique. Une fois que vous revenez en arrière, la plateforme Azure ouvre les opérations de gestion-plan sur les ressources afin de vous pouvez reprendre les opérations normalement sur ces ordinateurs virtuels dans le modèle de déploiement classique.

### <a name="abort"></a>Abandonner

Abandon est une étape facultative que vous pouvez utiliser pour annuler vos modifications au modèle de déploiement classiques et d’arrêter la migration.

>[AZURE.NOTE] Cette opération ne peut pas être exécutée une fois que vous avez déclenché l’opération de validation.  

### <a name="commit"></a>Valider

Après avoir terminé la validation, vous pouvez valider la migration. Les ressources n’apparaissent pas de plus dans classique et sont uniquement disponibles dans le modèle de déploiement du Gestionnaire de ressources. Les ressources migrées peuvent être gérés que dans le nouveau portail.

>[AZURE.NOTE] Il s’agit d’une opération équipotente. En cas d’échec, il est recommandé de relancer l’opération. S’il continue à échouer, créer un ticket de support ou de créer un billet avec une balise ClassicIaaSMigration sur notre [forum de machine virtuelle](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

## <a name="frequently-asked-questions"></a>Forum aux questions

**Ce plan de migration affecte aucun de mes services existants ou les applications qui s’exécutent sur des machines virtuelles Azure ?**

N° Les ordinateurs virtuels (classiques) sont des services totalement pris en charge dans la disponibilité générale. Vous pouvez continuer à utiliser ces ressources pour développer votre empreinte sur Microsoft Azure.

**Que se passe-t-il à mes machines virtuelles si je n’envisage pas de migration dans un avenir proche ?**

Nous ne sommes pas rejetant les API classiques existantes et le modèle de ressource. Nous souhaitons que faciliter la migration, en tenant compte des fonctionnalités avancées qui sont disponibles dans le modèle de déploiement du Gestionnaire de ressources. Nous vous recommandons vivement de consulter [certains des améliorations](virtual-machines-windows-compare-deployment-models.md) qui font partie de IaaS sous le Gestionnaire de ressources.

**Que signifie ce plan de migration pour mes outils existants ?**

Mise à jour de vos outils vers le modèle de déploiement du Gestionnaire de ressources est une des modifications plus importantes que vous devez tenir compte dans vos plans de migration.

**La durée est le temps d’indisponibilité du plan de gestion ?**

Cela dépend du nombre de ressources qui sont en cours de migration. Pour les déploiements plus petits (de quelques dizaines de machines virtuelles), complet de la migration doit prendre moins d’une heure. Pour les déploiements à grande échelle (plusieurs centaines de machines virtuelles), la migration peut prendre quelques heures.

**Puis-je restaurer après que ma migration des ressources sont validées dans le Gestionnaire de ressources ?**

Vous pouvez annuler votre migration tant que les ressources sont en état préparé. La restauration n’est pas pris en charge après que les ressources ont été correctement migrés par le biais de l’opération de validation.

**Puis-je restaurer ma migration en cas d’échec de l’opération de validation ?**

Impossible d’arrêter la migration si l’opération de validation échoue. Toutes les opérations de migration, y compris l’opération de validation, sont idempotents. Nous vous recommandons de relancer l’opération après un bref moment. Si vous êtes toujours confronté à une erreur, créer un ticket de support ou de créer un billet avec la balise ClassicIaaSMigration sur notre [forum de machine virtuelle](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

**Dois-je acheter un autre circuit de voie express si je dois utiliser IaaS sous le Gestionnaire de ressources ?**

N° Nous avons récemment activé le [déplacement ExpressRoute des circuits de standard pour le modèle de déploiement du Gestionnaire de ressources](../expressroute/expressroute-move.md). Vous n’êtes pas obligé d’acheter un nouveau circuit de ExpressRoute si vous avez déjà un.

**Que se passe-t-il si j’avais configuré les stratégies de contrôle d’accès basée sur les rôles pour mes ressources IaaS classiques ?**

Lors de la migration, les ressources de transformer de classique pour le Gestionnaire de ressources. Par conséquent, nous vous recommandons de planifier les mises à jour de stratégie RBAC à effectuer après la migration.

**Que se passe-t-il si j’utilise Azure sauvegarde ou récupération de Site Azure aujourd'hui ?**

Pour migrer votre Machine virtuelle qui sont activés pour la sauvegarde, voir [J’ai sauvegardé mon VMs classiques dans le coffre-fort de la sauvegarde. Je souhaite à présent migrer mes machines virtuelles à partir du mode classique au mode du Gestionnaire de ressources. Comment puis-je sauvegarder les dans le coffre-fort de services de récupération ?](../backup/backup-azure-backup-ibiza-faq.md#i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault)

**Puis-je valider mon abonnement ou des ressources pour voir si elles sont capables de migration ?**

Oui. Dans l’option de migration de plate-forme prise en charge, la première étape de la préparation de la migration est de valider que les ressources sont capables de migration. Dans le cas où l’opération de validation échoue, vous recevez des messages pour toutes les raisons que la migration ne peut pas être effectuée.

**Que se passe-t-il si j’exécute dans une erreur de quota lors de la préparation des ressources IaaS pour la migration ?**

Nous vous conseillons abandonner votre migration et puis ouvrez une demande de support pour augmenter les quotas dans la région où vous migrez les ordinateurs virtuels. Une fois la demande de quota est approuvée, vous pouvez démarrer l’exécution de la procédure de migration à nouveau.

**Comment signaler un problème ?**

Validez vos problèmes et les questions de migration sur notre [forum de machine virtuelle](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows), avec le mot clé ClassicIaaSMigration. Nous vous recommandons de valider toutes vos questions dans ce forum. Si vous avez un contrat de support, vous êtes invité à ouvrir une session et un ticket de support.

**Que se passe-t-il si je n’aime pas les noms des ressources que vous avez choisie lors de la migration de la plate-forme ?**

Toutes les ressources que vous fournissez explicitement pour les noms dans le modèle de déploiement classique sont conservées au cours de la migration. Dans certains cas, les nouvelles ressources sont créées. Par exemple : une interface réseau est créée pour chaque machine virtuelle. Nous avons actuellement ne prend en charge la capacité à contrôler les noms de ces nouvelles ressources créés pendant la migration. Connectez-vous à votre voix de cette fonctionnalité sur le [forum de commentaires Azure](http://feedback.azure.com).

* *Un message *« VM signale l’état global de l’agent comme n’est pas prêt. Par conséquent, la machine virtuelle ne peut pas être migrée. Assurez-vous que l’Agent de la machine virtuelle signale l’état global de l’agent sous forme de prêts à l’emploi »* ou *« machine virtuelle contient Extension dont le statut n’est pas signalé à partir de la machine virtuelle. Par conséquent, cette machine virtuelle ne peut pas être migrée. »***

Ce message est affiché lorsque la machine virtuelle n’a pas de connectivité sortante vers internet. L’agent de la machine virtuelle utilise connectivité sortante pour atteindre le compte de stockage Azure pour mettre à jour l’état de l’agent toutes les cinq minutes.


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous comprenez la migration des ressources IaaS classique pour le Gestionnaire de ressources, vous pouvez commencer la migration des ressources.

- [Technique approfondie sur la migration de plate-forme prise en charge de classique pour le Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Utilisation de PowerShell pour migrer des ressources IaaS de standard pour le Gestionnaire de ressources Azure](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Infrastructure du langage commun permet de migrer des ressources IaaS de classique pour le Gestionnaire de ressources Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Dupliquer une machine virtuelle classique pour le Gestionnaire de ressources Azure à l’aide de scripts PowerShell de communauté](virtual-machines-windows-migration-scripts.md)
