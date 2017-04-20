<properties
    pageTitle="Attribution de licences pour Azure AMF | Microsoft Azure"
    description="Découvrez comment attribuer des licences utilisateur pour une authentification à facteurs multiples Microsoft Azure."
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
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Attribution d’une licence Azure AMF, Azure AD Premium ou de mobilité de l’entreprise pour les utilisateurs

Si vous avez acheté Azure AMF, Azure AD Premium ou licences de la Suite de mobilité d’entreprise, vous n’avez pas besoin de créer un fournisseur d’authentification de plusieurs facteurs. Une fois que vous assignez les licences à vos utilisateurs, vous pouvez commencer à les activer pour AMF.

## <a name="to-assign-a-license"></a>Pour attribuer une licence

1. Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com) en tant qu’administrateur.
2. Sur la gauche, sélectionnez **Active Directory**.
3. Sur la page Active Directory, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez activer.
4. En haut de la page de l’annuaire, sélectionner des **licences**.
![Attribution de licences](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. Sur la page licences, sélectionnez **Authentification à plusieurs facteurs Azure**, **Active Directory prime**ou **Suite de mobilité d’entreprise**.  Si vous en avez uniquement, puis il doit être sélectionné automatiquement.
6. En bas de la page, cliquez sur **attribuer**.
![Attribution de licences](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
6. Dans la zone qui s’affiche, cliquez sur Suivant pour les utilisateurs ou les groupes auxquels à que vous souhaitez attribuer des licences.  Une coche verte doit s’afficher.
7. Cliquez sur l’icône de coche pour enregistrer les modifications.
![Attribution de licences](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
8. Vous devez voir un message indiquant le nombre de licences ont été affecté et combien a peut-être échoué.  Cliquez sur **Ok**.
![Attribution de licences](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)
