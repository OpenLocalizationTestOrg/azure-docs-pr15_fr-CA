<properties
    pageTitle="Stratégies de périphérique d’accès conditionnel pour services Office 365 | Microsoft Azure"
    description="Pour plus d’informations sur les conditions basées sur les périphériques contrôlent l’accès aux services Office 365. Alors qu’informations aux spécialistes de services d’accès Office 365 telles que Exchange et SharePoint Online au travail ou à l’école à partir de leurs appareils personnels, leur administrateur informatique souhaite que l’accès à secure.IT administrateurs peut provisionner les stratégies des périphériques pour sécuriser des ressources d’entreprise, tout en permettant sur les périphériques compatibles pour accéder aux services d’accès conditionnel."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>
# <a name="conditional-access-device-policies-for-office-365-services"></a>Stratégies de périphérique d’accès conditionnel pour services Office 365

Le terme « accès conditionnel » a de nombreuses conditions associées comme l’utilisateur authentifié de plusieurs facteur, authentifié device, périphérique compatible etc.. Cette rubrique focusses principalement sur des périphériques des conditions pour contrôler l’accès aux services Office 365. Alors qu’informations aux spécialistes de services d’accès Office 365 telles que Exchange et SharePoint Online au travail ou à l’école à partir de leurs appareils personnels, leur administrateur informatique souhaite l’accès sécurisé. Les administrateurs informatiques peuvent provisionner des stratégies de périphérique d’accès conditionnel pour sécuriser les ressources de l’entreprise, tout en permettant sur les périphériques compatibles pour accéder aux services. Les stratégies d’accès conditionnel à Office 365 peuvent être configurés à partir du portail d’accès conditionnel Microsoft Intune.

Azure Active Directory met en œuvre les stratégies d’accès conditionnel pour sécuriser l’accès aux services Office 365. Une administration des clients peut créer une stratégie d’accès conditionnel qui empêche un utilisateur sur un périphérique non conformes d’accéder à un service O365. L’utilisateur doit se conformer aux stratégies de périphérique de la société avant que l’accès au service. Alternativement, l’administrateur peut également créer une stratégie qui impose aux utilisateurs de simplement s’inscrire leurs périphériques pour accéder à un service O365. Stratégies peuvent appliqués à tous les utilisateurs d’une organisation, ou limitées à certains groupes cibles et améliorés au fil du temps pour inclure des groupes cibles supplémentaires.

Il est indispensable d’appliquer les stratégies de périphérique aux utilisateurs d’enregistrer leurs périphériques sur le service d’enregistrement de dispositif Azure Active Directory. Vous pouvez choisir d’activer une authentification multifacteur (AMF) pour l’enregistrement des périphériques avec le service d’enregistrement de dispositif Azure Active Directory. AMF est recommandé pour le service d’enregistrement de dispositif Azure Active Directory. Lorsqu’AMF est activé, les utilisateurs de l’enregistrement de leurs périphériques avec le service d’enregistrement de dispositif Azure Active Directory exige-t-on pour la seconde authentification de facteur.

##<a name="how-does-conditional-access-policy-work"></a>Comment fonctionne la stratégie d’accès conditionnel ?

Lorsqu’un utilisateur demande à accéder au service de O365 à partir d’une plate-forme de périphérique pris en charge, Azure Active Directory authentifie l’utilisateur et un périphérique à partir duquel l’utilisateur lance la demande ; et attribution de l’accès au service uniquement lorsque l’utilisateur est conforme à la stratégie définie pour le service. Les utilisateurs qui ne disposent pas de leur périphérique inscrit figurent les instructions de réparation comment s’inscrire et devenir compatibles pour accéder aux services de O365 d’entreprise. Les utilisateurs de périphériques iOS ou Android devront s’inscrire leurs périphériques à l’aide des applications de portail d’entreprise. Lorsqu’un utilisateur s’inscrit son périphérique, le périphérique est enregistré avec Azure Active Directory et inscrit pour la conformité et de gestion des périphériques. Clients doivent utiliser le service d’enregistrement de dispositif Azure Active Directory conjointement avec Microsoft Intune pour la gestion des périphériques mobiles pour service d’Office 365. L’inscription de périphérique est une condition préalable pour les utilisateurs de services d’accès Office 365 lors de l’application des stratégies de périphérique.

Lorsqu’un utilisateur s’inscrit son périphérique correctement, le périphérique devient approuvé. Azure Active Directory fournit Single-Sign-On pour accéder aux applications de société et applique la stratégie d’accès conditionnel pour accorder l’accès à une heure de service non seulement le premier l’utilisateur demande l’accès, mais chaque fois que l’utilisateur demande de renouvellement d’accès. Accès aux services sera refusé l’utilisateur lorsque les informations de connexion sont modifiées, périphérique est perdu/volé ou la stratégie n’est pas remplie au moment de la demande de renouvellement.

## <a name="deployment-considerations"></a>Considérations relatives au déploiement :
Devez utiliser service Azure Active Directory périphérique d’enregistrement pour l’enregistrement du périphérique.

Lorsque les utilisateurs doivent être authentifiés sur locaux, Active Directory Federation Services (ADFS) (1.0 et versions ultérieures) est requis. Authentification à plusieurs facteurs pour joindre des lieux de travail échoue lorsque le fournisseur d’identité n’est pas capable d’authentification à plusieurs facteurs. Par exemple, AD FS 2.0 n’est pas plusieurs facteurs d’authentification. Administration des clients doit s’assurer que les locaux AD FS est capable d’AMF et une méthode AMF valide est activée, avant d’activer le service d’enregistrement de dispositif Azure Active Directory AMF. Par exemple, AD FS dans Windows Server 2012 R2 dispose de fonctionnalités de l’AMF. Vous devez également activer une méthode d’authentification valide supplémentaires (AMF) sur le serveur AD FS avant d’activer le service d’enregistrement de dispositif Azure Active Directory AMF. Pour plus d’informations sur les méthodes AMF prises en charge dans AD FS, voir Configurer les méthodes d’authentification supplémentaires pour AD FS.

## <a name="frequently-asked-questions-faq"></a>Forum aux Questions (FAQ)

Q : quelle est la stratégie d’exclusion par défaut pour les plates-formes de périphérique non pris en charge ?

R : à l’heure actuelle, les stratégies d’accès conditionnel sont sélectivement appliqués aux utilisateurs sur les périphériques iOS ou Android. Applications sur d’autres plates-formes de périphérique sont, par défaut, pas affecté par la stratégie d’accès conditionnel pour les périphériques iOS ou Android. Administration des clients peut-être, toutefois, choisir de substituer la stratégie globale pour interdire l’accès aux utilisateurs sur des plates-formes non prises en charge.
Il se trouve sur la feuille de route pour étendre la stratégie d’accès conditionnel aux utilisateurs sur d’autres plates-formes, y compris Windows.

Q : lorsque sera stratégie d’accès conditionnel aux services Office 365 étendue à applications basé sur le navigateur (par exemple, OWA, SharePoint basée sur un navigateur).

A : à l’heure actuelle, les accès conditionnel aux services d’Office 365 est limité à des applications riches sur le périphérique. Il se trouve sur la feuille de route pour étendre la stratégie d’accès conditionnel aux utilisateurs l’accès à des services à partir de navigateurs.
