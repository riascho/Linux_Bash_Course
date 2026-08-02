# Linux Software Updates

Every 6 months a new ubuntu version gets released

**Before updating**

- make sure you have a backup of your data
- make sure you have enough (several GB) disk space available (see `df` command)
- make sure you have enough time in case something goes wrong
- wait a week or two after the release of a new version
- make sure all additional repositories support the new linux version
- have a backup drive available to boot from (ubuntu image on bootable USB drive), in case anything goes wrong

## Updating Ubuntu Version

1. Install all normal updates:

```bash
sudo apt update / sudo apt-get update
sudo apt full-upgrade / sudo apt-get dist-upgrade
```

2. reboot the system so the latest kernel is active

```bash
sudo reboot
```

3. make sure update manager is installed

```bash
sudo apt install update-manager-core
```

4. trigger the actual update

```bash
sudo do-release-upgrade

# modify /etc/update-manager/release-upgrades in case you're on the latest LTS and want a new minor version
Prompt=normal
```

- you can only upgrade one version at the time! (e.g. `LTS` -> `LTS` or `22.04` -> `22.10` -> `23.04`)
