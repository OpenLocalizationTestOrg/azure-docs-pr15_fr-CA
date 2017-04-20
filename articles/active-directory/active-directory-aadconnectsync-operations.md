<properties
   pageTitle="Azure Connect de AD sync : tâches opérationnelles et considérations | Microsoft Azure"
   description="Cette rubrique décrit les tâches opérationnelles pour la synchronisation d’Azure Connect de publicité et à préparer pour l’exploitation de ce composant."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Azure Connect de AD sync : tâches opérationnelles et examen
L’objectif de cette rubrique est de décrire des tâches opérationnelles pour la synchronisation Azure Connect d’Active Directory.

## <a name="staging-mode"></a>Mode de mise en attente
Mode de mise en attente peut être utilisé pour plusieurs scénarios, notamment :

-   Une haute disponibilité.
-   Tester et déployer de nouvelles modifications de la configuration.
-   Introduction d’un nouveau serveur et retirer l’ancien.

Avec un serveur en mode de mise en attente, vous pouvez apporter des modifications à la configuration et visualiser les modifications avant de mettre le serveur actif. Il vous permet également d’exécuter une importation complète et synchronisation complète pour vérifier que toutes les modifications sont censées avant d’effectuer ces modifications dans votre environnement de production.

Lors de l’installation, vous pouvez sélectionner le serveur en **mode de mise en attente**. Cette action rend le serveur actif pour l’importation et la synchronisation, mais il ne s’exécute pas des exportations. Un serveur en mode intermédiaire ne fonctionne pas la synchronisation de mot de passe ou de l’écriture différée de mot de passe, même si vous avez sélectionné ces fonctionnalités lors de l’installation. Lorsque vous désactivez le mode de mise en attente, le serveur lance l’exportation permet la synchronisation de mot de passe et permet l’écriture différée de mot de passe.

Vous pouvez encore forcer une exportation à l’aide du Gestionnaire de service de synchronisation.

Un serveur en mode de mise en attente continue à recevoir des modifications d’Active Directory et AD Azure. Il a toujours une copie des dernières modifications et peuvent durer de très rapide sur les responsabilités d’un autre serveur. Si vous apportez des modifications de la configuration de votre serveur principal, il est de votre responsabilité d’apporter les mêmes modifications au serveur en mode de mise en attente.

Pour ceux connaissant les anciennes technologies de synchronisation, le mode intermédiaire est différent dans la mesure où le serveur a sa propre base de données SQL. Cette architecture permet le serveur en mode intermédiaire doit être situé dans un autre centre de données.

### <a name="verify-the-configuration-of-a-server"></a>Vérification de la configuration d’un serveur
Pour appliquer cette méthode, procédez comme suit :

1. [Préparer](#prepare)
2. [Importer et synchroniser](#import-and-synchronize)
3. [Vérifier](#verify)
4. [Serveur actif de commutateur](#switch-active-server)

#### <a name="prepare"></a>Préparer

1. Installer Azure Connect d’Active Directory, sélectionnez le **mode de mise en attente**et désélectionnez **Démarrer la synchronisation** sur la dernière page de l’Assistant d’installation. Ce mode vous permet d’exécuter le moteur de synchronisation manuellement.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Signe off/nom d’utilisateur et dans le menu Démarrer, sélectionnez **Service de synchronisation**.

#### <a name="import-and-synchronize"></a>Importer et synchroniser

1. Sélectionnez les **connecteurs**et sélectionnez le premier connecteur avec le type de **Services de domaine Active Directory**. Cliquez sur **exécuter**, sélectionnez **importation intégrale**et **OK**. Effectuez ces étapes pour tous les connecteurs de ce type.
2. Sélectionnez le connecteur avec type **Azure Active Directory (Microsoft)**. Cliquez sur **exécuter**, sélectionnez **importation intégrale**et **OK**.
3. Assurez-vous que l’onglet connecteurs est toujours sélectionné. Pour chaque connecteur avec type de **Services de domaine Active Directory**, cliquez sur **exécuter**, sélectionnez **Synchronisation Delta**et **OK**.
4. Sélectionnez le connecteur avec type **Azure Active Directory (Microsoft)**. Cliquez sur **exécuter**, sélectionnez **Synchronisation Delta**et **OK**.

Vous avez maintenant transférées exportation à AD Azure et sur site AD (si vous utilisez le déploiement hybride d’Exchange). Les étapes suivantes vous permettent d’examiner ce qui est sur le point de modifier avant de réellement démarrer l’exportation vers les répertoires.

#### <a name="verify"></a>Vérifier

1. Démarrez une invite de commandes et accédez à`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Exécuter :`csexport "Name of Connector" %temp%\export.xml /f:x`  
Vous trouverez le nom du connecteur de Service de synchronisation. Il a un nom similaire à « contoso.com – DAS » pour Azure AD.
3. Exécuter :`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Vous avez un fichier appelé sous % temp % export.csv qui peut être examiné dans Microsoft Excel. Ce fichier contient toutes les modifications qui doivent être exportées.
5. Apportez les modifications nécessaires à la configuration ou de données et d’exécuter ces étapes à nouveau (importation et synchroniser et vérifier) jusqu'à ce que les modifications qui sont sur le point d’être exportées sont prévues.

**Présentation du fichier export.csv**

La plupart du fichier est explicite. Certaines abréviations pour comprendre le contenu :

- OMODT : le Type de Modification d’objet. Indique si l’opération au niveau de l’objet est un ajout, mise à jour ou supprimer.
- AMODT – Type de Modification d’attribut. Indique si l’opération au niveau de l’attribut est un ajout, mise à jour ou supprimer.

Si la valeur de l’attribut est à valeurs multiples, pas chaque modification est affichée. Uniquement le nombre de valeurs ajoutées et supprimées n’est visible.

#### <a name="switch-active-server"></a>Serveur actif de commutateur

1. Sur le serveur actif, désactivez le serveur (DirSync/FIM/Azure AD Sync) afin qu’il n’exporte pas pour Azure AD ou définir la zone de transit en mode (connexion à Azure AD).
2. Exécutez l’Assistant installation du serveur en **mode de mise en attente** et désactiver le **mode de mise en attente**.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Reprise après sinistre
Partie de la conception de la mise en œuvre consiste à planifier ce qu’il faut faire en cas de sinistre dans lequel vous perdez le serveur de synchronisation. Il existe différents modèles et celle à utiliser dépend de plusieurs facteurs, notamment :

-   Quelle est votre tolérance n’est pas en mesure d’apporter des modifications aux objets dans Azure annonce pendant le temps d’indisponibilité ?
-   Si vous utilisez la synchronisation de mot de passe, les utilisateurs acceptent qu’ils doivent utiliser l’ancien mot de passe dans Active Directory Azure au cas où ils le modifier en local ?
-   Vous avez une dépendance sur les opérations en temps réel, telles que l’écriture différée de mot de passe ?

Selon les réponses à ces questions et la stratégie de votre organisation, une des stratégies suivantes peut être implémentée :

-   Reconstruire lorsque cela est nécessaire.
-   Disposer d’un serveur de secours rechange, appelé **mode de la zone de transit**.
-   Utiliser des ordinateurs virtuels.

Si vous n’utilisez pas la base de données SQL Express intégré, puis vous devez également vérifier la section [Haute disponibilité de SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Reconstruire si nécessaire
Une stratégie viable consiste à planifier une reconstruction du serveur lorsque cela est nécessaire. En général, installer le moteur de synchronisation, puis effectuez que l’importation initiale et la synchronisation peuvent être effectuées dans les quelques heures. Si il n’est pas un serveur de rechange disponibles, il est possible d’utiliser temporairement d’un contrôleur de domaine pour héberger le moteur de synchronisation.

Le serveur de moteur de synchronisation ne stocke pas tout état sur les objets pour la base de données peut être reconstruite à partir des données dans Active Directory et AD Azure. L’attribut **sourceAnchor** est utilisé pour joindre des objets à partir de locaux et le nuage. Si vous régénérez le serveur existant objets local et le nuage, puis le moteur de synchronisation correspond à ces objets ensemble à nouveau sur la réinstallation. Les choses à documenter et à enregistrer sont les modifications de configuration apportées au serveur, telles que les règles de filtrage et de synchronisation. Ces configurations personnalisées doivent être réappliquées avant de démarrer la synchronisation.

### <a name="have-a-spare-standby-server---staging-mode"></a>Disposer d’un serveur de secours rechange - mode de mise en attente
Si vous disposez d’un environnement plus complexe, il est recommandé d’un ou plusieurs serveurs de secours. Lors de l’installation, vous pouvez activer un serveur en **mode de mise en attente**.

Pour plus d’informations, voir [mode de test](#staging-mode).

### <a name="use-virtual-machines"></a>Utiliser des machines virtuelles
Une méthode courante et prises en charge est d’exécuter le moteur de synchronisation sur une machine virtuelle. Dans le cas où l’hôte a un problème, l’image avec le serveur de moteur de synchronisation peut être migrée vers un autre serveur.

### <a name="sql-high-availability"></a>Haute disponibilité SQL
Si vous n’utilisez pas le SQL Server Express, qui est fourni avec Azure Connect de publicité, haute disponibilité pour SQL Server doit également être considérée. La solution de disponibilité élevée uniquement pris en charge est mise en cluster de SQL. Solutions non pris en charge incluent la mise en miroir et toujours sur.

## <a name="next-steps"></a>Étapes suivantes

**Rubriques de vue d’ensemble**  

- [Azure Connect de AD sync : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)  
- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)  
