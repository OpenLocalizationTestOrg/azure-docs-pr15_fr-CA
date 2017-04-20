<properties 
    pageTitle="Connue des réseaux | Microsoft Azure" 
    description="En configurant des réseaux connus, vous évitez les adresses IP appartenant à votre organisation incluse dans les modules d’authentification de plusieurs régions et ins signe provenant d’adresses IP avec des rapports d’activité suspecte." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markvi"/>

# <a name="known-networks"></a>Réseaux connus


Visibilité sur l’intégrité et la sécurité de l’annuaire de votre organisation, vous pouvez utiliser l’accès Azure d’Active Directory et les rapports d’utilisation. Avec ces informations, un administrateur de l’annuaire peut mieux déterminer où peut se trouver des risques de sécurité afin qu’ils puissent correctement à atténuer ces risques.

Il est possible que les rapports «*modules de connexion à partir de plusieurs zones géographiques*» et «*ins signe provenant d’adresses IP avec une activité suspecte*» signaler de manière incorrecte les adresses IP qui sont en réalité détenus par votre organisation. 

Cela peut se produire, par exemple, lorsque : 

- Un utilisateur dans votre office a connecté à distance à votre centre de données à San Francisco de Boston déclenche le rapport « Signer les modules à partir de plusieurs zones géographiques » 

- Un utilisateur de votre organisation tente de session plusieurs fois avec une déclencheurs de mot de passe incorrect le rapport « Signer ins provenant d’adresses IP avec une activité suspecte » 

Pour empêcher ces cas de génération de rapports de sécurité trompeur, vous devez ajouter des plages d’adresses IP connues à la liste d’adresse IP publique de votre société.    


###<a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Pour ajouter des plages d’adresses IP publiques de votre organisation, effectuez les opérations suivantes : 

1.  Ouverture de session sur le [portail de gestion Azure](https://manage.windowsazure.com).

2.  Dans le volet gauche, cliquez sur **Active Directory**. <br><br>![Fonctionnement de la découverte des applications de Cloud](./media/active-directory-known-networks/known-netwoks-01.png)

3.  Dans l’onglet **répertoire** , sélectionnez le répertoire.

4.  Dans le menu du haut, cliquez sur **configurer**. <br><br>![Fonctionnement de la découverte des applications de Cloud](./media/active-directory-known-networks/known-netwoks-02.png)

5.  Sur l’onglet Configurer, accédez à **vos plages d’adresses IP publiques aux entreprises** <br><br>![Fonctionnement de la découverte des applications de Cloud](./media/active-directory-known-networks/known-netwoks-03.png)

6.  Cliquez sur **Ajouter des plages d’adresses IP connue**.

7.  Ajouter des plages d’adresses dans la boîte de dialogue qui s’affiche, puis cliquez sur le bouton de vérification lorsque vous avez terminé. <br><br>![Fonctionnement de la découverte des applications de Cloud](./media/active-directory-known-networks/known-netwoks-04.png)


**Ressources supplémentaires**


* [Afficher vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)
* [Signer des modules provenant d’adresses IP avec une activité suspecte](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Modules de connexion à partir de plusieurs zones géographiques](active-directory-reporting-sign-ins-from-multiple-geographies.md)


