# Maintainer: Nick Bair <njbair at gmail dot com>
pkgname=bubbleupnpserver
pkgver=0.6.5
pkgver_suffix="-1_all"
pkgrel=1
pkgdesc="Stream UPnP content to Android devices over the Internet"
arch=('any')
url="http://www.bubblesoftapps.com/bubbleupnpserver/"
license=('custom:bubbleupnpserver')
depends=('jre7-openjdk-headless' 'ffmpeg')
install=${pkgname}.install
source=("http://www.bubblesoftapps.com/${pkgname}/${pkgver}/BubbleUPnPServer-${pkgver}.zip" "http://ppa.launchpad.net/bubbleguuum/${pkgname}/ubuntu/pool/main/b/${pkgname}/${pkgname}_${pkgver}${pkgver_suffix}.deb")
noextract=("BubbleUPnPServer-${pkgver}.zip")
md5sums=('27321aac68d5fcc75d756b0454637516'
         'ad1fb94185a9a22bd60fe6f7b7abbf3e')

package() {
  cd ${srcdir}

  msg2 "Extracting files..."
  ar x "${srcdir}/${pkgname}_${pkgver}${pkgver_suffix}.deb" data.tar.gz
  tar xf ${srcdir}/data.tar.gz -C ${pkgdir} --exclude='./etc*'
  tar xf ${srcdir}/data.tar.gz ./etc/default/${pkgname}
  tar xf ${srcdir}/data.tar.gz ./etc/init/${pkgname}.conf
  unzip -q "${srcdir}/BubbleUPnPServer-${pkgver}.zip" -d ${pkgdir}/usr/share/${pkgname} -x launch.bat

  msg2 "Shifting some files around..."
  install -D -m644 ${srcdir}/etc/default/${pkgname} ${pkgdir}/etc/default/${pkgname}
  install -D -m644 ${pkgdir}/usr/share/${pkgname}/LICENCE.txt ${pkgdir}/usr/share/licenses/${pkgname}/LICENSE

  msg2 "Converting upstart script..."
  echo "#!/bin/sh" > ${pkgdir}/usr/share/${pkgname}/${pkgname}.sh
  cat ${srcdir}/etc/init/${pkgname}.conf \
    | grep -vE '^(description|author|start on|stop on|respawn|script|end script)' \
    | sed -r 's/^(\t|env )//' \
    | sed '/^$/N;/^\n$/D' \
    >> ${pkgdir}/usr/share/${pkgname}/${pkgname}.sh

  msg2 "Installing systemd service unit file..."
  echo "[Unit]
Description=BubbleUPnP Server

[Service]
ExecStart=/usr/share/${pkgname}/${pkgname}.sh
SuccessExitStatus=1 2 SIGKILL

[Install]
WantedBy=network.target" > ${srcdir}/${pkgname}.service
  install -D -m644 ${srcdir}/${pkgname}.service ${pkgdir}/usr/lib/systemd/system/${pkgname}.service

  msg2 "Making startup scripts executable..."
  chmod +x ${pkgdir}/usr/share/${pkgname}/{${pkgname},launch}.sh
}

# vim:set ts=2 sw=2 et:
