# Software components

## Constants
A Table containing all constants.\
For example :
* INSTALL_DIR (base directory where all packages are installed)
* CACHE_DIR (cache directory used by Network downloader)
* DATA_DIR (directory where local database and other files are stored)


## Config
A Table containing all configuration.\
For example :
* BASE_URL (base directory from which all packages are downloaded)


## Network
Used to download/upload files.\
It transparently use CACHE_DIR to store downloaded files.\
Moreover, it first try to first download MD5 file, compare to local one and download only if necessary

Available functions:
* SetCacheSize(bytes) : set maximum allowed size for cache directory.\
If the cache size is reached when downloding a file, older files are removed from the cache directory.
* ClearCache() : clear cache directory.
* StartDownload(url$[, callback]) : Start download file at url$ and returns an handle to an internal downloader object in case you want to cancel it.
Optional callback is called from time to time to indicate download progress.
callback function must have following profile:
callback(Count, Total, Finished)
Count : Contains the number of bytes that have already been downloaded.
Total : Contains the size of the file being downloaded.
Finished : True if download is finished, False otherwise.
* CancelDownload(handle) : Cancel a download whose handle has been returned by StartDownload().
* StartUpload(file$, url$[, table]) : Upload file$ at url$ and returns an handle to an internal uploader object in case you want to cancel it.
Use table.user and table.password if they are specified.
Optional table.callback is called from time to time to indicate download progress.
table.callback function must have following profile:
callback(Count, Total, Finished)
Count : Contains the number of bytes that have already been downloaded.
Total : Contains the size of the file being downloaded.
Finished : True if download is finished, False otherwise.
* CancelUpload(handle) : Cancel an upload whose handle has been returned by StartUpload().


## Database
Store all available packages, their status (installed version, remote version).

Available functions:
* StartDownload([callback]) : Start download a new copy of remote database and merge it with local one.\
It uses Downloader.Download() to trigger the download.
* Packages() : Return a key/pair dictionnary of all packages.\
Key is the packagename, value is a subdictionnary containing following keys:
  * Category
  * LocalVersion : version at which package is installed. Empty if not installed.
  * RemoteVersion : version available remotely. Empty if no (more) available. 
* Dependencies(name$, version$, with_dev) : Returns a list of all packages to install in order to successfully install package $name at version $version.
Packages shall be installed in the same order as in returned list.
If with_dev is True then development packages are also taken into account.
* RequiredBy(name$, version$) : Returns a list of all packages which require package $name at version $version.


## Packages
Provide functions related to packages handling

Available functions:
* StartInstall(name$, version$, with_dev[, callback]) : Start installing package $name at version version$ and returns an handle to an internal installer object in case you want to cancel it.
If with_dev is True then development files are installed as well (if provided by the package).
Optional callback is called from time to time to indicate installation progress.
callback function must have following profile:
callback(Count, Total, Finished)
Count : Contains the number of files that have already been installed.
Total : Contains the total number files to install.
Finished : True if installation is finished, False otherwise.
Nota : StartInstall doesn't take care of installing the package mandatory or optional dependendies!
* CancelInstall(handle) : Cancel an installation whose handle has been returned by StartInstall().
* StartUnInstall(name$, version$, with_dev[, callback]) : Start uninstalling package $name at version version$ and returns an handle to an internal uninstaller object in case you want to cancel it.
If with_dev is True then development files are uninstalled as well (if provided by the package).
Optional callback is called from time to time to indicate uninstallation progress.
callback function must have following profile:
callback(Count, Total, Finished)
Count : Contains the number of files that have already been uninstalled.
Total : Contains the total number files to uninstall.
Finished : True if uninstallation is finished, False otherwise.
Nota : StartUnInstall doesn't take care if other packages depend on uninstalled package!
* CancelUnInstall(handle) : Cancel an uninstallation whose handle has been returned by StartUnInstall().



## Utils
Several utility functions.

Available functions:
* Walk(dir$, callback) : Walk recursively through dir$, calling callback function on each directory and file encountered.\
callback shall accept one parameter which is the same table as the one returned by NextDirectoryEntry function.\
And callback must return True if the walk shall continue, False otherwise.


## Versions
Provides utility functions around version checks.

Available functions:
* IsValid(version$) : Returns whether given version is a valid version (i.e is something like x-p or x.y-p or x.y.z-p where x, y, z and p are numbers)
* Compare(version1$, version2$) : Returns -1 if version1$ < version2$, 0 if version1$ = version2$, 1 if version1$ > version2$


## Requesters
Provides functions used to display request and get user answer

Available functions:
* YesNo(title$, message$) : Open a requester with title$, displaying message$ and Yes|No buttons.
Returns True use user selected Yes, False otherwise.
* OkCancel(title$, message$) : Open a requester with title$, displaying message$ and Ok|Cancel buttons.
Returns True use user selected Ok, False otherwise.

