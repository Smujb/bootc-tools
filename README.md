# Bootc Tools

## Installing

Clone this repository then sym link any of the files you want to use into `/usr/local/bin` if that is writeable on your system (otherwise, anywhere on the `PATH` that is). Note: I recommend putting them somewhere on `secure_path` if you use sudo for privelige escalation as all these scripts must be run as root.

```bash
sudo ln -s $(realpath bootc-db-diff) /usr/local/bin/bootc-db-diff
sudo ln -s $(realpath build-overlay-ext) /usr/local/bin/build-overlay-ext
```

## bootc-db-diff

```
usage: bootc-db-diff [-h] [-d {rollback,staged,overlay}]

Check packages that have been upgraded/added/removed between bootc composefs deployments

options:
  -h, --help            show this help message and exit
  -d, --deployment {rollback,staged,overlay}
                        which deployment to check the diff against
```

Find packages that have been upgraded/removed/added in between deployments with the ComposeFS backend. Currently only supports the `pacman` package manager but can theoretically support almost any.

## build-overlay-ext

>[!WARNING]
>Do not use a package manager with this script on a bootc system that does not have the package manager's database locked or pointed at a snapshot. Attempting to upgrade the system on a sysext is a terrible idea which ruins the whole point of atomic updates, and having packages out of sync with the base system will cause major breakages.

```
usage: build-overlay-ext [-h] [-f] [-c] [script]

Build a deployment-locked sysext out of a shell script. Builds against the booted and staged deployments by default.

positional arguments:
  script       filename of script to use

options:
  -h, --help   show this help message and exit
  -f, --force  rebuild the current deployment's overlay even if it already exists.
  -c, --clean  remove all extensions built by this script from /var/lib/extensions
```

Builds an overlay on the booted and (if it exists) staged deployments using systemd-sysext and a provided bash script. Intended for use alongside the Arch Linux Archive or similar projects, but is technically distro agnostic. Since the overlays are tied to deployments, this script must be run alongside `bootc update` to ensure the layered packages remain into the next deployment.

Do not use this alongside `bootc usroverlay` as this is not compatible with sysexts. Run `systemd-sysext refresh --mutable=ephemeral` to load up a mutable overlay that does not persist between reboots instead.
