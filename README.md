# docker-openvpn

My docker-compose setup to run a OpenVPN server to connect to my local LAN.

## Installation

## Installation

1. Make sure you've installed all requirements
1. Clone this repository:

```shell
git clone https://github.com/frdmn/docker-openvpn
```

1. Create a copy of the sample `.env` file and adjust it at will:

```shell
cp .env.sample .env
```

1. Spin up the container:

```shell
docker-compose up -d
```

## Configuration

You can make use of the following environment variables / configurations:

| Environment variable | Default value | Description
|----------------------|---------------|------------| 
| `OPENVPN_IMAGEVERSION` | `latest` | Image version of OpenVPN container |
| `OPENVPN_PORT` | `1194` | Port to bind OpenVPN on the host system (UDP) |

## Usage

### OpenVPN

#### Initialize the server configuration and create CA

```bash
export DYNHOSTNAME="dyndyns.hostname.com"
docker-compose run --rm openvpn ovpn_genconfig -u udp://${DYNHOSTNAME}
docker-compose run --rm openvpn ovpn_initpki
chown -R $(whoami):$(whoami) ./data/config
```

Please note, this steps has to be done just one time after spinning up the container initially.

#### Create client OpenVPN configuration/certifiactes

```bash
export CLIENTNAME="clientname-mac"
docker-compose run --rm openvpn easyrsa build-client-full ${CLIENTNAME}
```

If you don't want to use a password:

```bash
docker-compose run --rm openvpn easyrsa build-client-full ${CLIENTNAME} nopass
```

#### Revoke a client certificate

To keep the created keys, certificates and request files:

```bash
docker-compose run --rm openvpn ovpn_revokeclient ${CLIENTNAME}
```

Or in case you want to remove them permanetly as well:

```bash
docker-compose run --rm openvpn ovpn_revokeclient ${CLIENTNAME} remove
```

#### Debug mode

Just pass the `DEBUG=1` environment variable to the container:

```bash
docker-compose run -e DEBUG=1 openvpn
```

### Services

#### Start/create services

```shell
$ docker-compose up -d
Creating openvpn_openvpn_1  ... done
```

#### Stop services

```shell
$ docker-compose stop
Stopping openvpn_openvpn_1  ... done
```

#### Upgrade services

```shell
$ docker-compose stop
$ docker-compose pull
$ docker-compose rm
$ docker-compose up -d
```

#### Check logs

```shell
$ docker-compose logs -f
```

## Contributing

1. Fork it
1. Create your feature branch:

```shell
git checkout -b feature/my-new-feature
```

1. Commit your changes:

```shell
git commit -am 'Add some feature'
```

1. Push to the branch:

```shell
git push origin feature/my-new-feature
```

1. Submit a pull request

## Requirements / Dependencies

* Docker (incl. `docker-compose`)
* Forward port `${OPENVPN_PORT}` (`1194` by default) on your router to the client that will run the OpenVPN server
* Dynamic DNS hostname that your clients can use to resolve to the external IP of your home network

## Version

1.0.0

## License

[MIT](LICENSE)
