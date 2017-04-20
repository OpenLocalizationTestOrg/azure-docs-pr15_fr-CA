<properties
   pageTitle="La Classification des données pour Azure | Microsoft Azure"
   description="Cet article fournit une introduction aux principes de base de la classification des données et met en surbrillance les sa valeur, en particulier dans le contexte du cloud computing et à l’aide de Microsoft Azure"
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
   ms.author="yurid"/>

# <a name="data-classification-for-azure"></a>Classification des données pour Azure

Cet article fournit une introduction aux principes de base de la classification des données et met en surbrillance les sa valeur, en particulier dans le contexte du cloud computing et à l’aide de Microsoft Azure. 

## <a name="data-classification-fundamentals"></a>Principes de classification de données

La classification des données réussi dans une organisation nécessite une large prise en compte des besoins de votre organisation et une connaissance approfondie de l’emplacement de vos données de l’entreprise.  
 
Les données existent dans un des trois états de base : 

- Au repos 
- Dans le processus 
- En transit 
 
Les trois états nécessitent des solutions techniques uniques pour la classification des données, mais les principes appliqués de classification de données doivent être la même pour tous. Les données qui vous sont classifiées comme confidentielles doivent rester confidentielles au repos, de processus et en transit. 
 
Données peuvent également être structurées ou non structurées. Processus de classement par défaut pour les données structurées dans les bases de données et des feuilles de calcul sont moins complexes et fastidieux à gérer que celles des données non structurées comme les documents, code source et des e-mails. 

> [AZURE.TIP] Pour plus d’informations sur les capacités d’Azure et meilleures pratiques pour les données de chiffrement lire méthodes [Conseillées cryptage de données Azure](azure-security-data-encryption-best-practices.md)

En général, les organisations ont plus les données non structurées à des données structurées. Que si les données sont structurées ou non, il est important pour vous permet de gérer la sensibilité des données. Lorsqu’il est correctement implémenté, la classification des données permet de garantir que les données sensibles ou confidentielles actifs sont gérés avec une supervision plue que des ressources de données sont considérés comme publics ou libres de distribuer. 

### <a name="controlling-access-to-data"></a>Contrôle de l’accès aux données 

Authentification et autorisation sont confondent souvent avec eux et leurs rôles mal comprise. En réalité ils sont très différents, comme illustré dans la figure suivante.  

![Contrôle et accès aux données](./media/azure-security-data-classification/azure-security-data-classification-fig1.png)

### <a name="authentication"></a>Authentification 

L’authentification est généralement constitué d’au moins deux parties : un nom d’utilisateur ou votre code utilisateur pour identifier un utilisateur et un jeton, comme un mot de passe pour confirmer que les informations d’identification de nom d’utilisateur sont valide. Le processus ne fournit pas de l’utilisateur authentifié ayant accès à des articles ou des services ; Il vérifie que l’utilisateur est bien ceux qu’ils prétendent qu'être.   

> [AZURE.TIP] [Azure Active Directory](../active-directory/active-directory-whatis.md) fournit les services d’identité basé sur le cloud qui vous permettent d’authentifier et d’autoriser les utilisateurs. 

### <a name="authorization"></a>Autorisation
 
L’autorisation est le processus de fourniture d’un utilisateur authentifié la possibilité d’accéder à une application, jeu de données, fichier de données ou un autre objet. Affectation d’utilisateurs authentifiés les droits d’utiliser, modifier ou supprimer les éléments auxquels ils peuvent accéder requiert votre attention sur la classification des données. 

Autorisation réussie nécessite la mise en oeuvre d’un mécanisme permettant de valider les besoins des utilisateurs individuels d’accéder aux fichiers et informations basés sur une combinaison de rôles, la stratégie de sécurité et la stratégie de risques, considérations. Par exemple, n’est peut-être pas nécessaire que les données à partir des applications de métier (LOB) spécifiques est accessible par tous les employés, et seul un petit sous-ensemble d’employés probablement besoin d’accéder à des fichiers de ressources humaines (RH). Mais aux organisations de contrôler qui peut accéder à des données, comme ainsi que quand et comment, un système efficace pour authentifier les utilisateurs doit être en place. 

> [AZURE.TIP] dans Microsoft Azure, veillez à tirer parti du contrôle d’accès Azure Role-Based (RBAC) pour accorder uniquement le montant d’accès que les utilisateurs ont besoin pour effectuer leur travail. Pour plus d’informations, consultez [utiliser les affectations de rôles pour gérer l’accès à vos ressources d’Azure Active Directory](../active-directory/role-based-access-control-configure.md) . 

### <a name="roles-and-responsibilities-in-cloud-computing"></a>Rôles et responsabilités dans le cloud computing 

Bien que les fournisseurs de nuage peuvent vous aider à gérer les risques, les clients ont besoin assurer cette gestion de classification des données et l’application est correctement implémentée pour fournir le niveau approprié des services de gestion de données.  
 
Responsabilités de classification des données varient selon le modèle de service cloud est en place, comme indiqué dans la figure suivante. Les trois modèles de service cloud principal sont infrastructure sous la forme d’un service (IaaS), plateforme en tant que service (PaaS), logiciel en tant que service (SaaS). Mise en œuvre de mécanismes de classification des données peut également varier en fonction du vis-à-vis et les attentes du fournisseur de nuage. 

![Rôles](./media/azure-security-data-classification/azure-security-data-classification-fig2.png)

Bien que vous êtes responsable de la classification de vos données, fournisseurs de nuage prenez des engagements écrits sur comment ils sécurisée et à maintenir la confidentialité des données client stockées au sein de leur nuage.  

- Besoins des **fournisseurs de IaaS** sont limités pour s’assurer que l’environnement virtuel peut prendre en charge les exigences de conformité de client et des capacités de classification des données. IaaS fournisseurs ont un rôle réduit dans la classification des données, car ils ne doivent s’assurer que les données client résout les exigences de conformité. Toutefois, les fournisseurs doivent tout en garantissant que leurs environnements virtuels répondent aux exigences de classification de données en plus de sécuriser leurs centres de données.
- Responsabilités des **fournisseurs de services PaaS** peuvent être mélangées, dans la mesure où la plate-forme peut servir dans une approche multiniveau pour assurer la sécurité d’un outil de classification. Les fournisseurs de PaaS peuvent être responsables de l’authentification et éventuellement des règles d’autorisation et doivent fournir des capacités de classification des données pour leur couche application et de sécurité. Beaucoup comme des fournisseurs IaaS, PaaS fournisseurs doivent vous assurer que leur plate-forme est conforme à des exigences de classification des données pertinentes.
- **Fournisseurs de SaaS** seront souvent considérées comme partie d’une chaîne d’autorisation, et est nécessaire pour s’assurer que les données stockées dans cette solution SaaS application peut être contrôlée par type de classification. Les applications de SaaS peuvent être utilisées pour les applications métier et par la nécessité de leur nature même de fournir les moyens de les authentifier et autoriser les données utilisées et stockées. 

## <a name="classification-process"></a>Processus de classification 

De nombreuses organisations comprennent la nécessité de la classification des données et que voulez mettre en œuvre un véritable défi base : où commencer ?

Une façon simple et efficace pour mettre en œuvre la classification des données est d’utiliser la vérification du PLAN d’action, agir de modèle à partir de la [structure MOF](https://technet.microsoft.com/solutionaccelerators/dd320379.aspx). La figure suivante graphiques les tâches requises pour implémenter la classification des données dans ce modèle.  

1. Le **PLAN**. Identifier les données de l’entreprise, recourir à un conservateur de déployer le programme de classification et de développer des profils de protection des données. 
2. **DO**. Une fois les règles de classification des données sont convenus, déployer le programme et mettre en œuvre des technologies de mise en œuvre selon les besoins de données confidentielles.  
3. À **Vérifier**. Vérifier et valider le rapports pour vous assurer que les outils et méthodes utilisés sont adressage efficacement les stratégies de classification. 
4. **ACT**. Consulter le statut de l’accès aux données et affichage des fichiers et données qui nécessitent une révision à l’aide d’une méthodologie de reclassement et de révision d’adopter les modifications et de faire face aux nouveaux risques.  

![Planifier, faire, vérifier, agir](./media/azure-security-data-classification/azure-security-data-classification-fig3.png)
 
###<a name="select-a-terminology-model-that-addresses-your-needs"></a>Sélectionner un modèle de la terminologie qui répond à vos besoins
 
Il existe plusieurs types de processus de classification des données, y compris les processus manuels, processus fondée sur l’emplacement classent les données en fonction de l’emplacement du système de l’utilisateur, basée sur l’application de processus, tels que le classement de base de données spécifique et automatisation des processus utilisés par différentes technologies, dont certaines sont décrites dans la section « Protection des données confidentielles » plus loin dans cet article.  
 
Cet article présente les deux modèles de terminologie généralisées qui sont basés sur des modèles bien utilisés et respectées à l’industrie. Ces modèles de terminologie, qui proposent tous deux trois niveaux de sensibilité de classification, sont affichés dans le tableau suivant.  

> [AZURE.NOTE] lors de la classification d’un fichier ou une ressource qui combine les données qui sont généralement classifiées à différents niveaux, le plus haut niveau de classification actuelle doit établir la classification générale. Par exemple, un fichier contenant des données sensibles et restreintes doit être classé comme sensibles.  

| **Sensibilité**   | **Modèle de la terminologie 1**   | **Modèle de la terminologie 2** |
|--------------------|---------------------------|-------------------------|
| Élevé               | Confidentielles              | Restreint              |
| Support             | À usage interne uniquement     | Sensibles               |
| Faible                | Public                    | Sans restriction            |

#### <a name="confidential-restricted"></a>Confidentiel (limité) 

Les informations qui vous sont classifiées comme confidentielles ou restreintes incluent des données qui peuvent être grave à une ou plusieurs personnes et/ou entreprises si compromises ou perdues. Cette information est souvent fournie sur une base « besoin de savoir » et peut inclure : 

- Données à caractère personnel, y compris les informations d’identification personnelle de sécurité sociale ou numéro d’identification nationale, passport numéros, numéros de carte de crédit, du pilote numéros de licence, dossiers médicaux, et numéros d’ID de stratégie de l’assurance maladie.  
- Enregistrements financiers, y compris par exemple la vérification des numéros de compte financier ou les numéros de compte de placement. 
- Matériau de professionnels, tels que des documents ou des données qui sont unique et spécifique de propriété intellectuelle.  
- Données juridiques, y compris les éléments potentiels privilégié avocat. 
- Données d’authentification, y compris d’autres séquences d’identification tels que les fichiers de clé privée biométriques, paires de nom d’utilisateur mot de passe ou des clés de chiffrement privées. 

Données qui soit classifiées confidentielle fréquemment ont réglementaires et les exigences de conformité pour la gestion des données. 

#### <a name="for-internal-use-only-sensitive"></a>Pour usage interne uniquement (la casse)
 
Les informations qui vous sont classifiées comme étant de sensibilité moyenne comprennent des fichiers et des données qui n’auraient pas un grave impact sur une organisation ou un individuelles perte ou sa destruction. Ces informations peuvent inclure : 

- E-mail, dont la plupart peut être supprimée ou distribuée sans provoquer une crise (à l’exclusion des boîtes aux lettres ou courrier électronique de personnes qui sont identifiées dans la classification confidentielle).  
- Les documents et les fichiers qui ne contiennent pas de données confidentielles.
 
En règle générale, cette classification inclut tout ce qui n’est pas confidentielles. Cette classification peut inclure la plupart des données d’entreprise, car la plupart des fichiers qui sont gérés ou utilisés au quotidien peuvent être classés comme sensibles. À l’exception des données qui sont rendues publique ou qui sont confidentielles, toutes les données au sein de l’organisation d’une entreprise peuvent être classées comme sensibles par défaut. 

#### <a name="public-unrestricted"></a>Public (aucune restriction)
 
Informations qui sont classifiées comme public incluent les données et les fichiers qui ne sont pas critiques pour les besoins de l’entreprise ou des opérations. Cette classification peut également inclure des données qui a délibérément été publiées au public en vue de leur utilisation, tels que des documents marketing ou communiqués de presse. En outre, cette classification peut inclure des données telles que les e-mails de spam stockées par un service de messagerie. 

### <a name="define-data-ownership"></a>Définir la propriété des données
 
Il est important de définir une chaîne effacer privatives de liberté de propriété pour toutes les données de l’entreprise. Le tableau suivant identifie les rôles de propriété de données différents dans les efforts de classification des données et de leurs droits respectifs.  

| **Rôle**        | **Créer**    | **Modifier/supprimer.**   | **Délégué**  | **En lecture**    | **Archivage/restauration**   |
|-----------------|---------------|---------------------|---------------|-------------|-----------------------|
| Propriétaire           | X             | X                   | X             | X           | X                     |
| Dépositaire       |               |                     | X             |             |                       |
| Administrateur   |               |                     |               |             | X                     |
| Utilisateur\*          |               | X                   |               | X           |                       |
**Les utilisateurs peuvent bénéficier des droits supplémentaires, telles que modifier et supprimer par un dépositaire* 

> [AZURE.NOTE] Ce tableau ne fournit pas une liste exhaustive des rôles et des droits, mais simplement un échantillon représentatif. 

Le **propriétaire de la ressource** est le créateur d’origine des données, qui peuvent déléguer l’appartenance et affecter un dépositaire. Lorsqu’un fichier est créé, le propriétaire doit être en mesure d’attribuer un classement, ce qui signifie qu’ils ont une responsabilité de comprendre ce qui doit être classifiées comme confidentielles selon leurs stratégies de l’organisation. Toutes les données du propriétaire des biens une données peuvent être classées en automatique pour usage interne uniquement (la casse) sauf s’ils sont chargés de propriétaire ou de la création de types de confidentielles (limitées). Fréquemment, le rôle de l’utilisateur change une fois que les données sont confidentielles. Par exemple, le propriétaire peut créer une base de données des informations classifiées et céder leurs droits au dépositaire de données.  

> [AZURE.NOTE] les propriétaires actifs de données utilisent souvent un mélange de services, les périphériques et les supports, dont certaines sont personnelles et dont certaines appartiennent à l’organisation. Une stratégie claire d’organisation peut aider à garantir que l’utilisation de périphériques tels que les ordinateurs portables et les périphériques Smart Device est conformément aux lignes directrices de classification de données.  

Le **dépositaire de capital des données** est affectée par le propriétaire de la ressource (ou son délégué) pour gérer les ressources en fonction d’accords avec le propriétaire de la ressource ou conformément aux exigences de la stratégie applicable. Dans l’idéal, le rôle de conservateur peut être implémenté dans un système automatisé. Un dépositaire de capital permet de s’assurer que les contrôles d’accès nécessaires sont fournis et est responsables de la gestion et la protection des ressources déléguées à leurs soins. Les responsabilités du dépositaire de l’actif pourraient inclure :  

- Protection de l’actif, selon la direction du propriétaire de la ressource ou en accord avec le propriétaire de la ressource 
- Assurer le respect des règles de classification 
- Informer les propriétaires des ressources de toute modification convenus des contrôles ou procédures de protection avant ces modifications prenant effet le 
- Rapport au propriétaire de la ressource sur la modification ou de suppression des responsabilités du dépositaire de l’actif 
- Un **administrateur** représente un utilisateur qui est chargé de s’assurer que l’intégrité est préservée, mais ils ne sont pas un propriétaire de la ressource, un dépositaire ou un utilisateur. En fait, de nombreux rôles d’administrateur fournissent des services de gestion de conteneur de données sans avoir accès aux données. Le rôle d’administrateur inclut les ressources de sauvegarde et de restauration des données, gestion des enregistrements d’actifs, choix, acquisition et d’exploitation les périphériques et le stockage de cette maison. 
- L’utilisateur actif inclut toute personne qui a accès à des données ou un fichier. Affectation de l’accès est souvent déléguée par le propriétaire pour le dépositaire de l’actif.  

### <a name="implementation"></a>Mise en œuvre
  
Considérations relatives à la gestion s’appliquent à toutes les méthodes de classification. Ces considérations doivent inclure des détails sur la personne qui, quoi, où, quand et pourquoi un actif de données devrait être utilisé, accédé, modifié ou supprimé. Gestion des ressources doit être effectuée avec une compréhension de la façon dont une organisation affiche ses risques, mais une méthodologie simple peut être appliquée telle que définie dans le processus de classification des données. Considérations supplémentaires sur la classification des données incluent l’introduction de nouvelles applications et les outils et la gestion du changement après l’implémentation d’une méthode de classification.  

### <a name="reclassification"></a>Reclassement
 
Reclassement ou modification de l’état de la classification d’un actif de données doit être effectuée lorsqu’un utilisateur ou un système détermine que le profil de risque ou d’importance de la ressource de données a été modifiée. Cet effort est important de veiller à ce que le statut de classification continue à être valide et en cours. La plupart du contenu qui n’est pas classé manuellement peut être classé automatiquement ou basé sur l’utilisation par un dépositaire de données ou le propriétaire des données. 

### <a name="manual-data-reclassification"></a>Reclassement de données manuelles
 
Dans l’idéal, cet effort de garantir que les détails d’une modification sont capturés et auditées. La raison la plus probable de reclassement manuel serait pour des raisons de sensibilité, ou les enregistrements conservés dans le format de papier, ou une spécification pour consulter les données qui a été classées à l’origine. Parce que ce livre blanc considère que la classification des données et transférer des données vers le nuage, les efforts de reclassement manuelle nécessiterait l’attention sur une base au cas par cas et une analyse des risques est idéale pour répondre aux exigences de la classification. En règle générale, un tel effort considère la stratégie de l’organisation sur ce qui doit être classé, l’état de classification par défaut (toutes les données et des fichiers sensibles mais non confidentielles) et de prendre des exceptions pour les données à haut risque. 

### <a name="automatic-data-reclassification"></a>Reclassement de données automatique
 
Reclassement de données automatique utilise la même règle générale comme classement manuel. L’exception est que les solutions automatisées peuvent garantir que les règles soient suivis et appliqués en fonction des besoins. La classification des données peut être effectuée dans le cadre d’une stratégie de mise en œuvre de classification de données, qui peut être appliquée lorsque les données sont stockées, en cours d’utilisation et en transit à l’aide de la technologie d’autorisation.

- Basé sur l’application. L’utilisation de certaines applications par défaut définit un niveau de classification. Par exemple, les données de logiciel la relation client (CRM) de gestion, les ressources humaines et les outils de gestion des enregistrements d’état sont confidentielles par défaut. 
- Fondée sur l’emplacement. Emplacement des données peut aider à identifier la sensibilité des données. Par exemple, les données qui sont stockées par une ressources humaines ou du département financier sont plus susceptibles d’être de caractère confidentiel.  
 
### <a name="data-retention-recovery-and-disposal"></a>Cession, de restauration et de rétention des données 

Récupération de données et l’élimination, comme le reclassement des données, est un aspect essentiel de la gestion des ressources de données. Les principes pour la récupération de données et l’élimination devrait être définis par une stratégie de rétention de données et appliquées au cours de la même manière que le reclassement des données ; un tel effort doit être effectué par les rôles administrateur et de dépositaire sous la forme d’une tâche de collaboration.  

La non-utilisation d’une stratégie de rétention de données peut signifier la perte de données ou de non-respect des exigences de découverte réglementaires et juridiques. La plupart des organisations qui n’ont pas une stratégie de rétention de données clairement définies ont tendance à utiliser une stratégie de rétention par défaut « conserver tout ». Toutefois, une telle stratégie de rétention a des risques dans les scénarios de services cloud. 

Par exemple, une stratégie de rétention des données pour les fournisseurs de services de cloud peut être considéré comme comme « la durée de l’abonnement » (tant que le service est payé pour, les données sont conservées). Un tel accord de paie-pour-rétention ne résout pas les stratégies de rétention d’entreprise ou de réglementation. Définition d’une stratégie de données confidentielles peut garantir que les données sont stockées et supprimées en fonction des meilleures pratiques. En outre, une stratégie d’archivage peut être créée pour formaliser une connaissance sur les données qui doivent être supprimées et à quel moment. 

Stratégie de rétention de données doit répondre à la réglementation et les exigences de conformité, ainsi que les exigences de rétention juridique de l’entreprise. Données confidentielles peuvent provoquer des questions sur la durée de rétention et d’exceptions pour les données qui ont été stockées avec un fournisseur ; ces questions sont plus susceptibles de données qui n’ont pas été classifiées correctement. 

> [AZURE.TIP] en savoir plus sur les stratégies de rétention des données Azure et plus à lire [Contrat d’abonnement en ligne Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/)

## <a name="protecting-confidential-data"></a>Protection des données confidentielles
  
Une fois que les données sont confidentielles, recherche et mise en œuvre des moyens efficaces pour protéger les données confidentielles devient partie intégrante de toute stratégie de déploiement de protection de données. Protéger les données confidentielles requiert une attention supplémentaire à comment les données sont stockées et transmises dans les architectures classiques, ainsi que dans le nuage. 

Cette section fournit des informations de base sur certaines technologies permettant d’automatiser les efforts de mise en œuvre pour protéger les données qui ont été classifiées comme confidentielles. 
 
Comme le montre la figure suivante, ces technologies peuvent être déployés comme solutions nuage ou sur site, ou de manière hybride, avec certaines de leur déploiement sur site et d’autres dans le nuage. (Certaines technologies, telles que le chiffrement et la gestion des droits, étendent également aux périphériques de l’utilisateur).  

![Technologies](./media/azure-security-data-classification/azure-security-data-classification-fig4.png)

### <a name="rights-management-software"></a>Logiciel de gestion de droits  

Une seule solution pour prévenir la perte de données est un logiciel de gestion de droits. Contrairement aux approches qui tentent d’interrompre le flux d’informations au niveau des points de sortie dans une organisation, logiciel de gestion de droits fonctionne à des niveaux de profondes dans les technologies de stockage de données. Les documents sont chiffrés, et contrôler qui peut les décrypter utilise des contrôles d’accès qui sont définis dans une solution de contrôle d’authentification comme un service d’annuaire.  

> [AZURE.TIP] Vous pouvez utiliser Azure Rights Management (RMS Azure) comme la solution de protection des informations pour protéger les données dans des scénarios différents. Lecture [quoi consiste la gestion des droits Azure ?](https://docs.microsoft.com/rights-management/understand-explore/what-is-azure-rms) pour plus d’informations sur cette solution Azure.

Parmi les avantages du logiciel de gestion des droits : 

- Informations sauvegardées. Les utilisateurs peuvent protéger leurs données directement à l’aide d’applications compatibles avec gestion de droits. Aucune étape supplémentaire est requise : création de documents, envoi de courrier électronique et la publication des données offrent une expérience de protection de données cohérentes. 
- Protection se déplace avec les données. Les clients restent dans le contrôle de l’accès à leurs données, que ce soit dans le nuage, l’infrastructure informatique existante, ou au bureau de l’utilisateur. Organisations peuvent choisir de crypter leurs données et de restreindre l’accès en fonction des besoins de leur entreprise. 
- Stratégies de protection des informations par défaut. Les administrateurs et les utilisateurs peuvent utiliser des stratégies standard pour de nombreux scénarios d’entreprise, telles que « Société confidentiel-lecture seule » et « Ne pas transférer ». Un ensemble complet d’utilisation des droits sont prises en charge telles que lire, copier, imprimer, enregistrer, modifier et transmettre pour offrir plus de souplesse dans la définition des droits d’utilisation personnalisés. 

> [AZURE.TIP] Vous pouvez protéger les données dans le stockage Azure en utilisant le [Cryptage de Service de stockage Azure](../storage/storage-service-encryption.md) pour données au repos. Vous pouvez également utiliser le [Cryptage de disque Azure](azure-security-disk-encryption.md) pour protéger les données contenues sur les disques virtuels utilisés pour Azure Virtual Machines.

### <a name="encryption-gateways"></a>Passerelles de cryptage

Les passerelles de cryptage fonctionnent dans leurs couches pour fournir des services de cryptage en redirigeant tous les accès aux données basées sur le nuage. Cette approche ne doit pas être confondue avec celui d’un réseau privé virtuel (VPN). Les passerelles de cryptage sont conçus pour fournir une couche transparente aux solutions basées sur le nuage.   

Les passerelles de cryptage peuvent fournir un moyen de gérer et de sécuriser les données qui ont été classifiées comme confidentielles en cryptant les données en transit, ainsi que des données au repos.  
 
Les passerelles de cryptage sont placés dans le flux de données entre les périphériques de l’utilisateur et les données d’application centres pour fournir des services de cryptage et de décryptage. Ces solutions, comme les réseaux privés virtuels, sont principalement les solutions sur site. Ils sont conçus pour fournir à un tiers de contrôle sur les clés de cryptage, qui contribue à réduire le risque de placer les données et la clé de gestion avec un seul fournisseur. Ces solutions sont conçues, comme le cryptage, pour travailler de façon transparente et en toute transparence entre les utilisateurs et le service. 

> [AZURE.TIP] Vous pouvez utiliser Azure ExpressRoute pour étendre vos réseaux locaux dans le nuage de Microsoft via une connexion privée dédiée. Lisez la [présentation technique de ExpressRoute](../expressroute/expressroute-introduction.md) pour plus d’informations sur cette fonctionnalité. Une autre des options pour croisée connectivité locaux entre votre réseau de locaux et [d’Azure est un VPN de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

### <a name="data-loss-prevention"></a>Prévention des fuites de données 
Perte de données (parfois appelé les fuites de données) est importante, et la prévention de la perte de données externes via initiés malveillantes et accidentelles est primordiale pour de nombreuses organisations.  
 
Les technologies de prévention contre la perte de données peuvent aider à garantir que les solutions de services de messagerie ne transmettent pas les données qui ont été classifiées comme confidentielles. Les entreprises peuvent tirer parti des fonctionnalités DLP dans des produits existants afin d’éviter la perte de données. Ces fonctionnalités utilisent des stratégies qui peuvent être facilement créés de toutes pièces ou à l’aide d’un modèle fourni par le fournisseur de logiciel.  
 
Les technologies DLP peuvent effectuer l’analyse approfondie du contenu par le biais de correspondances de mots-clés, correspondance de dictionnaire, évaluation de l’expression régulière et autre examen de contenu à détecter le contenu qui viole les stratégies d’organisation DLP. Par exemple, DLP peut aider à empêcher la perte des types de données suivants : 

- Sécurité sociale et numéros d’identification nationales 
- Informations bancaires 
- Numéros de carte de crédit  
- Adresses IP 

Certaines technologies DLP offrent également la possibilité de substituer la configuration de DLP (par exemple, si une organisation a besoin de transmettre des informations de numéro de sécurité sociale à un processeur de paie). En outre, il est possible de configurer DLP afin que les utilisateurs soient avertis avant de même tenter d’envoyer des informations sensibles qui ne doivent pas être transmises. 

> [AZURE.TIP] Vous pouvez utiliser les fonctionnalités d’Office 365 DLP pour protéger vos documents. Lecture [contrôles de conformité Office 365 : Data Loss Prevention](https://blogs.office.com/2013/10/28/office-365-compliance-controls-data-loss-prevention/) pour plus d’informations.

## <a name="see-also"></a>Voir aussi

- [Meilleures pratiques de cryptage des données Azure](azure-security-data-encryption-best-practices.md)
- [Azure Gestion d’identité et contrôle d’accès de sécurité conseillées](azure-security-identity-management-best-practices.md)
- [Blog de l’équipe sécurité Azure](http://blogs.msdn.com/b/azuresecurity/)
- [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

