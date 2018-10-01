# NovaEvacuation
## Overview
If you want to move an instance from a dead or shut-down compute node to a new host server in the same environment (for example, because the server needs to be swapped out), you can evacuate it using nova evacuate.
An evacuation is only useful if the instance disks are on shared storage or if the instance disks are Block Storage volumes. Otherwise, the disks will not be accessible and cannot be accessed by the new compute node.
An instance can only be evacuated from a server if the server is shut down; if the server is not shut down, the evacuation will fail.
## Prerequisites
Before proceeding with the nova compute evacuation we need to update the nova-compute package as this has been an issue in our openstack environment and we can’t perform evacuation without updating nova-common package.
Check and follow the following support case for nova evacuation issue;

https://access.redhat.com/support/cases/#/case/02106123

And the updated nova-common package is available here;

https://access.redhat.com/downloads/content/rhel---7/x86_64/5055/openstack-nova-common/14.1.0-22.el7ost/noarch/fd431d51/package

## Notice
Don’t update the package with yum update, instead update the package manually. Updating using yum update might crash the whole openstack environment.
## Procedure
Before proceeding with the evacuation procedure one should confirm the following precautions;
	
* Clone the following git repository and use the evacuation.sh script provided in this repository;

  https://github.com/raonadeem/NovaEvacuation

* Add an rc source file with user having admin rights.
* Make sure that nova-compute service is down on the compute node for which we are going to perform the evacuation. This will   avoid nova-scheduler to allocate the instances on this failed/shutdown compute node while it’s in healthy state.

* To check the nova-compute service status use the following command;

  `./evacuation.sh -n dev-compute00.net -a check -k evacrc`
  - dev-compute00.sahaba.net is the node name which is in shutdown state and need to be repaired.

  - evacrc is an rc file with admin rights.

* To disable the nova-compute service and then perform the evacuation of all the instances for a particular node use the        following command;

  `./evacuation.sh -n dev-compute00.net -a disable -k evacrc -t nonlive`

  This will perform a nonlive migration of all the instances on dev-compute00.sahaba.net node to some other available node     and restart all the instances.
  
  **Notice:**
  This command would be successful if all the instances on this node are in running state and will fail if any of the 	       instance is not in running state.
	
* To enable the nova-compute service on a node once it’s in healthy and repaired state so that nova-scheduler can spawn the     instances on this node run the following command;

  `./evacuation.sh -n dev-compute00.net -a enable -k evacrc`

