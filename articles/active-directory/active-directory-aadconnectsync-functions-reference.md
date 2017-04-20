<properties
    pageTitle="Azure Connect de AD sync : référence des fonctions | Microsoft Azure"
    description="Référence de mise en service des expressions déclaratives dans Azure Connect de AD sync."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-functions-reference"></a>Azure Connect de AD sync : référence des fonctions

Dans Azure Connect de publicité, les fonctions sont utilisées pour manipuler une valeur d’attribut lors de la synchronisation.  
La syntaxe des fonctions est exprimée au format suivant :  
`<output type> FunctionName(<input type> <position name>, ..)`

Si une fonction est surchargée et accepte plusieurs syntaxes, toutes les syntaxes valides sont répertoriés.  
Les fonctions sont fortement typées et ils vérifient que le type passé correspond le type documenté.  
Si le type ne correspond pas, une erreur est levée.

Les types sont exprimées avec la syntaxe suivante :

- **emplacement** – binaire
- **bool** : booléen
- **dt** – Date et heure GMT
- **enum** – énumération des constantes connues
- **exp** -Expression, qui est supposée être évaluée en une valeur booléenne
- **mvbin** – le binaire à valeurs multiples
- **mvstr** – les chaîne à valeurs multiples
- **mvref** – les référence à valeurs multiples
- **num** -numérique
- **ref** – référence
- **str** : chaîne
- **var** – une variante de (quasiment) n’importe quel autre type
- **void** – ne retourne pas une valeur

Les fonctions avec les types, **mvbin**, **mvstr**et **mvref** ne peuvent travailler que sur des attributs à valeurs multiples. Fonctions avec **emplacement**, **str**et **ref** fonctionnent sur les attributs à valeur unique et à valeurs multiples.

## <a name="functions-reference"></a>Référence des fonctions

Liste des fonctions | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**Conversion** |  
[CBool](#cbool) | [La fonction CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[CRef](#cref) | [Fonction CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**Date / heure** |  
[DateAdd](#dateadd) | [DateFromNum](#datefromnum) | [FormatDateTime](#formatdatetime) | [Maintenant](#now)
[NumFromDate](#numfromdate) |  
**Répertoire** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**Évaluation** |  
[IsBitSet](#isbitset) | [La fonction IsDate](#isdate) | [La fonction IsEmpty](#isempty) | [IsGuid](#isguid)
[IsNull](#isnull) | [IsNullOrEmpty](#isnullorempty) | [La fonction IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**Mathématiques** |  
[BitAnd](#bitand) | [BitOr](#bitor) | [RandomNum](#randomnum)
**À valeurs multiples** |  
[Contient](#contains) | [Nombre](#count) | [Élément](#item) | [ItemOrNull](#itemornull)
[Jointure](#join) | [RemoveDuplicates](#removeduplicates) | [Fractionner](#split) |
**Déroulement du programme** |  
[Erreur](#error) | [IIF](#iif)  | [Commutateur](#switch)
**Texte** |  
[GUID](#guid) | [InStr](#instr) | [La fonction InStrRev](#instrrev) | [LCase](#lcase)
[Gauche](#left) | [Len](#len) | [LTrim](#ltrim) | [Mid](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [Remplacer](#replace)
[ReplaceChars](#replacechars) | [Oui](#right) | [RTrim](#rtrim) | [Trim](#trim)
[UCase](#ucase) | [Word](#word)

----------
### <a name="bitand"></a>BitAnd

**Description :**  
La BITAND, fonction définit les bits spécifiés sur une valeur.

**Syntaxe :**  
`num BitAnd(num value1, num value2)`

- valeur1, valeur2 : les valeurs numériques qui doivent être liées par and ensemble

**Remarques :**  
Cette fonction convertit les deux paramètres de la représentation binaire et définit un bit :

- 0 - si une ou les deux bits correspondants dans le *masque* et *l’indicateur* sont 0
- 1 - si les deux bits correspondants sont 1.

En d’autres termes, elle renvoie la valeur 0 dans tous les cas, sauf si les bits correspondants de ces deux paramètres sont à 1.

**Exemple :**  
`BitAnd(&HF, &HF7)`  
Renvoie 7 car hexadécimale « F » et « F7 » correspondent à cette valeur.

----------
### <a name="bitor"></a>BitOr

**Description :**  
La fonction BitOr définit les bits spécifiés sur une valeur.

**Syntaxe :**  
`num BitOr(num value1, num value2)`

- valeur1, valeur2 : valeurs numériques qui doivent être or

**Remarques :**  
Cette fonction convertit les deux paramètres de la représentation binaire et définit un bit 1 si une ou les deux bits correspondants dans le masque et l’indicateur sont 1 et 0 si les deux bits correspondants sont 0. En d’autres termes, elle renvoie 1 dans tous les cas, sauf si les bits correspondants de ces deux paramètres sont 0.

----------
### <a name="cbool"></a>CBool

**Description :**  
La fonction CBool renvoie une valeur booléenne en fonction de l’expression évaluée

**Syntaxe :**  
`bool CBool(exp Expression)`

**Remarques :**  
Si l’expression correspond à une valeur différente de zéro, CBool retourne True, sinon elle renvoie FAUX.

**Exemple :**  
`CBool([attrib1] = [attrib2])`  

Retourne True si les deux les attributs ont la même valeur.

----------
### <a name="cdate"></a>La fonction CDate

**Description :**  
La fonction CDate retourne un DateTime UTC à partir d’une chaîne. DateTime n’est pas un type d’attribut natifs synchronisé mais est utilisée par certaines fonctions.

**Syntaxe :**  
`dt CDate(str value)`

- : Une chaîne de valeur Avec une date, une heure et éventuellement sur fuseau horaire

**Remarques :**  
La chaîne retournée est toujours l’heure UTC.

**Exemple :**  
`CDate([employeeStartTime])`  
Retourne qu'une valeur DateTime en fonction de l’employé début

`CDate("2013-01-10 4:00 PM -8")`  
Retourne un DateTime qui représente « 2013-01-11 12:00 AM »

----------
### <a name="cguid"></a>CGuid

**Description :**  
La fonction CGuid convertit la représentation sous forme de chaîne d’un GUID en représentation binaire.

**Syntaxe :**  
`bin CGuid(str GUID)`

- Une chaîne de mise en forme dans ce modèle : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### <a name="contains"></a>Contient

**Description :**  
La fonction Contains trouve une chaîne à l’intérieur d’un attribut à valeurs multiples

**Syntaxe :**  
`num Contains (mvstring attribute, str search)`-la casse  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-la casse

- attribut : l’attribut à plusieurs valeurs à rechercher.
- recherche : chaîne à rechercher dans l’attribut.
- Casetype : CaseInsensitive ou CaseSensitive.

Retourne les index dans l’attribut à valeurs multiples où la chaîne a été trouvée. 0 est renvoyé si la chaîne n’est pas trouvée.

**Remarques :**  
Pour les attributs de chaîne à valeurs multiples, la recherche trouve des sous-chaînes dans les valeurs.  
Pour les attributs de référence, la chaîne recherchée doit correspondre exactement à la valeur pour être considéré comme une correspondance.

**Exemple :**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Si l’attribut proxyAddresses possède une adresse de messagerie principale (indiquée en majuscules « SMTP : »), puis renvoyer l’attribut proxyAddress, sinon renvoie une erreur.

----------
### <a name="convertfrombase64"></a>ConvertFromBase64

**Description :**  
La fonction ConvertFromBase64 convertit la valeur de spécifiée base64 encodée à une chaîne normale.

**Syntaxe :**  
`str ConvertFromBase64(str source)`-suppose Unicode pour le codage  
`str ConvertFromBase64(str source, enum Encoding)`

- source : chaîne encodée Base64  
- Le codage : UTF8 de Unicode, ASCII,

**Exemple**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Les deux exemples renvoient «*Hello world !*"

----------
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex

**Description :**  
La fonction ConvertFromUTF8Hex convertit la valeur spécifiée de la Hex UTF8 codée en une chaîne.

**Syntaxe :**  
`str ConvertFromUTF8Hex(str source)`

- source : chaîne encodée de 2 octets UTF8

**Remarques :**  
La différence entre cette fonction et les ConvertFromBase64([],UTF8) dans la mesure où le résultat est convivial pour l’attribut de nom unique.  
Ce format est utilisé par Azure Active Directory en tant que nom de domaine.

**Exemple :**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Retourne "*Hello world !*"

----------
### <a name="converttobase64"></a>ConvertToBase64

**Description :**  
La fonction ConvertToBase64 convertit une chaîne en chaîne Unicode au format base64.  
Convertit la valeur d’un tableau d’entiers en sa représentation sous forme de chaîne équivalente qui est encodée à l’aide de chiffres en base 64.

**Syntaxe :**  
`str ConvertToBase64(str source)`

**Exemple :**  
`ConvertToBase64("Hello world!")`  
Retourne « SABlAGwAbABvACAAdwBvAHIAbABkACEA »

----------
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex

**Description :**  
La fonction ConvertToUTF8Hex convertit une chaîne en une valeur Hex UTF8 codée.

**Syntaxe :**  
`str ConvertToUTF8Hex(str source)`

**Remarques :**  
Le format de sortie de cette fonction est utilisé par Azure Active Directory en tant que format d’attribut de nom unique.

**Exemple :**  
`ConvertToUTF8Hex("Hello world!")`  
Cette propriété renvoie 48656C6C6F20776F726C6421

----------
### <a name="count"></a>Nombre

**Description :**  
La fonction Count renvoie le nombre d’éléments dans un attribut à valeurs multiples

**Syntaxe :**  
`num Count(mvstr attribute)`

----------
### <a name="cnum"></a>CNum

**Description :**  
La fonction CNum prend une chaîne et renvoie un type de données numérique.

**Syntaxe :**  
`num CNum(str value)`

----------
### <a name="cref"></a>CRef

**Description :**  
Convertit une chaîne en un attribut de référence

**Syntaxe :**  
`ref CRef(str value)`

**Exemple :**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### <a name="cstr"></a>Fonction CStr

**Description :**  
La fonction CStr convertit en type de données string.

**Syntaxe :**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

- valeur : peut être une valeur numérique, un attribut de référence ou une valeur booléenne.

**Exemple :**  
`CStr([dn])`  
Peut renvoyer « cn = Jean, dc = contoso, dc = com »

----------
### <a name="dateadd"></a>DateAdd

**Description :**  
Retourne une valeur Date contenant la date à laquelle un intervalle de temps spécifié a été ajouté.

**Syntaxe :**  
`dt DateAdd(str interval, num value, dt date)`

- intervalle : expression chaîne qui est l’intervalle de temps que vous souhaitez ajouter. La chaîne doit avoir une des valeurs suivantes :
 - YYYY année
 - q trimestre
 - m mois
 - Jour de y de l’année
 - d jour
 - w, jour de la semaine
 - WW semaine
 - h heure
 - n minutes
 - s deuxième
- valeur : le nombre d’unités que vous souhaitez ajouter. Il peut être positif (pour obtenir des dates dans le futur) ou négative (pour obtenir des dates dans le passé).
- date : DateTime représentant la date à laquelle l’intervalle est ajouté.

**Exemple :**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Ajoute 3 mois et renvoie une valeur DateTime qui représente « 2001-04-01 ».

----------
### <a name="datefromnum"></a>DateFromNum

**Description :**  
Le convertit de fonction DateFromNum mettre en forme une valeur de date de l’annonce en un type DateTime.

**Syntaxe :**  
`dt DateFromNum(num value)`

**Exemple :**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Retourne un DateTime représentant 2012-01-01-23:00:00

----------
### <a name="dncomponent"></a>DNComponent

**Description :**  
La fonction DNComponent retourne la valeur d’un composant de nom unique spécifié de gauche.

**Syntaxe :**  
`str DNComponent(ref dn, num ComponentNumber)`

- DN : l’attribut de référence à interpréter
- ComponentNumber : Le composant pour retourner le nom de domaine

**Exemple :**  
`DNComponent([dn],1)`  
Si le nom de domaine est « cn = Jean, unité d’organisation =..., « il renvoie Joe

----------
### <a name="dncomponentrev"></a>DNComponentRev

**Description :**  
La fonction DNComponentRev retourne la valeur d’un composant de nom unique spécifié va de droite (fin).

**Syntaxe :**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- DN : l’attribut de référence à interpréter
- ComponentNumber - le composant dans le DN à renvoyer
- Options : Le contrôleur de domaine – ignorer tous les composants avec « dc = »

**Exemple :**  
Si le nom de domaine est « cn = Jean, unité d’organisation = Atlanta, unité d’organisation = GA, unité d’organisation = US, dc = contoso, dc = com » puis  
`DNComponentRev([dn],3)`  
`DNComponentRev([dn],1,"DC")`  
Les deux renvoient à nous.

----------
### <a name="error"></a>Erreur

**Description :**  
La fonction Error est utilisée pour retourner une erreur personnalisée.

**Syntaxe :**  
`void Error(str ErrorMessage)`

**Exemple :**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Si le nom de compte d’attribut n’est pas présent, génère une erreur sur l’objet.

----------
### <a name="escapedncomponent"></a>EscapeDNComponent

**Description :**  
La fonction EscapeDNComponent prend un composant d’un nom unique et la remplace afin qu’il puisse être représenté dans LDAP.

**Syntaxe :**  
`str EscapeDNComponent(str value)`

**Exemple :**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Permet de s’assurer que l’objet peut être créé dans un annuaire LDAP, même si l’attribut displayName comporte des caractères qui doivent être échappés dans l’annuaire LDAP.

----------
### <a name="formatdatetime"></a>FormatDateTime

**Description :**  
La fonction FormatDateTime est utilisée pour mettre en forme une valeur DateTime en chaîne dans un format spécifié

**Syntaxe :**  
`str FormatDateTime(dt value, str format)`

- valeur : une valeur dans le format DateTime
- format : une chaîne représentant le format à convertir.

**Remarques :**  
Les valeurs possibles pour le format vous pouvez trouver ici : [Formats de Date/heure définis par l’utilisateur (fonction Format)](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Exemple :**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Résultats dans « 2007-12-25 ».

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Vous risquez dans « 20140905081453.0Z »

----------
### <a name="guid"></a>GUID

**Description :**  
Le GUID de la fonction génère un nouveau GUID aléatoire

**Syntaxe :**  
`str GUID()`

----------
### <a name="iif"></a>IIF

**Description :**  
La fonction IIF renvoie un ensemble de valeurs possibles, en fonction d’une condition spécifiée.

**Syntaxe :**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition : toute valeur ou expression qui peut être évaluée à true ou false.
- renvoie ValeurSiVrai : si la condition a la valeur true, la valeur retournée.
- ValeurSiFaux : si la condition a la valeur false, la valeur retournée.

**Exemple :**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Si l’utilisateur est un stagiaire, renvoie l’alias d’un utilisateur avec « t- » ajouté au début de celui-ci reste l’alias de l’utilisateur en l’état.

----------
### <a name="instr"></a>InStr

**Description :**  
La fonction InStr recherche la première occurrence d’une sous-chaîne dans une chaîne

**Syntaxe :**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- contrôle de chaîne : chaîne à rechercher
- correspondance de chaîne : chaîne à rechercher
- Démarrer : position pour trouver la sous-chaîne de départ
- comparer : vbTextCompare ou vbBinaryCompare

**Remarques :**  
Renvoie la position où la sous-chaîne a été trouvée ou 0 si non trouvée.

**Exemple :**  
`InStr("The quick brown fox","quick")`  
Dans la liste 5

`InStr("repEated","e",3,vbBinaryCompare)`  
A la valeur 7

----------
### <a name="instrrev"></a>La fonction InStrRev

**Description :**  
La fonction InStrRev recherche la dernière occurrence d’une sous-chaîne dans une chaîne

**Syntaxe :**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- contrôle de chaîne : chaîne à rechercher
- correspondance de chaîne : chaîne à rechercher
- Démarrer : position pour trouver la sous-chaîne de départ
- comparer : vbTextCompare ou vbBinaryCompare

**Remarques :**  
Renvoie la position où la sous-chaîne a été trouvée ou 0 si non trouvée.

**Exemple :**  
`InStrRev("abbcdbbbef","bb")`  
Renvoie 7

----------
### <a name="isbitset"></a>IsBitSet

**Description :**  
La fonction IsBitSet des Tests si un bit est définie ou non

**Syntaxe :**  
`bool IsBitSet(num value, num flag)`

- valeur : une valeur numérique qui est evaluated.flag : une valeur numérique qui a le bit doit être évaluée

**Exemple :**  
`IsBitSet(&HF,4)`  
Renvoie True parce que le bit « 4 » est défini dans la valeur hexadécimale « F »

----------
### <a name="isdate"></a>La fonction IsDate

**Description :**  
Si l’expression peut s’évalue en un type DateTime, puis la fonction IsDate prend la valeur True.

**Syntaxe :**  
`bool IsDate(var Expression)`

**Remarques :**  
Utilisé pour déterminer si CDate() peut être réussie.

----------
### <a name="isempty"></a>La fonction IsEmpty

**Description :**  
Si l’attribut est présent dans la SC ou la MV, mais correspond à une chaîne vide, la fonction IsEmpty prend la valeur True.

**Syntaxe :**  
`bool IsEmpty(var Expression)`

----------
### <a name="isguid"></a>IsGuid

**Description :**  
Si la chaîne peut être convertie en un GUID, la fonction IsGuid est évalué à true.

**Syntaxe :**  
`bool IsGuid(str GUID)`

**Remarques :**  
Un GUID est défini en tant que chaîne suivant un de ces modèles : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Utilisé pour déterminer si CGuid() peut être réussie.

**Exemple :**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Si le StrAttribute a un format GUID, retourner une représentation binaire, sinon retourne une valeur Null.

----------
### <a name="isnull"></a>IsNull

**Description :**  
Si l’expression prend la valeur Null, alors la fonction IsNull retourne true.

**Syntaxe :**  
`bool IsNull(var Expression)`

**Remarques :**  
Pour un attribut, une valeur Null est exprimée par l’absence de l’attribut.

**Exemple :**  
`IsNull([displayName])`  
Renvoie la valeur True si l’attribut n’est pas présent dans la SC ou la MV.

----------
### <a name="isnullorempty"></a>IsNullOrEmpty

**Description :**  
Si l’expression est null ou une chaîne vide, la fonction IsNullOrEmpty retourne alors la valeur true.

**Syntaxe :**  
`bool IsNullOrEmpty(var Expression)`

**Remarques :**  
Pour un attribut, il serait ont la valeur True si l’attribut est absent ou est présent mais est une chaîne vide.  
L’inverse de cette fonction se nomme IsPresent.

**Exemple :**  
`IsNullOrEmpty([displayName])`  
Renvoie la valeur True si l’attribut n’est pas présent ou est une chaîne vide dans la SC ou la MV.

----------
### <a name="isnumeric"></a>La fonction IsNumeric

**Description :**  
La fonction IsNumeric renvoie une valeur booléenne indiquant si une expression peut être évaluée comme un type de numéro.

**Syntaxe :**  
`bool IsNumeric(var Expression)`

**Remarques :**  
Utilisé pour déterminer si CNum() peut être réussi à analyser l’expression.

----------
### <a name="isstring"></a>IsString

**Description :**  
Si l’expression peut être évaluée en un type chaîne, puis la fonction IsString prend la valeur True.

**Syntaxe :**  
`bool IsString(var expression)`

**Remarques :**  
Utilisé pour déterminer si CStr() peut être réussi à analyser l’expression.

----------
### <a name="ispresent"></a>IsPresent

**Description :**  
Si l’expression correspond à une chaîne qui n’est pas Null et n’est pas vide, alors la fonction IsPresent retourne true.

**Syntaxe :**  
`bool IsPresent(var expression)`

**Remarques :**  
L’inverse de cette fonction se nomme IsNullOrEmpty.

**Exemple :**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### <a name="item"></a>Élément

**Description :**  
La fonction Item renvoie un élément à partir d’un chaîne/attribut à valeurs multiples.

**Syntaxe :**  
`var Item(mvstr attribute, num index)`

- attribut : attribut à valeurs multiples
- index : l’index d’un élément dans la chaîne à valeurs multiples.

**Remarques :**  
La fonction Item est utile avec la fonction Contains depuis que la dernière fonction retourne l’index d’un élément dans l’attribut à valeurs multiples.

Génère une erreur si l’index est hors limites.

**Exemple :**  
`Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)`  
Renvoie l’adresse de messagerie principale.

----------
### <a name="itemornull"></a>ItemOrNull

**Description :**  
La fonction ItemOrNull renvoie un élément à partir d’un chaîne/attribut à valeurs multiples.

**Syntaxe :**  
`var ItemOrNull(mvstr attribute, num index)`

- attribut : attribut à valeurs multiples
- index : l’index d’un élément dans la chaîne à valeurs multiples.

**Remarques :**  
La fonction ItemOrNull est utile avec la fonction Contains depuis que la dernière fonction retourne l’index d’un élément dans l’attribut à valeurs multiples.

Si l’index est hors limites, puis renvoie une valeur Null.

----------
### <a name="join"></a>Jointure

**Description :**  
La fonction Join prend une chaîne à plusieurs valeurs et retourne une chaîne à valeur unique avec séparateur spécifié insérée entre chaque élément.

**Syntaxe :**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

- attribut : attribut à valeurs multiples contenant les chaînes à joindre.
- séparateur : toute chaîne, utilisée pour séparer les sous-chaînes dans la chaîne retournée. Si omis, le caractère espace ( » «) est utilisé. Si délimiteur est une chaîne de longueur nulle (" ») ou rien, tous les éléments de la liste sont concaténés sans délimiteurs.

**Remarques**  
Parité existe entre les fonctions joindre et fractionner. La fonction Join prend un tableau de chaînes et les joint à l’aide d’une chaîne de délimiteur, pour retourner une chaîne unique. La fonction Split accepte une chaîne et la sépare au niveau du délimiteur, pour retourner un tableau de chaînes. Toutefois, une différence essentielle est que la jointure peut concaténer des chaînes avec toute chaîne de délimiteur, fractionnement peut uniquement séparer des chaînes à l’aide d’un délimiteur de caractère unique.

**Exemple :**  
`Join([proxyAddresses],",")`  
Peut retourner :"SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### <a name="lcase"></a>LCase

**Description :**  
La fonction LCase convertit tous les caractères d’une chaîne en minuscules.

**Syntaxe :**  
`str LCase(str value)`

**Exemple :**  
`LCase("TeSt")`  
Retourne « test ».

----------
### <a name="left"></a>Gauche

**Description :**  
La fonction gauche renvoie un nombre spécifié de caractères à partir de la gauche d’une chaîne.

**Syntaxe :**  
`str Left(str string, num NumChars)`

- chaîne : la chaîne à retourner des caractères à partir de
- NumChars : un numéro identifiant le nombre de caractères à renvoyer à partir du début de chaîne (à gauche)

**Remarques :**  
Une chaîne contenant les caractères numChars première dans la chaîne :

- Si numChars = 0, retourner une chaîne vide.
- Si numChars < 0, renvoie la chaîne d’entrée.
- Si la chaîne est null, retourne la chaîne vide.

Si la chaîne contient moins de caractères que le nombre numChars spécifié dans, une chaîne identique à la chaîne (c'est-à-dire, contenant tous les caractères dans le paramètre 1) est retournée.

**Exemple :**  
`Left("John Doe", 3)`  
Retourne « Joh ».

----------
### <a name="len"></a>Len

**Description :**  
La fonction Len retourne le nombre de caractères dans une chaîne.

**Syntaxe :**  
`num Len(str value)`

**Exemple :**  
`Len("John Doe")`  
Renvoie 8

----------
### <a name="ltrim"></a>LTrim

**Description :**  
La fonction LTrim supprime les espaces blancs à partir d’une chaîne.

**Syntaxe :**  
`str LTrim(str value)`

**Exemple :**  
`LTrim(" Test ")`  
Retourne « Test »

----------
### <a name="mid"></a>Mid

**Description :**  
La fonction STXT renvoie un nombre spécifié de caractères à partir d’une position spécifiée dans une chaîne.

**Syntaxe :**  
`str Mid(str string, num start, num NumChars)`

- chaîne : la chaîne à retourner des caractères à partir de
- Démarrer : un numéro identifiant le démarrage de position dans la chaîne à retourner des caractères à partir de
- NumChars : un numéro identifiant le nombre de caractères à renvoyer à partir de la position dans la chaîne

**Remarques :**  
Renvoyer des caractères numChars commençant à partir du début de position dans la chaîne.  
Une chaîne contenant des caractères numChars à partir du début de position dans une chaîne :

- Si numChars = 0, retourner une chaîne vide.
- Si numChars < 0, renvoie la chaîne d’entrée.
- Si Démarrer > la longueur de chaîne, retourner une chaîne d’entrée.
- Si Démarrer < = 0, renvoie la chaîne d’entrée.
- Si la chaîne est null, retourne la chaîne vide.

S’il n’y a pas les caractères de NbCaractères restant dans la chaîne à partir du début de position, autant que possible des caractères sont renvoyés.

**Exemple :**  
`Mid("John Doe", 3, 5)`  
Retourne « ne hn ».

`Mid("John Doe", 6, 999)`  
Retourne « Doe »

----------
### <a name="now"></a>Maintenant

**Description :**  
La fonction maintenant renvoie une valeur DateTime qui spécifie la date et l’heure, en fonction de la date système et l’heure de votre ordinateur.

**Syntaxe :**  
`dt Now()`

----------
### <a name="numfromdate"></a>NumFromDate

**Description :**  
La fonction NumFromDate renvoie une date dans le format de date de l’annonce.

**Syntaxe :**  
`num NumFromDate(dt value)`

**Exemple :**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Propriété renvoie 129699324000000000

----------
### <a name="padleft"></a>PadLeft

**Description :**  
Le PadLeft fonction gauche-remplit une chaîne à une longueur spécifiée à l’aide d’un caractère de remplissage fourni.

**Syntaxe :**  
`str PadLeft(str string, num length, str padCharacter)`

- chaîne : la chaîne à compléter.
- longueur : un nombre entier représentant la longueur de la chaîne souhaitée.
- padCharacter : une chaîne composée d’un seul caractère à utiliser comme caractère de remplissage

**Remarques :**

- Si la longueur de la chaîne est inférieure à la longueur, padCharacter est ajouté à plusieurs reprises au début (à gauche) de la chaîne jusqu'à ce qu’il ait une longueur égale à la longueur.
- PadCharacter peut être un caractère d’espace, mais il ne peut pas être une valeur null.
- Si la longueur de chaîne est égal ou supérieur à la longueur, la chaîne est renvoyée inchangée.
- Si la chaîne a une longueur supérieure ou égale à la longueur, c’est une chaîne identique à la chaîne qui est retournée.
- Si la longueur de la chaîne est inférieure à la longueur, une nouvelle chaîne de la longueur désirée est retournée chaîne contenant rempli à l’aide d’un padCharacter.
- Si la chaîne est null, la fonction renvoie une chaîne vide.

**Exemple :**  
`PadLeft("User", 10, "0")`  
Retourne « 000000User ».

----------
### <a name="padright"></a>PadRight

**Description :**  
Le PadRight fonction droite-remplit une chaîne à une longueur spécifiée à l’aide d’un caractère de remplissage fourni.

**Syntaxe :**  
`str PadRight(str string, num length, str padCharacter)`

- chaîne : la chaîne à compléter.
- longueur : un nombre entier représentant la longueur de la chaîne souhaitée.
- padCharacter : une chaîne composée d’un seul caractère à utiliser comme caractère de remplissage

**Remarques :**

- Si la longueur de la chaîne est inférieure à la longueur, puis padCharacter est régulièrement ajouté à la fin (à droite) de la chaîne jusqu'à ce qu’il ait une longueur égale à la longueur.
- padCharacter peut être un caractère d’espace, mais il ne peut pas être une valeur null.
- Si la longueur de chaîne est égal ou supérieur à la longueur, la chaîne est renvoyée inchangée.
- Si la chaîne a une longueur supérieure ou égale à la longueur, c’est une chaîne identique à la chaîne qui est retournée.
- Si la longueur de la chaîne est inférieure à la longueur, une nouvelle chaîne de la longueur désirée est retournée chaîne contenant rempli à l’aide d’un padCharacter.
- Si la chaîne est null, la fonction renvoie une chaîne vide.

**Exemple :**  
`PadRight("User", 10, "0")`  
Retourne « User000000 ».

----------
### <a name="pcase"></a>PCase

**Description :**  
La fonction PCase convertit le premier caractère de chaque mot séparés par des espaces d’une chaîne en majuscules, et tous les autres caractères sont convertis en minuscules.

**Syntaxe :**  
`String PCase(string)`

**Remarques :**

- Cette fonction ne fournit pas actuellement une casse appropriée pour convertir un mot entièrement en majuscule, tel qu’un acronyme.

**Exemple :**  
`PCase("TEsT")`  
Retourne « Test ».

`PCase(LCase("TEST"))`  
Retourne « Test »

----------
### <a name="randomnum"></a>RandomNum

**Description :**  
La fonction RandomNum renvoie un nombre aléatoire entre un intervalle spécifié.

**Syntaxe :**  
`num RandomNum(num start, num end)`

- Démarrer : un numéro correspondant à la limite inférieure de la valeur aléatoire à générer
- fin : un numéro correspondant à la limite supérieure de la valeur aléatoire à générer

**Exemple :**  
`Random(100,999)`  
Peut retourner 734.

----------
### <a name="removeduplicates"></a>RemoveDuplicates

**Description :**  
La fonction RemoveDuplicates prend une chaîne à valeurs multiples et assurez-vous que chaque valeur est unique.

**Syntaxe :**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemple :**  
`RemoveDuplicates([proxyAddresses])`  
Retourne un attribut proxyAddress rectifié où toutes les valeurs en double ont été supprimés.

----------
### <a name="replace"></a>Remplacer

**Description :**  
La fonction Remplacer remplace toutes les occurrences d’une chaîne à une autre chaîne.

**Syntaxe :**  
`str Replace(str string, str OldValue, str NewValue)`

- chaîne : une chaîne pour remplacer les valeurs dans.
- OldValue : La chaîne à rechercher et à remplacer.
- NewValue : La chaîne à remplacer.

**Remarques :**  
La fonction reconnaît les monikers spéciales suivantes :

- \n – nouvelle ligne
- \r – retour chariot
- \t – onglet

**Exemple :**  
`Replace([address],"\r\n",", ")`  
Remplace le CRLF avec une virgule et un espace et pourrait conduire à « Un Microsoft Way, Redmond, WA, États-Unis »

----------
### <a name="replacechars"></a>ReplaceChars

**Description :**  
La fonction ReplaceChars remplace toutes les occurrences de caractères trouvés dans la chaîne de ReplacePattern.

**Syntaxe :**  
`str ReplaceChars(str string, str ReplacePattern)`

- chaîne : remplacer des caractères dans une chaîne.
- ReplacePattern : une chaîne qui contient un dictionnaire de caractères à remplacer.

Le format est {source1} : {target1}, {source2} : {target2}, {sourceN}, {targetN} où la source est le caractère à rechercher et la cible de la chaîne à remplacer.

**Remarques :**

- La fonction prend chaque occurrence de sources définies et les remplace par les cibles.
- La source doit être exactement un caractère (unicode).
- La source ne peut pas être vide ou dépasse un caractère (erreur d’analyse).
- La cible peut avoir plusieurs caractères, par exemple ö:oe, β:ss.
- La cible peut être vide, indiquant que le caractères doivent être supprimé.
- La source est sensible à la casse et doit correspondre exactement.
- (Virgule) et : (deux-points) sont des caractères réservés et ne peuvent pas être remplacés à l’aide de cette fonction.
- Les espaces et autres caractères blancs dans la chaîne de ReplacePattern sont ignorés.

**Exemple :**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Renvoie Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Retourne « ONeil », la graduation est défini pour être supprimé.

----------
### <a name="right"></a>Oui

**Description :**  
La fonction DROITE renvoie un nombre spécifié de caractères à partir de la droite (end) d’une chaîne.

**Syntaxe :**  
`str Right(str string, num NumChars)`

- chaîne : la chaîne à retourner des caractères à partir de
- NumChars : un numéro identifiant le nombre de caractères à renvoyer à partir de la fin (à droite) de chaîne

**Remarques :**  
NumChars est renvoyé à partir de la dernière position de la chaîne.

Une chaîne contenant les caractères numChars dernières dans une chaîne :

- Si numChars = 0, retourner une chaîne vide.
- Si numChars < 0, renvoie la chaîne d’entrée.
- Si la chaîne est null, retourne la chaîne vide.

Si la chaîne contient moins de caractères que le nombre NumChars spécifié dans, une chaîne identique à la chaîne est retournée.

**Exemple :**  
`Right("John Doe", 3)`  
Retourne « Doe ».

----------
### <a name="rtrim"></a>RTrim

**Description :**  
La fonction RTrim supprime les espaces à droite d’une chaîne.

**Syntaxe :**  
`str RTrim(str value)`

**Exemple :**  
`RTrim(" Test ")`  
Retourne « Test ».

----------
### <a name="split"></a>Fractionner

**Description :**  
La fonction Split accepte une chaîne séparée par un délimiteur et rend une chaîne à valeurs multiples.

**Syntaxe :**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

- valeur : la chaîne avec un caractère de délimitation pour séparer.
- séparateur : caractère à utiliser comme séparateur.
- limite : nombre maximum de valeurs qui peut retourner.

**Exemple :**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Retourne une chaîne à valeurs multiples de 2 éléments utiles pour l’attribut proxyAddress.

----------
### <a name="stringfromguid"></a>StringFromGuid

**Description :**  
La fonction StringFromGuid accepte un GUID binaire et le convertit en une chaîne.

**Syntaxe :**  
`str StringFromGuid(bin GUID)`

----------
### <a name="stringfromsid"></a>StringFromSid

**Description :**  
La fonction StringFromSid convertit un tableau d’octets contenant un identificateur de sécurité à une chaîne.

**Syntaxe :**  
`str StringFromSid(bin ObjectSID)`  

----------
### <a name="switch"></a>Commutateur

**Description :**  
La fonction Switch est utilisée pour renvoyer une valeur unique, en fonction des conditions évaluées.

**Syntaxe :**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr : expression Variant à évaluer.
- valeur : valeur à renvoyer si l’expression correspondante a la valeur True.

**Remarques :**  
La liste d’arguments de fonction Switch se compose de paires d’expressions et de valeurs. Les expressions sont évaluées de gauche à droite, et la valeur associée à la première expression ont la valeur True est retournée. Si les parties ne sont pas correctement mises en paires, une erreur d’exécution se produit.

Par exemple, si expr1 est True, Switch retourne valeur1. Si expr-1 est False, mais expr-2 est True, Switch renvoie value-2 et ainsi de suite.

Switch retourne un rien si :

- Aucune des expressions sont vraies.
- La première expression True possède une valeur correspondante qui est Null.

La fonction Switch évalue toutes les expressions, même si elle renvoie uniquement un d’eux. Pour cette raison, il est conseillé d’effets indésirables. Par exemple, si l’évaluation d’une expression entraîne une division par zéro, une erreur se produit.

Valeur peut également être la fonction d’erreur, ce qui retourne une chaîne personnalisée.

**Exemple :**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Renvoie la langue parlée dans certaines grandes villes, sinon retourne une erreur.

----------
### <a name="trim"></a>Trim

**Description :**  
La fonction SUPPRESPACE supprime les espaces à gauche et blanc à partir d’une chaîne.

**Syntaxe :**  
`str Trim(str value)`  

**Exemple :**  
`Trim(" Test ")`  
Retourne « Test ».

`Trim([proxyAddresses])`  
Supprime les espaces à gauche et pour chaque valeur de l’attribut proxyAddress.

----------
### <a name="ucase"></a>UCase

**Description :**  
La fonction UCase convertit tous les caractères d’une chaîne en majuscules.

**Syntaxe :**  
`str UCase(str string)`

**Exemple :**  
`UCase("TeSt")`  
Retourne « TEST ».

----------
### <a name="word"></a>Word

**Description :**  
La fonction de Word renvoie un mot contenu dans la chaîne, en fonction des paramètres décrivant les délimiteurs à utiliser et le nombre de word à retourner.

**Syntaxe :**  
`str Word(str string, num WordNumber, str delimiters)`

- chaîne : la chaîne à retourner un mot à partir de.
- WordNumber : un numéro identifiant le numéro de word doit retourner.
- délimiteurs : une chaîne représentant la delimiter(s) qui doit être utilisé pour identifier les mots

**Remarques :**  
Chaque chaîne de caractères dans la chaîne séparée par l’un des caractères de délimiteurs sont identifiés en tant que mots :

- Si nombre < 1, renvoie une chaîne vide.
- Si la chaîne est null, retourne la chaîne vide.

Si la chaîne contient moins que le nombre de mots, ou la chaîne ne contient pas tous les mots identifiées par des délimiteurs, une chaîne vide est retournée.

**Exemple :**  
`Word("The quick brown fox",3," ")`  
Retourne « brown »

`Word("This,string!has&many separators",3,",!&#")`  
Renvoie « a »

## <a name="additional-resources"></a>Ressources supplémentaires

* [Comprendre la mise en service des Expressions déclaratives](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure Sync de connexion Active Directory : Options de personnalisation de la synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
