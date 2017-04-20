<properties
    pageTitle="Forum aux questions sur l’accès conditionnel Azure Active Directory | Microsoft Azure"
    description="Questions fréquemment posées à propos de l’accès conditionnel "
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-faq"></a>Forum aux questions sur l’accès conditionnel Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Les applications qui fonctionnent avec les stratégies d’accès conditionnel ?

**A:** Reportez-vous à la rubrique, [les applications access-quoi conditionnelles sont pris en charge](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Les stratégies d’accès conditionnel sont appliquées pour la collaboration de B2B et les utilisateurs invités ?

**A:** Les stratégies sont appliquées pour les utilisateurs de collaboration B2B. Toutefois, dans certains cas, un utilisateur ne peut pas être en mesure de satisfaire les exigences de la stratégie si, par exemple, une organisation ne prend pas en charge plusieurs facteurs d’authentification. 

La stratégie n’est pas actuellement appliquée pour les utilisateurs invités de SharePoint. La relation de l’invité est conservée au sein de SharePoint. Stratégies de comptes ne sont pas soumises à des accès les utilisateurs invités sur le serveur d’authentification. L’accès invité peut être géré dans SharePoint.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Une stratégie de SharePoint Online s’applique-t-elle également à OneDrive pour les entreprises ?

**A:** Oui.
 
## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Pourquoi est-il impossible de définir une stratégie sur les applications clientes, telles que Word ou Outlook ?

**A:** Une stratégie d’accès conditionnel définit les conditions requises pour l’accès à un service et s’applique lorsque l’authentification se produit à ce service. La stratégie n’est pas définie directement sur une application cliente ; au lieu de cela, elle est appliquée lors de l’appel dans un service. Par exemple, une stratégie définie dans SharePoint s’applique aux clients appelant SharePoint, et une stratégie définie sur Exchange s’applique à Outlook.


## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Une stratégie d’accès conditionnel s’applique aux comptes de service ?

**A:** Les stratégies d’accès conditionnel s’appliquent à tous les comptes d’utilisateur. Cela inclut les comptes d’utilisateur utilisés comme comptes de service. Dans de nombreux cas, un compte de service qui s’exécute en mode sans assistance n’est pas en mesure de satisfaire une stratégie. Il s’agit, par exemple le cas, lors de l’AMF est requis. Dans ces cas, les comptes de services peuvent être exclus d’une stratégie, à l’aide des paramètres de gestion de stratégie accès conditionnel. Pour en savoir plus sur l’application d’une stratégie aux utilisateurs ici.
