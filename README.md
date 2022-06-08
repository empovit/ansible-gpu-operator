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

* `gpu_type` (optional) - physical (`gpu`, the default) or virtual (`vgpu`).
* `gpu_operator_channel` (optional) - channel to install the GPU Operator from, by default `v1.10`.
* `openshift_api_vip` - API VIP or DNS name of the OpenShift cluster you want to install the GPU Operator on, must be reachable from the machine that runs the role.
* `kubeconfig` - full path to a kubeconfig file, including the file name.
* `ngc_api_key` (required when `gpu_type` is `vgpu`) - NVIDIA NGC catalog API key for pulling driver and operator images.
* `ngc_email` (required when `gpu_type` is `vgpu`) -
email address used with NVIDIA NGC catalog.
* `nls_client_token` (required when `gpu_type` is `vgpu`) -
NVIDIA license system (NLS) token for vGPU guest driver.


Installing
----------------

The role can be installed individually `ansible-galaxy install role https://github.com/empovit/ansible-gpu-operator/issues` or as part of a _requirements.yml_ file:

```yaml
---
roles:
  - name: empovit.gpu_operator
    src: https://github.com/empovit/ansible-gpu-operator
```

using `ansible-galaxy install -r requirements.yml`

Example Playbook
----------------

Deploying on an OpenShift cluster with physical GPUs:

```yaml
    - hosts: localhost
      roles:
         - name: empovit.gpu_operator
           vars:
              openshift_api_vip: api.gpu-cluster.redhat.com
              kubeconfig: /home/totoro/kubeconfig-gpu
```

A cluster with a vGPU:

```yaml
    - hosts: localhost
      roles:
         - name: empovit.gpu_operator
           vars:
              gpu_type: vgpu
              openshift_api_vip: api.gpu-cluster.redhat.com
              kubeconfig: /home/totoro/kubeconfig
              ngc_api_key: V9t7QwTRzzCMALzGFI435ktA0pRQl53m8wp8v7MqYhz8JcLgF6JIB2HqZeYc
              ngc_email: totoro@example.com
              nls_client_token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjEyMzQ1Njc4OTAiLCJuYW1lIjoiVG90b3JvIiwicm9sZSI6IkdQVSBPcGVyYXRvciJ9.jufu49sz7Q2r2d95MZgj-HUXkQRg_PY6UjLay8CX3wA
```

License
-------

Apache 2.0