# shellcheck disable=2148,2034,2164,2154
# Maintainer: Matthieu Ippersiel <https://github.com/mippersiel>
# Maintainer: Martin Rys <https://rys.rs/contact>

pkgname=bookstack
_camelname=BookStack
pkgver=26.03.3
pkgrel=1
pkgdesc='A simple, self-hosted, easy-to-use platform for organizing and storing information'
arch=('any')
url="https://github.com/BookStackApp/BookStack"
license=('MIT')
# https://www.bookstackapp.com/docs/admin/installation/
depends=(
  'php'
  'php-gd'
)
optdepends=(
  'php-apache: webserver and a PHP implementation'
  'php-fpm: fastCGI'
  'mariadb: database'
  'nginx: webserver'
)
makedepends=('composer')
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/BookStackApp/BookStack/archive/v${pkgver}.tar.gz")
sha256sums=('3c21785a600bcf651caa15c4219b5089a492f57e774c0c4f5be3af09155b17ac')
options=('!strip')
backup=("etc/webapps/${pkgname}/config.env")

# UID and GID = 33 : Ref. https://wiki.archlinux.org/title/DeveloperWiki:UID_/_GID_Database
_var_files_owner=http
_var_files_group=http

package() {
  pushd "${srcdir}/${_camelname}-${pkgver}" > /dev/null
  COMPOSER_ALLOW_SUPERUSER=1 composer install --no-dev --ignore-platform-reqs --no-interaction
  popd > /dev/null

  # Install application
  install --directory "${pkgdir}/usr/share/webapps/${pkgname}" "${pkgdir}/etc/webapps/${pkgname}"
  cp --recursive "${srcdir}/${_camelname}-${pkgver}/"* "${pkgdir}/usr/share/webapps/${pkgname}"

  # Install license
  install -D "${srcdir}/${_camelname}-${pkgver}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}"

  # Install configuration file and link in /etc folder
  install -D --mode=640 "${srcdir}/${_camelname}-${pkgver}/.env.example" "${pkgdir}/etc/webapps/${pkgname}/config.env"
  chown ${_var_files_owner}:${_var_files_group} "${pkgdir}/etc/webapps/${pkgname}/config.env"
  install --directory "${pkgdir}/etc/webapps/${pkgname}"
  ln --symbolic "/etc/webapps/${pkgname}/config.env" "${pkgdir}/usr/share/webapps/${pkgname}/.env"

  # Relocate cache to /var/cache with symlink
  install --directory "${pkgdir}/var/cache"
  install --directory --owner=${_var_files_owner} --group=${_var_files_group} "${pkgdir}/var/cache/${pkgname}"
  rm -rf "${pkgdir}/usr/share/webapps/${pkgname}/bootstrap/cache"
  ln --symbolic "/var/cache/${pkgname}" "${pkgdir}/usr/share/webapps/${pkgname}/bootstrap/cache"

  # Relocate storage to /var/lib with symlinks
  install --directory "${pkgdir}/var/lib"
  mv "${pkgdir}/usr/share/webapps/${pkgname}/storage" "${pkgdir}/var/lib/${pkgname}"
  ln --symbolic "/var/lib/${pkgname}" "${pkgdir}/usr/share/webapps/${pkgname}/storage"

  # Merge upload directories and relocate to /var/lib with symlinks
  mv "${pkgdir}/usr/share/webapps/${pkgname}/public/uploads/.gitignore" "${pkgdir}/var/lib/${pkgname}/uploads"
  mv "${pkgdir}/usr/share/webapps/${pkgname}/public/uploads/.htaccess" "${pkgdir}/var/lib/${pkgname}/uploads"
  rmdir "${pkgdir}/usr/share/webapps/${pkgname}/public/uploads"
  ln --symbolic "/var/lib/${pkgname}/uploads" "${pkgdir}/usr/share/webapps/${pkgname}/public/uploads"

  # Change ownership of bookstack's /var/lib directories for php to read/write to it
  chown --recursive ${_var_files_owner}:${_var_files_group} "${pkgdir}/var/lib/${pkgname}"
}
