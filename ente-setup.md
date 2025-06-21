# Ente

On the following I use my local ip 192.168.xxx.xxx but you can replace it with your domain to make it public

## Install

Download installation script.

```bash
wget https://raw.githubusercontent.com/ente-io/ente/main/server/quickstart.sh
```

### Placing volume on external drive

I had the need to put volume data on a external drive.

If you don't need that, just execute `quickstart.sh`.

Comment the `docker compose up` at the end and run it.

Edit `compose.yaml` to modify `volume` section like that:

```yaml
volumes:
  postgres-data:
    external: true
  minio-data:
    external: true
```

Create docker volume by using:

```bash
docker volume create --opt type=none --opt o=bind --opt device=/mnt/data/ente/minio minio-data
docker volume create --opt type=none --opt o=bind --opt device=/mnt/data/ente/postgres postgres-data
```

go to folder my-ente and run 

```bash
docker compose up -d
docker compose logs -f
```

### make it accessible from non localhost

On `compose.yaml` under `web` add:

```yaml
environment:
    ENTE_API_ORIGIN: http://192.168.xxx.xxx:8080
    ENTE_ALBUMS_ORIGIN: https://192.168.xxx.xxx:3002
```

## Create admin account


Open browser to http://192.168.xxx.xxx:3000 and create a account.

On the log you will see the validation code close to the address email.

Download ente-cli from https://github.com/ente-io/ente/releases/tag/cli-v0.2.3 (maybe there is a newer, to be check).
and extract it.

Create `config.yaml` for the cli:

```yaml
endpoint:
    api: "http://192.168.xxx.xxx:8080"
```

Then run 

```bash
export ENTE_CLI_SECRETS_PATH=./secret.txt
./ente account add
./ente account list
```

Get the UserID and edit the file `my-ente/museum.yaml` to add at the end:

```yaml
internal:
    admins:
      - 158xxxxxxx
```

restart ente server by doing

```bash
docker compose down
docker compose up -d
```

Then you can put no limit to your account with

```bash
./ente admin update-subscription -u address@mail.com --no-limit True
```

## Setup storage for remote access

In `museum.yaml` change all `s3.<bucket>.endpoint` to: `192.168.xxx.xxx:3200`.

## Customize shared album link

In `museum.yaml` add:

```yaml
apps:
    public-albums: http://192.168.xxx.xxx:3002
```

## Auto start

in compose.yaml add `restart: unless-stopped` after each `image:` field.

## Mobile app

be sure to use a version with the fix mentionned in https://github.com/ente-io/ente/issues/6186 (not the case today on play store...)

## Reverse Proxy

Once everything works with this configuration, you can use a reverse proxy to use https protocol and avoid opening direct port on your router.