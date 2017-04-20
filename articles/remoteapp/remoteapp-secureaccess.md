
<properties 
    pageTitle="Sécurisation de l’accès à Azure RemoteApp et au-delà | Microsoft Azure"
    description="Découvrez comment sécuriser l’accès aux Azure RemoteApp à l’aide d’accès conditionnel dans Azure Active Directory"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>Sécurisation de l’accès à Azure RemoteApp et au-delà

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Dans cet article, nous donnera une vue d’ensemble de la façon dont un administrateur peut définir un canal d’accès sécurisé à partir de l’utilisateur final, par le biais de RemoteApp d’Azure et se terminant par une ressource sécurisée, tel qu’une base de données SQL ou une autre application back-end. L’objectif est de s’assurer que seuls les utilisateurs autorisés répondant aux conditions de votre choix peuvent accéder à des applications à distance, et que du back-end sécurisé n’est accessible à partir de l’environnement contrôlé Azure RemoteApp et non à partir d’autres emplacements.

Il existe 3 zones principales, que l’administrateur doit examiner :

![Considérations d’accès conditionnel RemoteApp Azure](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Lecture sur informations et réponses à ces questions.

## <a name="who-can-access-the-collection"></a>Qui peut accéder à la collection ?
L’administrateur choisit les utilisateurs qui peuvent accéder à des applications à distance dans la collection. Vous pouvez utiliser le travail d’Azure Active Directory (AD Azure) ou les comptes de l’établissement (précédemment appelés, « comptes d’organisation ») ou Microsoft (par exemple, @outlook.com). La plupart des scénarios d’entreprise utilisent les comptes AD Azure ; ils vous permettent d’utiliser l’accès conditionnel fonctionnalités présentées plus loin et sont également le seul choix possible pour les collections à un domaine. Le reste de cet article suppose que vous utilisez les comptes Active Directory Azure avec Azure RemoteApp.

**Ce que nous avons accompli :**

À l’aide de comptes Active Directory Azure pour contrôler l’accès à Azure RemoteApp nous donne deux choses :

1.  Nous savons toujours qui peut accéder aux applications nous ont publié et à accéder aux principaux ces applications de se connectent à.
2.  Nous contrôler la publicité Azure sous-jacente afin que nous pouvons créer et supprimer les comptes d’utilisateurs, définir des stratégies de mot de passe, utilisent une authentification multifactorielle, etc.. 

## <a name="how-is-the-collection-accessed-from-where"></a>Comment accéder à la collection ? À partir d'où ?
Généralement, les administrateurs souhaitent définir des stratégies pour l’accès à un environnement faisant face à Internet public, tels que RemoteApp d’Azure. Par exemple, ils souhaitent assurer aux utilisateurs un accès à l’environnement en dehors du réseau d’entreprise à utiliser une authentification multifacteur (AMF) à y accéder ; ou peut-être qu’ils devraient être bloqués complètement.

Azure RemoteApp les administrateurs peuvent utiliser les fonctionnalités disponibles via AD Azure pour définir des stratégies d’accès conditionnel pour leur environnement Azure RemoteApp. Elles permet également enrichi de reporting et d’alerte des fonctionnalités pour surveiller la façon dont l’environnement est effectué.

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>Comment faire pour configurer les accès conditionnel pour Azure RemoteApp
Nous allons parcourir un exemple de scénario – le RemoteApp Azure administrateur souhaite bloquer l’accès à l’environnement lorsque les utilisateurs sont en dehors du réseau d’entreprise.

>[AZURE.NOTE] Nous supposons que vous avez mis à niveau Azure AD au niveau Premium et que vous avez créé au moins une collection Azure RemoteApp.

1.  Dans Azure portal, cliquez sur l’onglet **Active Directory** . Cliquez ensuite sur le répertoire que vous souhaitez configurer.

    N’oubliez pas : Accès conditionnel est une propriété de votre répertoire et non de RemoteApp d’Azure, de sorte que toute la configuration est effectuée au niveau du répertoire. Cela signifie également que vous devez être l’administrateur d’annuaire pour effectuer ces modifications.

2.  Cliquez sur **Applications**, puis cliquez sur **Microsoft Azure RemoteApp** pour définir un accès conditionnel. Notez que vous pouvez configurer un accès conditionnel pour chaque application de « logiciel en tant que service » dans votre répertoire séparément.
![Configuration d’accès conditionnel pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.  Dans l’onglet **configurer** , valeur On à **Activer les règles d’accès** .
![Activer les règles d’accès pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.  Vous pouvez maintenant configurer des règles et sélectionnez la personne à les appliquer à :

    1. Cliquez sur **Bloquer l’accès lorsque pas au travail** pour empêcher complètement les utilisateurs d’accéder à Azure RemoteApp hors de l’environnement réseau que vous spécifiez.
    2. Cliquez sur l’option ci-dessous pour définir les plages d’adresses IP qui constituent votre « réseau approuvé ». Tout ce qui est en dehors de celles est rejeté.

5.  Testez votre configuration en lançant le client Azure RemoteApp à partir d’une adresse IP en dehors de la plage spécifiée. Une fois que vous vous connectez avec vos informations d’identification Active Directory Azure, vous devriez voir un message comme suit :

![Accès refusé pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### <a name="future-conditional-access-features"></a>Fonctionnalités de futurs accès conditionnel 
L’équipe Azure Active Directory fonctionne sur les nouvelles fonctionnalités dans l’accès conditionnel. Les administrateurs seront en mesure de créer de nouveaux types de règles au-delà de réseau les règles selon l’emplacement. Une version d’évaluation de la nouvelle fonctionnalité devrait être rapidement disponible.

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>Comment faire pour surveiller l’accès à Azure RemoteApp
Une fonctionnalité intéressante à utiliser parallèlement à accès conditionnel est la fonctionnalité de création de rapports d’Azure Active Directory prime. Vous pouvez utiliser des rapports pour surveiller qui accède à votre environnement et détecter toute activité suspecte.

Par exemple, vous pouvez voir les noms des utilisateurs ayant accédé à Azure RemoteApp, combien de fois il a fait et quand.

1.  Dans Azure portal, cliquez sur **Active Directory**, puis cliquez sur le répertoire.

2.  Accédez à l’onglet **rapports** .

3.  À partir de la liste des rapports, sélectionnez **utilisation de l’Application** dans les **applications intégrées**.

    Vous verrez certaines des statistiques agrégées pour Azure RemoteApp. 
![Statistiques agrégées d’accès Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.  Cliquez sur l’application pour afficher des informations sur les utilisateurs accédant à Azure RemoteApp.
![Statistiques d’accès utilisateur pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-userstats.png)
 
### <a name="summary"></a>Résumé
Avec Azure Active Directory Premium, vous pouvez définir des règles d’accès pour Azure RemoteApp (et d’autres logiciels comme des applications de service disponible par l’intermédiaire de publicité Azure). Les règles sont actuellement limitées aux stratégies selon l’emplacement de réseau, mais seront étendus à l’avenir d’autres aspects de la gestion de l’entreprise.

Azure AD Premium offre également le rapports et fonctionnalités d’étendre davantage le contrôle de l’administration de contrôle a sur leur environnement d’Azure RemoteApp.

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>Comment m’assurer que ma ressource sécurisée est accessible uniquement à partir de mon environnement Azure RemoteApp ?
Dans les sections précédentes de cet article, nous axée sur la sécurisation de l’accès à l’environnement Azure RemoteApp. Nous avons accompli qui en choisissant les utilisateurs autorisés à accéder et définir des règles d’accès pour contrôler plus précisément comment ils peuvent utiliser le service.

Un scénario courant pour les déploiements d’Azure RemoteApp est que les applications à distance ont besoin de communiquer avec une ressource de back-end, par exemple une base de données SQL. Cette ressource est hébergée sur site (par exemple, dans un réseau d’entreprise) ou dans le nuage (par exemple dans Azure, IaaS). Les administrateurs souhaitent souvent s’assurer que la ressource principale seulement sont accessibles par les applications déployées via Azure RemoteApp et pas par exemple par une application qui s’exécute directement sur le PC de l’utilisateur et accéder à via Internet public. RemoteApp Azure est souvent considéré comme l’environnement géré de façon centralisée et sécurisée et donc le seul chemin par le biais duquel les utilisateurs doivent interagir avec la ressource principale.

La solution consiste à placer l’environnement Azure RemoteApp et la ressource sécurisée dans l’API Azure Virtual Network (VNET) même. Si la ressource est dans un autre site, vous pouvez établir une connexion VPN de site à site, par exemple pour créer un VNet sur le centre de données Azure et de l’environnement du client local.

RemoteApp Azure prend en charge deux types de déploiements de collection dans laquelle vous pouvez fournir votre propre VNET :

-   Non liés à un domaine : les applications auront « ligne de mire » des autres ressources dans le VNET. Par exemple, il peut servir à connecter des applications à une base de données SQL qui utilise l’authentification SQL (applications permet d’authentifier l’utilisateur directement par rapport à la base de données)

-   À un domaine : les ordinateurs virtuels utilisés par Azure RemoteApp sont jointes à un contrôleur de domaine dans le VNET. Cela est utile lorsque les applications doivent s’authentifier sur un contrôleur de domaine Windows pour obtenir l’accès à une ressource de back-end.
![Une collection à un domaine dans Azure RemoteApp](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>Comment faire pour créer une connexion sécurisée entre Azure et mon environnement local
Il existe plusieurs options de configuration pour la connexion de vos environnements Azure et sur site. Une bonne vue d’ensemble des options est disponible ici.

Avec Azure RemoteApp, vous devez d’abord configurer votre VNet et l’utiliser pendant le processus de création de votre collection. 

## <a name="the-complete-solution"></a>La solution complète
Le diagramme ci-dessous présente une solution complète où nous avons un canal d’accès sécurisé de l’utilisateur final, par le biais de RemoteApp pour le Azure (ARA), dans la ressource du serveur principal.
![Sécuriser Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png) dans étape 1 nous avons sélectionné les utilisateurs et créé des règles d’accès qui déterminent le mode d’accès ARA. Dans l’exemple ci-dessous, nous uniquement autoriser l’accès pour les utilisateurs du réseau d’entreprise. Les utilisateurs non conforme ne sera pas en mesure d’accéder à tout l’environnement de ARA.
Dans « Étape 2 », nous avons exposée la ressource principale uniquement par le biais de la configuration de VNet/VPN qui nous contrôle. RemoteApp Azure a été placé dans le même VNet. Le résultat final est que la ressource seulement sont accessibles par le biais de l’environnement de ARA.


