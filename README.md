# eos-containerlab-step0
Personal experience in setting up container lab to play with EOS

## Host OS

Use a version part of the supported OSes, since that allows to run the installation script, list of supported OSes:
https://containerlab.dev/install/


## Install Docker

https://docs.docker.com/engine/install/

## CEOS image

1 - Download the CEOS image manually or use the use the [EOS downloader](https://github.com/titom73/eos-downloader) and place in it the host and then import it

```bash
$ docker import cEOS-lab-$EOS_VERSION.tar ceos:$EOS_VERSION
```

Example to import 4.33.3F with the image name 4.33.3
```bash
$ docker import cEOS-lab-4.33.3F.tar ceos:4.33.3
```

Confirm
```bash
$ docker images | grep ceos
ceos                    4.33.3    1f08459f4669   34 minutes ago   2.12GB
```

With the above in the topology YAML files to use 4.33.3 simply state:
```bash
topology:
  kinds:
    ceos:
      image: ceos:4:33.3
```

Alternatively the YAML file can be defined to use 4.33.3 by default or use another image which name can be passed as a parameter, exmaple:
```bash
topology:
  kinds:
    ceos:
      image: ceos:${EOS_VERSION:=4.33.3}
```

## Install container lab

```bash
bash -c "$(curl -sL https://get.containerlab.dev)"
```

> [!WARNING]
> For MacOS installation, please refer to containerlab [website](https://containerlab.dev/install/)

## Containerlab topology (example file)

File name topology.clab.yaml, example:
```bash
name: orr
mgmt:
  network: orr
  ipv4-subnet: 192.168.2.0/24 # management subnet

topology:
  kinds:
    ceos:
      image: ceos:${EOS_VERSION:=4.33.3} # use 4.33.3 unless specified otherwise
  nodes:
  # -------------------------------- #
    client:
      kind: ceos
      mgmt-ipv4: 192.168.2.112 # management IP 
      startup-config: configs/client.cfg # optional: startup config in local directory named configs
    r4-gw:
      kind: ceos
      mgmt-ipv4: 192.168.2.114 # management IP 
      startup-config: configs/r4-gw.cfg # optional: startup config in local directory named configs
  links:
  # -------------------------------- #
  # Links between the routers
    - endpoints: ["client:eth3", "r4-gw:eth1"] # connections between the two
```

## Containerlab start/stop

To start the lab, run:
```sh
containerlab deploy -t topology.clab.yaml
```

To destroy the lab:
```sh
containerlab destroy -t topology.clab.yaml
```

### Default credentials
admin/admin
