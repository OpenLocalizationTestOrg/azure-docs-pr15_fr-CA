<properties
   pageTitle="Quelles sont les nouveautés dans incorporées de BI d’alimentation"
   description="Obtenir les dernières informations sur les nouveautés de puissance BI incorporé"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="whats-new-in-power-bi-embedded"></a>Quelles sont les nouveautés dans incorporées de BI d’alimentation

**Alimentation BI** Embedded sont mises à jour sur une base régulière. Toutefois, pas chaque version inclut les nouvelles fonctionnalités de rencontrées par les utilisateurs ; certaines versions sont centrées sur les capacités du service principal. Nous étudierons les nouvelles fonctionnalités présentées à l’utilisateur ici. N’oubliez pas de consulter régulièrement.

## <a name="august-31st-2016"></a>31 août 2016

Inclus dans cette version :

- Tous les nouveaux SDK JavaScript qui prend en charge [le filtrage avancé et navigation entre les pages](power-bi-embedded-interact-with-reports.md).
- Incorporées de BI d’alimentation est maintenant pris en charge dans le centre de données Central du Canada. Vérifiez le [statut de centre de données](https://azure.microsoft.com/status/).

## <a name="july-11th-2016"></a>11 juillet 2016

Inclus dans cette version :

-    **Bonne nouvelle !** Le service incorporées de BI d’alimentation n’est plus en mode Aperçu, c’est maintenant GA (généralement disponible).  
-    Toutes les autres API ont déplacé à partir de **/beta** vers **/v1.0**.
-    .NET et le SDK de JavaScript a été mis à jour pour la **version 1.0**.
-    Appels de l’API de BI d’alimentation peuvent désormais être authentifiées à l’aide de clés de l’API. Jetons d’application ne sont nécessaires que pour l’incorporation. Dans le cadre de ce, fourniture et développement de jetons ont été désapprouvées dans v1.0 API, mais elles vous continuent à fonctionner dans la version bêta jusqu’au 30/12/2016. Pour plus d’informations, reportez-vous à la section [authentification et autorisation avec alimentation BI incorporé](power-bi-embedded-app-token-flow.md).
-    Prise en charge de ligne au niveau de sécurité (RLS) pour les jetons de l’application et de rapports incorporés. Pour plus d’informations, consultez [sécurité de niveau ligne avec alimentation BI incorporé](power-bi-embedded-rls.md).
-    Mise à jour d’exemple d’application pour tous les appels **v1.0** API.
-    Prise en charge de la BI incorporé alimentation pour Azure SDK, PowerShell et CLI.
-    Les utilisateurs peuvent exporter des données de visualisation à un **.csv**.
-    Alimentation BI incorporé est maintenant pris en charge dans tous les mêmes langues/paramètres régionaux comme Microsoft Azure. Pour plus d’informations, reportez-vous à la section [Azure - langues](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).
