# Disaster restore procedure

/!\ in progress

## Hardware-OS

- Type
  - Raspiv3
  - ROC RK
- OS
  - raspbianv9
  - armbianv9

### Flash OS

1. Plug SD Card into computer and flash with .img
2. Boot machine 1 by 1 (network connect) and give IP by MACaddr via router
3. play ansible-me.sh
4. reboot

## Custom

1. play other playbooks

### Chapo

Edit fstab to add

```plaintext
/dev/mapper/brain-torrent /torrent ext4 defaults 0 0
```

## Services

### ChapoWebServices

> Plug HDD to ROC chip

1. `git clone git@github.com:ChapChap/ChapoWebServices.git`
2. Enter each service directory *`-svc`*
3. Start every each service with `docker-compose up -d`
