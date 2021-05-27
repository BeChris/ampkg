# Files format

## Package file
A package file is what is generated from a recipe file and stored on the server.
This is an archive (tar.7z, lha, other ?) composed:
* Directories and/or files indicated in recipe "PACKAGED" section.
* A meta.json file at the root of the archive composed of following sections:
  * SIZE : total size of all files included in the package
  * MD5_SUMS : a dictionnary containing, for each file include in the package, its name and its MD5 sum
  * PROVIDES : content of recipe PROVIDES section
  * INSTALL : content of recipe INSTALL section
  * RUN_MANDATORY : content of recipe RUN_MANDATORY section
  * RUN_OPTIONAL : content of recipe RUN_OPTIONAL section
  * PRE_MESSAGES : content of recipe PRE_MESSAGES section
  * POST_MESSAGES : content of recipe POST_MESSAGES section
  * INSTALL_DEV : content of recipe INSTALL_DEV section

## Database file
A database file is a compressed JSON file containing list of all packages hosted on a server.\
It shall be a dictionnary whose keys are package name (+ version information) and values are a subdictionnary with basename to downloadable package and recipe files.\
Paths of downloadable package and recipe files are built by adding ARCH.package or .recipe suffixes to the basename.\
There can also be RUN_MANDATORY and RUN_OPTIONAL values with the same values as found in the corresponding package.


Example:
```
{
    "sdl2": {
        "2.0.12-2": {
            "BASENAME": "Development/Library/sdl2-2.0.12-2"
        },
        "2.0.14-1": {
            "BASENAME": "Development/Library/sdl2-2.0.14-1"
        }
    },
    "sdl2-devel": {
        "2.0.14-1": {
            "BASENAME": "Development/Library/sdl2-2.0.14-1"
        }
    }
    "sdl2_image": {
        "2.0.5-1": {
            "BASENAME": "Development/Library/sdl2_image-2.0.5-1",
            "RUN_MANDATORY": {
                "sdl2": "2.0.14"
            }
        }
    },
    "Hollywood:Player": {
        "9.0-1": {
            "BASENAME": "Development/Hollywood/HollywoodPlayer-9.0-1"
        }
    }
}
```


## On server
At the root of the server there shall one or more database file (one per supported architecture-OS combination).\
And for each database file its associated MD5 sum.\

For exemple, for a server hosting only MorphOS packages, we shall find in root directory:
* ppc-morphos.db : the database file
* ppc-morphos.db.md5 : the database file MD5 sum

For a server additionaly hosting AROS packages, we shall also find in root directory:
* x86-aros.db : the database file
* x86-aros.db.md5 : the database file MD5 sum


Then all expected package/recipe files in their respective directories with their associated MD5 sum:
(MD5 sums could used on client to keep a cache of already downloaded files and avoid downloading again some of them by checking their MD5)
* Development/Library/sdl2-2.0.12-2.ppc-morphos.package
* Development/Library/sdl2-2.0.12-2.ppc-morphos.package.md5
* Development/Library/sdl2-2.0.12-2.recipe
* Development/Library/sdl2-2.0.12-2.recipe.md5
* Development/Library/sdl2-2.0.14-1.ppc-morphos.package
* Development/Library/sdl2-2.0.14-1.ppc-morphos.package.md5
* Development/Library/sdl2-2.0.14-1.recipe
* Development/Library/sdl2-2.0.14-1.recipe.md5
* Development/Library/sdl2_image-2.0.5-1.ppc-morphos.package
* Development/Library/sdl2_image-2.0.5-1.ppc-morphos.package.md5
* Development/Library/sdl2_image-2.0.5-1.recipe
* Development/Library/sdl2_image-2.0.5-1.recipe.md5
* Development/Hollywood/HollywoodPlayer-9.0-1.ppc-morphos.package
* Development/Hollywood/HollywoodPlayer-9.0-1.ppc-morphos.package.md5
* Development/Hollywood/HollywoodPlayer-9.0-1.recipe
* Development/Hollywood/HollywoodPlayer-9.0-1.recipe.md5
* ...


## On user computer
A cache directory (path and maximum size shall be configurable) where:
* All downloaded files are stored.
A data directory where:
* ppc-morphos.db is downloaded from the server.
* An SQLite database containing, for all installed packages:
  * Version
  * For each file contained in the package : its absolute filename and its MD5
