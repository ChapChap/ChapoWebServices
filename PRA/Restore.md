# Disaster restore procedure

/!\ in progress

## Hardware-OS

- Type
  - Raspiv3
  - ROC RK
- OS
  - raspbianv9
  - armbienv9

### Flash OS

1. Plug SD Card into computer and flash with .img
2. Boot machine 1 by 1 (network connect) and give IP by MACaddr via router
3. play ansible-me.sh
4. play playbook ip
5. reboot

> Set IP addresses via router

## Custom

1. play other playbooks

## Services

1. git clone
