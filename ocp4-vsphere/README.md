# OpenShift 4 over VMware vSphere install

The goal of these playbooks is automate the Openshift 4 cluster deployments over vSphere platform.

All tasks from creating manifests, patching, creating and converting ignition files and interacting with vCenter should be automated by the playbook.

## Prerequisites

* vSphere ESXi and vCenter 6.5 or superior
* A datacenter created with vSphere host added to it
* This playbook will be executed on an installer node that will serve as the HTTP and DHCP servers.
* The installer node should have at least Ansible 2.7

## Install

Edit the variables on the `inventory` file for the cluster and vCenter parameters.

Add the reserved IP addresses for the Master, Bootstrap and Infra nodes.

The following DNS entries should have been created on the DNS Server:

`api.<cluster_name>.<base_domain>` - Should point to Control-Plane LB VIP
`api-int.<cluster_name>.<base_domain>` - Should point to Control-Plane LB VIP
`*.apps.<cluster_name>.<base_domain>` - Should point to Infra-Node LB VIP
`etcd-0.<cluster_name>.<base_domain>` - Should point to Control-Plane Node 0
`etcd-1.<cluster_name>.<base_domain>` - Should point to Control-Plane Node 1
`etcd-2.<cluster_name>.<base_domain>` - Should point to Control-Plane Node 2
`_etcd-server-ssl._tcp.<cluster_name>.<base_domain>` - Should be three `SRV` entries pointing to each of the Control-Plane nodes.

Two Load-balancer instances should be created, one for the control plane pointing to the Master-Nodes + the Bootstrap nodes. This LB VIP should be used on the DNS entries created above. Another Load-balancer instance should point to the Infra-Nodes and the VIP should match the `*.apps.cluster.domain` DNS entry.

The pullsecret has been saved on the playbook dir as `pullsecret.txt`

Execute the playbook with:

```bash
ansible-playbook main.yml
```

This playbook will import and run the node preparation tasks from `prepare-installer-node.yml` and then run the cluster deployment tasks from `deploy_ocp4.yml`.