# Cloud Foundry Container Runtime
A [BOSH](http://bosh.io/) release for [Kubernetes](http://kubernetes.io).  Formerly named **kubo**.

**Slack**: #cfcr on https://slack.cloudfoundry.org
**Pivotal Tracker**: https://www.pivotaltracker.com/n/projects/2093412

## Prerequisites
- A BOSH Director configured with UAA, Credhub, and BOSH DNS.
- [kubo-release](https://github.com/cloudfoundry-incubator/kubo-release)
- [kubo-deployment](https://github.com/cloudfoundry-incubator/kubo-deployment)

## Deploying CFCR

### Bosh Bootloader

[Bosh Bootloader](https://github.com/cloudfoundry/bosh-bootloader) (BBL) is our de-facto way to deploy Bosh and CFCR. See [here](https://github.com/cloudfoundry/bosh-bootloader/tree/master/plan-patches) for a list of CFCR plan patches that can be used on GCP/AWS/vSphere/Openstack IaaS's to configure your Bosh Deployment for CFCR. In each plan patch there are guidelines to follow.

### BOSH Lite
The `deploy_cfcr_lite` script will deploy a single master CFCR cluster and assumes the director is configure with the [default cloud config](https://github.com/cloudfoundry/bosh-deployment/blob/master/warden/cloud-config.yml). The kubernetes master host is deployed to a static IP: `10.244.0.34`.

```
git clone https://github.com/cloudfoundry-incubator/kubo-release.git

cd kubo-deployment
./bin/deploy_cfcr_lite
```

### Using CFCR Docs (DEPRECATED)

We are no longer supporting the following deployment strategies and are being deprecated
* https://docs-cfcr.cfapps.io/installing/gcp/
* https://docs-cfcr.cfapps.io/installing/aws/
* https://docs-cfcr.cfapps.io/installing/vsphere/
* https://docs-cfcr.cfapps.io/installing/openstack/

The following scripts are also deprecated
* https://github.com/cloudfoundry-incubator/kubo-deployment/blob/master/bin/deploy_bosh
* https://github.com/cloudfoundry-incubator/kubo-deployment/blob/master/bin/deploy_k8s

## Accessing the CFCR Cluster (kubectl)

### Without Load Balancer
1. Find the IP address of one master node e.g. `bosh -e ENV -d cfcr vms`
2. Login to the Credhub Server that stores the cluster's credentials:
  ```
  credhub login
  ```
3. Execute the [`./bin/set_kubeconfig` script](https://github.com/cloudfoundry-incubator/kubo-deployment/blob/master/bin/set_kubeconfig) to configure the `kubeconfig` for use in your `kubectl` client:
  ```
  cd kubo-deployment

  $ ./bin/set_kubeconfig <ENV>/cfcr https://<master_node_IP_address>:8443
  ```
