<properties
    pageTitle="Créer des plans de récupération | Microsoft Azure" 
    description="Créer des plans de récupération avec la récupération de Site Azure de basculer et restaurer les groupes de machines virtuelles et des serveurs physiques." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="create-recovery-plans"></a>Créer des plans de récupération

Le service de récupération de Site Azure contribue à votre stratégie de récupération (BCDR) de continuité d’activité et de reprise après sinistre de l’entreprise par l’orchestration de réplication, le basculement et la récupération de machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire sur site. Pour une vue d’ensemble rapide de lecture [ce qu’est la récupération de Site Azure ?](site-recovery-overview.md).


## <a name="overview"></a>Vue d’ensemble

Cet article fournit des informations sur la création et la personnalisation des plans de reprise. 

Les plans de récupération se composent d’un ou plusieurs groupes ordonnés qui contiennent des machines virtuelles ou des serveurs physiques que vous souhaitez basculer entre eux. Plans de récupération d’effectuer les opérations suivantes :

- Définir des groupes d’ordinateurs qui basculent et puis démarrent ensemble.
- Dépendances de modèle entre des machines en les groupant ensemble dans un groupe de plan de récupération. Pour exemple, si vous souhaitez basculer et afficher une application spécifique vous regroupera les ordinateurs virtuels pour cette application dans le même groupe de plan de récupération.
- Automatiser et étendre le basculement. Vous pouvez exécuter un test, planifié ou un basculement non planifié sur un plan de récupération. Vous pouvez personnaliser des plans de récupération avec des scripts, l’automatisation Azure et actions manuelles.

Les plans de reconstitution sont affichés sur les **Plans de récupération** dans le portail de récupération de Site.


Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum des Services Azure récupération](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Avant de commencer

Notez les points suivants :

- Un plan de récupération ne doit pas mélanger des ordinateurs virtuels avec une seule ou plusieurs cartes réseau. C’est parce que le mélange de ces ordinateurs virtuels n’est pas autorisée dans un service cloud Azure.
- Vous pouvez étendre les actions manuelles et les scripts, les plans de reprise. Notez les points suivants :
    - Écrire des scripts à l’aide de Windows PowerShell.
    - Vérifiez que les scripts utilisent des blocs try-catch afin que les exceptions sont traitées correctement. S’il existe une exception dans le script il s’arrête et la tâche affiche comme ayant échoué.  Si une erreur se produit, toute partie restante du script ne s’exécutera pas. Si cela se produit lorsque vous exécutez un basculement non planifié, le plan de récupération va continuer. Si cela se produit lorsque vous exécutez un basculement planifié, le plan de restauration s’arrête. Dans ce cas, corrigez le script, assurez-vous qu’il s’exécute comme prévu et puis exécutez à nouveau le plan de récupération.
    - La commande Write-Host ne fonctionne pas dans un script de plan de reprise, et le script échouera. Si vous souhaitez créer une sortie, créer un script de proxy qui à son tour s’exécute votre script principal et s’assurer que toute la sortie est transmise à l’aide de la >> commande.
    - Le script expire si elle ne retourne pas de 600 secondes.
    - Si quoi que ce soit est écrites dans STDERR, le script est considéré comme ayant échoué. Ces informations seront afficheront dans les détails de l’exécution du script.
    - Si vous utilisez VMM dans votre déploiement Notez que :

        - Dans un plan de récupération, les scripts s’exécutent dans le contexte du compte de Service VMM. Assurez-vous que ce compte dispose des autorisations de lecture sur le partage distant sur lequel se trouve le script et tester le script à exécuter au niveau de privilège du compte de service VMM.
        - Applets de commande VMM sont remis dans un module de Windows PowerShell. Le module de VMM Windows PowerShell est installé lorsque vous installez la console VMM. Le module VMM peut être chargé dans votre script en utilisant la commande suivante dans le script : Import-Module-virtualmachinemanager de nom. [Obtenir plus de détails](hhttps://technet.microsoft.com/library/hh875013.aspx).
        - Vérifiez que vous avez au moins un serveur de bibliothèque dans votre déploiement de VMM. Par défaut le chemin d’accès du partage de bibliothèque pour un serveur VMM réside localement sur le serveur VMM, avec le nom de dossier MSCVMMLibrary.
        - Si votre chemin d’accès du partage de bibliothèque est distante (ou locale mais pas partagé avec MSCVMMLibrary, configurez le partage comme suit (à l’aide de \\libserver2.contoso.com\share\ par exemple) :
            - Ouvrez l’Éditeur du Registre et accédez à Recovery\Registration de Site HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure.
            -  Modifier la valeur de ScriptLibraryPath et de placer la valeur en tant que \\libserver2.contoso.com\share\. spécifier le FQDN complet. Accorder des autorisations à l’emplacement partagé.
            -  Veillez à tester le script avec un compte d’utilisateur qui dispose des mêmes autorisations que le compte de service VMM, pour s’assurer que les scripts testés autonomes s’exécuter de la même façon qu’ils ne le sont dans les plans de récupération. Sur le serveur VMM, définissez la stratégie d’exécution de contourner comme suit :
                -  Ouvrez la console Windows PowerShell de 64 bits à l’aide de privilèges élevés.
                -  Type : **Set-executionpolicy ignorer**. [Obtenir plus de détails](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Créer un plan de récupération

Le mode dans lequel vous créez un plan de récupération dépend de votre déploiement de récupération de Site.

- **Réplication des machines virtuelles VMware et des serveurs physiques**, vous créez un plan et ajouter des groupes de réplication qui contiennent des machines virtuelles et des serveurs physiques à un plan de récupération.
- **Réplication des ordinateurs virtuels Hyper-V (dans les nuages VMM)**, vous créez un plan et ajouter protégés machines virtuelles de Hyper-V à partir d’un nuage VMM à un plan de récupération.
- **Réplication des ordinateurs virtuels Hyper-V (et non dans les nuages VMM)**: créez un plan et ajouter protégés machines virtuelles de Hyper-V à partir d’un groupe de protection à un plan de récupération.
- **La réplication SAN**: créer un plan et d’ajouter un groupe de réplication contenant des machines virtuelles pour le plan de récupération. Vous sélectionnez un groupe de réplication plutôt que des machines virtuelles spécifiques, car tous les ordinateurs virtuels dans un groupe de réplication doit basculer entre eux (basculement sur incident se produit au niveau de la couche de stockage tout d’abord).


Créez un plan de récupération comme suit :

1. Cliquez sur onglet de **Plans de récupération** > **Créer un Plan de récupération**.
Spécifiez un nom pour le plan de récupération et une source et une cible. Le serveur source doit avoir les ordinateurs virtuels qui sont activés pour le basculement et la restauration.

    - Si vous utilisez la réplication à partir de VMM à VMM, sélectionnez **Type de Source** > **VMM**et les serveurs source et cible de VMM. Cliquez sur **Hyper-V** pour voir les nuages qui sont configurés pour utiliser Hyper-V Replica. 
    - Si vous utilisez la réplication à partir de VMM à VMM à l’aide de SAN sélectionner un **Type de Source** > **VMM**et les serveurs source et cible de VMM. Cliquez sur **SAN** pour voir les nuages qui sont configurés pour la réplication du SAN.
    - Si vous utilisez la réplication à partir de VMM pour Azure sélectionner le **Type de Source** > **VMM**.  Sélectionnez le serveur VMM de source et **Azure** comme cible.
    - Si vous utilisez la réplication à partir d’un site de Hyper-V, sélectionnez **Type de Source** > **site de Hyper-V**. Sélectionnez le site comme source et **Azure **comme cible.
    - Si vous utilisez la réplication auprès de VMware ou d’un serveur physique sur site vers Azure, sélectionnez un serveur de configuration comme source et **Azure** comme cible

2. Dans la zone **Sélectionner les ordinateurs virtuels** , sélectionnez les ordinateurs virtuels (ou un groupe de réplication) que vous souhaitez ajouter au groupe par défaut (groupe 1) dans le plan de récupération.

## <a name="customize-recovery-plans"></a>Personnaliser des plans de récupération

Après que vous avez ajouté protégé des ordinateurs virtuels ou groupes de réplication dans le groupe plan de récupération par défaut et créé le plan, que vous pouvez le personnaliser :

- **Ajouter de nouveaux groupes**, vous pouvez ajouter des groupes de plans de récupération supplémentaires. Groupes que vous ajoutez sont numérotées dans l’ordre dans lequel vous les ajoutez. Vous pouvez ajouter jusqu'à sept groupes. Vous pouvez ajouter des ordinateurs ou des groupes de réplication plus à ces nouveaux groupes. Notez qu’un ordinateur virtuel ou un groupe de réplication peut figurer uniquement dans le groupe plan de récupération d’un seul.
- **Ajout d’un script **, vous pouvez ajouter des scripts qui, avant ou après une restauration, planification de groupe. Lorsque vous ajoutez un script, il ajoute un nouvel ensemble d’actions pour le groupe. Par exemple un ensemble d’étapes pour le groupe 1 sera créé avec le nom : groupe 1 : étapes préalables. Toutes les étapes préalables apparaît à l’intérieur de cet ensemble. Notez que vous pouvez uniquement ajouter un script sur le site principal si vous avez un serveur VMM déployé.
- **Ajouter une action manuelle**, vous pouvez ajouter des actions manuelles qui s’exécutent avant ou après un groupe de plan de récupération. Lors de l’exécution du plan de récupération, il s’arrête à l’endroit où vous avez inséré l’action manuelle et une boîte de dialogue vous invite à spécifier que l’action manuelle a été terminée.

## <a name="extend-recovery-plans-with-scripts"></a>Étendre des plans de récupération avec des scripts

Vous pouvez ajouter un script à votre plan de récupération :

- Si vous avez un site de source VMM, vous pouvez créer un script sur le serveur VMM et l’inclure dans votre plan de récupération.
- Si vous effectuez une réplication vers Azure vous pouvez intégrer des procédures opérationnelles d’Azure automation dans votre plan de récupération

### <a name="create-a-vmm-script"></a>Créer un script VMM


Créez le script comme suit :

1. Créer un nouveau dossier dans le partage de bibliothèque, par exemple \<VMMServerName > \MSSCVMMLibrary\RPScripts. Placer sur la source et cible les serveurs VMM.
2. Créez le script (par exemple RPScript) et vérifier qu’il fonctionne comme prévu.
3. Placez le script dans l’emplacement \<VMMServerName > \MSSCVMMLibrary sur les serveurs source et cible de VMM.

### <a name="create-an-azure-automation-runbook"></a>Créer une procédure d’automatisation Azure opérationnelle

Vous pouvez étendre votre plan de récupération en exécutant une procédure opérationnelle d’Azure automation dans le cadre du plan. [Pour en savoir plus](site-recovery-runbook-automation.md).


### <a name="add-custom-settings-to-a-recovery-plan"></a>Ajouter des paramètres personnalisés à un plan de récupération

1. Ouvrez le plan de récupération que vous souhaitez personnaliser.
2. Cliquez pour ajouter un ordinateur virtuel ou un nouveau groupe.
3. Ajouter un script ou un manuel action Cliquez sur n’importe quel élément dans la liste de **l’étape** , puis cliquez sur **Script** ou **Action manuelle**. Indiquez si vous souhaitez ajouter le script ou l’action avant ou après l’élément sélectionné. Utilisez les boutons de commande **Déplacer vers le haut** et **Déplacer vers le bas** pour déplacer la position du script vers le haut ou vers le bas.
4. Si vous ajoutez un script VMM, sélectionnez **basculement au script VMM**et dans chemin d’accès du **Script** , tapez le chemin d’accès relatif pour le partage. C’est le cas, pour notre exemple où se trouve le partage à \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, spécifiez le chemin d’accès : \RPScripts\RPScript.PS1.
5. Si vous ajoutez une automation Azure Edite le Registre, spécifiez le **Compte d’Automation Azure** dans lequel se trouve la procédure opérationnelle et sélectionnez le **Script de procédure opérationnelle Azure**de approprié.
5. Effectuez un basculement du plan de récupération pour vous assurer que le script fonctionne comme prévu.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez exécuter différents types de basculement sur le plan de récupération, y compris un basculement test pour vérifier votre environnement et les basculements planifiés ou non planifiés. [En savoir plus](site-recovery-failover.md).


 
