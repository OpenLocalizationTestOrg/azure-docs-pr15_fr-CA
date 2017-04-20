<properties
    pageTitle="Rapports d’Azure, plusieurs facteurs d’authentification"
    description="Il décrit comment utiliser la fonctionnalité d’authentification à plusieurs facteurs Azure - rapports."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="reports-in-azure-multi-factor-authentication"></a>Rapports dans Azure plusieurs facteurs d’authentification

Authentification à plusieurs facteurs Azure fournit plusieurs rapports qui peuvent être utilisés par vous et votre organisation. Ces rapports sont accessibles via le portail de gestion de l’authentification multifacteur, qui requiert que vous disposiez d’une licence de fournisseur d’AMF Azure, un Azure AMF, Azure AD Premium ou Suite de mobilité d’entreprise. Voici une liste des rapports disponibles.

Vous pouvez accéder aux rapports via le portail de gestion d’Azure.

Nom| Description
:------------- | :------------- |
Utilisation de | L’utilisation de le rapports affichent des informations sur l’utilisation globale, les utilisateurs et les détails de l’utilisateur.
État du serveur|Ce rapport affiche l’état des serveurs d’authentification selon plusieurs facteurs associés à votre compte.
Historique de l’utilisateur bloqué|Ces rapports présentent l’historique des demandes de bloquer ou de débloquer des utilisateurs.
Historique d’utilisateur ignorée|Affiche l’historique des demandes de contourner l’authentification à plusieurs facteurs pour le numéro de téléphone d’un utilisateur.
Alerte à la fraude|Affiche un historique des alertes de fraude soumis au cours de la plage de dates que vous avez spécifié.
La file d’attente|Listes de rapports en file d’attente pour le traitement et leur statut. Un lien pour télécharger ou afficher le rapport est fourni lorsque le rapport est terminé.

## <a name="to-view-reports"></a>Pour afficher les rapports

1.  Ouvrez une session sur http://azure.microsoft.com
2.  Sur la gauche, sélectionnez Active Directory.
3.  Sélectionnez une des options suivantes :
    - **Option 1**: cliquez sur l’onglet fournisseurs d’authentification selon plusieurs facteurs. Sélectionnez votre fournisseur de l’AMF et cliquez sur le bouton gérer en bas.
    - **Option 2**: sélectionnez le répertoire et cliquez sur l’onglet Configurer. Dans la section authentification à facteurs multiples, sélectionnez Gérer les paramètres de service. En bas de la page Paramètres du Service AMF, cliquez sur le le lien du portail.
4.  Dans le portail de gestion Azure plusieurs facteurs d’authentification, vous verrez la vue une section d’état dans la navigation de gauche. À partir de là, vous pouvez sélectionner les rapports décrits ci-dessus.

<center>![Nuage](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Ressources supplémentaires**

* [Pour les utilisateurs](./end-user/multi-factor-authentication-end-user.md)
* [Authentification à plusieurs facteurs Azure sur MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
