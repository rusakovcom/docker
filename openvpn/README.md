Install Openvpn server in Docker container (for example on Ubuntu) with getting myclient.ovpn for connection from Android, Windows, MacOS. Client you can download here: https://openvpn.net/client/

There is bash script install_openvpn_docker.sh or you can try to install manually:

# Install docker on ubuntu
apt update -y && apt install -y docker.io docker-compose

# Create directory for OpenVPN configuration
mkdir -p /opt/openvpn

# Run OpenVPN container with volume mounted
docker run -v /opt/openvpn:/etc/openvpn --rm -it kylemanna/openvpn ovpn_genconfig -u udp://{you domain or ip}

docker run -v /opt/openvpn:/etc/openvpn --rm -it kylemanna/openvpn ovpn_initpki

docker run -v /opt/openvpn:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full myclient

# Start OpenVPN server container in detached mode
docker run --name openvpn -v /opt/openvpn:/etc/openvpn -d -p 1194:1194/udp --cap-add=NET_ADMIN kylemanna/openvpn

# Export client configuration
docker run -v /opt/openvpn:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient myclient > myclient.ovpn




# P.S.
For RHEL Linux (Oracle) it may be the error below and you need to install loadable kernel modules (dnf install iptables kernel-modules)
docker run --privileged --name openvpn -v /opt/openvpn:/etc/openvpn -d -p 1194:1194/udp --cap-add=NET_ADMIN kylemanna/openvpn
modprobe: can't change directory to '/lib/modules': No such file or directory
iptables v1.8.4 (legacy): can't initialize iptables table nat': Table does not exist (do you need to insmod?)
