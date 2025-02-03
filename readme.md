## Start the Containers

```sh
docker compose up -d
```

## Enter the Kali container

```sh
docker exec -it kali-osint bash
```

## Verify torproxy (optional, need to install curl first)

```sh
curl --proxy socks5h://tor-proxy:9050 https://check.torproject.org
```

## Activate venv

```sh
source /opt/venv/bin/activate
```

## Change directory

```sh
cd /opt/PhoneInfoga/bin
```

## Install PhoneInfoga

```sh
../support/scripts/install
```

## Run PhoneInfoga

```sh
proxychains /opt/PhoneInfoga/bin/phoneinfoga scan -n "+15551234567" | tee /osint_results/phoneinfoga_results.txt
```

## Run Sherlock

```sh
proxychains python3 /opt/sherlock/sherlock.py username_here | tee /osint_results/sherlock_results.txt
```

## Run Skiptracer

```sh
proxychains python3 /opt/skiptracer/skiptracer.py | tee /osint_results/skiptracer_results.txt
```
