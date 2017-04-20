<properties
   pageTitle="Historique de publication de Version connecteur | Microsoft Azure"
   description="Cette rubrique répertorie toutes les versions des connecteurs pour Forefront Identity Manager (FIM) et le Gestionnaire d’identité Microsoft (MIM)"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="billmath"/>

# <a name="connector-version-release-history"></a>Historique de publication de Version de connecteur
Les connecteurs de Forefront Identity Manager (FIM) et le Gestionnaire d’identité Microsoft (MIM) sont fréquemment mis à jour.

>[AZURE.NOTE]
Cette rubrique est de FIM et MIM uniquement. Ces connecteurs ne sont pas pris en charge sur Azure Connect d’Active Directory.

Cette rubrique répertorie toutes les versions des connecteurs qui ont été publiés.

Liens apparentés :

- [Télécharger les dernières connecteurs](http://go.microsoft.com/fwlink/?LinkId=717495)
- Documentation de référence sur [Le connecteur LDAP générique](active-directory-aadconnectsync-connector-genericldap.md)
- Documentation de référence sur [Le connecteur SQL générique](active-directory-aadconnectsync-connector-genericsql.md)
- Documentation de référence de [Connecteur de Services Web](http://go.microsoft.com/fwlink/?LinkID=226245)
- Documentation de référence de [Connecteur de PowerShell](active-directory-aadconnectsync-connector-powershell.md)
- Documentation de référence de [Connecteur pour Lotus Domino](active-directory-aadconnectsync-connector-domino.md)

## <a name="111170"></a>1.1.117.0
Publié : 2016 mars

**Nouveau connecteur**  
Version initiale du [Connecteur de SQL générique](active-directory-aadconnectsync-connector-genericsql.md).

**Nouvelles fonctionnalités :**

- Connecteur LDAP générique :
    - Prise en charge pour l’importation de delta avec Isode.
- Connecteur de Services Web :
    - Mise à jour de l’activité de csEntryChangeResult et setImportErrorCode pour permettre les erreurs de niveau d’objet à retourner vers le moteur de synchronisation.
    - Mettre à jour les modèles SAP6 et SAP6User pour utiliser la nouvelle fonctionnalité d’erreur de niveau objet.
- Connecteur pour Lotus Domino :
    - Pour l’exportation, vous avez besoin d’une certification par le carnet d’adresses. Vous pouvez maintenant utiliser le même mot de passe pour tous les certificateurs afin de faciliter la gestion.

**Problèmes résolus :**

- Connecteur LDAP générique :
    - Pour le service d’annuaire IBM Tivoli, certains attributs de référence non détectées correctement.
    - Pour LDAP ouvert au cours d’une importation delta, les espaces situés au début et à la fin des chaînes ont été tronquées.
    - Pour Novell et NetIQ, une exportation de déplacer un objet entre les unités d’organisation/conteneur et en même temps de renommer l’objet a échoué.
- Connecteur de Services Web :
    - Si le service web possède plusieurs points de terminaison pour la même liaison, puis le connecteur n’a pas correctement trouvé ces points de terminaison.
- Connecteur pour Lotus Domino :
    - Exportation de l’attribut de nom complet à une base de données de messagerie n’a pas fonctionné.
    - Une exportation ajouté et supprimé les membres d’un groupe d’exporter uniquement les membres ajoutés.
    - Si un Document Notes est non valide (l’attribut IsValid a la valeur false), l’échec de connecteur.

## <a name="older-releases"></a>Versions plus anciennes
Avant mars 2016, les connecteurs ont été publiés dans les rubriques de prise en charge.

**LDAP générique**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, septembre de 2015
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, 2015 mars
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, 2015 janvier
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, 2014 septembre
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, 2014 mars

**WebServices**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, 2014 septembre

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, 2014 septembre

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, septembre de 2015
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, 2015 mars
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, 2014 août
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, 2014 février  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, les octobre 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, 2013 août

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration [d’Azure AD connexion de synchronisation](active-directory-aadconnectsync-whatis.md) .

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
