## Network > Internet Gateway > Console User Guide
This guide describes how to use the Internet Gateway service from the console.

## Manage Internet Gateways
### Create an Internet Gateway
1. To create an internet gateway, click **Create Internet Gateway** and enter values for the following items.
    * **Name**: Enter the name of the internet gateway.
    * **External Network**: Select the external network to which the internet gateway will be connected, from the list of predefined external networks. NHN Cloud currently operates 1 "Public Network" that can connect to the internet.
2. Click **Confirm** to create the internet gateway.

### Attach an Internet Gateway
You can use an internet gateway by associating it with a routing table. When the internet gateway is associated with a routing table, it is assigned a public IP for internet access and adds the network configuration to the routing table with which it is associated.
Association of internet gateway and routing table is provided in the **VPC > Routing** menu.

1. Select the routing table to associate with the internet gateway, and click **Attach Internet Gateway** button.
2. A list of currently attachable internet gateways is displayed in the **Internet Gateway** item. Select an internet gateway to attach from the list.
3. Click **Confirm** to associate the internet gateway with the routing table.

When the internet gateway is attached, a route entry for internet connection is added to the **Route** list in the routing table.

* **CIDR**: `0.0.0.0/0`
* **Gateway Type**: `INTERNET_GATEWAY`
* **Gateway**: Name of the attached internet gateway

This route cannot be deleted manually. It is automatically deleted when the internet gateway is detached.

### Detach an Internet Gateway
You can disassociate a routing table from an internet gateway. However, if there are instances or load balancers that associated floating IPs with subnets associated with the routing table, you can disassociate the routing table from the internet gateway after disassociating all the floating IPs.
Disassociation of internet gateway and routing table is provided in the **VPC > Routing** menu.

1. Select the routing table to disassociate from the internet gateway, and click **Detach Internet Gateway**.
2. Click **Confirm** to disassociate the internet gateway from the routing table.

When the internet gateway is detached, the route entry for internet connection is deleted from the **Route** list in the routing table.

### Delete an Internet Gateway
An internet gateway can be deleted regardless of its status of association with a routing table. If you delete an internet gateway associated with a routing table, it is automatically disassociated from the routing table and then deleted. So, similar to "Detach an Internet Gateway", if there are instances or load balancers that associated floating IPs with subnets associated with the routing table, you can delete the internet gateway after disassociating all the floating IPs.

1. Select the internet gateway to delete.
2. Click **Delete Internet Gateway**.
3. Click **Confirm** to delete the internet gateway.

<a id='restarting-guide-for-maintenance'></a>
### Restart Internet Gateways for Server Maintenance

NHN Cloud updates software of the internet gateway server on a regular basis to enhance security and reliability of its infrastructure services.
Internet gateways that are running on a maintenance target server must be restarted and migrated to the server where maintenance has been completed.

An internet gateway that requires a restart has a **! Restart** button displayed next to its name. You can use this button to restart the gateway.

Go to the project where the internet gateway specified as a maintenance target is located, and perform a restart with the following steps.

1. Check the internet gateway that is a maintenance target.
   An internet gateway with the **! Restart** button next to its name is the maintenance target.
   ![Internet gateway maintenance image 001](http://static.toastoven.net/prod_vpc/ConsoleGuide/ig_planned_migration_guide-en-001.png)
   You can check the detailed maintenance schedule by hovering the mouse cursor over the **! Restart** button.
   ![Internet gateway maintenance image 002](http://static.toastoven.net/prod_vpc/ConsoleGuide/ig_planned_migration_guide-en-002.png)
2. Select the maintenance target internet gateway and click the **! Restart** button next to its name.
   Internet connection of the instance using the maintenance target internet gateway is blocked until the restart is complete, so it is recommended to perform maintenance during the time when impact on the service operation is minimal.
   However, instances that are associated with floating IPs are not affected by the restart of the internet gateway.
3. When the window asking whether to restart the internet gateway appears, click **Confirm**.
   ![Internet gateway maintenance image 003](http://static.toastoven.net/prod_vpc/ConsoleGuide/ig_planned_migration_guide-en-003.png)
4. Wait until the status indicator turns green and the **! Restart** button disappears.
   If the internet gateway status indicator does not change or the **! Restart** button does not disappear, try 'Refresh'.
   ![Internet gateway maintenance image 004](http://static.toastoven.net/prod_vpc/ConsoleGuide/ig_planned_migration_guide-en-004.png)

The internet gateway becomes inoperable while it is being restarted.
If the internet gateway is not restarted normally, the issue will be automatically reported to the administrator and NHN Cloud will contact you separately.
