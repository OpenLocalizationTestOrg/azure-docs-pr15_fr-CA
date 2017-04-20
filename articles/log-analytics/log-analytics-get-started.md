<properties
    pageTitle="Mise en route de journal Analytique | Microsoft Azure"
    description="Vous pouvez obtenir en cours d’exécution avec Analytique de journal dans la Suite de gestion des opérations Microsoft (OMS) en quelques minutes."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="get-started-with-log-analytics"></a>Mise en route de journal Analytique

Vous pouvez obtenir en cours d’exécution avec Analytique de journal dans la Suite de gestion des opérations Microsoft (OMS) en quelques minutes. Lorsque vous choisissez la création d’un espace de travail de l’OMS, qui est similaire à un compte, vous disposez de deux options :

- Site Web de Microsoft Operations Management Suite
- Abonnement Microsoft Azure

Vous pouvez créer un espace de travail OMS disponible via le site Web OMS. Ou bien, vous pouvez utiliser un abonnement Microsoft Azure pour créer un espace de travail de l’OMS. Les deux espaces de travail sont fonctionnellement équivalents, mais un espace de travail libre OMS peut uniquement envoyer les 500 Mo de données tous les jours au service OMS. Si vous utilisez un abonnement Azure, vous pouvez également utiliser cet abonnement pour accéder aux autres services Azure. Quelle que soit la méthode que vous utilisez pour créer l’espace de travail, vous allez créer l’espace de travail avec un compte Microsoft ou un compte d’organisation.

Voici un aperçu du processus :

![diagramme d’intégration](./media/log-analytics-get-started/oms-onboard-diagram.png)

## <a name="log-analytics-prerequisites-and-deployment-considerations"></a>Conditions préalables de journal Analytique et les considérations relatives au déploiement

- Vous avez besoin d’un abonnement Microsoft Azure d’utiliser pleinement le journal Analytique. Si vous n’avez pas un abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) qui vous permet d’accéder à n’importe quel service Azure. Ou bien, vous pouvez créer un compte OMS gratuit sur le site Web de [Microsoft Operations Management Suite](http://microsoft.com/oms) et cliquez sur **essayer gratuitement**.
- Un espace de travail de l’OMS
- Chaque ordinateur Windows que vous souhaitez collecter des données à partir de doivent exécuter Windows Server 2008 SP1, ou version ultérieure
- Accès de [pare-feu](log-analytics-proxy-firewall.md) à l’OMS web les adresses du service
- Un serveur [OMS journal Analytique redirecteur](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) (passerelle) pour transférer le trafic à partir de serveurs d’OMS, si l’accès à Internet n’est pas disponible à partir d’ordinateurs
- Si vous utilisez le Gestionnaire des opérations, prend en charge un journal Analytique Operations Manager 2012 SP1 UR6 et au-dessus et Operations Manager 2012 R2 UR2 et les versions ultérieures. Prise en charge du proxy a été ajouté dans l’exclusion de UR7 Operations Manager 2012 SP1 et Operations Manager 2012 R2 UR3. Déterminer comment il sera intégrée avec l’OMS.
- Déterminer si vos ordinateurs disposent d’un accès Internet direct. Dans le cas contraire, ils nécessitent un serveur de passerelle pour accéder aux sites de services web OMS. Tous les accès se fait via HTTPS.
- Déterminer les technologies et les serveurs envoient des données au OMS. Par exemple, les contrôleurs de domaine, de SQL Server, etc.
- Accorder l’autorisation aux utilisateurs OMS et Azure.
- Si vous êtes préoccupé par l’utilisation des données, déployer chaque solution individuellement et tester l’impact sur les performances avant d’ajouter des solutions supplémentaires.
- Passez en revue l’utilisation des données et les performances lorsque vous ajoutez des fonctionnalités et solutions d’Analytique de journal. Cela inclut la collecte d’événements, journal collection, collecte des données de performance, etc.. Il est mieux d’abord minimale collection jusqu'à ce que l’utilisation des données ou impact sur les performances a été identifié.
- Vérifiez que les agents Windows ne sont pas également gérés à l’aide de Operations Manager, sinon les données en double seront produit. Cela s’applique également à Azure-base-agents qui ont des Diagnostics Azure activé.
- Après l’installation d’agents, vérifiez que l’agent fonctionne correctement. Si non, une vérification pour s’assurer que les API de cryptographie : Isolation de clé CNG (Next Generation) n’est pas désactivée à l’aide de la stratégie de groupe.
- Certaines solutions d’Analytique de journal possèdent des exigences supplémentaires



## <a name="sign-up-in-3-steps-using-the-operations-management-suite"></a>Inscrivez-vous en 3 étapes à l’aide de la Suite de gestion des opérations

1. Accédez au site Web de [Microsoft Operations Management Suite](http://microsoft.com/oms) et cliquez sur **essayer gratuitement**. Connectez-vous avec votre compte Microsoft, tels que Outlook.com, ou avec un compte d’organisation fourni par votre société ou un établissement d’enseignement à utiliser avec Office 365 ou d’autres services de Microsoft.
2. Fournissez un nom d’espace de travail unique. Un espace de travail est un conteneur logique où sont stockées vos données de gestion. Il vous fournit un moyen de partitionner les données entre les différentes équipes de votre organisation, comme les données sont exclusives à son espace de travail. Spécifiez une adresse de messagerie et de la région où vous souhaitez que vos données soient stockées.  
    ![créer l’espace de travail et de lier l’abonnement](./media/log-analytics-get-started/oms-onboard-create-workspace-link01.png)
3. Ensuite, vous pouvez créer un nouvel abonnement Azure ou un lien à un abonnement Azure. Si vous souhaitez continuer à l’aide de la version d’évaluation gratuite, cliquez sur **Non**.  
  ![créer l’espace de travail et de lier l’abonnement](./media/log-analytics-get-started/oms-onboard-create-workspace-link02.png)

Vous êtes prêt à commencer à utiliser le portail de la Suite de gestion des opérations.

Vous pouvez en savoir plus sur Configurer votre espace de travail et la liaison Azure existante de comptes à des espaces de travail créés à la Suite de gestion des opérations à [gérer l’accès au journal Analytique](log-analytics-manage-access.md).

## <a name="sign-up-quickly-using-microsoft-azure"></a>S’inscrire rapidement à l’aide de Microsoft Azure

1. Accédez au [portail Azure](https://portal.azure.com) se connecter, parcourir la liste des services et puis sélectionnez **Journal Analytique (OMS)**.  
    ![Azure portal](./media/log-analytics-get-started/oms-onboard-azure-portal.png)
2. Cliquez sur **Ajouter**, puis sélectionnez les options pour les éléments suivants :
    - Nom de **l’Espace de travail de l’OMS**
    - **Abonnement** - si vous disposez de plusieurs abonnements, choisissez celle que vous souhaitez associer à l’espace de travail.
    - **Groupe de ressources**
    - **Emplacement**
    - **Niveau de tarification**  
        ![Création rapide](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Cliquez sur **créer** et vous verrez les détails de l’espace de travail dans le portail Azure.       
    ![Détails de l’espace de travail](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         
4. Cliquez sur le lien du **Portail de l’OMS** pour ouvrir le site Web de Microsoft Operations Management Suite avec votre nouvel espace de travail.

Vous êtes prêt à commencer à utiliser le portail de la Suite de gestion des opérations.

Vous pouvez en savoir plus sur la configuration de votre espace de travail et la liaison des espaces de travail existants que vous avez créé à la Suite de gestion des opérations aux abonnements Azure à [gérer l’accès au journal Analytique](log-analytics-manage-access.md).

## <a name="get-started-with-the-operations-management-suite-portal"></a>Mise en route avec le portail Operations Management Suite
Pour choisir les solutions et connecter les serveurs que vous souhaitez gérer, cliquez sur la fenêtre de **paramètres** et suivez les étapes de cette section.  

![mise en route](./media/log-analytics-get-started/oms-onboard-get-started.png)  

1. **Ajouter des Solutions** - permet d’afficher vos solutions installées.  
    ![solutions](./media/log-analytics-get-started/oms-onboard-solutions.png)  
    Cliquez sur **visiter la galerie** pour ajouter plus de solutions.  
    ![solutions](./media/log-analytics-get-started/oms-onboard-solutions02.png)  
    Sélectionnez une solution, puis sur **Ajouter**.
2. **Connecter une source** - choisir comment vous souhaitez vous connecter à votre environnement de serveur pour collecter des données :
    - Se connecter de n’importe quel serveur Windows ou un client directement par l’installation d’un agent.
    - Connecter des serveurs Linux avec l’Agent de l’OMS pour Linux.
    - Utilisez un compte de stockage Azure configuré avec l’extension VM de diagnostic Windows ou Linux Azure.
    - System Center Operations Manager permet de joindre vos groupes d’administration ou de tout votre déploiement d’Operations Manager.
    - Activer la télémétrie Windows à utiliser Analytique de mise à niveau.
        ![sources connectées](./media/log-analytics-get-started/oms-onboard-data-sources.png)    

3. **Collecter des données** Configurer au moins une source de données pour remplir les données de votre espace de travail. Lorsque vous avez terminé, cliquez sur **Enregistrer**.    

    ![collecter des données](./media/log-analytics-get-started/oms-onboard-logs.png)    


## <a name="optionally-connect-servers-directly-to-the-operations-management-suite-by-installing-an-agent"></a>Vous pouvez également connecter des serveurs directement à la Suite de gestion des opérations en installant un agent

L’exemple suivant vous montre comment installer un agent de Windows.

1. Cliquez sur la fenêtre de **paramètres** , cliquez sur l’onglet **Sources connectées** , cliquez sur un onglet pour le type de source que vous souhaitez ajouter et soit télécharger un agent ou en savoir plus sur l’activation d’un agent. Par exemple, cliquez sur **Télécharger l’Agent Windows (64 bits)**. Pour les agents de Windows, vous ne pouvez installer l’agent sur Windows Server 2008 Service Pack 1 ou version ultérieure ou sous Windows 7 SP1 ou une version ultérieure.
2. Installer l’agent sur un ou plusieurs serveurs. Vous pouvez installer des agents un par un, ou à l’aide d’une méthode plus automatisée avec un [script personnalisé](log-analytics-windows-agents.md), ou vous pouvez utiliser une solution de distribution de logiciels existante que vous pourriez rencontrer.
3. Une fois que vous acceptez le contrat de licence et que vous cliquez sur votre dossier d’installation, sélectionnez **connecter l’agent pour Azure journal Analytique (OMS)**.   
    ![programme d’installation de l’agent](./media/log-analytics-get-started/oms-onboard-agent.png)

4. Sur la page suivante, vous êtes invité pour votre ID de l’espace de travail et de la clé de l’espace de travail. Votre ID de l’espace de travail et votre clé sont affichées à l’écran où vous avez téléchargé le fichier de l’agent.  
    ![clés de l’agent](./media/log-analytics-get-started/oms-onboard-mma-keys.png)  

    ![joindre des serveurs](./media/log-analytics-get-started/oms-onboard-key.png)
5. Lors de l’installation, vous pouvez cliquer sur **Avancé** pour éventuellement configurer votre serveur proxy et fournir des informations d’authentification. Cliquez sur le bouton **suivant** pour revenir à l’écran de d’informations d’espace de travail.
6. Cliquez sur **suivant** pour valider votre identificateur d’espace de travail et de la clé. Si des erreurs sont détectées, vous pouvez cliquer sur **précédent** pour apporter des corrections. Votre ID de l’espace de travail et votre clé sont validés, cliquez sur **installer** pour terminer l’installation de l’agent.
7. Dans le panneau de configuration, cliquez sur Agent de surveillance Microsoft > onglet d’Azure journal Analytique (OMS). Une icône de coche verte apparaît lorsque les agents communiquent avec le service Microsoft Operations Management Suite. Au départ, cela prend environ 5 à 10 minutes.

>[AZURE.NOTE] Les solutions d’évaluation de configuration et gestion de capacité ne sont actuellement pas pris en charge par des serveurs connectés directement à la Suite de gestion des opérations.


Vous pouvez également vous connecter à l’agent à System Center Operations Manager 2012 SP1 et versions ultérieures. Pour ce faire, sélectionnez la **connexion de l’agent de System Center Operations Manager**. Lorsque vous choisissez qu’option, vous envoyez des données au service sans matériel supplémentaire ou une chargez sur vos groupes d’administration.

Vous pouvez en savoir plus sur la connexion des agents à la Suite de gestion des opérations sur les [ordinateurs Windows de se connecter au journal Analytique](log-analytics-windows-agents.md).

## <a name="optionally-connect-servers-using-system-center-operations-manager"></a>Vous pouvez également connecter les serveurs à l’aide de System Center Operations Manager

1. Dans la console Operations Manager, sélectionnez **Administration**.
2. Développez le nœud **Des conseils opérationnels** et sélectionnez **Connexion de perspectives opérationnelles**.

  >[AZURE.NOTE] Selon quelle mise à jour de correctif cumulatif de SCOM vous utilisez, vous pouvez voir un noeud pour *System Center Advisor*, *Perspectives opérationnelles*ou *Suite de gestion des opérations*.

3. Cliquez sur le lien **d’inscription à des conseils opérationnels** vers le haut à droite, puis suivez les instructions.
4. À la fin de l’Assistant d’enregistrement, cliquez sur le lien **Ajouter un ordinateur/groupe** .
5. Dans la boîte de dialogue de **Recherche de l’ordinateur** , vous pouvez rechercher pour les ordinateurs ou les groupes contrôlés par Operations Manager. Sélectionnez les ordinateurs ou les groupes intégrés au journal Analytique, cliquez sur **Ajouter**, puis cliquez sur **OK**. Vous pouvez vérifier que le service OMS reçoit des données à partir de la mosaïque de **l’utilisation** du portail de la Suite de gestion des opérations. Données doivent apparaître dans environ 5 à 10 minutes.

Vous pouvez en savoir plus sur la connexion à la Suite de gestion des opérations à [Connecter Operations Manager pour journal Analytique](log-analytics-om-agents.md)d’Operations Manager.

## <a name="optionally-analyze-data-from-cloud-services-in-microsoft-azure"></a>Vous pouvez également analyser les données à partir des services en nuage de Microsoft Azure

À la Suite de gestion des opérations, vous pouvez rechercher rapidement événements et journaux IIS pour les services en nuage et les machines virtuelles en activant les diagnostics pour les Services en nuage Azure. Vous pouvez également recevoir des éclaircissements pour vos ordinateurs virtuels Azure par l’installation de l’Agent de surveillance de Microsoft. Vous pouvez en savoir plus sur la façon de configurer votre environnement Azure pour utiliser la Suite de gestion des opérations de stockage [Azure de se connecter au journal Analytique](log-analytics-azure-storage.md).


## <a name="next-steps"></a>Étapes suivantes

- [Solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et de collecter des données.
- Familiarisez-vous avec les [recherches de journaux](log-analytics-log-searches.md) afficher des informations détaillées collectées par les solutions.
- Utilisez les [tableaux de bord](log-analytics-dashboards.md) pour enregistrer et afficher vos propres recherches personnalisées.
