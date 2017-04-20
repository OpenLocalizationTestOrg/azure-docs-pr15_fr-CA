<properties
    pageTitle="Obtenir démarré Azure multi-facteurs Auth fournisseur | Microsoft Azure"
    description="Découvrez comment créer un fournisseur d’authentification selon plusieurs facteurs Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>



# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Mise en route avec un fournisseur d’authentification selon plusieurs facteurs Azure
Vérification d’en deux étapes est disponible par défaut pour les administrateurs globaux qui ont Azure Active Directory et les utilisateurs d’Office 365. Toutefois, si vous souhaitez tirer parti des [fonctionnalités avancées](multi-factor-authentication-whats-next.md) vous devez acheter la version complète d’Azure plusieurs facteurs d’authentification (AMF).

> [AZURE.NOTE]  Un fournisseur d’authentification selon plusieurs facteurs Azure permet de tirer parti des fonctionnalités offertes par la version complète de l’AMF d’Azure. Il est destiné aux utilisateurs qui **n’ont pas de licences via AMF d’Azure, prime d’AD Azure ou EMS**.  Azure AMF Azure AD Premium et EMS incluent la version complète de Azure AMF par défaut.  Si vous disposez de licences, vous n’avez pas besoin d’un fournisseur d’authentification selon plusieurs facteurs Azure.

Un fournisseur d’authentification selon plusieurs facteurs de Azure est requis pour télécharger le Kit de développement logiciel.

> [AZURE.IMPORTANT]  Pour télécharger le Kit de développement, créez un fournisseur d’authentification selon plusieurs facteurs Azure même si vous avez Azure AMF, DAS Premium ou licences de l’EMS.  Si vous créez un fournisseur d’authentification selon plusieurs facteurs Azure à cet effet et que vous avez déjà des licences, assurez-vous de créer le fournisseur avec le modèle **Par utilisateur** . Ensuite, liez le fournisseur dans le répertoire qui contient les licences Azure AMF, Azure AD Premium ou EMS.  Cela garantit que vous sont facturés uniquement si vous avez plusieurs utilisateurs uniques en utilisant le Kit de développement logiciel que le nombre de licences que vous possédez.


## <a name="to-create-a-multi-factor-auth-provider"></a>Pour créer un fournisseur d’authentification selon plusieurs facteurs

Utilisez les étapes suivantes pour créer un fournisseur d’authentification selon plusieurs facteurs Azure.

1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com) en tant qu’administrateur.
2. Sur la gauche, sélectionnez **Active Directory**.
3. Sur la page Active Directory, en haut, sélectionnez **Les fournisseurs d’authentification selon plusieurs facteurs**.
![Création d’un fournisseur de l’AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. En bas, cliquez sur **Nouveau**.
![Création d’un fournisseur de l’AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Sous Services de l’application, sélectionnez le **Fournisseur d’authentification selon plusieurs facteurs**
![création d’un fournisseur de l’AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Sélectionnez **Création rapide**.
![Création d’un fournisseur de l’AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Renseignez les champs ci-dessous et sélectionnez **créer**.
    1. **Nom** – nom du fournisseur authentification selon plusieurs facteurs.
    2. **Modèle d’utilisation** – si vous souhaitez activer des utilisateurs individuels ou par vérification de paie. Choisissez l’une des deux options :
        - Par authentification – modèle d’achat facturé par l’authentification. Généralement utilisé pour les scénarios qui utilisent l’authentification à plusieurs facteurs Azure dans une application pour les consommateurs.
        - Par utilisateur d’activé – modèle d’achat facturé par utilisateur activée. Généralement utilisé pour l’accès des employés aux applications telles que Office 365. Choisissez cette option si vous avez des utilisateurs qui sont déjà sous licence pour Azure AMF.
    2. **Répertoire** clients Azure Active Directory que le fournisseur d’authentification selon plusieurs facteurs est associé. Soyez conscient des éléments suivants :
        - Vous n’avez pas besoin d’un annuaire AD Azure pour créer un fournisseur d’authentification selon plusieurs facteurs. Laissez la zone vide si vous prévoyez de n’utiliser le serveur de l’authentification multifactorielle Azure ou SDK.
        - Le fournisseur d’authentification selon plusieurs facteurs doit être associé à un annuaire AD Azure pour tirer parti des fonctionnalités avancées.
        - Azure AD Connect, synchronisation de DAS ou synchronisation d’annuaire sont uniquement une exigence si vous synchronisez votre environnement d’Active Directory sur site avec un annuaire AD d’Azure.  Si vous utilisez uniquement un annuaire AD Azure qui n’est pas synchronisé, il n’est pas nécessaire.
![Création d’un fournisseur de l’AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. Une fois que vous cliquez sur Créer, le fournisseur d’authentification selon plusieurs facteurs est créé et vous devez voir un message indiquant : **fournisseur d’authentification selon plusieurs facteurs créé avec succès**. Cliquez sur **Ok**.
![Création d’un fournisseur de l’AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)
