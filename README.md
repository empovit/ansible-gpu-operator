GPU Operator
=========

An Ansible role for deploying the NVIDIA GPU Operator on an OpenShift cluster. It also deploys the Node Feature Discovery (NFD) Operator as a pre-requisite.

Requirements
------------

This role uses [kubernetes.core.k8s](https://docs.ansible.com/ansible/latest/collections/kubernetes/core/k8s_module.html) and [kubernetes.core.k8s_info](https://docs.ansible.com/ansible/latest/collections/kubernetes/core/k8s_info_module.html) modules. See the respective documentation pages for the Python dependencies, but normally they include:

* python >= 3.6
* kubernetes >= 12.0.0
* PyYAML >= 3.11
* jsonpatch

Role Variables
--------------

Parameter | Description | Required | Default Value
----|----|----|----
gpu_type                  | Physical (`gpu`) or virtual (`vgpu`) GPU | Optional | `gpu`
gpu_operator_channel      | Channel to install the GPU Operator from | Optional | `v22.9`
vgpu_guest_driver_version | vGPU guest driver version                | Optional | `510.47.03`
vgpu_guest_drive_image    | vGPU guest driver image                  | Optional | `vgpu-guest-driver-2-0`
openshift_api_vip     | API VIP or DNS name of the OpenShift cluster you want to install the GPU Operator on, must be reachable from the machine that runs the role | Required | -
kubeconfig            | Full path to a kubeconfig file, including the file name | Required | -
ngc_api_key           | NVIDIA NGC catalog API key for pulling driver and operator images | Required when `gpu_type` is `vgpu` | -
ngc_email             | Email address used with NVIDIA NGC catalog | Required when `gpu_type` is `vgpu` | -
nls_client_token      | NVIDIA license system (NLS) token for vGPU guest driver | Required when `gpu_type` is `vgpu` | -
openshift_version     | OpenShift version | Optional | `v4.12`

Check out [NVIDIA AI Enterprise with OpenShift - Create the ClusterPolicy Instance](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/openshift/nvaie-with-ocp.html#create-the-clusterpolicy-instance) for the right vGPU driver values for you OpenShift version.

Installing
----------------

Install the role using a _requirements.yml_ file:

```yaml
---
roles:
  - name: empovit.gpu_operator
    src: https://github.com/empovit/ansible-gpu-operator
    version: <branch or release tag>
```

by running `ansible-galaxy install -r requirements.yml`

Example Playbook
----------------

Deploying on an OpenShift cluster with physical GPUs:

```yaml
    - hosts: localhost
      roles:
         - role: empovit.gpu_operator
           vars:
              openshift_api_vip: api.gpu-cluster.redhat.com
              kubeconfig: /home/totoro/kubeconfig-gpu
```

A cluster with a vGPU:

```yaml
    - hosts: localhost
      roles:
         - role: empovit.gpu_operator
           vars:
              gpu_type: vgpu
              openshift_api_vip: api.gpu-cluster.redhat.com
              kubeconfig: /home/totoro/kubeconfig
              ngc_api_key: V9t7QwTRzzCMALzGFI435ktA0pRQl53m8wp8v7MqYhz8JcLgF6JIB2HqZeYc
              ngc_email: totoro@example.com
              nls_client_token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjEyMzQ1Njc4OTAiLCJuYW1lIjoiVG90b3JvIiwicm9sZSI6IkdQVSBPcGVyYXRvciJ9.jufu49sz7Q2r2d95MZgj-HUXkQRg_PY6UjLay8CX3wA
              gpu_operator_channel: v22.9
              vgpu_guest_driver_version: "510.85.02"
              vgpu_guest_drive_image: vgpu-guest-driver-2-2
              openshift_version: v4.12
```

License
-------

[Apache-2.0](LICENSE)