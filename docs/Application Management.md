
#**Application Management**#
Microservices-based architecture is replacing legacy monolithic applications in the industry. 
This helps to break down a huge monolithic application into a discrete set of services 
(also called micro-services) that can talk to each other through APIs. Spinnaker's application 
management feature helps to manage these services across various cloud platforms.

Regardless of the cloud architecture of each individual cloud platform, they are referred to as:

* Server Groups
 
* Clusters
 
* Applications
 
* Load Balancer
 
* Firewall

##**Server Groups**##
The basic unit of an application, The server group is the target environment in which the code 
is deployed, such as a virtual machine, cloud, or Docker image. Also considers basic configurations 
like metadata, number of instances, etc. A server group becomes a collection of instances of the 
currently running software after it is deployed.

##**Clusters**##
Clusters are logical collections of server groups in Spinnaker.

##**Applications**##
An application is a collection of clusters including firewalls and load balancers. 
It also includes the specific service which is to be deployed, all the configurations 
of that service and all the necessary infrastructure on which the service will run.

##**Load Balancer**##
Load balancers are responsible for maintaining traffic balance among instances in server groups. 
Also can enable health checks along with defined health criteria and the health check point. 
A load balancer is always connected with an ingress protocol and port range.

##**Firewall**##
A firewall is a set of rules defined by an IP address range (CIDR) accompanied by a communication 
protocol and a port range.

![Application_management.png](./Application_management.png)

Take note of the following in the image above:

* Both Applications 1 and 2 are cluster collections.
 
* Each cluster is a collection server group.
 
* The Load Balancer controls the traffic for server groups 1 and 2.
 
* The firewall controls the communication using a set of rules and protocols.




 

