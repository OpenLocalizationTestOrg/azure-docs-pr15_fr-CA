<properties
 pageTitle="Soumettre des tâches à un Pack de HPC cluster dans Azure | Microsoft Azure"
 description="Apprenez à configurer un ordinateur local à soumettre des tâches à un cluster HPC Pack dans Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Soumettre des travaux HPC à partir d’un ordinateur local à un cluster HPC Pack déployé dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configurer un ordinateur de client sur site pour soumettre les tâches à un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) dans Azure. Cet article vous explique comment configurer un ordinateur local avec les outils clients pour envoyer un travail via HTTPS sur le cluster dans Azure. De cette façon, plusieurs utilisateurs du cluster peuvent soumettre les tâches à un cluster HPC Pack en nuage, mais sans se connecter directement à la machine virtuelle de nœud principal ou d’accéder à un abonnement Azure.

![Soumettre une tâche à un cluster dans Azure][jobsubmit]

## <a name="prerequisites"></a>Conditions préalables

* Le **nœud de tête HPC Pack est déployé dans un ordinateur virtuel d’Azure** - nous vous conseillons d’utiliser des outils automatisés comme un [modèle d’Azure quickstart](https://azure.microsoft.com/documentation/templates/) ou à un [script PowerShell d’Azure](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour déployer le nœud de tête et le cluster. Vous devez le nom DNS du nœud principal et les informations d’identification d’un administrateur de cluster pour effectuer les étapes décrites dans cet article.

* **Ordinateur client** , vous avez besoin d’un ordinateur client Windows ou de Windows Server peut exécuter des utilitaires de client de HPC Pack (voir la [configuration système requise](https://technet.microsoft.com/library/dn535781.aspx)). Si vous souhaitez uniquement utiliser le portail web de HPC Pack ou l’API REST à soumettre des tâches, vous pouvez utiliser n’importe quel ordinateur client de votre choix.

* **Support d’installation HPC Pack** - installer les utilitaires client HPC Pack, le package d’installation gratuite de la dernière version du Pack de l’informatique hautes performances (HPC Pack 2012 R2) est disponible à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Assurez-vous que vous téléchargez de la même version de HPC Pack qui est installé sur la machine virtuelle de nœud principal.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Étape 1 : Installer et configurer les composants web sur le nœud principal

Pour activer une interface reste à soumettre des tâches au cluster via HTTPS, assurez-vous que les composants web de HPC Pack sont configurés sur le nœud principal HPC Pack. S’ils ne sont pas déjà installés, vous devez tout d’abord installer les composants web en exécutant le fichier d’installation HpcWebComponents.msi. Ensuite, configurez les composants en exécutant le script HPC PowerShell **Set-HPCWebComponents.ps1**.

Pour obtenir des procédures détaillées, reportez-vous à la section [installer les composants Web de Microsoft HPC Pack](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Certains modèles quickstart Azure pour HPC Pack Installer et de configurer automatiquement les composants web. Si vous utilisez le [script de déploiement des HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour créer le cluster, vous pouvez éventuellement installer et configurer les composants web dans le cadre du déploiement.

**Pour installer les composants web**

1. Se connecter à la machine virtuelle de nœud principal en utilisant les informations d’identification d’un administrateur de cluster.

2. À partir du dossier d’installation du Pack HPC, exécutez HpcWebComponents.msi sur le nœud principal.

3. Suivez les étapes de l’Assistant pour installer les composants web

**Pour configurer les composants web**

1. Sur le nœud principal, démarrez HPC PowerShell en tant qu’administrateur.

2. Pour changer de répertoire à l’emplacement du script de configuration, tapez la commande suivante :

    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Pour configurer l’interface REST et démarrer le Service Web de HPC, tapez la commande suivante :

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Lorsque vous êtes invité à sélectionner un certificat, cliquez sur le certificat correspond au nom DNS publique du nœud principal. Par exemple, si vous déployez le nœud de tête en utilisant le modèle de déploiement classique de machine virtuelle, le nom du certificat se présente comme CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Si vous utilisez le modèle de déploiement du Gestionnaire de ressources, le nom du certificat ressemble à CN =&lt;*HeadNodeDnsName*&gt;. &lt; *zone*&gt;. cloudapp.azure.com.

    >[AZURE.NOTE] Vous sélectionnez ce certificat ultérieurement lorsque vous soumettez des travaux au nœud principal à partir d’un ordinateur local. Ne pas sélectionner ou configurer un certificat qui correspond au nom de l’ordinateur du nœud principal dans le domaine Active Directory (par exemple, CN =*MyHPCHeadNode.HpcAzure.local*).

5. Pour configurer le portail web de soumission de la tâche, tapez la commande suivante :

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Une fois le script terminé, arrêtez et redémarrez le Service Planificateur de tâches de HPC en tapant les commandes suivantes :

    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Étape 2 : Installez les utilitaires client de HPC Pack sur un ordinateur local

Si vous souhaitez installer les utilitaires client de HPC Pack sur votre ordinateur, téléchargez les fichiers d’installation de HPC Pack (installation complète) à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Lorsque vous commencez l’installation, choisissez l’option d’installation des **utilitaires client de HPC Pack**.

Pour utiliser les outils clients de HPC Pack à soumettre des tâches au nœud principal de machine virtuelle, vous devez également exporter un certificat à partir du nœud de tête et l’installer sur l’ordinateur client. Le certificat doit être dans. Format de la région d’exécution limitée.

**Pour exporter le certificat du nœud de tête**

1. Sur le nœud principal, ajoutez le composant logiciel enfichable Certificats à une Console de gestion Microsoft pour le compte d’ordinateur Local. Pour ajouter le composant logiciel enfichable, consultez [Ajouter le composant logiciel enfichable Certificats à une console MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. Dans l’arborescence de la console, développez **certificats-ordinateur Local** > **personnels**, puis cliquez sur **certificats**.

3. Localisez le certificat que vous avez configuré pour les composants web de HPC Pack dans [étape 1 : installer et configurer les composants web sur le nœud de tête](#step-1:-install-and-configure-the-web-components-on-the-head-node) (par exemple, CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net).

4. Cliquez sur le certificat, puis cliquez sur **Toutes les tâches** > **Exporter**.

5. Dans l’Assistant Exportation de certificat, cliquez sur **suivant**et vérifiez que l’option **non, ne pas exporter la clé privée** est sélectionnée.

6. Suivez les étapes restantes de l’Assistant pour exporter le certificat dans le binaire codé DER X.509 (. Format de la région d’exécution limitée).


**Pour importer le certificat sur l’ordinateur client**


1. Copiez le certificat que vous avez exporté à partir du nœud de tête dans un dossier sur l’ordinateur client.

2. Sur l’ordinateur client, exécutez certmgr.msc.

3. Dans le Gestionnaire de certificats, développez **certificats – utilisateur actuel** > **d’Autorités de Certification racines de confiance**, cliquez sur **certificats**, puis cliquez sur **Toutes les tâches** > **importation**.

4. Dans l’Assistant Importation de certificat, cliquez sur **suivant** et suivez les étapes pour importer le certificat que vous avez exporté à partir du nœud de tête dans le magasin d’autorités de Certification racine de confiance.



>[AZURE.TIP] Vous verrez peut-être un avertissement de sécurité, car l’autorité de certification sur le nœud principal n’est pas reconnue par l’ordinateur client. À des fins de test, vous pouvez ignorer cet avertissement et terminer l’importation du certificat.

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Étape 3 : Exécution des tâches d’essai sur le cluster

Pour vérifier votre configuration, essayez d’exécuter des tâches sur le cluster dans Azure à partir de l’ordinateur local. Par exemple, vous pouvez utiliser les commandes de ligne de commande ou les outils de GUI du Pack HPC à soumettre des tâches au cluster. Vous pouvez également utiliser un portail web à soumettre des tâches.


**Pour exécuter les commandes d’envoi de travail sur l’ordinateur client**


1. Sur un ordinateur client où sont installés les utilitaires client de HPC Pack, démarrez une invite de commande.

2. Tapez un exemple de commande. Par exemple, pour répertorier tous les travaux sur le cluster, tapez une commande semblable à l’une des valeurs suivantes, selon le nom DNS complet du nœud principal :

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
    
    ou
    
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Utiliser le nom DNS complet du nœud principal, pas l’adresse IP, l’URL du planificateur. Si vous spécifiez l’adresse IP, une erreur s’affiche semblable à « le certificat de serveur doit avoir une chaîne valide de confiance ou à placer dans le magasin racine de confiance. »

3. Lorsque vous y êtes invité, tapez le nom d’utilisateur (sous la forme &lt;nom_domaine&gt;\\&lt;nom d’utilisateur&gt;) et le mot de passe de l’administrateur de cluster HPC ou d’un autre utilisateur de cluster que vous avez configurés. Vous pouvez choisir de stocker les informations d’identification localement pour plusieurs opérations de la tâche.

    La liste des tâches s’affiche.


**Utilisation du Gestionnaire de travail HPC sur l’ordinateur client**

1. Si vous n’avez pas précédemment stocker informations d’identification de domaine pour un utilisateur de cluster lors de l’envoi d’un travail, vous pouvez ajouter les informations d’identification dans le Gestionnaire d’informations d’identification.

    une barre d’outils. Dans le panneau de configuration sur l’ordinateur client, démarrez le Gestionnaire des informations d’identification.

    b. Cliquez sur les **Informations d’identification Windows** > **Ajouter une information d’identification générique**.

    c. Spécifiez l’adresse Internet (par exemple, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler ou https://&lt;HeadNodeDnsName&gt;.&lt; région&gt;.cloudapp.azure.com/HpcScheduler) et le nom d’utilisateur (&lt;nom_domaine&gt;\\&lt;nom d’utilisateur&gt;) et le mot de passe de l’administrateur de cluster ou d’un autre utilisateur de cluster que vous avez configurés.

2. Sur l’ordinateur client, démarrez le Gestionnaire de projet de HPC.

3. Dans la boîte de dialogue **Sélectionner un noeud de tête** , tapez l’URL pour le nœud de tête dans Azure (par exemple, https://&lt;HeadNodeDnsName&gt;. cloudapp.net ou https://&lt;HeadNodeDnsName&gt;.&lt; région&gt;. cloudapp.azure.com).

    HPC Job Manager s’ouvre et affiche une liste de tâches sur le nœud de tête.

**Pour utiliser le portail web s’exécutant sur le nœud de tête**

1. Démarrez un navigateur web sur l’ordinateur client et entrez l’une des adresses suivantes, selon le nom DNS complet du nœud principal :

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
    
    ou
    
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Dans la boîte de dialogue de sécurité qui s’affiche, tapez les informations d’identification de domaine de l’administrateur de cluster HPC. (Vous pouvez également ajouter d’autres utilisateurs de cluster dans des rôles différents. Reportez-vous à la section [Gestion des utilisateurs du Cluster](https://technet.microsoft.com/library/ff919335.aspx).)

    Le portail web s’ouvre sur l’affichage de la liste des tâches.

3. Pour soumettre un projet exemple qui renvoie la chaîne « Hello World » à partir du cluster, cliquez sur **nouvelle tâche** dans le cadre de gauche.

4. Sur la page de la **Nouvelle tâche** , **à partir des pages de présentation**, cliquez sur **HelloWorld**. La page de soumission de projet s’affiche.

5. Cliquez sur **Envoyer**. Si vous y êtes invité, fournissez les informations d’identification de domaine de l’administrateur de cluster HPC. Le travail est soumis, et l’ID du travail s’affiche sur la page **Mes travaux** .

6. Pour afficher les résultats de la tâche que vous avez soumis, cliquez sur l’ID de la tâche, puis cliquez sur **Afficher les tâches** pour afficher la sortie de la commande (sous **sortie**).

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez également soumettre des travaux au cluster Azure avec l' [API REST de Pack HPC](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).

* Si vous souhaitez soumettre des tâches de cluster à partir d’un client Linux, consultez l’exemple Python dans [HPC Pack 2012 R2 SDK et exemples de Code](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
