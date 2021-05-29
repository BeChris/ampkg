# ampkg
Un gestionnaire de paquets pour les développeurs et utilisateurs de systèmes d'exploitation basés sur ou dérivés d'AmigaOS (AmigaOS classic, AmigaOS4, AROS, MorphOS)

Etant inspiré de Haiku ports, Gentoo Linux Portage, Archlinux Pacman et d'autres, 

ampkg peut être utilisé avec une interface graphique ou dans le terminal (en utilisant des paramètres en ligne de commande).\

Les utilisateurs de cet outil peuvent être divisés en trois catégories:
1. Pour les développeurs, ampkg:
   1. Uniformise la construction de logiciels en se basant sur des conventions
   2. Facilite l'utilisation de dépendances obligatoires et/ou optionelles
   4. Etant basé sur des conventions, facilite la génération de paquets logiciels installables et l'upload pour approbation par des empaqueteurs

2. Pour les empaqueteurs, ampkg peut être utilisé pour:
   1. Accepter ou refuser les paquets soumis par les développeurs
   2. Une fois accepté ce paquet installable est rendu disponible aux utilisateurs pour installation automatique au travers de ampkg

3. Pour les utilisateurs, ampkg peut être utilisé pour:
   1. Lister/installer/mettre à jour/désinstaller des paquets

## Le cas des développeurs
**Les développeurs doivent avoir le SDK MorphOS installé.**

Imaginons un développeur développant ou portant un logiciel existant pour MorphOS.

Il écrit des fichiers .txt, .readme, .download, .patch, .build, ... dans lesquels il peut indiquer beaucoup d'informations à propos du logiciel porté mais aussi comment le construire.\
Il utilise ampkg pour construire, à partir des fichiers qu'il vient d'écire, un paquet binaire, peut l'installer et le tester sur son système.\
Une fois testé, il peut utiliser ampkg pour désinstaller le paquet de test.

Quand il est satisfait par le paquet, il utilise une page dédiée sur le serveur de téléchargement permettant la soumission d'un paquet.\
Il joint le paquet généré mais également tous les fichiers (.txt, .readme, .download, .patch, .build, ...) s'ils sont nécessaires.\
Grace à ces fichiers un autre développeur pourra recompiler le paquet sans problèmes.

## Le cas des modérateurs
**Les modérateurs n'ont pas besoin du SDK MorphOS.**

Processus à détailler comment le modérateur reçoit les paquets soumis par les développeurs.

Quand il récupère un paquet:
1. Il l'installe et le teste sur son sytème.
2. S'il accepte le paquet, il le déplace sur le serveur de téléchargement dans le dossier qui correspont à la catégorie.
3. Par contre, s'il refuse le paquet, il peut échanger avec le développeur par email jusqu'à ce que le paquet soit OK.

## Le cas des utilisateurs
**Les utilisateurs n'ont pas besoin du SDK MorphOS.**

Un utilisateur utilise ampkg pour obtenir la liste des paquets disponible ou pour savoir des paquets déjà installés peuvent être mis à jour.

Il peut alors installer de nouveaux paquets, mettre à jour ou désinstaller ceux déjà installés.

Dans le cas d'une installation ou d'une mise à jour, ampkg télécharge automatiquement le(s) paquet(s) (et ses/leurs dépendances obligatoires) et les installe.\
Si le(s) paquet(s) indique quelques dependances optionelles elles sont affichées à l'utilisateur qui peut choisir celle(s) qu'il veut installer en plus.

## Organisation sur le serveur de téléchargement
L'organisation et le nommage des fichiers répond à une convention qu'il faut suivre pour qu'un paquet soit correctement référencé.\
Ainsi, un paquet est une archive dont le nom doit correspondre à la convention : &lt;nom du paquet&gt;_&lt;version&gt;.&lt;extension&gt;\
C'est cette archive qui sera décompressée sur le poste de l'utilisateur lors de l'installation du paquets.\
Dans le même dossier où se trouve l'archive du paquet, d'autres fichiers avec la même racine (&lt;nom du paquet&gt;_&lt;version&gt;) mais une extension spécifique par type de fichier.\
Chacun de ces fichiers contient des informations additionelles et relatives au paquet (le nom du développeur, ...)


## Liste des extensions communes à tout type de paquet (déjà compilé ou pas)

Ci-dessous la liste des extensions de fichiers admises et leur signification:

### .txt
Contient une courte description du paquet en 100 caractères maximum et dans la langue correspondante au suffixe _LANG utilisé.\
Si ce fichier est absent alors une description par défaut "No description" sera affichée.


### .readme
Contenu pouvant être affiché à la demande de l'utilisateur ou avant l'installation du paquet.
Si ce fichier est absent alors aucun contenu ne sera affiché.


### .url
Lien vers la page d'accueil du projet et dans la langue correspondante au suffixe _LANG utilisé.\
Si ce fichier est absent alors aucun lien ne sera affiché.


### .pre_messages
Liste des messages à afficher avant l'installation/mise à jour du paquet.\
Ces messages sont concaténés et affichés en un seul bloc.\
Pour démarrer un nouveau bloc, incluez une chaine de caractère "@@@@" dans la liste.

Exemples:
```
Wellcome to this package
```


### .post_messages
Liste des messages à afficher après l'installation/mise à jour du paquet.\
Ces messages sont concaténés et affichés en un seul bloc.\
Pour démarrer un nouveau bloc, incluez une chaine de caractère "@@@@" dans la liste.

Exemples:
```
Well done!
See you soon.
```


### Localisation des chaines de description
Dans les fichiers .txt, .readme, .url, .pre_messages et .post_messages présentés ci-dessus il est possible d'indiquer du contenu en fonction de la langue du système.\
Pour se faire, il faut compartimenter le fichier en sections, chaque section correspondant à une langue (le code ISO_639-1 de langue en 2 lettres majuscules).\
Une section s'écrit [section].\
Si dans un fichier il n'y a pas de section, on considère que ça correspond à la langue Anglaise.\
Enfin, si la section correspondante à la langue du système n'est pas trouvée dans le fichier alors le contenu de la section [EN] est pris en compte (ou le contenu hors section s'il n'y en a pas).

Exemples:
```
[EN]
Contenu utilisé quand la langue du système est Anglais
[FR]
Contenu utilisé quand la langue du système est Francais
```

```
Contenu utilisé quand il n'y pas de section correspondante à la langue du sytème.
[FR]
Contenu utilisé quand la langue du système est Francais.
[EN]
Contenu utilisé quand la langue du système est Anglais.
[DE]
Contenu utilisé quand la langue du système est Allemand.
```


### .jpg ou .png
Fichiers screenshot de l'application.
**A NOTER**: il peut y avoir également les fichier _x.jpg ou _x.png (où 1 <= x <= 9) pour indiquer d'autres screenshots.


### .video
Liens vers des vidéos de l'application.


### .donation
Lien vers le site de donation du développeur (paypal, patreon, ...)


### .install
Quand un paquet est installé, l'action par défaut est que son archive est décompressée dans le dossier $(INSTALL_DIR)/$(CATEGORY).\
S'il faut faire autre chose alors les commandes à exécuter doivent être écrites dans ce fichier.


### .install_dev
Même chose que l'extension .install excepté qu'ici seuls les dossiers et/ou fichiers spécifiques au développement (headers, static libs, docs).\
L'utilisateur choisira s'il veut installer les fichiers de développement au moment d'installer un paquet.\


### .uninstall
Quand un paquet est désinstallé, l'action par défaut est que son dossier est supprimé de $(INSTALL_DIR)/$(CATEGORY).\
S'il faut faire autre chose alors les commandes à exécuter doivent être écrites dans ce fichier.


### .exe
Dans ce fichier est indiqué le chemin (relatif par rapport au dossier d'installation) vers l'exécutable principal du logiciel.\
Si ce fichier existe alors l'utilisateur a la possibilité d'exécuter le logiciel à tout moment ou après l'installation.


### .requires
Ce fichier contient les autres paquets nécessaires au bon fonctionnement de ce paquet.\
Il doit y avoir un paquets par ligne et il est possible de préciser une restriction de version.\
Si c'est une dépendances nécessaire à la contruction du paquet alors elle doit être préfixée par BUILD:
S'il est nécessaire que la machine soit sous une version spécifique de MorphOS alors il faut utiliser le nom spécial MorphOS

Exemples:
```
sdl2 >= 2.0.10
sdl2_ttf >= 2.0.9 <= 2.0.11
```

```
BUILD:sdl2-dev = 2.0.14
```

```
MorphOS >= 3.15
```

### .modifications
Liste de fichiers à modifier (et contenu à rajouter) après que le paquet ai été installé.\
Les modifications seton écrites entre des balises spéciales pour pouvoir être supprimées quand le paquet est désinstallé ou modifiées quand le paquet est mis à jour.\
Le fichier à modifier doit être indiqué dans une section [nom du fichier].\
Les lignes qui suivent correspondent à ce qu'il faut rajouter dans le fichier.\
Il est possible de cummuler les modifications de plusieurs fichiers.

Exemples:
Rajoute plusieurs lignes dans S:user-startup
```
[S:user-startup]
;For Ghostscript
c:assign <>NIL: gs: "Sys:Applications_ext/Office/Ghostscript/",
c:assign <>NIL: gs870: gs:
c:assign <>NIL: gsfonts: gs:fonts
c:assign <>NIL: Ghostscript: gs:
c:assign <>NIL: gscache: t:
```

Rajoute plusieurs lignes dans S:user-startup et dans SYS:Prefs/Env-Archive/sys/filesystems.conf
```
[S:user-startup]
;For Ghostscript
c:assign <>NIL: gs: "Sys:Applications_ext/Office/Ghostscript/",
c:assign <>NIL: gs870: gs:
c:assign <>NIL: gsfonts: gs:fonts
c:assign <>NIL: Ghostscript: gs:
c:assign <>NIL: gscache: t:
[SYS:Prefs/Env-Archive/sys/filesystems.conf]
DosType=0x4e544653 Filesystem="L:NTFileSystem3G" Name="NTFS"
```


### Exécution de commandes
Dans les fichiers .install, .install_dev et .uninstall présentés ci-dessus il est possible d'effectuer certaines opérations sur des dossiers et/ou fichiers.\
Il est possible d'indiquer une commande à exécuter par ligne.\
Dès qu'une commande échoue l'installation ou la désinstallation est interrompue.\
Pour des raisons de sécurité il n'est pas possible d'exécuter des commandes AmigaDOS classiques mais plutôt un ensemble restreint.\
Chacune de ces commandes crée les dossiers de destination si nécessaire.

Voici la liste des commandes utilisables:
* EXTRACT:&lt;fichier archive&gt; TO:&lt;dossier destination&gt;
Décompresse &lt;fichier archive&gt; dans le dossier de destination &lt;dossier destination&gt;
* COPY:&lt;source&gt; TO:&lt;dossier destination&gt;
Copie le dossier ou fichier &lt;source&gt; dans le dossier de destination &lt;dossier destination&gt;\
Si &lt;source&gt; est un dossier alors tout son contenu est copié.
* COPY:&lt;motif&gt; TO:&lt;dossier destination&gt;
Copie le(s) dossier(s) ou fichier(s) qui correpond(ent) à &lt;motif&gt; dans le dossier de destination &lt;dossier destination&gt;
* RENAME:&lt;ancien nom&gt; TO:&lt;nouveau nom&gt;
Renomme le dossier ou fichier &lt;ancien nom&gt; en &lt;nouveau nom&gt;
* DELETE:&lt;source&gt;
Supprime le dossier ou fichier &lt;source&gt;
* DELETE:&lt;motif&gt;
Supprime le(s) dossier(s) ou fichier(s) qui correpond(ent) à &lt;motif&gt;

**Nota**: un motif est du genre *.jpg, *.*, dossier/*.png, ...

Commandes à rajouter?


## Liste des extensions additionelles pour un paquet qui doit être compilé

### .download
Ce fichier contient la liste des fichiers à télécharger et dépôts git à cloner.\
Chaque lien doit se terminer par la séquence #&lt;informations additionelles&gt;.\
Cette information additionelle doit être:
* Pour le lien vers un fichier à télécharger : le MD5 du fichier.
* Pour un dépôt git à cloner : le tag, la branche ou le SHA1 auquel le dépôt sera cloné.
L'URL d'un dépôt git doit commencer par git+
Si le lien vers un fichier commence par file:// alors il sera téléchargé à partir d'un chemin relatif au fichier .download.

Exemples:
```
https://www.libsdl.org/release/SDL2-2.0.14.tar.gz#76ed4e6da9c07bd168b2acd9bfefab1b
git+https://github.com/libsdl-org/SDL.git#release-2.0.14
file://SDL2-2.0.14.patch#61206a217abb1377cf1eef59a8450f8e
```


### .patch
Ce fichier contient la liste des fichiers de patch à appliquer avant de lancer la compilation.\
Les patch sont appliqués dans le même ordre qu'ils apparaissent dans le fichier.

Exemples:
```
SDL2-2.0.14.patch
```


### .build
Ce fichier contient la liste des instructions à exécuter.\
Ici on considère que l'utilisateur a le SDK installé.
**IMPORTANT** : ne pas exécuter des commandes qui copient/génèrent des fichiers ailleurs que dans le dossier de construction du paquet

Exemples:
```
make -C SDL2
make -C SDL2 headers
```


### Vérifier les capacités processeur
Dans les fichiers .install, .install_dev, .uninstall, .exe, .requires, .modifications, .download, .patch et .build présentés ci-dessus il est possible d'indiquer du contenu en fonction des capacités du processeur.\
Pour se faire, il faut compartimenter le fichier en sections, chaque section correspondant à une ou plusieurs capacités séparés par des espaces.\
Une section s'écrit [section].\
Si une section correspond exactement aux capacités du processeur c'est cette section (et uniquement celle là) dont le contenu est pris en compte.\
Si dans un fichier il n'y a pas de section, Le contenu est applicable quelque soient les capacités du processeur.

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
Contenu utilisé dans tous les cas
[FPU]
Contenu utilisé seulement si le processeur a une FPU (qu'il ai ou pas une unité ALTIVEC).
```

```
[FPU ALTIVEC]
Contenu utilisé seulement si le processeur a une FPU et une unité ALTIVEC.
```
