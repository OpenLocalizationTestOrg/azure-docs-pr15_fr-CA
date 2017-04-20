<properties
    pageTitle="Résolution des problèmes : La gestion de mot de passe Active Directory Azure | Microsoft Azure"
    description="Procédures de dépannage courantes pour la gestion de mot de passe Active Directory Azure, y compris la réinitialisation, modification, écriture différée, l’enregistrement, les étapes et les informations à inclure lorsque la recherche de l’aide."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="asteen"/>

# <a name="how-to-troubleshoot-password-management"></a>Comment faire pour résoudre les problèmes de gestion de mot de passe

> [AZURE.IMPORTANT] **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Si vous rencontrez des problèmes avec la gestion de mot de passe, nous sommes ici pour vous aider. La plupart des problèmes que vous risquez de rencontrer peuvent être résolus avec quelques étapes simples de dépannage que vous pouvez consulter sur ci-dessous pour résoudre les problèmes de votre déploiement :

* [**Informations à inclure lorsque vous avez besoin d’aide**](#information-to-include-when-you-need-help)
* [**Problèmes liés à la configuration de la gestion de mot de passe dans le portail de gestion Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problèmes de rapports de gestion de mot de passe dans le portail de gestion Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problèmes avec le mot de passe réinitialiser le portail de l’enregistrement**](#troubleshoot-the-password-reset-registration-portal)
* [**Problèmes avec le mot de passe réinitialisé Portal**](#troubleshoot-the-password-reset-portal)
* [**Problèmes de mot de passe d’écriture en différé**](#troubleshoot-password-writeback)
  - [Codes d’erreur de mot de passe d’écriture différée journal des événements](#password-writeback-event-log-error-codes)
  - [Problèmes de connectivité de l’écriture différée de mot de passe](#troubleshoot-password-writeback-connectivity)

Si vous avez essayé de dépannage décrites ci-dessous et sont toujours en cours d’exécution dans des problèmes, vous pouvez publier une question sur les [Forums de publicité Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou contacter le support technique et nous allons étudier votre problème dès que possible.

## <a name="information-to-include-when-you-need-help"></a>Informations à inclure lorsque vous avez besoin d’aide

Si vous ne pouvez pas résoudre votre problème avec les conseils ci-dessous, vous pouvez contacter nos techniciens du support technique. Lorsque vous contactez les, il est recommandé d’inclure les informations suivantes :

 - **Une description générale de l’erreur** , le message d’erreur exact a l’utilisateur Voir ?  Si aucun message d’erreur s’est produite, décrire le comportement inattendu est remarqué, dans le détail.
 - **Page** -page étaient vous lorsque vous l’avez vu l’erreur (inclut l’URL) ?
 - **Date / heure / fuseau horaire** – quelle était la date précise et l’heure que vous avez vu l’erreur (inclure le fuseau horaire) ?
 - **Prend en charge le Code** – en quel est le code de prise en charge généré lorsque l’utilisateur vu l’erreur (pour le trouver, reproduire l’erreur, puis cliquez sur le lien Code de prise en charge au bas de l’écran et envoyer le technicien du support technique le GUID qui résulte).
   - Si vous êtes sur une page sans un code de prise en charge en bas, appuyez sur la touche F12 et rechercher le SID et le CID et envoyer ces deux résultats à l’ingénieur de support.

    ![][001]

 - **ID de l’utilisateur** – quel est l’ID de l’utilisateur qui a vu l’erreur (par exempleuser@contoso.com)?
 - **Informations sur l’utilisateur** – a l’utilisateur fédéré, synchronisées, du hachage de mot de passe seulement le nuage ?  L’utilisateur disposait d’une licence de DAS Premium ou Basic DAS attribuée ?
 - **Journal des événements applications** : Si vous utilisez l’écriture différée de mot de passe et l’erreur est dans votre infrastructure sur site, zip, une copie de votre journal des événements applications à partir de votre serveur Azure AD connecter et envoyer avec votre demande.

Incluant cette information va nous aider à résoudre le problème aussi rapidement que possible.


## <a name="troubleshoot-password-reset-configuration-in-the-azure-management-portal"></a>Résoudre les problèmes de configuration de réinitialisation de mot de passe dans le portail de gestion Azure
Si vous rencontrez une erreur lors de la configuration du mot de passe réinitialisé, vous pourrez peut-être le résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas la section <strong>Stratégie de réinitialisation de mot de passe utilisateur </strong>sous l’onglet <strong>configurer</strong> dans le portail de gestion Azure</p>
            </td>
            <td>
              <p>La section de la <strong>Stratégie de réinitialisation de mot de passe utilisateur </strong>n’est pas visible dans l’onglet <strong>configurer</strong> dans le portail de gestion Azure.</p>
            </td>
            <td>
              <p>Cela peut se produire si vous ne disposez pas d’une licence de DAS Premium ou DAS base affectée à l’administrateur d’effectuer cette opération. </p>
              <p>Pour corriger cette erreur, attribuer une licence DAS Premium ou Basic DAS pour le compte d’administrateur en question en accédant à l’onglet <strong>licences</strong> et essayez à nouveau.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas une des options de configuration dans la section <strong>Stratégie de réinitialisation de mot de passe utilisateur</strong> sont décrites dans la documentation.</p>
            </td>
            <td>
              <p>La section de la <strong>Stratégie de réinitialisation de mot de passe utilisateur </strong>est visible, mais le seul indicateur qui s’affiche sous l’indicateur <strong>Les utilisateurs activés pour la réinitialisation du mot de passe</strong> .</p>
            </td>
            <td>
              <p>Le reste de l’interface utilisateur s’affiche lorsque vous passez l’indicateur <strong>Les utilisateurs activés pour la réinitialisation du mot de passe</strong> <strong>Oui.</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas une option de configuration particulier.</p>
            </td>
            <td>
              <p>Par exemple, je ne vois pas l’option <strong>nombre de jours avant que l’utilisateur doit confirmer leurs données de contact</strong> lorsque je fais défiler la section <strong>Stratégie de réinitialisation de mot de passe utilisateur</strong> (ou d’autres exemples de la même émission).</p>
            </td>
            <td>
              <p>De nombreux éléments de l’interface utilisateur sont masqués jusqu'à ce qu’ils sont nécessaires. Essayez d’activer toutes les options de la page si vous souhaitez afficher.</p>
              <p>Pour plus d’informations sur tous les contrôles qui sont disponibles pour vous, consultez <a href="active-directory-passwords-customize.md#password-management-behavior">comportement de la gestion de mot de passe</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas l’option de configuration <strong>Écriture retour des mots de passe locaux</strong></p>
            </td>
            <td>
              <p>L’option <strong>d’Écriture retour des mots de passe local</strong> n’est pas visible sous l’onglet <strong>configurer</strong> dans le portail de gestion Azure</p>
            </td>
            <td>
              <p>Cette option n’est visible que si vous avez téléchargé Azure Connect d’Active Directory et configurés en écriture différée du mot de passe. Lorsque vous l’avez fait, cette option s’affiche et vous permet d’activer ou de désactiver l’écriture différée dans le nuage.</p>
              <p>Pour plus d’informations sur la procédure à suivre, reportez-vous à la section <a href="active-directory-passwords-getting-started.md#step-2-enable-password-writeback-in-azure-ad-connect">Mot de passe activer l’écriture différée dans Azure Connect d’Active Directory</a> .</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-management-reports-in-the-azure-management-portal"></a>Résoudre les problèmes de rapports de gestion de mot de passe dans le portail de gestion Azure
Si vous rencontrez une erreur lors de l’utilisation des rapports de gestion du mot de passe, vous pourrez peut-être le résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas les rapports de gestion de mot de passe</p>
            </td>
            <td>
              <p>Les rapports <strong>d’activité de réinitialisation de mot de passe</strong> et le <strong>mot de passe réinitialisé l’activité d’enregistrement</strong> ne sont pas visibles dans les rapports du <strong>Journal d’activité</strong> dans l’onglet <strong>rapports</strong> .</p>
            </td>
            <td>
              <p>Cela peut se produire si vous ne disposez pas d’une licence de DAS Premium ou DAS base affectée à l’administrateur d’effectuer cette opération. </p>
              <p>Pour corriger cette erreur, attribuer une licence DAS Premium ou Basic DAS pour le compte d’administrateur en question en accédant à l’onglet <strong>licences</strong> et essayez à nouveau.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Afficher les enregistrements d’utilisateur plusieurs fois</p>
            </td>
            <td>
              <p>Lorsqu’un utilisateur enregistre l’autre application de messagerie, téléphone mobile et les questions de sécurité, ils s’affichent dans des lignes distinctes au lieu d’une seule ligne.</p>
            </td>
            <td>
              <p>Actuellement, lorsqu’un utilisateur enregistre, nous ne pouvons pas supposer qu’ils enregistrent tous les éléments présents sur la page d’inscription. Par conséquent, nous allons nous connecter actuellement chaque élément de données sont enregistrées comme un événement distinct.</p>
              <p>Si vous voulez regrouper ces données, vous pouvez télécharger le rapport et ouvrir les données comme un tableau croisé dynamique dans excel pour avoir plus de souplesse.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Dépanner le portail de l’enregistrement de réinitialisation de mot de passe
Si vous rencontrez une erreur lors de l’enregistrement d’un utilisateur de réinitialisation de mot de passe, vous pourrez peut-être le résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Répertoire n’est pas activée pour la réinitialisation de mot de passe</p>
            </td>
            <td>
              <p>Votre administrateur n’a pas activé vous permet d’utiliser cette fonctionnalité.</p>
            </td>
            <td>
              <p>Basculez l’indicateur <strong>Les utilisateurs activés pour la réinitialisation du mot de passe</strong> à <strong>Oui</strong> , puis cliquez sur <strong>Enregistrer</strong> dans l’onglet configuration du répertoire de portail de gestion Azure. Vous devez disposer d’un Azure AD Premium ou la licence de base affecté à l’administrateur d’effectuer cette opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur ne dispose pas d’une licence de DAS Premium ou Basic DAS attribuée</p>
            </td>
            <td>
              <p>Votre administrateur n’a pas activé vous permet d’utiliser cette fonctionnalité.</p>
            </td>
            <td>
              <p>Attribuer une licence Azure AD Premium ou de base de Azure annonce à l’utilisateur sous l’onglet <strong>licences</strong> dans le portail de gestion Azure. Vous devez disposer d’un Azure AD Premium ou la licence de base affecté à l’administrateur d’effectuer cette opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de traitement de la demande</p>
            </td>
            <td>
              <p>Utilisateur voit une erreur indiquant que :</p>
              <p>

              </p>
              <p>Erreur de traitement de la demande </p>
              <p>Lorsque vous tentez de réinitialiser un mot de passe.</p>
            </td>
            <td>
              <p>Cela peut être dû à de nombreux problèmes, mais en règle générale, cette erreur est due par soit une coupure de courant ou de configuration problème de service qui ne peut pas être résolu. </p>
              <p>Si cette erreur se produit et il est ayant un impact sur votre entreprise, contactez le support technique et nous vous aideront ASAP. Consultez les <a href="#information-to-include-when-you-need-help">informations à inclure lorsque vous avez besoin d’aide</a> pour voir ce que vous devez fournir à l’ingénieur de support pour vous aider à assurer une résolution rapide.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-portal"></a>Résolution des problèmes liés au portail de réinitialisation de mot de passe
Si vous rencontrez une erreur lors de la réinitialisation d’un mot de passe d’un utilisateur, vous pourrez peut-être le résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Répertoire n’est pas activée pour la réinitialisation de mot de passe</p>
            </td>
            <td>
              <p>Votre compte n’est pas activée pour la réinitialisation de mot de passe</p>
              <p>Nous sommes désolés, mais votre administrateur n’a pas configuré votre compte pour une utilisation avec ce service. </p>
              <p>

              </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser votre mot de passe pour vous.</p>
            </td>
            <td>
              <p>Basculez l’indicateur <strong>Les utilisateurs activés pour la réinitialisation du mot de passe</strong> à <strong>Oui</strong> , puis cliquez sur <strong>Enregistrer</strong> dans l’onglet configuration du répertoire de portail de gestion Azure. Vous devez disposer d’un Azure AD Premium ou la licence de base affecté à l’administrateur d’effectuer cette opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur ne dispose pas d’une licence de DAS Premium ou Basic DAS attribuée</p>
            </td>
            <td>
              <p>Alors que nous ne pouvons pas réinitialiser automatiquement les mots de passe de compte non administrateur, nous pouvons contacter l’administrateur de votre organisation à faire pour vous.</p>
            </td>
            <td>
              <p>Attribuer une licence Azure AD Premium ou de base de Azure annonce à l’utilisateur sous l’onglet <strong>licences</strong> dans le portail de gestion Azure. Vous devez disposer d’un Azure AD Premium ou la licence de base affecté à l’administrateur d’effectuer cette opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Annuaire de réinitialisation de mot de passe, mais utilisateur possède les informations d’authentification manquant ou incorrect</p>
            </td>
            <td>
              <p>Votre compte n’est pas activée pour la réinitialisation de mot de passe</p>
              <p>Nous sommes désolés, mais votre administrateur n’a pas configuré votre compte pour une utilisation avec ce service. </p>
              <p>

              </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser votre mot de passe pour vous.</p>
            </td>
            <td>
              <p>Assurez-vous que cet utilisateur a formé correctement les données de contact sur le fichier dans le répertoire avant de continuer. Pour plus d’informations sur la configuration des informations d’authentification dans le répertoire afin que les utilisateurs ne voient pas cette erreur, consultez <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">les données sont utilisées par la réinitialisation du mot de passe</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Annuaire de réinitialisation de mot de passe, mais un utilisateur a uniquement une partie des données de contact sur le fichier lorsque la stratégie est configurée pour exiger des deux étapes de vérification</p>
            </td>
            <td>
              <p>Votre compte n’est pas activée pour la réinitialisation de mot de passe</p>
              <p>Nous sommes désolés, mais votre administrateur n’a pas configuré votre compte pour une utilisation avec ce service. </p>
              <p>

              </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser votre mot de passe pour vous.</p>
            </td>
            <td>
              <p>Avant de continuer, assurez-vous que cet utilisateur dispose d’au moins deux méthodes de contact configurés correctement (par exemple, téléphone portable et téléphone). Pour plus d’informations sur la configuration des informations d’authentification dans le répertoire afin que les utilisateurs ne voient pas cette erreur, consultez <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">les données sont utilisées par la réinitialisation du mot de passe</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory est activé pour la réinitialisation du mot de passe et utilisateur est correctement configuré, mais l’utilisateur est incapable de contacter </p>
            </td>
            <td>
              <p>Rappeler un message  Nous avons rencontré une erreur inattendue lors du contact avec vous.</p>
            </td>
            <td>
              <p>Cela peut être le résultat d’une erreur de service temporaire ou mauvaise configuration des données de contacts qui nous ne pourrions pas détecter correctement. Si l’utilisateur attend 10 secondes, essayez de nouveau et le lien « contacter votre administrateur » s’affiche. Essayez de cliquer de nouveau nouveau distribue l’appel, que vous cliquez sur « Contactez votre administrateur » pour envoyer un e-mail de formulaire d’utilisateur, mot de passe ou des administrateurs globaux (dans cet ordre de priorité) demande un mot de passe réinitialisé à effectuer pour ce compte d’utilisateur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur reçoit jamais la réinitialisation des mots de passe SMS ou un appel téléphonique</p>
            </td>
            <td>
              <p>Clics de l’utilisateur « texte me » ou « call me » et puis reçoit jamais rien.</p>
            </td>
            <td>
              <p>Cela peut être le résultat d’un numéro de téléphone de formulées dans le répertoire. Assurez-vous que le numéro de téléphone est au format « + ccc xxxyyyzzzzXeeee ». Pour plus d’informations sur la mise en forme de téléphone numéros à utiliser avec la réinitialisation de mot de passe voir <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quelles données sont utilisées par la réinitialisation du mot de passe</a>.</p>
              <p>Si vous avez besoin d’une extension de routage pour l’utilisateur en question, notez cette réinitialisation de mot de passe ne gère pas les extensions, même si vous en spécifiez une dans le répertoire (ils sont supprimés avant l’appel de la distribution). Essayez d’utiliser un nombre sans extension, ou de l’intégration de l’extension dans le numéro de téléphone de votre PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur reçoit jamais e-mail de réinitialisation de mot de passe</p>
            </td>
            <td>
              <p>Utilisateur clique sur « M’envoyer un e-mail », puis reçoit jamais rien.</p>
            </td>
            <td>
              <p>La cause la plus courante de ce problème est que le message est rejeté par un filtre de courrier indésirable. Vérifiez votre courrier indésirable, le dossier éléments supprimés ou courrier indésirable de l’e-mail.</p>
              <p>Assurez-vous également que vous voulez vérifier les e-mails pour le message... beaucoup de gens ont des adresses de messagerie très similaires et finissent par vérifier la mauvaise boîte de réception pour le message. Si aucune de ces options ne fonctionne pas, il est également possible que l’adresse de messagerie dans l’annuaire est incorrect, vérifiez pour vous assurer que l’adresse e-mail est celle de droite et essayez à nouveau. Pour plus d’informations sur la mise en forme de messagerie adresses pour une utilisation avec la réinitialisation de mot de passe voir <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quelles données sont utilisées par la réinitialisation du mot de passe</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>J’ai défini une stratégie de réinitialisation de mot de passe, mais lorsqu’un compte d’administrateur utilise la réinitialisation de mot de passe, cette stratégie n’est pas appliquée</p>
            </td>
            <td>
              <p>Comptes Admin réinitialiser leurs mots de passe voir les mêmes options activées pour la réinitialisation de mot de passe, par e-mail et téléphone mobile, quelle que soit la stratégie est définie dans la section <strong>Stratégie de réinitialisation de mot de passe utilisateur</strong> de l’onglet <strong>configurer</strong> .</p>
            </td>
            <td>
              <p>Les options configurées dans la section <strong>Stratégie de réinitialisation de mot de passe utilisateur</strong> de l’onglet <strong>configurer</strong> s’appliquent uniquement aux utilisateurs finaux de votre organisation.</p>
              <p>Microsoft gère et contrôle le mot de passe administrateur réinitialiser stratégie afin de garantir le niveau de sécurité le plus élevé</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur a empêché d’essayer trop de fois par jour de réinitialisation de mot de passe</p>
            </td>
            <td>
              <p>Utilisateur voit une erreur indiquant :</p>
              <p>

              </p>
              <p>Veuillez utiliser une autre option.</p>
              <p>Vous avez essayé de vérifier votre compte trop de fois dans l’heure (s) dernier 1. Pour des raisons de sécurité, vous devrez attendre 24 heures avant d’essayer à nouveau. </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser votre mot de passe pour vous.</p>
            </td>
            <td>
              <p>Nous mettre en œuvre un mécanisme de régulation automatique pour empêcher les utilisateurs d’essayer de réinitialiser leurs mots de passe trop souvent dans une courte période de temps. Ce problème se produit lorsque :</p>
              <ol class="ordered">
                <li>
Utilisateur tente de valider un numéro de téléphone 5 fois en une heure.<br\><br\></li>
                <li>
Utilisateur tente d’utiliser la porte de questions de sécurité 5 fois en une heure.<br\><br\></li>
                <li>
Utilisateur tente de réinitialiser un mot de passe pour le compte d’utilisateur 5 fois en une heure.<br\><br\></li>
              </ol>
              <p>Pour résoudre ce problème, demandez à l’utilisateur d’attendre 24 heures après la dernière tentative, et l’utilisateur sera ensuite en mesure de réinitialiser son mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utilisateur voit une erreur lors de la validation de son numéro de téléphone</p>
            </td>
            <td>
              <p>Lorsque vous essayez de vérifier un téléphone à utiliser en tant que méthode d’authentification, l’utilisateur voit une erreur indiquant :</p>
              <p>

              </p>
              <p>Numéro de téléphone incorrect spécifié.</p>
            </td>
            <td>
              <p>Cette erreur se produit lorsque le numéro de téléphone entré ne correspond pas le numéro de téléphone sur le fichier.</p>
              <p>Assurez-vous que l’utilisateur entre le numéro de téléphone complet, y compris zone et code de pays, lorsque vous tentez d’utiliser une méthode basée sur le téléphone de réinitialisation de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de traitement de la demande</p>
            </td>
            <td>
              <p>Utilisateur voit une erreur indiquant que :</p>
              <p>

              </p>
              <p>Erreur de traitement de la demande </p>
              <p>Lorsque vous tentez de réinitialiser un mot de passe.</p>
            </td>
            <td>
              <p>Cela peut être dû à de nombreux problèmes, mais en règle générale, cette erreur est due par soit une coupure de courant ou de configuration problème de service qui ne peut pas être résolu. </p>
              <p>Si cette erreur se produit et il est ayant un impact sur votre entreprise, contactez le support technique et nous vous aideront ASAP. Consultez les <a href="#information-to-include-when-you-need-help">informations à inclure lorsque vous avez besoin d’aide</a> pour voir ce que vous devez fournir à l’ingénieur de support pour vous aider à assurer une résolution rapide.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback"></a>Résoudre les problèmes de mot de passe d’écriture en différé
Si vous rencontrez une erreur lors de l’activation, la désactivation ou à l’aide de d’écriture différée de mot de passe, vous pourrez peut-être le résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Quel message d’erreur un utilisateur peut-il consulter ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Échecs de démarrage et intégration de la comptabilité</p>
            </td>
            <td>
              <p>Service de réinitialisation de mot de passe ne démarre pas dans les locaux avec erreur 6800 dans le journal des événements applications Azure AD connecter l’ordinateur.</p>
              <p>

              </p>
              <p>Après l’intégration, fédérés ou le hachage de mot de passe des utilisateurs synchronisés ne peut pas réinitialiser leur mot de passe.</p>
            </td>
            <td>
              <p>Lors de l’écriture différée de mot de passe est activée, le moteur de synchronisation appelle la bibliothèque de d’écriture différée pour réaliser la configuration (intégration) en contactant le service d’intégration de nuage. Les erreurs rencontrées lors de l’intégration ou lors du démarrage du point de terminaison WCF pour l’écriture différée de mot de passe va générer des erreurs dans le journal des événements dans le journal des événements de votre ordinateur Azure Connect d’Active Directory.</p>
              <p>Lors du redémarrage du service de ADSync, si l’écriture différée a été configurée, le point de terminaison WCF va être démarré. Toutefois, en cas d’échec de démarrage du point de terminaison, nous simplement enregistrer l’événement 6800 et permettre le démarrage du service de synchronisation. La présence de cet événement signifie que l’écriture différée mot de passe du point de terminaison n’a pas commencé jusqu'à. Détails du journal des événements pour cet événement (6800) ainsi que les entrées du journal des événements génèrent par PasswordResetService composant indique pourquoi le point de terminaison n’a pas pu démarrer. Passez en revue ces erreurs dans le journal des événements et essayez de relancer la connexion AD Azure si l’écriture différée de mot de passe ne fonctionne toujours pas. Si le problème persiste, essayez de désactiver et de réactiver la fonctionnalité d’écriture différée de mot de passe.</p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Lorsqu’un utilisateur tente de réinitialiser un mot de passe ou le déverrouillage d’un compte avec l’écriture différée de mot de passe activée, l’opération échoue. En outre, vous voyez un événement dans le journal des événements d’Azure Connect de publicité contenant : « moteur de synchronisation a renvoyé une erreur hr = 800700CE, message = le nom du fichier ou l’extension est trop longue » après l’opération de déverrouillage.
                            </p>
            </td>
            <td>
              <p>Cela peut se produire si vous avez mis à niveau d’anciennes versions d’Azure Connect de publicité ou de la synchronisation d’annuaire. Mise à niveau vers des versions plus anciennes d’Azure Connect de AD de définir un mot de passe pour le compte de l’Agent de gestion AD Azure (versions plus récentes définit un mot de passe de longueur de 127 caractères) 254 caractères. Ces mots de passe longs fonctionnent pour les opérations d’importation du connecteur Active Directory et d’exportation, mais ils ne sont pas pris en charge par l’opération de déverrouillage.
                            </p>
            </td>
            <td>
              <p>[Recherchez le compte Active Directory](active-directory-aadconnect-accounts-permissions.md#active-directory-account) pour Azure Connect de publicité et de réinitialiser le mot de passe contenir pas plus de 127 caractères. Ouvrez ensuite le **Service de synchronisation** dans le menu Démarrer. Accédez à **connecteurs** et trouver le **Connecteur Active Directory**. Sélectionnez-le, puis cliquez sur **Propriétés**. Accédez à la page **d’informations d’identification** et entrez le nouveau mot de passe. Cliquez sur **OK** pour fermer la page.
                            </p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Erreur de configuration en écriture différée lors de l’installation d’Azure Connect d’Active Directory.</p>
            </td>
            <td>
              <p>À la dernière étape du processus d’installation Azure Connect d’Active Directory, vous voyez une erreur indiquant que l’écriture différée de mot de passe ne peut pas être configurée.</p>
              <p>

              </p>
              <p>Le journal des événements Application de connexion AD Azure contient erreur 32009 avec le texte « Erreur d’obtention auth token ».</p>
            </td>
            <td>
              <p>Cette erreur se produit dans les deux cas suivants :</p>
              <ul>
                <li class="unordered">
Vous avez spécifié un mot de passe pour le compte d’administrateur global spécifié au début du processus d’installation Azure Connect d’Active Directory.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Vous avez tenté d’utiliser un utilisateur fédéré pour le compte d’administrateur global spécifié au début du processus d’installation Azure Connect d’Active Directory.<br\><br\></li>
              </ul>
              <p>Pour corriger cette erreur, vérifiez que vous n’êtes pas à l’aide d’un compte fédéré pour l’administrateur global que vous avez spécifié au début de la procédure d’installation Azure Connect de publicité, et que le mot de passe spécifié est correct.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de configuration en écriture différée lors de l’installation d’Azure Connect d’Active Directory.</p>
            </td>
            <td>
              <p>Le journal des événements machine Azure Connect de publicité contient erreur 32002 levée par le PasswordResetService.</p>
              <p>

              </p>
              <p>Le message d’erreur : « erreur de connexion à ServiceBus, le fournisseur de jetons n’a pas pu fournir un jeton de sécurité... »</p>
              <p>

              </p>
            </td>
            <td>
              <p>La cause de cette erreur est que le service de réinitialisation de mot de passe dans votre environnement local n’est pas en mesure de se connecter au point de terminaison du bus de service dans le nuage. Cette erreur est normalement normalement provoquée par une règle de pare-feu bloquant une connexion sortante vers une adresse web ou de port particulière.</p>
              <p>

              </p>
              <p>Assurez-vous que votre pare-feu autorise les connexions sortantes pour les éléments suivants :</p>
              <ul>
                <li class="unordered">
Tout le trafic via TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Pour les connexions sortantes <br\><br\></li>
              </ul>
              <p>

              </p>
              <p>Une fois que vous avez mis à jour de ces règles, redémarrer l’ordinateur Azure Connect d’Active Directory et l’écriture différée de mot de passe doit commencer à travailler à nouveau.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Mot de passe d’écriture différée point de terminaison sur-prem n’est pas accessible</p>
            </td>
            <td>
              <p>Après avoir travaillé pour certains temps, fédérés ou le hachage de mot de passe des utilisateurs synchronisés ne peut pas réinitialiser leur mot de passe.</p>
            </td>
            <td>
              <p>Dans de rares cas, le service d’écriture différée de mot de passe peut ne pas redémarrer lorsque Azure AD Connect a redémarré. Dans ce cas, vérifiez d’abord si l’écriture différée de mot de passe semble être activée sur montant Pour ce faire l’aide Azure Connect de publicité Assistant ou powershell (voir les HowTos section ci-dessus). Si cette fonctionnalité semble être activé, essayez de l’activation ou la désactivation de la fonctionnalité à nouveau soit par le biais de l’interface utilisateur ou PowerShell. Voir « étape 2 : activer l’écriture différée de mot de passe sur votre ordinateur de la synchronisation d’annuaire &amp; configurer des règles de pare-feu « <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">Comment faire pour activer/désactiver l’écriture différée de mot de passe</a> pour plus d’informations sur la procédure à suivre.</p>
              <p>

              </p>
              <p>Si cela ne fonctionne pas, essayez de désinstaller complètement réinstaller Azure Connect d’Active Directory.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreurs d’autorisations</p>
            </td>
            <td>
              <p>Fédéré ou mot de passe hachage synchronisés les utilisateurs qui tentent de la réinitialisation de leur mots de passe de voir une erreur après avoir soumis le mot de passe qui indique un problème de service.</p>
              <p>

              </p>
              <p>En outre, au cours des opérations de réinitialisation de mot de passe, vous verrez peut-être qu'un message d’erreur concernant l’agent de gestion a été refusé dans vos journaux d’événements locaux sur.</p>
            </td>
            <td>
              <p>Si vous constatez ces erreurs dans votre journal des événements, vérifiez que le compte d’agent de gestion AD (qui a été spécifié au moment de la configuration dans l’Assistant) dispose des autorisations nécessaires pour l’écriture différée de mot de passe.</p>
              <p>

              </p>
              <p>Remarque une fois que cette autorisation est accordée peut prendre jusqu'à 1 heure pour les autorisations diffusée via la tâche d’arrière-plan sdprop sur le contrôleur de domaine. </p>
              <p>Mot de passe réinitialisé pour fonctionner, l’autorisation doit être apposé sur le descripteur de sécurité de l’objet utilisateur dont mot de passe est réinitialisé. Jusqu'à ce que cette autorisation s’affiche sur l’objet utilisateur, réinitialisation de mot de passe continuera d’échouer avec accès refusé.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur lors de la configuration d’écriture différée de mot de passe à partir de l’Assistant de configuration Azure Connect de publicité </p>
            </td>
            <td>
              <p>Erreur « Impossible de trouver MA » dans l’Assistant lors de l’activation/désactivation de l’écriture différée de mot de passe</p>
            </td>
            <td>
              <p>Il existe un problème connu dans la version finale d’Azure Connect AD qui se manifeste dans la situation suivante :</p>
              <ol class="ordered">
                <li>
Vous configurez Azure Connect AD pour abc.com de clients (domaine vérifié) à l’aide de réf.. Cela se traduit par un connecteur DAS avec le nom « abc.com – DAS » en cours de création.<br\><br\></li>
                <li>
Puis, vous modifiez puis la Réf. DAS pour le connecteur (à l’aide de la synchronisation de l’ancien l’interface utilisateur) pour (Notez qu’il est le même client mais le nom de domaine différent). <br\><br\></li>
                <li>
Vous essayez à présent activer ou désactiver l’écriture différée de mot de passe. L’Assistant créer le nom du connecteur à l’aide de la Réf., comme « abc.onmicrosoft.com – DAS » et passer à l’applet de commande d’écriture différée de mot de passe. Une erreur se produit car il n’existe aucun connecteur créé avec ce nom.<br\><br\></li>
              </ol>
              <p>Ceci a été corrigé dans nos versions les plus récentes. Si vous possédez une version plus ancienne, l’une solution consiste à utiliser l’applet de commande powershell pour activer ou désactiver la fonctionnalité. Voir « étape 2 : activer l’écriture différée de mot de passe sur votre ordinateur de la synchronisation d’annuaire &amp; configurer des règles de pare-feu « <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">Comment faire pour activer/désactiver l’écriture différée de mot de passe</a> pour plus d’informations sur la procédure à suivre.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Impossible de réinitialiser le mot de passe pour les utilisateurs dans des groupes spéciaux, tels qu’Admins du domaine / etc. des administrateurs de l’entreprise.</p>
            </td>
            <td>
              <p>Fédéré ou mot de passe hachage synchronisés aux utilisateurs qui font partie de groupes protégés et la tentent de réinitialisation de leur mots de passe de voir une erreur après avoir soumis le mot de passe qui indique un problème de service.</p>
            </td>
            <td>
              <p>Les utilisateurs privilégiés dans Active Directory sont protégés à l’aide de AdminSDHolder. Consultez <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> pour plus de détails. </p>
              <p>

              </p>
              <p>Cela signifie que les descripteurs de sécurité sur ces objets sont périodiquement vérifiées correspondant à l’objet AdminSDHolder de spécifié dans un et sont réinitialisés si elles sont différentes. Par conséquent, les autorisations supplémentaires qui sont nécessaires pour l’écriture différée de mot de passe écouler pas à ces utilisateurs. Cela peut entraîner l’écriture différée de mot de passe ne fonctionne ne pas pour ces utilisateurs. Par conséquent, nous ne gèrent pas gestion des mots de passe pour les utilisateurs au sein de ces groupes car il rompt le modèle de sécurité d’Active Directory.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Échec d’opérations de réinitialisation avec l’objet n’a pas pu être trouvé.</p>
            </td>
            <td>
              <p>Fédéré ou mot de passe hachage synchronisés les utilisateurs qui tentent de la réinitialisation de leur mots de passe de voir une erreur après avoir soumis le mot de passe qui indique un problème de service.</p>
              <p>

              </p>
              <p>En outre, au cours des opérations de réinitialisation de mot de passe, vous pouvez observer une erreur dans vos journaux d’événements du service d’Azure AD Connect indiquant une erreur « Objet n’a pas pu être trouvé ».</p>
            </td>
            <td>
              <p>Cette erreur indique généralement que le moteur de synchronisation ne peut pas trouver l’objet utilisateur dans l’espace de connecteur DAS ou la MV lié ou un objet espace de connecteur Active Directory. </p>
              <p>

              </p>
              <p>Pour résoudre ce problème, assurez-vous que l’utilisateur est en effet synchronisé à partir de prem à DAS par l’instance actuelle de Azure AD Connect et examiner l’état des objets dans l’espace de connecteur et de MV. Vérifiez que l’objet AD CS est le connecteur à l’objet MV via la règle « Microsoft.InfromADUserAccountEnabled.xxx ».</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Échec d’opérations de réinitialisation avec plusieurs correspondances trouvé l’erreur</p>
            </td>
            <td>
              <p>Fédéré ou mot de passe hachage synchronisés les utilisateurs qui tentent de la réinitialisation de leur mots de passe de voir une erreur après avoir soumis le mot de passe qui indique un problème de service.</p>
              <p>

              </p>
              <p>En outre, pendant les opérations de réinitialisation de mot de passe, vous pouvez observer une erreur dans vos journaux d’événements du service Azure AD Connect indiquant une erreur « Plusieurs maches trouvés ».</p>
            </td>
            <td>
              <p>Cela indique que le moteur de synchronisation a détecté que l’objet MV est connecté à plusieurs objets AD CS via le « Microsoft.InfromADUserAccountEnabled.xxx ». Cela signifie que l’utilisateur a un compte activé dans plus d’une forêt. </p>
              <p>

              </p>
              <p>Ce scénario n’est actuellement pas pris en charge pour l’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Opérations de mot de passe échouent avec une erreur de configuration.</p>
            </td>
            <td>
              <p>Opérations de mot de passe échouent avec une erreur de configuration. Le journal des événements applications contient l’erreur d’Azure AD Connect 6329 avec du texte : 0x8023061f (l’opération a échoué parce que la synchronisation de mot de passe n’est pas activée sur cet Agent de gestion).</p>
            </td>
            <td>
              <p>Cela se produit si la configuration Azure AD Connect est modifiée pour ajouter&nbsp;une nouvelle forêt Active Directory (ou à supprimer et rajouter une forêt existante) <strong>une fois</strong> la fonctionnalité d’écriture différée de mot de passe a déjà été activée. Opérations de mot de passe pour les utilisateurs de ces forêts nouvellement ajoutés échoue. Pour résoudre le problème, désactivez et réactivez la fonctionnalité d’écriture différée de mot de passe une fois terminées les modifications de configuration de la forêt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Réécriture des mots de passe ont été correctement réinitialisés par works d’utilisateurs, mais la réécriture des mots de passe modifiés par un utilisateur ou d’échec de la réinitialisation d’un utilisateur par un administrateur.</p>
            </td>
            <td>
              <p>Lorsque vous tentez de réinitialiser un mot de passe pour le compte d’un utilisateur à partir du portail de gestion Azure, vous voyez un message indiquant : « le service de réinitialisation de mot de passe dans votre environnement local ne gère pas les administrateurs de la réinitialisation des mots de passe utilisateur. Mettez à niveau vers la dernière version de Azure Connect AD pour résoudre ce problème. »</p>
            </td>
            <td>
              <p>Cela se produit lorsque la version du moteur de synchronisation ne prend pas en charge l’opération d’écriture différée de mot de passe particulier qui a été utilisée. Les versions d’Azure Connect de publicité plus tard 1.0.0419.0911 prend en charge toutes les opérations de gestion de mot de passe, y compris le mot de passe réinitialisé d’écriture différée, l’écriture différée de modification de mot de passe et l’écriture différée de réinitialiser le mot de passe lancées par l’administrateur à partir du portail de gestion Azure.&nbsp; Versions de DirSync ultérieure à 1.0.6862 prennent en charge différée de réinitialisation de mot de passe uniquement. Pour résoudre ce problème, il est vivement recommandé d’installer la version la plus récente d’Azure Connect de publicité ou d’Azure Active Directory se connecter (pour plus d’informations, consultez <a href="active-directory-aadconnect">Outils d’intégration de répertoire</a>) pour résoudre ce problème et pour tirer le meilleur parti d’écriture différée de mot de passe de votre organisation.</p>
            </td>
          </tr>
        </tbody></table>


## <a name="password-writeback-event-log-error-codes"></a>Codes d’erreur de mot de passe d’écriture différée journal des événements
Meilleures pratiques lors de la résolution des problèmes d’écriture différée de mot de passe sont d’inspecter ce journal des événements applications sur votre ordinateur d’Azure Connect d’Active Directory. Ce journal des événements contiendra des événements à partir de deux sources d’intérêt pour l’écriture différée de mot de passe. La source de PasswordResetService décrit les opérations et les problèmes liés à l’opération d’écriture différée de mot de passe. La source ADSync décrira les opérations et les problèmes liés à la définition de mots de passe dans votre environnement Active Directory.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Code</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Nom / Message</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Source</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Description</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>SORTIE : MMS(4924) 0x80230619 – « une restriction empêche le mot de passe en cours de modification pour l’objet actuel spécifié. »</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Cet événement se produit lorsque le service de l’écriture différée de mot de passe essaie de définir un mot de passe dans votre répertoire local qui ne répond pas à l’âge de mot de passe, l’historique, la complexité ou condition de filtrage du domaine.</p>
              <ul>
                <li class="unordered">
Si vous avez un âge minimal de mot de passe et que vous avez récemment changé le mot de passe dans cette fenêtre de temps, vous ne serez pas en mesure de modifier le mot de passe à nouveau, jusqu'à ce qu’il atteigne l’âge spécifié dans votre domaine. À des fins de test, l’âge minimal doit être définie à 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez des exigences de l’historique des mots de passe activées, vous devez alors sélectionner un mot de passe n’a pas été utilisée dans les dernières fois de N, où N est le paramètre de l’historique du mot de passe. Si vous ne sélectionnez pas un mot de passe qui a été utilisée dans les dernier N fois, puis vous verrez un échec dans ce cas. À des fins de test, l’historique doit être définie à 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez des exigences de complexité de mot de passe, chacun d'entre eux sera appliquée lorsque l’utilisateur essaie de modifier ou réinitialiser un mot de passe.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez activés des filtres de mot de passe, et si un utilisateur sélectionne un mot de passe ne pas répondre aux critères de filtrage, puis la réinitialisation ou modifier l’opération échouera.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>RESSOURCES HUMAINES 8023042</p>
            </td>
            <td>
              <p>Moteur de synchronisation a renvoyé une erreur hr = 80230402, message = tentative d’obtention d’un objet a échoué car il y a des entrées dupliquées avec le même ancrage</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Cet événement se produit lorsque le même id utilisateur est activé dans plusieurs domaines.  Par exemple, si vous synchronisez les forêts compte/ressource et avez le même id utilisateur présent et activé dans chacun, cette erreur peut se produire.  </p>
              <p>Cette erreur peut également se produire si vous utilisez un attribut d’ancrage non unique (comme un alias ou un nom UPN) et deux utilisateurs partagent le même attribut d’ancrage.</p>
              <p>Pour résoudre ce problème, assurez-vous que vous ne disposez pas de n’importe quel utilisateur en double dans vos domaines et que vous utilisez un attribut d’ancrage unique pour chaque utilisateur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service sur site détecté demande pour un fédéré de réinitialisation de mot de passe ou le mot de passe hachage synchronisés utilisateur d’origine à partir du cloud. Cet événement est que le premier événement dans chaque mot de passe réinitialiser l’opération d’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur a sélectionné un nouveau mot de passe au cours d’une opération de réinitialisation de mot de passe, nous avons déterminé que ce mot de passe répond aux exigences de mot de passe d’entreprise, et ce mot de passe a été correctement réécrites dans l’environnement Active Directory local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur a sélectionné un mot de passe et que le mot de passe est arrivé avec succès à l’environnement local, mais lorsque nous avons tenté de définir le mot de passe dans l’environnement Active Directory local, une erreur s’est produite. Cela peut se produire pour plusieurs raisons :</p>
              <ul>
                <li class="unordered">
Le mot de passe utilisateur ne pas répondre à l’âge, l’historique, la complexité ou filtrer les exigences pour le domaine. Essayez un mot de passe totalement nouveau pour résoudre ce problème.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte de service de MA n’a pas les autorisations appropriées pour définir le nouveau mot de passe du compte d’utilisateur en question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte d’utilisateur est dans un groupe protégé, tels que les administrateurs de domaine ou d’entreprise, qui n’autorise pas les opérations de jeu de mot de passe.<br\><br\></li>
              </ul>
              <p>Consultez l' <a href="#troubleshoot-password-writeback">Écriture différée de résoudre les problèmes de mot de passe</a> pour en savoir plus sur les autres situtions peuvent provoquer cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement se produit si vous activez l’écriture différée de mot de passe avec Azure AD Connect et indique que nous avons commencé arrivant à votre organisation pour le service web d’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique le processus d’intégration a réussi et que la fonctionnalité d’écriture différée de mot de passe est prête à utiliser.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service sur site a détecté une demande de modification de mot de passe pour un fédérés ou synchronisation de hachage de mot de passe d utilisateur d’origine à partir du cloud. Cet événement est le premier événement dans chaque opération d’écriture différée de modification du mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur a sélectionné un nouveau mot de passe au cours d’une opération de changement de mot de passe, nous avons déterminé que ce mot de passe répond aux exigences de mot de passe d’entreprise, et ce mot de passe a été correctement réécrites dans l’environnement Active Directory local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur a sélectionné un mot de passe et que le mot de passe est arrivé avec succès à l’environnement local, mais lorsque nous avons tenté de définir le mot de passe dans l’environnement Active Directory local, une erreur s’est produite. Cela peut se produire pour plusieurs raisons :</p>
              <ul>
                <li class="unordered">
Le mot de passe utilisateur ne pas répondre à l’âge, l’historique, la complexité ou filtrer les exigences pour le domaine. Essayez un mot de passe totalement nouveau pour résoudre ce problème.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte de service de MA n’a pas les autorisations appropriées pour définir le nouveau mot de passe du compte d’utilisateur en question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte d’utilisateur est dans un groupe protégé, tels que les administrateurs de domaine ou d’entreprise, qui n’autorise pas les opérations de jeu de mot de passe.<br\><br\></li>
              </ul>
              <p>Consultez l' <a href="#troubleshoot-password-writeback">Écriture différée de résoudre les problèmes de mot de passe</a> pour en savoir plus sur les autres situations peuvent provoquer cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Le service sur site détecté la demande pour un fédéré de réinitialisation de mot de passe ou le mot de passe hachage synchronisés utilisateur provenant de l’administrateur pour le compte d’un utilisateur. Cet événement est le premier événement dans chaque opération d’écriture différée de réinitialiser le mot de passe initiées par l’administrateur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L’administrateur a sélectionné un nouveau mot de passe lors d’une opération de réinitialisation de mot de passe initiées par l’administrateur, nous avons déterminé que ce mot de passe répond aux exigences de mot de passe d’entreprise, et ce mot de passe a été correctement réécrites dans l’environnement Active Directory local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L’administrateur a sélectionné un mot de passe pour le compte d’un utilisateur et ce mot de passe est arrivé avec succès à l’environnement local, mais lorsque nous avons tenté de définir le mot de passe dans l’environnement Active Directory local, une erreur s’est produite. Cela peut se produire pour plusieurs raisons :</p>
              <ul>
                <li class="unordered">
Le mot de passe utilisateur ne pas répondre à l’âge, l’historique, la complexité ou filtrer les exigences pour le domaine. Essayez un mot de passe totalement nouveau pour résoudre ce problème.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte de service de MA n’a pas les autorisations appropriées pour définir le nouveau mot de passe du compte d’utilisateur en question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Le compte d’utilisateur est dans un groupe protégé, tels que les administrateurs de domaine ou d’entreprise, qui n’autorise pas les opérations de jeu de mot de passe.<br\><br\></li>
              </ul>
              <p>Consultez l' <a href="#troubleshoot-password-writeback">Écriture différée de résoudre les problèmes de mot de passe</a> pour en savoir plus sur les autres situtions peuvent provoquer cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement se produit si vous désactivez l’écriture différée de mot de passe avec Azure AD Connect et indique que nous avons commencé à offboarding votre organisation pour le service web d’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique le processus offboarding a réussi et que les capacités d’écriture différée de mot de passe a été correctement désactivée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le processus d’offboarding n’a pas réussi. Il peut s’agir d’une erreur d’autorisations sur le compte administrateur du nuage ou sur site spécifié lors de la configuration, ou parce que vous essayez d’utiliser un administrateur global nuage fédérés lors de la désactivation d’écriture différée de mot de passe. Pour résoudre ce problème, vérifiez votre administration des autorisations et que vous n’utilisez aucune fédéré compte lors de la configuration de la fonctionnalité d’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015 date</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service de l’écriture différée de mot de passe a démarré et qu’il est prêt à accepter des demandes de gestion de mot de passe à partir du cloud.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service de l’écriture différée de mot de passe s’est arrêté et que les demandes de gestion de mot de passe dans le nuage ne sera pas réussies.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons récupéré un jeton d’autorisation de l’administrateur global spécifié pendant l’installation de Azure AD Connect pour démarrer le processus d’intégration ou d’offboarding.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons créé avec succès la clé de cryptage de mot de passe qui sera utilisée pour crypter les mots de passe à partir du cloud à envoyer à votre environnement sur site.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une erreur inconnue lors d’une opération de gestion de mot de passe. Examinez le texte de l’exception dans l’événement pour plus de détails. Si vous rencontrez des problèmes, essayez de la désactivation et la réactivation d’écriture différée de mot de passe. Si cela ne résout pas le problème, inclure une copie de votre journal des événements avec l’id de suivi spécifié insider à l’ingénieur de support.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>Constatez </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une erreur de connexion au mot de passe nuage réinitialiser le service et se produit généralement lorsque le service sur site n’a pas pu se connecter au service web de réinitialisation de mot de passe. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une erreur de connexion à l’instance de bus de service de vos clients. Cela peut se produire car vous bloquez les connexions sortantes dans votre environnement local. Vérifiez votre pare-feu afin de vous autoriser les connexions via TCP 443 et <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>et essayez à nouveau. Si vous rencontrez toujours des problèmes, essayez de désactiver et réactiver l’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que l’entrée passée à nos API de service web n’est pas valide. Recommencez l’opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’il y a une erreur de décryptage du mot de passe est arrivé à partir du cloud. Cela peut provenir d’une incompatibilité de clé de décryptage entre le service en nuage et votre environnement local. Pour résoudre ce problème, activer et désactiver l’écriture différée de mot de passe dans votre environnement local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Au cours de l’intégration, nous allons enregistrer les informations spécifiques au client dans un fichier de configuration dans votre environnement local. Cet événement indique une erreur d’enregistrement de ce fichier s’est produite ou que lorsque le service a été démarré il y a une erreur de lecture du fichier. Pour résoudre ce problème, essayez la désactivation et la réactivation d’écriture différée de mot de passe pour forcer la ré-écriture de ce fichier de configuration. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ : cet événement n’est pas présent dans Azure Connect de publicité, seulement très tôt les versions de synchronisation d’annuaire pris en charge de l’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Au cours de l’intégration, nous envoyer des données dans le nuage pour le mot de passe local réinitialiser le service. Que les données sont ensuite écrites dans un fichier en mémoire avant d’être envoyées au service de synchronisation pour stocker ces informations de manière sécurisée sur le disque. Cet événement indique un problème lié à l’écriture ou la mise à jour des données dans la mémoire. Pour résoudre ce problème, essayez la désactivation et la réactivation d’écriture différée de mot de passe pour forcer une réécriture de cette configuration.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons reçu une réponse non valide à partir du service web de réinitialisation de mot de passe. Pour résoudre ce problème, essayez la désactivation et la réactivation d’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous ne pouvions pas obtenir une autorisation de jeton pour le compte d’administrateur global spécifié lors de l’installation d’Azure Connect d’AD. Cette erreur peut être provoquée par un nom d’utilisateur incorrect ou mot de passe spécifié pour le compte d’administrateur global ou parce que le compte d’administrateur global spécifié est fédéré. Pour résoudre ce problème, exécutez à nouveau la configuration avec le nom d’utilisateur correct et un mot de passe et assurez-vous que l’administrateur est géré (cloud uniquement ou synchronisation de mot de passe) compte.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une erreur s’est produite lors de la génération du mot de passe de clé de chiffrement ou de déchiffrement de mot de passe arrive à partir du service de cloud. Cette erreur probablement indique un problème avec votre environnement. Examinez les détails de votre journal des événements pour en savoir plus et résoudre ce problème. Vous pouvez également essayer de désactiver et réactiver le service de l’écriture différée de mot de passe pour résoudre ce problème.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service sur site ne peut pas communiquer correctement avec le service de web de réinitialisation de mot de passe pour lancer le processus d’intégration. Ceci peut venir d’une règle de pare-feu ou d’un problème d’obtention d’un jeton d’authentification pour vos clients. Pour résoudre ce problème, vérifiez que ne pas bloquer les connexions sortantes via TCP 443 et TCP 9350-9354 ou <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, et que le DAS admin compte vous utilisez pour intégré n’est pas fédéré. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ : cet événement n’est pas présent dans Azure Connect de publicité, seulement très tôt les versions de synchronisation d’annuaire pris en charge de l’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service sur site ne peut pas communiquer correctement avec le service de web de réinitialisation de mot de passe pour lancer le processus d’offboarding. Ceci peut venir d’une règle de pare-feu ou d’un problème d’obtention d’un jeton d’autorisation pour vos clients. Pour résoudre ce problème, vérifiez que ne pas bloquer les connexions sortantes sur 443 ou sur <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, et que le compte DAS d’administrateur que vous utilisez pour hors cote n’est pas fédéré. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avions réessayer de vous connecter à une instance de bus de service de vos clients. Dans des conditions normales, cela ne doit pas constituer un problème, mais si vous voyez cet événement plusieurs fois, prenez soin de vérifier votre connexion réseau à un bus de service, surtout si elle est une latence élevée ou une connexion à faible bande passante.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Pour surveiller le fonctionnement de votre service d’écriture différée de mot de passe, nous envoyer les données pulsation à notre mot de passe réinitialiser service web toutes les 5 minutes. Cet événement indique qu’une erreur s’est produite lors de l’envoi de ces informations d’état dans le service web de nuage. Ces informations de santé n’incluent pas une OII ou informations d’identification personnelles des données et sont purement une pulsation et les statistiques du service de base afin que nous pouvons fournir des informations d’état de service dans le nuage.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’une erreur inconnue renvoyée par Active Directory, consultez le journal d’événements de serveur Azure Connect de publicité pour les événements de la source de ADSync pour plus d’informations sur cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que l’utilisateur qui tente de réinitialiser ou de modifier un mot de passe est introuvable dans le répertoire local. Cela peut se produire lorsque l’utilisateur a été supprimé sur site, mais pas dans le cloud, ou s’il existe un problème avec la synchronisation. Vérifiez les journaux de synchronisation, ainsi que la dernière synchronisation quelques détails pour plus d’informations de l’exécution.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Lors de la réinitialisation d’un mot de passe ou la demande de modification est à l’origine du nuage, nous utilisons le point d’ancrage de nuage spécifié pendant l’installation d’Azure Connect de publicité pour déterminer comment lier cette demande à un utilisateur dans votre environnement local. Cet événement indique que nous avons trouvé des deux utilisateurs dans votre répertoire local avec le même attribut de point d’ancrage de nuage. Vérifiez les journaux de synchronisation, ainsi que la dernière synchronisation quelques détails pour plus d’informations de l’exécution.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le compte de service de l’Agent de gestion ne dispose pas des autorisations appropriées sur le compte en question, pour définir un nouveau mot de passe. Assurez-vous que le compte de MA dans la forêt de l’utilisateur dispose des autorisations de mot de passe de réinitialisation et la modification sur tous les objets de la forêt.  Pour plus d’informations sur la façon de faire à cet option, voir <a href="active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions">étape 4 : définissez les autorisations Active Directory appropriées</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons tenté de réinitialiser ou modifier un mot de passe d’un compte qui a été désactivé dans les locaux de. Activer le compte et recommencez l’opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Événement indique que nous avons tenté de réinitialiser ou modifier un mot de passe d’un compte qui a été verrouillé dans les locaux. Verrouillage peut se produire lorsqu’un utilisateur a essayé d’une modification ou la réinitialisation de mot de passe trop de fois sur une courte période. Déverrouiller le compte et recommencez l’opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que l’utilisateur a spécifié un mot de passe actuel lors de l’opération de modification de réalisation d’un mot de passe. Spécifier le mot de passe en cours et essayez à nouveau.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement se produit lorsque le service de l’écriture différée de mot de passe essaie de définir un mot de passe dans votre répertoire local qui ne répond pas à l’âge de mot de passe, l’historique, la complexité ou condition de filtrage du domaine.</p>
              <ul>
                <li class="unordered">
Si vous avez un âge minimal de mot de passe et que vous avez récemment changé le mot de passe dans cette fenêtre de temps, vous ne serez pas en mesure de modifier le mot de passe à nouveau, jusqu'à ce qu’il atteigne l’âge spécifié dans votre domaine. À des fins de test, l’âge minimal doit être définie à 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez des exigences de l’historique des mots de passe activées, vous devez alors sélectionner un mot de passe n’a pas été utilisée dans les dernières fois de N, où N est le paramètre de l’historique du mot de passe. Si vous ne sélectionnez pas un mot de passe qui a été utilisée dans les dernier N fois, puis vous verrez un échec dans ce cas. À des fins de test, l’historique doit être définie à 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez des exigences de complexité de mot de passe, chacun d'entre eux sera appliquée lorsque l’utilisateur essaie de modifier ou réinitialiser un mot de passe.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si vous avez activés des filtres de mot de passe, et si un utilisateur sélectionne un mot de passe ne pas répondre aux critères de filtrage, puis la réinitialisation ou modifier l’opération échouera.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une écriture du problème précédent d’un mot de passe dans votre répertoire local en raison d’un problème de configuration avec Active Directory. Vérifiez le journal des événements applications Azure AD connecter l’ordinateur pour les messages du service ADSync pour plus d’informations sur l’erreur qui s’est produite. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ : cet événement n’est pas présent dans Azure Connect de publicité, seulement très tôt les versions de synchronisation d’annuaire pris en charge de l’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ : cet événement n’est pas présent dans Azure Connect de publicité, seulement très tôt les versions de synchronisation d’annuaire pris en charge de l’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ : cet événement n’est pas présent dans Azure Connect de publicité, seulement très tôt les versions de synchronisation d’annuaire pris en charge de l’écriture différée.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback-connectivity"></a>Dépannage de la connectivité de l’écriture différée de mot de passe

Si vous rencontrez des interruptions de service avec le composant d’écriture différée de mot de passe d’Azure Connect d’Active Directory, voici quelques étapes rapides, que vous pouvez prendre pour résoudre ce problème :

 - [Redémarrer la publicité Azure se connecter le Service de synchronisation](#restart-the-azure-AD-Connect-sync-service)
 - [Désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe](#disable-and-re-enable-the-password-writeback-feature)
 - [Installer la dernière version d’Azure Connect de publicité](#install-the-latest-azure-ad-connect-release)
 - [Résoudre les problèmes de mot de passe d’écriture en différé](#troubleshoot-password-writeback)

En général, nous recommandons que vous exécutez ces étapes dans l’ordre ci-dessus afin de récupérer votre service de la manière la plus rapide.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Redémarrer la publicité Azure se connecter le Service de synchronisation
Le redémarrage du Service de synchronisation se connectent AD Azure peut aider à résoudre les problèmes de connectivité ou d’autres problèmes temporaires avec le service.

 1. En tant qu’administrateur, cliquez sur **Démarrer** sur le serveur exécutant **Azure Connect d’Active Directory**.
 2. Entrez **« services.msc »** dans la zone Rechercher, puis appuyez sur **entrée**.
 3. Recherchez l’entrée de **connexion d’annonces Microsoft Azure** .
 4. Avec le bouton droit sur l’entrée service et cliquez sur **redémarrer**, attendez que l’opération se termine.

    ![][002]

Ces étapes seront rétablir votre connexion avec le service de cloud et résoudre toute interruption, que vous pouvez être confronté.  Si le redémarrage du Service de synchronisation ne résout pas votre problème, nous vous recommandons d’essayer de désactiver et de réactiver la fonctionnalité d’écriture différée de mot de passe sous la forme d’une étape suivante.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe
Désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe peuvent aider à résoudre les problèmes de connectivité.

 1. En tant qu’administrateur, ouvrez l' **Assistant de configuration Azure Connect d’Active Directory**.
 2. Dans la boîte de dialogue **se connecter à Active Directory Azure** , entrez vos **informations d’identification administrateur global de publicité Azure**
 3. Dans la boîte de dialogue **se connecter au service d’annuaire Active Directory** , entrez vos **informations d’identification administrateur de Services de domaine Active Directory**.
 4. Dans la boîte de dialogue **qui identifie de manière unique les utilisateurs** , cliquez sur le bouton **suivant** .
 5. Dans la boîte de dialogue **Options** , décochez la case de **l’écriture différée de mot de passe** .

    ![][003]

 6. Cliquez sur **suivant** dans les pages restantes de la boîte de dialogue sans apporter aucune modification jusqu'à ce que vous arrivez à la page **prêt à configurer** .
 7. Vérifiez que la page Configurer indique l' **option d’écriture différée de mot de passe comme désactivé** , puis cliquez sur le bouton vert de la **configurer** pour valider vos modifications.
 8. Dans la boîte de dialogue **terminé** , désélectionnez l’option **Synchroniser maintenant** et puis cliquez sur **Terminer** pour fermer l’Assistant.
 9. Ouvrez à nouveau l' **Assistant de configuration Azure Connect d’Active Directory**.
 10.    **Répétez les étapes 2 à 8**, mais assurez-vous d' **Activer l’option d’écriture différée de mot de passe** sur les **fonctionnalités facultatives** de l’écran pour réactiver le service.

    ![][004]

Ces étapes seront rétablir votre connexion grâce à notre service de cloud et résoudre toute interruption, que vous pouvez être confronté.

Si la désactivation et la réactivation de la fonctionnalité d’écriture différée de mot de passe ne résout pas votre problème, il est recommandé que vous essayez d’installer de nouveau Azure Connect annonce comme une étape suivante.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installer la dernière version d’Azure Connect de publicité
Réinstaller le package Azure Connect d’Active Directory permet de résoudre les problèmes de configuration qui peuvent affecter votre capacité à soit se connectent à nos services en nuage ou de gérer les mots de passe dans votre environnement Active Directory local.
Nous vous recommandons, vous effectuez cette opération uniquement après avoir tenté les deux premières étapes décrites ci-dessus.

 1. Téléchargez la dernière version d’Azure Connect d’annonce [ici](active-directory-aadconnect.md#install-azure-ad-connect).
 2. Depuis que vous avez déjà installé Azure Connect d’Active Directory, vous devrez effectuer une mise à niveau sur place pour mettre à jour votre installation d’Azure Connect d’Active Directory vers la version la plus récente.
 3. Exécutez le package téléchargé et suivez l’à l’écran instructions pour mettre à jour votre ordinateur Azure Connect d’Active Directory.  Aucune des opérations manuelles supplémentaires ne sont nécessaires, sauf si vous avez personnalisé la sortie de règles de synchronisation standard, auquel cas vous devez **sauvegarder ces fichiers avant de procéder à une mise à niveau et de redéployer manuellement leur lorsque vous avez terminé**.

Ces étapes seront rétablir votre connexion grâce à notre service de cloud et résoudre toute interruption, que vous pouvez être confronté.

Si l’installation de la version la plus récente du serveur Azure Connect d’annonce ne résout pas votre problème, nous vous recommandons la désactivation et la réactivation d’écriture différée de mot de passe en tant que dernière étape après l’installation de la dernière synchronisation QFE.

Si le problème persiste, nous vous recommandons que vous examinez une [Écriture différée de résoudre les problèmes de mot de passe](#troubleshoot-password-writeback) et le [mot de passe Active Directory Azure Forum aux questions de gestion](active-directory-passwords-faq.md) pour voir si votre problème peut être discuté.


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Des liens vers le mot de passe réinitialiser la documentation
Vous trouverez ci-dessous des liens vers toutes les pages de documentation de réinitialisation de mot de passe Active Directory Azure :

* **Vous êtes ici car vous rencontrez des problèmes de connexion ?** Si tel est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Mode de fonctionnement**](active-directory-passwords-how-it-works.md) - en savoir plus sur les différents composants de service et de six chacune d’entre-elles
* [**Mise en route**](active-directory-passwords-getting-started.md) - Apprenez à vous permettent aux utilisateurs de réinitialiser et de modifier leurs mots de passe nuage ou sur site
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’aspect et la convivialité et le comportement du service pour les besoins de votre organisation
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) - Apprenez à déployer rapidement et efficacement gérer les mots de passe dans votre organisation
* [**Se référant**](active-directory-passwords-get-insights.md) - en savoir plus sur nos capacités de création de rapports intégrées
* [**Forum aux questions**](active-directory-passwords-faq.md) : obtenir des réponses aux questions fréquemment posées
* [**En savoir plus**](active-directory-passwords-learn-more.md) - profondeur accédez aux détails techniques du fonctionne du service



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"
