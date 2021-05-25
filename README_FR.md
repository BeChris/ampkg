# ampkg
Un gestionnaire de paquets pour les développeurs et utilisateurs de systèmes d'exploitation basés sur ou dérivés d'AmigaOS (AmigaOS classic, AmigaOS4, AROS, MorphOS)

Etant inspiré de Haiku ports, Gentoo Linux Portage, Archlinux Pacman et d'autres, 

ampkg peut être utilisé avec une interface graphique ou dans le terminal (en utilisant des paramètres en ligne de commande).\

Ci-dessous, un fichier de recette est abrégé en **recipe**.

Les utilisateurs de cet outil peuvent être divisés en trois catégories:
1. Pour les développeurs, ampkg:
   1. Uniformise la construction de logiciels en se basant sur des recipe
   2. Facilite l'écriture de recipe
   3. Facilite l'utilisation de dépendances obligatoires et/ou optionelles
   4. Etant basé sur des recipe, facilite la génération de paquets logiciels installables et l'upload des fichiers de recette pour approbation par des empaqueteurs

2. Pour les empaqueteurs, ampkg peut être utilisé pour:
   1. Accepter ou refuser les recipe soumis par les développeurs
   2. Une fois accepté, il est possible de générer à partir du recipe un paquet installable
   3. Ce paquet installable peut être uploadé sur un serveur de téléchargement et rendu disponible aux utilisateurs pour installation automatique au travers de ampkg

3. Pour les utilisateurs, ampkg peut être utilisé pour:
   1. Lister/installer/mettre à jour/désinstaller des paquets

## Qu'est-ce qu'un recipe?
Un recipe permet aux développeurs, à partir d'un logiciel donné, d'automatiquement télécharger ses sources, (éventuellement) les patcher, les compiler et générer un paquet binaire.
Il est également possible de générer un paquet à partir d'un logiciel fourni seulement sous forme binaire (sans ses sources).

Un dépôt GitHub contiendra les recipes organisés en dossier/sous-dossiers : les dossiers dans lesquels un recipe se situe fournissent sa catégorie.
Les catégories sont celles du site morphos-storage.\
Appelons ce dépôt GitHub morphos-recipes.

Dans ce dépôt il sera possible de trouver le même recipe mais à des versions différentes.\
Exemples : sdl2-2.0.3 et sdl2-2.0.4

Les paquets générés à partir de ces recipes seront hébergés sur le serveur morphos-storage.

Un recipe doit être nommé de la même façon que le logiciel à construire, être écrit en JSON et avoir l'extension .recipe.\
Exemples : sdl2-2.0.3.recipe ou sdl2-2.0.4.recipe

## Le cas des développeurs
**Les développeurs doivent avoir le SDK MorphOS installé.**

Imaginons un développeur développant ou portant un logiciel existant pour MorphOS.

Il écrit un recipe dans lequel il peut indiquer beaucoup d'informations à propos du logiciel porté mais aussi comment le construire.\
Le recipe peut être écrit à la main mais ampkg pourra servir pour faciliter sa mise au point au travers d'une interface graphique.\
Il utilise ampkg pour construire, à partir du recipe qu'il vient d'écire, un paquet binaire, peut l'installer et le tester sur son système.\
Une fois testé, il peut utiliser ampkg pour désinstaller le paquet de test.

Quand il est satisfait par le recipe, il utilise encore une fois ampkg pour le soumettre (procesus à détailler)\

## Le cas des empaqueteurs
**Les empaqueteurs doivent avoir le SDK MorphOS installé.**

Processus à détailler comment l'empaqueteur reçoit les recipes soumis par les développeurs.

Quand il récupère un recipe:
1. Il utilise ampkg to build, depuis le recipe récupéré, un paquet binaire, l'installe et le teste sur son sytème.
2. S'il accepte le recipe il l'upload (ainsi que le paquet binaire associé) sur morphos-storage en utilisant une fois de plus ampkg.
3. Par contre, s'il refuse le recipe, il peut échanger avec le développeur par email jusqu'à ce que le recipe soit OK.

## Le case des utilisateurs
**Les utilisateurs n'ont pas besoin du SDK MorphOS.**

Un utilisateur utilise ampkg pour obtenir la liste des paquets disponible ou pour savoir des paquets déjà installés peuvent être mis à jour.

Il peut alors installer de nouveaux paquets, mettre à jour ou désinstaller ceux déjà installés.

Dans le cas d'une installation ou d'une mise à jour, ampkg télécharge automatiquement le(s) paquet(s) (et ses/leurs dépendances obligatoires) et les installe.\
Si le(s) paquet(s) indique quelques dependances optionelles elles sont affichées à l'utilisateur qui peut choisir celle(s) qu'il veur installer en plus.

## Plus de détails à propos des recipe

Il doivent contenir des sections ablogatoires et éventuellement des sections optionelles.
Chaque section peut contenir, soit:
1. Une chaine de caractères (en une seule ou plusieurs lignes)
2. Un dictionnaire contenant des paires clé/valeur
3. A COMPLETER

Plusieurs choix sont affichés comme ceci : (choice1|choice2|choice3)\
La partie optionelle d'une valeur est affichée comme ceci : [optional part]

## Utilisation des comentaires
Le format JSON n'autorisant pas officiellement les commentaires, la convention suivante sera utilisée:\
Pour ajouter un commentaire à une section, une section spécifique comportant le même nom (mais commençant par //) sera utilisée.

Exemples:
```
{
    "//SUMMARY": "This a comment for SUMMARY section",
    "SUMMARY": "This is the real SUMMARY section"
}
```

## Utilisation d'alias
Dans n'importe quelle valeur de type chaine de caractères il est possible d'utiliser des alias : il seront automatiquement remplacés par leur valeur.\
Référencer un alias est réalisé comme ceci : $(alias)\

Ci-dessous, la liste des alias utilisables:

### CATEGORY
Remplacé par la catégorie du logiciel installé.

## Localisation des chaines de description
La langue par défaut est l'anglais.
Mais il est possible de fournir, pour la même section, une chaine de caractères par langue.
Les sections concernées sont:
1. SUMMARY
2. HOMEPAGE
3. PRE_MESSAGES
4. POST_MESSAGES
5. CHANGELOG
6. LINKS

Pour préciser la langue, la section doit se terminer par le suffixe :LANG (où LANG est le code ISO_639-1 de langue en 2 lettres majuscules).\
Si aucune section:LANG n'est trouvée (où LANG est la langue du système) alors le contenu de la section sans suffixe :LANG est utilisé.

Exemples:
```
{
    "//SUMMARY:EN": "Contenu utilisé quand la langue du système est Anglais",
    "SUMMARY:EN": "This is the real SUMMARY section",
    "//SUMMARY:FR": "Contenu utilisé quand la langue du système est Francais",
    "SUMMARY:FR": "Ceci est la VRAI section SUMMARY"
}
```

```
{
    "SUMMARY": "Contenu utilisé quand il n'y pas de section SUMMARY:LANG par rapport à la langue du sytème",
    "SUMMARY:FR": "Contenu utilisé quand la langue du système est Francais",
    "SUMMARY:EN": "Contenu utilisé quand la langue du système est Anglais",
    "SUMMARY:DE": "Contenu utilisé quand la langue du système est Allemand"
}
```

## Utilisation de guillemets dans les chaines de caractères
S'il est nécessaire d'utiliser des quillemets ils doivent être "échappés" avec deux \ (backslash) caractères.

Exemples:
```
{
    "SUMMARY": "A SUMMARY with \\"quotes\\""
}
```

## Vérifier les capacités processeur
Certaines actions devraient être effectuées seulement si le processeur a ou n'a pas une capacité spécifique.\

Les sections concernées sont:
1. INSTALL
2. PATCHSET
3. BUILD_MANDATORY
4. BUILD_OPTIONAL
5. RUN_MANDATORY
6. RUN_OPTIONAL
7. BUILD
8. PRE_MESSAGES
9. POST_MESSAGES
10. INSTALL_DEV
11. FILES_MODIFICATION

Par défaut ces sections, si incluses dans le recipe, sont prises en compte quelque soit les capacités du processeur.\
Pour restreindre l'utilisation d'une section dans un cas où le processor a une ou plusieurs capacités, la section en question doit se terminer par le suffixe :CAPS.\
CAPS est liste de capacités écrites en majuscule et séparées par des espaces.

Les capacités supportées sont:
1. Pour les processeurs PowerPC:
   1. FPU : le processeur a une FPU
   2. ALTIVEC : le processeur a une unité Altivec
   3. PERFMONITOR : le processeur a une extension de type performance measurement
   4. DATASTREAM : le processeur a une extension datastream
2. Pour les processeurs x86:
   1. FPU : le processeur a une FPU
   2. MMX : le processeur a le jeu d'instructions MMX
   3. SSE : le processeur a le jeu d'instructions SSE
   4. SSE2 : le processeur a le jeu d'instructions SSE2
   5. AVX : le processeur a le jeu d'instructions AVX
   6. AVX2 : le processeur a le jeu d'instructions AVX2
   7. Capacités à rajouter ?

Exemples:
```
{
    "//BUILD:FPU": "Exécute cette commande seulement si le processeur a une FPU (qu'il ai ou pas une unité ALTIVEC)",
    "BUILD:FPU": "make -f Makefile.fpu"
}
```

```
{
    "//BUILD:FPU ALTIVEC": "Exécute cette commande seulement si le processeur a une FPU et une unité ALTIVEC",
    "BUILD:FPU ALTIVEC": "make -f Makefile.altivec_fpu"
}
```

```
{
    "//BUILD": "Exécute cette command quelque soit les capacités du processeur",
    "BUILD": "make -f Makefile.generic"
}
```


## Sections obligatoires
### SUMMARY
Contient une courte description du paquet en 100 caractères maximum.

Exemples:
```
{
    "SUMMARY": "A short summary."
}
```

### ARCH
Contient une valeur pouvant être exprimée par (arm|ppc|x86|m68k)-(aros|amigaos|morphos) [restrictions de version] pour indiquer pour quelle architecture le paquet est généré.\
La valeur spéciale **any** peut être utilisée que le paquet peut être installé et utilisé sur n'importe quelle architecture et OS.

Pour le moment seules **ppc-morphos** et **any** seront supportées.

Exemples:
```
{
    "//ARCH": "Utilisable sur n'importe quelle ordinateur à base de PowerPC sous MorphOS à n'importe quelle version",
    "ARCH": "ppc-morphos"
}
```

```
{
    "//ARCH": "Utilisable sur n'importe quelle ordinateur à base de PowerPC sous MorphOS en version >= 1.5",
    "ARCH": "ppc-morphos >= 1.5"
}
```

```
{
    "//ARCH": "Utilisable sur n'importe quelle ordinateur à base de PowerPC sous MorphOS en version >= 1.5 et 2.0",
    "ARCH": "ppc-morphos >= 1.5 <= 2.0"
}
```

```
{
    "//ARCH": "Utilisable sur n'importe quelle architecture/OS (par exemple pour des paquets contenant que de la documentation ou des données)",
    "ARCH": "any"
}
```


### URL
Une list de lien(s) vers n'importe quel fichier ou dépôt git à cloner.\
Chaque lien vers un fichier doit être associé avec le MD5 de ce fichier.\
Et chaque dépôt git doit être associé à un tag, une branche ou un SHA1 auquel le dépôt sera cloné.\
L'URL d'un dépôt git doit commencer par git+
Si le lien vers un fichier commence par file:// alors il sera téléchargé à partir d'un chemin relatif au recipe.

Exemples:
```
{
    "//URL": "Une archive et un patch avec leur MD5 associé",
    "URL": [
        {
            "https://www.libsdl.org/release/SDL2-2.0.14.tar.gz": "76ed4e6da9c07bd168b2acd9bfefab1b"
        },
        {
            "file://SDL2-2.0.14.patch": "61206a217abb1377cf1eef59a8450f8e"
        }
    ]

}
```

```
// A git repository to be cloned at release-2.0.14 tag
{
    "//URL": "Un dépôt git à cloner sur le tag release-2.0.14",
    "URL": [
        {
            "git+https://github.com/libsdl-org/SDL.git": "release-2.0.14"
        }
    ]

}
```

### PROVIDES
Liste des composants (avec leur version) fournis par le paquet.\
Il peuvent être des dépendances obligatoire ou optionelles pour construire ou exécuter d'autre paquets (cf sections RUN_MANDATORY, RUN_OPTIONAL, BUILD_MANDATORY et BUILD_OPTIONAL)

Exemples:
```
{
    "PROVIDES": [
        {
            "sdl2": "2.0.14-1"
        }
    ]
}
```

**IMPORTANT**: Il est possible que, pour un même logiciel servant de base (dans le cas ci-dessus sdl2 2.0.14), le recipe évolue.\
Dans ce cas, la dernière partie de la version (ici le -1 dans 2.0.14-1) doit être incrémentée à chaque fois que le recipe évolue. 


### PACKAGED
Cette section est utilisée pour fournir une liste de dossiers et/ou fichier issus de fichiers téléchargés ou de dépôt git clonés (voir la section _URL_ ci-dessus).\
Ces dossiers et/ou fichiers seront inclus dans le paquet généré.

Exemples:
```
{
    "//PACKAGED": "Quand le paquet est généré inclut tout ce qu'il y a dans le dossier SDL2",
    "PACKAGED": [
        "SDL2"
    ]
}
```

### INSTALL
Cette section est utilisée pour fournir une liste de dossiers et/ou fichiers issus du paquet téléchargé par ampkg.\
Ces dossiers et/ou fichiers seront copiés sur le système de l'utilisateur quand le paquet est installé ou mis à jour.\
Il seront supprimés quand le paquet est désinstallé.

**IMPORTANT**: Les chemins terminant avec ':' or '/' sont considérés comme des dossiers de destination.\
Sinon ils sont considérés comme des fichiers de destination.

Exemples:
```
{
    "//INSTALL": "Lors de l'installation, copie depuis le paquet le fichier SDL2/README.txt dans Docs: et tous les fichiers de SDL2/Libs/ dans Libs:",
    "INSTALL": [
        {
            "SDL2/README.txt": "Docs:"
        },
        {
            "SDL2/Libs/*.*": "Libs:"
        }
    ]

}
```

### MAINTAINERS
Une liste de personnes qui maintiennent ce paquet.\
Chaque personne devrait être identifiée comme ceci : "Nom complet &lt;adresse email&gt;".\
A chaque fois qu'il y a un nouveau mainteneur, il est encouragé de le rajouter en début de liste.


## Sections optionelles
### HOMEPAGE
Lien vers la page d'accueil du projet

Exemples:
```
{
    "HOMEPAGE": "https://www.libsdl.org/index.php"
}
```

### PATCHSET
Liste d'un ou plusieurs fichier de patch à appliquer aux sources de l'archive après qu'elle ai été téléchargée et décompressée et juste avant d'être compilée.\
Les patch sont appliqués dans le même ordre qu'il apparaissent dans la liste.

Exemples:
```
{
    "PATCHSET": [
        "patch1.txt",
        "patch2.txt"
    ]
}
```

### BUILD_MANDATORY
List des paquets qui **doivent** être installés sur le système du développeur pour pouvoir compiler le paquet associé au recipe.\ 
Pour chaque paquet obligatoire une [restrictions de version] peut être indiquée.\
Si aucun paquet correspondant à cette restriction n'est trouvé alors le paquet ne sera pas installé.

Exemples:
```
{
    "//BUILD_MANDATORY": "Le paquet à compiler a absolument besoin de sdl2-devel en version >= 2.0.10 et du compilateur hollywood en version 8.0",
    "BUILD_MANDATORY": [
        "sdl2-devel >= 2.0.10",
        "hollywood:compiler = 8.0",
    ]
}
```

### BUILD_OPTIONAL
Même chose que BUILD_MANDATORY excepté que les paquets indiqués sont optionnels et non obligatoires : si un d'eux n'est pas trouvé la construction du paquet est quand même lancée.

### RUN_MANDATORY
List des paquets qui **doivent** être installés sur le système de l'utilisateur pour pouvoir exécuter un binaire inclus dans le paquet installé.\ 
Pour chaque paquet obligatoire une [restrictions de version] peut être indiquée.\
Si aucun paquet correspondant à cette restriction n'est trouvé alors le paquet ne sera pas installé.

Exemples:
```
{
    "//RUN_MANDATORY": "Le paquet à installer a absolument besoin de sdl2 en version >= 2.0.10 et sdl2_ttf en version entre 2.0.9 et 2.0.11 inclusifs",
    "RUN_MANDATORY": [
        "sdl2 >= 2.0.10",
        "sdl2_ttf >= 2.0.9 <= 2.0.11"
    ]
}
```

### RUN_OPTIONAL
Même chose que RUN_MANDATORY excepté que les paquets indiqués sont optionnels et non obligatoires : si un d'eux n'est pas trouvé il est quand même possible d'exécuter un binaire du paquet.

### BUILD
Liste des étapes à suivre pour construire le paquet.\
Chaque chaine de caractères dans la liste est une commande à exécuter et les commandes à exécuter le sont dans le même ordre qu'elles apparaissent dans la liste.\
Si une commande échoue (code retour <> 0) alors le paquet n'est pas construit.

Exemples:
```
{
    "//BUILD": "Construit SDL2 en deux étapes en utilisant make à chaque fois",
    "BUILD": [
        "make -C SDL2",
        "make -C SDL2 headers"
    ]
}
```

### PRE_MESSAGES
Liste des messages à afficher avant l'installation/mise à jour du paquet.\
Ces messages sont concaténés et affichés en un seul bloc.\
Pour démarrer un nouveau bloc, incluez une chaine de caractère "@@@@" dans la liste.\
Si un message commence avec file:// alors le contenu du fichier en question est affiché.

Exemples:
```
{
    "PRE_MESSAGES": [
        "Wellcome to this package"
    ]
}
```

### POST_MESSAGES
Liste des messages à afficher après l'installation/mise à jour du paquet.\
Ces messages sont concaténés et affichés en un seul bloc.\
Pour démarrer un nouveau bloc, incluez une chaine de caractère "@@@@" dans la liste.\
Si un message commence avec file:// alors le contenu du fichier en question est affiché.

Exemples:
```
{
    "POST_MESSAGES": [
        "Well done!",
        "See you soon."
    ]
}
```

### CHANGELOG
Liste des changements apportés par rapport à la version précédente du paquet.\
Ils doivent être indiqués sous la forme d'une liste de chaine de caractères.

Exemples:
```
{
    "CHANGELOG": [
        "* Added support for PS5 DualSense and Xbox Series X controllers to the HIDAPI controller driver",
        "* Added joystick functions to get additional information"
    ]
}
```

### INSTALL_DEV
Même chose que la section INSTALL excepté qu'ici seuls les dossiers et/ou fichiers spécifiques au développement (headers, static libs, docs).\
L'utilisateur choisira s'il veut installer les fichiers de développement au moment d'installer un paquet.\
Les mêmes règles sur les chemins d'accès décrites dans la section INSTALL s'appliquent ici.

### FILES_MODIFICATION
Liste de fichiers à modifier (et contenu à rajouter) après que le paquet ai été installé.\
Les modifications seton écrites entre des balises spéciales pour pouvoir être supprimées quand le paquet est désinstallé ou modifiées quand le paquet est mis à jour.

Exemples:
```
{
    "//FILES_MODIFICATION": "Rajoute plusieurs lignes dans S:user-startup",
    "FILES_MODIFICATION": {
        "S:user-startup": [
            ";For Ghostscript",
            "c:assign <>NIL: gs: \\"Sys:Applications_ext/Office/Ghostscript/\\"",
            "c:assign <>NIL: gs870: gs:",
            "c:assign <>NIL: gsfonts: gs:fonts",
            "c:assign <>NIL: Ghostscript: gs:",
            "c:assign <>NIL: gscache: t:"
        ]
    }
}
```

```
{
    "//FILES_MODIFICATION": "Rajoute plusieurs lignes dans S:user-startup et dans SYS:Prefs/Env-Archive/sys/filesystems.conf",
    "FILES_MODIFICATION": {
        "S:user-startup": [
            ";For Ghostscript",
            "c:assign <>NIL: gs: \\"Sys:Applications_ext/Office/Ghostscript/\\"",
            "c:assign <>NIL: gs870: gs:",
            "c:assign <>NIL: gsfonts: gs:fonts",
            "c:assign <>NIL: Ghostscript: gs:",
            "c:assign <>NIL: gscache: t:"
        ],
        "SYS:Prefs/Env-Archive/sys/filesystems.conf": [
            "DosType=0x4e544653 Filesystem=\\"L:NTFileSystem3G\\" Name=\\"NTFS\\""
        ]
    }
}
```

### SCREENSHOTS
Liste d'URL vers des images représentant des screenshot du paquet

### VIDEOS
Liste d'URL vers des vidéos relatives au paquet

### LINKS
Liste d'URL vers des sites de donation, du développeur, ...
