<properties
    pageTitle="Documentation de gouvernement Azure | Microsoft Azure"
    description="Cela fournit une comparaison des fonctionnalités et des conseils sur le développement d’applications pour Azure gouvernement"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="09/29/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-compute"></a>Gouvernement Azure Compute

##  <a name="virtual-machines"></a>Ordinateurs virtuels

Pour plus d’informations sur ce service et comment l’utiliser, consultez [Les tailles de Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Variations

Les références suivantes de la machine virtuelle sont généralement disponible (GA) dans Azure :

RÉFÉRENCE DE LA MACHINE VIRTUELLE|US Gov VA|US Gov IA|Notes
---|---|---|---
A|GA|GA|Aucun
Dv1|GA|-|Aucun
DSv1|GA|-|Aucun
Dv2|GA|GA|15 sera bientôt disponible
F|GA|GA|Aucun
G|Planifié|-|Aucun

###  <a name="data-considerations"></a>Considérations sur les données

Les informations suivantes identifient la frontière Azure gouvernement pour Azure Virtual Machines :

| Données réglementé/contrôlé autorisé | Données réglementé/contrôlé non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Les données entrées, stockées et traitées dans une machine virtuelle peut contenir des données d’exportation contrôlée. Fichiers binaires dans Azure Virtual Machines en cours d’exécution. Authentificateurs statiques, telles que les mots de passe et les broches de la carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Chaînes de connexion SQL.  Autres sécurité informations/secrets, tels que des certificats, des clés de chiffrement, des clés principales et les clés de stockage stockées dans des services Azure.  | Métadonnées ne sont pas autorisée à contenir des données d’exportation contrôlée. Ces métadonnées incluent toutes les données de configuration saisies lors de la création et la maintenance de votre ordinateur virtuel de Azure.  N’entrez pas réglementés/contrôle des données dans les champs suivants : client des noms de rôles, groupes de ressources, les noms de déploiement, les noms de ressources, les balises de ressources  

## <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour, abonnez-vous à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure gouvernement Blog.</a>
