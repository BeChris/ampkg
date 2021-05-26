# Needed features

In lists below, top items have the greatest priority.

In parenthesis, minimum role autorised to do the actions given:
- A server admin has higher priority than a developper
- A developper has higher priority than a user
- A user has the lowest priority


## Cache management
- [ ] Clean cache directory (user)


## Packages management
- [ ] Detect already installed packages with their associated version (user)
- [ ] Get list of available packages (on the server) with their associated version (user)
- [ ] Install a package available on the server with its associated dependencies (user)
- [ ] Upgrade a package available on the server and its associated dependencies (user)
- [ ] Uninstall a package and its associated dependencies (user)
- [ ] Check files installed by a package are not missing/damaged (user)
- [ ] Send a request related to a given package to indicate it did not (un)install correctly, doesn't work as expected, a new version of the package shall be proposed, ... (user)
- [ ] Install/upgrade a package from a locally generated package file.


## Recipe file
- [ ] Check whether a recipe file is valid (no JSON error, no missing mandatory sections, mandatory dependencies are existing, ...) (user)
- [ ] Submit a recipe file (user)
- [ ] Generate from scratch or edit an existing recipe file (user)


## Package building
- [ ] Build a package from a recipe file (user)


## Server admin
- [ ] Upload a package and its associated recipe file to a server (server admin)
- [ ] Delete a package and its associated recipe file from a server (server admin)

