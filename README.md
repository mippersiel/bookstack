# Bookstack Arch Linux AUR package

A simple, self-hosted, easy-to-use platform for organizing and storing information

## Info

This `PKGBUILD` is based on [C0rn3j's AUR package](https://aur.archlinux.org/packages/bookstack).

## Modifications

- Setup the `http` user and group ID directly in package instead of relying on `tempfiles.d`
- Moved all writable paths to `/var/lib` removing the requirement of overriding `php-fpm`'s service file to allow writes to `/usr` paths
- Cosmetic changes in `PKGBUILD`