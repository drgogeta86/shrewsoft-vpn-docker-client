# Container to connect to a Shrew Soft IPSec VPN

You cannot use podman (even rootfull)

You need a valid configuration file named SERVER_NAME.vpn in folder VPN. See example in `VPN/ssl.example.org.vpn`. We use `ike-qtgui` to make and export the configuration file.

This is a fork of https://github.com/jecicorp/shrewsoft-vpn-docker-client.git to include an SSH Server for tunneling pourpose

**Warning, in the sample we define `policy-list-include` to route only targeted resources.**

``` shell
#.envrc
export VPN_SITE_NAME=ssl.example.org
export VPN_USER=user
export VPN_PASSWORD=xxxxxxx

# First build (don't forget the last dot)
sudo docker build -t vpn-ike --build-arg "SITE_NAME=${VPN_SITE_NAME}" .

# Start Daemon
AUTHORIZED_SSH_KEY=$(cat ~/.ssh/id_ecdsa.pub)
docker run --init --rm -e AUTHORIZED_SSH_KEY="${AUTHORIZED_SSH_KEY}" -d --name vpn-ike -p12222:22 --device=/dev/net/tun --cap-add=NET_ADMIN --cap-add=DAC_READ_SEARCH  --cap-add=CAP_SYS_CHROOT  vpn-ike


# Start VPN connexion (CTRL+C top stop)
# This will ask for key password
sudo docker exec -it vpn-ike ikec -r "${VPN_SITE_NAME}" -u "${VPN_USER}" -p "${VPN_PASSWORD}" -a 

# Stop Daemon
sudo podman stop vpn-ike
```
