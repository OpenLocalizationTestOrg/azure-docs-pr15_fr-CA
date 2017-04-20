<properties
    pageTitle="Algorithme de hachage de signature de modification pour l’approbation des parties de la réponse Office 365 | Microsoft Azure"
    description="Cette page fournit des instructions permettant de modifier l’algorithme SHA pour l’approbation de fédération avec Office 365"
    keywords="SHA1, SHA256, O365, fédération, aadconnect, adfs, Federation Services, sha de modification, l’approbation de fédération, approbation de tiers de confiance"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="samueld"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="change-signature-hash-algorithm-for-office-365-replying-party-trust"></a>Modifier l’algorithme de hachage de signature pour Office 365 réponse d’approbation de la partie

## <a name="overview"></a>Vue d’ensemble

Azure Active Directory Federation Services (ADFS) signe ses jetons Azure Microsoft Active Directory pour vous assurer qu’ils ne peuvent pas être falsifiés. Cette signature peut être basée sur SHA1 ou SHA256. Azure Active Directory prend désormais en charge les jetons signés à l’aide d’un algorithme SHA256, et nous vous recommandons de définir l’algorithme de signature de jetons à SHA256 pour le niveau de sécurité le plus élevé. Cet article décrit les étapes nécessaires pour définir l’algorithme de signature de jetons à la plus sécurisée SHA256 niveau.

## <a name="change-the-token-signing-algorithm"></a>Modifier l’algorithme de signature de jetons

Après avoir défini l’algorithme de signature avec l’un des deux processus ci-dessous, ADFS signe les jetons pour Office 365 compter d’approbation tiers avec SHA256. Vous n’avez pas besoin d’apporter des modifications de configuration supplémentaires, et cette modification n’a aucun impact sur votre capacité à accéder à Office 365 ou autres applications Azure AD.

### <a name="ad-fs-management-console"></a>Console de gestion AD FS

1. Ouvrez la console de gestion AD FS sur le serveur ADFS principal.
2. Développez le nœud AD FS et cliquez sur **Approbations des parties de confiance**.
3. Avec le bouton droit de votre confiance de partie utilisatrice Office 365/Azure et sélectionnez **Propriétés**.
4. Sélectionnez l’onglet **Avancé** , puis sélectionnez l’algorithme de hachage sécurisé SHA256.
5. Cliquez sur **OK**.

![Algorithme de signature SHA256--MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Applets de commande PowerShell de FS AD

1. Sur n’importe quel serveur ADFS, ouvrez PowerShell sous des privilèges d’administrateur.
2. Définir l’algorithme de hachage sécurisé à l’aide de l’applet de commande **Set-AdfsRelyingPartyTrust** .

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Lire également

* [Réparer la confiance d’Office 365 avec Azure Connect d’AD](./active-directory-aadconnect-federation-management.md#repairing-the-trust)
