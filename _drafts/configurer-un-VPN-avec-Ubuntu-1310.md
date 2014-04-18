
# Links

* https://wiki.debian.org/openvpn%20for%20server%20and%20client
* https://www.digitalocean.com/community/articles/how-to-setup-and-configure-an-openvpn-server-on-debian-6
* https://www.digitalocean.com/community/articles/how-to-setup-and-configure-an-openvpn-server-on-centos-6
* Comparatif PPTP, L2TP, VPN : https://www.bestvpn.com/blog/4147/pptp-vs-l2tp-vs-openvpn-vs-sstp/
* http://doc.ubuntu-fr.org/openvpn

sudo aptitude install openvpn udev easy-rsa

Configurer la PKI:
sudo -s
cd /usr/share/easy-rsa
vim vars # Pour mettre à jour les infos par défault

source vars
./clean-all
./build-ca

Générer la clé du serveur:
./build-key-server <servername>
./build-dh

Générer la clé pour le client:
./build-key <clientname>

Copier la clé vers le client:
tar cvzf <clientname>.tar.gz -C keys <clientname>.{crt,key} ca.crt

Configurer les clés pour le serveur OpenVPN:
cp keys/ca.crt keys/<servername>.{crt,key} keys/dh1024.pem /etc/openvpn/

Configuration du serveur:
cd /etc/openvpn
gzip -cd /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz  > server.conf
vim server.conf # TODO

Activation du routage:
iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE
service iptables save # TODO : not working on ubuntu

vim /etc/sysctl.conf
# Controls IP packet forwarding
net.ipv4.ip_forward = 1


Côté client:
sudo apt-get install openvpn network-manager-openvpn

