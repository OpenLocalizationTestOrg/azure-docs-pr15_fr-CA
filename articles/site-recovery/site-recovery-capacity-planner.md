<properties
    pageTitle="Planification de capacité pour la protection des machines virtuelles et physiques dans la récupération de Site Azure | Microsoft Azure"
    description="Récupération de Site Azure coordonne la réplication, le basculement et la restauration des machines virtuelles et des serveurs physiques situés sur Azure ou sur un site secondaire sur site local." 
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
    ms.date="07/12/2016" 
    ms.author="raynew"/>

# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Planification de capacité pour la protection des machines virtuelles et physiques dans la récupération de Site Azure

L’outil Planificateur de capacité de restauration de Site Azure vous aide à déterminer vos besoins de protection des ordinateurs virtuels Hyper-V, machines virtuelles de VMware et des serveurs physiques Windows/Linux avec récupération de Site Azure.


## <a name="overview"></a>Vue d’ensemble

Le Planificateur de capacités de récupération de Site permet d’analyser votre environnement source et les charges de travail et déterminer les besoins en bande passante et les ressources serveur que vous avez besoin dans votre emplacement source pour les ressources (machines virtuelles et stockage, etc.), que vous aurez besoin dans votre emplacement cible. 

Vous pouvez exécuter l’outil de deux modes :

- **Planification rapide**: exécuter l’outil dans ce mode pour obtenir des projections de réseau et de serveur basé sur un nombre moyen de machines virtuelles, des disques, de stockage et taux de modification.
- **La planification détaillée**: exécutez l’outil dans ce mode et fournir les détails de chaque charge de travail au niveau de la machine virtuelle. Analyser la compatibilité de la machine virtuelle et obtenir des projections de réseau et de serveur.

## <a name="before-you-start"></a>Avant de commencer

Avant d’exécuter l’outil :

1. Rassembler des informations sur votre environnement, y compris les machines virtuelles, des disques par ordinateur virtuel, le stockage par disque.
2. Identifiez votre taux de modifications (évolution du) quotidiennes pour les données répliquées. Pour ce faire :

    - Si vous êtes répliquer des ordinateurs virtuels Hyper-V, téléchargez l' [outil de planification de capacité de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) pour obtenir le taux de modification. [En savoir plus](site-recovery-capacity-planning-for-hyper-v-replication.md) sur cet outil. Nous vous recommandons de qu'exécuter cet outil sur une semaine pour capturer les moyennes.
    - Si vous utilisez la réplication des machines virtuelles VMware, utiliser la [solution matérielle-logicielle de planification de la capacité vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) pour déterminer le taux d’évolution du.
    - Si vous utilisez la réplication des serveurs physiques, vous devrez évaluer manuellement.

## <a name="run-the-quick-planner"></a>Exécution du module Projets rapide
1.  Téléchargez et ouvrez l’outil [Planificateur de capacité de restauration de Site Azure](http://aka.ms/asr-capacity-planner-excel) . Vous aurez besoin exécuter les macros ainsi, sélectionnez cette option pour activer la modification et activer le contenu lorsque vous y êtes invité. 
2.  Dans la zone **Sélectionnez un type d’agent de planification** sélectionnez **Module rapide** à partir de la zone de liste.

    ![Mise en route](./media/site-recovery-capacity-planner/getting-started.png)

3.  Dans la feuille de calcul **Capacity Planner** , entrez les informations requises. Vous devez renseigner tous les champs entourées en rouge dans la capture d’écran ci-dessous.

    - Dans la zone **Sélectionnez votre scénario** choisissez **Hyper-V pour Azure** ou **VMware/physiques vers Azure**.
    - Dans la **moyenne des données quotidiennes modifier taux (%)** dans les informations vous collecter à l’aide de l' [outil de planification de capacité de Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou la [solution matérielle-logicielle de planification de la capacité vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
    - **La compression** s’applique uniquement à la compression proposée lors de la réplication des ordinateurs virtuels VMware ou les serveurs physiques vers Azure. Nous estimons à 30 % ou plus, mais vous pouvez modifier le paramètre selon vos besoins. Pour répliquer des ordinateurs virtuels Hyper-V vers Azure compression, vous pouvez utiliser un matériel tiers tel que Riverbed. 
    -  Dans **Les entrées de rétention** spécifier combien de temps les réplicas doivent être conservées. Si vous utilisez la réplication VMware ou la valeur d’entrée de serveurs physiques en jours. Si vous utilisez la réplication Hyper-V la spécifier en heures.
    -  Les paramètres qui sont utilisés pour calculer les besoins en réplication initiale d’entrée dans le **nombre d’heures de réplication initiale pour le lot de machines virtuelles doit se terminer** et le **nombre de machines virtuelles par lot de la réplication initiale** .  Lors de la récupération de Site est déployée, l’ensemble de données initial doit être téléchargé. 

    ![Entrées](./media/site-recovery-capacity-planner/inputs.png)

2.  Une fois que vous avez mis les valeurs de l’environnement source, la sortie affichée inclut :

    - **La bande passante requise pour la réplication delta** (Mo/s). La bande passante du réseau pour la réplication delta est calculée sur le taux de modification des données quotidienne moyenne.
    - **La bande passante requise pour la réplication initiale** (Mo/s). Les valeurs de la réplication initiale que vous placez dans la bande passante du réseau pour la réplication initiale est calculée. 
    - **Stockage requis (en Go)** est le stockage Azure total requis.
    - **E/s total sur les comptes de stockage standard** est calculé en fonction de la taille de l’unité 8 K IOPS sur les comptes de stockage standard.  Modifier les taux pour le planificateur rapide de que ce nombre est calculé en fonction de tous les disques des ordinateurs virtuels de source de données quotidiennes. Pour le Planificateur de détaillées que le nombre est calculé selon le nombre total d’ordinateurs virtuels qui sont mappés vers Azure VM standard et données Modifier taux sur ces ordinateurs virtuels. 
    - **Nombre de comptes de stockage standard** fournit le nombre total de comptes de stockage standard nécessaires pour protéger les ordinateurs virtuels. Notez qu’un compte de stockage standard peut contenir jusqu'à 20 000 IOPS sur tous les ordinateurs virtuels dans un stockage standard et Ops ES/500 s maximales pris en charge par disque. 
    - **Nombre de disques de blob requis** donne le nombre de disques qui sera créé sur le stockage Azure.
    - **Nombre de primes de stockage requis** fournit le nombre total des primes de stockage nécessaires pour protéger les ordinateurs virtuels. Notez qu’une machine virtuelle avec e/s élevé (plus de 20000) de la source doit être un compte de stockage premium. Un compte de stockage premium peut contenir jusqu'à 80 000 IOPS.
    - **Total Ops ES/s sur le stockage de la prime** est calculée sur base 256 K taille d’unité d’e/s sur les comptes de stockage total de la prime.  Modifier les taux pour le planificateur rapide de que ce nombre est calculé en fonction de tous les disques des ordinateurs virtuels de source de données quotidiennes. Pour le module détaillé le nombre est calculé en fonction du nombre total d’ordinateurs virtuels qui sont mappées à la prime Azure VM (série DS et GS) et les données Modifier taux sur ces ordinateurs virtuels. 
    - **Nombre de serveurs de la configuration requise** indique le nombre de serveurs de configuration est requis pour le déploiement (1)
    - **Nombre de serveurs de traitement supplémentaires requis** indique si les serveurs de traitement supplémentaires sont nécessaires en plus du serveur de processus qui est configuré sur le serveur de configuration par défaut.
    - **espace de stockage supplémentaire de 100 % sur la source** indique si l’espace de stockage supplémentaire est requise dans l’emplacement source.
            
    ![Sortie](./media/site-recovery-capacity-planner/output.png)
 
## <a name="run-the-detailed-planner"></a>Exécuter l’agent de planification détaillée


1.  Téléchargez et ouvrez l’outil [Planificateur de capacité de restauration de Site Azure](http://aka.ms/asr-capacity-planner-excel) . Vous aurez besoin exécuter les macros ainsi, sélectionnez cette option pour activer la modification et activer le contenu lorsque vous y êtes invité. 
2.  Dans la zone **Sélectionnez un type d’agent de planification** sélectionnez **Agent de planification détaillées** à partir de la zone de liste.

    ![Mise en route](./media/site-recovery-capacity-planner/getting-started-2.png)

3.  Dans la feuille de calcul de **Qualification de la charge de travail** , entrez les informations requises. Vous devez renseigner tous les champs marqués.

    - Dans les **cœurs de processeur** spécifier le nombre de cœurs sur un serveur source.
    - Dans une **allocation de mémoire en Mo** , spécifiez la taille de la mémoire RAM d’un serveur source. 
    - Le **Nombre de cartes réseau** de spécifier le nombre de cartes réseau sur un serveur source. 
    -  Dans **l’espace de stockage Total (en Go)** , spécifiez la taille totale de stockage de la machine virtuelle. Par exemple, si le serveur source a 3 disques de 500 Go, la taille de stockage totale est de 1 500 Go.
    -  Nombre **de disques attachés** spécifient le nombre total de disques d’un serveur source.
    -  **Utilisation de la capacité disque** permet de spécifier l’utilisation moyenne.
    -  **Modifier quotidiennement les taux (%)** de spécifier que les données à jour modifier taux d’un serveur source.
    -  Dans **taille de mappage de Azure** Entrez taille Azure VM que vous souhaitez mapper. Si vous ne souhaitez pas effectuer cette opération manuellement, cliquez sur le**Calcul des machines virtuelles IaaS**. Notez que si vous entrez un paramètre manuel et puis cliquez sur Calculer le VMs IaaS vos paramètres manuels peut être remplacée, car le processus de calcul identifie automatiquement la meilleure taille d’Azure VM.

    ![Qualification de la charge de travail](./media/site-recovery-capacity-planner/workload-qualification.png)

4.  Si vous cliquez sur **Calculer le VMs IaaS** ici est ce qu’il fait :

    - Valide les entrées obligatoires.
    - Calcule les IOPS et suggère la meilleure Azure VM aize de correspondance pour chaque VM qui est éligible pour la réplication vers Azure. Si une taille appropriée d’Azure VM ne peut pas être détectée, qu'une erreur est générée. Par exemple, si le nombre de disques attachés dans 65 une erreur est problèmes depuis la taille plus élevée Azure VM est 64.
    - Indique un compte de stockage qui peut être utilisé pour une machine virtuelle d’Azure.
    - Calcule le nombre total de comptes de stockage standard et les primes de stockage requis pour la charge de travail. Défiler vers le bas à droite pour afficher le type de stockage Azure et le compte de stockage qui peut être utilisé pour un serveur source
    - Se termine et trie le reste de la table basée sur un type de stockage requis (standard ou premium) affecté à une machine virtuelle et le nombre de disques associés. Pour tous les ordinateurs virtuels répondant aux besoins de la sauvegarde d’Azure, la colonne A (est VM qualifié ?) affiche Oui. Si un ordinateur virtuel ne peut pas être sauvegardé sur Azure, une erreur est affichée.

Les colonnes AA à AE sont la sortie et fournissent des informations pour chaque machine virtuelle.

![Qualification de la charge de travail](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### <a name="example"></a>Exemple
Par exemple, pour les machines virtuelles de six avec les valeurs indiquées dans le tableau, l’outil calcule et attribue la meilleure correspondance Azure VM et les besoins en stockage Azure.

![Qualification de la charge de travail](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- Dans la sortie de l’exemple, notez les points suivants :
    
    - La première colonne est une colonne de validation pour les machines virtuelles, des disques et l’évolution du.
    - Deux comptes de stockage standard et compte de stockage qu’une seule prime sont nécessaires pour les machines virtuelles de cinq. 
    -  VM3 ne bénéficier de protection car un ou plusieurs disques sont plus de 1 To.
    -  VM1 et l’ordinateur virtuel 2 peuvent utiliser le premier compte de stockage standard
    -  VM4 pouvez utiliser le deuxième compte de stockage standard.
    -  VM5 et VM6 ont besoin d’un compte de stockage premium et peuvent tous deux utiliser un seul compte.

    >[AZURE.NOTE]  Ops ES/s sur le stockage standard et premium sont calculées au niveau de la machine virtuelle et non au niveau du disque. Un ordinateur virtuel standard peut gérer jusqu'à 500 Ops ES/s par disque. Si les e/s pour un disque sont supérieures à 500, vous devez stockage de prime. Toutefois si Ops ES/s pour un disque de plus de 500 mais Ops ES/s pour les disques de l’ordinateur virtuel total sont dans les limites d’Azure VM standard prise en charge (taille de mémoire virtuelle, nombre de disques, nombre de mémoire de cartes, processeur,) puis le planificateur choisit une norme série de machine virtuelle et non dans la DS ou GS. Vous devez mettre à jour manuellement la cellule Azure taille de mappage avec la série DS ou GS appropriées VM.

5. Une fois que tous les détails sont en place, cliquez sur **Envoyer les données à l’outil de planification** pour ouvrir le **Planificateur de capacités** de charges de travail sont mis en surbrillance pour indiquer si elles sont éligibles pour la protection ou non.


### <a name="submit-data-in-the-capacity-planner"></a>Envoyer des données dans le Planificateur de capacités

1.  Lorsque vous ouvrez la feuille de calcul **Capacity Planner** , elle est remplie en fonction des paramètres que vous avez spécifié. Le mot « Charge » apparaît dans la cellule de la **source des entrées Infra** pour indiquer que la feuille de calcul de **Qualification de la charge de travail** d’entrée. 
2.  Si vous souhaitez apporter des modifications, vous devrez modifier la feuille de calcul de **Qualification de la charge de travail** et cliquez de nouveau sur Envoyer les données à l’outil de planification.  

    ![Planificateur de capacité](./media/site-recovery-capacity-planner/capacity-planner.png)


