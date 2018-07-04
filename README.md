# Cloud Foundry Container Runtime
A [BOSH](http://bosh.io/) release for [Kubernetes](http://kubernetes.io).  Formerly named **kubo**.

**Slack**: #cfcr on https://slack.cloudfoundry.org
**Pivotal Tracker**: https://www.pivotaltracker.com/n/projects/2093412

## Prerequisites
- A BOSH Director configured with UAA, Credhub, and BOSH DNS.
- [kubo-release](https://github.com/cloudfoundry-incubator/kubo-release)
- [kubo-deployment](https://github.com/cloudfoundry-incubator/kubo-deployment)

## Deploying CFCR

#### Single Master
1. Update the bosh cloud config. See [here](https://github.com/cloudfoundry-incubator/kubo-deployment/tree/master/configurations) for supported IaaS cloud configs.
   ```
         cd kubo-deployment
         bosh -e ENV update-cloud-config #TODO
         ```
1. *Download the [latest version of kubo-release](https://github.com/cloudfoundry-incubator/kubo-release/releases/latest)
1. Upload the release to the director
   `bosh -e ENV upload-release`
1. Run deploy
                ```
                cd kubo-deployment

                bosh -d cfcr manifests/cfcr.yml \
                         --ops-file manifests/ops-files/misc/single-master.yml
                ```
1. Add kubernetes system components
   `bosh -d cfcr run-errand apply-specs`
1. To confirm the cluster is operational
  `bosh -d cfcr run-errand apply-specs`

*Optionally, to deploy master of CFCR use the [local-release ops-file](https://github.com/cloudfoundry-incubator/kubo-deployment/blob/master/manifests/ops-files/kubo-local-release.yml)

```
cd kubo-deployment

bosh -d cfcr manifests/cfcr.yml \
   --ops-file manifest/ops-file/kubo-local-release.yml
```

#### BOSH Lite
The `deploy_cfcr_lite` script will deploy a single master CFCR cluster and assumes the director is configure with the [default cloud config](https://github.com/cloudfoundry/bosh-deployment/blob/master/warden/cloud-config.yml). The kubernetes master host is deployed to a static IP: `10.244.0.34`.

```
git clone https://github.com/cloudfoundry-incubator/kubo-release.git

cd kubo-deployment
./bin/deploy_cfcr_lite
```
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
