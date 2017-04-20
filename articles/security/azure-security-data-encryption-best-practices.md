<properties
   pageTitle="Pratiques recommandées de sécurité des données et le cryptage | Microsoft Azure"
   description="Cet article fournit un ensemble de meilleures pratiques pour la sécurité des données et des capacités Azure à l’aide de cryptage intégrées."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yuridio"/>

#<a name="azure-data-security-and-encryption-best-practices"></a>Meilleures pratiques de sécurité de données Azure et cryptage

Une des clés de protection des données dans le nuage est comptabilisation des états possibles dans lesquels vos données peuvent se produire, et les contrôles sont disponibles pour cet état. Aux fins de données Azure sécurité cryptage les meilleures pratiques et les recommandations seront les États de données suivantes :

- Au repos : Cela inclut des informations de tous les objets de stockage, les conteneurs et les types qui existent de manière statique sur un support physique, être magnétique ou disque optique.

- Transit : Lorsque les données sont transférées entre des composants, des emplacements ou des programmes, tels que sur le réseau, à travers un bus de service (à partir de locaux vers le cloud et vice versa, y compris les connexions hybrides tels que ExpressRoute), ou les lors d’un processus d’entrée/sortie, elle est considérée comme étant en mouvement.

Dans cet article, nous allons étudier un ensemble de données Azure sécurité et chiffrement de meilleures pratiques. Ces meilleures pratiques proviennent de notre expérience avec la sécurité des données Azure et de cryptage et de l’expérience des clients tels que vous-même.

Pour chaque meilleure pratique, nous allons expliquer :

- Quelle est la meilleure pratique
- Raison pour laquelle vous souhaitez activer que les meilleures pratiques
- Quel peut être le résultat si vous ne parvenez pas à activer la meilleure pratique
- Alternatives possibles à la pratique recommandée
- Comment vous pouvez apprendre à activer la meilleure pratique

Cet article de la sécurité des données Azure et les meilleures pratiques de cryptage est basé sur un avis de consensus et les fonctionnalités de la plateforme Azure et les ensembles de fonctionnalités, tels qu’ils existent au moment de la que rédaction de cet article. Les technologies et les avis de changer avec le temps, et cet article sera mis à jour régulièrement pour refléter ces modifications.

Données Azure sécurité et cryptage meilleures pratiques décrites dans cet article sont les suivantes :

- Appliquer l’authentification selon plusieurs facteur
- Contrôle d’accès (RBAC) basé sur un rôle de l’utilisation
- Crypter les machines virtuelles Azure
- Utiliser des modèles de sécurité matériel
- Gérer des stations de travail sécurisé
- Activer le cryptage de données SQL
- Protéger les données en transit
- Renforcer le cryptage des données au niveau fichier


## <a name="enforce-multi-factor-authentication"></a>Appliquer l’authentification selon plusieurs facteur

La première étape de l’accès aux données et de contrôle dans Microsoft Azure consiste à authentifier l’utilisateur. [Azure plusieurs facteurs d’authentification (AMF)](../multi-factor-authentication/multi-factor-authentication.md) est une méthode de vérification de l’identité de l’utilisateur à l’aide d’une méthode autre qu’uniquement un nom d’utilisateur et un mot de passe. Cette authentification méthode vous aide à garantir l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple.

En activant les AMF Azure pour vos utilisateurs, vous ajoutez un second niveau de sécurité pour les connexions utilisateur et des transactions. Dans ce cas, une transaction peut accéder à un document situé dans un serveur de fichiers dans votre site SharePoint. AMF Azure vous aide également à réduire le risque qu’une information d’identification compromise aura accès aux données de l’organisation informatique.

Par exemple : Si vous appliquez Azure AMF pour vos utilisateurs et que vous configurez pour qu’il utilise un appel téléphonique ou un message texte en tant que vérification, si les informations d’identification de l’utilisateur sont compromise, l’attaquant ne sera pas en mesure d’accéder à toute ressource car il n’a pas accès au téléphone de l’utilisateur. Les organisations qui n’ajoutent pas cette couche supplémentaire de protection de l’identité sont plus sensibles pour l’attaque de vol d’informations d’identification, ce qui peut conduire à la compromission des données.

Une autre solution pour les organisations qui souhaitent conserver l’authentification contrôle local consiste à utiliser des [Azure plusieurs facteurs d’authentification serveur](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), également appelé AMF sur site. À l’aide de cette méthode vous serez toujours en mesure d’appliquer une authentification multifacteur, tout en conservant l’AMF serveur local.

Pour plus d’informations sur Azure AMF, lisez l’article [mise en route avec authentification à plusieurs facteurs Azure dans le nuage](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Contrôle d’accès (RBAC) basé sur un rôle de l’utilisation
Restreindre l’accès basé sur les principes de sécurité [doivent savoir](https://en.wikipedia.org/wiki/Need_to_know) et le [moins de privilèges possible](https://en.wikipedia.org/wiki/Principle_of_least_privilege) . Il est impératif pour les entreprises qui veulent appliquer des stratégies de sécurité pour accéder aux données. Azure contrôle d’accès basée sur les rôles (RBAC) permet d’attribuer des autorisations à des utilisateurs, des groupes et des applications au niveau d’une portée. La portée d’une affectation de rôle peut être une seule ressource, un groupe de ressources ou un abonnement.

Vous pouvez tirer parti [des rôles RBAC intégrés](../active-directory/role-based-access-built-in-roles.md) dans Azure pour affecter des privilèges aux utilisateurs. Envisagez d’utiliser des *Collaborateurs de compte de stockage* pour les opérateurs de cloud qui doivent gérer des comptes de stockage et le rôle de *Collaborateur de compte de stockage classique* pour gérer les comptes de stockage classique. Pour les opérateurs de nuage qui a besoin de gérer des ordinateurs virtuels et compte de stockage, envisagez de les ajouter au rôle de *Contributeur de la Machine virtuelle* .

Les entreprises qui n’appliquent pas de contrôle d’accès aux données en tirant parti des capacités de RBAC envoie plus de privilèges que nécessaire pour leurs utilisateurs. Cela peut conduire à la compromission des données par certains utilisateurs ayant accès aux données qu’ils ne devraient pas avoir en premier lieu.

Vous pouvez apprendre Azure RBAC en lisant l’article de [Contrôle d’accès Azure Role-Based](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Crypter les Machines virtuelles Azure
Pour de nombreuses organisations, [cryptage des données au repos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire à la souveraineté des données, de la conformité et de la confidentialité des données. Chiffrement de disque Azure permet aux administrateurs d’informatique crypter les disques Windows et Linux IaaS Machine virtuelle (VM). Cryptage de disque Azure tire parti de la fonctionnalité BitLocker standard d’industrie de Windows et de la fonction Crypt-DM de Linux pour fournir le cryptage de volume pour le système d’exploitation et les disques de données.

Vous pouvez utiliser le cryptage de disque Azure pour protéger et protéger vos données afin de répondre à vos exigences de conformité et de sécurité de l’organisation. Organisations doivent envisager également l’utilisation du chiffrement pour aider à atténuer les risques liés aux non autorisé de données access. Il est également recommandé de chiffrer les disques avant d’écrire des données sensibles pour les.

Veillez à chiffrer des volumes de données de votre ordinateur virtuel et le volume de démarrage afin de protéger les données au repos dans votre compte de stockage Azure. Sauvegarder les clés de cryptage et les secrets grâce à [Azure clé coffre-fort](../key-vault/key-vault-whatis.md).

Pour vos serveurs Windows local, prendre en compte le cryptage suivant meilleures pratiques :

- Utiliser [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) pour le cryptage des données
- Stocker les informations de récupération dans les services AD DS.
- S’il existe toute préoccupation que les clés BitLocker ont été compromis, nous vous recommandons de mettre en forme du lecteur pour supprimer toutes les instances des métadonnées BitLocker à partir du disque ou que vous déchiffrer et chiffrez la totalité du disque à nouveau.

Les entreprises qui n’appliquent pas de cryptage des données sont plus susceptibles d’être exposés à des problèmes d’intégrité des données, tels que des utilisateurs malveillants ou non autorisés, voler des données et compromission des comptes, l’accès non autorisé à des données dans un format clair. Outre ces risques, les entreprises qui doivent se conformer aux réglementations du secteur, doivent prouver qu’ils sont la preuve de diligence et utilisent les contrôles de sécurité appropriés pour améliorer la sécurité des données.

Vous plus d’informations sur le chiffrement des disques Azure en lisant l’article [Azure de chiffrement de disque pour Windows et Linux IaaS VMs](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Utiliser des Modules de sécurité matériels

Les solutions de cryptage permet de crypter les données clés secrètes. Par conséquent, il est essentiel que ces clés sont stockées en toute sécurité. Gestion des clés devient partie intégrante de la protection des données, dans la mesure où il sera utilisé pour stocker les clés secrètes qui sont utilisées pour crypter les données.

Chiffrement de disque Azure utilise [Azure clé de coffre-fort](https://azure.microsoft.com/services/key-vault/) pour vous aider à contrôler et de gérer des clés de cryptage de disque et les secrets de votre abonnement au coffre-fort clé, tout en s’assurant que toutes les données sur les disques de l’ordinateur virtuel sont cryptées au reste de votre stockage Azure. Vous devez utiliser Azure clé de coffre-fort pour auditer les clés et l’utilisation de la stratégie.

Il existe de nombreux risques inhérents liés aux contrôles de sécurité appropriés n’ayant ne pas mis en place pour protéger les clés secrètes qui ont été utilisés pour chiffrer vos données. Si les pirates ont accès aux clés secrètes, ils seront en mesure de décrypter les données et potentiellement avoir accès aux informations confidentielles.

Vous pouvez en savoir plus sur les recommandations générales pour la gestion des certificats dans Azure en lisant l’article [la gestion des certificats dans Azure : choses à faire et à ne pas faire](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Pour plus d’informations sur le coffre-fort de clé d’Azure, lire [mise en route de la chambre forte de clé Azure](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Gérer des stations de travail sécurisé

Dans la mesure où la grande majorité des attaques ciblent l’utilisateur final, le point de terminaison devient l’un des principaux points d’attaque. Si un attaquant compromet le point de terminaison, il peut exploiter les informations d’identification de l’utilisateur pour accéder aux données de l’organisation. La plupart des attaques de point de terminaison sont en mesure de tirer parti du fait que les utilisateurs sont des administrateurs dans leurs stations de travail locales.

Vous pouvez réduire ces risques à l’aide d’une station de travail sécurisée de gestion. Nous vous recommandons d’utiliser un de [Stations de travail d’accès privilégié (patte)](https://technet.microsoft.com/library/mt634654.aspx) pour réduire la surface d’attaque dans les stations de travail. Ces stations de travail de gestion sécurisé peuvent vous aider à atténuer certains de ces attaques garantissent vos données plus sûres. Veillez à utiliser la patte pour renforcer la protection et de verrouiller votre station de travail. Il s’agit d’une étape importante à fournir des garanties de haute sécurité pour les comptes, les tâches et protection des données sensibles.

Manque de protection du point de terminaison peut mettre vos données en danger, assurez-vous d’appliquer des stratégies de sécurité sur tous les périphériques qui sont utilisés pour consommer des données, quel que soit l’emplacement des données (nuage ou sur site).

Pour en savoir que plus sur le privilège accéder station de travail en lisant l’article de la [Sécurisation de l’accès privilégié](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Activer le cryptage de données SQL

[Chiffrement transparent des données de base de données de SQL Azure](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) protège contre la menace des activités malveillantes en effectuant le cryptage en temps réel et décryptage de la base de données, les sauvegardes associées et fichiers journaux des transactions inactives sans nécessiter de modifications à l’application.  TDE crypte le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de cryptage de la base de données.

Même si le stockage est crypté, il est très important pour le crypter votre base de données elle-même. Il s’agit d’une implémentation de l’approche défense en profondeur pour protéger les données. Si vous utilisez une [Base de données de SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) et que vous souhaitez protéger les données sensibles telles que les numéros de sécurité sociale ou de carte de crédit, vous pouvez crypter les bases de données avec le cryptage FIPS 140-2 validées 256 bits AES qui répond aux exigences de nombreuses normes de l’industrie (par ex., HIPAA, PCI).

Il est important de comprendre que les fichiers liés à l' [extension de pool de mémoire tampon](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) ne sont pas chiffrés lorsqu’une base de données est crypté à l’aide de TDE. Vous devez utiliser les outils de cryptage au niveau du système de fichiers telles que BitLocker ou le [Système de fichiers EFS](https://technet.microsoft.com/library/cc700811.aspx) (Encrypting File System) pour BPE les fichiers associés.

Depuis un utilisateur autorisé tel qu’un administrateur de sécurité ou un administrateur de base de données peut accéder aux données même si la base de données est cryptée avec TDE, vous devez également suivre les recommandations ci-dessous :

- Authentification au niveau de la base de données SQL
- Authentification AD Azure à l’aide des rôles RBAC
- Les utilisateurs et les applications doivent utiliser des comptes distincts pour l’authentification. De cette façon, vous pouvez limiter les autorisations accordées aux utilisateurs et aux applications et réduire les risques des activités malveillantes
- Implémenter la sécurité au niveau de la base de données à l’aide des rôles de base de données fixes (tels que db_datareader ou db_datawriter), ou vous pouvez créer des rôles personnalisés pour votre application accorder des autorisations explicites sur les objets de base de données sélectionnée

Les entreprises qui n’utilisent pas de cryptage au niveau de base de données peuvent être plus vulnérables aux attaques qui peuvent compromettre des données situées dans des bases de données SQL.

Vous pouvez plus d’informations sur le cryptage SQL TDE en lisant l’article [Chiffrement Transparent des données avec la base de données de SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Protéger les données en transit

Protection des données en transit doit être la partie essentielle de votre stratégie de protection des données. Dans la mesure où les données pour déplacer en avant et en arrière à partir de nombreux emplacements, est recommandé de toujours utiliser les protocoles SSL/TLS pour échanger des données entre différents emplacements. Dans certaines circonstances, vous souhaiterez peut-être isoler le canal de l’ensemble de la communication entre votre local et le nuage infrastructure à l’aide d’un réseau privé virtuel (VPN).

Pour les données de transfert entre votre infrastructure sur site et l’Azure, vous devez envisager les garanties appropriées telles que HTTPS ou VPN.

Pour les organisations qui ont besoin de sécuriser l’accès à partir de plusieurs stations de travail situé sur site d’Azure, utilisez [Azure VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Pour les organisations qui ont besoin de sécuriser l’accès à partir d’une station de travail qui se trouve sur site d’Azure, utilisez [Point-à-Site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Lier des jeux peuvent être déplacés sur un WAN à grande vitesse dédié de données plus grandes comme [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si vous choisissez d’utiliser ExpressRoute, vous pouvez également crypter les données au niveau de l’application à l’aide de [SSL/TLS](https://support.microsoft.com/kb/257591) ou autres protocoles pour une protection accrue.

Si vous interagissez avec le stockage Azure via le portail d’Azure, toutes les transactions se produisent via HTTPS. [API REST de stockage](https://msdn.microsoft.com/library/azure/dd179355.aspx) via le protocole HTTPS peut également être utilisée pour interagir avec [Le stockage Azure](https://azure.microsoft.com/services/storage/) et [Base de données de SQL Azure](https://azure.microsoft.com/services/sql-database/).

Les entreprises qui ne parviennent pas à protéger les données en transit sont plus sensibles pour [homme dans le milieu](https://technet.microsoft.com/library/gg195821.aspx), [écoute clandestine](https://technet.microsoft.com/library/gg195641.aspx) et piratage de session. Ces attaques peuvent être la première étape de l’accès à des données confidentielles.

Vous pouvez apprendre option Azure VPN en lisant l’article de la [planification et la conception de la passerelle VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Renforcer le cryptage des données au niveau fichier

Une autre couche de protection qui peut augmenter le niveau de sécurité pour vos données crypte le fichier lui-même, quel que soit l’emplacement du fichier.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utilise les stratégies de cryptage, d’identité et d’autorisation pour aider à sécuriser vos fichiers et vos e-mails. RMS Azure fonctionne sur plusieurs périphériques, téléphones, tablettes et PC en protégeant à la fois au sein de votre organisation et à l’extérieur de votre organisation. Cette fonctionnalité est possible car Azure RMS ajoute un niveau de protection qui sont conservées avec les données, même lorsqu’il quitte les limites de votre organisation.

Lorsque vous utilisez Azure RMS pour protéger vos fichiers, vous utilisez le chiffrement standard avec prise en charge complète de [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Lorsque vous exploitez Azure RMS pour la protection des données, vous avez l’assurance que la protection reste avec le fichier, même si celui-ci est copié vers un stockage qui n’est pas sous le contrôle de l’informatique, tel qu’un service de stockage cloud. Le même se produit pour les fichiers partagés par courrier électronique, le fichier est protégé en tant que pièce jointe à un message électronique, avec des instructions comment ouvrir la pièce jointe protégée.

Lors de la planification pour l’adoption de RMS d’Azure, nous recommandons les éléments suivants :

- Installation de l' [application de partage de RMS](https://technet.microsoft.com/library/dn339006.aspx). Cette application s’intègre avec Office applications en installant un bureau complément afin que les utilisateurs peuvent facilement protéger les fichiers directement.
- Configurer des applications et des services pour prendre en charge d’Azure RMS
- Créer des [modèles personnalisés](https://technet.microsoft.com/library/dn642472.aspx) qui reflètent les besoins de votre entreprise. Par exemple : un modèle pour des données secrètes supérieur qui doivent s’appliquer à tout très secret concernant les e-mails.

Les entreprises qui sont faibles sur la protection de fichiers et de [classification des données](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) peuvent être plus vulnérables aux fuites de données. Sans la protection de fichier appropriée, les organisations ne pourrez obtenir des perspectives métier, de surveiller de manière inappropriée et de prévenir l’accès malveillant aux fichiers.

Vous pouvez apprendre Azure RMS en consultant l’article [Mise en route de la gestion des droits Azure](https://technet.microsoft.com/library/jj585016.aspx).
