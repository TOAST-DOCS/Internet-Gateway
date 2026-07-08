<a id="network-internet-gateway-overview"></a>
## Network > Internet Gateway > Overview { #network-internet-gateway-overview }

You can use an internet gateway to connect resources in your VPC to the internet. You can provide services to the outside by associating floating IPs with instances and load balancers that can connect to the internet through an internet gateway.

When you associate an internet gateway with a routing table, the internet gateway automatically configures the routing required for internet connection and assigns a public IP address required for external communication.
By blocking incoming traffic from outside to the public IP address of the internet gateway, it prevents direct access to instances or load balancers in the VPC with the public IP address of the internet gateway. Therefore, it is not possible to directly access resources in a VPC only by connecting an internet gateway. Instead, you can associate a floating IP with an instance or a load balancer to access resources in a VPC.

You can use an internet gateway by associating it with a routing table. Therefore, you can configure subnets associated with a specific route table to be internet-facing so that only some of the subnets in your VPC are internet-facing, while others are isolated from external networks.