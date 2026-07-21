# Package Management

## `dpkg`

Debian Package Manager, also used for Ubuntu, is responsible for installing software packages (not dependencies). Those packages are distrubted as `.deb` files (compressed archive).

```bash
# manual install (after downloaded)
dpkg -i package.deb
# only works if dependencies are already installed

# remove package
dpkg -r package_name
```

## `apt`

Builds on top of `dpkg` and uses package sources (repositories). It connects to a central repository to check which packages are available and where to download.

`apt-get` has stable API (for shell scripts)
`apt` API and parameters might change (for user interfaces)

### updating package definitions

This means fetching the latest list of available packages from the repositories (which versions available, where to download etc.)

```bash
# getting updated packages
sudo apt update
sudo apt-get update

# installing software packages (and its dependencies)
sudo apt install package_name

# installing without dependencies
sudo apt install --no-install-recommends package_name

# uninstall a package
sudo apt remove package_name
```

`apt` remembers which packages were installed manually and which pkg were installed as dependencies

### managing upgrades

This is to install any available updates on our system. No old packages will be removed!

```bash
sudo apt upgrade

# installs new packages (if new dependecies)
sudo apt-get upgrade --with-new-pkgs

# bigger upgrade (uninstalls clashing dependencies if necessary)
sudo apt full-upgrade
sudo apt-get dist-upgrade

# uninstalls all dependencies that are no longer needed
sudo apt autoremove
sudo apt-get autoremove
```

## Package Repositories

The system reads the following files for its repositories:

- `/etc/apt/sources.list`
- Additional (3rd party) repositories:
  `/etc/apt/sources.list.d/*`

! IMPORTANT: those repositories provide a list with packages, their versions and so on. We must fully trust those repositories (they could even replace existing software on our system) !

### syntax of `sources.list`

```bash
Types: deb # deb contains binary packages (deb-src would be source code)
URIs: http://ports.ubuntu.com/ubuntu-ports/
Suites: noble noble-updates noble-backports # release code name / update type
Components: main restricted universe multiverse
# officially supported by Canonical: "main" (free) "restricted" (non-free software)
# community supported / 3rd party: "universe" (free) "multiverse" (non-free)
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg # path to the keyring that is used to verify the repository’s authenticity
```

### custom repositories

To add a new repository for our `apt` sources, create a new file in `/etc/apt/sources.list.d/`.
Also need to add the GPG key to our system for this repository (this will also verify updates to repository)

### Personal Package Archive (`ppa`)

community where users can provide repositories for others: https://launchpad.net/ubuntu/+ppas

```bash
# add a ppa
sudo add-apt-repository ppa:package_name
sudo apt update

# remove a ppa
sudo add-apt-repository --remove ppa:package_name
```

!Note: any software installed through a `ppa` will still have to be uninstalled manually, even if the `ppa` was removed.

## Dependency Management

Usually `apt` resolves dependency issues automatically. However, if there are any dependency conflicts, `apt` can try to "fix" them by identifying the inconsistencies and solving them via installing, upgrading or removing packages.

```bash
# list dependencies
sudo apt show program_name
sudo apt-chache show program_name

# conflict resolution
sudo apt install --fix-broken
sudo apt-get install -f

# re-run installations scripts
sudo dpkg-reconfigure program_name
```

## `snap`

This is another package manager, that helps to bridge the gaps of `apt` where for example all dependencies must be installed globally.

`snap` simply bundles an application with all its dependencies. This requires more disk space but it allows each application to have different dependencies and even be Linux distribution independent.
`snap` is a centralized repository where anybody can publish applications (author verification is needed) -> https://snapcraft.io
All `snap`-installed applications will be **mounted** in `/var/lib/snapd`

```bash
# update repository
snap refresh

# install a program
snap install program_name
```
