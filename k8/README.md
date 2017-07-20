# OAI-Kubernetes
Open Air Interface EPC's MME, HSS, PWG &amp; SGW deployed at Kubernetes

OpenAirInterface is an implementation of the 3GPP specifications concerning the Evolved Packet Core Networks, that means it contains the implementation of the following network elements: MME, HSS, S-GW, P-GW.
[More information about Open Air Interface](https://gitlab.eurecom.fr/oai/openair-cn)

This project is aimed to run OpenAirInterface  in Docker Containers on a Kubernetes infrastructure

## Install Requirements
Please note that this procedure uses Juju 2.X for deploying and performing configuration changes at K8 but any other K8 methodologies are supported


The enviroment installation:

- Install Kubernetes 1.7 
- Install Linux 4.7.2 low latency Kernel (4.7.1 is also supported) at all the K8-Workers
- Enable support for running Privileged Containers at all the K8-Workers
( make sure to have “jq” otherwise install first sudo apt install jq )

juju show-status kubernetes-master --format json | \
    jq --raw-output '.applications."kubernetes-master".units | keys[]' | \
    xargs -I UNIT juju ssh UNIT "echo -e '\n# Security Context \nKUBE_ALLOW_PRIV=\"--allow-privileged=true\"' | sudo tee -a /etc/default/kube-apiserver && sudo systemctl restart kube-apiserver.service"

juju show-status kubernetes-worker --format json | \
    jq --raw-output '.applications."kubernetes-worker".units | keys[]' | \
    xargs -I UNIT juju ssh UNIT "echo -e '\n# Security Context \nKUBE_ALLOW_PRIV=\"--allow-privileged=true\"' | sudo tee -a /etc/default/kubelet && sudo systemctl restart kubelet.service"

## Deploy Containers
