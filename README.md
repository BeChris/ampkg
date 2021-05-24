# ampkg
A package manager tool for developpers and users of operating systems based or derived from AmigaOS (AmigaOS classic, AmigaOS4, AROS, MorphOS)

Being inspired by Haiku portage, Gentoo Linux Portage, Archlinux Pacman and so on, 

ampkg can be used with a Graphic User Interface or via the terminal (using commandline arguments).\
People using the tool can be split in three different categories:
1. For the developpers, ampkg:
   1. Formalize software building by using recipe files
   2. Ease writing of recipe files
   3. Make usage of mandatory and/or optional dependencies easy
   4. Based on recipe files, ease generation of installable packages and upload for approval by packagers

2. For the packagers, ampkg can be used to:
   1. Accept or refuse recipe and associated package(s) submited by developpers
   2. Upon acceptation a package is available for users to be installed on their system

3. For the users, ampkg can be used to:
   1. Enable packages listing/installation/upgrade/uninstallation

## What is a recipe file?
A recipe file enable developpers to automatically download, (enventually) patch, compile and generate a binary package from a given software.

A GitHub repository will contain recipe files organized in subdirectories : the directories in which a recipe is located gives it category.\
Categories are those from morphos-storage site.\
Let's call this GitHub repository morphos-recipes.

In the GitHub repository there will be possible to find the same package but at different versions.\
Example : sdl2-2.0.3 and sdl2-2.0.4

Packages generated from those recipe files will be hosted on morphos-storage server.

A recipe file must be named after the software to be built, written in JSON format and have a .recipe extension.\
Example : sdl2-2.0.3.recipe or sdl2-2.0.4.recipe

## Developpers use cases
**Developpers must have MorphOS SDK installed on their system.**

Let's imagine a developper developping or porting an existing software to MorphOS.

He writes a recipe file in which he can indicates a lot of informations about the ported software but also how to build it.\
He uses ampkg to build, from the recipe file he just wrote, a binary package, install it on his system and test it.\
Upon tested, he is free to use ampkg to uninstall the test package.

When he is satisfied by his recipe file he must make a recipe submission.\
To do so he must:
1. To be done only once : Fork morphos-recipes repository within GitHub and clone his fork locally on his hard drive
2. Put the recipe file in relevant directory architecture providing the recipe file category (for example : in Network/Streaming)
3. Commit and push the recipe file to his morphos-recipes fork
4. Within GitHub web interface create an Issue and submit a pull request assciated to this Issue.

## Packagers use cases
**Packagers must have MorphOS SDK installed on their system.**

A packager regularly look at morphos-recipes repository whether there are new pull requests (or he is automatically notified by email).

When there is a pull request:
1. He downloads the recipe file and use ampkg to build, from the recipe file he just downloaded, a binary package, install it on his system and test it.
2. If the package seems to be correct the packager can accept the pull request within GitHub web interface (I suppose that it triggers a merge of developper commit on master branch)
He must also upload the generated package on morphos-storage using once again ampkg tool.
3. Instead, if the package seems to be broken, he can exchange with the developper using GitHub web interface on relevant Issue until the package is OK.

## End users use cases
**End users don't need MorphOS SDK installed on their system.**

An end user uses ampkg to get list of available packages or whether already installed packages can be upgraded.\
He can then install new package(s), upgrade or uninstall already installed ones.

In case of installation or upgrade, ampkg automatically download relevant package(s) (and their MANDATORY dependencies) and install them on user system.\
If the package indicates some OPTIONAL depedencies they are show to the user and he can choose to install them or not.

## More about recipe files

They must contain mandatory sections and eventually optional sections.\
Each section can contain either:
1. A string (one line or spead on several lines)
2. A dictionnary containing key/value pairs
3. TO BE CONTINUED

Several choices are show like this : (choice1|choice2|choice3)\
Value optional part are shown like this : [optional part]

## Using comments
JSON fileformat not officially supporting comments, the following convention will be followed:\
To add a comment to a section, a specific section with the same name (but starting with //) will be used.

Examples:
```
{
    "//SUMMARY": "This a comment for SUMMARY section",
    "SUMMARY": "This is the real SUMMARY section"
}
```

## Using aliases
For any string value it is possible to use aliases : their value will be automatically substituted by ampkg.\
Referencing an alias is done like this : $(alias)\

Below list of available aliases:

### CATEGORY
Replaced by category of package to be installed/upgraded/uninstalled.

## Mandatory sections
### SUMMARY
Contains the package short description in 100 characters maximum.

Examples:
```
{
    "SUMMARY": "A short summary."
}
```

### ARCH
Contains a value matching pattern (arm|ppc|x86|m68k)-(aros|amigaos|morphos) [version restrictions] to indicate for which architecture the package is generated.\
The special **any** value can be used to indicate the package can be installed and used on any architecture and any OS.

For the moment only **ppc-morphos** and **any** will be supported.

Examples:
```
{
    "//ARCH": "usable on any PowerPC based computers running MorphOS at any version",
    "ARCH": "ppc-morphos"
}
```

```
{
    "//ARCH": "usable on any PowerPC based computers running MorphOS at version >= 1.5",
    "ARCH": "ppc-morphos >= 1.5"
}
```

```
{
    "//ARCH": "usable on any PowerPC based computers running MorphOS at version >= 1.5 and <= 2.0",
    "ARCH": "ppc-morphos >= 1.5 <= 2.0"
}
```

```
{
    "//ARCH": "usable on any architecture (for example for data only packages)",
    "ARCH": "any"
}
```


### URL
A list of link(s) to any file to be downloaded or to git repository(ies) to be cloned.\
Each link to a file must be associated to the MD5 of the file.\
And each git repository must be associated a tag, branchname or SHA1 at which the repository will be cloned.\
A git repository URL must starts with git+
If the link to the file starts with file:// then it will be downloaded from a path relative to the recipe file.

Examples:
```
{
    "//URL": "An archive an a patch file to be downloaded with their associated MD5 sum",
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
{
    "//URL": "A git repository to be cloned at release-2.0.14 tag",
    "URL": [
        {
            "git+https://github.com/libsdl-org/SDL.git": "release-2.0.14"
        }
    ]

}
```

### PROVIDES
List of components (with their version) provided by the package.\
They can be optional or mandatory prerequisites to build or to run other packages (refer to RUN_MANDATORY, RUN_OPTIONAL, BUILD_MANDATORY and BUILD_OPTIONAL sections).

Examples:
```
{
    "PROVIDES": [
        {
            "sdl2": "2.0.14-1"
        }
    ]
}
```

**IMPORTANT**: it may be possible, for the same upstream package version (in this case sdl2 2.0.14), to evolve the corresponding recipe file.\
In this case, the last part in version value (here the -1 in 2.0.14-1) must be increased everytime the recipe evolves but provides the same
upstream package version.


### PACKAGED
This section is used to provide a list of directories and/or files from downloaded files or cloned git repositories (see _URL_ section above).\
Those directories and/or files will be included in generated package.

Examples:
```
{
    "//PACKAGED": "When the package is generated include everything from the SDL2 directory",
    "PACKAGED": [
        "SDL2"
    ]
}
```

### INSTALL
This section is used to provide a list of directories and/or files from downloaded package.\
Those directories and/or files will be copied on user system when the package is installed or upgraded.
They will be deleted when the package is removed.

**IMPORTANT**: paths ending with ':' or '/' are considered to be destination directories.\
Otherwise they are considered to be destination files.

Examples:
```
{
    "//INSTALL": "At installation, copy from package SDL2/README.txt file into user Docs: and all files from SDL2/Libs/ into user Libs:",
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
A list of persons maintaining this package.\
Each person shall be indicated as "Fullname &lt;email address&gt;".\
Each time there is a new maintainer it is encouraged to put him at first position in the list.


## Optional sections
### HOMEPAGE
Link to the project homepage

Examples:
```
{
    "HOMEPAGE": "https://www.libsdl.org/index.php"
}
```

### PATCHSET
List of one or more patch files to be applied to the archive after it has been downloaded and uncompressed and before it is compiled.\
Patch are applied in the same order they appear in the list.

Examples:
```
{
    "PATCHSET": [
        "patch1.txt",
        "patch2.txt"
    ]
}
```

### BUILD_MANDATORY
List of packages which **must** be installed on developer system in order to be able to compile the package associated to current recipe.\
For each mandatory package an optional [version restrictions] can be indicated.\
If no package matching this restriction is found then the package won't be installed.

Examples:
```
{
    "//BUILD_MANDATORY": "The package to be compiled absolutly needs sdl2 at version >= 2.0.10 and hollywood compiler at version 8.0",
    "BUILD_MANDATORY": [
        "sdl2-devel >= 2.0.10",
        "hollywood:compiler = 8.0",
    ]
}
```

### BUILD_OPTIONAL
Same as BUILD_MANDATORY except packages provided in the list are optional and not mandatory : if one of them is not found package building is still executed

### RUN_MANDATORY
List of packages which **must** be installed on user system in order to be able to execute binaries included in the package to be installed.\
For each mandatory an optional version restriction can be indicated (if no package match this restriction is found then the package won't be installed).

Examples:
```
{
    "//RUN_MANDATORY": "The package to be installed absolutly needs sdl2 at version >= 2.0.10 and sdl2_ttf at version between 2.0.9 and 2.0.11 inclusive",
    "RUN_MANDATORY": [
        "sdl2 >= 2.0.10",
        "sdl2_ttf >= 2.0.9 <= 2.0.11"
    ]
}
```

### RUN_OPTIONAL
Same as RUN_MANDATORY except packages provided in the list are optional and not mandatory.

### BUILD
List of steps to follow in order to generate the package.\
Each string in the list is a command to execute and commands are executed in the same order as they appear in the list.\
If a command fails (return code <> 0) the package is not built.

Examples:
```
{
    "//BUILD": "Build SDL2 in two steps using make each time",
    "BUILD": [
        "make -C SDL2",
        "make -C SDL2 headers"
    ]
}
```

### PRE_MESSAGES
List of messages to be displayed before package installation/upgrade is done.\
They are all concatenated and displayed in one message bloc.\
To start a new bloc, include a string "@@@@" in the message list.\
If a message starts with file:// then the content of file path given after file:// is displayed.

Examples:
```
{
    "PRE_MESSAGES": [
        "Wellcome to this package"
    ]
}
```

### POST_MESSAGES
List of messages to be displayed after package installation/upgrade is done.\
They are all concatenated and displayed in one message bloc.\
To start a new bloc, include a string "@@@@" in the message list.\
If a message starts with file:// then the content of file path given after file:// is displayed.

Examples:
```
{
    "POST_MESSAGES": [
        "Well done!",
        "See you soon."
    ]
}
```

### CHANGELOG
List of changes compared to previous package version.\
Must be indicated as a list of strings.

Examples:
```
{
    "CHANGELOG": [
        "* Added support for PS5 DualSense and Xbox Series X controllers to the HIDAPI controller driver",
        "* Added joystick functions to get additional information"
    ]
}
```

### INSTALL_DEV
Same as INSTALL section except that it shall indicates only files specific to development files (headers, static libs, docs).\
User will choose whether he wants to install development files when installing a package.\
The same rules on paths formating found in INSTALL section apply here also.

### SCREENSHOTS
List of URL to images representing screenshots of the package

### VIDEOS
List of URL to videos related to the package

### LINKS
List of URL to donation sites, developper site, ...
