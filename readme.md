## Start the Containers

```sh
docker compose up -d
```

## Check if setup.sh Ran Successfully

```sh
docker logs kali-osint
```

## Enter the Kali container

```sh
docker exec -it kali-osint bash
```

## Verify torproxy

```sh
curl --proxy socks5h://tor-proxy:9050 https://check.torproject.org
```

## Activate venv

```sh
source /opt/venv/bin/activate
```

## Run PhoneInfoga

```sh
proxychains python3 /opt/PhoneInfoga/phoneinfoga.py -n "+15551234567" | tee /osint_results/phoneinfoga_results.txt
```

## Run Sherlock

```sh
proxychains python3 /opt/sherlock/sherlock.py username_here | tee /osint_results/sherlock_results.txt
```

## Run Skiptracer

```sh
proxychains python3 /opt/skiptracer/skiptracer.py | tee /osint_results/skiptracer_results.txt
```
