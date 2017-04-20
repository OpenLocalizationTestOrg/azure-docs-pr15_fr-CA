# <a name="pull-request-comment-automation"></a>Extraction d’automation de commentaire de demande

Nous utilisons automation de commentaire dans les commentaires de demande d’extraction pour permettre des collaborateurs et processus de révision des auteurs à attribuer des étiquettes de lecteur de la demande d’extraction.

| Commentaire | Ce qu’il fait | Disponibilité|
| -------- |-------------|-------------|
|#déconnexion | Lorsque l’auteur d’un article tape le commentaire **#signe hors tension** dans le flux de commentaire, l’étiquette de **prêt-à-fusion** est affectée. | Public et privé|
|#déconnexion | Si un collaborateur qui n’est pas l’auteur répertoriés tente d’approuver une demande d’extraction public avec le commentaire du **signe # off** , un message est écrit à la demande d’extraction indiquant que l’étiquette peut être affectée uniquement par l’auteur. | Public |
|#désactiver blocage | Si vous tapez **#hold-off** dans un commentaire de demande d’extraction, il supprime l’étiquette **prêt-à-fusion** - au cas où vous changez d’avis ou faites une erreur. Dans le mis en pension privés, cela affecte l’étiquette **not-fusion** . | Public et privé |
| #Veuillez-clôture | Les auteurs peuvent taper le commentaire de la **#please-fermer** dans le flux de commentaire d’une demande d’extraction pour le fermer si vous décidez de ne pas faire les modifications fusionnées. | Public |

##<a name="troubleshooting-sign-offs-in-the-public-repo"></a>Dépannage des validations dans le mis en pension public

Le mis en pension publique d’approbation automation est permet uniquement à l’auteur pour vous déconnecter. Un traitement manuel exceptionnel peut être nécessaire :

- **Les auteurs de l’article**: pour utiliser l’automatisation de commentaire référentiel public, votre compte GitHub réel doit correspondre exactement le compte GitHub dans les métadonnées de l’article. Importance de la mise en majuscules de votre compte. Si vous êtes bloqués à partir de l’approbation en raison de ce problème, envoyer des messages à l’alias azdocprs.

- **Autres employés**: Si vous êtes un employé qui est l’approbation, au nom de l’auteur, et vous sont bloqués par l’automatisation, contactez azdocprs avec le lien de demande d’extraction. Indiquer qui vous sont--PMs sur la même équipe de produit, les collègues de l’équipe de rédaction et de l’écriture des responsables d’équipe sont considérées comme des sources fiables.



## <a name="related"></a>Liées

- [Etiquette de demande d’extraction et de meilleures pratiques pour les collaborateurs de Microsoft](contributor-guide-pull-request-etiquette.md)

- [Passez en revue les critères de qualité pour la demande d’extraction](contributor-guide-pr-criteria.md)
