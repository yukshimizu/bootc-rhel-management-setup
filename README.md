# bootc-rhel-management-setup
This repo includes ansible playbooks and roles for setting up [a demo environment](https://github.com/yukshimizu/bootc-rhel-management-demo) of managing RHEL bootc images with Red Hat Ansible Automation Platform.

## Project structure
|Directory           |Description|
|:-------------------|:----------|
|[aws](aws/README.md)|Playbooks for deployng on AWS.|
|[oci](oci/README.md)|Playbooks for deployng on OCI.|
|roles               |Shared roles used by above playbooks.|

### Roles
|Name     |Description|
|:--------|:----------|
|[aap](roles/aap/README.md)        |A role to create an Ansible Automation Controller on a VM.|
|[builder](roles/builder/README.md)|A role to create a Builder VM on a VM.|


## Subscriptions
Basically, the demo environment requires to get access to Red Hat gold image, so you must have a matching Red Hat product subscription and must connect their cloud provider accounts to Red Hat.
Please refer to the [Cloud Access user interface](https://access.redhat.com/management/cloud) or Cloud Sources on [cloud.redhat.com](https://cloud.redhat.com/) as described in [Red Hat Cloud Access program overview](https://docs.redhat.com/en/documentation/subscription_central/1-latest/html/getting_started_with_rhel_system_registration/con-red-hat-cloud-access-program-overview).

Required subscriptions:
- Red Hat Enterprise Linux for x86_64
- Red Hat Ansible Automation Platform Subscription