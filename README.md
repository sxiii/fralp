# FreeRadius over alpine:edge with EAP-TLS (fralp)

## Steps to build container

### Unpacking
```
git clone https://github.com/sxiii/fralp
cd fralp
```

### Build EasyRSA container
```
docker build easyrsa -t easyrsa
```

### Generate certificates (example client name: toshiba)
```
docker run -it -v pki:/easyrsa/pki easyrsa build-client-full toshiba
```

After generating certificates and private keys, you need to copy them manually from a "pki" docker volume to your client (total of 3 files):
* ca.crt (Central Authority Certificate)
* toshiba.crt (Your own certificate file)
* toshiba.key (Your own key file)
The files inside PKI docker volume can be located by navigating to /easyrsa/pki path.

### Build FRALP docker container
```
docker build -t fralp .
```

### Running freshly-built docker container (in visible mode)
```
sudo docker run -it -p 1812:1812/udp --restart=always -v pki:/etc/raddb/certs -e CLIENT_ADDRESS=192.168.1.1 -e CLIENT_SECRET=password -e PRIVATE_KEY_PASSWORD=password fralp:latest
```

Replace "-it" flag with "-d" to daemonize the process to background.
Replace CLIENT_ADDRESS with wifi-hotspot address, as long as CLIENT_SECRET and PRIVATE_KEY_PASSWORD with according values.

Now you can configure your AP with WPA2 Enterprise, AES, the server IP and client secret.
Use two certificates along with user private key to authenticate against freeradius tls.

